# **NightParade レベルデザイン仕様書（最終確定版）**

## **対象ミッション: 「動力コア奪還：制御室の死闘」**

## **1\. ミッション概要**

* **目的:** 通路のトラップを突破して「巨大スイッチ（動力コア）」を制御室のソケットへ運び、電力を復旧させて脱出する。  
* **主要ギミック:** 動く熱線レーザー、重量級運搬（ぬりかべ・巨大スイッチ）、ドアストッパー防衛戦。  
* **パーティ編成:** 4体固定（天狗・河童・妖狐・鎌鼬）。プレイヤー不在枠はNPCが担当し、魂移しによる切り替えが可能。

## **2\. 進行フェーズ詳細**

### **Phase 1: 試練の廊下（タイミング回避と遮蔽）**

* **環境:** 左右の壁から「熱線レーザー」がスキャンするように往復移動している。  
* **ギミック:** \* **動くレーザー:** 周期的に通路を横断する。触れると大ダメージ。  
  * **攻略の遊び:** \* **鎌鼬:** レーザーが重なる瞬間にタイミングよく「瞬身」を繰り出すことで、無敵時間を利用してすり抜ける。  
    * **その他キャラ:** 鎌鼬が先行してレーザーの発生装置を一時停止させるか、NPC「ぬりかべ」を盾にして安全に進む。  
* **レベル設計の肝:** 瞬身の連打では抜けられないよう、レーザーの密度と周期を設定。プレイヤーの「観察とリズム」を試す。

### **Phase 2: ぬりかべの解放と合同運搬**

* **環境:** 通路の終端で拘束されている「ぬりかべ」を解放する。  
* **ギミック:**  
  * **重量級運搬:** ぬりかべを次のエリアまで運ぶ。  
  * **集団推力:** プレイヤー単独では重いが、NPCと協力して「体押し」することで移動可能。  
  * **河童の突っ張り:** ぬりかべを一気に3m押し出し、進行を加速させる。

### **Phase 3: 制御室の防衛「ドアストッパー」**

* **環境:** 中央に「巨大スイッチ（動力コア）」がある広間。  
* **ギミック:**  
  * **物理的封鎖:** スイッチを運搬する間、四方の入り口から敵兵が殺到する。  
  * **ぬりかべの配置:** ぬりかべを入り口にハメ込み、物理的に「蓋」をする。  
  * **押し合いバトル:** \* 外側から敵（一般兵）が群れで押し寄せる。集団推力計算（![][image1]）により、敵が20体を超えると推力4.8となり、プレイヤー1人の力では押し返せなくなる。  
    * 河童が内側から「超・突っ張り（推力10）」を放ち、溜まった敵をまとめて弾き飛ばすカタルシスを提供。

### **Phase 4: 動力タービンの属性点火**

* **環境:** スイッチをソケットに設置したが、出力不足。3つのタービンを回す必要がある。  
* **ギミック（属性シナジーの活用）:**  
  * **タービン起動:** 1\. 天狗がタービン前に「風の柱」を生成。  
    2\. 鎌鼬が「諸刃の剣」を風に当て「斬撃の柱」に。  
    3\. 妖狐が「火の玉」を風に当て「火柱」に。  
    4\. 河童が「消火活動」を風に当て「水柱」に。  
  * これら3種類の属性柱を完成させることでタービンが最大回転し、電力が復旧する。

## **3\. 物理・パラメータ設定（調整変数）**

| オブジェクト | 重量クラス | 推力閾値 | 特記事項 |
| :---- | :---- | :---- | :---- |
| **ぬりかべ** | 極重 | 1.1 | プレイヤー1人の体押し(1)では動かない。2人以上またはスキルが必要。 |
| **巨大スイッチ** | 極重 | 1.1 | ぬりかべと同一設定。特定の溝にハメ込む判定を持つ。 |
| **一般兵(Tier1)** | 軽 | \- | 推力1.0。K=0.2の減衰補正を受ける。 |

