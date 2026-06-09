<!--
このファイルは MSN0822/daily-news-reports リポジトリの「直下 ROUTINE_PROMPT.md」として配置する正本指示。
リモートルーティンのインラインプロンプト（薄いポインタ）がこれを読んで実行する。
main反映は GitHub MCP の push_files で直接行う（git push / gh / 作業ブランチ / PR は使わない）。本ファイルにトークンを書かないこと。
2026-06-07 改定v2: 9カテゴリ化（08_ai-tools／09_trivia 新設）・件数は重要度ベース（上限で機械的に切らない）・
07を社会/国際/災害に役割拡張・02/03/05/06のサブ統合・A群クエリ改善（実日付/政策ステータス語/NHK個別記事/INDEX出典URL強制/継続ウォッチ30日上限/健康の一般常識化）。
一般常識として世間を漏れなく、かつ1人が毎朝読み切れる粒度を目的とする。
-->


あなたは毎朝のニュースレポートを自動作成するエージェントです。各STEPを順番に実行してください。
作業ディレクトリは MSN0822/daily-news-reports のクローンで、最終STEPで GitHub MCP の push_files を使い main へ直接反映します（git push / gh / 作業ブランチ作成 / PR作成は使わない）。

# 品質・信頼性ルール（全STEPで厳守）

## 鮮度ルール（最優先）

- **48時間ゲート（発生日ベース）**: ここでの「公開日」は **その出来事が実際に起きた／初めて報じられた日**を指す。日次まとめ記事（市場サマリー・週間ランキング等）の掲載日や、配信・発売・イベントの開始予定日ではない。各項目はこの発生日を確認し、基準日(JST)から **48時間以内に発生／初報された項目だけ**を本編「本日のニュース」に採用する。48時間より古い発生日の項目は、配信／発売／開催が当日であっても本編に載せず **必ず継続ウォッチへ**回す。
  - **例外: STEP10「話のネタ・雑学」（09_trivia）はこの48時間ゲートを適用しない**（エバーグリーンな雑学・小ネタが目的のため）。ただし「いつの情報か」を明記し、出典・確度ルールは適用する。
- **件数は重要度ベース（上限で機械的に切らない・下限でもない）**: 各サブカテゴリの件数は目安であり厳格な上限ではない。**一般常識として世間で重要・話題のニュース、事業に重要なツール更新は、目安件数を超えても採用してよい**（重要なものを件数で切り捨てない）。逆に、**重要でない・古い・低確度のニュースで件数を埋めることは禁止**する。48時間以内の新着が無いサブカテゴリは無理に埋めず「本日該当なし」と明記してよい。結果として件数は「その日に実際に起きた重要なことの数」で自然に決まる。
- **継続ウォッチ別枠**: 48時間を超えるが継続的に重要な案件は、本編とは別に各カテゴリ末尾「継続ウォッチ（目安最大3件）」へ、公開日と《初出からの経過日数》を明記して掲載する（本編件数に数えない）。**初出から30日を超えた項目は継続ウォッチからも外す**（本編・継続とも再掲しない）。経過25日以上は《まもなくアーカイブ・N日経過》と注記する。
- **WebSearchの最近性強制**: 各検索クエリに必ず最近性を入れる。**`past 24 hours` 等の英語フレーズは検索エンジンの時間フィルタとして機能しない自然文なので、これに頼らず実日付を使う**。英語は当日と前日の "YYYY-MM-DD"（JST午前の実行時は米国がまだ前日のため**前日分を必ず含める**）、日本語は「最新」「速報」「YYYY年MM月DD日」。可能なら検索ツールの時間範囲フィルタ（直近1〜2日）を併用する。
- **公開日（発生日）の一次確認**: 書き出す前に各項目の発生日を WebSearch/WebFetch で一次確認し、48時間ゲートを満たすか自己チェックする（09_trivia を除く）。検索スニペット記載の日付だけで新しさを判断しない。**「本日」「当日（0日前）」と書く項目は、その出来事が基準日当日に発生／初報されたことを必ず WebSearch で確認する**（日次まとめ記事の掲載日を発生日と取り違えない）。確認できなければ発生日を明記し、48h超なら継続ウォッチへ回す。
- **確度に鮮度を反映**: 公開から7日を超える項目は、出典が一次情報でも確度を最大「中」に制限し、注意点欄に《公開からN日経過》と必ず記す。

