# 🌿 Konoha: Simple wrapper of Japanese Tokenizers

[![GitHub stars](https://img.shields.io/github/stars/himkt/konoha?style=social)](https://github.com/himkt/konoha/stargazers)

![Build Status](https://github.com/himkt/konoha/workflows/Python%20package/badge.svg?style=flat-square)
![Python](https://img.shields.io/badge/python-3.6%20%7C%203.7%20%7C%203.8-blue?logo=python)
[![GitHub Issues](https://img.shields.io/github/issues/himkt/konoha.svg?cacheSeconds=60&color=yellow)](https://github.com/himkt/konoha/issues)
[![GitHub Pull Requests](https://img.shields.io/github/issues-pr/himkt/konoha.svg?cacheSeconds=60&color=yellow)](https://github.com/himkt/konoha/issues)
[![GitHub Release](https://img.shields.io/github/release/himkt/konoha.svg?cacheSeconds=60&color=red)](https://github.com/himkt/konoha/releases)

`Konoha` is a Python library for providing easy-to-use integrated interface of various Japanese tokenziers,
which enables you to switch a tokenizer and boost your pre-processing.

## Supported tokenizers

<a href="https://github.com/buruzaemon/natto-py"><img src="https://img.shields.io/badge/MeCab-natto--py-ff69b4"></a>
<a href="https://github.com/chezou/Mykytea-python"><img src="https://img.shields.io/badge/KyTea-Mykytea--python-ff69b4"></a>
<a href="https://github.com/mocobeta/janome"><img src="https://img.shields.io/badge/Janome-janome-ff69b4"></a>
<a href="https://github.com/WorksApplications/SudachiPy"><img src="https://img.shields.io/badge/Sudachi-sudachipy-ff69b4"></a>
<a href="https://github.com/google/sentencepiece"><img src="https://img.shields.io/badge/Sentencepiece-sentencepiece-ff69b4"></a>

Also, `konoha` provides rule-based tokenizers (whitespace, character) and a rule-based sentence splitter.


## Quick Start with Docker

Simply run followings on your computer:

```bash
git clone https://github.com/himkt/konoha  # download konoha
cd konoha && docker-compose up --build  # build and launch contaier
```

Tokenization is done by posting a json object to `localhost:8000/api/tokenize`.
You can also batch tokenize by passing `texts: ["１つ目の入力", "２つ目の入力"]` to the server.

(API documentation is available on `localhost:8000/redoc`, you can check it using your web browser)

Send a request using `curl` on you terminal.

```json
$ curl localhost:8000/api/tokenize -X POST -H "Content-Type: application/json" \
    -d '{"tokenizer": "mecab", "text": "これはペンです"}'

{
  "tokens": [
    [
      {
        "surface": "これ",
        "part_of_speech": "名詞"
      },
      {
        "surface": "は",
        "part_of_speech": "助詞"
      },
      {
        "surface": "ペン",
        "part_of_speech": "名詞"
      },
      {
        "surface": "です",
        "part_of_speech": "助動詞"
      }
    ]
  ]
}
```


## Installation

I recommend you to install konoha by `pip install 'konoha[all]'` or `pip install 'konoha[all_with_integrations]'`.
(`all_with_integrations` will install `AllenNLP`)

- Install konoha with a specific tokenizer: `pip install 'konoha[(tokenizer_name)]`.
- Install konoha with a specific tokenizer and AllenNLP integration: `pip install 'konoha[(tokenizer_name),allennlp]`.
- Install konoha with a specific tokenzier and remote file support: `pip install 'konoha[(tokenizer_name),remote]'`


## Example

### Word level tokenization

```python
from konoha import WordTokenizer

sentence = '自然言語処理を勉強しています'

tokenizer = WordTokenizer('MeCab')
print(tokenizer.tokenize())
# => [自然, 言語, 処理, を, 勉強, し, て, い, ます]

tokenizer = WordTokenizer('Sentencepiece', model_path="data/model.spm")
print(tokenizer.tokenize(sentence))
# => [▁, 自然, 言語, 処理, を, 勉強, し, ています]
```

For more detail, please see the `example/` directory.

### Remote files

Konoha supports dictionary and model on cloud storage (currently supports Amazon S3).
It requires installing konoha with the `remote` option, see [Installation](#installation).

```python
# download user dictionary from S3
word_tokenizer = WordTokenizer("mecab", user_dictionary_path="s3://abc/xxx.dic")
print(word_tokenizer.tokenize(sentence))

# download system dictionary from S3
word_tokenizer = WordTokenizer("mecab", system_dictionary_path="s3://abc/yyy")
print(word_tokenizer.tokenize(sentence))

# download model file from S3
word_tokenizer = WordTokenizer("sentencepiece", model_path="s3://abc/zzz.model")
print(word_tokenizer.tokenize(sentence))
```

### Sentence level tokenization

```python
from konoha import SentenceTokenizer

sentence = "私は猫だ。名前なんてものはない。だが，「かわいい。それで十分だろう」。"

tokenizer = SentenceTokenizer()
print(tokenizer.tokenize(sentence))
# => ['私は猫だ。', '名前なんてものはない。', 'だが，「かわいい。それで十分だろう」。']
```


## Test

```
python -m pytest
```

## Article

- Introducing Konoha (in Japanese): [トークナイザをいい感じに切り替えるライブラリ konoha を作った](https://qiita.com/klis/items/bb9ffa4d9c886af0f531)
- Implementing AllenNLP integration (in Japanese): [日本語解析ツール Konoha に AllenNLP 連携機能を実装した](https://qiita.com/klis/items/f1d29cb431d1bf879898)

## Acknowledgement

Sentencepiece model used in test is provided by @yoheikikuta. Thanks!
