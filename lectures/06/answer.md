# 第6回 解答

## 演習

- STEP 1 のツールの名前：`glossary_search_glossary`、`glossary_make_quiz`
- STEP 2 の [tool call]：`glossary_search_glossary({'query': '3レベル'})`
- STEP 3 の [tool call]：なし。Webページ取得用のMCPサーバーが未設定であった。
- STEP 4 の [tool call]：`glossary_search_glossary({'query': 'S3 バージョニング'})`。AWS検索用のMCPサーバーが未設定であり、用語集にも該当する情報がなかった。
- 気づき・疑問：外部情報の取得を依頼しても、対応するMCPサーバーが設定されていなければ、そのツールは利用できないことが分かった。また、モデルが回答することと、外部情報を実際に取得して回答することは区別する必要があると感じた。

## 宿題

### 取り組んだレベル

実装

### やったこと

- `app/tools.py` に第6回のクイズを返す `make_quiz` を新規実装した。
- `app/mcp_server.py` に登録し、MCPツールとして公開した。
- 未登録の講義回に対して、`ToolError` を用いてエラーの理由と利用可能な講義回を返すようにした。
- 正常系・異常系のテストを追加した。

### 結果

- ツール一覧に `glossary_make_quiz` が表示された。
- 正常系：`glossary_make_quiz({'lecture_no': 6})` が呼び出され、第6回のクイズが正常に返された。
- 異常系：`glossary_make_quiz({'lecture_no': 99})` が呼び出され、以下のエラーが返された。

`[tool error] glossary_make_quiz -> Error executing tool make_quiz: 第99回は登録されていません。利用可能な講義回は第6回です。登録済みの講義回を指定してください。`

- モデルも第99回が登録されていないことを説明した。
- `uv run pytest -q`：24件すべて成功した。

### 気づき・疑問

- MCPを利用することで、エージェント本体を変更せずにツールを追加できることが分かった。
- `ValueError` では詳細なエラー情報が失われたが、`ToolError` を使用することで、具体的なエラーの理由と修正方法をモデルに伝達できた。
- ツール側では利用可能な講義回を第6回と明示したが、モデルは「第6回など」と回答した。ツールの実行結果とモデルの最終回答の正確性は、分けて評価する必要があると感じた。