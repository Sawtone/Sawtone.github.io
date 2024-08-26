---
layout: post
title: 一次心血来潮的举动——修改博客 JavaScript 处理逻辑
categories: [JavaScript, HTML]
description: JavaScript, HTML
keywords: JavaScript, HTML
---

# 随着学习逐渐深入，逐渐从刻意逃避一些问题，到找原主提 Issues 交流，到现在自己尝试修改，受益匪浅


## JavaScript 生成目录逻辑与超链接属性冲突

### 背景

写 <a href="https://sawtone.github.io/2024/08/23/RYF-JavaScript-ECMAScript6-Study/" target="_blank">阮一峰 JavaScript & ECMAScript6 学习笔记与思考</a> 这篇博客时

我使用内嵌超链接的方式，以章节为分界，为每个小节设置了一个带超链接的三级标题

```markdown
// 例子
### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-basic-introduction.md" target="_blank">Part 1-1 导论</a>
```

这样，翻阅与复习读书笔记感悟的时候，就可以随时返回原书位置查看原处

初衷很好，实践中出现了新的问题

### 问题所在

仔细阅读与分析源码后，我发现：

页面内跳转实际上是为各级标题添加锚点

而我在标题内添加的超链接与添加的锚点发生冲突，导致用户点击时会优先执行标题自带的超链接跳转到外部网站，将原本的锚点跳转覆盖掉

锚点能让我更好地寻找相关笔记，但超链接也能让我随时回看书籍原文，两者缺一不可，我不得不解决这一问题

对于这一问题，我的个人猜测是：博客生成目录时直接捕捉了标题本身，而不管标题是什么，这就导致标题本身的“超链接属性”被一同捕捉

浅析问题，我需要知道的有：

- 添加锚点的逻辑是怎样的？
- 超链接的优先级为何高于锚点？
- 避免这种情况的方法有哪些？哪种最优？

### 添加锚点的逻辑

#### Section 1
```JavaScript
// Section 1
(function($){
  $.fn.toc = function(options) {
    var defaults = {
      noBackToTopLinks: false,
      title: '目录',
      minimumHeaders: 2,
      headers: 'h1, h2, h3, h4, h5, h6',
      listType: 'ul', // values: [ol|ul]
      showEffect: 'show', // values: [show|slideDown|fadeIn|none]
      showSpeed: 0 // set to 0 to deactivate effect
    },
    settings = $.extend(defaults, options);
```
Section 1 设置了一个合并的对象 settings，用来控制目录生成配置

`$.extend(defaults, options)` 将用户设置 options 与 默认配置 defaults 合并成对象 settings

#### Section 2

```JavaScript
// Section 2
var headers = $(settings.headers).filter(function() {
      if ($(this).parent('blockquote').length > 0) {
        return false;
      }
```

Section 2 设置了 headers，它是通过 `$(settings.headers)` 得到的 jQuery 对象集合

##### 浅析 jQuery 对象

jQuery 对象出自 jQuery 库，它通过 jQuery 选择器或工具函数返回

jQuery 对象封装了一个或多个 DOM 元素，并为这些元素提供了一套统一的方法接口，方便进行操作和处理

jQuery 对象与 DOM 对象可以相互转换：

```JavaScript
// JQuery -> DOM
var domElement = $("#myElement")[0];

// DOM -> JQuery
var domElement = document.getElementById("name");
var jqueryElement = $(domElement);
```

#### Section 3

