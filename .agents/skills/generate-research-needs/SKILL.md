---
name: generate-research-needs
description: 要件整理結果と事前共有資料からリサーチエージェントへの指示書を生成。「リサーチ論点を作る」「research_needs生成」などのトリガーで起動。
---

# generate-research-needs

`requirements/<case-id>.md` の内容 **+ `cases/<case-id>.md` の pre_shared_assets** から、research-agent が空転しない具体的な指示書を作る。事前資料 URL は必ず research_needs に含めて、research-agent が `client-asset-parse` / `competitor-scrape` で実取得するようにする。

## いつ起動するか

- `extract-requirements` 完了後、自動連続実行
- orchestrator から「リサーチ論点を再生成」と依頼された時

## 入力

- `requirements/<case-id>.md`
- `cases/<case-id>.md`（pre_shared_assets 必須）

## 実行手順

### 1. 要件 + 事前資料 doc の読み込み

- requirements の brief_type、ターゲット、オファー、ポジショニング情報を確認
- `cases/<case-id>.md` の `pre_shared_assets` を全件取得

### 2. 競合候補のリストアップ（重要: 事前資料を必ず統合）

以下を **全て** `research_needs.competitors` に含める:

1. **hearing G ブロックで挙がった競合**（hearing doc の 25-27 番）
2. **`pre_shared_assets[].type == competitor_ref` の URL 全件**（最重要）
3. **`pre_shared_assets[].type == inspiration_like` / `inspiration_dislike` の URL 全件**（参考サイトとして deep_scan）

URL が hearing にも事前資料にもない競合は `url: null, discover: true` で research-agent に発見指示。

3〜7 本程度に絞る。各エントリに `reason` と `priority` を付ける。

### 3. クライアント既存サイトの deep_scan 指示

`pre_shared_assets[].type == existing_website` がある場合、`research_needs.client_assets` に **deep_scan 指示** を含める:

```yaml
research_needs:
  client_assets:
    - { type: existing_website, url: "https://...", deep_scan: true, priority: high }
    - { type: product_catalog, url: "https://...", deep_scan: true, priority: high }
    - { type: voc_review, url: "https://...", deep_scan: medium }
```

research-agent はこれらを `client-asset-parse` skill で `fetch_lp.py` 経由で取得・構造化する。

### 4. 市場リサーチ論点の設計

- `knowledge/research-needs-guide.md` の「市場リサーチの切り口」から、当案件に必要なものを選ぶ
- 各 topic に `time_horizon` と `angle` を必ず付ける
- priority を付ける
- preferred_sources に Perplexity Sonar / e-Stat 等を明示
- **`pre_shared_assets[].type == industry_report` がある場合**、その URL も `preferred_sources` に追加

### 5. VOC キーワード設計

- hearing の Pains / Gains から「ターゲットが困った時に発する言葉」を予想
- ネガティブワード（「失敗」「困った」「やめた」）と組み合わせ
- 媒体（x / review_site / reddit / yahoo_chiebukuro）を選択
- **`pre_shared_assets[].type == voc_review` がある場合**、その URL も `media` に "client_voc_url" として追加

### 6. research_needs を frontmatter に書き込む

下記の構造で。

### 7. 完了処理

- `requirements/<case-id>.md` の `status: done` に更新
- case ハブ doc の `status: research`、`assignee: site-research-agent` に更新
- orchestrator に通知 → orchestrator が site-research-agent を session run で起動

## research_needs の完全例

```yaml
research_needs:
  competitors:
    # hearing で挙がった
    - name: "Canva"
      url: "https://www.canva.com/ja_jp/"
      source: hearing
      reason: "クライアント言及・テンプレ運用の代表"
      priority: high
    # 事前資料 competitor_ref から
    - name: "Adobe Express"
      url: "https://www.adobe.com/express/"
      source: pre_shared_assets
      reason: "クライアントが提供した競合 URL"
      priority: high
    # discover 必要
    - name: null
      url: null
      discover: true
      source: discovery
      reason: "市場リーダー想定、Perplexity で発見"
      priority: medium

  client_assets:
    # 既存サイト deep_scan
    - { type: existing_website, url: "https://client.example.com/", deep_scan: true, priority: high }
    - { type: product_catalog, url: "https://client.example.com/products", deep_scan: true, priority: high }
    - { type: voc_review, url: "https://client.example.com/reviews", deep_scan: true, priority: medium }
    - { type: sales_deck, url: "https://drive.google.com/...", deep_scan: false, priority: low, note: "認証必要なら skip" }

  market_intelligence:
    - topic: "国内デザインツール市場規模"
      time_horizon: "2024-2026"
      angle: "中小企業向け SaaS"
      priority: high
      preferred_sources: ["e-Stat", "Perplexity Sonar"]
    - topic: "AI 制作ツール導入率"
      time_horizon: "2024-2025"
      angle: "広告代理店"
      priority: medium
      preferred_sources: ["Perplexity Sonar", "https://industry-report-url.com"]  # 事前資料の業界レポート URL

  voc:
    - keyword: "AI バナー 違和感"
      media: "x"
      reason: "AI 制作物への不満を観察"
      priority: medium
    - keyword: "Canva やめた"
      media: "x"
      reason: "競合からのスイッチング要因"
      priority: medium
    # 事前資料 voc_review があれば
    - media: "client_voc_url"
      url: "https://client.example.com/reviews"
      reason: "自社レビューページから顧客の声を直接観察"
      priority: high
```

## NG

- 競合 URL を推測で書く（不明なら必ず `discover: true`）
- 事前資料 `competitor_ref` を research_needs.competitors に含め忘れる
- 事前資料 `existing_website` を `client_assets.deep_scan: true` に含め忘れる
- priority を全部 high にする（research-agent が空転する）
- 市場リサーチの topic を「市場全般」など曖昧にする
- 事前資料 URL があるのに research-agent に渡さない
