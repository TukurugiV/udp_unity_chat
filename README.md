# UnityでUDPチャット

## 機能
### チャット開始処理
- Unity画面上でlistenポートを入力(自分のポートを設定)
- Unity画面上でRemoteIPとRemotePortを入力(送信先のIPアドレスとポートを設定)
- Unity画面上でConnectボタンを押下(UDPクライアントを上記の情報で作成)
### 情報の変更処理
- チャット開始処理時に入力した情報から変更したい場合、変更したい箇所をの情報を変更し、それ以外はそのまま入力
- Unity画面上でChangeボタンを押下(UDPクライアントの再生成)
### メッセージの送信処理
- メッセージ入力枠に送信したいメッセージを入力(文字数の上限なし)
- Enterボタンを押下

## 内部処理
### パケットの構成
- 1セグメントにつき1024バイト
- ヘッダー部
	- \[0]:タイムスタンプ(long 8byte)
	- \[1]:セグメントナンバー(int 4byte)
	- \[2]:総セグメント数(int 4byte)
	- ヘッダーサイズ:16バイト
- ボディー部
	- メッセージ(1セグメント最大1008文字)
### 送信前処理
1. UDPクライアントのヌルチェック,送信先のエンドポイントヌルチェック
2. メッセージの取得
3. メッセージのバイト数を取得
4. バイト数より何セグメントに分けて送信するかを計算,決定
5. ヘッダーの作成
7. パケットの作成
8. 総セグメントの数分パケットを送信(メッセージを作成→送信　の繰り返し)

### 受信時処理
- udpClienct.BeginReciveにてコールバック関数を設定(非同期受信)
- データ受信後パケットを分析(事前に決まっているパケットの構成に沿って各種値を取得)
- タイムスタンプとメッセージの記憶用配列を総セグメント数を要素数として作成
- 受信した順番で先ほどの配列に順次代入
- この配列の要素全てに値が入った(not null)場合、すべてのパケットを受信できたとして、ボディー部を全て結合
- タイムスタンプは一番最後に受信したパケットの値を採用
- UIに表示するため、スクロールビューに追加