```JavaScript
.each(function(_, header) {
      this_level = get_level(header);
      if (!settings.noBackToTopLinks && this_level === highest_level) {
        $(header).addClass('top-level-header').after(return_to_top);
      }
      if (this_level === level) {// same level as before; same indenting
        html += "<li class=\"toc-item toc-level-" + this_level + "\">";
        html += "<a class=\"jumper\" href='#" + fixedEncodeURIComponent(header.id) + "'>";
        html += "<span class='toc-text'>" + header.innerHTML + "</span>";
        html += "</a>";

      } else if (this_level <= level){ // higher level than before; end parent ol
        for(i = this_level; i < level; i++) {
          html += "</li></"+settings.listType+">"
        }
        html += "<li class='toc-item toc-level-" + this_level + "'><a class=\"jumper\" href='#" + fixedEncodeURIComponent(header.id) + "'>";
        html += "<span class='toc-text'>" + header.innerHTML + "</span>";
        html += "</a>";
      }
      else if (this_level > level) { // lower level than before; expand the previous to contain a ol
        for(i = this_level; i > level; i--) {
          html += "<"+settings.listType+" class='toc-child'><li class='toc-item toc-level-" + i + "'>"
        }
        html += "<a class=\"jumper\" href='#" + fixedEncodeURIComponent(header.id) + "'>";
        html += "<span class='toc-text'>" + header.innerHTML + "</span>";
        html += "</a>";
      }
      level = this_level; // update for the next one
    });
```
Section 3 是具体的目录生成操作

首先，通过遍历 headers 进一步得到 header，header 是单个的 DOM 元素，包含某个符合选择器的标题元素

随后，通过 `this_level = get_level(header); ` 获取标题层级，进一步通过若干次字符串相加生成新的锚点标题


### 超链接优先级为何高于锚点

```
// 以下方三级标题为例
// ### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-string.md" target="_blank">Part 2-4 字符串与字符集</a>

/*
经过 each 操作后，将得到如下代码(近似)：
<li class="toc-item toc-level-3">
    <a class="jumper" href='#example-id'>
        <span class='toc-text'><a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-string.md" target="_blank">Part 2-4 字符串与字符集</a></span>
    </a>
</li>
*/
```

不难看到，同一个标题内部有两层 `<a>` 标签

外层 `jumper` 标签实现锚点跳转，内层 `toc-text` 标签中包含原始标题的超链接

由于 HTML 标准规定，点击事件会优先触发内层的 `<a>` 标签的跳转行为

所以点击目录内三级标题会优先跳转超链接，覆盖锚点的跳转功能

而如果标题只是正常的文字的话，内层标签就不会存在超链接跳转，锚点便可以正常使用

### 如何解决问题

#### 刻意回避，直接打补丁

一种简单而有效的办法，把超链接设置在三级标题附近，避开其本身

```
### Part 2-4 字符串与字符集
<a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-string.md" target="_blank">[点击此处跳转原书]</a>

网页将显示：
Part 2-4 字符串与字符集
[点击此处跳转原书]
```

或者单独设置锚点，放置在标题右侧

这样就可以点击目录里标题旁边的锚点跳转，超链接依旧保留在目录里

```
### Part 2-4 字符串与字符集 <a href="https://sawtone.github.io/2024/08/23/RYF-JavaScript-ECMAScript6-Study/#part-2-4-字符串与字符集" target="_blank">(#part-2-4)</a>

网页将显示：
Part 2-4 字符串与字符集 (#part-2-4)
```

但此时我想了解与分析一下 JavaScript 处理逻辑，故不采用这种打补丁式的方法

#### 设置判断与处理

在原代码的基础上设置判断条件，若存在外部链接，则获取内部的纯文本标题，然后正常处理

这种方法很不错，根本上解决了这一问题

但考虑到 JavaScript 的灵活性，是否有更简单的办法解决这一问题呢

```JavaScript
.each(function(_, header) {
      var $header = $(header);
      this_level = get_level(header);
      var externalLink = $header.find('a[href^="http"]'); // 查找标题中是否有外部链接
      
      if (externalLink.length > 0) {
        // 如果存在外部链接，将外部链接移除并获取纯文本内容
        var headerText = $header.text(); // 获取标题的纯文本内容
        html += "<li class='toc-item toc-level-" + this_level + "'>";
        html += "<a class='jumper' href='#" + fixedEncodeURIComponent(header.id) + "'>";
        html += "<span class='toc-text'>" + headerText + "</span>"; // 使用纯文本生成目录项
        html += "</a></li>";
      } else {
        // 如果没有外部链接，正常生成目录项
        html += "<li class='toc-item toc-level-" + this_level + "'>";
        html += "<a class='jumper' href='#" + fixedEncodeURIComponent(header.id) + "'>";
        html += "<span class='toc-text'>" + header.innerHTML + "</span>";
        html += "</a></li>";
      }

      // ......
}
```

