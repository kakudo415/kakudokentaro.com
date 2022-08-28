# kakudokentaro.com

## URL候補

### ドメイン

- kakudo415.com
- kakudo.dev
- kakudokentaro.com

### パス名

- /docs/2022/08/28/short-title
- /articles/2022/08/28/short-title
- /blog/2022/08/28/short-title
- /log/2022/08/28/short-title
- /entry/2022/08/28/short-title
- /2022/08/28/short-title
- /2022/08/28/001

### 先行研究

- https://developers.googleblog.com/2022/08/gdsc-lead-in-islamabad-develops-proof-of-concept-android-application-to-prevent-counterfeiting.html
- https://aws.amazon.com/jp/blogs/startup/techblog-iam-sso-practice/
- https://aws.amazon.com/jp/blogs/aws/announcing-the-latest-aws-heroes-august-2022/
- https://www.allthingsdistributed.com/2022/03/curious-about-automated-reasoning.html
- https://aws.amazon.com/jp/blogs/architecture/a-multi-dimensional-approach-helps-you-proactively-prepare-for-failures-part-2-infrastructure-layer/
- https://techblog.yahoo.co.jp/entry/2022082530334621/
- https://engineering.fb.com/2022/07/28/security/five-security-principles-for-billions-of-messages-across-metas-apps/
- https://engineering.linecorp.com/ja/blog/sigmod2022/
- https://techblog.zozo.com/entry/compositional-layouts-apply-shadow
- https://engineering.mercari.com/blog/entry/20220121-securing-terraform-monorepo-ci/

## CMS

アクセスごとに、
1. 対応するHTMLファイルがあるか確かめる。
   1. あった場合、原稿更新日時とHTMLファイル生成日時を比較する。
      1. 原稿更新日時以降にHTMLファイルが生成されていれば、そのHTMLファイルを返す。
      2. HTML生成後に再度原稿が更新されていれば、生成し直して返す。
   2. なかった場合は、対応する原稿があるか確かめる。
      1. あった場合は、HTMLファイルを生成して返す。
      2. なかった場合は、404。
