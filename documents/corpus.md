corpus
======

**corpus**は文書を文書変数と共に格納するための**quanteda**独自のオブジェクトであり、**corpus**に含まれる文書は原型を保つ。 文書の文節への変換はこの段階で行うが必要がある。**corpus**の作成・操作を行う関数は`corpus_*`と命名されている。

corpusの作成
------------

**corpus**を作成するためのテキストの読み込みは、Rの基本的な機能を利用する。Rに読み込むテキストの文字コードは常にUTF-8となるように注意する。

``` r
require(quanteda) # パッケージの読み込み
```

``` r
# テキストのみの場合
txt <- readLines("data/asahi_head.txt")
corp <- corpus(txt)
head(corp)
```

    ##                                                                                  text1 
    ##                               "解散時期、政権見極め　支持率・株価も考慮か　同日選視野" 
    ##                                                                                  text2 
    ##                                   "大統領府が談話、世論沈静化図る　日韓合意受け２度目" 
    ##                                                                                  text3 
    ## "（新発想で挑む　地方の現場から：１）常識を打ち破ろう　水田にトウモロコシ、農の救世主" 
    ##                                                                                  text4 
    ##         "「中国はいずれＴＰＰに参加」　「『Ｇゼロ』後の世界」著者、イアン・ブレマー氏"

``` r
# テキストが文書変数を伴う場合
data <- read.csv('data/asahi.csv', sep = "\t", stringsAsFactors = FALSE)
corp <- corpus(data, text_field = 'head')
head(corp)
```

    ##                                                                             text592027 
    ##                               "解散時期、政権見極め　支持率・株価も考慮か　同日選視野" 
    ##                                                                             text592028 
    ##                                   "大統領府が談話、世論沈静化図る　日韓合意受け２度目" 
    ##                                                                             text592029 
    ## "（新発想で挑む　地方の現場から：１）常識を打ち破ろう　水田にトウモロコシ、農の救世主" 
    ##                                                                             text592030 
    ##         "「中国はいずれＴＰＰに参加」　「『Ｇゼロ』後の世界」著者、イアン・ブレマー氏"

``` r
head(docvars(corp))
```

    ##                  date edition section page length
    ## text592027 2016-01-01    朝刊  ３総合    3   1288
    ## text592028 2016-01-01    朝刊  ３総合    3    595
    ## text592029 2016-01-01    朝刊  １経済    4   3214
    ## text592030 2016-01-01    朝刊  １経済    4    983
    ## text592031 2016-01-01    朝刊  １外報    7    375
    ## text592032 2016-01-01    朝刊  １外報    7    497
    ##                                        hash year month
    ## text592027 8b94af77cf10b662e4728e89257d252b 2016     1
    ## text592028 2c974c3cdb7a2e995fda5316d1bf6961 2016     1
    ## text592029 845f04b7f5bf7b8a3641a959bcbb93ba 2016     1
    ## text592030 ffbb56d8ce52e4faa11e2b4cc2eec56f 2016     1
    ## text592031 e3b5c2a6947176d8c9645db599ab8daa 2016     1
    ## text592032 5412a90ca34a10016410bea6a15c0d41 2016     1

### corpusの保存

``` r
save(corp, file = 'data/data_corpus_asahi_head.RData')
```

### テキストが複数のファイルに分かれている場合

