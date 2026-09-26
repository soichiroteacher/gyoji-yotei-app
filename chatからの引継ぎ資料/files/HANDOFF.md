# 引き継ぎ資料: 中学校行事予定管理アプリ

Claude(claude.aiおよびClaude Code)での対話を通じて開発してきた、中学校の行事予定管理アプリの引き継ぎ資料です。
このアプリは、元々Excelマクロで運用されていた行事予定管理を、ブラウザだけで完結するツールに置き換えるために作られました。

**この資料は、有料プランの期限切れなど何らかの理由でこれまでの会話履歴が引き継げなくなった場合でも、
新しいClaudeセッションがこのファイルとGitHubリポジトリだけを見て作業を再開できるように書かれています。**

## 現在の状況と次にやること(2026-09-26時点・まずここを読む)

**現在の状態**: 作業は一区切りした状態で、途中で止まっている実装はない。最新コミットはGitHubにpush済み、appcopyにもミラー済み。個々の変更の経緯・理由は`TODO.md`(項目1〜31)に詳しく記録してある。ユーザー向けの変更内容は、アプリの「使い方 / マニュアル」→「更新履歴」にある。

**タブ構成(現在)**: 予定入力 / 月間予定表(教員用・家庭用を画面上部のボタンで切替) / 年間予定表(同) / 給食 / 時数・集計 / 実績 / 設定。「年間予定入力」は2026-09-26に「予定入力」へ改称した(TODO.mdの過去項目やアプリの更新履歴の過去分には旧名が残っている)。

**ユーザーの返答待ち・未解決のもの**

- **「繋がってない」の件(2026-09-25頃)**: ユーザーから「繋がってない」とだけ届き、何を指すか尋ねたが返答がないまま別の依頼に移った。候補は、前年度から作った候補が入力画面に出ない/GitHub Pagesやappcopyの版が古い/ファイルの読み書き、など。次回、話題に出たら確認する。
- **GitHubリポジトリの公開状態**: リポジトリは**PUBLIC**(作成時はPrivateのつもりだったが、`gh repo view`で公開状態と確認済み)。2026-09-25の見直しでユーザーに提示したが、「対応しない(このまま)」との判断。サンプルデータのみで実データは含まれていないことは確認済み。**実データ(実在の学校名「横川中学校」や本番の予定)は絶対にこのフォルダに置かない・コミットしないこと。**
- **実績タブの全校表示の横幅**: 全校表示は列(学級×6)がかなり横長になる。使ってみて見づらければ、教科名を略称で表示するなどの調整を提案済み。

**実物のExcelでの見た目確認が必要なもの**(この環境にはExcel/LibreOfficeが無く、画面上で見た目を確かめられない。ユーザーが実際のExcelで確認して指摘する流れになっている)

- 月間Excel: 日・曜26px/週番27px相当、掃除・給食・授業2.8、学校行事等は元の8割(48)。境目の罫線はthin・グレー(808080)。見出しの背景色なし。
- 年間Excel: ○月行事欄8pt・行事ごとに改行、存在しない日の斜線、年計等の④、見出しの揃え(日・曜は左、○月行事は中央)。

**次にやるとよいこと(候補)**

1. 実績タブを実際の時間割で試してもらい、使い勝手のフィードバックを受ける(全校表示の横幅、貼り付けの数字→時限の対応が学校の運用に合うか、●のコマの扱い)。
2. 特別支援学級(5組)は実績タブ・時数集計の対象外にしてある。5組にも目標時数を持たせたいか、必要に応じて確認する。
3. 授業週数(時数・集計タブ)の数え方が学校の数え方と合っているか、実データで確認してもらう(サンプルデータは長期休業が設定されていないため週数が多めに出る)。
4. `TODO.md`の「保留」の記載(項目9の他の見出しの見づらさ等)は、ユーザーから再度依頼があれば対応。

## どこに何があるか(最重要)

