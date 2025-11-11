---
layout: post
title: Notion 페이지를 PDF로 변환할 때 Mermaid 다이어그램을 그림으로 보이게 만드는 방법
date: 2025-11-10
categories: [Tool]
tags: [Tool, Notion, Mermaid]
---

Notion에서 페이지를 파일로 내보낼 때, Mermaid 코드블록은 보통 코드 텍스트로 남아 있고 자동으로 렌더링되지 않는다.

따라서 PDF에도 코드 텍스트만 출력되고 다이어그램이 보이지 않는 문제가 있다.

Notion 문서를 Export 한 경우에도 Mermaid 다이어그램이 실제 그림으로 보이도록 만드는 해보자.

1. Notion 페이지를 HTML로 Export
2. HTML body 내부에 하단에 다음과 같은 스크립트를 추가
  ```html
  <script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
  <script>
    document.addEventListener('DOMContentLoaded', function () {
      var codeBlocks = document.querySelectorAll('code.language-Mermaid, code.language-mermaid');
      codeBlocks.forEach(function (codeBlock) {
        var pre = codeBlock.parentElement;
        if (!pre) {
          return;
        }
        var container = document.createElement('div');
        container.className = 'mermaid';
        container.textContent = codeBlock.textContent;
        pre.replaceWith(container);
      });
      if (window.mermaid) {
        mermaid.initialize({ startOnLoad: false });
        mermaid.init(undefined, document.querySelectorAll('.mermaid'));
      }
    });
  </script>
  ``` 
3. HTML 파일을 브라우저를 열어서 '인쇄' 'PDF로 저장'
