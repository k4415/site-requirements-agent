---
name: generate-research-needs
description: 要件整理結果からリサーチエージェントへの指示書を生成。「リサーチ論点を作る」「research_needs生成」などのトリガーで起動。
---

# generate-research-needs

`requirements/<case-id>.md` の内容から、research-agent が空転しない具体的な指示書を作る。

## いつ起動するか

- `extract-requirements` 完了後、自動連続実行
- orchestrator から「リサーチ論点を再生成」と依頼された時

## 実行手順

1. **要件 doc の読み込み**
   - brief_type、ターゲット、オファー、ポジショニング情報を確認

2. **競合候補のリストアップ**
   - hearing G ブロック「競合・参考」で挙がった会社・サイトを必ず含める
   - URL が hearing で取れていない競合は `url: null, discover: true` で research-agent に発見指示
   - 3〜5 本に絞る
   - 各エントリに `reason` と `priority` を付ける

3. **市場リサーチ論点の設計**
   - `knowledge/research-needs-guide.md` の「市場リサーチの切り口」から、当案件に必要なものを選ぶ
   - 各 topic に `time_horizon` と `angle` を必ず付ける
   - priority を付ける

4. **VOC キーワード設計**
   - hearing の Pains / Gains から「ターゲットが困った時に発する言葉」を予想
   - ネガティブワード（「失敗」「困った」「やめた」）と組み合わせ
   - 媒体（x / review_site / reddit / yahoo_chiebukuro）を選択

5. **research_needs を frontmatter に書き込む**

6. **完了処理**
   - `requirements/<case-id>.md` の `status: done` に更新
   - case ハブ doc の `status: research`、`assignee: site-research-agent` に更新
   - orchestrator に通知 → orchestrator が site-research-agent を session run で起動

## 例

```yaml
research_needs:
  competitors:
    - name: "Canva"
      url: "https://www.canva.com/ja_jp/"
      reason: "クライアント言及・テンプレ運用の代表"
      priority: high
    - name: "Adobe Express"
      url: null
      discover: true
      reason: "近接プロダクト・市場リーダー想定"
      priority: medium
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
  voc:
    - keyword: "AI バナー 違和感"
      media: "x"
      reason: "AI 制作物への不満を観察"
      priority: medium
    - keyword: "Canva やめた"
      media: "x"
      reason: "競合からのスイッチング要因"
      priority: medium
```

## NG

- 競合 URL を推測で書く（不明なら必ず `discover: true`）
- priority を全部 high にする（research-agent が空転する）
- 市場リサーチの topic を「市場全般」など曖昧にする
