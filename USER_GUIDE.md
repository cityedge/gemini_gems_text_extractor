# Gem Text Extractor v1.0.0 User Guide

Gem Text Extractor は、Google Drive に保存された Gemini Gem のファイルから、人間が読みやすい UTF-8 テキストを取り出すためのローカルブラウザツールです。

---

## 目次

- [このツールでできること](#このツールでできること)
- [なぜ普通のテキストエディタでは文字化けして見えるのか](#なぜ普通のテキストエディタでは文字化けして見えるのか)
- [基本的な使い方](#基本的な使い方)
- [画面の項目](#画面の項目)
- [抽出される内容](#抽出される内容)
- [TXT保存](#txt保存)
- [UTF-8 BOM](#utf-8-bom)
- [参照ファイル情報](#参照ファイル情報)
- [日本語--English-切り替え](#日本語--english-切り替え)
- [文字の正規化](#文字の正規化)
- [フォールバック抽出](#フォールバック抽出)
- [プライバシー](#プライバシー)
- [対応環境](#対応環境)
- [GitHub Pages で公開する場合](#github-pages-で公開する場合)
- [現在の制限事項](#現在の制限事項)
- [English quick guide](#english-quick-guide)

---

## このツールでできること

現在の v1.0.0 では、次の処理を行えます。

- Gemini Gem の保存ファイルをブラウザから読み込む
- 複数ファイルを一度に選択する
- タイトルを抽出する
- Gem の説明を抽出する
- Gem の指示本文を抽出する
- 参照ファイルの名前、URL、MIME type を可能な範囲で抽出する
- 抽出した内容を編集可能なテキストエリアに表示する
- 抽出内容をコピーする
- UTF-8 テキストとして保存する
- 保存時に UTF-8 BOM を付けるか選択する
- 日本語 / English の UI を切り替える
- 既知構造を認識できないファイルから、汎用的に UTF-8 文字列を探索する

---

## なぜ普通のテキストエディタでは文字化けして見えるのか

確認した Gem ファイルでは、日本語本文そのものは UTF-8 で格納されています。ただし、ファイル全体は通常の `.txt` のようなプレーンテキストではありません。

ファイル内部には、フィールド番号、長さ、その他の構造情報を表すバイナリデータが含まれています。構造は Protocol Buffers の wire format に近く、UTF-8 の文字列はその中の length-delimited field として格納されています。

そのため、ファイル全体をテキストとして直接開くと、UTF-8 の本文と制御用バイトが混ざり、文字化けや不自然な記号が見えることがあります。

Gem Text Extractor は単純な「文字コード変換」ではなく、確認済みのバイナリ構造から必要な文字列フィールドを取り出します。

---

## 基本的な使い方

1. `index.html` をブラウザで開きます。
2. Google Drive から対象の Gem ファイルをローカルへ取得します。
3. ファイルを **Gem ファイルをここにドロップ** の領域へドラッグ＆ドロップします。
4. または、ドロップゾーンをクリックしてファイルを選択します。
5. 解析が終わると、ファイルごとに抽出結果が表示されます。
6. 内容を確認し、必要に応じて編集します。
7. **TXT保存** または **コピー** を使用します。

複数ファイルを同時に選択できます。各ファイルは独立した結果カードとして表示されます。

---

## 画面の項目

### English / 日本語

右上のボタンで UI 言語を切り替えます。

### ドロップゾーン

Gem ファイルをドラッグ＆ドロップする領域です。クリックすると通常のファイル選択ダイアログが開きます。

### 保存時に UTF-8 BOM を付ける

オンの場合、保存する `.txt` の先頭に UTF-8 BOM を追加します。Windows 系アプリで文字コードの自動判定を安定させたい場合に便利です。

### 参照ファイル情報も含める

オンの場合、Gem 内から検出できた参照ファイル名、URL、MIME type を出力に含めます。

### TXT保存

現在テキストエリアに表示されている内容を `.txt` として保存します。テキストエリアを手動編集した場合は、編集後の内容が保存されます。

### コピー

現在のテキストエリアの内容をクリップボードへコピーします。

---

## 抽出される内容

既知の Gem 構造を認識した場合、基本的に次の形で出力されます。

```text
# Gem title

## 説明
Gem description...

## Gem の指示
Gem instructions...

## 参照ファイル
- example.txt (text/plain)
  https://...
```

英語 UI でファイルを読み込んだ場合は、見出しも `Description`、`Gem Instructions`、`Reference Files` のように英語になります。

Gem 本文そのものは翻訳しません。保存されている原文を抽出します。

---

## TXT保存

保存ファイル名は、原則として読み込んだファイル名をもとに作られます。

既知のバイナリ拡張子として `.bin`、`.gem`、`.pb`、`.proto`、`.dat` が付いている場合はその拡張子を外し、`.txt` を付けます。拡張子のない Gem ファイルの場合は、元の名前に `.txt` が追加されます。

---

## UTF-8 BOM

UTF-8 BOM は必須ではありません。モダンなエディタでは BOM なし UTF-8 でも通常は正しく読めます。

一方、Windows の一部ソフトウェアや古いテキスト処理環境では、BOM がある方が UTF-8 と認識されやすい場合があります。そのため v1.0.0 では既定でオンにしています。

UTF-8 BOM が不要なワークフローではチェックを外してください。

---

## 参照ファイル情報

Gem 内に参照ファイル関連の情報が保存されている場合、次の情報を可能な範囲で抽出します。

- ファイル名
- URL
- MIME type

保存形式によっては一部しか取得できない場合があります。また、参照ファイルの**実データそのものを展開する機能ではありません**。

---

## 日本語 / English 切り替え

言語切り替えは UI 表示と、新たに解析した結果のセクション見出しに反映されます。

選択言語はブラウザの `localStorage` に保存されるため、同じブラウザで再度開いたときにも設定が維持されます。

すでに表示済みの抽出結果本文は、言語切り替え後に自動変換されません。必要な言語に切り替えてから再度ファイルを読み込むと、その言語の見出しで生成されます。

---

## 文字の正規化

抽出した文字列には次の軽い正規化を行います。

- BOM (`U+FEFF`) の除去
- NBSP (`U+00A0`) を通常の半角スペースへ変換
- narrow NBSP (`U+202F`) を通常の半角スペースへ変換
- 改行を LF に統一
- Unicode NFC 正規化

これにより、見た目は普通のスペースなのに検索や比較で一致しない、といった問題を減らします。

---

## フォールバック抽出

確認済みの Gem 構造を認識できなかった場合、ツールは処理を中止せず、汎用 UTF-8 文字列抽出へ切り替えます。

このモードでは Protocol Buffers 風の length-delimited field を再帰的に走査し、人間が読める UTF-8 文字列を集めます。

フォールバックではタイトル・説明・指示本文の意味的な区別を保証できないため、余分な内部文字列や重複に近い文字列が含まれる可能性があります。

結果カードのステータスに **汎用文字列抽出 / Generic string extraction** と表示された場合は、このモードが使われています。

---

## プライバシー

v1.0.0 のファイル解析は、ブラウザ内の JavaScript で実行されます。

Gem ファイルをサーバーへアップロードする処理はありません。外部 API も使用していません。

GitHub Pages で公開した場合でも、GitHub は静的な HTML や画像を配信するだけで、Gem ファイルの解析は利用者のブラウザ上で実行されます。

ブラウザに保存されるアプリ設定は UI 言語のみです。

---

## 対応環境

推奨環境:

- Desktop Google Chrome
- Microsoft Edge

必要な主な Web API / JavaScript 機能:

- File API / `File.arrayBuffer()`
- `TextDecoder`
- `BigInt`
- `Blob`
- `URL.createObjectURL()`
- `navigator.clipboard`（コピー機能）
- `localStorage`（言語設定）

クリップボード機能はブラウザのセキュリティ条件によって制限される場合があります。その場合でも TXT 保存は利用できます。

---

## GitHub Pages で公開する場合

アプリはビルド不要の静的構成です。

GitHub Pages の公開対象となる場所に、このパッケージの `index.html` と関連ファイルを配置してください。アプリ本体は外部 JavaScript や CDN に依存していないため、`index.html` 単体でも動作します。

README のスクリーンショット表示には `gem_text_extractor.png` が必要です。

---

## 現在の制限事項

- Gemini Gem の保存形式は Google の公開・固定仕様を前提にしていません。
- 本ツールの構造解析は、確認済みサンプルに基づく実装です。
- Google 側で保存形式が変更されると、既知構造として認識できなくなる可能性があります。
- フォールバック抽出では、必要な文字列だけを完全に分類できるとは限りません。
- 参照ファイルのメタデータ抽出はベストエフォートです。
- 参照ファイルの実体をダウンロード・復元する機能はありません。
- Gem 本文の翻訳機能はありません。
- ファイル内容の完全な Protocol Buffers schema を定義・復元するツールではありません。

---

## English quick guide

Gem Text Extractor reads Gemini Gem files locally in your browser and extracts readable UTF-8 text from their binary structure.

### Workflow

1. Open `index.html` in a modern browser.
2. Drag one or more Gem files into the drop zone, or click the zone to choose files.
3. Review the extracted title, description, instructions, and reference-file metadata.
4. Edit the text if necessary.
5. Click **Save TXT** or **Copy**.

### Privacy

The selected Gem files are processed locally. v1.0.0 does not upload file contents or call an external API. Only the selected UI language is stored in `localStorage`.

### Format compatibility

The parser is based on Gem file structures observed in sample files and is not an official Google file-format implementation. If the known structure cannot be detected, the app falls back to generic UTF-8 string extraction.
