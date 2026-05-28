---
name: extract-requirements
description: hearing doc を読み、6セクションの要件定義を構造化する。「要件整理」「ヒアリング結果を整理」などのトリガーで起動。
---

# extract-requirements

hearing doc を読み、`knowledge/requirements-template.md` のフォーマットに沿って要件定義書を構造化する。

## いつ起動するか

- orchestrator から「ヒアリング結果を要件定義に整理」と依頼された時
- `requirements/<case-id>.md` がまだ存在しない or `status: needs_revision` の時

## 実行手順

1. **入力 doc 読み込み**
   - `hearing/<case-id>.md` 全体を読む
   - frontmatter の coverage / awareness_level_estimate / followup_questions を確認

2. **6 セクションの抽出**
   - ビジネスゴール: hearing A・D ブロックから
   - ターゲット: hearing B・C ブロック（JTBD + VPC）から
   - サイトの役割: hearing D ブロックから
   - オファー: hearing E ブロックから
   - 制約・スコープ: hearing F ブロックから（スコープは固定）
   - ブランド・トーン: hearing H ブロックから（参照のみ、brief で詳細化）

3. **生の言葉の引用**
   - クライアントの発話は意訳せず、`[<client-name>] "..."` で引用
   - 構造化要約は最小限の整形のみ

4. **brief_type の暫定確定**
   - `knowledge/brief-type-heuristics.md` の判定フローを適用
   - 拮抗 or 情報不足なら `unresolved_questions` に追加 or `asks` を生やす

5. **未確定事項の明示**
   - hearing で `needs_followup` だった項目を `unresolved_questions` に転記
   - 「decision_moment の具体エピソードが薄い」など定性的な不足も明示

6. **保存**
   - `requirements/<case-id>.md` に保存
   - frontmatter: `status: done` または `status: in_progress`（research_needs 未生成の場合）

7. **次 skill 起動**
   - 必須セクションが揃ったら `generate-research-needs` skill を起動
   - 必須セクションが欠ける場合は `escalate-gaps` skill を起動

## NG

- hearing にないことを追加する
- 「優良」「狙い目」など評価語を使う
- brief_type を推測で決める（拮抗なら必ず asks）
- 6 セクション全部書かずに次へ進める
