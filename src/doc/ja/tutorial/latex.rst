*********************************
Sage，LaTeXと仲間たち
*********************************

著者: Rob Beezer (2010-05-23)

SageとTeX派生の組版システムLaTeXは，緊密な相乗的協働関係にある．
ここでは，最も基本的なものから始めて，かなり特殊かつ難解なものに至るまでの両者の多様な相互関係を概観する．
(このチュートリアルを初めて読む場合は，この節は飛ばしてかまわない)


概観
========

SageはLaTeXを多種多様な形で利用している．
利用のメカニズムを理解する早道は，まず三種類の代表的な利用法のあらましを把握しておくことだろう．

#. Sageでは全オブジェクトに対してLaTeX形式の印字表現が可能になっている．
   Sageオブジェクト ``foo`` のLaTeX形式による印字表現を見たければ，SageノートブックまたはSageコマンド入力行で ``latex(foo)`` と実行する．
   出力された文字列をTeXの数式モード( 例えば一対の ``$`` で囲まれた領域 )内で処理すると十分に使える ``foo`` の表現が得られるはずだ．
   これについては以下で実例を見る．

   同じ手順によってSageをLaTeX文書の作成に活用することができる．
   目的のオブジェクトをSage上で作成あるいは計算しておいてから，そのオブジェクトを ``latex()`` 経由で表示した出力をLaTeX文書にカット/ペーストすればよい．

#. ノートブックインターフェイスは，webブラウザ上で数式を明瞭に表示するため `MathJax <http://www.mathjax.org>`_ を使用するよう設定されている．
   MathJaxはJavaScriptで書かれたオープンソースの数式表示エンジンで，現行のブラウザ全てで動作し，TeX形式で表現された数式の全てではないにしても,その大部分を表示することができる．
   その目的はTeXで表わされた短い数式を正確に表示することであって，複雑な表や文書構造の表現と管理を支援するものではない．
   ノートブックにおける見たところ自動的な数式のレンダリング表示は，数式オブジェクトの ``latex()`` 出力をMathJaxが動作可能なHTML形式に変換して得られたものである．
   MathJaxはそれ自身のスケーラブルフォントを利用しており，方程式などTeX形式で表現されたテキストを静的なインライン画像に変換する方法より優れているといえる．

#. Sageコマンドラインあるいはノートブック上でMathJaxでは処理しきれないほどLaTeXコードが複雑化してしまった場合は，システム上で公開運用されているLaTeXによって処理することもできる．
   Sageには，Sage自体をビルドして使用するために必要な物ほとんど全てが含まれているが，TeXはその例外となっている．
   場合によっては，TeXシステムおよび関連の変換ユーティリティ群を別途インストールしなければ欲しい機能が完全には使えないこともあるかもしれない．



ここで ``latex()`` 関数の基本的な用法を試してみよう.

::

    sage: var('z')
    z
    sage: latex(z^12)
    z^{12}
    sage: latex(integrate(z^4, z))
    \frac{1}{5} \, z^{5}
    sage: latex('a string')
    \text{\texttt{a{ }string}}
    sage: latex(QQ)
    \Bold{Q}
    sage: latex(matrix(QQ, 2, 3, [[2,4,6],[-1,-1,-1]]))
    \left(\begin{array}{rrr}
    2 & 4 & 6 \\
    -1 & -1 & -1
    \end{array}\right)

ノートブック上ではMathJaxの基本機能は自動的に動作するが，ここでは ``MathJax`` クラスを使ってMathJaxの働きを少しばかり観察してみることにしよう．
``MathJax`` クラスの ``eval`` 関数はSageオブジェクトをLaTeX形式に変換し，さらにCSSの "math"クラスを呼び出すHTMLにラップしてMathJaxを起動する．