#### 巧用 DOM 元素常用属性

仔细分析源码，遍历操作里调用的 DOM 属性似乎有点说法

```JavaScript
 if (this_level === level) {// same level as before; same indenting
        html += "<li class=\"toc-item toc-level-" + this_level + "\">";
        html += "<a class=\"jumper\" href='#" + fixedEncodeURIComponent(header.id) + "'>";
        html += "<span class='toc-text'>" + header.innerHTML + "</span>";
        html += "</a>";
      }
```

对于 `html += "<span class='toc-text'>" + header.innerHTML + "</span>";` 

这里调用了 `header.innerHTML` 来嵌入原标题内容，但 `innerHTML` 在引入原标题的同时也引入了 `<a>` 超链接，问题的关键似乎变得很简单

`header.textContent`: 获取元素的纯文本内容，不包含任何 HTML 标签

修改源代码中三处 `header.innerHTML` 为 `header.textContent`，问题解决

### 后记

此时的我还没有学到 DOM 这一特殊对象，通过分析与思考，得以提前接触了 DOM 的操作，受益匪浅

继续写阮一峰 JS 学习笔记去咯(

#### 人生第一次 pr

https://github.com/mzlogin/mzlogin.github.io/pull/181

泪目~

<img src="/images/blog/mzpr.png" alt="mzlogin" style="width:800px; box-shadow: 20px 20px 35px rgba(0,0,0,0.3);" />


### 附录：相关源码(修改前)

```JavaScript
(function($){
  $.fn.toc = function(options) {
    var defaults = {
      noBackToTopLinks: false,
      title: '目录',
      minimumHeaders: 2,
      headers: 'h1, h2, h3, h4, h5, h6',
      listType: 'ul', // values: [ol|ul]
      showEffect: 'show', // values: [show|slideDown|fadeIn|none]
      showSpeed: 0 // set to 0 to deactivate effect
    },
    settings = $.extend(defaults, options);

    function fixedEncodeURIComponent (str) {
      return encodeURIComponent(str).replace(/[!'()*]/g, function(c) {
        return '%' + c.charCodeAt(0).toString(16);
      });
    }

    var headers = $(settings.headers).filter(function() {
      if ($(this).parent('blockquote').length > 0) {
        return false;
      }
      // get all headers with an ID
      var previousSiblingName = $(this).prev().attr( "name" );
      if (!this.id && previousSiblingName) {
        this.id = $(this).attr( "id", previousSiblingName.replace(/\./g, "-") );
      }
      return this.id;
    }), output = $(this);
    if (!headers.length || headers.length < settings.minimumHeaders || !output.length) {
      $(this).hide();
      $('.post-directory-title').css('display', 'none');
      return;
    }

    if (0 === settings.showSpeed) {
      settings.showEffect = 'none';
    }

    var render = {
      show: function() { output.hide().html(html).show(settings.showSpeed); },
      slideDown: function() { output.hide().html(html).slideDown(settings.showSpeed); },
      fadeIn: function() { output.hide().html(html).fadeIn(settings.showSpeed); },
      none: function() { output.html(html); }
    };

    var get_level = function(ele) { return parseInt(ele.nodeName.replace("H", ""), 10); }
    var highest_level = headers.map(function(_, ele) { return get_level(ele); }).get().sort()[0];
    var return_to_top = '<i class="icon-arrow-up back-to-top"> </i>';

    var level = get_level(headers[0]),
    this_level,
    html = "<p><strong class=\"toc-title\">" + settings.title + "</strong></p>\n";
    html += " <"+settings.listType+" class=\"toc\">";
    headers.on('click', function() {
      if (!settings.noBackToTopLinks) {
        window.location.hash = this.id;
      }
    })
    .addClass('clickable-header')
    .each(function(_, header) {
      this_level = get_level(header);
      if (!settings.noBackToTopLinks && this_level === highest_level) {
        $(header).addClass('top-level-header').after(return_to_top);
      }
      if (this_level === level) {// same level as before; same indenting
        html += "<li class=\"toc-item toc-level-" + this_level + "\">";
        html += "<a class=\"jumper\" href='#" + fixedEncodeURIComponent(header.id) + "'>";
        html += "<span class='toc-text'>" + header.innerHTML + "</span>";
        html += "</a>";

      } else if (this_level <= level){ // higher level than before; end parent ol
        for(i = this_level; i < level; i++) {
          html += "</li></"+settings.listType+">"
        }
        html += "<li class='toc-item toc-level-" + this_level + "'><a class=\"jumper\" href='#" + fixedEncodeURIComponent(header.id) + "'>";
        html += "<span class='toc-text'>" + header.innerHTML + "</span>";
        html += "</a>";
      }
      else if (this_level > level) { // lower level than before; expand the previous to contain a ol
        for(i = this_level; i > level; i--) {
          html += "<"+settings.listType+" class='toc-child'><li class='toc-item toc-level-" + i + "'>"
        }
        html += "<a class=\"jumper\" href='#" + fixedEncodeURIComponent(header.id) + "'>";
        html += "<span class='toc-text'>" + header.innerHTML + "</span>";
        html += "</a>";
      }
      level = this_level; // update for the next one
    });
    html += "</"+settings.listType+">";
    if (!settings.noBackToTopLinks) {
      $(document).on('click', '.back-to-top', function() {
        $(window).scrollTop(0);
        window.location.hash = '';
      });
    }

    render[settings.showEffect]();
  };
})(jQuery);

$(document).ready(function(){
  $('.post-directory').toc();

  var fixmeTop = $('#post-directory-module').offset().top;
  var tocSections = $('.clickable-header');
  var tocSectionOffsets = [];

  var calculateTocSections = function(){
    tocSectionOffsets = [];
    tocSections.each(function(index, section){
      tocSectionOffsets.push(section.offsetTop);
    })
  }
  calculateTocSections();
  // Calculates the toc section offsets, which can change as images get loaded
  $(window).bind('load', calculateTocSections);

  var highlightTocSection = function(){
    var highlightIndex = 0;
    var sectionsCount = tocSectionOffsets.length;
    var currentScroll = $(window).scrollTop();

    if (currentScroll+60 > tocSectionOffsets[sectionsCount-1]) {
      highlightIndex = sectionsCount;
    } else {
      for (var i=0; i<sectionsCount; i++) {
        if (currentScroll+60 <= tocSectionOffsets[i]) {
          highlightIndex = i;
          break;
        }
      }
    }
    if (highlightIndex == 0) {
      highlightIndex += 1;
    }
    $('.toc-item .jumper').removeClass('on');
    $('.toc-item .jumper').eq(highlightIndex-1).addClass('on');
  }
  highlightTocSection();

  var updateTocHeight = function() {
      var height = document.documentElement.clientHeight;
      height = height || 'auto';
      $('.post-directory').css('max-height', height);
  }

  $(window).scroll(function() {
    var currentScroll = $(window).scrollTop();
    if (currentScroll >= fixmeTop) {
      $('#post-directory-module').css({
        top: '0',
        position: 'fixed',
        width: 'inherit'
      });
      $('.post-directory').css('overflow', 'auto');
    } else {
      $('#post-directory-module').css({
        position: 'inherit',
        width: 'inherit'
      });
      $('.post-directory').css('overflow', 'hidden');
      $('.post-directory').scrollTop(0);
    }

    highlightTocSection();
  });

  updateTocHeight();

  $(window).on("resize", function() {
      updateTocHeight();
  });
});

$(".jumper").on("click", function( e ) {
  e.preventDefault();
  $("body, html").animate({
    scrollTop: $( $(this).attr('href') ).offset().top
  }, 600);
});
```















