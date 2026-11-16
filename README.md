# tell_vocabulary

英会話学習アプリ Tell が使う語彙データと、それを作るための元データです。アプリはこのデータを同梱せず、初回起動のときに、このリポジトリのタグを付けた版からダウンロードします。ダウンロードしたファイルは、アプリに書いてある SHA-256 と照らし合わせます。`wordnet/`・`numberbatch/`・`conceptnet/` は、Tell の `tools/Tell.DataBuilder` で `generated/` を作り直すときだけ使う元データで、アプリはダウンロードしません。

| ディレクトリ | 内容 | ライセンス |
| --- | --- | --- |
| `generated/` | Tell の `tools/Tell.DataBuilder` で生成したデータ | CC BY-SA 4.0 |
| `vocabulary/` | 手で管理しているデータ（許可リストなど） | CC BY-SA 4.0 |
| `ngsl/` | NGSL 1.2 の公式リストの写し | CC BY-SA 4.0 |
| `cefrj/` | CEFR-J Wordlist 1.5 の写し | 独自の条件（下記） |
| `wordnet/` | Open English WordNet 2025 の写し（DataBuilder の元データ） | CC BY 4.0 |
| `numberbatch/` | ConceptNet Numberbatch 19.08 から、NGSL の見出し語の行だけを取り出したもの（DataBuilder の元データ） | CC BY-SA 4.0 |

| `conceptnet/` | ConceptNet 5.7 assertions の人が書いた関係を NGSL の見出し語ごとに数えたもの（DataBuilder の元データ） | CC BY-SA 4.0 |

## なぜ配布元のデータの写しを置いているか

アプリは、ダウンロードしたファイルが、データを作ったときのものと同じかを SHA-256 で確かめます。NGSL と CEFR-J を配布元から直接ダウンロードすると、配布元でファイルが差し替えられたとき（版の更新や、表記の手直しなど）にハッシュが合わなくなります。そうなると、アプリを更新するまで、新しくインストールした人は初回起動のダウンロードを終えられません。また、差し替えられた内容は、このリポジトリの生成データ（`generated/`）を作ったときの元データと食い違います。

そのため、データを作ったときに使ったファイルを、そのままここに写して置いています。中身は一切変えていません。

DataBuilder の元データ（WordNet・Numberbatch・ConceptNet）も、同じく、配布元で差し替えられたり、配布が止まったりしても、`generated/` を同じ元データから作り直せるように置いています（ConceptNet は更新が止まっているプロジェクトです）。Numberbatch の元のファイル（英語のみ、約325MB）は、GitHub に置ける大きさを超えるため置かず、NGSL の見出し語の行だけを取り出したものを置いています。今の `generated/` は、これだけで作り直せます。元のファイルが要るのは、NGSL の見出し語の一覧そのものを変えたときだけです。

CEFR-J Wordlist の利用条件には、再配布してよいかが書かれていません。ここに置いているのは、上の理由でファイルの差し替えに備えるためだけです。権利者から求められた場合は、すぐに取り下げます。

## generated/

| ファイル | 内容 |
| --- | --- |
| `excluded_nouns.csv` | NGSL 外の動詞・形容詞・副詞と関連しているため、範囲外とする名詞（約10,000語） |
| `semantic_vectors.csv` | NGSL の見出し語の意味のベクトル（Numberbatch の300次元から全語の平均を引き、長さを1にして8ビットに縮めたもの）。意味の近さをコサイン類似度で測る |
| `topic_words.csv` | 最初の一言・新しい話題の材料にする具体的な名詞（WordNet の最初の名詞の意味の分類で選ぶ。形容詞・副詞にもなる語は除く。v4 は CEFR-J に名詞の項目があり、ConceptNet の人が書いた UsedFor の始点・AtLocation の始点と終点の合計が1以上の語に絞り、`vocabulary/topic_excluded_words.txt` を生成時に除く。267語。WordNet は noun.artifact・food・animal・plant・location・object・substance・phenomenon・event の9分類） |
| `word_ranks.csv` | NGSL の本体の見出し語の頻度の順位（SFI の高い順）。順位が大きいほど難しいとみなす（約2,800語） |