- **ローカル作業フォルダ**: `C:\Users\idolo\Documents\projects\myapp\行事予定アプリ\`
- **本体ファイル**: `index.html`(単一HTMLファイル、ビルド不要。旧ファイル名`行事予定管理アプリ_Phase1.html`から2026-09-09にリネーム。GitHub Pagesでリダイレクトなしにそのまま開けるようindex.htmlに統一した)
- **旧ファイル名の転送ページ**: `行事予定管理アプリ_Phase1.html`は11行だけのリダイレクトページ(旧URLのブックマークが404にならないよう残している)。本体ではないので編集しない。
- **GitHubリポジトリ(バックアップ・履歴管理)**: https://github.com/soichiroteacher/gyoji-yotei-app (**PUBLIC**。作成時はPrivateのつもりだったが実際は公開状態。GitHub Pagesでも公開されている。`origin`、ブランチ`master`)
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

**2026年度Excelデータのインポート変換 — 完了(2026-09-08)**: ユーザーが実際に使っている2026年度(R8)の運用Excel(`original.xlsm`と同系統、マクロ入り)を受け取り、Claude側で直接読み取って変換した。方針通り、アプリ側に汎用インポート機能は作らなかった。

- 元ファイルの構造: 月ごとのシートではなく、`DB全体`(全員向け行事)・`DB教のみ`(教員のみ向け)・`DB他欄`(会議/掃除/部活動/給食/①〜⑥時限など、日付ごとに1行)という年間分をまとめて持つマスターシートを、マクロが`4月職`/`4月生`等の表示用シートに反映する仕組みだった。`DB他欄`の列28・29に、`DB全体`/`DB教のみ`の内容が全角スペース区切りで**既に結合済みの状態**で入っていたため、そこから直接読み取れば済んだ(個別の行事欄を自分で結合する必要はなかった)。
- 変換手順: `unzip`でxlsmを展開 → PowerShellの`[xml]`でシートXML+`sharedStrings.xml`を解析しTSVへダンプ(自作スクリプト。**重要**: 日本語コメントを含む`.ps1`ファイルはUTF-8 BOM付きで保存しないと、Windows PowerShell 5.1がシステムのコードページで誤読し、コード自体が壊れて分かりにくい形で誤動作する。実際にこれで丸1回ハマった) → TSVをアプリのローカルサーバー配下に置いてブラウザ側`fetch`で読み込み → ブラウザのJSでアプリ自身の`emptyDayRecord`/`getDayStatus`/`normalizeState`等の関数を使って`state`を組み立てる(自前で型を再現するより安全) → 一時的に`static-server.ps1`へ`POST /__save_import`エンドポイントを追加し、組み立てた`state`をそのままファイルへ書き出させた(作業後は元に戻した)。
- 学年別の登校日(`schoolDay`)は元データに明示的な列が無かったため、「平日かつ祝日でないのに①〜⑥が学年別に全て空欄」→非登校日、「土日祝なのに①〜⑥に何か入っている」→登校日(土曜授業等)、という推定ロジックで補完した。実際に春季/夏季/冬季休業日や、学校公開等の特別土曜授業を正しく検出できた。境界日や校外学習日など、まれに誤判定の可能性があるため、インポート後はユーザー側で年間予定表をひと通り目視確認することが望ましい。
- 未対応のまま(元データに対応する列が見当たらなかった、または対応が難しかった)フィールド: `weeklyDuty`(週番)、`memo`(このアプリで新設した項目)、`staffMeeting`/`planningMeeting`のチェックボックス(元データでは「会議」欄に「【職員会議】」のようにテキストで注記されているのみで、別列としては存在しなかった。テキスト自体は`meeting`欄にそのまま取り込み済み)。
- **重要な注意**: 変換済みファイルは実在の学校名・行事内容を含む本番データのため、`C:\Users\idolo\Documents\projects\myapp\`直下(行事予定アプリのGitリポジトリの**外**)に保存した。**このアプリのプロジェクトフォルダ(`行事予定アプリ`)の中には、実データやそれに類するファイルを絶対に置かないこと**(appcopyミラーとGitHub自動pushの対象になり、公開リポジトリに実データが漏れる恐れがあるため)。作業用に一時フォルダ(`.importwork`等)を切る場合も、作業完了後は必ずプロジェクトフォルダの外へ移すか削除すること。

## 環境上の注意(重要)

- **Python・Node.jsは実質使用不可**: `python`/`python3`/`node`はWindowsのApp Execution Alias(Microsoft Storeへの誘導スタブ)がPATH上にあるだけで、実体がインストールされていない。`xlsx`スキル(openpyxl等)はこの環境では使えないため、Excelファイルを読む場合はGit Bashの`unzip`コマンド、またはPowerShellの`System.IO.Compression.ZipFile`/`[xml]`キャスト(いずれも.NET標準機能でzip展開・XML解析が可能)を使うこと。
- ローカルサーバーは`.claude/static-server.ps1`(PowerShell製の簡易HTTPサーバー、`.claude/launch.json`の`static-server`設定でClaude Codeの`preview_start`から起動)を使う。`file://`直開きは`crypto.subtle`/`localStorage`がブロックされるため不可。
- 動作確認はClaude Codeの Browser pane (MCPツール`mcp__Claude_Browser__*`)で、実際にページを操作・`javascript_exec`でstateを検証する方式で行っている。Playwright/Node.jsは使っていない(過去の資料に記載があったが誤り、本資料で訂正)。

