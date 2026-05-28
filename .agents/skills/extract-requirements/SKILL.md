---
name: extract-requirements
description: hearing doc + 事前共有資料を読み、6 セクション + 事前資料サマリ + 制作素材一覧の要件定義を構造化する。「要件整理」「ヒアリング結果を整理」などのトリガーで起動。
---

# extract-requirements

`hearing/<case-id>.md` と `cases/<case-id>.md` の `pre_shared_assets` / `creative_assets` を読み、`knowledge/requirements-template.md` のフォーマットに沿って要件定義書を構造化する。

## いつ起動するか

- orchestrator から「ヒアリング結果を要件定義に整理」と依頼された時
- `requirements/<case-id>.md` がまだ存在しない or `status: needs_revision` の時

## 入力

- `hearing/<case-id>.md`（必須）
- `cases/<case-id>.md`（必須。**pre_shared_assets / creative_assets を活用**するため）

## 実行手順

### 1. 入力 doc 読み込み

- `hearing/<case-id>.md` 全体を読む
- frontmatter の coverage / awareness_level_estimate / followup_questions を確認
- **`cases/<case-id>.md` の frontmatter から `pre_shared_assets` / `creative_assets` を取得**
- pre_shared_assets に URL があれば、その中身を確認すべきかをマークする（実取得は research-agent の役割。ここでは「要件整理に必要な情報源」として一覧化する）

### 2. 8 セクションの抽出

| セクション | 主な情報源 |
|---|---|
| ビジネスゴール | hearing A・D + `pre_shared_assets.existing_website`（現状把握） |
| ターゲット | hearing B・C（JTBD + VPC）+ `pre_shared_assets.voc_review` |
| サイトの役割 | hearing D |
| オファー | hearing E + `pre_shared_assets.product_catalog`（価格表で補完） |
| 制約・スコープ | hearing F |
| ブランド・トーン | hearing H + `creative_assets.brand_guide`（参照リンク） |
| **事前共有資料サマリ**（新） | `cases/<case-id>.md.pre_shared_assets` |
| **制作素材一覧**（新） | `cases/<case-id>.md.creative_assets` |

#### 事前共有資料サマリの書き方

```markdown
## 7. 事前共有資料サマリ

| # | type | URL | status | 活用先 |
|---|---|---|---|---|
| 1 | existing_website | https://... | received | research-agent の deep_scan、現状の言語化 |
| 2 | product_catalog | https://... | received | offer フィールド補完 |
| 3 | voc_review | https://... | received | VOC キーワード抽出、social_proof 候補 |
| 4 | competitor_ref | https://... | received | research-agent の competitor-scrape 必須対象 |
| 5 | inspiration_like | https://... | received | structure.recommended_sections の参考 |
```

#### 制作素材一覧の書き方

```markdown
## 8. 制作素材一覧（後続制作チーム向け）

| # | type | url / value | status | 用途 |
|---|---|---|---|---|
| 1 | logo | https://drive.google.com/... | received | hero / footer |
| 2 | brand_guide | https://... | received | デザイン全体のトーン参照 |
| 3 | brand_colors | #1A73E8 / #F4B400 | received | デザイン |
| 4 | mandatory_badge | https://... | received | ISO27001 バッジ、必須要素 |
| 5 | product_images | - | pending | 撮影予定（6月中旬） |
```

### 3. 生の言葉の引用

- クライアントの発話は意訳せず、`[<client-name>] "..."` で引用
- 構造化要約は最小限の整形のみ
- 事前資料 URL から拾った情報は **「(出典: <url>)」** を必ず併記

### 4. brief_type の暫定確定

- `knowledge/brief-type-heuristics.md` の判定フローを適用
- 拮抗 or 情報不足なら `unresolved_questions` に追加 or `asks` を生やす

### 5. 未確定事項の明示

- hearing で `needs_followup` だった項目を `unresolved_questions` に転記
- 「decision_moment の具体エピソードが薄い」など定性的な不足も明示
- 事前資料が `pending` 状態のものを `pending_assets` として明示

### 6. 保存

- `requirements/<case-id>.md` に保存
- frontmatter:
  ```yaml
  inputs:
    - "../hearing/<case-id>.md"
    - "../cases/<case-id>.md"  # pre_shared_assets / creative_assets 参照のため
  ```
- `status: done` または `status: in_progress`（research_needs 未生成の場合）

### 7. 次 skill 起動

- 必須セクションが揃ったら `generate-research-needs` skill を起動
- 必須セクションが欠ける場合は `escalate-gaps` skill を起動

## NG

- hearing にないことを推測で追加する
- 事前資料 URL を確認せずに「情報なし」と書く（少なくとも URL の存在は記録する）
- 「優良」「狙い目」など評価語を使う
- brief_type を推測で決める（拮抗なら必ず asks）
- 必須セクション全部書かずに次へ進める
- 事前資料 URL の出典を書かずに情報を引用する
