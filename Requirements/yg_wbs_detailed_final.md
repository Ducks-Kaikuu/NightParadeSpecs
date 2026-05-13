# 妖怪協力ゲーム WBS実装計画書（詳細版・最終調整）

## 追加/修正ポイント（最終調整）
- 動的生成ActorのReplication方針を補強
- Interaction受信をInterface化して統一
- ClaudeCode向けに「必須注意事項」を明文化

---

# WBS 0. 共通ルール固定

## 0.1 設計原則
- UIで説明しない
- 理解はワールド表現で行う
- Abilityは結果を確定しない
- 結果はSynergySystemのみが決定する
- Host authorityで全結果を確定
- InteractionはInterface経由で統一的に呼び出す

## 0.2 禁止事項
- Ability内で分岐して結果生成
- UIで属性説明
- キャラからField状態を直接変更
- AIに命令UIを持たせる
- Cast前提で反応先を呼び分ける（Interface未使用）

---

# WBS 1. YGCore

## 1.1 Enum定義
- EYGElementType (Water, Fire, Wind, Slash)
- EYGStatusEffectType (Burn, Slip, Panic, Stun, WindLift, Down)
- EYGFieldDominance (Water, Fire, Neutral)

## 1.2 Struct
- FYGStatusEffectSpec
- FYGSynergyRequest
- FYGFieldState
- FYGElementBalanceConstants

完了条件:
- 全モジュールがCore型を使用
- 優勢/均衡/減衰/柱強度/弾強度の定数は FYGElementBalanceConstants に集約

## 1.3 Interface
- IYGInteractionInterface
  - OnElementReceived(EYGElementType Element, float Strength, AActor* Instigator)

完了条件:
- 属性入力は Interface 経由で送信できる
- Execute_OnElementReceived による統一呼び出しが可能

---

# WBS 2. GameplayRuntime

## 2.1 StateComponent
機能:
- 状態追加
- 状態削除
- Replication
- OnRep_ActiveEffects

完了条件:
- 全Actorが共通状態を持つ
- 状態異常の真値は StateComponent に集約

## 2.2 InteractionResponseComponent
機能:
- IYGInteractionInterface を実装
- 属性入力受付
- 状態変換
- 物理反応発火

完了条件:
- Water/Fire/Wind/Slashの反応統一
- AI/キャラ/NPC/監視装置が同一入口で反応

## 2.3 Interaction → State → AI 連携
フロー:
1) Interface 経由で OnElementReceived を受信
2) UYGInteractionResponseComponent が変換
3) UYGStateComponent.ApplyEffect を呼ぶ
4) StateComponent の状態が更新
5) StateTree Evaluator が StateComponent を参照
6) 条件成立で Panic/Slip/WindLift/Stun 等へ遷移

完了条件:
- AIは直接エフェクトを付与しない（必ず Interaction 経由）

---

# WBS 3. FieldRuntime

## 3.1 FieldContainer
機能:
- 水スタック
- 火スタック
- 優勢判定
- 均衡状態
- 減衰
- Physical Materialによる補正

完了条件:
- 水火スタック管理
- 優勢判定は FYGElementBalanceConstants のみ参照

## 3.2 SynergySystem（最重要）
機能:
- 水×火 → 相殺 + 蒸気
- 水×風 → 水柱
- 火×風 → 火柱
- 水×斬 → 水弾
- 火×斬 → 火弾

生成仕様:
- 生成座標:
  - 既定: FieldActor の重心
  - 衝突起点がある場合: 衝突点を優先（オプション）
- Instigator:
  - FYGSynergyRequest.Instigator を必ず継承
- Replication:
  - Host（Listen Server）で Spawn
  - Column/Projectile/Steam は bReplicates = true
  - 動く生成物（Projectile など）は SetReplicatedMovement(true) を必須確認事項とする

完了条件:
- すべての属性結果がここで確定
- クライアントで結果を確定しない
- 生成位置/所有権/同期方針がログで追跡可能

