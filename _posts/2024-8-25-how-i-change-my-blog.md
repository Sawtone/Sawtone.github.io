---
layout: post
title: 一次心血来潮的举动——试图修改博客 JavaScript 处理逻辑
categories: [JavaScript, HTML]
description: JavaScript, HTML
keywords: JavaScript, HTML
---

# 随着学习逐渐深入，对于博客源码不合心意的地方，逐渐从刻意逃避，到找原主提 Issues 交流，到现在自己尝试修改，受益匪浅


## JavaScript 生成目录逻辑与超链接属性冲突

### 背景

写 <a href="https://sawtone.github.io/2024/08/23/RYF-JavaScript-ECMAScript6-Study/" target="_blank">阮一峰 JavaScript & ECMAScript6 学习笔记与思考</a> 这篇博客时

我使用上面提到的超链接方式，以章节为分界，为每个小节设置了一个带超链接的三级标题

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
```

```JavaScript
var headers = $(settings.headers).filter(function() {
      if ($(this).parent('blockquote').length > 0) {
        return false;
      }
```

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















