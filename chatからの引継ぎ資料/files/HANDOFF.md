# 引き継ぎ資料: 中学校行事予定管理アプリ

Claude(claude.aiおよびClaude Code)での対話を通じて開発してきた、中学校の行事予定管理アプリの引き継ぎ資料です。
このアプリは、元々Excelマクロで運用されていた行事予定管理を、ブラウザだけで完結するツールに置き換えるために作られました。

**この資料は、有料プランの期限切れなど何らかの理由でこれまでの会話履歴が引き継げなくなった場合でも、
新しいClaudeセッションがこのファイルとGitHubリポジトリだけを見て作業を再開できるように書かれています。**

## どこに何があるか(最重要)

- **ローカル作業フォルダ**: `C:\Users\idolo\Documents\projects\myapp\行事予定アプリ\`
- **本体ファイル**: `行事予定管理アプリ_Phase1.html`(単一HTMLファイル、ビルド不要)
- **GitHubリポジトリ(バックアップ・履歴管理)**: https://github.com/soichiroteacher/gyoji-yotei-app (Private、`origin`、ブランチ`master`)
  - アプリファイルを編集するたびに自動で`git add`→`commit`→`push`する運用(ユーザーの`C:\Users\idolo\.claude\CLAUDE.md`のグローバル指示)。force pushはしない。
  - つまり、ローカルのファイルが万一失われても、このGitHubリポジトリの最新コミットに全履歴が残っている。
- **クラウドミラー(Googleドライブ同期)**: `C:\Users\idolo\Documents\projects\appcopy\行事予定アプリ\`(`.git`を除いた全ファイルを都度上書きコピー。閲覧用)
- **GitHub CLI**: このマシンには`gh`コマンドがインストール済み(`C:\Program Files\GitHub CLI\`、システムPATHにも登録済み)。GitHubアカウント`soichiroteacher`で認証済み。以前、インストール後にセッションを再起動しないまま使ったため一時的に`gh`コマンドが見つからず(PATHはプロセス起動時点のものを引き継ぐ仕様のため)フルパス指定が必要だったことがあるが、セッション再起動後は解消し、通常どおり`gh`だけで呼び出せる(2026-09-07 再起動により解消済み)。

## 概要

- **形式**: 単一のHTMLファイル(ビルド不要、ブラウザで直接開くだけで動作)
- **対応ブラウザ**: Chrome / Edge 推奨(File System Access APIを使用するため)。他ブラウザではダウンロード方式にフォールバック
- **依存ライブラリ**: なし(外部ライブラリ・CDN一切不使用。Excel生成も自前のZIP/OOXML実装)
- **データ形式**: `.dat`拡張子のJSONファイル(`state`オブジェクトをそのままシリアライズ)。`formatVersion: 7`

## 元になったExcelファイル

`original.xlsm`という中学校の年間行事予定表マクロファイルを参照しながら開発しました。シート構成:
- 入力: 設定、DB全体、DB教のみ、DB他欄
- 出力: 4月職(教員用月間)、4月生(家庭用月間)、年(職員)、年(生徒)
- 集計: 行事一覧、時数集計、給食日数

このファイルは会話の初期段階でのみ参照し、現在の作業ディレクトリには残っていません(必要なら再アップロードが必要)。

**進行中のタスク**: ユーザーは2026年度の実際の運用をExcelファイルで開始しており、それをこのアプリの`.dat`形式に変換してインポートしたいと考えている。変換はアプリ側に汎用インポート機能を作るのではなく、**Claude側で実ファイルを直接読み取って変換する**方針で合意済み(汎用インポートUIは学校ごとの書式差異に対応しきれず、割に合わないため)。**ユーザーからまだファイルの提供を受けていない**(「また渡します」との回答待ち)。ファイルが渡されたら、`unzip`(Git Bashに存在)でxlsxを展開してXMLを直接解析するか、PowerShellの`System.IO.Compression.ZipFile`/`[xml]`で読み取る(このマシンにPython・Node.jsは実質使えない。下記「環境上の注意」参照)。読み取った内容を`state.days`等にマッピングし、`.dat`ファイル(`JSON.stringify(state)`をそのまま書き出したもの)として書き出せばアプリで開ける。

## 環境上の注意(重要)

- **Python・Node.jsは実質使用不可**: `python`/`python3`/`node`はWindowsのApp Execution Alias(Microsoft Storeへの誘導スタブ)がPATH上にあるだけで、実体がインストールされていない。`xlsx`スキル(openpyxl等)はこの環境では使えないため、Excelファイルを読む場合はGit Bashの`unzip`コマンド、またはPowerShellの`System.IO.Compression.ZipFile`/`[xml]`キャスト(いずれも.NET標準機能でzip展開・XML解析が可能)を使うこと。
- ローカルサーバーは`.claude/static-server.ps1`(PowerShell製の簡易HTTPサーバー、`.claude/launch.json`の`static-server`設定でClaude Codeの`preview_start`から起動)を使う。`file://`直開きは`crypto.subtle`/`localStorage`がブロックされるため不可。
- 動作確認はClaude Codeの Browser pane (MCPツール`mcp__Claude_Browser__*`)で、実際にページを操作・`javascript_exec`でstateを検証する方式で行っている。Playwright/Node.jsは使っていない(過去の資料に記載があったが誤り、本資料で訂正)。

