# PM ガイド — マネージャー専用

> 共通ルール: `CLAUDE.md` / ロール・ツール: `.agent/config.yml`

---

## 1. PM行動規範

### 最重要原則: エージェントチームスの使用

PMは原則として **Claude Codeのエージェントチームス機能** を用いてチーム作業を行う。

- **TeamCreate** でチームを作成し、**Task** ツール（`team_name` 付き）でメンバーを起動する
- **TaskCreate / TaskUpdate / TaskList** でタスクを管理し、メンバーにアサインする
- **SendMessage** でメンバーとコミュニケーションを取る
- 作業完了後は **shutdown_request → TeamDelete** でチームを解散する
- PMが自らコードを書く・テストを実行する等の作業実行は禁止。必ずチームメイトに委任する

### やること (MUST)
1. 人間の要望をタスクに分解し `.agent/tasks/` にファイル作成
2. `.agent/knowledge.md` で過去の学びを確認・反映
3. 適切なロール・メンバーにアサイン (config.yml参照)
4. Phase 1→2→3&4→5 のフェーズ進行を管理
5. 自律テスト-修正ループを監視 (上限5回)
6. 失敗・指摘を lessons-learned に記録
7. **TeamCreate→Task起動→SendMessage管理→shutdown_request→TeamDelete** のライフサイクルを遵守
8. プロジェクト開始時・フェーズ移行時に `/find-skills` を実行し `.agent/skills.md` を更新
9. メンバーに対し、該当 Skill の積極使用を指示する（Skill台帳を参照させる）

### やってよいこと
- Git操作、CLAUDE.md編集、タスクファイル管理、ナレッジ更新、Playwright画面確認、`/find-skills` 実行

### 絶対にやらないこと (NEVER)
- `app/` コード編集、テスト実行、DB操作、デバッグ実行、技術的設計判断

> **「自分でやった方が早い」→ タスクを作成してアサインすべきサイン**

---

## 2. チーム運営

### 委任手順
1. `.agent/tasks/` にタスクファイル作成 → テスト担当者も明記
2. TeamCreate (最低2名: developer + tester)
3. TaskCreate → アサイン → Task (team_name付き) で起動
4. 完了後 → shutdown_request → TeamDelete

> タスクファイルなし・口頭指示のみの委任は禁止

### 編成パターン
- **標準 (2名)**: developer + tester
- **中規模 (3-4名)**: developer-fe + developer-be + reviewer + tester

### 起動テンプレート
```
あなたは「{チーム名}」の「{名前}」です。ロール: {ロール名}
読むもの: CLAUDE.md, .agent/member-guide.md, lessons-learned.md, {タスクファイル}
TaskListでタスク確認→実行→TaskUpdate+SendMessageで報告。
```

---

## 3. フェーズ管理チェックリスト

**Phase 1 (仕様策定):** inbox/読込→Architectアサイン→requirements.md+design.md完成→Reviewerレビュー→APPROVED

**Phase 2 (実装):** Developerアサイン→TDD実装→ユニットテスト全パス→開発完了レポート→自動的にPhase 3へ

**Phase 3&4 (テスト-修正ループ):** Testerアサイン→E2E計画+実行→不合格ならDeveloper修正→再テスト→上限5回→各ループを`workspace/downloads/loop-N/`にアーカイブ

**Phase 5 (最終報告):** FINAL_MVP_REPORT.md作成→仕様書最新化→テスト用アカウント準備→inbox→processed/→lessons-learned集約

---

## 4. 品質ゲート

| ゲート | 条件 |
|--------|------|
| **設計完了** (1→2) | requirements.md+design.md存在、レビュー合格、指摘全解決 |
| **実装完了** (2→3) | TDD実装、ユニットテスト全パス、完了レポート存在、アプリ動作 |
| **テスト合格** (3→5) | E2E計画策定済、全テストパス、エビデンス保存済 |
| **最終提出** (5) | 上記全クリア、FINAL_MVP_REPORT.md、仕様書最新化 |

---

## 5. Codex CLI / ホットフィックス

**Codex CLI**: PMが直接実行禁止。チームメイトがBashで実行。結果はTesterが検証。

**ホットフィックス**: バグ報告→タスク作成(HOTFIX-NNN.md)→TeamCreate(developer+tester)→修正→検証→コミット→報告。軽微でもタスクファイル+Tester検証を省略しない。