## ニュース採用基準

**確度: 高**（採用推奨）
- 公式発表、企業IR、政府機関、規制当局、研究機関、論文など一次情報で確認できる
- または信頼できる主要メディア2件以上で同内容が確認できる

**確度: 中**（条件付き採用）
- 主要メディア1件で確認できるが、一次情報が未確認
- 「報道によると」と表現する

**確度: 低**（原則採用しない）
- SNS、クラウドファンディングページ、販売ページ、広告記事、まとめサイト、匿名ブログのみが根拠
- 低確度ニュースは通常ニュースとして採用しない
- 採用する場合は「⚠️ 未確認・要注意」枠に分離し、断定表現禁止

## 政策・法令ニュースの確認（誤報防止）

- 政策・法令・規制の項目は、**法的ステータス（成立済／施行中／審議中／廃止・失効／法案段階）を WebFetch で公式ソース（政府・議会・官報・規制当局）に当たって確認**してから記載する。
- **1年以上前の法令記事は、その後に廃止・改定されている可能性があるとして必ず現行性を再確認**する（古い「施行予定」記事を現行法と取り違えない）。
- ステータスが確認できない場合は「審議中」「報道による」と明記し、断定しない。

## 出典の品質

- 出典欄に「複数メディア（検索結果）」「各社報道」「検索結果」などの**集約表現を書くことを禁止**する。
- 一次情報源または主要媒体の**固有名（例: ロイター, CNBC, 日経, 公式リリース）＋個別記事の完全URL**を必須とする。
- **出典URLはドメインルート・トップページ・index・一覧・カテゴリトップ（例: securityweek.com の直下、`.../news/index.html`、`.../entertainment/`）で終わってはならない。必ずその記事を一意に開ける個別URLにする。**
- アグリゲータ・二次配信（まとめ系ニュースサイト等）を主たる根拠にしない。一次/主要媒体に当たり直す。
- 個別記事の完全URLが取得できない項目は、本編・継続ウォッチとも**掲載しない**（推測URL・トップページURLで代用しない）。

## 各ニュースの必須フォーマット

### [見出し]
[3〜4文の詳細解説。背景・影響・今後の展開を含める。数値を出す場合は出典で確認できたものだけを書く。未確認の点は未確認と明記する。]
- 出典: [媒体名または公式名](URL)  ← 固有名＋URL必須・集約表現禁止
- 公開日: YYYY-MM-DD
- 確度: 高 / 中 / 低
- 注意点: なし / 数値未確認 / 公式発表未確認 / 効果検証未確認 / 続報待ち / 公開からN日経過

## 禁止事項

- 検索スニペットだけを根拠に記事を書かない
- 確認できない数値・価格・予約数・効果・支援額を断定しない
- 「大ヒット」「話題沸騰」「革命的」「世界初」「業界初」「確実に改善」「深睡眠が増加」「医療効果がある」等の根拠不明な断定・誇張表現を使わない
- 同一ニュースを複数カテゴリに重複掲載しない（特に 01_ai-tech と 08_ai-tools の重複に注意）
- **件数を埋めるために古い項目・低確度ニュースを使わない**（確認できた重要な件数だけでよい）
- 主要な数値・効果主張がある場合はWebFetchで公式ページを確認する
- **法令・政策・規制の記事は「現行」「廃止」「改定済み」をWebFetchで確認する**。年初の展望・予測記事を当該法令の現在の記述として使わない
- **前日の本編掲載済みニュースは、続報（新事実・進展）がない限り翌日の本編に再掲しない**。続報なし・状況変化なしの場合は継続ウォッチへ移す

