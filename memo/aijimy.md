# api.aijimy.com API エンドポイント一覧

**ベースURL**: `https://api.aijimy.com/get?code={CODE}&text={INPUT}`  
**公式サイト**: https://converter.aijimy.com/  
**レスポンス形式**: プレーンテキスト（日本語）

---

## 翻訳

| code | 内容 |
|------|------|
| `translate-en2jp` | 英語 → 日本語 |
| `translate-jp2en` | 日本語 → 英語 |
| `translate-jp2ko` | 日本語 → 韓国語 |
| `translate-ko2jp` | 韓国語 → 日本語 |
| `translate-jp2cn` | 日本語 → 中国語 |
| `translate-cn2jp` | 中国語 → 日本語 |
| `translate-jp2fr` | 日本語 → フランス語 |
| `translate-fr2jp` | フランス語 → 日本語 |
| `translate-jp2es` | 日本語 → スペイン語 |
| `translate-es2jp` | スペイン語 → 日本語 |
| `translate-jp2hi` | 日本語 → ヒンディー語 |
| `translate-hi2jp` | ヒンディー語 → 日本語 |

---

## 文字列変換・操作

| code | 内容 |
|------|------|
| `upper-text` | 小文字 → 大文字 |
| `lower-text` | 大文字 → 小文字 |
| `capitalize-text` | 先頭を大文字に |
| `remove-whitespace` | 空白を除去 |
| `remove-nonprintable` | 非印字文字を除去 |
| `exclude-char` | 指定文字を除去 |
| `split-string` | 文字列を分割 |
| `replace-regex` | 正規表現で置換 |
| `check-regex` | 正規表現チェック |
| `encrypt-aes` | AES暗号化 |

---

## 日本語変換

| code | 内容 |
|------|------|
| `convert-hira2kana` | ひらがな → カタカナ |
| `convert-kana2hira` | カタカナ → ひらがな |
| `convert-hira2romaji` | ひらがな → ローマ字 |
| `convert-fullkana2halfkana` | 全角カナ → 半角カナ |
| `convert-halfkana2fullkana` | 半角カナ → 全角カナ |
| `convert-name-kanji2kana` | 氏名漢字 → カタカナ |
| `convert-name-kanji2hira` | 氏名漢字 → ひらがな |
| `split-person` | 氏名を姓・名に分割 |

---

## 日付・時間

| code | 内容 |
|------|------|
| `convert-dateepoch` | 日付 → Unixエポック |
| `convert-epochdate` | Unixエポック → 日付 |
| `convert-iso8601` | ISO 8601形式に変換 |
| `convert-24hour` | 12時制 → 24時制 |
| `convert-seconds` | 秒 → 時分秒 |
| `japanese-date` | 日付 → 和暦 |
| `convert-ad` | 和暦 → 西暦 |
| `check-holiday` | 祝日かどうかを確認 |
| `get-holiday` | 指定月の祝日一覧 |
| `get-holiday-name` | 祝日名を取得 |
| `check-leap` | 閏年かどうかを確認 |
| `count-days` | 日数を計算 |
| `shift-date` | 日付をずらす |
| `end-month` | 月末日を取得 |
| `get-weekday` | 曜日を取得 |
| `get-weeknumber` | 週番号を取得 |
| `get-quarter` | 四半期を取得 |
| `get-age` | 年齢を計算 |
| `get-days-since-birth` | 生後日数を計算 |
| `year-days` | 年間日数を取得 |
| `name-month` | 月の英語名を取得 |

---

## 単位変換

| code | 内容 |
|------|------|
| `convert-length-in2cm` | インチ → cm |
| `convert-weight-lb2kg` | ポンド → kg |
| `convert-weight-kg2lb` | kg → ポンド |
| `convert-temperature-c2f` | 摂氏 → 華氏 |
| `convert-temperature-f2c` | 華氏 → 摂氏 |
| `convert-speed-kmh2mph` | km/h → mph |
| `convert-speed-mph2kmh` | mph → km/h |
| `convert-area-ft2m` | ft² → m² |
| `convert-area-m2ft` | m² → ft² |
| `convert-volume-l2gal` | リットル → ガロン |
| `convert-volume-gal2l` | ガロン → リットル |
| `convert-pressure-pa2psi` | Pa → psi |
| `convert-pressure-psi2pa` | psi → Pa |
| `convert-energy-cal2j` | カロリー → ジュール |
| `convert-energy-j2cal` | ジュール → カロリー |

---

## 住所・郵便番号・地理

| code | 内容 |
|------|------|
| `get-address-fromzip` | 郵便番号 → 住所 |
| `get-zip` | 住所 → 郵便番号 |
| `get-address-detail` | 住所の詳細情報 |
| `normalize-address` | 住所を正規化 |
| `extract-address` | テキストから住所を抽出 |
| `get-prefecture` | 都道府県名を取得 |
| `get-prefecture-code` | 都道府県コードを取得 |
| `get-city` | 市区町村を取得 |
| `get-city-code` | 市区町村コードを取得 |
| `get-distancecalc` | 2地点間の距離を計算 |
| `get-ip2address` | IPアドレス → 住所 |
| `get-globalip` | グローバルIPを取得 |

