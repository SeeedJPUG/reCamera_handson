# reCameraのハンズオン資料
_2025.05.05版_

# 目次
- [はじめに](#はじめに)
- [デバイス仕様](#デバイス仕様)
  - [同梱物の確認](#同梱物の確認)
  - [電源投入とインジケーターライト](#電源投入とインジケーターライト)
- [ネットワーク接続方法](#ネットワーク接続方法)
  - [1. USB接続を使用する方法](#1-usb接続を使用する方法)
    - [UsbNcm Host Deviceについて](#usbncm-host-deviceについて)
    - [Windowsでのネットワーク接続問題](#windowsでのネットワーク接続問題)
  - [2. Wi-Fi接続を使用する方法](#2-wi-fi接続を使用する方法ワイヤレス機能付きモデルの場合)
  - [3. Ethernet接続を使用する方法](#3-ethernet接続を使用する方法)
- [初期設定](#初期設定)
- [OSの更新](#osの更新初期バージョンのrecamera-をお持ちの場合)
- [reCameraでよく使用するURL](#recameraでよく使用するurl)
- [Node-REDダッシュボードの使用](#node-redダッシュボードの使用)
  - [Node-REDプログラミングの基本](#node-redプログラミングの基本)
  - [reCameraのNode-RED環境で使用できるノード群](#recameraのnode-red環境で使用できるノード群)
    - [ダッシュボードノード](#ダッシュボードノードダッシュボード2)
    - [reCameraノード](#recameraノード)
    - [reCamera gimbalノード](#recamera-gimbalノード)
    - [VisionAIノード](#visionaiノード)
    - [sscmaノード](#sscmaseeed-sensecraft-model-assistantノード)
- [Node-REDフローの作成方法](#node-redフローの作成方法)
  - [SenseCraftの登録方法](#sensecraftの登録方法)
  - [reCameraサンプルアプリケーション](#recameraサンプルアプリケーション)
- [複数のreCamera デバイスの区別方法](#複数のrecamera-デバイスの区別方法)
- [カメラの向き](#カメラの向き)
- [開発方法](#開発方法)
- [補足、注意事項、トラブルシューティング](#補足注意事項トラブルシューティング)
  - [デバイスのリセット方法](#デバイスのリセット方法)
- [まとめ](#まとめ)
- [参考リンク](#参考リンク)

# はじめに
> 最終更新: 2025.05.06

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505020335.png)

この記事は**reCamera（リカメラ）**のハンズオンの内容です。

ここでは、デバイスのセットアップから基本的な使い方まで、ステップバイステップで説明します。初心者向けにAIビジョン機能を活用するための手順を紹介します。

この内容は以下のリンクの内容を日本語化し、加筆などを行ったものになります。詳細は以下のリンクの内容も御覧ください。

https://wiki.seeedstudio.com/recamera_getting_started/

なお、私が購入したものは有線タイプの**reCamera 2002 64GB**なので、以降の内容でWiFiモデルのことについては参考元の資料からの引用となりますのでご了承ください。

**reCamera**は、プロセッサとカメラセンサーを組み合わせたスタンドアロンシステムで、デバイスのみでAIによる検出や制御タスクを処理することができます。また、**RISC-V SoC**を搭載し、カメラ性能が**5MP@30FPS**、**1TOPS**という処理速度のパフォーマンスがあります。

# デバイス仕様

デバイスの仕様は以下のようになっています。

| 項目 | 仕様 |
|------|------|
| プロセッサー | SOPHGO SG2002 RISC-V SoC |
| CPU | C906 (1GHz) + C906 (700MHz) |
| MCU | 8051@8KB SRAM |
| AIパフォーマンス | 1 TOPS (INT8) |
| メモリ | 256MB |
| ストレージ | 8GB/64GB eMMC（モデルによる） |
| カメラ | 5MP、30FPS |
| 接続 | USB Type-C、イーサネット、Wi-Fi（2.4GHz/5GHz）、Bluetooth 4.2/5.0（モデルによる） |
| 電源 | USB Type-C |
| ソフトウェア | Linux OS、Node-RED、SenseCraft AI |
| サポートAIモデル | YOLO v5/v8/11 など |## 同梱物の確認

**reCamera**には以下の内容物がはいっています。揃っていることを確認してください。

- **reCamera**本体
- USBタイプCケーブル
- その他の付属品

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505014952.jpg)

## 電源投入とインジケーターライト

付属のUSBタイプCケーブルを使用して、デバイスの電源を入れます。緑色のLEDが点灯することを確認してください。

**LEDの点灯色の意味**

- 中央の緑色のライト　…　電源オン
- 点滅する赤色のライト　…　CPUのハートビート
- 青色のライト　…　eMMCのステータス（読み書き時に点滅）

## ネットワーク接続方法

**reCamera**をネットワークに接続するには、デバイスによって以下の方法があります。

### 1. USB接続を使用する方法

**reCamera**をUSBケーブルでPCに接続し、ブラウザから`192.168.42.1`にアクセスします。

PCが**NCMドライバー**を正しく認識していることを確認してください。

**確認方法**

Windowsの【デバイスマネージャー】から【 ネットワークアダプター】の項目を確認します。

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504122313.png)

<span style="color:red">※ドライバーが見つからない場合は、次の手順でインストールしてください</span>

1. 【Win+X キー】を押して【デバイスマネージャー】を選択
2. 【その他のデバイス】内の認識されていないデバイスを見つけて【ドライバーの更新】をクリック
3. 【Microsoft】から【UsbNcm Host Device】を選択し、【次へ】ボタンをクリック
4. ダイアログでアラートが表示されたら【はい】ボタンをクリック
5. **cmd**などのターミナルウィンドウで`ping 192.168.42.1`と入力して接続確認

**ipconfigでのネットワークデバイスの確認**

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504122537.png)

**pingコマンドでの疎通確認**

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504122544.png)

### **UsbNcm Host Device**について

**UsbNcm Host Device**は、USBネットワークコントロールモデル(**NCM**)デバイス用のWindowsドライバです。**NCM**はUSB-IFによって定義された規格で、USBを介してイーサネットパケットを転送するための最新のUSB標準通信インターフェースです。このドライバにより、PCと**reCamera**間でUSB経由のネットワーク接続が可能になります。Windows11では標準でこのドライバがサポートされており、USBケーブルで接続するだけで自動的にネットワークアダプターとして認識されます。

### Windowsでのネットワーク接続問題

**reCamera**のUSBネットワークを使用した後、Windows11でネットワークアクセスに問題がある場合

1. 【Win+X】を押して【コントロールパネル】をクリック
2. 【すべてのコントロールパネル項目】→【ネットワーク接続】を選択
3. 【UsbNcm Host Device】を右クリック
4. 【プロパティ】→【TCP/IPv4】→【プロパティ】→【詳細設定】→【自動メトリック】のチェックを外す→【インターフェイスメトリック】フィールドに【255】と入力→【OK】ボタンをクリック

### 2. Wi-Fi接続を使用する方法（ワイヤレス機能付きモデルの場合）

電源投入時、**reCamera**は**アクセスポイント(AP)**を有効にしてネットワーク設定を変更できるようにします。スマートフォンやラップトップのWi-Fiリストを開くと、`reCamera_******`というWi-Fi名が表示されるはずです。APの命名規則は`recamera_+ デバイスのMACアドレスの下6桁`となっています。デフォルトのパスワードは`12345678`です。

このAPに接続すると、ウェブサイトが自動的にポップアップ表示されます。表示されない場合は、ブラウザのアドレスに`192.168.16.1`と入力してページにアクセスできます。ここで**reCamera**を接続したいWi-Fiを選択できます。

### 3. Ethernet接続を使用する方法

Ethernetポートを使用する場合は、付属のケーブルを使用してルータに接続できます。デバイスの電源はタイプCケーブルによる接続が必要です。カメラの背面のネジを外して接続します。
<span style="color:red">ネジは6角なので別途工具が必要になりますのでご注意ください。</span>

また、デバイスのIPアドレスはDHCP経由で取得されるので、各々のIPアドレス体系となりますのでご注意ください。

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504121150.png)

## 初期設定

ブラウザから**reCamera**のIPアドレス（`192.168.42.1`など）にアクセスし、パスワードを変更してください。このパスワードを忘れないようにしてください。
<span style="color:red">※忘れた場合は、デバイスを工場出荷時の状態にリセットする必要があります。</span>デフォルトパスワードはユーザネームと同じとなっています。

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504123507.png)

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504123516.png)

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504123511.png)

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504123521.png)

## OSの更新（初期バージョンのreCamera をお持ちの場合）

初期バージョンの**reCamera**をお持ちの場合は、次の手順に従って最新バージョンのOSにアップグレードしておきます。

1. 管理ページの【三】をクリックし、サイドバーの中にある【System】をクリックし、【Update】の項目にある【Software Update】の【Check】ボタンをクリック
2. OSバージョンを確認し、【チェックして適用】ボタンをクリック
3. システムの更新が完了するまで数分待つ
4. デバイスを再起動して管理ページのURLを更新
5. 初期パスワード（recamera）から新パスワードへの変更ダイアログが表示されるので、パスワードを変更
6. 管理ページの【三】をクリックし、サイドバーの中にある【System】をクリックし、【System Info】のOSが変更を確認

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504125623.png)

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504125627.png)

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504125630.png)

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504130122.png)

これでデバイスへの接続準備が整いました。

# reCameraでよく使用するURL

以降使用する可能性のあるリンクを一覧にしておきます。

| ページ | URL |
|---|---|
| <span style="color:red">**ホームページ**</span> | `http://<ip_address>/#/init` |
| <span style="color:red">**ワークスペース**</span> | `http://<ip_address>/#/workspace` (OSバージョン0.1.4以上) |
| **ネットワーク設定** | `http://<ip_address>/#/network` |
| **セキュリティ** | `http://<ip_address>/#/security` |
| **ターミナル** | `http://<ip_address>/#/terminal` |
| **システム** | `http://<ip_address>/#/system` |
| **電源** | `http://<ip_address>/#/power` |
| <span style="color:red">**オリジナルNode-RED**</span> | `http://<ip_address>:1880` |

# Node-REDダッシュボードの使用

**reCamera**は**Node-RED**との統合していて、ビデオストリームのプレビュー、モデルの変更などができる組み込みダッシュボードUIの例を提供しています。これをカスタマイズして独自のアプリケーションと統合することができます。

ネットワークに接続されていれば、ブラウザで**ip_address:1880（`192.168.42.1:1880`など）**にアクセスして**Node-RED**上でダッシュボードを表示できます。ここでモデルのプレビュー、他のモデルへの切り替え、または便利なノードを使用して必要なアプリケーションを構築することができます。

reCamera内のNode-REDへのアクセス（USB接続のデフォルトのIPアドレスの場合　http://192.168.42.1:1880/ ）

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250504/20250504130435.png)

## **Node-RED**プログラミングの基本

**Node-RED**は、データの収集、変換、視覚化を行うアプリケーションを構築できるローコードプログラミングプラットフォームです。その直感的なフロー型プログラミングにより、プログラミングの経験がなくても、ドラッグ＆ドロップで簡単にアプリケーションを作成できます。

**参考**

https://nodered.org/

## reCameraのNode-RED環境で使用できるノード群

### **ダッシュボードノード（ダッシュボード2）**

- ボタン、スライダー、スイッチなどのUIコンポーネント
- データの視覚化
- ユーザー入力の受け取り

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505002615.png)

### **reCameraノード**

‐ reCameraのLED制御

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505004315.png)

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505002619.png)

### **reCamera gimbalノード**

‐ reCameraに接続するジンバルを制御
‐ モーターの角度・スピードの制御及び情報の取得

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505002621.png)

### **VisionAIノード**

   - カメラの制御（オン/オフ）
   - ビデオストリームの提供
   - 異なるAIモデル（YOLOなど）の読み込み
   - モデルパラメータの調整
   - 物体検出や認識処理

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505002626.png)

### **sscma（Seeed SenseCraft Model Assistant）ノード**

‐ デバイス情報の表示など

このノードはサブフローによって構成されています。

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505002629.png)

# Node-REDフローの作成方法

1. ブラウザでデバイスの`ip_address`にアクセスし、ワークスペース（`http://<ip_address>/#/workspace`）に移動
2. 左側のパレットからノードをドラッグ＆ドロップ
3. ノード間を線で接続してデータの流れを作成
4. ノードをダブルクリックして設定（プロパティ）を変更
5. 右上の【Deploy】ボタンをクリックして変更を適用

**ワークスペースを開いた様子**

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505010630.png)

※開発時にはワークスペースの左下に【My Application】のした部分にある【Login to SenseCraft】ボタンをクリックしてログインをしてください。

## SenseCraftの登録方法

【Login to SenseCraft】ボタンをクリックして、ログインのダイアログの【Sign Up】ボタンをクリック

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505012110.png)

ユーザ情報の`*`のついた項目を入力します。【Verification Code】の欄はメールアドレス入力後に【Get captcha】をクリックしてメール宛てに届くコードを入力してください。入力が完了したら【Sign Up】ボタンをクリックしてください。

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505011816.png)

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505011950.png)

## reCameraサンプルアプリケーション

これらのサンプルプロジェクトは**sscma（Seeed SenseCraft Model Assistant）**のサイトにありクローンして使用する事ができます。

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505004921.png)

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505004926.png)

このサンプルでは以下の様な機能を持っています。

- モデル出力のプレビュー
- 人、犬、猫、ボトルなどのカウントデモ
- 異なるモデルへの切り替え
- カメラストリームの表示

**実行の様子**

![image](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20250505/20250505014031.png)

# 複数のreCamera デバイスの区別方法

複数の**reCamera**を使用する場合は、デバイスの背面にあるMACアドレスまたはS/N番号で区別することができます。または、USBまたはAPを介してデバイスをラップトップに接続すると、コマンドを使用してMACアドレスを表示することもできます。

# カメラの向き

デフォルトのカメラビューの向きは、タイプCコネクタが下向きとる状態です。トレーニングしたモデルの精度に影響する可能性があるため、カメラの向きに注意してください。

# 開発方法

**reCamera**には安定した**reCamera OS**が搭載されており、ファームウェアのOTAアップデートをサポートしています。さらに、**Node-RED**と**Sensecraft AIプラットフォーム**がデバイスに統合されており、より簡単な展開が可能です。この統合により、初心者の開発者は**Node-RED**でノードをベースとしたモジュール式のプログラミング体験を得ることができます。上級開発者には、Linuxベースの深い開発とPythonも用意されています。

# 補足、注意事項、トラブルシューティング

## デバイスのリセット方法

デバイスのパスコードを忘れた場合などでデバイスをリセットしたい場合は、ユーザーボタンを長押ししながらデバイスを電源に接続します。デバイスの赤いライトが点滅ではなく常時点灯したら、ユーザーボタンを離します。

# まとめ

このガイドでは、**reCamera**の基本的なセットアップと使用方法について説明しました。**reCamera**は強力なAIビジョン機能を備えたオープンソースカメラプラットフォームで、さまざまな用途に活用できます。詳細については公式ドキュメントを参照してください。

# 参考リンク

- [商品ページ](https://www.seeedstudio.com/reCamera-2002-64GB-p-6252.html)
- [クイックスタートガイド（Seeed wiki）](https://wiki.seeedstudio.com/recamera_getting_started/)
- [ユーザーマニュアル](https://files.seeedstudio.com/wiki/reCamera/recamera-user-manual.pdf)
- [データシート](https://files.seeedstudio.com/wiki/reCamera/reCamera_one_pager.pdf)
- [reCamera Github](https://github.com/Seeed-Studio/OSHW-reCamera-Series)
- [reCamera OS Github](https://github.com/Seeed-Studio/reCamera-OS)

# 販売ページ

* [スイッチサイエンス reCamera 2002 64GB](https://www.switch-science.com/products/10092)
* [スイッチサイエンス reCamera 2002w 64GB](https://www.switch-science.com/products/10227)
* [秋月電子 reCamera 2002 64GB](https://www.marutsu.co.jp/pc/i/3005449/)
* [マルツ reCamera 2002 64GB](https://akizukidenshi.com/catalog/g/g130258/)

---

*このチュートリアルは https://wiki.seeedstudio.com/recamera_getting_started/ の情報を日本語化および加筆を行い作成されています*