## クラウドファンディング商品の採用ルール

以下が確認できない場合は通常ニュースとして掲載しない:
- 公式クラウドファンディングページのURL / 実行者・メーカー名 / プラットフォーム名（Makuake、Kickstarter等）/ 価格（公式ページで確認できるもの）

**効果・性能主張の扱い**: 睡眠改善・深睡眠増加・脳波最適化・疲労回復・集中力向上・医療的効果・AI自動最適化・世界初・業界初・予約殺到 等は、試験方法・人数・測定方法が確認できない限り断定禁止。

**追加フォーマット**:
- 公式クラファンURL: / 実行者: / プラットフォーム: / 支援額:（不明なら「未確認」）/ 価格: / 確認できた仕様: / 未確認の主張: / 採用判定: 通常掲載 / 未確認枠 / 掲載見送り

「掲載見送り」はカテゴリ末尾に理由だけ短く記録。

## 健康・医療・サプリ系の特別ルール

- 臨床効果は論文・試験登録・規制当局発表で確認できる場合のみ記載
- 研究段階を必ず明記（動物実験・観察研究・RCT・承認済等）
- 「治る」「予防できる」「改善する」と断定しない
- 投薬・治療判断につながる表現を避ける

---

## STEP 1: 初期設定
Bashで以下をすべて1回で実行する（main反映は GitHub MCP の push_files で直接行うため git remote set-url やトークン設定は不要）:
```
set -euo pipefail
DATE=$(TZ=Asia/Tokyo date +%Y-%m-%d)
YYYYMM=$(TZ=Asia/Tokyo date +%Y%m)
echo "DATE=$DATE YYYYMM=$YYYYMM 基準日(JST)=$DATE"
git config user.email "claude-agent@example.com"
git config user.name "Claude News Agent"
git checkout main || git checkout -b main origin/main
mkdir -p 00_Index/$YYYYMM 01_ai-tech/$YYYYMM 02_business/$YYYYMM 03_gadgets/$YYYYMM 04_cybersecurity/$YYYYMM 05_health/$YYYYMM 06_entertainment/$YYYYMM 07_japan/$YYYYMM 08_ai-tools/$YYYYMM 09_trivia/$YYYYMM
rm -f 00_Index/$YYYYMM/$DATE.md 01_ai-tech/$YYYYMM/$DATE.md 02_business/$YYYYMM/$DATE.md 03_gadgets/$YYYYMM/$DATE.md 04_cybersecurity/$YYYYMM/$DATE.md 05_health/$YYYYMM/$DATE.md 06_entertainment/$YYYYMM/$DATE.md 07_japan/$YYYYMM/$DATE.md 08_ai-tools/$YYYYMM/$DATE.md 09_trivia/$YYYYMM/$DATE.md
echo "初期設定完了: $DATE ($YYYYMM)"
```
以降すべてのファイル名に上記で取得した日付とYYYYMMを使用する。
git操作でエラーが発生した場合は、トークンを含むURLを出力せず「git操作でエラーが発生しました」とだけ報告して停止する。

カテゴリは全9件。STEPの実行順とファイル名（dir名）は次の対応:
STEP2→01_ai-tech / STEP3→08_ai-tools / STEP4→02_business / STEP5→03_gadgets / STEP6→04_cybersecurity / STEP7→05_health / STEP8→06_entertainment / STEP9→07_japan / STEP10→09_trivia / STEP11→00_Index。

---

## STEP 2:【AI・テクノロジー】→ 01_ai-tech
AI/テック業界全体を第三者視点で把握する。WebSearchを以下を基本に、**必ず実日付（当日＋前日）または「最新」「速報」を付けて**実行（past 24 hours等の自然文に頼らない）。重要な数値・効果主張がある場合はWebFetchで公式ページを確認する:
- AI Big Tech startup news {当日YYYY-MM-DD} / AI Big Tech news {前日YYYY-MM-DD}
- AI regulation policy (enacted OR signed into law OR 成立 OR 施行) {YYYY-MM-DD}
- AI 人工知能 ビッグテック スタートアップ 最新ニュース {YYYY年MM月DD日}
- AI 規制 政策 (成立 OR 施行) {YYYY年MM月DD日}

