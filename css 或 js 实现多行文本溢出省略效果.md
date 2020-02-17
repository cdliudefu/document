## css 或 js 实现多行文本溢出省略效果

```html
<div class="line two">从搜索引擎优化角度出发，301重定向是网址重定向最为可行的一种办法。当网站的域名发生变更后，搜索引擎只对新网址进行索引，同时又会把旧地址下原有的外部链接如数转移到新地址下，从而不会让网站的排名因为网址变更而收到丝毫影响。同样，在使用301永久性重定向命令让多个域名指向网站主域时，亦不会对网站的排名产生任何负面影响</div>
```

```css
<style>
   .line{
     width:300px;
     height:60px;
     border:1px solid #ccc;
     line-height: 20px;
   }
   .one{
     overflow: hidden;
     text-overflow: ellipsis;
     white-space: nowrap;
   }

   .two{
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;
    overflow: hidden;
    /* 兼容 */
    position: relative;
    line-height: 20px;
    max-height: 40px;
   }
   /*采用伪类*/
   .two::after{
     content: '...';
     position: absolute;
     bottom: 0;
     right: 0;
     padding-left: 40px;
     background: -webkit-linear-gradient(left,transparent,#fff 55%);
     background: -o-linear-gradient(left,transparent,#fff 55%);
     background: -moz-linear-gradient(left,transparent,#fff 55%);
     background: linear-gradient(left,transparent,#fff 55%);
   }
  
  </style>
```
采用JS实现
```js
<script>
    let str = '这是文本这是文本这是文本这是文本这是文本这是文本这是文本这是文本这是文本这是文本这是文本这是文本这是文本这是文本这是文本是文本这是文本这是文本是文本这是文本这是文本是文本这是文本这是文本是文本这是文本这是文本是文本这是文本这是文本是文本这是文本这是文本是文本这是文本这是文本是文本这是文本这是文本是文本这是文本这是文本';
    let box = document.getElementById('box');
    H = box.offsetHeight;
    for(i=0; i<str.length; i++){
        box.innerHTML = str.substring(0,i);
        if(H<box.scrollHeight){
            box.style.overflow = 'hidden';
            box.innerHTML = str.substring(0,i-3) + '...';
            break;
        }
    }
  // const p = document.querySelector('.line')
  // let words = p.innerHTML.split(/(?<=[\u4e00-\u9fa5])|(?<=\w*?\b)/g)
  // while (p.scrollHeight > p.clientHeight) {
  //   words.pop()
  //   p.innerHTML = words.join('') + '...'
  // }
</script>
```
