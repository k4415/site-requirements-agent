# Requirements Template

`requirements/<case-id>.md` の標準フォーマット。

```yaml
---
title: "<案件名> 要件定義"
status: done                  # in_progress | done | needs_revision
case_id: <case-id>
brief_type: lp | saas | undecided   # 未確定なら asks に出す
inputs:
  - "../hearing/<case-id>.md"
  - "../cases/<case-id>.md"   # pre_shared_assets / creative_assets 参照
unresolved_questions:         # まだ確認が必要な質問
  - "<質問>"
pending_assets:               # まだ受領できていない事前資料・素材
  - { type: product_images, note: "撮影予定（6月中旬）" }
research_needs:               # research-agent に投げる指示書
  competitors: [...]
  client_assets: [...]        # 事前資料 URL の deep_scan 指示
  market_intelligence: [...]
  voc: [...]
last_updated: <ISO8601>
---

# 要件定義

## 1. ビジネスゴール

- 事業ゴール: <hearing から該当箇所引用>
- 行動変化（behavior_change）: <サイト訪問者にどう動いてほしいか>
- 成功定義: <KPI 閾値・期日>

## 2. ターゲット

### ICP（ある場合）
- 業種:
- 規模:
- 地域:
- Triggers（なぜ今買うか）:

### ペルソナ
- 役割 / 立場:
- JTBD（When [...], I want to [...], so I can [...]）:
- Pains:
- Gains:
- 意識レベル（暫定）: unaware / problem_aware / solution_aware / product_aware / most_aware

## 3. サイトの役割

- 一次 CV:
- 流入元（既知）:
- KPI 目標:
- ファネル上の位置づけ:

## 4. オファー

- 商品/プラン:（事前資料 product_catalog URL から補完可: <出典 URL>）
- 価格:
- 保証 / 返金 / 無料体験:
- 緊急性ドライバー（あれば）:

## 5. ポジショニング（SaaS の場合は必須）

- Competitive alternatives:
- Unique attributes:
- Market category:

## 6. 制約・スコープ

### 制約
- 公開希望日（hearing Q22 or case doc.due_date）:
- NG表現:
- 必須要素（creative_assets の is_mandatory: true から）:
- 法定表記（特商法・薬機法等）:

### スコープ
- 含むもの: 戦略ブリーフまで
- 含まないもの: コピー本文 / デザイン / SEO / 集客

## 7. 事前共有資料サマリ

| # | type | URL | status | 活用先 |
|---|---|---|---|---|
| 1 | existing_website | https://... | received | research-agent の deep_scan、現状把握 |
| 2 | product_catalog | https://... | received | offer フィールド補完 |
| 3 | voc_review | https://... | received | VOC キーワード抽出、social_proof 候補 |
| 4 | competitor_ref | https://... | received | competitor-scrape 必須対象 |
| 5 | inspiration_like | https://... | received | structure.recommended_sections 参考 |
| 6 | industry_report | https://... | received | market-research の preferred_sources |

（取得状況は `cases/<case-id>.md.pre_shared_assets` の最新で常に更新する）

## 8. 制作素材一覧（後続制作チーム向け）

| # | type | url / value | status | 用途 |
|---|---|---|---|---|
| 1 | logo | https://drive.google.com/... | received | hero / footer |
| 2 | brand_guide | https://... | received | デザイン全体のトーン参照 |
| 3 | brand_colors | #1A73E8 / #F4B400 | received | デザイン |
| 4 | fonts | Inter / Noto Sans JP | received | デザイン |
| 5 | mandatory_badge | https://... | received | ISO27001 バッジ、必須要素 |
| 6 | product_images | - | pending | 撮影予定（6月中旬） |
| 7 | compliance | 特商法表記必須 | received | brief.constraints.compliance_notes |

（取得状況は `cases/<case-id>.md.creative_assets` の最新で常に更新する）

## 9. 推奨リサーチ論点（research-agent 向け指示書）

[research_needs の中身を人間可読に展開]

### 競合候補
1. <競合名> <URL> — source: hearing | pre_shared_assets — 理由
2. <競合名> <URL> — ...
...

### クライアント既存資産（deep_scan）
- 既存サイト: https://... — deep_scan: true
- 商品カタログ: https://... — deep_scan: true
- 顧客レビューページ: https://... — deep_scan: true

### 市場・業界リサーチ
- 市場規模（<時間軸>）
- 業界トレンド（<切り口>）
- 事前資料 industry_report URL があれば preferred_sources に追加

### VOC（Voice of Customer）
- キーワード: <list>
- 媒体: X / レビューサイト / Reddit / client_voc_url（事前資料 voc_review URL）

## 10. 未解決事項

unresolved_questions の本文化:
1. ...

## 11. 待ち（pending_assets）

クライアント側でまだ準備できていない素材:
1. <type>: <note>
```
