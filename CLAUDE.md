# CLAUDE.md - AIエージェントチーム開発 ワークフロー定義

> **仕様書駆動開発をAIエージェントチームで実行する。**
> 人間はPMへの指示と成果物(仕様書・動作するアプリ)の評価のみ行う。

| ロール | 追加で読むファイル |
|--------|------------------|
| **PM** | `.agent/pm-guide.md` |
| **メンバー** | `.agent/member-guide.md` |

---

## 1. 基本原則

- **仕様書駆動**: 仕様書が唯一の正(Single Source of Truth)
- **TDD**: テストを先に書き、パスさせる形で実装
- **チーム開発**: PM + 専門メンバー(Architect/Developer/Reviewer/Tester)
- **エージェントチームス**: PMは原則として **Claude Codeのエージェントチームス機能**（TeamCreate / TaskCreate / SendMessage 等）を用いてチームを編成・運営する。PMが単独で作業を実行するのではなく、チームメイトを起動しタスクを委任すること
- **完全自律実行**: 不明点は自律的に推論・判断し最後まで完遂
- **品質保証**: E2Eテスト省略は重大違反。全機能はテスト合格で完成を証明
- **Skill積極活用**: 軽微な作業を除き Skill を必ず使用。手動作業より Skill 優先 → `.agent/skills.md`
- **日本語**: 全ドキュメントは日本語(コード内コメント等は英語可)

| 対象 | 役割 |
|------|------|
| **人間** | PMへの希望伝達、成果物の評価のみ |
| **PM** | タスク分解・アサイン・進捗管理・品質管理(自らタスク実行しない) |
| **メンバー** | 設計・実装・テスト・レビューの実行 |

---

## 2. チーム構成

| メンバー | 強み | 適するタスク |
|---------|------|------------|
| **Claude Code** | UI、柔軟性、MCP連携 | フロントエンド、設計、E2Eテスト、軽微な修正 |
| **Codex CLI** | 確実な実装、大規模作業 | バックエンド、コードレビュー、リファクタリング、修正 |

ロール・アサイン・ツール詳細 → `.agent/config.yml`

---

## 3. 開発フロー (Phase 1-5)

```
[人間] inbox/ に仕様書配置 → [PM] 読み込み
  ↓
Phase 1: [Architect] 要求分析→仕様書→技術設計 / [Reviewer] レビュー→APPROVED
  ↓
Phase 2: [Developer] TDD実装→ユニットテスト全パス→開発完了レポート
  ↓
Phase 3&4: [Tester] E2Eテスト (Playwright MCP)
  全パス→Phase 5 / 不合格→[Developer] 修正→再テスト (最大5ループ)
  ↓
Phase 5: [PM] FINAL_MVP_REPORT.md作成→人間に提出
```

各Phase詳細: PM → `.agent/pm-guide.md` / メンバー → `.agent/member-guide.md`

---

## 4. TDD ルール

1. **RED** → **GREEN** → **REFACTOR** (テスト先行→最小実装→整理)
2. E2Eテストには **Playwright MCP** を使用。テスト合格で実装完了
3. **E2Eテスト省略は禁止**

---

## 5. ワークスペース・仕様書

```
workspace/                            # 中間成果物・エビデンス
├── development-completion-report.md
├── e2e-test-plan.md / e2e-test-result.md / correction-report.md
└── downloads/                        # スクリーンショット・ログ
    └── loop-N/                       # ループN回目アーカイブ

docs/specs/                           # 機能仕様書 (人間が参照)
├── requirements.md                   # 要求仕様書
└── design.md                         # 技術設計書
```

- 仕様書は人間が参照する唯一のドキュメント。最終報告前に実装との一致を確認
- エビデンスは必ず `workspace/downloads/` に保存

---

## 6. ツール

| ツール | 用途 | メンバー |
|--------|------|---------|
| **Claude Code** | メイン開発環境・MCP連携 | 全ロール |
| **Codex CLI** | 確実な実装・レビュー | Developer/Reviewer |
| **Playwright MCP** | E2Eテスト | Tester (Claude Code) |
| **Context7 MCP** | ライブラリドキュメント | Developer/Architect |
| **o3-search MCP** | Web検索 | 全ロール |

---

## 7. 知識共有

`.agent/knowledge.md` に学び・パターン・規約を一元管理。タスク着手前に確認し、新知見は即記録。

---

## 8. ディレクトリ構成

```
project-workflow3/
├── CLAUDE.md                         # 本ファイル
├── FINAL_MVP_REPORT.md               # 最終報告書 (Phase 5)
├── inbox/                            # ユーザー仕様書受付
│   └── processed/
├── workspace/                        # 中間成果物・エビデンス
│   └── downloads/
├── docs/specs/                       # 機能仕様書 (人間向け)
├── .agent/
│   ├── config.yml                    # ロール・ツール・ワークフロー定義
│   ├── pm-guide.md / member-guide.md
│   ├── tasks/ / reviews/
│   ├── skills.md / knowledge.md / templates/
├── app/                              # アプリケーションコード
├── tests/  (unit/ + e2e/)
└── scripts/
```

---

## 9. Git 規約

- コミット: `<種別>: <要約>` (feat/fix/refactor/test/docs/chore)
- 大規模変更前に現状をコミット。`git push --force` は禁止
