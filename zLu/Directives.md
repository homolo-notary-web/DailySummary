# Directives

<a name="NQTrX"></a>
# VUE
<a name="jhaSp"></a>
### Vue 指令生命周期
bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置<br />inserted：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。<br />update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 。<br />componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用。<br />unbind：只调用一次，指令与元素解绑时调用。
<a name="y4WXL"></a>
### 生命周期函数中的参数
el: 指令所绑定的元素，可以用来直接操作 DOM，就是放置指令的那个元素。<br />binding: 一个对象，里面包含了几个属性，这里不多展开说明，官方文档上都有很详细的描述。<br />vnode：Vue 编译生成的虚拟节点。<br />oldVnode：上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用

写在了main.js中，使用时直接用v-drag指令。
```javascript
Vue.directive('drag', {
  // 指令的定义
  bind: function(el, binding) {
    var touch, disX, disY;
    el.ontouchstart = e => {
      if (e.touches) {
        //有可能对象在e上也有可能对象在e.touches[0]上
        touch = e.touches[0];
      } else {
        touch = e;
      }
      disX = touch.clientX - el.offsetLeft; //鼠标位置X减去元素距离左边距离（鼠标到元素左边的距离）
      disY = touch.clientY - el.offsetTop; //鼠标位置Y减去距离顶部距离（鼠标到元素顶部的高度）
    };
    el.ontouchmove = e => {
      if (e.touches) { 
        //有可能对象在e上也有可能对象在e.touches[0]上
        touch = e.touches[0];
      } else {
        touch = e;
      }
      //用鼠标的位置减去鼠标相对元素的位置，得到元素的位置
      let left = touch.clientX - disX;
      let top = touch.clientY - disY;

      //移动当前元素
      el.style.left = left + 'px';
      el.style.top = top + 'px';
      e.preventDefault(); //阻止页面的滑动默认事件
    };
    el.ontouchend = e => {
      // el.ontouchmove = null;
      // el.ontouchend = null;
    };
  }
});
```

<a name="qD413"></a>
# Angular
官方文档：[https://www.angular.cn/api/core/Directive](https://www.angular.cn/api/core/Directive)<br />直接上例子

```javascript
import { AfterViewInit, Directive, ElementRef, Input } from '@angular/core';

@Directive({
  selector: '[appTableResizable]',
})
export class TableResizableDirective implements AfterViewInit {
  // tslint:disable-next-line:no-input-rename
  @Input('appTableResizable')
  use = false;
  columnWidths: string[];
  usePixel = false;
  constructor(private $element: ElementRef<HTMLTableElement>) {}

  ngAfterViewInit() {
    // 拿到了table元素
    if (!this.use) {
      return;
    }
    const el = (() => {
      let result = this.$element.nativeElement;
      if (result.tagName === 'NZ-TABLE') {
        result = result.querySelector('table');
      }
      if (result.tagName !== 'TABLE') {
        throw new TypeError('Must be a TABLE element');
      }
      if (!result.tHead) {
        throw new TypeError('Must have a THEAD element');
      }
      return result;
    })();
    // console.log(el);
    el.classList.add('table-resizable');
    const tr = el.tHead.rows[0];
    if (!tr) {
      throw new TypeError(
        'Must have at least one TR element inside THEAD element',
      );
    }

    setTimeout(() => {
      const ths = Array.from(tr.cells) as HTMLTableHeaderCellElement[];
      if (ths.length <= 1) {
        return;
      }
      --ths.length; // 最后一个方格总是自动列宽的（用于占满整行）

      if (!Array.isArray(this.columnWidths)) {
        this.columnWidths = new Array(ths.length).fill('');
      } else {
        this.columnWidths.length = ths.length;
        this.columnWidths.forEach((x, i, arr) => {
          if (!x) {
            arr[i] = '';
          }
        });
      }

      ths.forEach(th => {
        if (th.classList.contains('no-resize')) {
          return;
        }
        if (this.columnWidths[th.cellIndex]) {
          console.log(th.width);
          th.width = this.columnWidths[th.cellIndex];
        }

        const i = document.createElement('i');
        i.classList.add('resize-indicator');
        th.appendChild(i);

        i.addEventListener('mousedown', e => {
          if (e.button === 1) {
            // 鼠标中键
            th.width = '';
            return;
          }
          if (e.button !== 0) {
            return;
          }
          const startX = e.pageX;
          const startThWidth = th.clientWidth;
          document.body.classList.add('table-resizing');

          let mousemoveHandler;

          // tslint:disable-next-line: no-shadowed-variable
          document.body.addEventListener(
            'mousemove',
            (mousemoveHandler = doc => {
              if (doc.button !== 0) {
                return;
              }
              const pixel = doc.pageX - startX + startThWidth;
              if (this.usePixel) {
                th.width = pixel + '';
              } else {
                th.width = (pixel / tr.offsetWidth) * 100 + '%';
              }
              this.columnWidths[th.cellIndex] = th.width;
            }),
          );

          // tslint:disable-next-line: no-shadowed-variable
          document.body.addEventListener(
            'mouseup',
            docu => {
              if (docu.button !== 0) {
                return;
              }
              document.body.removeEventListener('mousemove', mousemoveHandler);
              document.body.classList.remove('table-resizing');
            },
            { once: true },
          );
        });
      });
    }, 100);
  }
}

```