| `word_glosses.csv` | NGSL と CEFR-J A1〜B1 の4,697語の WordNet の定義11,752個。1語につき3義まで、各義の最初の例文を1つ（あれば）。品詞内は WordNet の頻度順。品詞をまたぐ頻度の記録はないため、各品詞の第1義を先にし、同順位は名詞・動詞・形容詞・副詞の順 |
| `word_synonyms.csv` | WordNet の同じ synset に属する NGSL の語の組。返事で使ってもよい語を選ぶとき、単なる類義語を避けるために使う |

## vocabulary/

| ファイル | 内容 |
| --- | --- |
| `allowed_nouns.txt` | 除外ルールに当てはまるが、易しいので許可する名詞（名詞と判定されたときだけ許可） |
| `allowed_words.txt` | チェッカーの誤判定を直すため、品詞にかかわらず許可する語。会話で見つけたときに追加する |
| `topic_excluded_words.txt` | 規則で外れず、実際に候補に出て困った語を DataBuilder が除く一覧（主に動詞、範囲・枠組み・抽象、機能語や別の語と紛れる語、地面・素材など）。アプリは読まない |
| `unsuitable_words.txt` | 話題の候補と、返事で使ってよい語の両方から外す語（暴力・性・差別・薬物・たばこ・病気・お酒など）。`conversation_excluded_words.txt` と合わせて使う。語彙チェックには使わない |

| `conversation_excluded_words.txt` | 話題の材料と、返事で使ってもよい語の両方から除く語（user・fellow・scene など、人の呼び方や意味のぼやけた語）。語彙チェックには使わない |

お酒の語も除くことにした（2026-11-17、ユーザーの判断）。`allowed_words.txt` の sightseeing・temple は、CEFR-J A2・A1 で許可できることを実際のチェッカーで確認して外した。English は形容詞として使うので残す（同日、ユーザーの判断）。

## ngsl/

| ファイル | 内容 |
| --- | --- |
| `NGSL_12_lemmatized_for_research.csv` | 研究用。考えられる活用形をすべて含む |
| `NGSL_12_lemmatized_for_teaching.csv` | 教育用。よく使われる活用形のみ |
| `SUP_lemmatized.csv` | 補足リスト（曜日・月・数詞など52語） |
| `NGSLwithSFI-31K.xlsx` | 約31,000語の頻度順位。`generated/word_ranks.csv` の元データ。アプリはダウンロードしない |

取得元は https://www.newgeneralservicelist.com/ 、取得日は 2026-11-08。

## cefrj/

| ファイル | 内容 |
| --- | --- |
| `cefrj-vocabulary-profile-1.5.csv` | 見出し語・品詞・CEFR の段階（A1〜B2）など。Tell は A1〜B1 の語を、NGSL の範囲外でも AI の返答で使ってよい語にする |

取得元は https://github.com/openlanguageprofiles/olp-en-cefrj （元の配布は http://www.cefr-j.org/download.html ）、取得日は 2026-11-14。

## wordnet/

| ファイル | 内容 |
| --- | --- |
| `english-wordnet-2025.xml.gz` | Open English WordNet 2025（約11MB）。`generated/excluded_nouns.csv`・`topic_words.csv`・`word_glosses.csv`・`word_synonyms.csv` の元データ |

取得元は https://github.com/globalwordnet/english-wordnet/releases/tag/2025-edition 、取得日は 2026-11-14。

## numberbatch/

| ファイル | 内容 |
| --- | --- |
| `numberbatch-ngsl.txt.gz` | ConceptNet Numberbatch 19.08（英語のみ）から、NGSL の見出し語の行だけを取り出したもの（約1.8MB）。`generated/semantic_vectors.csv` の元データ。Tell の DataBuilder が作る |

