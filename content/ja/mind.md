---
title: "思考 — BFE001"
description: "僕の頭の中の仕組み——ハイポファンタジア、ワイヤーフレーム思考、空間記憶。"
---

<!-- section: $040000 "僕の頭の仕組み" -->

自分の記憶にはほとんど映像がありません。いくつかはありますが、覚えていることの
大半は感覚か、言葉では簡単に説明できない情報です。
*「昨日のあの金髪の女の子覚えてる？」* と言われても誰のことかわからない。
10年前の元社員の話をしていて、会話の途中でその人が黒人だったことに気づいたことも
ありますが、名前を教えてもらうまで誰のことかわかりませんでした。僕の脳は
見た目を検索キーとして保存していないのです。

これには名前があります: **ハイポファンタジア**——視覚的イメージが弱い認知スタイル。自分がスペクトルのどこに位置するか、<a href="vviq2.html" class="deep-link">VVIQ2テスト</a>で測定できます。
赤いリンゴを想像してと言われれば、上のヘタや底のくぼみはなんとなく浮かぶ。
でもリンゴ全体は？せいぜい赤い丸です。有機的な形はシンプルな幾何学に
崩れてしまう。技術的なもの——コンピュータ、家、錠前の機構——は頭の中で
自由に回転できますが、ワイヤーフレームとして現れます。テクスチャのない構造。

<div class="pullquote">
僕の脳は世界を構造、関係性、空間マップとして表現します——画像としてではなく。
壁、床、部屋：これまで住んだすべての家をメンタルで浮遊できますが、
ワイヤーフレームモードで。色のない幾何学。
</div>

それでも空間認識は強いです。これまで住んだすべての家やアパートを頭の中で
浮遊できます——異なるフロアの部屋同士の位置関係、窓、スイッチ、家具の場所。
Half-Lifeに付属していたHammerのような3Dレベルエディタを使ったことがあれば
想像してください。ブラシジオメトリ、エンティティ、関係性。僕の内部表現は
だいたいそんな感じです。

複雑なシステムは、空間ダイアグラムとロジックツリーの中間のようなものとして
頭の中に現れます。ノードと接続、依存関係と制約——でもそのグラフは3D空間に
押し込めません。関係が交差したり重なったりして、物理的なレイアウトでは
表現できないからです。ソフトウェアアーキテクチャやインフラを考えるとき、
その構造の中をナビゲートしています。

<div class="mem-block">
<span class="label">COGNITIVE_PROFILE</span> <span class="comment">; まとめ</span>
  <span class="dim">├──</span> <span class="keyword">spatial_structure</span>    <span class="val">■■■■■■■■■░</span>  <span class="comment">; 強い</span>
  <span class="dim">├──</span> <span class="keyword">system_modeling</span>     <span class="val">■■■■■■■■■░</span>  <span class="comment">; 強い</span>
  <span class="dim">├──</span> <span class="keyword">mechanistic_reason</span>  <span class="val">■■■■■■■■■■</span>  <span class="comment">; とても強い</span>
  <span class="dim">├──</span> <span class="keyword">recognition_memory</span>  <span class="val">■■■■■■■■░░</span>  <span class="comment">; 強い</span>
  <span class="dim">├──</span> <span class="keyword">visual_imagery</span>      <span class="val">■■░░░░░░░░</span>  <span class="comment">; 弱い</span>
  <span class="dim">├──</span> <span class="keyword">face_recall</span>         <span class="val">■░░░░░░░░░</span>  <span class="comment">; とても弱い</span>
  <span class="dim">└──</span> <span class="keyword">timeline_ordering</span>  <span class="val">■■░░░░░░░░</span>  <span class="comment">; 弱い</span>
</div>

<!-- section: $080000 "スナップショットとしての記憶" -->

僕の記憶は検査できる凍結された瞬間——実行中のプログラムのブレークポイントの
ようなものです。動画の再生ではありません。一つ一つが空間マップ上のある場所に
ピン留めされた1フレームで、メタデータが付いています：誰がいたか、何が起きたか、
その時の感情。でも連続したタイムラインはなく、前も後もありません。

<div class="snapshot">
    <p>
        ディジョンでの7歳の誕生日、41 Rue Neuve Bergère。祖母が所有する駐車場、
        奥には小さな木立と緑地の向こうにイベントルーム。学校の子どもたちが
        テーブルを囲んでいる。笑いすぎてジュースが鼻から出た。そのテーブルの
        正確な位置を指し示せる。でも顔はなく、映像もなく、あの子どもたちが
        誰だったかの記憶もない。その瞬間の前も後も何もない。
    </p>
    <div class="snapshot-meta">LOC: 41 Rue Neuve Bergère, Dijon · AGE: 7 · ANCHOR: 空間 + 感情スパイク</div>
