# Brief Type Heuristics

`brief_type` を `lp` / `saas` のどちらに確定するかの判定ルール。

## LP / キャンペーンサイト 寄りのサイン

- 単発キャンペーン / 期間限定オファー
- 1 商品 / 1 プランで完結
- CV ゴールが「資料DL / 予約 / 購入 / イベント申込」
- ICP 概念が薄い（B2C 寄り or 個人事業）
- 既存サイトとは別に「単発の用途」で作る
- 短期間（数週間〜数ヶ月）の運用想定

## SaaS / サービス紹介サイト 寄りのサイン

- 継続契約モデル / サブスク / リテンションが重要
- 機能数が複数 / プランが複数
- ICP（firmographics）を明確に語れる
- 競合代替手段が「自社内製 / Excel運用 / 別ソフト」など非ソフトウェアを含む
- 中長期（数年）の運用想定
- 導入事例 / カスタマーサクセス的な情報が豊富

## 判定フロー

1. 上記の各リストを hearing 内容と照合
2. LP サインが 3 つ以上 ∧ SaaS サインが 1 つ以下 → `brief_type: lp`
3. SaaS サインが 3 つ以上 ∧ LP サインが 1 つ以下 → `brief_type: saas`
4. 両方 2 つ以上ある（拮抗）→ `asks` で人間判断
5. どちらも 1 つ以下（情報不足）→ hearing-agent に再依頼

## 拮抗時の `asks` テンプレ

```yaml
asks:
  - id: brief_type_decision
    question: "サイト種別がLPとSaaS紹介の両方の特徴を持っています。どちらに寄せますか？"
    options:
      - { value: lp, label: "LP寄せ（オファー軸・期間限定的）" }
      - { value: saas, label: "SaaS寄せ（ポジショニング軸・継続契約）" }
      - { value: hybrid_lp, label: "LPだがSaaSの要素を含める" }
      - { value: hybrid_saas, label: "SaaS紹介サイトだがLPの要素を含める" }
    blocking: true
    context:
      lp_signs: ["...", "..."]
      saas_signs: ["...", "..."]
```

`hybrid_*` を選んだ場合の挙動は requirements 内で説明：
- `brief_type` は片方に寄せる（schema 上の必須フィールドはそちらに従う）
- もう片方の要素は brief の `notes` や custom field で補足

## 暫定確定後のチェック

判定後、以下を確認：
- LP に確定した場合: `structure.recommended_sections` の見積もりが描ける（hearing から）
- SaaS に確定した場合: `positioning.market_category` の素案が描ける（hearing から）

描けない場合は再ヒアリングが必要。