---

## 電話・通信

| code | 内容 |
|------|------|
| `convert-telephone-global` | 電話番号を国際形式に変換 |
| `convert-telephone-local` | 電話番号を国内形式に変換 |
| `check-telephone-number` | 電話番号の正否チェック |
| `get-telephone-regioncode` | 電話番号の地域コードを取得 |

---

## 法人・インボイス情報

| code | 内容 |
|------|------|
| `get-company-name` | 法人番号 → 法人名 |
| `get-company-kana` | 法人番号 → 法人名カナ |
| `get-company-number` | 法人名 → 法人番号 |
| `get-company-count` | 法人数を取得 |
| `get-companyname-fromdomain` | ドメイン → 法人名 |
| `extract-company` | テキストから法人名を抽出 |
| `extract-ceo` | テキストから代表者名を抽出 |
| `extract-corporatenumber` | テキストから法人番号を抽出 |
| `invoice-name` | インボイス登録番号 → 法人名 |
| `invoice-address` | インボイス登録番号 → 住所 |
| `invoice-check-registration` | インボイス登録状況を確認 |
| `invoice-registration-date` | インボイス登録日を取得 |
| `invoice-disposal-date` | インボイス廃止日を取得 |
| `invoice-update-date` | インボイス更新日を取得 |

---

## 銀行情報

| code | 内容 |
|------|------|
| `get-bank-cd` | 銀行名 → 銀行コード |
| `get-bank-nm` | 銀行コード → 銀行名 |
| `get-bankbranch-cd` | 支店名 → 支店コード |
| `get-bankbranch-nm` | 支店コード → 支店名 |

---

## 税金・金融

| code | 内容 |
|------|------|
| `calc-tax10` | 税込金額を計算（税率10%） |
| `calc-tax8` | 税込金額を計算（税率8%） |
| `get-fx-rate` | 為替レートを取得 |

---

## 天気

| code | 内容 |
|------|------|
| `get-todays-weather` | 今日の天気を取得 |
| `get-week-forecasts` | 週間天気予報を取得 |
| `get-tomorrows-highest-temperature` | 明日の最高気温を取得 |
| `get-tomorrows-lowest-temperature` | 明日の最低気温を取得 |

---

## 抽出（AI）

| code | 内容 |
|------|------|
| `extract-date` | テキストから日付を抽出 |
| `extract-time` | テキストから時刻を抽出 |
| `extract-amount` | テキストから金額を抽出 |
| `extract-person` | テキストから人名を抽出 |
| `extract-url` | テキストからURLを抽出 |
| `extract-keyphrase` | テキストからキーフレーズを抽出 |
| `extract-department` | テキストから部署名を抽出 |
| `extract-billing-amount` | 請求金額を抽出 |
| `extract-billingdate` | 請求日を抽出 |
| `extract-total-amount` | 合計金額を抽出 |
| `extract-tax-amount` | 税額を抽出 |
| `extract-unit-price` | 単価を抽出 |
| `extract-payment-deadline` | 支払期日を抽出 |
| `extract-delivery-date` | 納期を抽出 |
| `extract-diagram-number` | 図番を抽出 |
| `extract-chemical-symbol` | 化学記号を抽出 |

---

## 生成（AI）

| code | 内容 |
|------|------|
| `generate-summary` | 要約を生成 |
| `summarize-text` | テキストを要約 |
| `generate-synonym` | 類義語を生成 |
| `generate-keyword` | キーワードを生成 |
| `generate-articletitle` | 記事タイトルを生成 |
| `generate-mailtext` | メール文章を生成 |
| `generate-xlsxfunction` | Excel関数を生成 |
| `generate-spreadsheetfunction` | スプレッドシート関数を生成 |
| `generate-guid` | GUIDを生成 |
| `analyze-sentiment` | 感情分析 |
| `analyze-sentiment-rank` | 感情スコアを取得 |

---

## 入札情報

| code | 内容 |
|------|------|
| `get-bid-search` | 入札案件を検索 |
| `get-bid-count` | 入札件数を取得 |
| `get-bid-description` | 入札説明を取得 |
| `get-bid-attachment` | 入札添付ファイルを取得 |

---

## 交通

| code | 内容 |
|------|------|
| `get-ekispert-station-name` | 駅名を取得 |
| `get-ekispert-url` | 乗換案内URLを取得 |

---

## YouTube

| code | 内容 | textに渡す値 |
|------|------|------------|
| `get-youtube-videodata` | 動画情報（説明・再生数・高評価数・コメント数など） | 動画ID |
| `get-youtube-channeldata` | チャンネル情報（名前・登録者数・動画数・再生数） | チャンネルID（UC...） |

---

## エラーレスポンス

| メッセージ | 意味 |
|-----------|------|
| `ERROR：パラメータが不正です` | 存在しないcodeまたは不正なtext |
| `ERROR：回答がありません` | codeは正しいがデータなし |
| `INFO：アクセス上限に達しました...` | 無料枠の上限超過（要アカウント登録） |
