# Pomodoro Timer 開発計画

## 1. Phase3の目的

Phase3「開発計画」では、Phase2で設計されたシステム構成と機能要件をもとに、具体的な開発作業計画を作成します。これにより、実装フェーズ（Phase4以降）での開発がスムーズに行えるようになります。

## 2. 開発方針

- React + TypeScript + Viteの技術スタックを採用
- ローカルストレージへの永続化を実装
- タイマー画面を画面調停レイヤーとして設計し、コンポーネントとHookの責務を明確にする
- TimerHookとHistoryHookの分離を実践し、データの流れを明確にする
- タイマー完了時に作業履歴を保存する仕組みを実装
- GitHub ActionsによるCI/CDを実装
- AWS S3 / CloudFrontへのデプロイを計画

## 3. 実装対象スコープ

- TimerPageコンポーネント
- usePomodoroTimer Hook
- useWorkHistory Hook
- workHistoryStorage Service
- WorkSession型定義
- timeユーティリティ関数
- タイマー画面のUIコンポーネント（TimerDisplay、TimerControls、WorkHistoryList）
- ローカルストレージとの連携機能
- TimerPageが完了状態を検知し履歴を保存するロジック

## 4. 実装対象外スコープ

- バックエンドAPIの実装
- 認証機能
- データベース接続
- サーバーサイドレンダリング（SSR）
- モバイルアプリ版（PWA対応）
- カスタムテーマ設定機能
- 通知機能（音声・システム通知）

## 5. 作業分解、WBS

1. 開発環境構築
2. コンポーネント設計実装
3. Hook実装
4. Service実装
5. UIコンポーネント実装
6. ローカルストレージ連携実装
7. TimerPage画面実装
8. 統合テスト
9. CI/CD設定
10. AWSデプロイ設定

## 6. タスク一覧

- [ ] プロジェクト構成作成（React + TypeScript + Vite）
- [ ] コンポーネント設計実装
- [ ] usePomodoroTimer Hook実装
- [ ] useWorkHistory Hook実装
- [ ] workHistoryStorage Service実装
- [ ] WorkSession型定義
- [ ] timeユーティリティ関数実装
- [ ] TimerDisplayコンポーネント実装
- [ ] TimerControlsコンポーネント実装
- [ ] WorkHistoryListコンポーネント実装
- [ ] ローカルストレージ連携実装
- [ ] TimerPage画面実装
- [ ] 統合テスト
- [ ] GitHub Actions CI/CD設定
- [ ] AWS S3/CloudFrontデプロイ設定

## 7. 優先順位

1. プロジェクト構成作成
2. HookとServiceの実装
3. UIコンポーネント実装
4. TimerPage画面実装
5. 統合テスト
6. CI/CD設定
7. AWSデプロイ設定

## 8. 想定ブランチ

- `main`：安定版コード。マージはプルリクエスト経由。
- `develop`：開発用ブランチ。各機能の開発はこのブランチから切り出し。
- `feature/xxx`：各機能単位の開発ブランチ。

## 9. 想定Issue

- Issue #1: Viteプロジェクト初期化
- Issue #2: ESLint / Prettier設定
- Issue #3: Vitest設定
- Issue #4: 基本ディレクトリ構成作成
- Issue #5: WorkSession型とtimeユーティリティ作成
- Issue #6: workHistoryStorage Service作成
- Issue #7: usePomodoroTimer Hook作成
- Issue #8: useWorkHistory Hook作成
- Issue #9: TimerDisplay Component作成
- Issue #10: TimerControls Component作成
- Issue #11: WorkHistoryList Component作成
- Issue #12: TimerPage統合
- Issue #13: タイマー完了時の履歴保存連携
- Issue #14: Unit Test追加
- Issue #15: TimerPage Integration Test追加
- Issue #16: CI workflow作成
- Issue #17: deploy workflow作成
- Issue #18: AWS S3 / CloudFrontリリース手順作成
- Issue #19: ロールバック手順作成

## 10. Pull Request分割方針

- 各機能単位でPRを作成
- Hook、Service、コンポーネントの実装を分けてPR作成
- テストコードも含めてPRにまとめる
- プルリクエストはマージ前にはレビュー必須

## 11. レビュー方針

- コードレビューは各プルリクエストに対して必須
- テストカバレッジの確認
- 型定義、コンポーネント設計、Hookの責務分担を確認
- CI/CD設定の妥当性確認
- AWSデプロイ設定の確認

## 12. 完了条件

- development-plan.md が作成されている
- git-workflow.md が作成されている
- task-breakdown.md が作成されている
- Phase4以降のタスク、依存関係、Issue/PR分割方針が整理されている
- レビュー指摘が反映されている
- プロジェクトオーナーまたはテックリードがPhase3を承認している
- 承認されるまでPhase4へ進まない

## 13. リスクと対策

| リスク | 対策 |
|--------|------|
| 技術選定の妥当性 | Phase2で決定したReact+TypeScript+Viteを守る |
| Hook設計の複雑化 | 開発初期段階から責務分担を明確にする |
| テストカバレッジ不足 | テスト対象候補を確認し、テストコードも含める |
| CI/CD設定の難易度 | 既存の実装サンプルを参考にし、段階的に設定 |
| AWSデプロイの失敗 | デプロイ手順を事前に確認し、ロールバック手順を準備 |

## 14. セルフレビュー結果

以下の点についてセルフレビューを実施しました：

1. **Phase1、Phase2の内容との整合性**
   - Phase1の要件定義とPhase2のシステム設計に従って計画が作成されています
   - React + TypeScript + Viteの技術スタックが明確に反映されています
   - ローカルストレージ永続化、TimerPageを画面調停レイヤーとする設計、Hook分離などの要件が開発計画に反映されています

2. **Phase3としての実装の適切さ**
   - Phase3は計画ドキュメント作成のみであり、実装は行わないという指示に従っています
   - 実装の具体的な詳細は記載されていないため、計画に踏み込みすぎていません

3. **Phase4以降のタスクの粒度**
   - 各フェーズのタスクが具体的に進められる粒度で整理されています
   - Phase4からPhase8までの各フェーズのタスクが明確に分かれています

4. **Git運用の実務的さ**
   - Gitワークフローが実務的なブランチ戦略、コミットメッセージルール、PRルールを含んでいます
   - 開発フロー例や運用例も記載されており、実務での適用性が高いです

5. **Issue / PR単位の適切さ**
   - 各タスクに想定されるIssueとPRタイトルが記載されています
   - Issue単位で作業を分割しており、PR単位でのレビューも可能になります

6. **CI/CDとAWSリリースの見通し**
   - GitHub ActionsによるCI/CDとAWS S3/CloudFrontへのデプロイまで見通しが立っています
   - 各フェーズの完了条件にCI/CDやAWS関連の設定が含まれています

## 15. レビュワーに確認してほしいポイント

1. Phase2で決定された設計内容（React + TypeScript + Vite、LocalStorage永続化、TimerPage画面調停レイヤー）が開発計画に適切に反映されているか
2. 各フェーズのタスクが具体的に進められる粒度になっているか
3. Gitワークフローが実務的かつプロジェクトの規模に合ったものになっているか
4. プロジェクト全体の完了条件が明確で、CI/CDとAWSデプロイまで見通しが立っているか
5. Issue/PRの分割方針が適切かどうか
6. リスク対策が具体的かつ実行可能なものになっているか
