# **NightParade プレイヤーキャラクター仕様書（完全版・マスター）**

## **1\. 基本コンセプト**

* **魂移し（ソウル・スワップ）:** プレイヤーは1体の固定操作ではなく、4体1組の群れを「魂」としてコントロールする。プレイヤー不在枠はNPCとして自律行動する。  
* **UIレスな状態表現:** モーション、VFX、SEで状態を伝え、没入感を最大化する。  
* **物理干渉の連鎖:** 敵・味方・オブジェクトが物理演算で相互に影響し合うカオスな戦場。  
* **広々とした乱戦空間:** 乱戦と運搬を両立するため、マップの主要路は最低でもキャラクター4体が横に並んで通れる幅（約4キャラ分以上）を確保して設計する。

## **2\. 属性・状態異常の定義**

本作の基本属性は「火・水・風・斬」の4種に限定し、内部処理では「デバフ層」と「物理層」を明確に分離して管理する。

### **2.1 属性フラグ管理**

* **火 (Fire):** 炎上状態。継続ダメージを与え、周囲に延焼する。  
* **水 (Water):** \* **極低温 (Extreme Cold / Debuff Layer):** 主にプレイヤー対象。移動速度低下 ＋ **スタミナ消費増大**。  
  * **濡れ (Wet / Physical Layer):** 全オブジェクト対象。**地面の摩擦係数低下（スライド距離延長）** ＋ 属性耐性変化。  
* **風 (Wind):** 浮遊状態。重力の影響を無効化し、摩擦をゼロにする。  
* **斬 (Slash):** 装甲破損。防御力を低下させ、被ダメージを1.5倍にする。

## **3\. アクションとスタミナ管理（予兆表現）**

スタミナバーは表示しない。以下の3段階の予兆と、スキルの消費量を完全に連動させる。

1. **余裕（100-30%）:** 通常走行・アクション可能。  
2. **警告（30-10%）:** 足元から上がる土煙（VFX）が増え、足音が「ドタドタ」と重くなる（SE）。  
3. **限界（10%以下）:** 肩で大きく息をし始め、画面縁が僅かに明滅（ビネット演出）する。  
4. **枯渇 (0%):** 「膝つき疲労モーション」に移行。約2秒間、移動以外の全アクションが不可。

## **4\. 魂移し（キャラクターチェンジ）**

プレイヤーは4体1組のパーティをシームレスに切り替えて操作する。

* **操作権移譲:** 十字キーの各方向に割り当てられたキャラへ瞬時に憑依（上：天狗、左：河童、右：妖狐、下：鎌鼬）。  
* **カメラワーク:** 切り替え時、カメラは瞬時にターゲットを追尾するが、0.1〜0.2秒の補間（Lerp）を挟んでスムーズに遷移させる。  
* **NPC化:** 操作を離れたキャラは自律AI（追従・支援・蘇生優先）に切り替わる。  
* **強制移譲:** 操作中のキャラが幽霊になった場合、自動的に最も近い生存キャラへ操作権が移る。

## **5\. フレンドリーファイア（FF）と衝突判定**

* **斬属性FF:** 全員が味方の「斬」属性でダメージを受ける。  
* **衝突判定:** プレイヤー同士は、重量に関わらず常に「押し退け」が可能。

## **6\. 物理オブジェクト（重量級）の移動ロジック**

### **6.1 移動の優先順位と計算式**

1. **通常状態:** 摩擦が極めて高い。  
   * **体押し（推力1）:** 本当にゆっくり動く。  
   * **集団での体押し（減衰補正）:** ![][image1] （N=参加人数、K=0.2）  
   * **妖狐の爆弾（推力5）:** 約1mスライド。  
   * **河童の突っ張り（推力10）:** 約3mスライド。  
2. **ホバー状態 (天狗の風):** 摩擦ゼロ。天狗の継続風(推力2)で運搬可。  
   * **河童の突っ張り（推力10）:** **約10m高速スライド**。  
   * **鎌鼬の瞬身:** 正面衝突で「ブレーキ」および「反対方向への弾き出し」が可能。

