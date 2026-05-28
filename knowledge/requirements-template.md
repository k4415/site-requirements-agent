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
unresolved_questions:         # まだ確認が必要な質問
  - "<質問>"
research_needs:               # research-agent に投げる指示書
  competitors: [...]
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

- 商品/プラン:
- 価格:
- 保証 / 返金 / 無料体験:
- 緊急性ドライバー（あれば）:

## 5. ポジショニング（SaaS の場合は必須）

- Competitive alternatives:
- Unique attributes:
- Market category:

## 6. 制約・スコープ

### 制約
- 公開希望日:
- NG表現:
- 必須要素（ロゴ、バッジ等）:
- 既存素材:

### スコープ
- 含むもの: 戦略ブリーフまで
- 含まないもの: コピー本文 / デザイン / SEO / 集客

## 7. 推奨リサーチ論点（research-agent 向け指示書）

[research_needs の中身を人間可読に展開]

### 競合候補
1. <競合名> <URL> — 理由
...

### 市場・業界リサーチ
- 市場規模（<時間軸>）
- 業界トレンド（<切り口>）

### VOC（Voice of Customer）
- キーワード: <list>
- 媒体: X / レビューサイト / Reddit / etc

## 8. 未解決事項

unresolved_questions の本文化:
1. ...
```