元のファイルは https://conceptnet.s3.amazonaws.com/downloads/2019/numberbatch/numberbatch-en-19.08.txt.gz （2026-11-13 に取得）。

## conceptnet/

| ファイル | 内容 |
| --- | --- |
| `conceptnet-ngsl.csv` | NGSL の全2,859見出し語について、UsedFor の始点・AtLocation の始点・AtLocation の終点の3つの数（0も記録）。dataset が `/d/conceptnet/` または `/d/verbosity` で始まる辺だけを数える。語は英語 URI の `/c/en/` の次の要素で照合。`generated/topic_words.csv` の元データ。アプリはダウンロードしない |

元データは ConceptNet 5.7 assertions（https://conceptnet.s3.amazonaws.com/downloads/2019/edges/conceptnet-assertions-5.7.0.csv.gz ）、取得日は 2026-11-16。ライセンスは CC BY-SA 4.0。元の約498MB のファイルは置かず、Tell の DataBuilder で関係の数だけを書き出す。

## 出典・ライセンス

| データ | ライセンス | 出典 |
| --- | --- | --- |
| NGSL 1.2 | CC BY-SA 4.0 | Browne, C., Culligan, B., & Phillips, J. *The New General Service List* (version 1.2). https://www.newgeneralservicelist.com/ |
| Open English WordNet 2025 | CC BY 4.0 | McCrae, J. P., et al. https://github.com/globalwordnet/english-wordnet （`wordnet/` に写しを置いている） |
| ConceptNet Numberbatch 19.08 | CC BY-SA 4.0 | Luminoso Technologies, Inc. https://github.com/commonsense/conceptnet-numberbatch 。Speer, R., Chin, J., & Havasi, C. (2017). ConceptNet 5.5: An Open Multilingual Graph of General Knowledge. AAAI 2017（NGSL の見出し語の行だけを `numberbatch/` に置いている） |
| ConceptNet 5.7 | CC BY-SA 4.0 | Speer, R., Chin, J., & Havasi, C. (2017). ConceptNet 5.5: An Open Multilingual Graph of General Knowledge. AAAI 2017。https://conceptnet.io/ （人が書いた関係の数だけを `conceptnet/` に置いている） |
| `generated/` | CC BY-SA 4.0 | NGSL・Open English WordNet・Numberbatch・CEFR-J・ConceptNet から生成。NGSL を加工したデータのため、NGSL と同じライセンスになる |
| `vocabulary/` | CC BY-SA 4.0 | Tell の開発者が作成 |
| CEFR-J Wordlist 1.5 | 独自の条件（研究・商用とも無料、出典の表示が必要。CC BY-SA ではない） | The CEFR-J Wordlist Version 1.5. Compiled by Yukio Tono, Tokyo University of Foreign Studies. 著作権は東京外国語大学 投野研究室 |

> This data contains semantic vectors from ConceptNet Numberbatch, by Luminoso Technologies, Inc. You may redistribute or modify the data under the terms of the CC-By-SA 4.0 license.

CC BY-SA 4.0：https://creativecommons.org/licenses/by-sa/4.0/

## 更新のしかた

1. `vocabulary/` は、このリポジトリで直接直してコミットする。
2. `generated/` は、Tell のリポジトリで `dotnet run --project tools/Tell.DataBuilder` を実行して作り直す（出力先はこのリポジトリの `generated/`）。Numberbatch の元のファイルを Tell の `tools/fetch-numberbatch.sh` で取ってあれば、`numberbatch/` も作り直す。ConceptNet の元のファイル（`tools/fetch-conceptnet.sh`）があれば `conceptnet/` も作り直す。
3. 新しい版を出すときは、タグ（`v1`、`v2` …）を打ってプッシュし、Tell の側のファイルの一覧（タグと SHA-256）を更新する。打ったタグの中身は変えない（アプリがそのタグの SHA-256 を持っているため）。