## データモデル(`state`オブジェクト、2026-09-26時点)

```js
{
  formatVersion: 7,
  meta: {
    schoolName: '',
    fiscalYear: 2026,              // 4月始まりの年度
    term2StartMonth: 8,            // 2学期開始月(既定8月)
    term3StartMonth: 1,            // 3学期開始月(既定1月)
    editPasswordHash: '<SHA-256>', // 編集パスワードのハッシュ(既定"0000")
    versionLabelMonthly: '', versionLabelAnnual: '', // 予定表タイトルに添える版表記(年間Excelでは【】付きで表示)
    supportClassEnabled: false,    // 特別支援学級(4つ目の学級=内部ではg4)を使うか
    supportClassName: '5組'        // その表示名
  },
  manualDayFlags: {},              // 廃止済み・空のまま維持(読込時に自動移行)
  holidayOverrides: {},            // "YYYY-MM-DD" -> {type:'add'|'remove', label:''} 祝日の手動例外
  eventCandidates: [],             // 行事の候補プール。{id, text, useCount, repeatable?}
  colors: { holiday:'D9D9D9', absent:'BFBFBF' }, // 土日祝の網掛け色・欠セルの色(6桁HEX)
  mealCountOverrides: {},          // "YYYY-MM-DD" -> {g1..g4:bool} 宿泊行事等の給食扱い例外
  printCounts: [],                 // 印刷部数の履歴ログ
  eventHourEntries: [],            // 授業枠に載らない行事の時数(時数・集計タブで手入力)
  editLock: { active:false, since:null },
  days: {
    "YYYY-MM-DD": {
      eventsAnnual: '', eventsMonthly: '', eventsTeacherOnly: '', eventsTeacherOnlyAnnual: '',
      meeting: '', planningMeeting: false, staffMeeting: false,
      cleaning: null, club: '',
      lunch: {g1:'', g2:'', g3:'', g4:''},     // '○' / '×' / '弁' / ''(自由入力)
      periods: {g1:[6個], g2:[...], g3:[...], g4:[...]}, // ①〜⑥。'1'〜'6'/'道'/'総'/'学'/'行'/'音'/'弁'/'欠'/'●'
      schoolDay: {g1:null, g2:null, g3:null, g4:null},   // 学年別登校日。null=自動、true/false=明示
      weeklyDuty: '', memo: '', memoInOutput: false
    }
  },
  presets: {...}, colWidths: {},
  requiredHours: { g1:{国語:140,...,特別活動:35}, g2:{...}, g3:{...} }, // 必要時数(=実績タブの目標)。12教科
  annualSettings: { teacher:{...}, family:{...} },
  // ▼実績タブ(2026-09-26新設)
  classCounts: { g1:3, g2:3, g3:3 },   // 学年ごとの学級数(1〜12)。学級IDは "学年-組"(例 "1-2"=1年2組)
  classTimetables: { "1-2": {1:[①〜⑥の教科], ..., 5:[...]} }, // 学級ごとの基本時間割(キーは曜日 1=月〜5=金)
  actuals: { "YYYY-MM-DD": { "1-2": [①〜⑥] } } // 実績タブで書き換えたコマだけ保存。null=自動判定のまま、''=授業なし、文字列=教科
}
```