::

    sage: from sage.misc.html import MathJax
    sage: mj = MathJax()
    sage: var('z')
    z
    sage: mj(z^12)
    <html>\[z^{12}\]</html>
    sage: mj(QQ)
    <html>\[\newcommand{\Bold}[1]{\mathbf{#1}}\Bold{Q}\]</html>
    sage: mj(ZZ[x])
    <html>\[\newcommand{\Bold}[1]{\mathbf{#1}}\Bold{Z}[x]\]</html>
    sage: mj(integrate(z^4, z))
    <html>\[\frac{1}{5} \, z^{5}\]</html>



基本的な使い方
===================

上で概観したように，SageのLaTeX支援機能を利用する最も基本的な方法は ``latex()`` 関数を使って数学オブジェクトのLaTeX形式による印字表現を生成することである．
生成されたLaTeX表現は別仕立てのLaTeX文書に貼り付けて利用すればよい．
この方法はノートブックでもSageコマンドラインでも同じように通用する．


もう一つの手軽な方法に ``view()`` コマンドの使用がある．
Sageコマンドラインで  ``view(foo)`` を実行すると ``foo`` のLaTeX表現が得られるから，これをLaTeX文書に取り込んで使っているシステムにインストールされたTeXで処理する．
TeXの出力を適切なビューワーで表示して出来上がりである．
使用するTeXのバージョンや，それに応じた出力とビューワーは選択することができる( :ref:`sec-custom-processing` 節を参照)．


ノートブック上では, ``view(foo)`` コマンドはMathJaxがワークシート上でLaTeX表現を正しくレンダリングできるよう適切なHTMLとCSSの組を生成する．
ユーザーの目に映るのはSageデフォルトのASCII文字出力ではなく，きれいにフォーマットされた数式出力ということになる．
ただしSageの数学オブジェクト全てがMathJaxで表示可能なLaTeX表現をもつとは限らない．
表示できない場合にはMathJaxではなくシステム上のTeXを使って処理し，出力をワークシートに表示可能な画像へ変換することができる．
これに必要な一連の処理をどうやってコントロールするかについては :ref:`sec-custom-generation` 節で解説する．


ノートブックにはTeXを利用するための機能があと二つある．
一つはワークシートの最初のセルのすぐ上に並ぶ四つのドロップダウンボックスの右にある "Typeset"ボタンである．
これをチェックしておくと，以降はセルの評価結果はMathJaxで処理されて印刷品質で表示される．
ただし，この機能は遡及的に作用するわけではなく，事前に評価済みのセルについては評価し直してやる必要がある．
つまるところ "Typeset"ボタンにチェックを入れるのは，以降のセル評価出力を ``view()`` コマンドでラップして表示することを意味することになる．


ノートブックのTeX支援機能の二つ目は，ワークシートの注釈にTeX形式が利用可能な点である．
ワークシート上でセルの間にカーソルが移動すると青色のバーが現れるから，そこで ``<shift-click>`` するとTinyMCEというちょっとしたワードプロセッサが起動する．
これを使えばテキスト入力を行ない，かつWSIWYGエディタ経由でHTMLとCSSを作成して書式付けすることができる．
つまりワークシート内で書式付きテキストを作成し注釈として付加することが可能なわけだ．
一方，ダラー記号(``$``)あるいはダラー記号2つ(``$$``)の間に挟まれた文字列はMathJaxによってインラインあるいはディスプレイ数式として解釈される．



.. _sec-custom-generation:


LaTeXコード生成のカスタマイズ
==================================

``latex()`` コマンドによるLaTeXコードの生成をカスタマイズする方法は何通りも用意されている．
ノートブックでもSageコマンドラインでも，すでに ``latex`` という名前のオブジェクトが定義済みで， ``latex.`` (ピリオド ``.`` に 注意)と入力して ``[Tab]`` キーを押せばメソッドの一覧を表示することができる．

.. ..
..    There are several ways to customize the actual LaTeX code generated by
..    the ``latex()`` command.  In the notebook and at the Sage command-line
..    there is a pre-defined object named ``latex`` which has several methods,
..    which you can list by typing ``latex.``, followed by the tab key
..    (note the period).

ここでは ``latex.matrix_delimiters`` メソッドに注目してみよう．
このメソッドを使えば，行列を囲む記号を大丸かっこ，角かっこ，中かっこ，縦棒などに変更することができる．
何か形式上の制限があるわけではないから，好きなように組み合わせてかまわない．
LaTeXで使われるバックスラッシュには，Pythonの文字列内でエスケープするためもう1個のバックスラッシュを付ける必要があることに注意．


::

    sage: A = matrix(ZZ, 2, 2, range(4))
    sage: latex(A)
    \left(\begin{array}{rr}
    0 & 1 \\
    2 & 3
    \end{array}\right)
    sage: latex.matrix_delimiters(left='[', right=']')
    sage: latex(A)
    \left[\begin{array}{rr}
    0 & 1 \\
    2 & 3
    \end{array}\right]
    sage: latex.matrix_delimiters(left='\\{', right='\\}')
    sage: latex(A)
    \left\{\begin{array}{rr}
    0 & 1 \\
    2 & 3
    \end{array}\right\}

``latex.vector_delimiters`` メソッドも同様の機能をもつ．


(整数，有理数，実数など)標準的な環や体をどんな書体で表示するかは ``latex.blackboard_bold`` メソッドによって制御することができる．
デフォルトではボールド体で表示されるが，手書きの場合にやるように黒板ボールド体(重ね打ち体)を使うこともできる．
それにはSageのビルトインマクロ ``\Bold{}`` を再定義してやればよい．


::

    sage: latex(QQ)
    \Bold{Q}
    sage: from sage.misc.html import MathJax
    sage: mj=MathJax()
    sage: mj(QQ)
    <html>\[\newcommand{\Bold}[1]{\mathbf{#1}}\Bold{Q}\]</html>
    sage: latex.blackboard_bold(True)
    sage: mj(QQ)
    <html>\[\newcommand{\Bold}[1]{\mathbb{#1}}\Bold{Q}\]</html>
    sage: latex.blackboard_bold(False)

新しいマクロやパッケージなどを追加して，TeXの高い拡張性を利用することができる．
まず，ノートブックでMathJaxが短いTeXコードを解釈する際に使われる，自分用のマクロを追加してみよう．


::

    sage: latex.extra_macros()
    ''
    sage: latex.add_macro("\\newcommand{\\foo}{bar}")
    sage: latex.extra_macros()
    '\\newcommand{\\foo}{bar}'
    sage: var('x y')
    (x, y)
    sage: latex(x+y)
    x + y
    sage: from sage.misc.html import MathJax
    sage: mj=MathJax()
    sage: mj(x+y)
    <html>\[\newcommand{\foo}{bar}x + y\]</html>


以上のようなやり方で追加したマクロは，MathJaxでは対応しきれない大規模な処理が発生してシステム上のTeXが呼ばれるような場合にも使われる．
自立したLaTeX文書のプリアンブルを定義する ``latex_extra_preamble`` コマンドの使い方は以下で具体例を示す．
これまで通りPython文字列中ではバックスラッシュが二重になっていることに注意．


::

    sage: latex.extra_macros('')
    sage: latex.extra_preamble('')
    sage: from sage.misc.latex import latex_extra_preamble
    sage: print(latex_extra_preamble())
    \newcommand{\ZZ}{\Bold{Z}}
    ...
    \newcommand{\Bold}[1]{\mathbf{#1}}
    sage: latex.add_macro("\\newcommand{\\foo}{bar}")
    sage: print(latex_extra_preamble())
    \newcommand{\ZZ}{\Bold{Z}}
    ...
    \newcommand{\Bold}[1]{\mathbf{#1}}
    \newcommand{\foo}{bar}


長く複雑なLaTeX表現を処理するために，LaTeXファイルのプリアンブルでパッケージ類を付加してやることができる．
``latex.add_to_preamble`` コマンドを使えば好きなものをプリアンブルに取り込めるし， ``latex.add_package_to_preamble_if_available`` コマンドはプリアンブルへの取り込みを実行する前に，指定したパッケージが利用可能かどうかをチェックしてくれる．


以下の例ではプリアンブルでgeometryパッケージを取り込み，ページ上でTeXに割り当てる領域(実質的にはマージン)サイズを指定している．
例によってPython文字列のバックスラッシュは二重になっていることに注意．


::

    sage: from sage.misc.latex import latex_extra_preamble
    sage: latex.extra_macros('')
    sage: latex.extra_preamble('')
    sage: latex.add_to_preamble('\\usepackage{geometry}')
    sage: latex.add_to_preamble('\\geometry{letterpaper,total={8in,10in}}')
    sage: latex.extra_preamble()
    '\\usepackage{geometry}\\geometry{letterpaper,total={8in,10in}}'
    sage: print(latex_extra_preamble())
    \usepackage{geometry}\geometry{letterpaper,total={8in,10in}}
    \newcommand{\ZZ}{\Bold{Z}}
    ...
    \newcommand{\Bold}[1]{\mathbf{#1}}

あるパッケージの存在確認をした上で取り込みを実行することもできる．
例として，ここでは存在しないはずのパッケージのプリアンブルへの取り込みを試みてみよう．


::

    sage: latex.extra_preamble('')
    sage: latex.extra_preamble()
    ''
    sage: latex.add_to_preamble('\\usepackage{foo-bar-unchecked}')
    sage: latex.extra_preamble()
    '\\usepackage{foo-bar-unchecked}'
    sage: latex.add_package_to_preamble_if_available('foo-bar-checked')
    sage: latex.extra_preamble()
    '\\usepackage{foo-bar-unchecked}'


.. _sec-custom-processing:


LaTeX処理のカスタマイズ
============================

システムで公開運用されているTeXシステムから好みの種類を指定して，出力形式を変更することも可能だ．
さらに，ノートブックがMathJax(簡易TeX表現用)とTeXシステム(複雑なLaTeX表現用)を使い分ける仕方を制御することができる．


``latex.engine()`` コマンドを使えば，複雑なLaTeX表現に遭遇した場合，システム上で運用されているTeXの実行形式  ``latex``, ``pdflatex`` または ``xelatex`` の内どれを使って処理するかを指定することができる．
``view()`` がsageコマンドラインから発行されると，実行形式 ``latex`` が選択されるから，出力はdviファイルとなりsageにおける表示にも(xdviのような)dviビューワーが使われる．
これに対し，TeX実行形式として ``pdflatex`` が設定された状態でsageコマンドラインから ``view()`` を呼ぶと，出力はPDFファイルとなってSageが表示に使用するのはシステム上のPDF表示ユーティリティ(acrobat，okular, evinceなど)となる．


ノートブックでは，簡単なTeX表現だからMathJaxで処理できるのか，あるいは複雑なLaTeX表現のためシステム上のTeXを援用すべきなのか，判断の手掛りを与えてやる必要がある．
手掛りとするのは文字列リストで，リストに含まれる文字列が処理対象のLaTeX表現に含まれていたらノートブックはMathJaxを飛ばしてlatex(もしくは ``latex.engine()`` コマンドで指定された実行形式)による処理を開始する．
このリストは ``latex.add_to_mathjax_avoid_list`` および ``latex.mathjax_avoid_list`` コマンドによって指定される．


::

    sage: latex.mathjax_avoid_list([])  # not tested
    sage: latex.mathjax_avoid_list()    # not tested
    []
    sage: latex.mathjax_avoid_list(['foo', 'bar'])  # not tested
    sage: latex.mathjax_avoid_list()                # not tested
    ['foo', 'bar']
    sage: latex.add_to_mathjax_avoid_list('tikzpicture')  # not tested
    sage: latex.mathjax_avoid_list()                      # not tested
    ['foo', 'bar', 'tikzpicture']
    sage: latex.mathjax_avoid_list([])  # not tested
    sage: latex.mathjax_avoid_list()    # not tested
    []


ノートブック上で  ``view()`` コマンド，あるいは "Typeset"ボタンがチェックされた状態でLaTeX表式が生成されたが， ``latex.mathjax_avoid_list`` によってシステム上のLaTeXが別途必要とされたとしよう．
すると，そのLaTeX表式は(``latex.engine()`` で設定した)指定のTeX実行形式によって処理される．
しかし，Sageは(コマンドライン上のように)外部ビューワーを起動して表示する代わりに，結果をセル出力としてきっちりトリミングした1個の画像に変換してからワークシートに挿入する．


変換が実際にどう実行されるかについては，いくつもの要因が影響している．
しかし大勢は，TeX実行形式として何が指定されているか，そして利用可能な変換ユーティリティは何かによって決まるようだ．
``dvips``, ``ps2pdf``, ``dvipng`` そして ``ImageMagick`` に含まれる ``convert`` の四種の優秀な変換ユーティリティがあれば，あらゆる状況に対処できるだろう．
目標はワークシートに挿入して表示可能なPNGファイルを生成することで，LaTeX表式からdvi形式へのLaTeXエンジンによる変換が成功していれば，dvipngが変換を仕上げてくれる．
LaTeX表式とLaTeXエンジンの生成するdvi形式にdvipngが扱えないspecial命令が入っている場合には，dvipsでポストスクリプトファイルへ変換する．
ポストスクリプトあるいは ``pdflatex`` エンジンによって出力されたPDFファイルは  ``convert`` ユーティリティによってPNG形式へ変換される．
ここで紹介した二つの変換ユーティリティは ``have_dvipng()`` と ``have_convert()`` ルーチンを使って存在を確認することができる．


必要な変換プログラムがインストールされていれば変換は自動的に行われる．
ない場合は，何が不足していて，どこからダウンロードすればよいかを告げるエラーメッセージが表示される．


どうすれば複雑なLaTeX表式を処理できるのか，その具体例として次節(:ref:`sec-tkz-graph`)ではLaTeXの ``tkz-graph`` パッケージを使って高品位の連結グラフを作成する方法を解説する．
他にも例が見たければ，パッケージ化済みのテストケースが用意されている．
利用するには， 以下で見るように ``sage.misc.latex.LatexExamples`` クラスのインスタンスである ``sage.misc.latex.latex_examples`` オブジェクトをインポートしなければならない．
現在，このクラスには可換図，組合せ論グラフと結び目理論，およびpstricks関連の例題が含まれており，各々がxy, tkz-graph, xypic, pstricksパッケージの使用例になっている．
インポートを終えたら， ``latex_examples`` をタブ補完してパッケージに含まれる実例を表示してみよう．
例題各々で適正な表示に必要なパッケージや手続きが解説されている．
(プリアンブルやlatexエンジン類が全て上手く設定されていても)実際に例題を表示するには ``view()`` を使わなくてはならない．



::

    sage: from sage.misc.latex import latex_examples
    sage: latex_examples.diagram()
    LaTeX example for testing display of a commutative diagram produced
    by xypic.
    <BLANKLINE>
    To use, try to view this object -- it will not work.  Now try
    'latex.add_to_preamble("\\usepackage[matrix,arrow,curve,cmtip]{xy}")',
    and try viewing again. You should get a picture (a part of the diagram arising
    from a filtered chain complex).


.. _sec-tkz-graph:


具体例：  tkz-graphによる連結グラフの作成
===============================================

``tkz-graph`` パッケージを使って高品位の連結グラフ(以降はたんに「グラフ」と呼ぶ)を作成することができる．
このパッケージは ``pgf`` ライブラリの ``tikz`` フロントエンド上に構築されている．
したがってその全構成要素がシステム運用中のTeXで利用可能でなければならないが，TeXシステムによっては必要なパッケージが全て最新になっているとは限らない．
満足すべき結果を得るには，必要なパッケージの最新版をユーザのtexmf配下にインストールする必要が生ずるかもしれない．
個人または公開利用のためのTeXのインストールや管理運用についてはこのチュートリアルの範囲を越えるが，必要な情報は簡単に見つかるはずだ．
必要なファイル類は :ref:`sec-system-wide-tex` 節に挙げられている．


まずは土台とするLaTeX文書のプリアンブルで必要なパッケージが付加されることを確認しておこう．
グラフ画像はdviファイルを経由すると正しく生成されないので，latexエンジンとしては ``pdflatex`` プログラムを指定するのが一番だ．
すると，Sageコマンドライン上で ``view(graphs.CompleteGraph(4))`` の実行が可能になり，完結したグラフ `K_4` の適切なPDF画像が生成されるはずだ．


ノートブックでも同様の動作を再現するには，グラフを表わすLaTeXコードのMathJaxによる処理を ``mathjax avoid list`` を使って抑止する必要がある．
グラフは ``tikzpicture`` 環境で取り込まれるから，文字列 ``tikzpicture`` を ``mathjax avoid list`` に入れておくとよい．
そうしてからワークシートで ``view(graphs.CompleteGraph(4))`` を実行するとpdflatexがPDFファイルを生成し，ついで ``convert`` ユーティリティが抽出したPNG画像がワークシートの出力セルに挿入されることになる．
ノートブック上でグラフをLaTeXにグラフを処理させるために必要なコマンド操作を以下に示す．
::

    sage: from sage.graphs.graph_latex import setup_latex_preamble
    sage: setup_latex_preamble()
    sage: latex.extra_preamble() # random - システムで運用されているTeXに依存
    '\\usepackage{tikz}\n\\usepackage{tkz-graph}\n\\usepackage{tkz-berge}\n'
    sage: latex.engine('pdflatex')
    sage: latex.add_to_mathjax_avoid_list('tikzpicture')  # not tested
    sage: latex.mathjax_avoid_list()                      # not tested
    ['tikz', 'tikzpicture']

ここまで設定してから ``view(graphs.CompleteGraph(4))`` のようなコマンドを実行すると， ``tkz-graph``  で表現されたグラフが  ``pdflatex`` で処理されてノートブックに挿入される．
LaTeXに ``tkz-graph`` 経由でグラフを描画させる際には多くのオプションが影響するが，詳細はこの節の範囲を越えている．
興味があればレファレンスマニュアル "LaTeX Options for Graphs"の解説を見てほしい．



.. _sec-system-wide-tex:

TeXシステムの完全な運用
================================

TeXをSageに統合して運用する際，高度な機能の多くはシステムに独立してインストールされたTeXがないと利用できない．
Linux系システムではTeXliveを基にした基本TeXパッケージを採用しているディストリビューションが多く，OSXではTeXshop，WindowsではMikTeXなどが使われている．
``convert`` ユーティリティは `ImageMagick <http://www.imagemagick.org/>`_ パッケージ(簡単にダウンロード可能)に含まれているし， ``dvipng``, ``ps2pdf`` と ``dvips`` の三つのプログラムはTeXパッケージに同梱されているはずだ．
また ``dvipng`` は http://sourceforge.net/projects/dvipng/ から， ``ps2pdf`` は `Ghostscript <http://www.ghostscript.com/>`_ の一部として入手することもできる．


連結グラフの作画には，PGFライブラリの新しいバージョンに加えて ``tkz-graph.sty`` を https://www.ctan.org/pkg/tkz-graph から入手する必要があり，
さらに ``tkz-arith.sty``　とおそらく ``tkz-berge.sty`` も https://www.ctan.org/pkg/tkz-berge から入手する必要がある.



外部プログラム
=================

TeXとSageのさらなる統合運用に役立つプログラムが三つある．
その一番目がsagetexで，このTeXマクロ集を使えばLaTeX文書からSage上の多様なオブジェクトに対する演算や組み込みコマンド ``latex()`` によるフォーマットなどを実行することができる．
LaTeX文書のコンパイル処理過程で，Sageの演算やLaTeXによるフォーマット支援などの全ての機能も自動的に実行されるのである．
sagetexを使えば，例えば数学試験作成において，問題の計算そのものをSageに実行させて対応する解答を正確に維持管理することなどが可能になる．
詳細は :ref:`sec-sagetex` 節を参照してほしい．
