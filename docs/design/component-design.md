# Pomodoro Timer コンポーネント設計書

## 文書管理

| 項目 | 内容 |
| --- | --- |
| サービス名 | Pomodoro Timer |
| フェーズ | Phase2 システム設計 |
| ステータス | レビュー用ドラフト |
| 作成日 | 2026-06-22 |
| 最終更新日 | 2026-06-22 |

## 1. 目的

本書は、Pomodoro Timerの初期リリースで実装する主要コンポーネント、Hook、Service、型の責務を定義する。

## 2. コンポーネント一覧

| 種別 | 名前 | 配置先 | 責務 |
| --- | --- | --- | --- |
| Page | TimerPage | `src/pages/TimerPage.tsx` | タイマー画面全体の構成とデータ連携を担当する。 |
| Component | TimerDisplay | `src/components/TimerDisplay.tsx` | 残り時間と状態を表示する。 |
| Component | TimerControls | `src/components/TimerControls.tsx` | 開始、停止、リセット操作を提供する。 |
| Component | WorkHistoryList | `src/components/WorkHistoryList.tsx` | 作業履歴を一覧表示する。 |
| Hook | usePomodoroTimer | `src/hooks/usePomodoroTimer.ts` | タイマー状態と操作関数を提供する。 |
| Hook | useWorkHistory | `src/hooks/useWorkHistory.ts` | 作業履歴の読み込み、追加、状態管理を提供する。 |
| Service | workHistoryStorage | `src/services/workHistoryStorage.ts` | LocalStorageへの保存と読み込みを担当する。 |
| Type | WorkSession | `src/types/workSession.ts` | 作業履歴データの型を定義する。 |
| Utility | time | `src/utils/time.ts` | 時間表示や秒変換などの純粋関数を提供する。 |

## 3. Page設計

### 3.1 TimerPage

| 項目 | 内容 |
| --- | --- |
| 責務 | タイマーHookと履歴Hookを組み合わせ、画面全体を構成する。 |
| 利用Hook | `usePomodoroTimer`, `useWorkHistory` |
| 子コンポーネント | `TimerDisplay`, `TimerControls`, `WorkHistoryList` |
| 注意点 | LocalStorageを直接操作しない。タイマー完了時はTimerHookの状態遷移を検知し、TimerPageが`WorkSession`を生成して`useWorkHistory.addSession()`へ渡す。 |

TimerPageは、UIコンポーネントではなく画面調停レイヤーとして以下を担当する。

- `usePomodoroTimer` からタイマー状態とセッション生成に必要な情報を受け取る。
- `status` が `completed` へ遷移した場合に、履歴保存対象かを判断する。
- `WorkSession` を生成し、`useWorkHistory.addSession()` を呼び出す。
- 同一の完了イベントで履歴が二重保存されないように制御する。
- `TimerDisplay`、`TimerControls`、`WorkHistoryList` には表示に必要な値とイベントハンドラのみを渡す。

## 4. Component設計

### 4.1 TimerDisplay

| 項目 | 内容 |
| --- | --- |
| Props | `remainingSeconds`, `status` |
| 表示 | `mm:ss`形式の残り時間、状態テキスト |
| 副作用 | なし |

### 4.2 TimerControls

| 項目 | 内容 |
| --- | --- |
| Props | `status`, `onStart`, `onPause`, `onReset` |
| 表示 | 開始、停止、リセットボタン |
| 副作用 | なし。操作は親から渡された関数を呼び出す。 |

### 4.3 WorkHistoryList

| 項目 | 内容 |
| --- | --- |
| Props | `sessions` |
| 表示 | 作業日時、作業時間、状態 |
| 副作用 | なし |
| 空状態 | 履歴がない場合は空状態を表示する。 |

## 5. Hook設計

### 5.1 usePomodoroTimer

| 項目 | 内容 |
| --- | --- |
| 責務 | タイマー状態、残り時間、開始、停止、リセットを管理する。 |
| 主な戻り値 | `status`, `remainingSeconds`, `startedAt`, `endedAt`, `durationSeconds`, `start`, `pause`, `reset` |
| 初期値 | 25分 |
| 設計方針 | 経過時間は時刻差分をもとに計算する。完了時は`completed`へ遷移し、履歴保存に必要な情報を呼び出し元へ返す。 |

### 5.2 useWorkHistory

| 項目 | 内容 |
| --- | --- |
| 責務 | 作業履歴の初期読み込み、追加、画面状態への反映を行う。 |
| 主な戻り値 | `sessions`, `addSession` |
| 利用Service | `workHistoryStorage` |
| 注意点 | データ形式の詳細をUIへ漏らしすぎない。 |

## 6. Service設計

### 6.1 workHistoryStorage

| 関数 | 内容 |
| --- | --- |
| `loadWorkSessions()` | LocalStorageから履歴を読み込み、`WorkSession[]`を返す。 |
| `saveWorkSessions(sessions)` | 履歴配列をJSON化してLocalStorageへ保存する。 |

### 6.2 エラー時の扱い

- 読み込み時にデータがない場合は空配列を返す。
- JSON parseに失敗した場合は空配列を返す。
- 不正な形式のデータは破棄する。
- 保存時の例外は呼び出し元で扱えるようにする。

## 7. 型設計

```ts
export type TimerStatus = 'idle' | 'running' | 'paused' | 'completed';

export type WorkSessionStatus = 'completed' | 'stopped';

export type WorkSession = {
  id: string;
  startedAt: string;
  endedAt?: string;
  durationSeconds: number;
  status: WorkSessionStatus;
};
```

## 8. テスト対象候補

| 対象 | テスト観点 |
| --- | --- |
| `formatSeconds` | 秒数を`mm:ss`へ変換できるか。 |
| `usePomodoroTimer` | 開始、停止、リセット、完了時の状態遷移が正しいか。 |
| `TimerPage` | `completed`遷移時に`WorkSession`を生成し、二重保存せずに履歴追加できるか。 |
| `workHistoryStorage` | 正常データ、空データ、不正JSONを扱えるか。 |
| `WorkHistoryList` | 履歴あり、履歴なしの表示が正しいか。 |

## 9. レビュー観点

- コンポーネントの責務が明確か
- Pageが肥大化しすぎない構成になっているか
- HookとServiceの境界が自然か
- LocalStorage依存がUIへ漏れていないか
- テスト対象が切り出しやすい設計になっているか

## 10. 完了条件

- 主要コンポーネント、Hook、Service、型が定義されている。
- 実装前に責務分担が確認できる。
- レビューで過不足がないことを確認している。