`normalizeState(obj)`が、欠けているフィールドを古い`.dat`ファイル読込時に自動補完する(後方互換)。**新しいフィールドを追加した際は必ずこの関数にも追記すること。** 廃止したフィールド`extraHours`(その他の手入力)・`subjectActuals`(教科別実績の手入力)は、読込時に`delete`で取り除いている。

### 「①〜⑥」欄と実績タブの関係(2026-09-26に方針変更)

以前は「①〜⑥の数字は時限の便宜番号で、教科の推定には使わない」方針だったが、実績タブの新設時にユーザーと確認して次のように決めた。
- 実績タブの各コマの初期値は、予定入力の①〜⑥から決める。「道」「総」「学」「音」→道徳・総合・特別活動・音楽として自動で数える。「1」〜「6」「●」は授業コマとして教科を入力する(未入力は「教科未入力のコマ」として数える)。「行」「欠」「弁」・空欄は授業なし。
- 実績タブではどのコマも手で書き換えられる(`state.actuals`に保存。予定入力側には反映しない)。自動の教科を消すと''(授業なし)、元の教科を入れ直すと書き換えは取り消し(null)。
- 基本時間割の「この月に貼り付け」では、①〜⑥の数字を**基本時間割の何時間目か**として扱う(例: 月曜の③に「2」→月曜の②の教科)。既に教科が入っているコマと「●」のコマは変更しない。
- 教科名は`normalizeSubject`で必要時数の12教科名にそろえる(「国」→国語、「英」→外国語、「保体」→保健体育、「学活」→特別活動 等)。
- 特別支援学級(5組、g4)は目標時数が無いため実績タブ・時数集計の対象外。
## 主要機能と対応する関数(概略)

