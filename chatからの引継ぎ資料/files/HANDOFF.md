# 引き継ぎ資料: 中学校行事予定管理アプリ

Claude(claude.ai)での対話を通じて開発してきた、中学校の行事予定管理アプリの引き継ぎ資料です。
このアプリは、元々Excelマクロで運用されていた行事予定管理を、ブラウザだけで完結するツールに置き換えるために作られました。

## 概要

- **形式**: 単一のHTMLファイル(ビルド不要、ブラウザで直接開くだけで動作)
- **対応ブラウザ**: Chrome / Edge 推奨(File System Access APIを使用するため)。他ブラウザではダウンロード方式にフォールバック
- **依存ライブラリ**: なし(外部ライブラリ・CDN一切不使用。Excel生成も自前のZIP/OOXML実装)
- **現在のファイル**: `行事予定管理アプリ_Phase1.html`(約3,270行、単一`<script>`タグ内にほぼ全ロジック)
- **データ形式**: `.dat`拡張子のJSONファイル(`state`オブジェクトをそのままシリアライズ)。`formatVersion: 7`

## 元になったExcelファイル

`original.xlsm`という中学校の年間行事予定表マクロファイルを参照しながら開発しました。シート構成:
- 入力: 設定、DB全体、DB教のみ、DB他欄
- 出力: 4月職(教員用月間)、4月生(家庭用月間)、年(職員)、年(生徒)
- 集計: 行事一覧、時数集計、給食日数

このファイルは会話の初期段階でのみ参照し、現在の作業ディレクトリには残っていない可能性があります(必要なら再アップロードが必要です)。

## データモデル(`state`オブジェクト)

```js
{
  formatVersion: 7,
  meta: {
    schoolName: '',
    fiscalYear: 2026,              // 4月始まりの年度
    term2StartMonth: 8,            // 2学期開始月(既定8月)
    term3StartMonth: 1,            // 3学期開始月(既定1月)
    editPasswordHash: '<SHA-256>', // 編集パスワードのハッシュ(既定"0000")
  },
  manualDayFlags: {},              // 【廃止済み・空のまま維持】旧「休業/登校 変更」列の名残。
                                    // 読み込み時にnormalizeState()が自動的に
                                    // eventsAnnual+schoolDayへ移行してから空にする
  days: {
    "YYYY-MM-DD": {
      eventsAnnual: '', eventsMonthly: '', eventsTeacherOnly: '',
      meeting: '', planningMeeting: false, staffMeeting: false,
      cleaning: '', club: '',
      lunch: {g1:'', g2:'', g3:''},        // '○' または '' (チェックボックス化済み)
      periods: {g1:['','','','','',''], g2:[...], g3:[...]}, // ①〜⑥
      schoolDay: {g1:null, g2:null, g3:null}, // 学年別登校日。null=自動判定、true/false=明示的上書き
      weeklyDuty: '', memo: '', memoInOutput: false
    }
  },
  presets: { cleaning:[...], club:[...], lunch:[...](未使用), period:[...], weeklyDuty:[...] },
  colWidths: {},                   // 入力グリッドの列幅(px)
  requiredHours: {                 // 学習指導要領の年間標準授業時数(編集可能)
    g1:{国語:140,...}, g2:{...}, g3:{...}
  },
  annualSettings: {                // 年間予定表(教員用/家庭用)の表示設定
    teacher: {includeMonthly:true, includeTeacherOnly:true, includeMeeting:false},
    family:  {includeMonthly:true, includeTeacherOnly:false, includeMeeting:false}
  }
}
```

### 重要な設計変更の経緯(直近)

1. **登校日チェックボックス方式への移行**: 当初は右端に「休業/登校 変更」というテキスト列があり、手動で休業日/登校日を上書きする方式でしたが、学年ごとに独立した「登校日」チェックボックス(①〜⑥の左、給食の右)に置き換えました。チェックが外れているとその学年の①〜⑥欄が入力不可になります。`isSchoolDay(y,m,d,grade)`関数が判定の基点で、時数集計・年間予定表の年計/学期計/月計もすべてこれを参照します。
2. **給食のチェックボックス化**: 給食欄も文字入力(○/×等)からチェックボックスに変更(あるかないかのみ)。
3. **年度は設定タブでのみ変更可能**: 他のタブでは表示のみ(`fyDisplay`等のspan要素)。年間予定入力タブには月送りボタン(◀▶)を追加。

## 主要機能と対応する関数(概略)

| 機能 | 関連関数 |
|---|---|
| 祝日自動計算 | `computeNationalHolidays`, `nationalHolidaysFor`, `getDayStatus` |
| 学年別登校日判定 | `isSchoolDay` |
| 入力グリッド描画 | `renderAgenda`, `attachGridHandlers`, `COLS`定義 |
| 複数セル選択・コピペ | `paintSelection`, `selectionBounds`, `document.addEventListener('paste'/'keydown')` |
| メモの複数行モーダル | `openMemoModal`/`closeMemoModal` |
| 週番まとめて入力(週単位/月ローテーション) | `openWeeklyDutyModal`, `getWeeksForMonth`, `applyWeeklyDutyToWeek`, `renderWeeklyDutyMonthPreview` |
| 週番のセル結合表示(プレビュー/Excel) | `renderMonthlyPreview`内のrun計算、`buildMonthlyExportGrid`内のrun計算 |
| 月間予定表プレビュー(教員用/家庭用) | `renderMonthlyPreview`, `PV_COLS_TEACHER`, `PV_COLS_FAMILY` |
| 年間予定表(教員用/家庭用、表示内容選択可) | `renderAnnualPreview`, `buildAnnualEventParts`, `annualEventHtml/Text` |
| 時数・集計(月/学期/年、必要時数比較) | `computeStatsForRange`, `getTermMonths`, `renderStats`, `renderRequiredHoursEditor` |
| Excel出力(自前ZIP/OOXML実装) | `buildZipStore`, `buildXlsxBytes`, `buildSheetXml`, `buildMonthlyExportGrid`, `buildAnnualExportGrid` |
| PDF/印刷(自動縮小付き) | `printCurrentTab`(内容の実測幅から`zoom`スタイルを計算) |
| 編集パスワードロック | `applyLockUI`, `openPasswordModal`, `submitPasswordUnlock`, `editUnlocked`変数 |
| 自動バックアップ(IndexedDBにフォルダハンドル保存) | `openBackupDB`, `idbSet/idbGet`, `runBackupNow`, `maybeRunBackup` |
| サンプルデータ生成/即時読込 | `generateSampleState`, `exportSampleData`, `loadSampleDataInMemory` |
| ファイル保存(File System Access API) | `doOpen`, `doSaveAs`, `doSave`, `writeToHandle`, `supportsFSA`変数 |

