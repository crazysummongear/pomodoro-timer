# Pomodoro Timer シーケンス図

## 文書管理

| 項目 | 内容 |
| --- | --- |
| サービス名 | Pomodoro Timer |
| フェーズ | Phase2 システム設計 |
| ステータス | レビュー用ドラフト |
| 作成日 | 2026-06-22 |
| 最終更新日 | 2026-06-22 |

## 1. 目的

本書は、Pomodoro Timerの主要操作について、ユーザー、UI、Hook、Service、LocalStorageの責務と処理順序を明確にする。

## 2. タイマー開始

```mermaid
sequenceDiagram
  actor User as 利用者
  participant Page as TimerPage
  participant Hook as usePomodoroTimer

  User->>Page: 開始ボタンを押す
  Page->>Hook: start()
  Hook->>Hook: startedAtとremainingSecondsを設定
  Hook-->>Page: status=runningを返す
  Page-->>User: 実行中のタイマーを表示
```

## 3. タイマー停止

```mermaid
sequenceDiagram
  actor User as 利用者
  participant Page as TimerPage
  participant Hook as usePomodoroTimer

  User->>Page: 停止ボタンを押す
  Page->>Hook: pause()
  Hook->>Hook: 経過時間を計算
  Hook->>Hook: 残り時間を保存
  Hook-->>Page: status=pausedを返す
  Page-->>User: 停止中のタイマーを表示
```

## 4. タイマーリセット

```mermaid
sequenceDiagram
  actor User as 利用者
  participant Page as TimerPage
  participant Hook as usePomodoroTimer

  User->>Page: リセットボタンを押す
  Page->>Hook: reset()
  Hook->>Hook: 状態を初期値に戻す
  Hook-->>Page: status=idleを返す
  Page-->>User: 初期状態のタイマーを表示
```

## 5. タイマー完了時の作業履歴保存

タイマー完了時、`usePomodoroTimer` は履歴を直接保存しない。`TimerPage` が `completed` への状態遷移を検知し、タイマー情報から `WorkSession` を組み立てて `useWorkHistory` に渡す。

```mermaid
sequenceDiagram
  actor User as 利用者
  participant Page as TimerPage
  participant TimerHook as usePomodoroTimer
  participant HistoryHook as useWorkHistory
  participant Service as workHistoryStorage
  participant LS as LocalStorage

  TimerHook-->>Page: status=completed, startedAt, endedAt, durationSeconds
  Page->>Page: WorkSessionを生成する
  Page->>Page: 同一完了イベントが未保存であることを確認する
  Page->>HistoryHook: addSession(workSession)
  HistoryHook->>HistoryHook: 既存履歴の先頭にworkSessionを追加する
  HistoryHook->>Service: saveWorkSessions(sessions)
  Service->>LS: setItem("pomodoro.workSessions.v1", json)
  LS-->>Service: 保存結果
  Service-->>HistoryHook: 保存完了
  HistoryHook-->>Page: 最新履歴を返す
  Page-->>User: 履歴一覧を更新
```

### 5.1 WorkSession生成ルール

| 項目 | 設定元 |
| --- | --- |
| id | `crypto.randomUUID()` などで生成する。 |
| startedAt | `usePomodoroTimer` が保持する開始日時。 |
| endedAt | 完了検知時の日時。 |
| durationSeconds | 初期リリースでは25分を秒換算した値、またはタイマーHookが返す実作業秒数。 |
| status | タイマー完了時は `completed` とする。 |

途中停止のみでは履歴を保存しない。`stopped` は将来、明示的な中断記録機能を追加する場合に利用する。

## 6. 作業履歴読み込み

```mermaid
sequenceDiagram
  actor User as 利用者
  participant Page as TimerPage
  participant HistoryHook as useWorkHistory
  participant Service as workHistoryStorage
  participant LS as LocalStorage

  User->>Page: 画面を開く
  Page->>HistoryHook: 初期化
  HistoryHook->>Service: loadWorkSessions()
  Service->>LS: getItem("pomodoro.workSessions.v1")
  LS-->>Service: JSON文字列またはnull
  Service->>Service: JSON parseと最低限の検証
  Service-->>HistoryHook: WorkSession[]
  HistoryHook-->>Page: 履歴状態
  Page-->>User: 履歴一覧を表示
```

## 7. レビュー観点

- 各操作の責務分担が明確か
- UIと永続化処理が直接結合していないか
- LocalStorageの読み書きがServiceに閉じているか
- タイマー完了時の履歴保存フローが自然か
- TimerHookからHistoryHookへ直接依存せず、TimerPageが調停役になっているか
- 同一完了イベントによる二重保存を避ける考慮があるか
- 後続のテストケースへ展開しやすい粒度か

## 8. 完了条件

- 主要操作のシーケンスが記載されている。
- Phase1の機能要件をすべてカバーしている。
- レビューで処理順序に重大な矛盾がないことを確認している。
