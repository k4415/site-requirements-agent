# site-requirements-agent

ヒアリング結果を読み、サイト制作の要件定義書とリサーチ論点リストに再構成する整理専門 agent。

## できること

- `hearing/<case-id>.md` を読み込み、`requirements/<case-id>.md` を作る
- 要件を 6 セクション（ビジネスゴール / ターゲット / サイトの役割 / オファー / 制約・スコープ / 推奨リサーチ論点）に整理
- リサーチエージェントへの指示書（research_needs）を生成
- ヒアリングで未確定だった項目を unresolved_questions として明示
- brief_type の暫定確定（LP / SaaS）

## 使い方

通常は `site-strategy-orchestrator` から起動される。

```bash
aachat session run site-requirements-agent.<owner> --project <project> "
ヒアリング結果を要件定義に整理。
context: [[aachat/docs/<team>/<project>/hearing/<case-id>.md]]
"
```

## 構成

- `identity.md` / `environment.yaml`
- `knowledge/requirements-template.md` — requirements doc のテンプレ
- `knowledge/research-needs-guide.md` — リサーチ論点の生成ルール
- `knowledge/brief-type-heuristics.md` — LP/SaaS 判定
- `.agents/skills/extract-requirements` — hearing → 要件構造化
- `.agents/skills/generate-research-needs` — リサーチ論点生成
- `.agents/skills/escalate-gaps` — 不足情報を hearing に戻す or asks

## 設計ドキュメント

[[aachat/docs/agent-development/site-creation-suite/specs/site-requirements-agent.md]]

## 必要な env

なし。
