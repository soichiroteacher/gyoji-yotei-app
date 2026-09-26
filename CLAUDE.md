# 行事予定管理アプリ(school-events)

- 全アプリ共通のルールは `../CLAUDE.md`。必ず従うこと。
- 作業を始める前に [HANDOFF.md](HANDOFF.md) を読むこと(データの形・環境の注意・次にやること)。細かな経緯は TODO.md。
- 変更したら、`APP_VERSION` の更新、アプリ内の「更新履歴」、README.md(先生向け)・CHANGELOG.md・HANDOFF.md を同時に更新する(詳しくは HANDOFF.md)。
- 新しいデータ項目を足したら `normalizeState` にも追記する(古いデータファイルを読めるように)。
- 実在の学校名や本番の行事予定などの実データは、このフォルダに置かない(GitHub と Google ドライブに送られるため)。