[**readtext**](https://github.com/kbenoit/readtext)パッケージを利用すると簡単にフォルダやファイルの名前から文書変数を生成できる。

corpusの操作
------------

``` r
load('data/data_corpus_asahi_2016.RData') # Rオブジェクトの読み込み
ndoc(data_corpus_asahi_2016) # 文書の数
```

    ## [1] 16401

``` r
texts(data_corpus_asahi_2016)[[1]] # 最初の文書
```

    ## [1] "　今の衆院議員の任期が残り３年近くある中、安倍晋三首相が今夏の衆参同日選も選択肢に入れるのは、衆院解散のタイミングが憲法改正を含む今夏以降の政権運営に大きく影響するからだ。首相が実際に同日選に踏み切るかは、年初から続く三つの選挙が一つの判断材料になりそうだ。▼１面参照　首相は今のところ、２０１７年４月に消費税率を１０％に引き上げる方針だ。与党内では「消費増税後、しばらく景気が冷え込む可能性がある。解散のタイミングが難しくなる」との見方が多い。このため、自民内には、増税準備が本格化する前の１６年中の解散を求める声がある。　与党内で同日選論が取りざたされ始めたきっかけは昨年１１月下旬以降の軽減税率をめぐる自民、公明両党の協議だ。首相や菅義偉官房長官が協議を主導し、軽減税率の適用範囲を１兆円規模に拡大。公明党の意向を丸のみした。公明は「衆参の比例区、選挙区の四つの投票先を支持者に覚えてもらうのは難しい」（党幹部）と同日選に否定的だが、首相周辺は「次は向こうが譲る番」。首相が同日選の選択肢を温存するためにも公明に譲歩したと説明する。　首相や菅氏が気脈を通じる橋下徹前大阪市長の存在もある。政府高官は「橋下さんが政界復帰するなら次の衆院選だ」と明言。影響力が衰えないうちに、橋下氏が同日選で「おおさか維新の会」から立候補すれば参院の獲得議席も増え、自公とおおさか維新で改憲に手が届くという筋書きだ。　野党が統一候補を模索する中、選挙区事情が複雑になる同日選なら、野党の足並みを乱すことができるとの思惑もある。自民党の重鎮議員は「選挙に大義はいらない。『勝てる』ことが大義だ」と語る。　行方を占うのが、米軍普天間飛行場（沖縄県宜野湾市）の移設計画を進める安倍政権と、これに反対する翁長雄志知事側が激突する１月の宜野湾市長選、６月の沖縄県議選、加えて自公候補と野党統一候補が戦う４月の衆院北海道５区補選だ。自民の幹事長経験者は「この勝敗や情勢が同日選の指標になる」と語る。　「補選はしっかりやらないといけない」。昨年１２月２３日夜、首相は東京・赤坂の中華料理店で、麻生太郎財務相、甘利明経済財政相、菅官房長官とこんな言葉を交わした。沖縄の選挙でも官邸は「絶対に勝たねばならない」（幹部）と鼻息が荒い。　ただ、同日選に踏み切るには一定の内閣支持率を維持していることが必須だ。世界経済の変調による株価下落など不安定要素も多い。また、野党統一候補への有権者の期待が高まれば、同日選はリスクになる。首相はこうした要素を加味しながら、最終的に判断するとみられる。　■今後の主な政治日程　＜２０１６年＞　１月　通常国会召集　〃　　沖縄県宜野湾市長選　３月　日米韓首脳会談？　〃　　安全保障関連法施行　４月　衆院北海道５区補選投開票　５月　伊勢志摩サミット（２６、２７日）　６月　通常国会会期末（１日）　〃　　沖縄県議の任期満了　７月　参院選投開票　＜１７年＞　４月　消費税率１０％への引き上げ（予定）　＜１８年＞　９月　安倍総裁２期６年の任期満了、自民党総裁選？１２月　衆院議員の任期満了"

``` r
texts(data_corpus_asahi_2016)[[2]] # 二番目の文書
```

    ## [1] "　慰安婦問題を巡る日韓合意を受け、韓国大統領府は３１日、２度目の対国民メッセージを発表した。ソウルの在韓国日本大使館そばにある少女像の移転問題などで、韓国世論が硬化しつつある事態を憂慮した。　メッセージは、「様々な批判も聞いている」と受け止める一方、「政府が最善を尽くした結果を無効だと言うなら、どんな政府も厄介な問題は扱わなくなる」と訴えた。合意を巡る報道について「事実ではない報道で、感情を刺激することは、問題解決の助けに全くならない」とした。　韓国の調査会社リアルメーターによれば、世論調査で日韓合意への支持は４３・２％、不支持が５０・７％だった。合意支持は、与党支持層では７８・１％だったが、野党支持層ではわずか８・５％だった。年齢別では５０代以上の過半数が合意を支持したが、２０代では３１・０％、３０代では２３・８％しか支持しなかった。　また１２月２８日から３０日にかけての朴槿恵（パククネ）大統領の支持率は４２・１％。前週に比べて０・４ポイント下がった。　一方、韓国外交省はフェイスブックで日韓合意について説明。「最終的かつ不可逆的な解決」について「日本側も合意に反する行為は許されない、という意味が明確に含まれる」とした。歴史認識問題で韓国を刺激する発言や行動を自制するよう要請。少女像に関しては、移転を事前に約束したとの「合意はない」と明確に否定した。　（ソウル＝牧野愛博）"

### 文書の選択

``` r
table(docvars(corp, 'month'))
```

    ## 
    ##    1    2    3    4    5    6    7    8    9   10   11   12 
    ## 1252 1241 1326 1287 1454 2035 1633  983 1122 1227 1368 1473

``` r
corp_may <- corpus_subset(data_corpus_asahi_2016, month == 5) # 5月の記事だけを選択
ndoc(corp_may)
```

    ## [1] 1454

``` r
corp_morning <- corpus_subset(data_corpus_asahi_2016, edition == '朝刊') # 朝刊だけを選択
ndoc(corp_morning)
```

    ## [1] 14461

``` r
table(weekdays(docvars(corp_morning, 'date')))
```

    ## 
    ##    Friday    Monday  Saturday    Sunday  Thursday   Tuesday Wednesday 
    ##      2133      1310      2413      1765      2412      2040      2388

### 文書の分割

``` r
corp_sent <- corpus_segment(data_corpus_asahi_2016, what = "other", delimiter = "。") # 記事を句点で分割
ndoc(corp_sent)
```

    ## [1] 392792

``` r
head(texts(corp_sent))
```

    ##                                                                                                                                                                      text1.1 
    ## "今の衆院議員の任期が残り３年近くある中、安倍晋三首相が今夏の衆参同日選も選択肢に入れるのは、衆院解散のタイミングが憲法改正を含む今夏以降の政権運営に大きく影響するからだ。" 
    ##                                                                                                                                                                      text1.2 
    ##                                                                                     "首相が実際に同日選に踏み切るかは、年初から続く三つの選挙が一つの判断材料になりそうだ。" 
    ##                                                                                                                                                                      text1.3 
    ##                                                                                          "▼１面参照　首相は今のところ、２０１７年４月に消費税率を１０％に引き上げる方針だ。" 
    ##                                                                                                                                                                      text1.4 
    ##                                                                                                               "与党内では「消費増税後、しばらく景気が冷え込む可能性がある。" 
    ##                                                                                                                                                                      text1.5 
    ##                                                                                                                             "解散のタイミングが難しくなる」との見方が多い。" 
    ##                                                                                                                                                                      text1.6 
    ##                                                                                             "このため、自民内には、増税準備が本格化する前の１６年中の解散を求める声がある。"