## テスト方法

このプロジェクトでは自動テストフレームワークは導入せず、Playwright(Node.js)のヘッドレスブラウザを都度その場で書いて動作検証してきました。典型的なパターン:

```js
const { chromium } = require('playwright');
(async () => {
  const browser = await chromium.launch();
  const page = await browser.newPage({ viewport: { width: 1700, height: 900 } });
  await page.goto('file:///path/to/index3.html');
  await page.waitForTimeout(300);
  await page.click('#btnUnlock');
  await page.fill('#pwInput', '0000');   // 既定パスワード
  await page.click('#pwSubmit');
  // ...操作...
  await page.screenshot({path:'out.png'});
  await browser.close();
})();
```

Excel出力の検証は生成した`.xlsx`を`openpyxl`(Python)で読み込んで値・書式・結合セルを確認し、さらに`/mnt/skills/public/xlsx/scripts/office/soffice.py --headless --convert-to pdf`でLibreOffice変換して見た目も確認する、という二段構えで行っていました。

**既知の注意点**: LibreOfficeは`shrinkToFit`(セル内文字の自動縮小)の描画サポートが不完全で、実際にはセルに全文が入っているのに表示上は見切れて見えることがあります。実際のMicrosoft Excelでは正しく表示されます。判断に迷ったらセルの値そのもの(`cell.value`)を確認してください。

## 既知の未実装・今後の課題

設定タブのヘルプ文言(「まだ実装されていない機能」相当)に基づく残課題:

- **教科(国語・数学など)ごとの個別の時数記録・比較**: 現状、①〜⑥は「時限の便宜番号」を入力するだけで、どの教科かまでは記録していません。必要時数との比較も「道徳」「総合的な学習の時間」「特別活動」と「教科(数字入力分の合計)」までに留まります。個別教科ごとにしたい場合は、時間割(曜日×時限→教科のマッピング)という新しいデータ構造が必要になり、設計から検討が必要です。

## 直近のやり取りで対応した内容(新しい順)

1. PDF出力の自動縮小(用紙幅を超える内容を`zoom`で縮小してから印刷)、Excel出力の行高固定+`shrinkToFit`化、記号表示(【】・点線下線)の削除
2. サンプルデータの「すぐに試す」機能を`confirm()`/`alert()`から自前モーダルに変更(ネイティブダイアログが環境によって抑制され反応しないことがあったため)
3. 各学年①〜⑥の見出しに「(時限)」を追加
4. 年度設定を「設定」タブのみに一本化し、他タブは表示専用に。年間予定入力タブに月送りボタン(◀▶)を追加
5. 「入力済み◯/30日」表示の削除
6. 給食欄のチェックボックス化
7. 白黒印刷を考慮した色分け見直し(この後、【】と点線下線は不要と判断され上記1で削除済み)
8. 週番の入力候補(datalist)対応、月間予定表(教員用)でのセル結合表示、月まとめ入力(学年ローテーション)
9. サンプルデータをすぐに試す機能(元ファイルを誤って上書きしない安全策込み)
10. 必要時数(学習指導要領)の初期値・編集機能、自動バックアップ(IndexedDBでフォルダハンドル永続化)
11. 年間予定表の教員用/家庭用分離、表示内容(月のみ/教員のみ/職員会議)の個別選択
12. メモ(根拠等)の複数行モーダル化、教員用Excel出力への任意組み込み
13. 週番まとめて入力機能、Excel出力の複数月まとめ出力、PDF/印刷対応
14. 編集パスワードロック機能、テストデータ生成機能(1年分)、時数集計の学期別・年間計対応
15. 学年ごとの登校日チェックボックス方式への全面移行(旧「休業/登校 変更」列廃止)

それ以前(会話の中盤〜前半)には、入力グリッドの基本機能、月間/年間予定表プレビュー、Excel出力の基礎、●などの文字欠けバグ修正なども行っています。

## Claude Codeでの作業にあたって

- ファイルは1つのHTMLファイルにすべて収まっているため、`grep`/`sed`等で該当箇所を探しながらの編集が基本です。
- 変更後は必ず`node --check`でJS構文チェック、Python `html.parser`でHTML構文チェック、`getElementById`参照と`id`属性の整合性チェックを行う習慣がありました(このリポジトリには専用のlintスクリプトはなく、都度その場でワンライナーを書いています)。
- 見た目や新機能の検証は、上記のPlaywrightスクリプトをその都度書いて確認するスタイルで進めてきました。テストコードは使い捨てで、リポジトリには保存されていません。
