#  欧州の山火事対策を加速する Sentinel-3 の迅速データ提供（NRTデータ配信）

## 概要 (Summary)

気候変動に伴い欧州各地（特に地中海沿岸部など）で頻発・激甚化する山火事（Wildfires）に対し、欧州宇宙機関（ESA）と欧州委員会が推進するコペルニクス計画の地球観測衛星**「Sentinel-3（センチネル-3）」**が、これまで以上に迅速なニアリアルタイム（NRT: Near Real-Time）観測データの提供を開始しました。

火災発生の検知から消防・防災機関へのデータ配信までのタイムラグを極限まで短縮することで、初期消火活動の効率化や住民避難判断の迅速化に直結する宇宙インフラの運用高度化について記録します。

---

## 1. Sentinel-3 の観測ペイロードと火災検知メカニズム

<img width="2249" height="1080" alt="Wildfire_damage_east_of_Belgrade_Serbia_pillars" src="https://github.com/user-attachments/assets/045e5ec7-60d4-4be5-b814-27cafb03cb84" />


Sentinel-3は海洋・陸域の広域環境を高精度にモニタリングする光学・放射計衛星であり、火災検知においては主に以下のセンサーが中核を担っています。

| 搭載機器 | 正式名称 | 火災監視における役割 |
| :--- | :--- | :--- |
| **SLSTR** | Sea and Land Surface Temperature Radiometer（海陸表面温度放射計） | 専用の「火災検知チャンネル（FRP: Fire Radiative Power）」を備え、夜間・昼間を問わず熱源（ホットスポット）の温度と放出エネルギーを定量化。 |
| **OLCI** | Ocean and Land Colour Instrument（海洋陸域カラー測定器） | 21バンドの光学センサーにより、火災に伴う煙プローブ（煙霧）の拡散範囲や植生の乾燥度（燃焼危険度）を広域マッピング。 |

---

## 2. 「より速いデータ提供（Faster Data）」を実現した技術・運用パイプライン

従来の衛星データ配信では、衛星が地上局上空を通過しデータをダウンリンクしてから処理・配信されるまでに数時間〜半日を要する場合がありました。今回の改善により、以下のプロセスで**撮影から1〜2時間以内（一部は数十分レベル）**での配信が実現されています。

1. **地上局ネットワーク（Direct Downlink）の最適化**
   欧州高緯度地上局（スヴァールバル等）への高頻度ダウンリンクルートを再設計し、軌道上データ滞留時間を最小化。
2. **自動化されたエッジ／クラウドデータ処理パイプライン**
   受信した生データ（Level 0）から、火災放射電力（FRP）やアクティブファイアの位置座標（Level 2）をクラウド上で完全自動抽出・フィルタリング。
3. **EFFIS（欧州森林火災情報システム）への即時データフィード**
   コペルニクス緊急管理サービス（Copernicus EMS）および欧州森林火災情報システム（EFFIS）のダッシュボードへAPI直結でアラートを送信。

---

## 3. 防災・気候変動対策における実践的メリット

- **初期火災の早期発見**
  地上監視カメラや通報が届きにくい広大な森林地帯・山岳部における初期発火を宇宙からいち早く特定。
- **煙・有害微粒子（エアロゾル）の拡散予測**
  煙のプルーム高度や進行方向を大気質予測モデルに即座にフィードバックし、風下の都市部に対する大気汚染警告や航空管制の支援に寄与。
- **消火リソース（航空消防隊等）の最適配分**
  どの火線（ファイアフロント）が最も熱エネルギーを放出し拡大リスクが高いかを客観的数値（FRP値）に基づいてトリアージ。

---

## 4. 学びと所感 (Takeaway)

- 衛星の解像度（空間分解能）だけでなく、「観測から現場への到達速度（時間分解能・レイテンシ）」がいかに人命救助や防災オペレーションにおいて決定的なファクターになるかを再認識した。
- 地球観測（EO）ミッションの価値は、宇宙空間でのセンサー技術のみならず、地上側のデータパイプライン自動化・クラウド配信基盤（Space-to-Cloud）とのシームレスな統合によって最大化される。




<br><br><br>


## Summary

In response to the increasing frequency and intensity of wildfires across Europe, the European Space Agency (ESA) and EUMETSAT have implemented an enhanced data transmission strategy for the Copernicus **Sentinel-3** satellite mission. 

By restructuring how data is downlinked as the spacecraft passes over ground stations, the delivery latency for wildfire detection datasets over central and southern Europe has been drastically reduced from the standard 3 hours to **within 60–90 minutes** (up to three times faster), providing civil protection and emergency response teams with critical near-real-time (NRT) intelligence.

---

## 1. Sentinel-3 Payloads and Fire Detection Capabilities

<img width="2249" height="1080" alt="Wildfire_damage_east_of_Belgrade_Serbia_pillars" src="https://github.com/user-attachments/assets/045e5ec7-60d4-4be5-b814-27cafb03cb84" />

Sentinel-3 carries high-resolution optical, thermal, and altimetry instruments designed for comprehensive ocean, land, and atmospheric monitoring.

| Instrument | Full Name | Role in Wildfire Monitoring |
| :--- | :--- | :--- |
| **SLSTR** | Sea and Land Surface Temperature Radiometer | Features dedicated fire channels (FRP: Fire Radiative Power) to measure thermal radiation, pinpoint hot spots, and quantify the energy released by active fires both day and night. |
| **OLCI** | Ocean and Land Colour Instrument | Features 21 spectral bands to track smoke plume dynamics, aerosol dispersion, and assess pre-fire vegetation dryness (fuel conditions). |

---

## 2. Technical Strategy: How "Faster Data Delivery" Was Achieved

Standard global NRT delivery requirements for Sentinel-3 aim for data delivery within 3 hours of capture. To accelerate delivery for Europe during peak wildfire seasons, engineers revamped the orbit-to-ground downlink pipeline:

1. **Quasi-Simultaneous Capture and Downlink over Svalbard**
   - As Sentinel-3 orbits northward along near-longitudinal tracks, data captured over central and southern Europe was previously downlinked after the pass once in line of sight with the Svalbard ground station (Norway).
   - By modifying the data cut-off threshold and enabling **quasi-simultaneous data acquisition and downlink** while within Svalbard’s antenna visibility, the latency for European scenes dropped to **60–90 minutes**.
2. **Automated Cloud Processing Pipeline**
   - Raw data (Level 0) is instantly pre-processed into calibrated Level 1 and Level 2 Active Fire / FRP products in the cloud without human intervention.
3. **Integration with Copernicus EMS & EFFIS**
   - Output datasets feed directly into the **Copernicus Emergency Management Service (CEMS)** and the **European Forest Fire Information System (EFFIS)** dashboards, delivering immediate tactical feeds to firefighting authorities.

---

## 3. Practical Benefits for Disaster Mitigation & Civil Protection

- **Rapid Initial Triage & Containment**: Enables emergency services to detect isolated ignition points in remote or sparsely populated terrain hours earlier.
- **Dynamic Risk Mapping via Fire Radiative Power (FRP)**: Quantitative thermal metrics assist commanders in deploying airborne firefighting resources to high-intensity firefronts.
- **Air Quality & Plume Trajectory Forecasting**: Faster aerosol optical depth (AOD) measurements improve regional smoke dispersion models, protecting public health and safeguarding flight corridors.

---

## 4. Takeaway

- While spatial resolution is critical, **temporal latency (sensor-to-user lead time)** is the decisive factor in operational disaster response.
- Repurposing existing in-orbit satellite infrastructure through operational optimizations—without launching new hardware—demonstrates the immense agility of modern space software and data architectures.
