Flexbugs
========

This repository is a community-curated list of flexbox issues and cross-browser workarounds for them. The goal is that if you're building a website using flexbox and something isn't working as you'd expect, you can find the solution here.

As the spec continues to evolve and vendors nail down their implementations, this repo will be updated with newly discovered issues and remove old issues as they're fixed or become obsolete. If you discover a bug that's not listed here, please [report it](#contributing) so everyone else can benefit.

## The bugs and their workarounds

1. [flexアイテムの最小コンテンツサイズが順守されない](#1-minimum-content-sizing-of-flex-items-not-honored)
2. [ `align-items:center` に設定された縦並びのflexアイテムが、親コンテナからはみ出してしまう ](#2-column-flex-items-set-to-align-itemscenter-overflow-their-container)
3. [flexコンテナの `min-height` が内包するflexアイテムに適用されない](#3-min-height-on-a-flex-container-wont-apply-to-its-flex-items)
4. [省略記法の`flex` 定義で、`flex-basis` に単位のない値を指定すると無視される](#4-flex-shorthand-declarations-with-unitless-flex-basis-values-are-ignored)
5. [縦並びのflexアイテムが、本来のアスペクト比を保持してくれない](#5-column-flex-items-dont-always-preserve-intrinsic-aspect-ratios)
6. [`flex` のデフォルト値が仕様と違う](#6-the-default-flex-value-has-changed)
7. [`flex-basis` の計算が `box-sizing:border-box` を考慮していない](#7-flex-basis-doesnt-account-for-box-sizingborder-box)
8. [`flex-basis` が `calc()` をサポートしていない](#8-flex-basis-doesnt-support-calc)
9. [幾つかの HTML 要素は flex コンテナになれない](#9-some-html-elements-cant-be-flex-containers)
10. [ネストされたflexコンテナで `align-items: baseline` が効かない](#10-align-items-baseline-doesnt-work-with-nested-flex-containers)
11. [flexアイテムを折り返す場合に、最小・最大サイズの定義が無視される](#11-min-and-max-size-declarations-are-ignored-when-wrapping-flex-items)
12. [インライン要素がflexアイテムとして扱われない](#12-inline-elements-are-not-treated-as-flex-items)
13. [省略記法のflexを使った時、flex-basisでimportant指定が無視される](#13-importance-is-ignored-on-flex-basis-when-using-flex-shorthand)
14. [Flex containers with wrapping the container is not sized to contain its items](#14-flex-containers-with-wrapping-the-container-is-not-sized-to-contain-its-items)

### 1. Minimum content sizing of flex items not honored

flexアイテムの最小コンテンツサイズが順守されない

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
    <th align="left">バグトラッカ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/MYbrrr">1.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/ByQJOQ">1.1.b</a> &mdash; <em>workaround</em><br>
      <a href="http://codepen.io/philipwalton/pen/ByQJqQ">1.2.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/wBopYg">1.2.b</a> &mdash; <em>workaround</em>
    </td>
    <td>
      Chrome (fixed in 44)<br>
      Opera (fixed in 31)<br>
      Safari
    </td>
    <td>
      <a href="https://code.google.com/p/chromium/issues/detail?id=426898">Chrome #426898</a><br>
      <a href="https://bugs.webkit.org/show_bug.cgi?id=146020">Safari #146020</a>
    </td>
  </tr>
</table>

Flexアイテムがコンテナ内に収まりきらない場合、それらのアイテムは`flex-shrink`プロパティに基づいてプロポーショナルに縮小されます。ほとんどのブラウザで許されているものとは逆に、未指定の際に縮小することにはなっていません。少なくとも指定されているminimum heightまたはwidthと同じ大きさであるべきですし、minimum height または widthプロパティが設定されていない場合でも、最小サイズはコンテンツのデフォルト最小サイズとなるべきです。

[現行のflexboxの仕様](http://www.w3.org/TR/css-flexbox/#flex-common)によると:

> デフォルトで、flexアイテムは最小コンテンツサイズ（最長の単語の長さまたは固定サイズ要素）に応じて縮小されません。これを変更するには、min-width または min-heightプロパティを設定して下さい。

#### Workaround

仕様では、`flex-shrink` の初期値は `1` であると定めていますが、それはコンテンツのデフォルト最小サイズにしたがって縮小すべきではないとしています。代わりに`flex-shrink` を `0`に設定し、`flex-basis` を `auto`に設定することで、常に同じ振る舞いにすることができます。これにより、flexアイテムのサイズは、（指定されていれば）その高さと幅になるか、デフォルトコンテンツサイズと同等の大きさになります。

### 2. Column flex items set to `align-items:center` overflow their container

`align-items:center` に設定された縦並びのflexアイテムが、親コンテナからはみ出してしまう

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/xbRpMe">2.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/ogYpVv">2.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>
      Internet Explorer 10-11 (fixed in Edge)
    </td>
  </tr>
</table>

column directionのflexコンテナ上で `align-items:center` を使用し、flexアイテムのコンテンツが大きすぎる場合、IE10-11でコンテナをはみ出します。

#### Workaround

ほとんどの場合、flexアイテムに`max-width:100%`を指定するだけで修正できます。flexアイテムにpaddingまたはborderが設定されている場合、 `box-sizing:border-box` を利用しているか確認する必要があります。flexアイテムにマージンがある場合、`box-sizing`だけでは効果がありません。代わりにコンテナ要素にpaddingを設定してもよいでしょう。

### 3. `min-height` on a flex container won't apply to its flex items

flexコンテナの `min-height` が内包するflexアイテムに適用されない

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
    <th align="left">バグトラッカ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/RNoZJP">3.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/KwNvLo">3.1.b</a> &mdash; <em>workaround</em><br>
      <a href="http://codepen.io/philipwalton/pen/VYmbmj">3.2.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/JdvdJE">3.2.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10-11 (fixed in Edge)</td>
    <td><a href="https://connect.microsoft.com/IE/feedback/details/802625/min-height-and-flexbox-flex-direction-column-dont-work-together-in-ie-10-11-preview">IE #802625</a></td>
  </tr>
</table>

flexアイテムのサイズと位置を決定するためには、それらのコンテナの大きさを知っている必要があります。例えば、flexアイテムが縦中央に位置するとき、親の高さを知る必要があります。flexアイテムが残りの空間を埋めるようにgrowする場合にも同じことがいえます。

In IE 10-11, `min-height` declarations on flex containers work to size the containers themselves, but their flex item children do not seem to know the size of their parents. They act as if no height has been set at all.

#### Workaround

By far the most common element to apply `min-height` to is the body element, and usually you're setting it to `100%` (or `100vh`). Since the body element will never have any content below it, and since having a vertical scroll bar appear when there's a lot of content on the page is usually the desired behavior, substituting `height` for `min-height` will almost always work as shown in demo [3.1.b](http://codepen.io/philipwalton/pen/KwNvLo).

For cases where `min-height` is required, the workaround is to add a wrapper element around the flex container that is itself a flex container in the column direction. For some reason nested flex containers are not affected by this bug. Demo [3.2.a](http://codepen.io/philipwalton/pen/VYmbmj) shows a visual design where `min-height` is required, and demo [3.2.b](http://codepen.io/philipwalton/pen/JdvdJE) shows how this bug can be avoided with a wrapper element.

### 4. `flex` shorthand declarations with unitless `flex-basis` values are ignored

//優先

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/OPbQgO">4.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/ByQYZJ">4.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10-11 (fixed in Edge)</td>
  </tr>
</table>

Prior to the release of IE 10, the [flexbox spec at the time](http://www.w3.org/TR/2012/WD-css3-flexbox-20120322/#flexibility) stated that a flexbox item's preferred size required a unit when using the `flex` shorthand:

>  If the &lt;preferred-size&gt; is ‘0’, it must be specified with a unit (like ‘0px’) to avoid ambiguity; unitless zero will either be interpreted as as one of the flexibilities, or is a syntax error.

This is no longer true in the spec, but IE 10-11 still treat it as true. If you use the declaration `flex: 1 0 0` in one of these browsers, it will be an error and the entire rule (including all the flexibility properties) will be ignored.

#### Workaround

When using the `flex` shorthand, always include a unit in the `flex-basis` portion. For example: `1 0 0%`.

**Important:** using a `flex` value of something like `1 0 0px` can still be a problem because many CSS minifiers will convert `0px` to `0`. To avoid this, make sure to use `0%` instead of `0px` since most minifiers won't touch percentage values for other reasons.

### 5. Column flex items don't always preserve intrinsic aspect ratios

//優先

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/LEbQON">5.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/wBoyry">5.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10-11 (fixed in Edge)</td>
  </tr>
</table>

The [March 2014 spec](http://www.w3.org/TR/2014/WD-css-flexbox-1-20140325/#min-size-auto) has the following to say about how size determinations are made for flex items:

> On a flex item whose overflow is not visible, this [auto] keyword specifies as the minimum size the smaller of: (a) the min-content size, or (b) the computed width/height, if that value is definite.

Demo [5.1.a](http://codepen.io/philipwalton/pen/LEbQON) contains an image whose height is 200 pixels and whose width is 500 pixels. Its container, however, is only 300 pixels wide, so after the image is scaled to fit into that space, its computed height should only be 120 pixels. The text quoted above does not make it clear as to whether the flex item's min-content size should be based the image's actual height or scaled height.

The [most recent spec](http://dev.w3.org/csswg/css-flexbox/#min-size-auto) has resolved this ambiguity in favor of using sizes that will preserve an element's intrinsic aspect ratio.

#### Workaround

You can avoid this problem by adding a container element to house the element with the intrinsic aspect ratio. Since doing this causes the element with the intrinsic aspect ratio to no longer be a flex item, it will be sized normally.

### 6. The default `flex` value has changed

//あとで（IE10のみ）

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/myOYqW">6.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/azBrLo">6.1.b</a> &mdash; <em>workaround</em><br>
      <a href="http://codepen.io/philipwalton/pen/zvvQdB">6.2.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/LppoOj">6.2.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10 (fixed in 11)</td>
  </tr>
</table>

When IE 10 was being developed, the [March 2012 spec](http://www.w3.org/TR/2012/WD-css3-flexbox-20120322/#flexibility) said the initial value for the `flex` property was `none`, which translates to `0 0 auto`. The [most recent spec](http://www.w3.org/TR/css3-flexbox/#flex-property) sets the initial `flex` value to the initial values of the individual flexibility properties, which corresponds to `initial` or `0 1 auto`. Notice that this means IE 10 uses a different initial `flex-shrink` value (technically it was called `neg-flex` in the spec at the time) from every other browser. Other browsers (including IE 11) use an initial value of `1` rather than `0`.

This bug can manifest itself in two ways: when not setting any flex values or when using one of the `flex` shorthands. In both cases, flex items in IE 10 will behave differently from all other browsers. The following table illustrates the difference:

<table>
  <tr>
    <th align="left">Declaration</th>
    <th align="left">What it should mean</th>
    <th align="left">What it means in IE 10</th>
  </tr>
  <tr>
    <td>(no flex declaration)</td>
    <td><code>flex: 0 1 auto</code></td>
    <td><code>flex: 0 0 auto</code></td>
  </tr>
  <tr>
    <td><code>flex: 1</code></td>
    <td><code>flex: 1 1 0%</code></td>
    <td><code>flex: 1 0 0px</code></td>
  </tr>
  <tr>
    <td><code>flex: auto</code></td>
    <td><code>flex: 1 1 auto</code></td>
    <td><code>flex: 1 0 auto</code></td>
  </tr>
  <tr>
    <td><code>flex: initial</code></td>
    <td><code>flex: 0 1 auto</code></td>
    <td><code>flex: 0 0 auto</code></td>
  </tr>
</table>

#### Workaround

If you have to support IE 10, the best solution is to *always* set an explicit `flex-shrink` value on all of your flex items, or to always use the longhand form (rather than the shorthand) in `flex` declarations to avoid the gotchas shown in the table above. Demo [6.1.a](http://codepen.io/philipwalton/pen/myOYqW) shows how not setting any flexibility properties causes an error, and demo [6.2.a](http://codepen.io/philipwalton/pen/zvvQdB) shows how using `flex: 1` can have the same problem.

### 7. `flex-basis` doesn't account for `box-sizing:border-box`

//優先

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/JoWjyb">7.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/XJMWem">7.1.b</a> &mdash; <em>workaround</em><br>
      <a href="http://codepen.io/philipwalton/pen/ZYLdqb">7.1.c</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10-11 (fixed in Edge)</td>
  </tr>
</table>

An explicit `flex-basis` value (i.e., any value other than `auto`) is supposed to act just like `width` or `height`. It determines the initial size of a flex item and then the other flexibility properties allow it to grow or shrink accordingly.

IE 10-11 always assume a content box model when using `flex-basis` to determine a flex item's size, even if that item is set to `box-sizing:border-box`. Demo [7.1.a](http://codepen.io/philipwalton/pen/JoWjyb) shows that an item with a `flex-basis` value of `100%` will overflow its container by the amount of its border plus its padding.

#### Workaround

There are two ways to work around this bug. The first requires no additional markup, but the second is slightly more flexible:

1. Instead of setting an explicit `flex-basis` value, use `auto`, and then set an explicit width or height. Demo [7.1.b](http://codepen.io/philipwalton/pen/XJMWem) shows this.
2. Use a wrapper element that contains no border or padding so it works with the content box model. Demo [7.1.c](http://codepen.io/philipwalton/pen/ZYLdqb) show this.

### 8. `flex-basis` doesn't support `calc()`

//優先

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/ogBrye">8.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/bNgPKz">8.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10-11 (fixed in Edge)</td>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/VYJgJo">8.2.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/pvXGmW">8.2.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10 (fixed in 11)</td>
  </tr>
</table>

IE 10-11 ignore `calc()` functions used in `flex` shorthand declarations. Demo [8.1.a](http://codepen.io/philipwalton/pen/ogBrye) shows `flex:0 0 calc(100%/3)` not working in IE.

In IE 10, `calc()` functions don't even work in longhand `flex-basis` declarations (though this does work in IE 11). Demo [8.2.a](http://codepen.io/philipwalton/pen/VYJgJo) shows `flex-basis: calc(100%/3)` not working in IE 10.

#### Workaround

Since this bug only affects the `flex` shorthand declaration in IE 11, an easy workaround (if you only need to support IE 11) is to always specify each flexibility property individually. Demo [8.1.b](http://codepen.io/philipwalton/pen/bNgPKz) offers an example of this.

If you need to support IE 10 as well, then you'll need to fall back to setting `width` or `height` (depending on the container's `flex-direction` property). You can do this by setting a `flex-basis` value of `auto`, which will instruct the browser to use the element's [main size](http://dev.w3.org/csswg/css-flexbox/#box-model) property (i.e., its `width` or `height`). Demo [8.2.b](http://codepen.io/philipwalton/pen/pvXGmW) offers an example of this.

### 9. Some HTML elements can't be flex containers

//優先

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
    <th align="left">バグトラッカ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/ByZgpW">9.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/mywZpr">9.1.b</a> &mdash; <em>workaround</em><br>
      <a href="http://codepen.io/philipwalton/pen/wKBqdY">9.2.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/EVaRaX">9.2.b</a> &mdash; <em>workaround</em>
    </td>
    <td>
      Chrome (fixed in 54)<br>
      Edge<br>
      Firefox<br>
      Opera<br>
      Safari
    </td>
    <td>
      <a href="https://code.google.com/p/chromium/issues/detail?id=262679">Chrome #262679 (fixed)</a><br>
      <a href="https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/4511145/">Edge #4511145</a><br>
      <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=984869">Firefox #984869</a><br>
      <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1230207">Firefox #1230207 (fixed)</a><br>
      <a href="https://bugs.webkit.org/show_bug.cgi?id=148826">Safari #148826</a>
    </td>
  </tr>
</table>

Certain HTML elements, like `<fieldset>` and `<button>`, do not work as flex containers. The browsers default rendering of those element's UI conflicts with the `display: flex` declaration.

Demo [9.1.a](http://codepen.io/philipwalton/pen/ByZgpW) shows how `<button>` elements don't work in Firefox, and demo [9.2.a](http://codepen.io/philipwalton/pen/wKBqdY) shows that `<fieldset>` elements don't work in most browsers.

#### Workaround

The simple solution to this problem is to use a wrapper element that can be a flex container (like a `<div>`) directly inside of the element that can't. DEMO [9.1.b](http://codepen.io/philipwalton/pen/mywZpr) and [9.2.b](http://codepen.io/philipwalton/pen/EVaRaX) show workaround for the `<button>` and `<fieldset>` elements, respectively.

### 10. `align-items: baseline` doesn't work with nested flex containers

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
    <th align="left">バグトラッカ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/vOOejZ">10.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/MwwEVd">10.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>
      Firefox
    </td>
    <td>
      <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1146442">Firefox #1146442</a>
    </td>
  </tr>
</table>

In Firefox, nested flex containers don't contribute to the baseline that other flex items should align themselves to. Demo [10.1.a](http://codepen.io/philipwalton/pen/vOOejZ) shows the line on the left incorrectly aligning itself to the second line of text on the right. It should be aligned to the first line of text, which is the inner flex container.

#### Workaround

This bug only affects nested containers set to `display: flex`. If you set the nested container to `display: inline-flex` it works as expected. Note that when using `inline-flex` you will probably also need to set the width to `100%`.


### 11. Min and max size declarations are ignored when wrapping flex items

//優先

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
    <th align="left">バグトラッカ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/anon/pen/BNrGwN">11.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/anon/pen/RPMqjz">11.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Safari</td>
    <td>
      <a href="https://bugs.webkit.org/show_bug.cgi?id=136041">Safari #136041</a>
    </td>
  </tr>
</table>

Safari uses min/max width/height declarations for actually rendering the size of flex items, but it ignores those values when calculating how many items should be on a single line of a multi-line flex container. Instead, it simply uses the item's `flex-basis` value, or its width if the flex basis is set to `auto`.

This is problematic when using the `flex: 1` shorthand because that sets the flex basis to `0%`, and an infinite number of flex items could fit on a single line if the browser thinks their widths are all zero. Demo [11.1.a](http://codepen.io/philipwalton/pen/BNrGwN) show an example of this happening.

This is also problematic when creating fluid layouts where you want your flex items to be no bigger than X but no smaller than Y. Since Safari ignores those values when determining how many items fit on a line, that strategy won't work.

#### Workaround

The only way to avoid this issue is to make sure to set the flex basis to a value that is always going to be between (inclusively) the min and max size declarations. If using either a min or a max size declaration, set the flex basis to whatever that value is, if you're using both a min *and* a max size declaration, set the flex basis to a value that is somewhere in that range. This sometimes requires using percentage values or media queries to cover all possible scenarios. Demo [11.1.b](http://codepen.io/philipwalton/pen/RPMqjz) shows an example of setting the flex basis to the same value as the min width to workaround this bug in Safari.

### 12. Inline elements are not treated as flex-items

//優先

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/qdMgdg">12.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/NqLoNp">12.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10-11 (fixed in Edge)</td>
  </tr>
</table>

Inline elements, including `::before` and `::after` pseudo-elements, are not treated as flex items in IE 10. IE 11 fixed this bug with regular inline element, but it still affects the `::before` and `::after` pseudo-elements.

#### Workaround

This issue can be avoided by adding a non-inline display value to the items, e.g. `block`, `inline-block`, `flex`, etc. Demo [12.1.b](http://codepen.io/philipwalton/pen/NqLoNp) shows an example of this working in IE 10-11.

### 13. Importance is ignored on flex-basis when using flex shorthand

//あとで（IE10のみ）

<table>
  <tr>
    <th align="left">デモ</th>
    <th align="left">影響を受けるブラウザ</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/ZbRoYw">13.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/rOKvNb">13.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10 (fixed in 11)</td>
  </tr>
</table>

When applying `!important` to a `flex` shorthand declaration, IE 10 applies `!important` to the `flex-grow` and `flex-shrink` parts but not to the `flex-basis` part. Demo [13.1.a](http://codepen.io/philipwalton/pen/ZbRoYw) shows an example of a declaration with `!important` not overriding another declaration in IE 10.

#### Workaround

If you need the `flex-basis` part of your `flex` declaration to be `!important` and you have to support IE 10, make sure to include a `flex-basis` declaration separately. Demo [13.1.b](http://codepen.io/philipwalton/pen/rOKvNb) shows an example of this working in IE 10.


### 14. Flex containers with wrapping the container is not sized to contain its items

<table>
  <tr>
    <th align="left">Demos</th>
    <th align="left">Browsers affected</th>
    <th align="left">Tracking Bugs</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/gregwhitworth/pen/LNvpea">14.1</a> &mdash; <em>bug</em><br>
    </td>
    <td>
        Chrome<br>
        Firefox<br>
        Safari<br>
        Opera<br>
    </td>
    <td>
        <a href="https://bugs.chromium.org/p/chromium/issues/detail?id=507397">Chrome #507397</a><br>
        <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=995020">Firefox #995020</a><br>
        <a href="https://bugs.webkit.org/show_bug.cgi?id=157648">Safari #157648</a>
    </td>
  </tr>
</table>

When you make a flex container a float or to be absolutely positioned the
dimensions of the container are determined by its contents if they are not
explicitly set (aka: shink-to-fit). To determine these dimensions accurately
you need to know the dimensions from layout which the heuristics they're
using to bypass this layout pass produces the incorrect result, thus the container
cannot encompass its items correctly.

#### Workaround

In order to workaround this you need to provide the explicit number of rows and columns
for the flex container to allow the container to size around them. Unfortionately, this
results in removing the responsiveness. Here is the same example [using the workaround](http://codepen.io/gregwhitworth/pen/yOrYEp).

## Acknowledgments

Flexbugs was created as a follow-up to the article [Normalizing Cross-Browser Flexbox Bugs](http://philipwalton.com/articles/normalizing-cross-browser-flexbox-bugs/). It's maintained by [@philwalton](https://twitter.com/philwalton) and [@gregwhitworth](https://twitter.com/gregwhitworth). If you have any questions or would like to get involved, please feel free to reach out to either of us on Twitter.

## Contributing

If you've discovered a flexbox bug and would like to submit a workaround for it, please open an issue or submit a pull request. Make sure to submit relevant test cases or screenshots and indicate which browsers are affected.

Please only submit bugs if they have a viable workaround and the workaround applies to most use cases. If you do not know of a workaround, but you're reasonably confident one exists, please indicate that in the issue and the community can help investigate.

**Note: Do not submit bugs here in lieu of reporting them to browser vendors. [Reporting bugs to browser vendors](https://www.smashingmagazine.com/2011/09/help-the-community-report-browser-bugs/) is the best and fastest way to get bugs fixed.**