### **6.2 属性シナジー**

* **風 × 斬 \= 斬撃の柱 / 風 × 火 \= 火柱 / 風 × 水 \= 水柱 / 火 × 水 \= 蒸気爆発**

## **7\. 死亡とリスポーン（幽霊状態とロスト）**

### **7.1 第一段階：ダウン（幽霊状態）**

* **魂の乖離:** 本体が地形に引っかかり、幽霊との距離が5m以上離れると、幽霊が消滅しロストする。

### **7.2 第二段階：ロスト（完全死亡）**

* **復帰方法:** 120秒経過、または「魂のオーブ」を収集することで降下リスポーン。  
* **魂のオーブ:** パーティの合計撃破数が一定値（例：敵10体）に達するごとに1つ生成。復帰タイマーを30秒短縮。

## **8\. キャラクター個性（妖怪別詳細）**

### **8.1 河童 (Kappa)**

* **基本推力:** 10 (極強) / **固有アクション:** 超・突っ張り（物理衝撃）、消火活動（水属性）。

### **8.2 天狗 (Tengu)**

* **基本推力:** 2 (継続) / **特性:** 落下キャンセル / **固有アクション:** 風の柱（ホバー）、大旋風（吸い寄せ）。

### **8.3 妖狐 (Yoko)**

* **基本推力:** 5 (爆発) / **固有アクション:** 爆弾（デコイ・推力）、火の玉（属性）。

### **8.4 鎌鼬 (Kamaitachi)**

* **基本推力:** 0  
* **固有アクション:**  
  * **瞬身 (Blink Dash):** 移動5.0m、消費30%（4回で枯渇）、CD0.5s、無敵0.3s。  
  * **諸刃の剣 (Razor Edge):** 装甲剥がし、風の柱へのシナジー起動。

## **9\. カメラ仕様**

### **9.1 完全フリーカメラ (Complete Free Camera)**

* **360度自由旋回:** 右スティック操作により、キャラクターの進行方向とは独立して周囲を自由に確認可能。  
* **非拘束移動:** 移動入力中も、カメラを回転させるだけではキャラクターの向きは変わらない。攻撃やスキル発動時にのみ、カメラの正面方向へ瞬時に向きを補正する。  
* **カメラリセット:** スティック押し込み（R3等）により、視点を即座にキャラクターの背後（進行方向）へ戻す。  
* **オートリターン:** 「瞬身」や「超・突っ張り」などの直線的なアクション発動時は、操作性を高めるため、カメラが自動的に進行方向を向くよう滑らかに追従する。

### **9.2 動的追従とフィードバック**

* **視野角 (FOV):** 重量物運搬時や乱戦中は自動的にFOVを広げ、周囲の状況を把握しやすくする。  
* **画面シェイク:** 強力なアクションヒット時に重量に応じた振動を発生させ、質量感を伝える。

### **9.3 ダメージ・スタミナ演出**

* **ビネット:** スタミナ限界時に画面端が暗くなる。  
* **彩度低下:** 被弾時に彩度を下げ、残像をかけることで妖怪特有のダメージ感を表現。

### **9.4 魂移しフォーカス**

* 切り替え直後、正面の重要ターゲットを画面中央に捉えるソフトアシスト。