</div>

時間そのものが自分にとって抽象的です。空間はマッピングできても、空間の中の
変化はマッピングできない。マップは更新できますが、以前のバージョンは大抵
失われます。誕生日や既知のイベントに紐づいていて時間に固定された記憶は
いくつかありますが、基本的に記憶をタイムライン上に配置できません。
何か特別なことがない限り、昨日や先週何をしたかを写真を見ずに思い出すのは
ほぼ不可能です。

今は時間的なインデックスをテクノロジーに外部委託しています——主にGoogle Photos
で、物や場所をすばやく検索してタイムスタンプを取得できます。本質的には
外部記憶システムです：脳が空間マップと構造的知識を保持し、スマホが
*いつ* を提供してくれる。

<div class="mem-block">
<span class="label">MEMORY_MODEL</span>
  <span class="dim">├──</span> <span class="keyword">index</span>: <span class="val">空間的（場所細胞）</span>
  <span class="dim">├──</span> <span class="keyword">format</span>: <span class="val">凍結スナップショット + メタデータ</span>
  <span class="dim">├──</span> <span class="keyword">visual_layer</span>: <span class="val">なし</span>
  <span class="dim">├──</span> <span class="keyword">temporal_tag</span>: <span class="val">まばら、イベント起点のみ</span>
  <span class="dim">└──</span> <span class="keyword">ext_storage</span>: <span class="val">Google Photos（時間インデックス）</span>
</div>

<!-- section: $0C0000 "好奇心のループ" -->

子どもの頃、電卓やリモコン、理解したいものは何でも分解していました。
たいていの場合、面白い部分を全部隠すつまらない黒いエポキシの塊に
出くわしましたが。でも衝動はいつも同じ：仕組みを見て、内部モデルを作って、
挙動を予測する。

<div class="mem-block">
<span class="label">CURIOSITY_CYCLE</span>
  <span class="val">システムに出会う</span>
      <span class="dim">↓</span>
  <span class="val">内部構造を推測する</span>
      <span class="dim">↓</span>
  <span class="val">メンタルモデルを構築する</span>
      <span class="dim">↓</span>
  <span class="val">システムが予測可能になる</span>
      <span class="dim">↓</span>
  <span class="val">興味が薄れる → 次の未知へ</span>
</div>

その裏返しとして、何でも自分で理解してやろうとすると、すべてが難しくなる。
かつてブログに書いたことがある——ギークとはシャドックのようなものだと。
シンプルにできるのに、なぜわざわざ複雑にするのか？　一晩中Firefoxを
ソースからコンパイルして——パッチを当て、デバッグし、依存関係を追いかけて
——朝6時に太陽が昇ったら`apt-get install`でリリース版を入れて寝る。
ブラウザが目的じゃない。その過程で学んだ百のことが目的なのだ。
これが最終的に**Azusa**という擬似Linuxディストリビューションの構築に
つながった。glibcをはじめとするGNUコンポーネントを専用ディレクトリに
配置し、別のディストリビューションと共存できるように設計されている。
つまりすべてを自分でコンパイルするということ——中には相当手強い
パッケージもある。

今ではCPUの内部構造を理解しています——命令からレジスタ、論理ゲートから
トランジスタまで——電卓やリモコンのチップがどう作られているかも、
分解しなくてもだいたいわかります。機械的なものにも同じことが言えます：
ドアの取っ手の機構、錠前、機械。見える可動部分を観察するだけで、
どう作られたか想像でき、内部の仕組みをすぐにリバースエンジニアリングできます。

同じ直感はソフトウェアにも当てはまります。ほとんどのシステムの内部がほぼ
瞬時に見えて、最終的な成果物に至るまでのプロセスの見当がつきます。
完全に再現できることよりも、プロセスを理解することで満足することを
学びました。報酬はモデルであって、成果物ではないのです。

複雑な問題のデバッグはバックグラウンドで一番うまくいきます。頭の片隅に
置いておくと、解決策が浮かぶのはたいていシャワーを浴びている時。
脳が複雑な関係モデルを構築し、リラックスすると、デフォルトモードネットワークが
正しい構造が現れるまでそれを再構成してくれるのです。
