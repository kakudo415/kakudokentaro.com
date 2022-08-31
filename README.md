# kakudokentaro.com

## URL候補

### ドメイン

- kakudo415.com
- kakudo.dev
- kakudokentaro.com

### パス名

- /
- /about
- /contact
- /blog
- /blog/2022
- /blog/2022/08
- /blog/2022/08/28
- /blog/2022/08/28/short-title

### 先行事例

- https://www.w3.org/blog/2022/08/https-redirection-observations/

W3Cがこのスタイルだからこれで良いじゃん！

## CMS

アクセスごとに、
1. 対応するHTMLファイルがあるか確かめる。
   1. あった場合、原稿更新日時とHTMLファイル生成日時を比較する。
      1. 原稿更新日時以降にHTMLファイルが生成されていれば、そのHTMLファイルを返す。
      2. HTML生成後に再度原稿が更新されていれば、生成し直して返す。
   2. なかった場合は、対応する原稿があるか確かめる。
      1. あった場合は、HTMLファイルを生成して返す。
      2. なかった場合は、404。
