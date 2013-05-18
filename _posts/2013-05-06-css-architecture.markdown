¡---
layout: post
title: "CSS Architecture"
byline: "Philip Walton"
date: 2013-05-06 10:00:00
---
**Credit**  
This article is translated with permission of Philip Walton([@philwalton](https://twitter.com/philwalton)). 
Original article is written at his Blog. 
[CSS Architecture](http://philipwalton.com/articles/css-architecture/)  

本記事はPhilip Walton([@philwalton](https://twitter.com/philwalton))氏の了承を得て翻訳された記事です。
原文は彼のブログにて書かれた[CSS Architecture](http://philipwalton.com/articles/css-architecture/) です。

## CSS Architecture
## CSSアーキテクチャ

To many Web developers, being good at CSS means you can take a visual mock-up and replicate it perfectly in code. You don’t use tables, and you pride yourself on using as few images as possible. If you’re really good, you use the latest and greatest techniques like media queries, transitions and transforms. While all this is certainly true of good CSS developers, there’s an entirely separate side to CSS that rarely gets mentioned when assessing one’s skill.
多くのWebデベロッパとって、良いCSSとはビジュアルモックアップをコードで完全に再現できることを意味する。tableタグを使わず、また出来る限り画像を少なくすることに誇りを感じる。もし良ければ、メディアクエリ、Transitions、Transformといった最新で偉大な技術を使う。
これらすべてが良いCSSデベロッパとしての確かな真実ではあるものの、スキルとして評価されるときにあまり言及されないCSSのまったく別の側面がある。

Interestingly, we don’t usually make this oversight with other languages. A Rails developer isn’t considered good just because his code works to spec. This is considered baseline. Of course it must work to spec; its merit is based on other things: Is the code readable? Is it easy to change or extend? Is it decoupled from other parts of the application? Will it scale?
興味深いことに、私たちは通常他言語においてはこのようなことを見過ごさない。Railsのデベロッパは、仕様通りにコードが動くというだけで、それが良いと考えない。これはあくまで基本的なことだと考える。もちろん仕様通りに動くことは必要であるが、これらのメリットは他の事に基づいている。そのコードは読みやすいか？変更・拡張しやすいか？アプリケーションのその他パーツと分離できるか？拡張性があるか？といったことだ。

These questions are natural when assessing other parts of the code base, and CSS shouldn’t be any different. Today’s web applications are larger than ever, and a poorly thought-out CSS architecture can cripple development. It’s time to evaluate CSS the same way we evaluate every other part of the application. It cannot be an afterthought or written off as merely the “designer’s” problem.
これらの疑問は、コードの基礎以外を評価するときに自然に湧いてくるもので、CSSはそれらと異なるべきではない。今時のWebアプリケーションは従来のものよりも大きく、そしてまともに考えられていないCSSアーキテクチャは開発を不便にする。CSSはすべてのWebアプリケーションの一部として他言語同様に評価する時がきている。ただの思いつきや、単にデザイナーの問題として終わらせるわけにはいかない。

## The Goals of Good CSS Architecture

In the CSS community, a general concensus of best practices is very difficult to come by. Judging purely by [the comments on Hacker News](https://news.ycombinator.com/item?id=2658948) and the [reaction of developers](http://2002-2012.mattwilcox.net/archive/entry/id/1054/) to the release of [CSS Lint](http://csslint.net/), it’s clear that many people disagree over even the basic things CSS authors should and shouldn’t do.
CSSコミュニティにおいて、ベストプラクティスの合意を得るのは非常に難しい。単にCSS LintがリリースされたときのHacker Newsでのコメントと、デベロッパーの反応で判断すると、基本的なCSSの事柄をコーダーがすべきかそうでないかということですら、多くの人が同意しないのは明白だ。

So instead of laying out an argument for my own set of best practices, I think we should start by defining our goals. If we can agree upon the goals, hopefully we can start to spot bad CSS not because it breaks our preconceived notions of what’s good but because it actually hinders the development process.
つまり、自身のベストプラクティスの議論を並べる代わりに、自分たちのゴールを定義することから始めるべきだと私は考えている。もしゴールによって同意することができたなら、願わくば、悪いCSSの見当をつけはじめるのは、何が良いのかという先入観に向うのではなく、開発プロセスの阻害要因を明確にするからだ。

I believe the goals of good CSS architecture shouldn’t be that different from the goals of all good software development. I want my CSS to be predictable, reusable, maintainable, and scalable.
私は良いCSSアーキテクチャのゴールは、すべての良いソフトウェアのゴールと異なるべきではないと確信している。CSSは予測、再利用、保守、拡張しやすいものであってほしい。

### Predictable
### 予測しやすい

Predictable CSS means your rules behave as you’d expect. When you add or update a rule, it shouldn’t affect parts of your site that you didn’t intend. On small sites that rarely change, this isn’t as important, but on large sites with tens or hundreds of pages, predictable CSS is a must.
予測しやすいCSSとはあなたのルールが期待通りに振る舞うことを意味する。ルールを追加・更新したとき、そのルールは意図せずサイトの一部に影響を与えるべきではない。滅多に変更されない小規模なサイトであれば、このことはあまり重要ではないが、数十、数百ページの大規模なサイトであれば、予測しやすいCSSは必須といえる。

### Reusable
### 再利用しやすい

CSS rules should be abstract and decoupled enough that you can build new components quickly from existing parts without having to recode patterns and problems you’ve already solved.
CSSのルールは抽象的で、十分に分離されているべきである、それはパターンとすでに解決した問題を書きなおす必要なく、既存のパーツから新しいコンポーネントを速くつくることができるということだ。

### Maintainable
### 保守しやすい

When new components and features need to be added, updated or rearranged on your site, doing so shouldn’t require refactoring existing CSS. Adding component X to the page shouldn’t break component Y by its mere presence.
サイトに新しいコンポーネントと機能が追加・更新されるか、再編される必要があるとき、既存のCSSのリファクタリングを必要とすべきではない。ページにコンポーネントXを追加するときに、そのわずかな存在によってコンポーネントYを壊すべきではない。

### Scalable
### 拡張しやすい

As your site grows in size and complexity it usually requires more developers to maintain. Scalable CSS means it can be easily managed by a single person or a large engineering team. It also means your site’s CSS architecture is easily approachable without requiring an enormous learning curve. Just because you’re the only developer touching the CSS today doesn’t mean that will always be the case.
サイトが大きく、複雑に成長していくにつれて、通常はたくさんのデベロッパをメンテナンスのために必要とする。
拡張しやすいCSSとは、ひとりのデベロッパか、大きなエンジニアチームかを問わず、容易に管理できることを意味する。またそのサイトのCSSアーキテクチャに、巨大な学習曲線を必要することなく容易に近づけるという意味でもある。あなたが今日CSSを触る唯一のデベロッパだからとって、先々にも常にあなただけであるというわけではない。

## Common Bad Practices
## よくあるバッドプラクティス

Before we look at ways to achieve the goals of good CSS architecture, I think it can be helpful to look at common practices that get in the way of our goals. It’s often only through repeated mistakes that we can begin to embrace an alternate path.
良いCSSアーキテクチャのゴールを達成するための方法を考える前に、ゴールの妨げとなるよくあるプラクティスを考えるのが有用だと考えている。それはしばしば過ちを繰り返すことによってのみ、代替方法を捉えはじめることができる。

The following examples are all generalizations of code I’ve actually written, and, while technically valid, each has lead to disaster and headache. Dispite my best intentions and the promise that this time would be different, these patterns consistently got me into trouble.
以下の例はすべて私が実際に書いたことのあるコードを一般したもので、技術的に正当であるものの、それぞれ災難や頭痛を招いた。細心の注意を払い、今回は違うと見込んでいたにも関わらず、それらのパターンは常に私を問題に巻き込んだ。

### Modifying Components Based On Who Their Parents Are
### 親に基づいたコンポーネントを修正する

In almost every site on the web there will be a particular visual element that looks exactly the same with each occurrence, except one. And when faced with this one-off situation almost every new CSS developer (and even experienced ones) handles it the same way. You figure out some unique parent for this one particular occurrence (or you create one), and you write a new rule to handle it.
ほとんどのWebサイトにおいて、ひとつを除き、全く同じ見た目である特定のデザイン要素がある。そして、こうした単発なシチューエーションに直面したとき、ほとんどの新しいCSSデベロッパ（もしくはそれらを経験したことがあるデベロッパさえも）、同じ方法で対処する。この特定の要素（または自身で作った要素）のユニークな親を見つけ出し、その特定の要素を操作するための新しいルールを書く。

```
.widget {
  background: yellow;
  border: 1px solid black;
  color: black;
  width: 50%;
}

#sidebar .widget {
  width: 200px;
}

body.homepage .widget {
  background: white;
}
```

At first this might seem like fairly harmless code, but let’s examine it based on the goals established above.
一見それほど害の無いものだと思えるが、前述のゴールに基づいて分析してみよう。

First, the widget in the examle is not predictable. A developer who’s made several of these widgets will expect it to look a certain way, yet when she uses it in the sidebar or on the homepage, it will look different, despite the markup being exactly the same.
最初に、例にあるウィジェットは予測ができない。デベロッパは、誰かがつくったいくつかのウィジェットが同じような見た目であることを期待するも、彼女はそれをサイドバーまたホームページで使うとき、マークアップは一緒であるにもかからわず、見た目が異なるだろう。

It’s also not very reusable or scalable. What happens when the way it looks on the homepage is requested on some other page? New rules will have to be added.
また再利用性または拡張性もほとんど無い。ホームページと同じ見た目のそれを他のページにも求められた時、どうなるだろうか？新しいルールが追加しなければいけない。

Lastly, it’s not easily maintainable because if the widget were to get redesigned, it would have to get updated in several places in the CSS, and unlike the above example, the rules that commit this particular anti-pattern rarely appear right next to each other.
最後に、もしそのウィジェットのデザインが変更されたら、あらゆるところにあるCSSを更新する必要があり、また例とは異なるが、それぞれのすぐとなりにめったに現れない特定のアンチパターンにはたらくルールとなるため、再利用性や保守性がない。

Imagine if this type of coding were done in any other language. You’re essentially making a class definition, and then in another part of the code you’re reaching into that class definition and changing it for a particular use case. This directly violates the open/closed principle of software development:
他言語においてこのようなコーディングをする場合を想像してほしい。基本的なクラス定義をし、それからコードの中の別の場所のクラス定義と特定の利用例のための変更に手を入れる。これはソフトウェア開発におけるオープン/クローズの原則に直接反する:

    Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.
    ソフトウェアの仕様（クラス、モジュール、関数、など）は拡張に対してオープンにあるべきで、修正に対してはクローズであるべきだ。

Later in this article we’ll look at how to modify components without relying on parent selectors.
本記事の後半で、どうやって親セレクタに頼らずにコンポーネントをどう修正するのかをみてみよう。

### Overly Complicated Selectors
### 過剰に複雑なセレクタ

Occasionally an article will make its way around the Internet showcasing the power of CSS selectors and proclaiming that you can style an entire site without using any classes or IDs.
時々、サイト全体でクラスまたはIDを使わずにスタイルをつくるための、CSSセレクタと宣言の力を紹介するショーケースサイトの記事がある。

While technically true, the more I develop with CSS, the more I stay away from complex selectors. The more complicated a selector the more coupled it is to the HTML. Relying on HTML tags and combinators keeps your HTML squeaky clean, but it makes your CSS gross and dirty.
技術的には正しいが、私はCSSの開発をすればするほど、複雑なセレクタを避ける。セレクタが複雑になるほど、HTMLとの関係が密になる。HTMLタグと組み合わせを非常にきれいにしておくことへの依存は、CSSを粗く汚くする。

```
#main-nav ul li ul li div { }
#content article h1:first-child { }
#sidebar > div > h3 + p { }
```

All of the above examples make logical sense. The first is probably styling a dropdown menu, the second says that the article’s main heading should look different than all other h1 elements, and the last example is likely adding some extra spacing for the first paragraph in the sidebar sections.
上記のコード例すべて意味を成している。ひとつはおそらくドロップダウン型メニューのスタイルであること、ふたつめは記事の主な見出しが他のh1要素とはことなる見た目であることを意味し、最後の例はサイドバーセクションにおいて、最初の段落に何らかの余白を加えたいように見える。

If this HTML were never going to change, an argument could be made for its merits, but how realistic is it to assume the HTML will never change? Overly complicated selectors can be impressive and they can rid the HTML of all presentational hooks, but they rarely help us achieve our goals for good CSS architecture.
もしHTMLが今後まったく変わらないのであればメリットはありえるが、どうやって現実的にはHTMLが変わらないことを前提にできるだろうか。過剰に複雑なセレクタは感動的で、見た目のフックとなるHTMLを取り除くことができるが、良いCSS設計のゴールを成し遂げる手助けにはほとんどならない。

These examples above are not reusable at all. Since the selector is pointing to a very particular place in the markup, how could another component with a different HTML structure reuse those styles? Taking the first selector (the dropdown) as an example, what if a similar looking dropdown were needed on a different page and it wasn’t inside of the #main-nav element? You’d have to recreate the entire style.
上記の例はすべて再利用しづらい。セレクタがマークアップの非常に特定な場所を指してから、どうやって異なるHTML構造である他のコンポーネントでこれらのスタイルを再利用できるだろうか？例にある（ドロップダウンの）最初のセレクタを取りあげると、もし同じ見た目のドロップダウンは違うページで、`#main-nav`要素の中になければどうだろうか？まったく同じスタイルを再度つくることになる。

These selectors are also very unpredictable if the HTML needs to change. Imagine that a devloper wanted to change the div in the third example to the HTML5 section tag, the whole rule would break.
また、もしHTMLを変更する必要がある場合、これらのセレクタにはほとんど予測しづらい。デベロッパが3つ目の例にあるdivタグをHTMLのsectionタグに変更したいと思ったときを想像すると、すべてのルールが破綻するだろう。

Finally, since these selectors only work when the HTML remains constant, they’re by definition not maintainable or scalable.
最後に、HTMLが一定に保たれている時にだけセレクタが機能するので、当然保守性や拡張性もない。

In large applictaions you have to make trade-offs and compromises. The fragility of complex selectors are rarely worth the price in the name of keeping your HTML “clean”.
大規模なアプリケーションにおいては、妥協や譲歩をしなければいけない。複雑なセレクタの壊れやすさは、HTMLを"クリーン"に保つということの価値とほとんど見合わない。

#### OVERLY GENERIC CLASS NAMES
#### 過剰に一般的なクラス名

When creating reusable design components, it’s very common to scope (as it were) the component’s sub-elements inside the component’s class name. For example:
再利用しやすいデザインのコンポーネントを作るとき、コンポーネントのクラス名の中のコンポーネントの副要素を（いわば）スコープにするのはとてもよくあることだ。

```
<div class="widget">
  <h3 class="title">...</h3>
  <div class="contents">
    Lorem ipsum dolor sit amet, consectetur adipiscing elit.
    In condimentum justo et est dapibus sit amet euismod ligula ornare.
    Vivamus elementum accumsan dignissim.
    <button class="action">Click Me!</button>
  </div>
</div>
```

```
.widget {}
.widget .title {}
.widget .contents {}
.widget .action {}
```

The idea is that the .title, .contents, and .action sub-element classes can be safely styled without having to worry about those styles spilling over to any other elements styled with the same classes. This is true, but it doesn’t prevent the styling of classes with those same names from spilling into this component.
この考え方は`title`、`.contents`、`.action`といった副要素が、同じクラス名を持つその他の要素に広がるという心配をする必要もなく、安全にスタイルをつくることができるというものだ。これは正しいが、そのコンポーネント内で同じ名前のクラスのスタイルをつくるのを防ぐことができない。

On a large project it’s very likely that a class name like .title would get used in another context or even by itself. If that happened, the widget’s title would suddenly look very different than intended.
大規模なプロジェクトにおいて、`.title`のような名前のクラスはその他の文脈やそれ自身としても使われる可能性が高い。そうすると、ウェジェットのタイトルが突然意図とは異なる見た目になる。

Overly generic class names lead to very unpredictable CSS.
過剰に一般的なクラス名は、非常に予測しづらいCSSにしてしまうことがある。

#### MAKING A RULE DO TOO MUCH
#### 過剰にルールをつくる

Sometimes you make a visual component that needs to be 20 pixels from the top left corner of a section in your site:
時々、サイト内のセクション左上の角から20pxに配置する必要があるというような、見た目のコンポーネントをつくることがある。

```
.widget {
  position: absolute;
  top: 20px;
  left: 20px;
  background-color: red;
  font-size: 1.5em;
  text-transform: uppercase;
}
```

Then down the road you need to use this exact same component in a different location. The above CSS won’t work because it’s not reusable in different contexts.
やがて、異なる場所でまったく同じ名前のコンポーネントを使う必要があるときがある。前述のCSSは、異なるコンテキストでは再利用しづらいので機能しない。

The problem is that you’re making this one selector do too much. You’re defining the look and feel as well as the layout and position within the same rule. The look and feel is reusable but the layout and position is not. And since they’re used together, the whole rule is compromised.
この問題は、ひとつのセレクタに多くのことをやらせようとしていることにある。同じルールに見た目もレイアウト・配置も定義している。見た目は再利用でくるが、レイアウト・配置は再利用しづらい。そのため一緒に使うと、すべてのルールが信用できなくなる。

While this may seem harmless at first, it often leads to copying and pasting from less CSS-savvy developers. If a new team member wants something to look like a particular component, say an .infobox, they’ll probably start by trying that class. But if that doesn’t work because it positions that new infobox in an undesired way, what are they likely to do? In my experience, most new developers will not break the rule up into its reusable parts. Instead they’ll simply copy and paste the lines of code needed for this particular instance into a new selector, unnecessarily duplicating code.
はじめは害がないように見えても、時々CSSの経験が少ないデベロッパがコピー＆ペーストしてしまうのを誘発してしまう。もし新しいメンバーが特定のコンポーネントと同じ見た目のものが欲しいと思った場合、例えば.infoboxというようなものであれば、まず彼らはたぶんそのクラスを試みることから始めるだろう。ところが、新しいinfoboxは望まない形で配置されるため機能せず、次に彼らはどのようにするだろうか？私の経験上、ほとんどの新しいデベロッパーは、それらのルールを再利用しやすいパーツに分けることはしない。代わりに、単純に特定のインスタンスに必要なコードの数行を新しいセレクタにコピー&ペーストし、不要なコードを複製することになる。

### The Cause
### 原因

All of the above bad practices share one similarity, they place far too much of the styling burden on the CSS.
前述のすべてのバッドプラクティス???

That may seem like strange statement. After all, it is a stylesheet; shouldn’t it bear most (if not all) of the styling burden? Isn’t that what we want?
予想外の声明のようだ。結局のところ、スタイルシートであり、（すべてじゃなくとも）スタイルの責任がかかるべきだろうか？どのようにすれば良いだろうか？

The simple answer to this question is “yes”, but, as usual, things aren’t always so simple. Separating content from presentation is a good thing, but just because your CSS is separate from your HTML doesn’t mean your content is separate from your presentation. Put another way, striping all presentational code from your HTML doesn’t fulfill the goal if your CSS requires an intimate knowledge of your HTML structure in order to work.
この問いの単純な答えは"イエス"だが、いつもの通り、物事はいつもそんなに単純ではない。見た目からコンテンツを切り離すのは良いことだが、CSSをHTMLから切り離したというだけで、見た目からコンテンツを切り離したということではない。言い換えれば、HTMLからすべての見た目に関するコードをはがすことが、もしCSSが機能するためにHTML構造に関する深い知識を要するとしても、ゴールを果たすということではない。

Furthermore, HTML is rarely just content; it’s almost always structure too. And often that structure consists of container elements with no purpose other than to allow the CSS to isolate a certain group of elements. Even without presentational classes, this is still clearly presentation mixed into the HTML. But is it necessarily mixing presentation with content?
その上、HTMLはただのコンテンツであるということはめったになく、構造もまた常に同じだ。また、構造が、いくつかの要素グループから分離するCSSを許容するような、目的を持たない包括要素で成ることがある。見た目のクラスが無かったとしても、HTML内に見た目が混ざっていることはまだ明白だ。しかし、必ずしもコンテンツと見た目が混ざるだろうか？

I believe, given the current state of HTML and CSS, it’s necessary and often wise to have the HTML and CSS work together as a presentational layer. The content layer can still be abstracted away via templates and partials.
現在の状態のHTMLとCSSは与えられたとき、必要であり、見た目のレイヤーとして一緒にHTMLとCSSを機能させる必要がある。コンテンツレイヤーはテンプレートと部品からまだ抽象化できる。

### The Solution
### 解決方法

If your HTML and your CSS are going to work together to form the presentation layer of a web application, they need to do so in a way that promotes all of the principles of good CSS architecture.
もしHTMLとCSSがWebアプリケーションのプレゼンテーションレイヤーから一緒に機能するとすれば、良いCSS設計の原則を形成するすべての方法を必要とする。

The best approach that I’ve found is for the CSS include as little HTML structure as possible. The CSS should define how a set of visual elements look and (in order to minimize coupling with the HTML) those elements should look as they’re defined regardless of where they appear in the HTML. If a certain component needs to look different in a different scenario, it should be called something different and it’s the HTML’s responsibility to call it that.
私が
できるだけ小さいHTML構造を含むCSSのためのベストアプローチを発見した。CSSはどうやってビジュアル要素の見た目のセットを定義するべきで、（HTMLとの連結を極力小さくするために）HTMlがどこに出現するかに関わらず定義されたように見なければいけない。もし特定のコンポーネントが異なるシナリオの中で異なる見た目である必要がある場合、それは異なって呼び出されるべきだし、HTMLの責務として呼ぶべきだ。

As an example, the CSS might define a button component via the .button class. If the HTML wants a particular element to look like a button, it should use that class. If there’s a situation were the button needs to look different (perhaps larger and full-width), then the CSS needs to define that look as well with a new class, and the HTML can include that new class to employ the new look.
例をあげると、CSSはボタンコンポーネントを'.button'クラスとして定義するだろう。もしHTMLがボタンのような見た目の特定の要素を求める場合、そのクラスを使うべきだ。もしそうしたシチュエーションで異なる見た目（たぶん大きなものや、幅がいっぱいのもの）のボタンを必要とした場合、HTMLは新しい見た目の役割をする新しいクラスを含むことができる。

The CSS defines what your components look like, and the HTML assigns those looks to the elements on the page. The less the CSS needs to know about the HTML structure the better.
CSSがコンポーネントがどういう見た目であるかを定義し、HTMLがページ上の要素に見た目のCSSを割り当てる。CSSが少なければ少ないほど、HTML構造についてより知っておく必要がある。

A huge benefit of declaring exactly what you want in the HTML is it allows other developers to look at the markup and know exactly what the element is supposed to look like. The intent is obvious. Without this practice it’s impossible to tell if the look of an element is intentional or accidental, and this leads to confusion on the team.
HTMlが何を求めているかを明確に宣言することの大きな利益は、他のデベロッパがマークアップから見つけ、要素がどのような見た目になるかを明確に知ることを許容する。その目的は明白だ。このプラクティスが無ければ、もしある要素の見た目が意図的か偶発的を伝えることは不可能で、チームを混乱に導く。

A common objection to putting a lot of classes in the markup is the extra effort required to do so. A single CSS rule could target a thousand instances of a particular component. Is it really worth writing that classes a thousand times just to have it explicitly declared in the markup?
マークアップに多くのクラスを置くことに対するよくある異論は、そうすることに余分な努力を必要とする。単一クラスルールはある特定のコンポーネントのたくさんのインスタンスを対象とすることができる。マークアップ上でクラスを何千回も明示的に宣言することは本当に価値があるのだろうか。

While this concern is clearly valid, it can be misleading. The implication is that either you use a parent selector in the CSS or you write that HTML class 1000 times by hand, but there are obviously other alternatives. View level abstractions in Rails or other frameworks can go a long way toward keeping the visual look explicitly declared in the HTML without having to write the same class over and over again.
その懸念は明らかに正当であるものの、誤解を招きかねない。この意味あいは、CSSで親セレクタを使うか、それともHTMLに1000回クラスを手で書くかということであるが、代替案があることは明白だ。Railsのビューレベルの抽象概念かその他のフレームワークは、同じクラスを何度も書く必要無く、HTMLで明白に宣言された見た目を維持する目的で大いに役立つ。

### Best Practices
### ベストプラクティス

After making the above mistakes over and over again, and paying the consequences later on down the road, I’ve come up with the following bits of advice. While by no means comprehensive, my experience has shown that sticking to these principles will help you better achieve the goals of good CSS architecture.
これらの過ちを何度も繰り返し、やがて後でその結果に苦しんだ後に、ちょっとしたアドバイスを思いついた。決して総合的であるとはいえないが、私の経験で、これらの原則に忠実であることが良いCSSアーキテクチャのゴールを成し遂げる手助けになるということを説明しよう。

#### BE INTENTIONAL
#### 意図的であれ

The best way to make sure your selectors don’t style unwanted elements is to not give them the opportunity. A selector like #main-nav ul li ul li div could very easily end up applying to unwanted elements as your markup changes down the road. A style like .subnav, on the other hand, will have absolutely no chance of accidentally applying to an unwanted element. Applying classes directly to the elements you want to style is the best way to keep your CSS predictable.
セレクタが、望んでいない要素にスタイルをあてないようにする確実な方法は、その機会をセレクタに与えないことだ。'#main-nav ul li ul li div'のようなセレクタは非常に容易に、将来的にマークアップが変わったとしても望まない要素にスタイルを適用しやすい。一歩で、'.subnav'のような形式であれば、望まない要素に偶然にもスタイルが適用される機会はほとんどありえない。スタイルを適用させたい要素に直接クラスを適用することは、予測しやすいCSSを維持する一番の方法だ。

```
/* Grenade */
#main-nav ul li ul { }

/* Sniper Rifle */
.subnav { }
```

Given the two examples above, think of the first one like a grenade and the second like a sniper rifle. The grenade might work just fine today, but you never know when an innocent civilian could move inside the blast radius.
2つの例をあげるが、1つ目は手榴弾のように、2つ目はスナイパーライフルのように考えてほしい。この手榴弾は、今は思っている通りに機能するが、いつか無実な市民がその爆発圏内に入ってくるかは知りようがない。

#### SEPARATE YOUR CONCERNS
#### 懸念を分離せよ

I’ve already mentioned that a well organized component layer can help loosen the coupling of HTML structure in the CSS. In addition to that, your CSS components themselves should be modular. Components should know how to style themselves and do that job well, but they should not be responsible for their layout or positioning nor should they make too many assumptions about how they’ll be spaced in relation to surrounding elements.
すでに言及したが、よく組織されたコンポーネントレイヤーはCSSの中のHTML構造を関係をゆるめるのを手助けすることができる。付け加えると、CSSコンポーネント自身がモジュールであるべきだ。コンポーネントはそれ自身がどのようなスタイルで、どのように振る舞うかを知っておくべきであるが、それらのレイアウトや配置や、周りの要素との関係からどう引き離されるかについての多すぎる仮説の責任を持つべきではない。

In general, components should define how they look, but not their layout or position. Be careful when you see properties like background, color, and font in the same rule as position, width, height, and margin.
一般的に、コンポーネントはそれらがどのように見えるかを定義すべきだが、レイアウトや配置については異なる。'background'、'color'、'font'のようなプロパティと、'position'、'width'、'height'、'margin'といったプロパティと同じルールであった場合には注意しよう。

Layout and position should be handled by either a separate layout class or a separate container element. (Remember that to effectively separate content from presentation it’s often essential to separate content from its container.)
レイアウトや配置は他の分離されたレイアウト用のクラスや、コンテナ要素によって制御されるべきだ。（プレゼンテーションとコンテンツを効果的に分離することは、大抵コンテナとコンテンツを分離するために不可欠であることを思い出してほしい。）

#### NAMESPACE YOUR CLASSES
#### クラスの名前空間

We already examined why parent selectors aren’t 100% effective at encapsulation and preventing style cross-contamination. A much better approach is applying namespaces to the classes themselves. If an element is a member of a visual component, every one of its sub-element classes should use the component’s base class name as a namespace.
なぜ親セレクタがカプセル化や相互汚染の予防に100%効果的でないことは、すでに調べた通りだ。より良いアプローチはクラス自身に名前空間を持たせる方法だ。もし、ある要素がビジュアルコンポーネントの1つである場合、そのサブ要素となるクラスすべてが、コンポーネントのべ＝スクラス名を名前空間として使用すべきだ。

```
/* High risk of style cross-contamination */
/* 相互汚染のリスクが高い */
.widget { }
.widget .title { }

/* Low risk of style cross-contamination */
/* 相互汚染のリスクが低い */
.widget { }
.widget-title { }
```

Namespacing your classes keeps your components self-contained and modular. It minimizes the likelihood that an existing class will conflict, and it lowers the specificity required to style child elements.
クラスに名前空間を持たせることで、コンポーネントを独立、モジュールとして維持させることができる。これは既存のクラスが衝突する可能性を最小限にし、子要素にスタイルを適用するセレクタの詳細度を低く抑えることができる。

#### EXTEND COMPONENTS WITH MODIFIER CLASSES
#### コンポーネントはモディファイアクラスで拡張する

When an existing component needs to look slightly different in a certain context, create a modifier class to extend it.
既存のコンポーネントが、あるコンテキストにおいてわずかに異なる見た目である必要があるとき、その拡張をするためのモディファイアクラスをつくる。

```
/* Bad */
.widget { }
#sidebar .widget { }

/* Good */
.widget { }
.widget-sidebar { }
```

We’ve already seen the downsides of modifying components based on one of their parent elements, but to reiterate: A modifier class can be used anywhere. Location based overrides can only be used in a specific location. Modifier classes can also be reused as many times as you need. Lastly, modifier classes express the intention of the developer very clearly right in the HTML. Location based classes, on the other hand, are completely invisible to a developer only looking at the HTML, greatly increasing the probability that it will be overlooked.
親要素のひとつに基づいて修正されたコンポーネントのマイナス面についてはすでに分かっているが、繰り返される: モディファイアクラスはどこでも使うことができる。場所を軸にしたクラスは、特定の場所でしか使えない。モディファイアクラスはまた必要なだけ何度も再利用することができる。最後に、モディファイアクラスはデベロッパがHTML上でわかりやすくしたいという意図を表現する。一方で場所を軸にしたクラスの場合、そのデベロッパしかHTML上で判別できないよう完全に隠蔽し、見落とされる可能性を非常に高めてしまう。

#### ORGANIZE YOUR CSS INTO A LOGICAL STRUCTURE
#### CSSをロジカルに体系化せよ

[Jonathan Snook](http://snook.ca/), in his excellent book [SMACSS](http://smacss.com/), argues for organizing your CSS rules into four separate categories: base, layout, modules, and state. Base consists of reset rules and element defaults. Layout is for positioning of site-wide elements as well as generic layout helpers like grid systems. Modules are reusable visual elements, and state refers to styling that can be toggled on or off via JavaScript.
[Jonathan Snook](http://snook.ca/)氏の素晴らしい書籍、[SMACSS](http://smacss.com/)では、CSSルールを4つに分類されたカテゴリで体系化することを唱えている。それはベース、レイアウト、モジュール、ステートの4つだ。ベースはルールのリセットと要素のデフォルトスタイルの定義で構成される。レイアウトはsite-wideな要素の配置や、およびグリッドシステムのような一般的なレイアウトヘルパーとなる。モジュールは再利用しやすいビジュアル要素で、ステートはJavaScriptでオン・オフを切り替えるスタイルを指す。

In the SMACSS system, modules (which are equivalent to what I call components) comprise the vast majority of all the CSS rules, so I often find it necessary to break them down even further into abstract templates.
SMACSSのシステムでは、モジュール（私がコンポーネントと呼んでいるものと同等のもの）は、すべてのCSSルールの大部分から成るため、私はよくもっと抽象的なテンプレートに分類する必要があるルールに出くわす。

Components are standalone visual elements. Templates, on the other hand, are building blocks. Templates don’t stand on their own and rarely describe look and feel. Instead, they’re single, repeatable patterns that can be put together to form a component.
コンポーネントは独立したビジュアル要素だ。一方、テンプレートはブロックを作る。テンプレートは自立せず、見た目を表現することは滅多にない。
テンプレートは単独でコンポーネントを形成するためにまとめられた繰り返しやすいパターンとなる。

To provide a concrete example, a component might be a modal dialog box. The modal might have the site’s signature background gradient in the header, it might have a drop shadow around it, it might have a close button in the top right corner, and it might be positioned fixed and centered vertically and horizontally. Each of these four patterns might be used again and again all over the site, so you wouldn’t want to have to recode those patterns each time. As such they’re all templates, and together they comprise the modal component.
具体的な例を挙げると、モーダルダイアログがコンポーネントのひとつだ。このモーダルはサイトの特徴的なグラデーション背景をヘッダーに持ち、周辺にドロップシャドウ、右上の角にモーダルを閉じるボタン、天地中央に固定配置されるとしよう。これら4つのパターンがそれぞれサイト全体で繰り返して使われるとしたら、その度にこれらのパターンを残そうとは思わないだろう。そのように、これらはすべてのテンプレートに存在し、一緒にモーダルコンポーネントから成る。

I typically don’t use template classes in the HTML unless I have a good reason. Instead I use a preprocessor to include the template styles in the component definition. I’ll discuss this and my rational for doing so in more detail later.
例によって私は、特別な理由がない限り、HTML上でテンプレートのクラスを使うことはない。代わりにコンポーネント定義にあるテンプレートのスタイルを含めるためにプリプロセッサを使う。この点と私が合理的にどうするかについては後ほど解説しよう。

#### USE CLASSES FOR STYLING AND STYLING ONLY
#### クラスをスタイルのために使い、スタイルのためだけに使う

Anyone who has worked on a large project has come across an HTML element with a class whose purpose was completely unknown. You want to remove it, but you’re hesitant because it may have some purpose that you’re not aware of. As this happens again and again, over time, your HTML become filled with classes that serve no purpose just because team members are afraid to delete them.
大きなプロジェクトで働いたことのある誰しもが、目的がまったく不明なクラスがあるHTMLの要素に出くわすことがあるだろう。それを取り除きたいと思っても、自分が知らないところで何かの目的をもって存在しているかもしれない、とためらってしまう。このようなことが何度も起こると、そのうちHTMLは、ただ単にチームメンバーはそれらのクラスを削除することを恐れているというだけで、何の目的も持たないクラスで溢れることになる。

The problem is that classes are generally given too many responsibilities in front-end web development. They style HTML elements, they act as JavaScript hooks, they’re added to the HTML for feature detections, they’re used in automated tests, etc.
この問題は、クラスがフロントエンドWeb開発において、大抵の場合責任を多く与えすぎることにある。クラスはHTMLの要素のスタイルをつくる、JavaScriptのフックとして機能させる、特徴検出（フィーチャーディテクション）のためにHTMLに追加される、自動テストで使われる、などだ。

This is a problem. When classes are used by too many parts of the application, it becomes very scary to remove them from your HTML.
これは問題である。クラスがアプリケーションの大部分で使われる時、HTMLからクラスを取り除くのを非常に恐れてしまう。

However, with an established convention, this problem can be completely avoided. When you see a class in the HTML, you should be able to tell instantly what its purpose is. My recommendation is to give all non-styled classes a prefix. I use .js- for JavaScript and I use .supports- for Modernizr classes. All classes without a prefix are for styling and styling only.
しかしながら、定着した慣習とともに、この問題は完全に避けられる。HTML上でクラスを見つけたら、その目的が何であるかをすぐに伝えるべきだ。私のおすすめは、スタイルを持たないすべてのクラスに接頭辞をつけることだ。私は、JavaScriptのために`js-`を、Modernizrのためには`.supports-`を使う。接頭辞がないクラス以外のすべては、スタイルを持ち、スタイルだけを持つようにする。

This makes finding unused classes and removing them from the HTML as easy as searching the stylesheets directory. You can even automate this process in JavaScript by cross referencing the classes in the HTML with the classes in the document.styleSheets object. Classes that aren’t in document.styleSheets can be safely removed.
この方法は使われていないクラスの発見や、スタイルシートのディレクトリから検索するように、HTMLから取り除くことができるようになる。JavaScriptを用いてHTMLと`document.styleSheets`オブジェクトを相互参照して、このプロセスを自動化することもできる。

In general, just as it’s a best practice to separate your content from your presentation, it’s also important to separate your presentation from your functionality. Using styled classes as JavaScript hooks deeply couples your CSS and JavaScript in a way that can make it hard or impossible to update the look of certain elements without breaking functionality.
一般的に、これが見た目からコンテンツを分離するためのベストプラクティスであるのと同じように、機能性から見た目を分離するのもまた重要である。見方によれば、スタイルを持つクラスをJavaScriptのフックとして使うことはCSSとJavaScriptを深く結合することになり、機能性を失わずに特定の要素の見た目を更新することが難しい、また不可能な状態にしてしまう。

#### NAME YOUR CLASSES WITH A LOGICAL STRUCTURE
#### 論理的な構造でクラスの命名をする

These days most people write CSS with hyphens as word separators. But hyphens alone are usually not enough to distinguish between different types of classes.
最近では多くのデベロッパがCSSを書くときにハイフンを単語の区切りとして使う。しかしハイフン単独では普通十分にクラスのタイプの違いの区別がつかない。

[Nicolas Gallagher](http://nicolasgallagher.com/) recently wrote about [his solution to this problem](http://nicolasgallagher.com/about-html-semantics-front-end-architecture/) which I have also adopted (with slight changes) with great success. To illustrate the need for a naming convention consider the following:
[Nicolas Gallagher](http://nicolasgallagher.com/)は、私も（わずかな変更で）大きな成果のためにまた導入した
[彼のこの問題への解決方法](http://nicolasgallagher.com/about-html-semantics-front-end-architecture/)について
最近記事を書いた。

```
/* A component */
/* コンポーネント */
.button-group { }

/* A component modifier (modifying .button) */
/* コンポーネントの修飾子(`.button`の修飾) */
.button-primary { }

/* コンポーネントのサブオブジェクト (`.button`の中で有効なオブジェクト) */
.button-icon { }

/* Is this a component class or a layout class? */
/* これはコンポーネントクラスなのか、レイアウトクラスなのか */
.header { }
```

From looking at the above classes, it’s impossible to tell what type of rule they apply to. This not only increases confusion during development, but it also makes it harder to test your CSS and HTML in an automated way. A structured naming convention allows you to look at a class name and know exactly what its relationship is to other classes and where it should appear in the HTML — making naming easier and testing possible where it previously was not.
前述のクラスを見ると、それぞれがどう適用されるルールなのかを伝えることは不可能だ。これは開発中の混乱を増やすだけでなく、自動的な方法でCSSとHTMLをテストすることを困難にしてしまう。構造化された命名規則はクラス名で考える余地を与え、他のクラスとはどういう関係か、HTMLのどこに現れるのかが、はっきりと分かる。あらかじめ命名規則があれば、命名をより容易に、テストも可能にしてくれる。

```
/* Templates Rules (using Sass placeholders) */
/* テンプレートルール (Sass プレースホルダセレクタ) */
%template-name
%template-name--modifier-name
%template-name__sub-object
%template-name__sub-object--modifier-name

/* Component Rules */
/* コンポーネントルール */
.component-name
.component-name--modifier-name
.component-name__sub-object
.component-name__sub-object--modifier-name

/* Layout Rules */
/* レイアウトルール */
.l-layout-method
.grid

/* State Rules */
/* ステートルール */
.is-state-type

/* Non-styled JavaScript Hooks */
/* スタイルの無いJavaScriptのフック */
.js-action-name
```

The first example redone:
最初の例を見直すと、

```
/* A component */
/* コンポーネント */
.button-group { }

/* A component modifier (modifying .button) */
/* コンポーネントの修飾子(`.button`の修飾) */
.button--primary { }

/* A component sub-object (lives within .button) */
/* コンポーネントのサブオブジェクト (`.button`の中で有効なオブジェクト) */
.button__icon { }

/* A layout class */
/* レイアウトクラス */
.l-header { }
```

### Tools
### ツール

Maintaining an effective and well-organized CSS architecture can be very difficult, especially on large teams. A few bad rules here and there can snowball into an unmanageable mess. Once your application’s CSS has entered into the realm of specificity wars and !important trumps, it can be next to impossible to recover without starting over. The key is to avoid those problems from the beginning.
効果的でよくまとまったCSS設計の保守は非常にむずかしく、特に大きなチームであるほどそうだ。あちこちにあるいくつかの悪いルールは、管理できないごちゃごちゃな、雪だるま式に増える。そうなり始めると、アプリケーションのCSSは詳細度の戦いや、`!important`のトランプに入ってしまう。

Fortunately, there are tools that can make controlling your site’s CSS architecture much easier.
幸運なことに、サイトのCSS設計をより容易に制御できるツールはある。

#### PREPROCESSORS
#### プリプロセッサ

These days it’s impossible to talk about CSS tools without mentioning preprocessors, so this article won’t be any different. But before I praise their usefulness, I should offer a few words of caution.
最近はプリプロセッサの話題を抜きにして、CSSツールについての話をすることはできず、本記事も例外ではない。しかしそれらの便利さを称賛する前に、いくつかの注意点について提案するべきだろう。

Preprocessors help you write CSS faster, not better. Ultimately it gets turned into plain CSS, and the same rules should apply. If a preprocessor lets you write your CSS faster then it also lets you write bad CSS faster, so it’s important to understand good CSS architecture before thinking a preprocessor will solve your problems.
プリプロセッサはCSSをより速く書く手助けをしてくれるが、より良くしてくれるわけではない。結局のところ、プリプロセッサはプレーンなCSSになり、同じルールが適用される。もしプリプロセッサがCSSコーディング速くするとしたら、それは悪いCSSコーディングを早くするともいえるので、プリプロセッサが問題を解決してくれるかもしれないということを考える以前に、良いCSS設計を理解することが重要だ。

Many of the so-called “features” of preprocessors can actually be very bad for CSS architecture. The following are some of the “features” I avoid at all costs (and though the general ideas apply to all preprocessor languages, these guidelines apply specifically to Sass).
プリプロセッサの特徴として多く挙げられていることは、CSS設計を実のところ非常に悪くしてしまう。下記に挙げるのは、私が避けるすべてのコストととしてのいくつかの特徴だ。（これらはの考えは全般的にすべてのプリプロセッサ言語に当てはめることができるが、これらのガイドラインは特にSassに対してのものとなる）

- Never nest rules purely for code organization. Only nest when the outputted CSS is what you want.
- ネストを単にコードをまとめるために使ってはいけない。ネストは出力されたCSSで必要なときにだけ使う。
- Never use a mixin if you’re not passing an argument. Mixins without arguments are much better used as templates which can be extended.
- 引数に渡せないミックスインを使ってはいけない。引数を持たないミックスインは、拡張できるテンプレートとして使われるのが好ましい。
- Never use @extend on a selector that isn’t a single class. It doesn’t make sense from a design perspective and it bloats the compiled CSS.
- `@extend`は単一クラスセレクタに使ってはいけない。設計視点からみてつじつまが合わないし、コンパイルされたCSSを膨れ上がらせる。
- Never use @extend for UI components in component modifier rules because you lose the inheritance chain (more on this in a bit).
- コンポーネント修飾子ルールの中のUIコンポーネントのために`@extend`を使ってはいけない、なぜなら継承チェーンを失うからだ。（少しだけであっても）

The best parts of preprocessors are functions like [@extend](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#extend) and [%placeholder](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#placeholder_selectors_). Both allow you to easily manage CSS abstraction without adding bloat to your CSS or a huge list of base classes in your HTML that can be very hard to manage.
プリプロセッサの優れた機能は[@extend](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#extend) と [%placeholder](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#placeholder_selectors_)だ。両者とも、CSSにルールを追加してCSSを膨らませたり、HTMLに管理が難しくなるほどの膨大な量のベースクラスで溢れさせることなく、容易にCSSを管理できるようにしてくれる。

@extend should be used with care though because sometime you want those classes in your HTML. For example, when you first learn about @extend it might be tempting to use it with all of your modifier classes like so:
@extendは注意して使われるべきにも関わらず、そのうちHTMLにそれらのクラスを求めてしまう。例えば、はじめて@extendを覚えたとき、それを次のように修飾子クラスのすべてに使いたいと思うかもしれない。

```
.button {
  /* button styles */
}

/* Bad */
.button--primary {
  @extend .button;
  /* modification styles */
}
```

The problem with doing this is you lose the inheritance chain in the HTML. Now it’s very difficult to select all button instances with JavaScript.
このように書くことの問題は、HTML上の継承チェーンを失ってしまうことだ。すぐにこれはJavaScriptでボタンインスタンスを選択することを非常に難しくする。

As a general rule, I never extend UI components or anything that I might want to know the type of later. This is what templates are for and another way to help distinguish between templates and components. A template is something you wouldn’t ever need to target in your application logic, and therefore can be safely extended with a preprocessor.
全般的なルールとして、私はUIコンポーネントや、後でどういうタイプかを知りたいと思う何かに対して拡張することはしない。
これはテンプレートがどういうものであるか、テンプレートとコンポーネントを区別するための別の方法である。
テンプレートは、アプリケーションロジックにおいて今後ターゲットにする必要が無い何かであるから、安全にプリプロセッサで拡張することができるのだ。

Here’s how it might look using the modal example referenced above:
ここで、モーダルをどのような見た目で作られるかの例を下記に挙げると、

```
.modal {
  @extend %dialog;
  @extend %drop-shadow;
  @extend %statically-centered;
  /* other modal styles */
  /* その他モーダルのスタイル */
}

.modal__close {
  @extend %dialog__close;
  /* other close button styles */
  /* その他閉じるボタンのスタイル */
}

.modal__header {
  @extend %background-gradient;
  /* other modal header styles */
  /* その他モーダルのヘッダーのスタイル */
}
```

#### CSS LINT
#### CSS LINT

[Nicole Sullivan](http://www.stubbornella.org/content/) and [Nicholas Zakas](http://www.nczonline.net/) created [CSS Lint](http://csslint.net/) as a code quality tool to help developers detect bad practices in their CSS. Their site describes it as such:
[Nicole Sullivan](http://www.stubbornella.org/content/) と [Nicholas Zakas](http://www.nczonline.net/) は、デベロッパがCSSの中にあるバッドプラクティスを見つける手助けをする、コードの品質ツール [CSS Lint](http://csslint.net/)を作った。彼らのサイトではこのように述べられている。

    CSS Lint points out problems with your CSS code. It does basic syntax checking as well as applying a set of rules to the code that look for problematic patterns or signs of inefficiency. The [rules] are all pluggable, so you can easily write your own or omit ones you don’t want.
    CSS Lintの特徴はCSSコードから問題を露見させることにある。基本的な構文チェックも、問題のあるパターンまたは非効率の予兆を探すためのルールセットをコードに適用することをおこなう。CSS Lintのルールはすべてプラグイン的に使えるので、容易に自分用のルールにすることや、望まいルールを省略することもできる。

While the general ruleset may not be perfect for most projects, the best feature of CSS Lint is its ability to be customized exactly how you want it. This means you can pick and choose the rules you want from their default list as well as write your own.
CSS Lint全般のルールセットは、多くのプロジェクトにおいておおよそ完璧に当てはまるものではないものの、CSS Lintの最も特徴的といえるのは、欲しいルールで厳密にカスタマイズできる能力にある。これはデフォルトで用意されているルールから欲しいルールを選別できるも、自身でルールを書けることを意味する

A tool like CSS Lint is essential for any large team to ensure at least a baseline of consistency and convention compliance. And like I hinted at previously, one of the great reasons for conventions is they allow for tools like CSS Lint to easily identify anything that breaks them.
CSS Lintのようなツールは、いくらか大きなチームにおいて、少なくとも一貫性、規則の順守の基準を保証するために欠かせない。そして私が先にヒントを出したように、規則を持つことの大きな理由のひとつは、規則を壊す何かの特定を容易にするためのCSS Lintのようなツールを考慮することだ。

Based on the conventions I’ve proposed above, it becomes very easy to write rules to detect particular antipatterns. Here are a few suggestions that I use:
先に提案したように、規則に基づくことによって、特定のアンチパターンを調べるためのルールを書くことを非常に容易になる。

- Don’t allow IDs in your selectors.
- IDセレクタを許可しない
- Don’t use non-semantic type selectors (e.g. DIV, SPAN) in any multi-part rule.
- `div`、`span`といった意味を持たないセレクタを複数のルール上で使わない
- Don’t use more than 2 combinators in a selector.
- 2つ以上の結合子をセレクタで使わない
- Don’t allow any class names that begin with “js-”.
- `js-`から始めるクラス名にすることを許可しない
- Warn if frequently using layout and positioning for non “l-” prefixed rules.
- `l-`接頭辞がついていないルールでレイアウト・配置のスタイルが頻繁に使われるようであれば注意する
- Warn if a class defined by itself is later redefined as a child of something else.
- 自身によって定義されたクラスが、その他の何かの子供として後に再定義されるようになったら注意する

These are obviously just suggestions, but they’re intended to get you thinking about how to enforce the standards you want on your projects.
これらはただの提案であることは明白だが、自身のプロジェクトで、あなたが望む標準化をどのように強制するか、を考えるのを目的としている。

#### HTML INSPECTOR 
#### HTML Inspector

Earlier I suggested that it would be easy to search your HTML classes and all linked stylesheets and warn if a class was used in the HTML but not defined in any stylesheet. I’m currently developing a tool called the [HTML Inspector](https://github.com/philipwalton/html-inspector) to make this process easier.
前に私が提案したHTML上のクラスと、リンクしたすべてのスタイルシートを容易に調査できる方法で、もし、どのスタイルシートで定義されてされていないのに、HTML上で使われているクラスがあれば注意しよう。現在私が開発している[HTML Inspector](https://github.com/philipwalton/html-inspector) と呼ばれるツールは、このプロセスをより簡単にしてくれる。

HTML Inspector traverses your HTML and (much like CSS Lint) allows you to write your own rules that throw errors and warnings when some convention is being broken. I currently use the following rules:
HTML Inspectorは（CSS Lintのように）HTMLを横断し、自身で書いたルールで、規則が守られていない時にエラーと注意を返してくれる。

- Warn if the same ID is used more than once on a page.
- 同じIDをページ上で1つ以上使われていれば注意する
- Don’t use any classes that aren’t mentioned in any stylesheet or pass a whitelist (like “js-” prefixed classes).
- どのスタイルシートでも使われていなかったり、（接頭辞に`js-`がある、というような）ホワイトリストをパスしていないクラスは使わない
- Modifer classes shouldn’t be used without their base class.
- 修飾子クラスはベースクラス無しで使われるべきではない
- Sub-object classes shouldn’t be used when no ancestor contains the base class.
- サブオブジェクトクラスは祖先にベースクラスを含んでいないときに使われるべきではない
- Plain old DIV or SPAN elements, without classes attached, should not be used in the HTML.
- クラスが適用されていない、プレーンな`div`や`span`要素はHTML上で使ってはいけない

### Summary

CSS isn’t just visual design. Don’t throw out programming best practices just because you’re writing CSS. Concepts like OOP, DRY, the open/closed principle, separation of concerns, etc. still apply to CSS.
CSSは単純にビジュアルデザインをつくるためのものではない。CSSを書いているというだけで、プログラミングのベストプラクティスをないがしろにしてはいけない。それは、OOP（オブジェクト指向プログラミング）、DRY（Don't Repeat Yourself）、開放/閉鎖原則、関心の分離、などの概念だ。これらはCSSにも適用できる余地がある。

The bottom line is that whatever you do to organize your code, make sure you judge your methods by whether or not they actually help make your development easier and more maintainable in the long term.
要するに、どんなにコードをうまくまとめるために、
その手法が正確に開発をより容易にすることを手助けしたり、長期間に渡ってより保守しやすいかどうかで判断するということを確かめてほしい。