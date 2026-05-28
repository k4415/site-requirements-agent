# Research Needs Guide

`research_needs` フィールドの生成ルール。

## 構造

```yaml
research_needs:
  competitors:
    - name: "競合A"
      url: "https://example.com"   # hearing にあれば必須、なければ research-agent に発見指示
      reason: "市場リーダー / クライアント言及 / 近接プロダクト"
      priority: high              # high | medium | low
  market_intelligence:
    - topic: "業界市場規模"
      time_horizon: "2024-2026"
      angle: "AI 導入率"
      priority: high
      preferred_sources: ["e-Stat", "Perplexity Sonar", "業界レポート"]
  voc:
    - keyword: "AI コピー 失敗"
      media: "x"                  # x | review_site | reddit | yahoo_chiebukuro
      reason: "想定ターゲットの愚痴を観察"
      priority: medium
```

## 競合候補の挙げ方

### hearing で挙がったもの（必須）
- ヒアリングのブロック G「競合・参考」で挙がった会社・サイトを必ず含める
- URL がない場合は research-agent に発見を依頼（`url: null, discover: true`）

### 補完するもの（推奨）
- ヒアリングで挙がっていなくても、明らかに同カテゴリの市場リーダーがいる場合は追加
- 「近接プロダクト」（直接競合ではないが代替手段になりうる）も含める
- 3〜5 本に絞る（多すぎると research-agent が空転する）

## 市場リサーチの切り口

priority high で挙げるべき項目：

- **市場規模**: 直近年次 + 予測（2〜3 年先）
- **主要プレイヤー**: 売上 / シェア / 資金調達ステータス
- **トレンド**: ターゲットの行動変化につながる潮流

priority medium:

- **規制・コンプライアンス**: 業界固有のルール変化
- **テクノロジー潮流**: AI 普及度合いなど

priority low（必要なら）:

- **マクロ経済**: 金利・物価・為替の影響

### 推奨情報源

| 切り口 | 一次ソース | 二次ソース |
|---|---|---|
| 日本国内市場規模 | e-Stat API（経済センサス・商業統計） | 矢野経済 / 業界紙 |
| 海外市場規模 | Statista / Crunchbase | Gartner / IDC |
| 業界トレンド | Perplexity Sonar（出典付き） | X 観察 |
| 競合状況 | Crunchbase（資金調達） | 各社 IR / 公式ブログ |

## VOC キーワードの設計

- ターゲットが「困った時に発する言葉」を予想する
- ヒアリングで取れた「生の言葉」をそのまま検索キーワードに転用するのが理想
- ネガティブワード（「失敗」「困った」「やめた」）と組み合わせると VOC が拾いやすい

例:
- 「AI コピー 失敗」「AI バナー 違和感」 → AI 制作ツールへの不満
- 「広告制作 外注 高い」 → 内製化検討者の本音

## 優先度の付け方

- **high**: ブリーフの core fields（positioning, single_minded_proposition）に直結
- **medium**: ブリーフの supporting fields（rtb, social_proof）に使う
- **low**: あれば嬉しいが、ないとブリーフが書けないわけではない

research-agent はリソース配分の参考にする（high を必ず実施、medium は予算次第、low はオプション）。
