---
layout: default
title: Search
---
<div class="row-fluid">
 <div class="span9">
  <div id="st-results-container"></div>
 </div>
 <div class="span3">
  <a href="http://swiftype.com?ref=pb">
   <img src="http://swiftype.com/assets/media/swiftype-logo-lightbg-large.png" alt="Search by Swiftype" />
  </a>
 </div>
</div>
<script type='text/javascript' src="/lib/hogan/js/hogan-2.0.0.js"></script>
<script type="text/javascript">
  {% raw %}
  var resultTemplate = Hogan.compile('<div class="st-result"><h4 class="title"><a href="{{url}}" class="st-search-result-link">{{title}}</a></h4><h6>{{url}}</h6><div class="st-metadata"><span class="st-snippet">{{{highlight.body}}}</span></div></div>');
  {% endraw %}
  var renderPagination = function (ctx, resultInfo) {
    var currentPage = resultInfo['page'].current_page, totalPages = resultInfo['page'].num_pages;
    $(renderPaginationForType('page', currentPage, totalPages)).appendTo(ctx.resultContainer);
  };

  var renderPaginationForType = function (type, currentPage, totalPages) {
    var pages = '<div class="st-page pagination"><ul>', previousPage, nextPage;

    if (currentPage == 1) {
      pages += '<li class="disabled"><span><i class="icon-double-angle-left"></i></span></li>';
      pages += '<li class="disabled"><span><i class="icon-angle-left"></i></span></li>';
    } else {
      pages += '<li><a href="#" class="st-prev" data-hash="true" data-page="1"><i class="icon-double-angle-left"></i></a></li>';
      previousPage = currentPage - 1;
      if (previousPage > 0) {
        pages += '<li><a href="#" class="st-prev" data-hash="true" data-page="' + previousPage + '"><i class="icon-angle-left"></i></a></li>';
      }
    }

    var s_range = currentPage - 3;
    var e_range = currentPage + 3;
    if (s_range < 1) {
      e_range += (1 - s_range);
      if (e_range > totalPages) e_range = totalPages;
      s_range = 1;
    }
    if (e_range > totalPages) {
      s_range -= (e_range - totalPages);
      if (s_range < 1) s_range = 1;
      e_range = totalPages;
    }

    if (s_range > 1) {
      pages += '<li class="disabled"><span>...</span></li>';
    }
    for (i=s_range; i <= e_range; i++) {
      if (i == currentPage) {
        pages += '<li class="active"><span>'+i+'</span></li>';
      } else {
        pages += '<li><a href="#" class="st-prev" data-hash="true" data-page="'+i+'">'+i+'</a></li>';
      }
    }
    if (e_range < totalPages) {
      pages += '<li class="disabled"><span>...</span></li>';
    }

    if (currentPage == totalPages) {
      pages += '<li class="disabled"><span><i class="icon-angle-right"></i></span></li>';
      pages += '<li class="disabled"><span><i class="icon-double-angle-right"></i></span></li>';
    } else {
      if (currentPage < totalPages) {
        nextPage = currentPage + 1;
        pages += '<li><a href="#" class="st-next" data-hash="true" data-page="' + nextPage + '"><i class="icon-angle-right"></i></a></li>';
      }
      pages += '<li><a href="#" class="st-next" data-hash="true" data-page="' + totalPages + '"><i class="icon-double-angle-right"></i></a></li>';
    }
    pages += '</ul></div>';
    return pages;
  };

  var customResultsRenderer = function(ctx, data) {
    var $resultContainer = ctx.resultContainer,
      config = ctx.config;
  
    $resultContainer.html('');

    renderPagination(ctx, data.info);
  
    $.each(data.records['page'], function (idx, item) {
      ctx.registerResult($(resultTemplate.render(item)).appendTo($resultContainer), item);
    });
  };

  $('#st-search-input').swiftypeSearch({
    resultContainingElement: '#st-results-container',
    engineKey: 'ybEhsDqz2mEFrMtBHiwB',
    renderResultsFunction: customResultsRenderer,
    searchFields: {'page': ['url','title','body']}
  });
</script>