## データモデル(`state`オブジェクト、2026年9月時点)

```js
{
  formatVersion: 7,
  meta: {
    schoolName: '',
    fiscalYear: 2026,              // 4月始まりの年度
    term2StartMonth: 8,            // 2学期開始月(既定8月)
    term3StartMonth: 1,            // 3学期開始月(既定1月)
    editPasswordHash: '<SHA-256>', // 編集パスワードのハッシュ(既定"0000")
    versionLabelMonthly: '', versionLabelAnnual: '', // 予定表タイトルに添える版表記(月間・年間で別々)
  },
  manualDayFlags: {},              // 廃止済み・空のまま維持(読込時に自動移行)
  holidayOverrides: {},            // "YYYY-MM-DD" -> {type:'add'|'remove', label:''} 祝日の手動例外
  eventCandidates: [],             // 日程未定の行事を一次保存する候補プール。{id, text, useCount}
  colors: { holiday:'D9D9D9', absent:'808080' }, // 土日祝の網掛け色・欠セルの色(6桁HEX)。設定タブで変更可
  extraHours: { g1:0, g2:0, g3:0 },              // 行事予定にのらない時間数の手入力分(必要時数比較に合算)
  mealCountOverrides: {},          // "YYYY-MM-DD" -> {g1:bool,g2:bool,g3:bool} 宿泊行事等の給食扱い例外
  printCounts: [],                 // 印刷部数の履歴ログ。{id, date, target:'staff'|'student', staffName, count, note}
  subjectActuals: {                // 教科別「実際に行った授業」の年度累計コマ数(手入力)
    g1:{国語:0,社会:0,数学:0,理科:0,美術:0,保健体育:0,技術家庭:0,外国語:0}, g2:{...}, g3:{...}
  },
  days: {
    "YYYY-MM-DD": {
      eventsAnnual: '', eventsMonthly: '', eventsTeacherOnly: '',
      meeting: '', planningMeeting: false, staffMeeting: false,
      cleaning: '', club: '',
      lunch: {g1:'', g2:'', g3:''},        // '○' または '' (チェックボックス化済み)
      periods: {g1:['','','','','',''], g2:[...], g3:[...]}, // ①〜⑥。値は '1'〜'6'(時限の便宜番号)/'道'/'総'/'学'/'行'/'音'/'弁'/'欠'/'●'
      schoolDay: {g1:null, g2:null, g3:null}, // 学年別登校日。null=自動判定、true/false=明示的上書き
      weeklyDuty: '', memo: '', memoInOutput: false
    }
  },
  presets: { cleaning:[...], club:[...], lunch:[...](未使用), period:[...], weeklyDuty:[...] },
  colWidths: {},                   // 入力グリッドの列幅(px)
  requiredHours: {                 // 学習指導要領の年間標準授業時数(編集可能)。SUBJECTS定数は12教科分
    g1:{国語:140,...,道徳:35,総合:50,特別活動:35}, g2:{...}, g3:{...}
  },
  annualSettings: {                // 年間予定表(教員用/家庭用)の表示設定
    teacher: {includeMonthly:true, includeTeacherOnly:true, includeMeeting:false},
    family:  {includeMonthly:true, includeTeacherOnly:false, includeMeeting:false}
  }
}
```

`normalizeState(obj)`が、上記フィールドが欠けている古い`.dat`ファイルを読み込んだ際に自動補完する(後方互換)。新しいフィールドを追加した際は必ずこの関数にも追記すること。