## **4\. デッドロック（進行不能）回避策**

* **NPCの自律行動:** プレイヤーが属性パズルに気づかない場合、対応するNPCがスタンプ（Need Fire等）を出し、ターゲット付近で待機する。  
* **魂移しによる自己解決:** プレイヤーが1人でも、キャラクターを切り替えることで「天狗で浮かせて、河童で押す」といった連携を完遂できる。  
* **魂のオーブ:** 激しい防衛戦で仲間がロストしても、敵を10体倒すごとに「魂のオーブ」が出現。30秒のリスポーン短縮により、少人数での詰みを防止する。

## **5\. カオス（ドタバタ）の意図的配置**

* **ぬりかべの暴走:** 河童がホバー状態のぬりかべを突き飛ばした際、勢い余って味方NPCを壁との間に挟んでダウンさせてしまう「事故」が発生しやすい幅で通路を設計する。  
* **引火連鎖:** 妖狐が火柱を作ろうとして外した「火の玉」が、運搬中の味方NPC（一反木綿等）に引火し、河童が慌てて消火に走るドタバタ劇を促す。

[image1]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAKMAAAAYCAYAAACWYU02AAADPklEQVR4Xu2ajXHbMAxGNUNX6AxZoSt0ha7QFbJBR+gI3aAbdIMskAFavWu/BEEAEWQt2fLx3fFiU/wBoI8gJWdZJpPJZDKZTA7l+1q++co74cdaPvjKye3yy1cUOcNNRoyj/p2Kz2v5uZZHf6HB81qelr/9+Ms41+BhLb99peHTv0Ibype3l5ePy+t14sDno2FBYBc2ZPNz/VoxZsfBNu41n1kYxL0FsaUtcaUwBlp5B4MiKIomqkLAmERZhb+MUTHQM9LHgpP40ILsIsFFbAl6T3STdB8yMUJ4Iw8AuzgGCSWiFrSx/fje1EmPGLNMpMzTy0gfS9O55fW6so8NkKj6vxfKzlti5PrR2/XX5f0Clq0tIk1gP3VkzZAeMdLOTwDRxBVG+lgqWZGAiszOlqD3piJG3cgK1ueIqr/aYi2ytTVGFGvtBKmfPWLUYJ5o4gojfQQ2V+y2go3srIyxNxUxKrNXwW9/RoasPiLShmz19RW0VV8kM2owT8+qtYz0EWy3raD6YwV9+G4fBvzKvwYVMVa3R4HvfuegrhUzgWAibegZwde3kP3RMemFnoFpGwUky5gtRvoI5ty6ecCDi/fNnlsQpb9h16AiRhiJlzJhT0aErQxIfe97Xfo0Y51NGJFlxpYYOcNoW7VFc/tSeY1RESPO+zba7vSiHMFW8XZulZ53mHuKURmyR4iQZUbI6jOwAe20zpldA+sViYe6qL7FSB9REWO2BcteSjNAB7CnGEczI9DHx1C2VsdiJ7JvAYh3ulB7xEi7KCC3KsbML9nb3DYOoiJGtenBnhGjM2QLYpyJsbKIEZ0/I0a71QtbYoxEhnF+G6WNN7qCH7sHbYcZ3kaLAtp6BXIUFTHqeFEly4RZfYQeVmwm00OgRb/S2JjTjrns0UV1b9DPYMp0EpgPRiRGVsSlfoHxY/eArdF5D9/0CxPOe58EPozYfEmYX34QC9kb2aUbXmHrqbk3Q8ou+/3JfAf0Q71NDtKOLyNJaxMEqIeSzOkK1eBmjC6CM7LLjSzCAm/tRKfnEmLsfcVwVvD1fxb+ZGdGDvVnBBFmx43JDcHWkf68dAdwJOo5402uzPxP78lkMpncAX8AHDEyrA7KuK8AAAAASUVORK5CYII=>