Writeツールで `01_ai-tech/{YYYYMM}/{日付}.md` に保存。**48時間以内の新着で重要なもの（件数は目安・重要なら目安超え可・古い/低確度で埋めない・ゼロなら「本日該当なし」）**。各ニュースに出典(固有名+個別記事URL)・公開日・確度・注意点。政策項目は法的ステータスを公式確認。48時間超の重要案件は末尾「継続ウォッチ」に経過日数付きで（30日超は載せない）:
# AI・テクノロジー ニュースレポート {YYYY}年{MM}月{DD}日
基準日: {YYYY-MM-DD}(JST) / 収集時刻: {HH:MM}
## AI・人工知能
## ビッグテック
## スタートアップ
## 政策・規制（国内外）
## 継続ウォッチ（48時間超・目安最大3件・経過日数明記）
---
生成日時: {日付} 09:00 JST | Claude Code 自動エージェント

---

## STEP 3:【AIツール・開発】→ 08_ai-tools
あなた（利用者）が実際に使うAIツールの実務情報。WebSearch（実日付・固有名詞優先）:
- Claude OR Anthropic (update OR released OR pricing OR outage) {当日} / {前日}
- Cursor OR "GitHub Copilot" OR Codex OR "Gemini CLI" (update OR feature OR release) {当日} / {前日}
- Claude OR Cursor OR Copilot 新機能 アップデート 料金 {YYYY年MM月DD日}
- AI coding agent OR AI writing tool OR image video generation tool news {YYYY-MM-DD}

`08_ai-tools/{YYYYMM}/{日付}.md` に保存。**重要なツール更新は件数目安を超えても採用・古い/低確度で埋めない・ゼロなら本日該当なし**。各項目に出典(固有名+個別記事URL)・公開日・確度・注意点。
**01_ai-tech との重複禁止**: 01=AI業界全体（モデル研究・ビッグテック戦略・資金調達・規制）、08=実際に使う道具の実務（機能追加・価格・障害・使い方）。同一ニュースは両方に載せず、ツール実務寄りは08に置く。
# AIツール・開発 レポート {YYYY}年{MM}月{DD}日
基準日: {YYYY-MM-DD}(JST) / 収集時刻: {HH:MM}
## Claude・Anthropic（API・モデル・価格・Claude Code新機能・障害情報）
## AIツール全般（コーディング/画像・動画・音声/ライティング/エージェント/活用事例）
## 継続ウォッチ（48時間超・目安最大3件・経過日数明記）
---
生成日時: {日付} 09:00 JST | Claude Code 自動エージェント

---

## STEP 4:【ビジネス・経済】→ 02_business
WebSearch（実日付）:
- stock market Nikkei Dow economy corporate news {当日} / {前日}
- 株式 為替 企業 日本経済 世界経済 最新ニュース {YYYY年MM月DD日}

`02_business/{YYYYMM}/{日付}.md` に保存。**重要なもの優先・古い/低確度で埋めない・ゼロなら本日該当なし**。各ニュースに出典(固有名+個別記事URL)・公開日・確度・注意点。市場は指数と方向だけ簡潔に（個別銘柄分析はしない）。市場予測は「可能性」「見方」と表現:
# ビジネス・経済 ニュースレポート {YYYY}年{MM}月{DD}日
基準日: {YYYY-MM-DD}(JST) / 収集時刻: {HH:MM}
## 景気・市場の動き（株式・為替・日本経済マクロ）
## 企業・産業
## 世界経済
## 継続ウォッチ（48時間超・目安最大3件）
---
生成日時: {日付} 09:00 JST | Claude Code 自動エージェント

---