| 機能 | 関連関数 |
|---|---|
| 祝日自動計算 | `computeNationalHolidays`, `nationalHolidaysFor`, `getDayStatus` |
| 学年別登校日判定 | `isSchoolDay`, `anySchoolDay`(いずれかの学年), `allSchoolDay`(全学年。土曜の網掛けを外す判定に使用), `activeGrades`(5組有効時は4を含む) |
| タブ切替(月間/年間は教員用・家庭用を画面内で切替) | `showView`, `viewForTab`, `monthlyAudience`/`annualAudience`変数、`.aud-switch[data-kind]` |
| 入力グリッド描画 | `renderAgenda`, `attachGridHandlers`, `COLS`定義, `getFieldValue`/`setFieldValue` |
| 複数セル選択・コピペ | `paintSelection`, `selectionBounds`, `document.addEventListener('paste'/'keydown')` |
| メモの複数行モーダル | `openMemoModal`/`closeMemoModal` |
| 行事候補プール・複数枠入力モーダル | `openEventPickerModal`, `renderEventPickerBoxes`, `eventPickerBoxValues`, `renderEventCandidateList`, `importPreviousYearEventsToCandidates` |
| 週番まとめて入力(週単位/月ローテーション) | `openWeeklyDutyModal`, `getWeeksForMonth`, `applyWeeklyDutyToWeek`, `renderWeeklyDutyMonthPreview` |
| 月間予定表プレビュー(教員用/家庭用) | `renderMonthlyPreview`, `PV_COLS_TEACHER`, `PV_COLS_FAMILY` |
| 年間予定表(教員用/家庭用、表示内容選択可) | `renderAnnualPreview`, `buildAnnualEventParts`, `annualEventHtml/Text` |
| 給食タブ(給食実施日数・喫食数・給食のない日) | `renderMealsTab`, `computeStatsForRange`, `computeAnnualMealSummary` |
| 時数・集計タブ(授業日数・授業週数、行事時数、日課内訳、曜日時限別) | `renderStats`, `renderTeachingDaysWeeksPanel`, `computeTeachingDaysWeeks`, `computeStatsForRange`, `getTermMonths` |
| 必要時数の編集(設定タブ) | `renderRequiredHoursEditor` |
| 実績タブ(学級ごとの時間割・教科時数) | `renderActualsTab`, `renderActualsSummary`, `createActGrid`(Excel風の表。選択・コピペ・キー操作・IME対応), `tallyActuals`, `actualEffective`/`actualOverride`/`setActualOverride`, `normalizeSubject`, `actPushUndo`/`actUndo`/`actRedo`(実績タブ専用の元に戻す), `classList`/`actScopeClasses` |
| 印刷部数の記録(履歴ログ) | `renderPrintCountList`(設定タブ) |
| 表示色の設定(土日祝網掛け・欠セル) | `applyColorSettings`, `lightenHex`, `state.colors` |
| Excel出力(自前ZIP/OOXML実装) | `buildZipStore`, `buildXlsxBytes`, `buildSheetXml`, `buildXlsxStyles`, `buildMonthlyExportGrid`, `buildAnnualExportGrid` |
| Excelの書式番号 | `XSTYLE`(名前→`cellXfs`の位置)。**`buildXlsxStyles`の`<xf>`の並び順と`XSTYLE`の番号は1対1で対応**するので、書式を追加・削除したら両方を合わせ、`cellXfs count`も直す。境目・外枠の太線は`xstyleToRight/Left/Bottom/...`で派生書式に置き換えている(`rightThickCols`) |
| PDF/印刷(自動縮小付き) | `printCurrentTab`(内容の実測幅から`zoom`スタイルを計算) |
| 編集パスワードロック | `applyLockUI`, `openPasswordModal`, `submitPasswordUnlock`, `editUnlocked`変数 |
| 自動バックアップ(IndexedDBにフォルダハンドル保存) | `openBackupDB`, `idbSet/idbGet`, `runBackupNow`, `maybeRunBackup` |
| サンプルデータ生成/即時読込 | `generateSampleState`, `exportSampleData`, `loadSampleDataInMemory`(**注**: 内部で`editUnlocked`を`false`にリセットするため、呼び出し順に注意) |
| ファイル保存(File System Access API) | `doOpen`, `doSaveAs`, `doSave`, `writeToHandle`, `supportsFSA`変数 |
| 保存/読込(内部形式) | `loadStateFromText(text)` (= `JSON.parse`→`normalizeState`)、保存は各所で`JSON.stringify(state)` |

## テスト方法

自動テストフレームワークは導入していない。Claude Codeの Browser pane(MCP: `mcp__Claude_Browser__*`)を使い、その場でJavaScriptを書いて動作検証するスタイル。典型的なパターン:

```js
// preview_start({name:'static-server'}) → navigate({url:'http://localhost:8791/'}) の後
loadSampleDataInMemory();       // サンプルデータを読み込む(内部でeditUnlockedがfalseにリセットされる)
// 編集モードにする(★サンプルデータ読込の"後"に行う)。submitPasswordUnlockは引数を取らず、
// #pwInput の値を読む点に注意(submitPasswordUnlock('0000')と書いても解除されない)
document.getElementById('pwInput').value = '0000';
await submitPasswordUnlock();
document.querySelector('nav.tabs .tab[data-tab="stats"]').click();
// ...操作・検証...
```

