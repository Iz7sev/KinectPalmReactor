# Kinect Palm Reactor / REACTOR: DRONE INVASION

Azure KinectとUnityで、両手の手のひらからレーザーを撃つヒーロー風シューティングゲームです。
手を開いてチャージし、画面のドローンへ照準を合わせて得点を競います。

「めざめる絵の図書館」とは独立したUnityプロジェクトです。

## ダウンロード

**KinectPalmReactor-UnityProject.zip** をダウンロードして展開してください。
Unity Hubへ追加するフォルダーは、展開後の `PalmGame` です。

## ゲームの内容

- **1人60秒、2〜6人が交代**でプレイし、最後にスコアを比較します。
- 左右の手を独立して認識。右手がシアン、左手がピンクの照準です。
- 手を開いたまま約0.45秒チャージすると、その照準位置へレーザーを発射します。
- 手を閉じる・下ろすと発射を止められます。敵がいない場所へ撃つとMISSになります。
- 通常ドローン100点、小型ドローン200点、金色ドローン500点。
- 発射・命中音、敵の出現予告、撃破時の衝撃波、火花、コンボ、残り10秒の演出があります。
- 全員のスコア、命中数、発射数、命中率を表示し、結果をローカルCSVへ保存します。

## 準備と起動

必要なものはWindows x64、Unity **6000.6.4f1**、Azure Kinect DK、USB 3.0接続、Kinectへの電源です。Meta Questは使いません。

1. [Azure Kinect Sensor SDK 1.4.2](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/releases/tag/v1.4.2)をインストールします。
2. Unityを開く前に、展開した `PalmGame` 内のPowerShellで次を実行します。

   ```powershell
   .\Setup-Dependencies.ps1
   ```

   インストール済みSDKからDLLをコピーし、MediaPipe Unity Plugin 0.16.3とGoogleの手モデル、必要な.NETライブラリを公式配布元から取得します。実行ポリシーなどで制限される場合は、PC管理者の方針に従ってください。
3. Unity Hubから `PalmGame` を開き、`Assets/Game.unity` を開いてPlayします。
4. 他のカメラ利用アプリを終了し、Kinectへ手のひらを映します。2〜3mでの利用を想定しています。
5. 参加人数と名前を設定し、**START MATCH**を押します。
6. 3秒のカウントダウン後に開始。終了したら次の人に交代し、**NEXT PLAYER READY**を押します。

## カメラとプロジェクター

- `SILHOUETTE`: 深度シルエット表示。オフにするとカラー映像です。
- `CAPTURE EMPTY BACKGROUND (5s)`: 押して5秒間全員がカメラの外へ出ると、静止した家具などを背景として除外します。
- `SILHOUETTE RANGE`: 深度の対象距離。初期値3.8mです。
- `REACH`: 小さな手の動きで画面端まで照準を動かす倍率。全員同じ条件で調整してください。

画面に向かって立ち、Kinectを画面側からプレイヤーへ向けます。画面の敵に実際に手を届かせる必要はありません。
手を認識する画像は960×540、最大2つの手を処理します。照明や距離、設置位置によって追従が変わります。
`MOUSE PRACTICE` をオンにすると、クリックで射撃を確認できます。

## 結果の保存

各対戦の結果をUnityの `Application.persistentDataPath/ShootingResults` 以下へCSVで保存します。
公開ZIPには個人の名前、過去の結果CSV、カメラ映像・録画・背景深度データを含めていません。

## 主なコード

| ファイル | 役割 |
|---|---|
| `Assets/Scripts/TargetGame.cs` | 画面、敵、レーザー、得点表示、演出、CSV |
| `Assets/Scripts/ShootingRules.cs` | 1分間の対戦、チャージ、照準、深度判定 |
| `Assets/Scripts/MediaPipeGunPose.cs` | 両手・手のひらの認識。初期試作時の名前を保持 |
| `Assets/Scripts/GunPoseRules.cs` | 手の形の判定 |
| `Assets/Scripts/AzureKinectInput.cs` | Azure Kinectのカラー映像と深度入力 |

## 検証と依存ソフト

公開パッケージのC#実行時スクリプトはコンパイル済み、対戦・手・両手チャージ・投影・シルエットの**42項目のテスト**を実行済みです。
テストのソースは `Tests` に含めています。.NET 8 SDKで `dotnet run --project Tests/RulesTests.csproj` を実行できます。
公開パッケージを新しいPCへ展開してからの依存ファイル全件ダウンロードと実機起動は未検証です。

第三者のSDK、モデル、大容量パッケージは同梱せず、セットアップ時に取得します。利用条件は `THIRD_PARTY_NOTICES.md` を参照してください。
このゲームはオリジナルのドローンとUIで構成したヒーロー風の試作です。Marvelの公式作品ではなく、キャラクター画像やロゴを同梱していません。
公開だけで任意の再利用ライセンスを付与するものではありません。

関連作品: [めざめる絵の図書館](https://github.com/Iz7sev/EnchantedSilhouetteLibrary)