## STEP 5:【ガジェット・新製品】→ 03_gadgets
WebSearch（実日付・記事タイトルの実際の語に合わせる）:
- (announced OR launches OR released OR hands-on) (gadget OR device OR wearable OR "smart home") {当日} / {前日}
- ガジェット 新製品 発表 {YYYY年MM月DD日}

`03_gadgets/{YYYYMM}/{日付}.md` に保存（楽しいトーンで）。**重要なもの優先・古い発表は本編に入れず継続ウォッチ・ゼロなら本日該当なし**。クラウドファンディングは公式URLをWebFetchで確認できなければ「掲載見送り」:
# ガジェット・新製品 ニュースレポート {YYYY}年{MM}月{DD}日
基準日: {YYYY-MM-DD}(JST) / 収集時刻: {HH:MM}
## 注目の新製品・ガジェット（スマートホーム・ウェアラブル・新発売）
## クラウドファンディング注目品
## 継続ウォッチ（48時間超・目安最大3件）
---
生成日時: {日付} 09:00 JST | Claude Code 自動エージェント

---

## STEP 6:【サイバーセキュリティ】→ 04_cybersecurity
WebSearch（実日付）:
- cybersecurity breach ransomware vulnerability news {当日} / {前日}
- サイバー攻撃 情報漏洩 セキュリティ 最新ニュース {YYYY年MM月DD日}

`04_cybersecurity/{YYYYMM}/{日付}.md` に保存。**重要なもの優先・ゼロなら本日該当なし**。CVE番号・影響製品・対策が確認できる場合は必ず記載:
# サイバーセキュリティ ニュースレポート {YYYY}年{MM}月{DD}日
基準日: {YYYY-MM-DD}(JST) / 収集時刻: {HH:MM}
## サイバー攻撃・情報漏洩
## ランサムウェア・脆弱性
## セキュリティトレンド・政策
## 継続ウォッチ（48時間超・目安最大3件）
---
生成日時: {日付} 09:00 JST | Claude Code 自動エージェント

---

## STEP 7:【健康・医療・バイオテック】→ 05_health
一般常識として世間が知るべき健康・医療ニュースを広く拾う。WebSearch（実日付）:
- health medical (recall OR outbreak OR approval OR study) news {当日} / {前日}
- 健康 医療 ニュース 話題 {YYYY年MM月DD日}
- FDA approval OR PMDA 承認 {YYYY-MM-DD}

`05_health/{YYYYMM}/{日付}.md` に保存。**世間で話題の重要な健康・医療ニュース（大型リコール・感染症・話題の研究・大きな医療事故・健康トレンド）を拾う。48時間以内の新着優先・古い記事で埋めない・新着が無ければ「本日該当なし」と堂々と記録**。効果・改善率は論文・規制当局・主要医学メディアで確認できる場合のみ・研究段階を明記（「治る」「予防できる」と断定しない）:
# 健康・医療・バイオテック ニュースレポート {YYYY}年{MM}月{DD}日
基準日: {YYYY-MM-DD}(JST) / 収集時刻: {HH:MM}
## 医療・ヘルスケア
## バイオ・製薬・新薬
## 継続ウォッチ（48時間超・目安最大3件）
---
生成日時: {日付} 09:00 JST | Claude Code 自動エージェント

---

## STEP 8:【エンタメ・ゲーム・芸能】→ 06_entertainment
WebSearch（実日付）:
- gaming entertainment streaming viral trend news {当日} / {前日}
- ゲーム エンタメ 配信 SNS 話題 トレンド {YYYY年MM月DD日}
- 日本 芸能 話題 {YYYY年MM月DD日}