- 実績タブの表(`createActGrid`)の操作は、`td.ag-cell`への`mousedown`、`.ag-editor`への`input`/`keydown`/`ClipboardEvent('paste'|'copy', {clipboardData: new DataTransfer()})`をdispatchして検証できる。
- Browser paneのスクリーンショットは縮小されて返ることがある(ページは1280px幅でも画像は800px)。座標をクリックする場合は、`getBoundingClientRect`の値をそのまま使うとずれるので注意。
- `window.confirm`を使う機能(貼り付けの確認など)は、テスト時に`window.confirm = ()=>true`で置き換える。

Excel出力の関数(`exportTeacherMonthlyXlsx`等)は内部で`downloadBytes(bytes, name)`を呼んでファイル保存ダイアログを開こうとするため、テスト時は`window.downloadBytes`を一時的に差し替えてバイト列だけ捕捉するとよい。

コンソールエラーは`read_console_messages({onlyErrors:true})`で確認。`favicon.ico`の404と、ページを離れる際の`beforeunload`ブロック警告は無害なので無視してよい。

## Git / GitHub運用ルール(ユーザーのグローバル指示、`C:\Users\idolo\.claude\CLAUDE.md`より)

- アプリファイルを編集・追加・削除したら、**確認を取らず自動的に**:
  1. `C:\Users\idolo\Documents\projects\appcopy\行事予定アプリ\`へ`.git`を除く全体をミラーコピー(`cp -r`。誤って`.git`が混入したら`rm -rf`で除去すること)
  2. `git add` → `git commit`(変更内容が分かる日本語コミットメッセージ) → `git push origin master`
- **force pushは絶対にしない**。pushが拒否された場合は状況をユーザーに報告して指示を仰ぐ(force pushで解決しない)。
- リモート未設定やpush失敗時は黙って握りつぶさず、ユーザーに知らせる。

## 既知の未実装・今後の課題(2026-09-26時点)

- 冒頭の「現在の状況と次にやること」を参照(返答待ちの件、Excel実物での確認事項、次の候補)。
- 2026年度Excelデータのインポート変換は完了済み(上記参照)。`weeklyDuty`(週番)と職員会議・企画会議のチェックボックスは元データに対応する列が無く未設定のまま。ユーザーが必要に応じて手動入力する想定。
- 実績タブの元に戻す履歴(Ctrl+Z)はページを開いている間だけ有効(再読み込みや別ファイル読込で消える)。
- 年間予定表の年計・学期計・月計の注記(画面下)は「①〜⑥のいずれかが入力されている日数」と書かれているが、実際の集計は登校日(`isSchoolDay`)の日数。見直す場合は注記か集計のどちらかをそろえる。
- `TODO.md`(プロジェクトルート)が詳細な作業ログ兼アイデアメモ。「保留」と書かれた項目は、ユーザーから再依頼があれば対応する。
- ユーザーは白黒印刷が前提。Excelや画面で「色だけで区別する」表現は避ける(休業日・欠の網掛けと、実績タブ等の画面上の補助色は現状維持でよいと確認済み)。
- 見た目(幅・フォント・揃え等)の判断が分かれる変更は、実装前にユーザーに確認する(ユーザーの希望)。

## 直近の変更履歴(新しい順、Claude Codeでの作業分)

詳細は`TODO.md`の各項目と`git log`を参照。2026-09-10〜09-26の主な変更:

- **09-26**: 時数・集計タブに授業日数・授業週数(登校日のある週/日数÷5)を追加(TODO 31)。「年間予定入力」→「予定入力」に改称(30)。実績タブを新設し(28)、学年・全校の一覧+Excel風操作に作り直し(29)。月間・年間予定表の教員用/家庭用タブを統合(27)。
- **09-25**: 全体見直しで見つかった不具合修正(予定入力タブの土曜網掛け、1〜3月の年度表記、未使用データ・Excel書式の削除、参考シートの色廃止など。TODO 26)。
- **09-10〜09-11**: 特別支援学級(5組)の追加、給食タブの分離、月間・年間Excelの大幅な書式調整(和暦、列幅、罫線、斜線、フォント、背景色の廃止)、土曜の網掛けを「全学年登校時のみ外す」に変更、行事候補の使用済みを既定で隠す、前年度候補の取り込み改善、登校日へまとめて入力、バージョン表示に時刻(TODO 10〜25)。

0. **2026年度Excelデータのインポート変換**(2026-09-08): 実際の運用Excel(.xlsm)を読み取り、`.dat`ファイルへ変換。詳細は上記「2026年度Excelデータのインポート変換 — 完了」参照。アプリ本体のコード変更は伴わない(データ移行のみ)。

1. **教科別実績の手入力機能**(2026-09-07): 「時数・集計」タブに、道徳・総合・特別活動・音楽を除く8教科について実際の授業コマ数を手入力できる表を追加。「必要時数との比較」表にも反映。
2. **GitHubリポジトリ新規作成・自動push運用の開始**(2026-09-07): `gyoji-yotei-app`(Private)を作成、`origin`に設定。以降アプリファイル変更時は自動commit+push。
3. **印刷部数記録・年間給食喫食数と給食のない日一覧・必要時数比較への自由入力行**(2026-09-07): 設定タブに印刷部数の履歴ログ、時数・集計タブに給食喫食数集計(宿泊行事の例外はチェックボックスで都度切替)、必要時数比較に「その他」手入力行を追加。年度変更ボタンのラベル更新漏れバグも修正。
4. **17項目の一括改善**(2026-09-07以前): 印刷枚数記録・給食のない日一覧・行事時間数の自由入力・年間予定の右揃え/A3縦向き調整・行事等の複数枠入力モーダル・セル色の設定化・欠セル暗色化・非登校日の網掛け・週番縦書き・入力グリッドのグループ罫線 等。詳細はGitHubのコミット履歴を参照。
5. それ以前(候補プール機能、Excel書式を原本の罫線・色分けに合わせる調整、時限・週番列のスリム化等)は`git log`参照。

## Claude Codeでの作業にあたって

- ファイルは1つのHTMLファイルにすべて収まっているため、`Grep`/`Read`/`Edit`ツールで該当箇所を探しながらの編集が基本。
- **Editツールの既知の罠**: ソース中には全角空白を`\u3000`という6文字のエスケープで書いた箇所がある。これを`old_string`に含めるとツール側で実際の全角空白に変換されてしまい、一致しない。その場合は、PowerShellで`[System.IO.File]::ReadAllText`→`IndexOf`で位置を特定→文字列を差し替え→`UTF8Encoding($false)`(BOMなし)で書き戻す方法で編集する。
- **環境**: Node.js・Pythonは使えない(Git BashやPowerShellで代用)。
- **バージョン表示**: 変更のたびに`APP_VERSION`(index.html冒頭付近、`'YYYY-MM-DD HH:MM'`形式)を現在時刻に更新する。Git Bashで`sed -i "s/const APP_VERSION = '[^']*';/const APP_VERSION = '$(date '+%Y-%m-%d %H:%M')';/" index.html`
- 変更後は必ずBrowser paneで実際にアプリを動かして確認する(コンソールエラーの有無、対象機能の動作、既存機能への影響)。見た目だけでなく`javascript_exec`で`state`の中身も検証すること。
- 変更のたびに、appcopyへのミラーコピーとGitHubへのcommit+pushを行う(上記「Git / GitHub運用ルール」参照)。
- **アプリ内の更新履歴を必ず更新する**: 「使い方 / マニュアル」モーダル(`helpBody`のinnerHTML、`<h4>更新履歴</h4>`の直後にある`<ul class="changelog">`)に、ユーザー向けの平易な言葉で1行追記する(実装の詳細ではなく「何ができるようになったか」を書く)。日付は`YYYY-MM-DD`形式。ユーザーがGitHubのコミット履歴を見なくても、アプリを開くだけで変更内容を確認できるようにするための機能なので、機能追加・不具合修正のたびに欠かさず追記すること。