## 3.3 ColumnActor
- 水柱 / 火柱 / 風柱 / 斬柱
- bReplicates = true
- 固定物であっても同期方針を明示

完了条件:
- 安全属性判定と縦方向効果が成立

## 3.4 ProjectileActor
- 水弾 / 火弾
- bReplicates = true
- SetReplicatedMovement(true) を前提に設計

完了条件:
- 遠距離干渉が全クライアントで一致

## 3.5 SteamActor
- 水蒸気生成
- 軽い視界妨害VFX
- bReplicates = true（必要時）
- 動く場合は SetReplicatedMovement(true) を検討

完了条件:
- 水×火の結果が視覚的に分かる

## 3.6 Physical Material 連携
方針:
- 地面の Physical Material を直接書き換えない
- Actor側の Movement/Interaction で摩擦/滑りを制御

実装:
- UYGInteractionResponseComponent:
  - 水接触時: 摩擦係数低下（Slip）
  - 氷面: さらに低下
- AYGElementFieldActor:
  - 接地面の PhysicalMaterial を参照し、流動係数に反映

完了条件:
- 坂で水が流れる
- 滑りはキャラ毎に制御可能

---

# WBS 4. CharacterRuntime

## 4.1 PlayableCharacter
- SNAnimation継承
- AbilityComponent
- StateComponent
- InteractionResponseComponent
- AffinityComponent

完了条件:
- SNAnimationベースで動作

## 4.2 AbilitySystem
- Tag駆動
- 発火のみ担当
- 結果確定は禁止

完了条件:
- Index駆動禁止
- AbilityはSynergySystemに橋渡しのみ

## 4.3 CharacterSwitch
- Possess切替
- ViewTarget切替
- 各キャラが背面カメラを保持

完了条件:
- SetViewTargetWithBlend を標準化
- Controllerに独自カメラを持たない

---

# WBS 5. AIRuntime

## 5.1 EnemyBase
- StateComponent保持
- InteractionResponse適用
- Detection/Patrolを保持

完了条件:
- 敵/NPC/監視装置が共通反応基盤を持つ

## 5.2 StateTree
状態:
- Idle
- Patrol
- Alert
- Chase
- Attack
- Panic
- Slip
- WindLift
- Stun
- Down

完了条件:
- 状態がAI行動を上書き
- Evaluator は StateComponent のみ参照

---

# WBS 6. UIRuntime

## 6.1 HUD最小構成
- PartyPanel
- AbilityPanel
- Objective

完了条件:
- UI最小構成
- 説明UI/危険UI/シナジーUIなし

---

# WBS 7. VFX設計

## 7.1 水
- 流れる
- 広がる

## 7.2 火
- 明るい
- 揺れる

## 7.3 風
- 歪み
- 渦

## 7.4 斬
- 一瞬
- 残像

## 7.5 蒸気
- 短時間
- 視界妨害

完了条件:
- UI無しで属性が識別可能

---

# WBS 8. リスポーン/チェックポイント

## 8.1 チェックポイント
- 最新アンカーを保存（PlayerState または SaveGame）
- 更新トリガ:
  - 特定地点通過
  - ミッション進行

## 8.2 復帰
- 復帰位置:
  - 操作キャラ周囲 or 最新チェックポイント
- 復帰時:
  - 無敵時間
  - 状態リセット（重要エフェクトのみ保持可）

完了条件:
- マルチ/ソロで一貫した復帰位置

---

# ClaudeCode向け必須注意事項

- 動的生成Actorは bReplicates = true を確認すること
- 移動する生成Actor（Projectile 等）は SetReplicatedMovement(true) の要否を必ず判断し、必要なら有効化すること
- 属性入力は IYGInteractionInterface 経由で送ること
- Cast前提で OnElementReceived を直接呼ばないこと
- Host authority 以外で結果を確定しないこと