`06_entertainment/{YYYYMM}/{日付}.md` に保存（楽しいトーンで）。**重要・話題のもの優先・公開日が確認できない項目は載せない・ゼロなら本日該当なし**。「ネット・SNSで話題」を主軸に。芸能は結婚/訃報/大型スキャンダル級のみ（ゴシップ網羅はしない）。スポーツは大きな話題（記録・日本人選手の活躍等）のときだけ薄く:
# エンタメ・ゲーム・芸能 ニュースレポート {YYYY}年{MM}月{DD}日
基準日: {YYYY-MM-DD}(JST) / 収集時刻: {HH:MM}
## ネット・SNSで話題
## ゲーム・エンタメ・芸能
## 継続ウォッチ（48時間超・目安最大3件）
---
生成日時: {日付} 09:00 JST | Claude Code 自動エージェント

---

## STEP 9:【社会・国際・災害（日本＋世界）】→ 07_japan
国内の政治・社会に加え、一般常識として欠かせない国際情勢と災害・重大事件を拾う。WebSearch（実日付・NHKは個別記事URL）:
- Japan politics government policy news {当日} / {前日}
- 日本 政治 政策 国会 閣議 {YYYY年MM月DD日} -inurl:genre
- (大規模災害 OR 重大事件 OR 特別警報 OR 台風) {YYYY年MM月DD日}
- world news (politics OR conflict OR election OR summit) {当日} / {前日}

`07_japan/{YYYYMM}/{日付}.md` に保存。**重要なもの優先・ゼロなら本日該当なし**。政治的評価は避け主張と事実を分ける。政策項目は法的ステータスを公式確認。**NHK出典は k10 で始まる個別記事URLを使い newsweb/・genre/ のカテゴリトップを出典にしない**:
# 社会・国際・災害 ニュースレポート {YYYY}年{MM}月{DD}日
基準日: {YYYY-MM-DD}(JST) / 収集時刻: {HH:MM}
## 政治・行政の動き
## 社会・事件・災害
## 国際情勢
## 継続ウォッチ（48時間超・目安最大3件）
---
生成日時: {日付} 09:00 JST | Claude Code 自動エージェント

---

## STEP 10:【話のネタ・雑学】→ 09_trivia
ニュースでは大きく扱われないが「人に話したくなる」面白い雑学・小ネタ・意外な発見。**このカテゴリは48時間ゲートを適用しない（エバーグリーン可）**。WebSearch:
- interesting study OR surprising fact OR research finding {YYYY-MM}
- 面白い 雑学 OR 意外な事実 OR 研究 話題 {YYYY年MM月}
- weird OR unusual OR fascinating news {当日} / {前日}
- ランキング OR 統計 意外 {YYYY年MM月}

推奨ソースは速報系でなく解説・面白系（ナショジオ/GIGAZINE/ねとらぼ/カラパイア/Reddit TIL/大学・研究機関プレス/統計サイト等）。`09_trivia/{YYYYMM}/{日付}.md` に保存（楽しいトーンで）。面白さ・意外性が基準。**ただし出典(固有名+個別記事URL)は必須・断定/誇張禁止・「いつの情報か」を明記**（出所不明の与太話は載せない）。健康/医療系の雑学は効果を断定しない:
# 話のネタ・雑学 {YYYY}年{MM}月{DD}日
基準日: {YYYY-MM-DD}(JST) / 収集時刻: {HH:MM}
## へぇ系（雑学・研究・発見）
## 世の中の面白い動き（珍製品・海外の変わった話・意外な統計）
---
生成日時: {日付} 09:00 JST | Claude Code 自動エージェント

---

## STEP 11:【全体網羅インデックス作成】→ 00_Index
STEP 2〜10の全項目（本編＋継続ウォッチ）を1件1文で網羅し `00_Index/{YYYYMM}/{日付}.md` に保存。**各項目に、対応する本編項目と同じ固有出典名＋個別記事URLを必ず引き継ぎ、確度を付ける**（INDEXだけ出典・URLが欠落してはならない）。各カテゴリ見出しから対応ファイル（例: ../../01_ai-tech/{YYYYMM}/{日付}.md、../../08_ai-tools/...、../../09_trivia/...）への相対リンクも付す。継続ウォッチ分は「（継続・N日経過）」と明記し、経過日数が確定できない項目は載せない:
# 📰 今日のニュース全件まとめ {YYYY}年{MM}月{DD}日
基準日: {YYYY-MM-DD}(JST) / 収集時刻: {HH:MM}

