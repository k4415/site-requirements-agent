---
name: escalate-gaps
description: 必須要素が hearing から読み取れない時の再依頼/エスカレーション判断。「不足情報の対応」などのトリガーで起動。
---

# escalate-gaps

要件整理の途中で必須要素が hearing 内に見つからない時の判断フロー。

## いつ起動するか

- `extract-requirements` skill が「必須セクションが書けない」と判定した時
- `generate-research-needs` skill が「研究すべき方向性すら決まらない」と判定した時

## 判定フロー

1. **不足セクションを特定**
   - 必須: ビジネスゴール / ターゲット / オファー / 制約
   - SaaS の場合の追加必須: ポジショニング情報

2. **hearing doc を再確認**
   - 該当情報が本当にないか？言い回しが違うだけで取れている可能性は？
   - 発話ログ部分も含めて grep する

3. **不足が確認されたら 3 つの選択肢から選ぶ**
   - A. **hearing-agent に再依頼**: 該当質問だけを `followup_questions` に追加し、hearing-agent を session run で起動
   - B. **asks で人間判断**: 「該当情報なしで進めるか」を案件オーナーに聞く
   - C. **デフォルト値で埋める**: 業界平均などのデフォルト（最後の手段）

## 選択基準

| 不足 | 推奨選択肢 |
|---|---|
| ビジネスゴール（業績目標）が曖昧 | A（再ヒアリング） |
| ターゲット（顧客像）が不明 | A（再ヒアリング） |
| オファー（価格・プラン）が不明 | A（再ヒアリング、ここは必須） |
| 制約（NG表現・公開日） | B（asks で人間オーナーに） |
| ポジショニング（SaaS の market_category） | A（再ヒアリング） |
| 競合候補 | 不足なら research-agent に「発見指示」で渡す（discover: true）。escalate 不要 |

## 実行

### A. hearing-agent への再依頼

```bash
aachat session run site-hearing-agent.<owner> --project <project> "
追加ヒアリングをお願いします。
context: [[aachat/docs/<team>/<project>/hearing/<case-id>.md]]
追加質問:
- <質問1>
- <質問2>
完了したら hearing doc の coverage を更新し、requirements-agent に session run で戻してください。
"
```

### B. asks で人間判断

```yaml
asks:
  - id: gap_<section-name>
    question: "<セクション> の情報が不足しています。どうしますか？"
    options:
      - { value: rehearing, label: "クライアントに追加ヒアリング" }
      - { value: default, label: "デフォルト値で進める" }
      - { value: skip, label: "該当セクションなしで進める" }
    blocking: true
```

### C. デフォルト値（最後の手段）

業界平均などのプレースホルダで埋め、`unresolved_questions` に明記。
brief-agent が最終的に検出する保険を残す。

## NG

- 不足を勝手に推測で埋める（C 以外）
- 全部 A に流して hearing-agent を疲弊させる
- 必須セクションを skip して次フェーズに進める