[image1]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAQMAAAAYCAYAAAD+t5gnAAAFTklEQVR4Xu2aDZHcOBCFB8NRCIZQCIWjsBSOQhgEQiCEwTE4BiEQAJf5kryL7lW3pLY9npldfVWuLduypP57kj17uSwWi8VisVhU+Pt6vPjFIn9dj3d+8crX6/HFLwbQjnk4PNvO7dP1eN+cz0LfzHGxWCRQJP9efhbdH3bvCOj7g18M0DwcrrUi8+2ybZ5R34vbQHz+8YuvBBajLfn3FFAkrMoU7MwKXmWPGERJ9dHOZ8HGxTkQM4omo1dMvXuPgufkPTjcT58v/y8uBhi9LtCGwo0OitkdVRUDxlci+W6lFSvaRK8VGVlb7N/y2nELsBXRclEcIV9gCzsnj8GZ8CqWxRv7uMf8sNF3fUAsdJ+294iN/JhBjlZjdDT4DV9rV4+vXCDkY+abxeQHmUE4YfRu7YP2GImBhIRxaau/GMvfVmy41wqPz78nVO2z7aEx743siezqQeK27Smera9SPOPFWYHnZ3xJGxKYeUc7varQH4VESDnY4xF2mor9i9/4BXb86RcdEgZnZwlD5zimosoEN2rvYsAEo3YqTOEBoY9RgDJ4Nko6yBLyXlTFgLZeONi7JVnxg/dVgTFnxEBjZLZ6zpyNC2xE9Ap7Nl4jgjj0XtP+QytHhj6QKFDZSpGtsIiCX8dp7XlkwC3FgOBmux36fI1iwPVIdHvsFQPGbF/lMuTvzNZefp7BjBgAbbL6aOF1vBeLl0uenz0i/9EXxxAKXZPKdgUtrSgQ5N4zmcE8O6PyEgMMYZ4KiEREW7iRqETQPps7fTy7GPj8JQZ+fcQRYjCKdZsjxJpnyJ2WLYVxJBUxmFqBLz9tjeojuz5Dmyuq1am+KOYpxTCyLaeKsi3OSNFnEgQYg7a842CQB2TPzqD33Oz8gHaVIxOgHm2AR+i7ihe9EsOvjzhCDEYrpRe64t76aiqhb4jnXgZtKv7yV2TqMaqZWRifRZJc0066FHNfcbNDA+1lVGwaR2MKDwh9zATIYdXpPTea39kwn958W+STKAG4PrtqiSPEYIQnv4QLUYDqnG+B515GJVYCAUAQ9HcriKr8JgEuz4eVd+aBUTL5CsDkIuNGxSbBqYrB7AdOkk+JFjHjizOpBDTbGUB2HShA7vlBDPCVX+eY8fVo3vQRtWltrixA5JzPs3fMQttonk4lVi3kpItiFXb6jN2+YpXn40WVQZteEft9zqOPR94uoyoGM1Aso+Qa3T+bakBp66u5fMXqUwGfe18VRvMmP6KiVOwp7r1FcgSeexnVWMEROwOJqi/W2i34N5iU2aIaFbHf5zwKtLfLuIUY0GfP4ayQs8mPsNC2cgx/4w2oJhht3Qb5qmd7xK3FIMuFl8tvu6MF5Ww89zJo09t1OtjZip1/Q5hFRe/5pf/z4Jj6XlUxNAqc8PucP5IY0Bbn90BZ3aH3RkURwXw9CfwnWPD/LJ3lCDHoxbrXt+K9pTiOxnMvgza+OmeQi5Ft2fUevRzRrmFKpCqG9gLr9zmPEtDbZVTFACdmBnNvJATA81MKegLYJxvlM/ebfNT6WauEUDJssesIMWA+DuKFQCFcjBHNTavaPZHP9TM2tnDu38cEbWbyzHcEzuwOYZQj9KF80P1s7j/wIsvwpHM0WHseJdKoHyarObXFPTvPFhKKpIuSTbTBrvb/yOAvjj07nb1icMSHsWeh8or5sPhKkkGbXmL5ewnvepFKUuC+wkXQrlVH+qo4mzFmt2z6sNiz7y2yVwy2CPizQh1F+b5YLH6BIMyI/zODCLwV0VssdvFoP9ceDbvi2V3oYvGm4fXxtQoCItD7JrVYLBaLxWLxJHwHdW4ryXRn9IoAAAAASUVORK5CYII=>