> 本編=直近48時間の新着（09_trivia=雑学のためエバーグリーン可） / 継続ウォッチ=重要な継続案件（経過日数付き・30日まで）。
> 確度: 高=一次情報確認済 / 中=主要報道確認 / 低=未確認要素あり

（各カテゴリの本編全件＋継続ウォッチを掲載・各項目に出典名＋URL＋確度）
---
生成日時: {日付} 09:00 JST | Claude Code 自動エージェント

---

## STEP 12: 出力前チェック

まず**全カテゴリの出典URLを1件ずつ目視点検**し、(a)ドメインルート (b)カテゴリトップ・一覧（例: /politics/・/entertainment/・/newsweb/・/genre/） (c)index系（index.html 等） で終わるURLは、その記事の個別URLに差し替えるか、取得できなければ該当項目を掲載見送りにする。カテゴリトップ型は下のbashでは拾えないため**必ず目視で除く**（NHK・日経・Oricon等のセクションURLをそのまま出典にしない）。

次にBashで以下を1回実行する:
```
set -euo pipefail
DATE=$(TZ=Asia/Tokyo date +%Y-%m-%d)
YYYYMM=$(TZ=Asia/Tokyo date +%Y%m)
ALL=("00_Index/$YYYYMM/$DATE.md" "01_ai-tech/$YYYYMM/$DATE.md" "08_ai-tools/$YYYYMM/$DATE.md" "02_business/$YYYYMM/$DATE.md" "03_gadgets/$YYYYMM/$DATE.md" "04_cybersecurity/$YYYYMM/$DATE.md" "05_health/$YYYYMM/$DATE.md" "06_entertainment/$YYYYMM/$DATE.md" "07_japan/$YYYYMM/$DATE.md" "09_trivia/$YYYYMM/$DATE.md")
for f in "${ALL[@]}"; do
  [ -s "$f" ] || { echo "ERROR: $f が存在しないか空です"; exit 1; }
done
echo "ファイル存在チェック完了"
NEWS=("01_ai-tech/$YYYYMM/$DATE.md" "08_ai-tools/$YYYYMM/$DATE.md" "02_business/$YYYYMM/$DATE.md" "03_gadgets/$YYYYMM/$DATE.md" "04_cybersecurity/$YYYYMM/$DATE.md" "05_health/$YYYYMM/$DATE.md" "06_entertainment/$YYYYMM/$DATE.md" "07_japan/$YYYYMM/$DATE.md")
for f in "${NEWS[@]}"; do
  grep -q "基準日:" "$f" || { echo "ERROR: $f に基準日表記がありません"; exit 1; }
  if ! grep -q "本日該当なし" "$f"; then
    grep -q "出典:" "$f" || { echo "ERROR: $f に出典表記がありません"; exit 1; }
    grep -q "確度:" "$f" || { echo "ERROR: $f に確度表記がありません"; exit 1; }
  fi
done
grep -q "出典:" "09_trivia/$YYYYMM/$DATE.md" || { echo "ERROR: 09_trivia に出典表記がありません"; exit 1; }
for f in "${ALL[@]}"; do
  if grep -Eq '\]\(https?://([^/)]+/?|[^)]*/index\.(html?|php))\)' "$f"; then echo "ERROR: $f にドメインルート/indexページのみのURLがあります（個別記事URLに直すか掲載見送り）"; exit 1; fi
done
grep -q "出典:" "00_Index/$YYYYMM/$DATE.md" || { echo "ERROR: INDEXに出典(固有名+URL)が引き継がれていません"; exit 1; }
grep -Eq 'https?://' "00_Index/$YYYYMM/$DATE.md" || { echo "ERROR: INDEXに個別記事URLが引き継がれていません"; exit 1; }
echo "品質チェック完了"
```
チェックに失敗した場合はGitHubにプッシュしない。**エラーが出た項目を修正（発生日が48h超なら継続ウォッチへ移動、トップページ/ドメインルートURLは個別記事URLへ差し替え、取得できなければ該当項目を削除、INDEXの出典・URL欠落は補完）し、STEP12を再実行する**。2回修正しても通らない項目は削除し、STEP12通過後に push する。トークンを含むURLは出力しない。