### 「①〜⑥」欄の数字の意味(教科別実績機能の設計に関わる重要な確認事項)

ユーザーへの確認の結果、①〜⑥欄に入力する数字(1〜6)や記号(道/総/学/行/音/弁/欠/●)は、**時間割(曜日×時限→教科)を使った自動的な教科推定には使わない**ことが判明した。数字はあくまで「時限の便宜番号」であり、個別教科(国語・数学など)の判別には使えない。そのため教科別の実績は、`subjectActuals`という**別の手入力フィールド**として実装した(「時数・集計」タブの「教科別実績の入力」)。道徳・総合的な学習の時間・特別活動・音楽は、それぞれ既存の記号(道/総/学/音)から自動集計されるため`subjectActuals`には含まない(残り8教科: 国語・社会・数学・理科・美術・保健体育・技術家庭・外国語)。

## 主要機能と対応する関数(概略)

| 機能 | 関連関数 |
|---|---|
| 祝日自動計算 | `computeNationalHolidays`, `nationalHolidaysFor`, `getDayStatus` |
| 学年別登校日判定 | `isSchoolDay`, `anySchoolDay` |
| 入力グリッド描画 | `renderAgenda`, `attachGridHandlers`, `COLS`定義, `getFieldValue`/`setFieldValue` |
| 複数セル選択・コピペ | `paintSelection`, `selectionBounds`, `document.addEventListener('paste'/'keydown')` |
| メモの複数行モーダル | `openMemoModal`/`closeMemoModal` |
| 行事候補プール・複数枠入力モーダル | `openEventPickerModal`, `renderEventPickerBoxes`, `eventPickerBoxValues`, `renderEventCandidateList`, `importPreviousYearEventsToCandidates` |
| 週番まとめて入力(週単位/月ローテーション) | `openWeeklyDutyModal`, `getWeeksForMonth`, `applyWeeklyDutyToWeek`, `renderWeeklyDutyMonthPreview` |
| 月間予定表プレビュー(教員用/家庭用) | `renderMonthlyPreview`, `PV_COLS_TEACHER`, `PV_COLS_FAMILY` |
| 年間予定表(教員用/家庭用、表示内容選択可) | `renderAnnualPreview`, `buildAnnualEventParts`, `annualEventHtml/Text` |
| 時数・集計(月/学期/年、必要時数比較、教科別実績、給食喫食数) | `computeStatsForRange`, `getTermMonths`, `renderStats`, `renderRequiredHoursEditor`, `computeAnnualMealSummary` |
| 印刷部数の記録(履歴ログ) | `renderPrintCountList`(設定タブ) |
| 表示色の設定(土日祝網掛け・欠セル) | `applyColorSettings`, `lightenHex`, `state.colors` |
| Excel出力(自前ZIP/OOXML実装) | `buildZipStore`, `buildXlsxBytes`, `buildSheetXml`, `buildXlsxStyles`, `buildMonthlyExportGrid`, `buildAnnualExportGrid` |
| PDF/印刷(自動縮小付き) | `printCurrentTab`(内容の実測幅から`zoom`スタイルを計算) |
| 編集パスワードロック | `applyLockUI`, `openPasswordModal`, `submitPasswordUnlock`, `editUnlocked`変数 |
| 自動バックアップ(IndexedDBにフォルダハンドル保存) | `openBackupDB`, `idbSet/idbGet`, `runBackupNow`, `maybeRunBackup` |
| サンプルデータ生成/即時読込 | `generateSampleState`, `exportSampleData`, `loadSampleDataInMemory`(**注**: 内部で`editUnlocked`を`false`にリセットするため、呼び出し順に注意) |
| ファイル保存(File System Access API) | `doOpen`, `doSaveAs`, `doSave`, `writeToHandle`, `supportsFSA`変数 |
| 保存/読込(内部形式) | `loadStateFromText(text)` (= `JSON.parse`→`normalizeState`)、保存は各所で`JSON.stringify(state)` |

## テスト方法

自動テストフレームワークは導入していない。Claude Codeの Browser pane(MCP: `mcp__Claude_Browser__*`)を使い、その場でJavaScriptを書いて動作検証するスタイル。典型的なパターン:

```js
// preview_start({name:'static-server'}) → navigate({url:'http://localhost:8791/行事予定管理アプリ_Phase1.html'}) の後
loadSampleDataInMemory();       // サンプルデータを読み込む(内部でeditUnlockedがfalseにリセットされる)
editUnlocked = true;            // ★必ずサンプルデータ読込の"後"に true にすること
applyLockUI();
document.querySelector('nav.tabs .tab[data-tab="stats"]').click();
// ...操作・検証...
loadStateFromText(JSON.stringify(emptyState())); // 最後に空状態へリセットしておく
```

Excel出力の関数(`exportTeacherMonthlyXlsx`等)は内部で`downloadBytes(bytes, name)`を呼んでファイル保存ダイアログを開こうとするため、テスト時は`window.downloadBytes`を一時的に差し替えてバイト列だけ捕捉するとよい。

コンソールエラーは`read_console_messages({onlyErrors:true})`で確認。`favicon.ico`の404と、ページを離れる際の`beforeunload`ブロック警告は無害なので無視してよい。

## Git / GitHub運用ルール(ユーザーのグローバル指示、`C:\Users\idolo\.claude\CLAUDE.md`より)

- アプリファイルを編集・追加・削除したら、**確認を取らず自動的に**:
  1. `C:\Users\idolo\Documents\projects\appcopy\行事予定アプリ\`へ`.git`を除く全体をミラーコピー(`cp -r`。誤って`.git`が混入したら`rm -rf`で除去すること)
  2. `git add` → `git commit`(変更内容が分かる日本語コミットメッセージ) → `git push origin master`
- **force pushは絶対にしない**。pushが拒否された場合は状況をユーザーに報告して指示を仰ぐ(force pushで解決しない)。
- リモート未設定やpush失敗時は黙って握りつぶさず、ユーザーに知らせる。

## 既知の未実装・今後の課題(2026年9月時点)

- **2026年度Excelデータのインポート変換**: 上記「進行中のタスク」参照。ファイル提供待ち。
- `TODO.md`(プロジェクトルート)に、その他の将来アイデアのメモがある。

## 直近の変更履歴(新しい順、Claude Codeでの作業分)

1. **教科別実績の手入力機能**(2026-09-07): 「時数・集計」タブに、道徳・総合・特別活動・音楽を除く8教科について実際の授業コマ数を手入力できる表を追加。「必要時数との比較」表にも反映。
2. **GitHubリポジトリ新規作成・自動push運用の開始**(2026-09-07): `gyoji-yotei-app`(Private)を作成、`origin`に設定。以降アプリファイル変更時は自動commit+push。
3. **印刷部数記録・年間給食喫食数と給食のない日一覧・必要時数比較への自由入力行**(2026-09-07): 設定タブに印刷部数の履歴ログ、時数・集計タブに給食喫食数集計(宿泊行事の例外はチェックボックスで都度切替)、必要時数比較に「その他」手入力行を追加。年度変更ボタンのラベル更新漏れバグも修正。
4. **17項目の一括改善**(2026-09-07以前): 印刷枚数記録・給食のない日一覧・行事時間数の自由入力・年間予定の右揃え/A3縦向き調整・行事等の複数枠入力モーダル・セル色の設定化・欠セル暗色化・非登校日の網掛け・週番縦書き・入力グリッドのグループ罫線 等。詳細はGitHubのコミット履歴を参照。
5. それ以前(候補プール機能、Excel書式を原本の罫線・色分けに合わせる調整、時限・週番列のスリム化等)は`git log`参照。

## Claude Codeでの作業にあたって

- ファイルは1つのHTMLファイルにすべて収まっているため、`Grep`/`Read`/`Edit`ツールで該当箇所を探しながらの編集が基本。
- **Editツールの既知の罠**: ソース中の全角空白(`\u3000`)を含む文字列を`old_string`にすると、ツールの文字列マッチングが失敗することがある。全角空白をまたぐ既存行を編集する代わりに、ASCII文字だけで前後をマッチさせるか、新しいステートメントを追加する形で回避する。
- 変更後は必ずBrowser paneで実際にアプリを動かして確認する(コンソールエラーの有無、対象機能の動作、既存機能への影響)。見た目だけでなく`javascript_exec`で`state`の中身も検証すること。
- 変更のたびに、appcopyへのミラーコピーとGitHubへのcommit+pushを行う(上記「Git / GitHub運用ルール」参照)。