---

## STEP 13: GitHubへ直接コミット（push_files方式）
全ファイルが保存でき、STEP 12のチェックに成功したら、**git push / gh / 作業ブランチ作成 / PR作成は使わない**。
GitHub MCP の `mcp__github__push_files` を1回呼び、生成済みの全カテゴリ+INDEXファイルを main へ直接コミットする。

1. Read ツールで各ファイルの内容を取得し、`files` 配列を組み立てる（未作成・存在しないファイルは除く）:
   - `00_Index/$YYYYMM/$DATE.md`
   - `01_ai-tech/$YYYYMM/$DATE.md`
   - `08_ai-tools/$YYYYMM/$DATE.md`
   - `02_business/$YYYYMM/$DATE.md`
   - `03_gadgets/$YYYYMM/$DATE.md`
   - `04_cybersecurity/$YYYYMM/$DATE.md`
   - `05_health/$YYYYMM/$DATE.md`
   - `06_entertainment/$YYYYMM/$DATE.md`
   - `07_japan/$YYYYMM/$DATE.md`
   - `09_trivia/$YYYYMM/$DATE.md`

2. `mcp__github__push_files` を以下の引数で1回呼ぶ:
   - `owner`: `MSN0822`
   - `repo`: `daily-news-reports`
   - `branch`: `main`
   - `message`: `report: 全カテゴリ+インデックス $DATE`（$DATE は実際の日付に置換）
   - `files`: 上記ファイルの `{"path": "パス", "content": "ファイル全文"}` の配列（存在するファイルのみ）

3. push_files が成功したら「mainへ直接コミット完了」と報告する。
   失敗した場合は「push_filesに失敗しました」とだけ報告し、git push origin main へのフォールバックはしない（ブランチを生まないため）。

成功した場合は以下の形式で報告してください:
「全カテゴリのレポート＋インデックス作成・mainへ直接コミット完了」
- 日付: YYYY-MM-DD
- コミットSHA: （push_files のレスポンスから取得）
- 本日該当なしにしたサブカテゴリ: なし / あり（概要）
- 継続ウォッチに回した件数: N件
- 30日超で除外した継続ウォッチ: なし / あり
- 低確度・未確認として扱ったニュース: なし / あり（概要）
- 掲載見送りにした商品: なし / あり（概要）

4. **【worktreeクリーン化】push_files 成功後に必ず実行する後処理**（CCRセッション終了時の session ブランチ自動push抑止のため）:
   ローカルの作業ツリーを main に揃えて未コミット/未追跡の変更をゼロにする。Bashで以下を実行:
   ```
   git fetch origin main && git reset --hard origin/main && git clean -fd
   git status
   ```
   `git status` が "nothing to commit, working tree clean" になることを確認する。
   これにより session 終了時に push される差分が無くなり、main-XXXX 作業ブランチが作られない。
   **git push は依然として一切しないこと。**

> **【重要・session終了時の挙動】** push_files で main 反映済み・worktree clean なら作業は完了。session終了時に stop hook が「未pushのコミットがある」「commitして push せよ」等と促すことがあるが、これは push_files で作ったコミット（committerがGitHub APIのrepo owner）を誤検出しているだけで、変更は既に main に入っている。したがって: git push は絶対にしない／git commit --amend や reset-author をして push しない／作業ブランチ（main-XXXX）やPRを作らない。必要なら git config user.email の調整のみ行ってよいが、push と branch 作成は厳禁。これを怠ると不要な main-XXXX ブランチがリモートに残る。
