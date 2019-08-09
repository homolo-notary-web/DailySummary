# angluar 中运用 echarts

## 快速使用

### 你可以使用如下命令通过 npm 安装 ECharts

```typescript
npm install echarts --save
```

### 在 ts 中引入

```typescript
import { Component, OnInit } from '@angular/core';
import echarts from 'echarts/lib/echarts';
import 'echarts/map/js/china.js';
import 'echarts/map/js/province/shanghai.js';
@Component({
  selector: 'app-manage-crises',
  templateUrl: './manage-crises.component.html',
  styleUrls: ['./manage-crises.component.scss']
})
export class ManageCrisesComponent implements OnInit {
  constructor() {}

  ngOnInit() {
    console.log(echarts);
    echarts.init(document.getElementById('main')).setOption({
      series: {
        type: 'pie',
        data: [
          { name: 'A', value: 1212 },
          { name: 'B', value: 2323 },
          { name: 'C', value: 1919 }
        ]
      }
    });
    this.mapGeo();
  }
  mapGeo() {
    const chart = echarts.init(document.getElementById('map'));
    chart.setOption({
      tooltip: {
        backgroundColor: 'red',
        formatter: function(params) {
          console.log(params);
          const str = '<div>' + params.name + '</div>';
          return str;
        }
      },
      geo: {
        map: '上海',
        label: {
          normal: {
            show: true,
            textStyle: {
              color: '#333',
              fontSize: 12,
              fontFamily: 'Arial'
            }
          }
        },
        roam: true,
        itemStyle: {
          normal: {
            borderColor: '#E6E6E6',
            borderWidth: 0.8,
            areaColor: '#2cacf7'
          }
        }
      },
      series: [
        {
          type: 'map',
          map: '',
          geoIndex: 0,
          roam: true,
          zoom: 1
        }
      ]
    });
  }
}
```

### html 中的容器

```html
<div id="main" style="width: 600px;height:400px;"></div>
<div id="map" style="width: 1200px;height:800px;"></div>
```

## 异步加载更新

- 只需要在拿到数据之后渲染图标就行

```typescript
const myChart = echarts.init(document.getElementById('main'));
$.get('data.json').done(function(data) {
  myChart.setOption({
    title: {
      text: '异步数据加载示例'
    },
    tooltip: {},
    legend: {
      data: ['销量']
    },
    xAxis: {
      data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
    },
    yAxis: {},
    series: [
      {
        name: '销量',
        type: 'bar',
        data: [5, 20, 36, 10, 10, 20]
      }
    ]
  });
});
```

## 使用 dataset 管理数据

```typescript
const myChart = echarts.init(document.getElementById('main'));
const option = {
  legend: {},
  tooltip: {},
  dataset: {
    // 提供一份数据。
    source: [
      ['product', '2015', '2016', '2017'],
      ['Matcha Latte', 43.3, 85.8, 93.7],
      ['Milk Tea', 83.1, 73.4, 55.1],
      ['Cheese Cocoa', 86.4, 65.2, 82.5],
      ['Walnut Brownie', 72.4, 53.9, 39.1]
    ]
  },
  // 声明一个 X 轴，类目轴（category）。默认情况下，类目轴对应到 dataset 第一列。
  xAxis: { type: 'category' },
  // 声明一个 Y 轴，数值轴。
  yAxis: {},
  // 声明多个 bar 系列，默认情况下，每个系列会自动对应到 dataset 的每一列。
  series: [{ type: 'bar' }, { type: 'bar' }, { type: 'bar' }]
};
myChart.setOption(option);
```

## 上海地区的地图渲染

```javascript
const chart = echarts.init(document.getElementById('map'));
chart.setOption({
  tooltip: {
    // hover每个地区的提示框
    backgroundColor: 'red',
    formatter: function(params) {
      // 提示框浮层内容格式器，支持字符串模板和回调函数两种形式。
      console.log(params);
      const str = '<div>' + params.name + '</div>';
      return str;
    }
  },
  visualMap: {
    //  是视觉映射组件，用于进行『视觉编码』，也就是将数据映射到视觉元素（视觉通道），在地图的左下角显示
    min: 800,
    max: 50000,
    text: ['High', 'Low'],
    realtime: false,
    calculable: true,
    inRange: {
      color: ['lightskyblue', 'yellow', 'orangered']
    }
  },
  series: [
    {
      type: 'map', // 地图主要用于地理区域数据的可视化，配合 visualMap 组件用于展示不同区域的人口分布密度等数据。
      map: '上海',
      roam: true, // 是否缩放
      zoom: 1,
      itemStyle: {
        // 自定义样式
        normal: { label: { show: true } },
        emphasis: { label: { show: true } }
      },
      label: {
        // 图形上的文本标签，可用于说明图形的一些数据信息，比如值，名称等，这里可以显示地图上各地区的名字
        normal: {
          show: true,
          textStyle: {
            color: '#333',
            fontSize: 12,
            fontFamily: 'Arial'
          }
        }
      },
      data: [
        { name: '浦东新区', value: 2007.34 },
        { name: '徐汇区', value: 2057.34 },
        { name: '松江区', value: 8057.34 },
        { name: '宝山区', value: 40057.34 },
        { name: '金山区', value: 4057.34 },
        { name: '奉贤区', value: 9457.34 },
        { name: '崇明区', value: 10057.34 },
        { name: '嘉定区', value: 30057.34 }
      ]
    }
  ]
});
```

## 徐汇区到各区的线图 demo

```javascript
 linesMap() {
    const chart = echarts.init(document.getElementById('lines'));
    const  geoCoordMap = {
      '宝三区': [121.48, 31.4],
      '嘉定区': [121.27, 31.38],
      '松江区': [121.22, 31.03],
      '崇明区': [121.4, 31.62],
      '青浦区': [121.12, 31.15],
      '浦东新区': [121.53, 31.22],
      '奉贤区': [121.47, 30.92],
      '徐汇区': [121.43, 31.18]
    };
    const pdxq = [
      [{ name: '徐汇区' }, { name: '宝三区', value: 50 }],
      [{ name: '徐汇区' }, { name: '嘉定区', value: 50 }],
      [{ name: '徐汇区' }, { name: '松江区', value: 50 }],
      [{ name: '徐汇区' }, { name: '崇明区', value: 50 }],
      [{ name: '徐汇区' }, { name: '青浦区', value: 50 }],
      [{ name: '徐汇区' }, { name: '浦东新区', value: 50 }],
      [{ name: '徐汇区' }, { name: '奉贤区', value: 50 }]
    ];
    const color = ['#a6c84c'];
    const series = [];
    [['徐汇区', pdxq]].forEach((item, i) => {
      console.log(item);
      series.push({
        name: item[0] + ' Top10',
        type: 'lines',
        zlevel: 1,
        effect: {
          show: true,
          period: 6,
          trailLength: 0.7,
          color: '#fff',
          symbolSize: 3
        },
        lineStyle: {
          normal: {
            color: color[i],
            width: 0,
            curveness: 0.2
          }
        },
        data: this.convertData(item[1])
      },
        {
          name: item[0] + ' Top10',
          type: 'lines',
          zlevel: 2,
          symbol: ['none', 'arrow'],
          symbolSize: 10,
          effect: {
            show: true,
            period: 6,
            trailLength: 0,
            symbol: this.planePath,
            symbolSize: 15
          },
          lineStyle: {
            normal: {
              color: color[i],
              width: 1,
              opacity: 0.6,
              curveness: 0.2
            }
          },
          data: this.convertData(item[1])
        },
        {
          name: item[0] + ' Top10',
          type: 'effectScatter',
          coordinateSystem: 'geo',
          zlevel: 2,
          rippleEffect: {
            brushType: 'stroke'
          },
          label: {
            normal: {
              show: true,
              position: 'right',
              formatter: '{b}'
            }
          },
          symbolSize: function (val) {
            return val[2] / 8;
          },
          itemStyle: {
            normal: {
              color: color[i]
            }
          },
          data: [].slice.apply(item[1]).map(function (dataItem) {
            console.log(dataItem);
            return {
              name: dataItem[1].name,
              value: geoCoordMap[dataItem[1].name].concat([dataItem[1].value])
            };
          })
        });
    });
    chart.setOption({
      backgroundColor: '#404a59',
      title: {
        text: '模拟迁徙',
        subtext: '数据纯属虚构',
        left: 'center',
        textStyle: {
          color: '#fff'
        }
      },
      tooltip: {
        trigger: 'item'
      },
      legend: {
        orient: 'vertical',
        top: 'bottom',
        left: 'right',
        data: ['徐汇区 Top10'],
        textStyle: {
          color: '#fff'
        },
        selectedMode: 'single'
      },
      geo: {
        map: '上海',
        label: {
          emphasis: {
            show: false
          }
        },
        roam: true,
        itemStyle: {
          normal: {
            areaColor: '#323c48',
            borderColor: '#404a59'
          },
          emphasis: {
            areaColor: '#2a333d'
          }
        }
      },
      series: series
    });
  }
  convertData = function (data) {
    const res = [];
    for (let i = 0; i < data.length; i++) {
      const dataItem = data[i];
      const fromCoord = this.geoCoordMap[dataItem[0].name];
      const toCoord = this.geoCoordMap[dataItem[1].name];
      if (fromCoord && toCoord) {
        res.push({
          fromName: dataItem[0].name,
          toName: dataItem[1].name,
          coords: [fromCoord, toCoord]
        });
      }
    }
    return res;
  };
```

# 处理为保存的更改

> 在一体化办证平台公证事项受理登记时，基本信息登记后如果要离开这个页面，没保存的情况下，会弹出模态框信息没保存是否离开的功能

- 生成一个守卫，命令如下

```typescript
ng generate guard leave-guard
```

- 里面的内容

```typescript
import { Injectable } from '@angular/core';
import { CanDeactivate } from '@angular/router';
import { Observable } from 'rxjs';

export interface CanComponentDeactivate {
  canDeactivate: () => Observable<boolean> | Promise<boolean> | boolean;
}

@Injectable()
export class CanDeactivateGuard
  implements CanDeactivate<CanComponentDeactivate> {
  canDeactivate(component: CanComponentDeactivate) {
    return component.canDeactivate ? component.canDeactivate() : true;
  }
}
```

- 在相应的 admin-routing.module.ts 路由页面中引入守卫服务

```typescript
import { CanDeactivateGuard } from 'app/service/leave-guard.service';
// 相应的页面路由数组中添加
{
  path: 'settings',
  component: SettingsComponent,
  canDeactivate: [CanDeactivateGuard]
}
```

- 在受理登记的表单页面中

```typescript
canDeactivate(): Observable<boolean> | boolean {
    if (this.globalEdit === false) {
      return true;
    }
    const obs = new Observable<boolean>(observer => {
      this.modalService.confirm({
        nzTitle: '<i>您正在离开页面</i>',
        nzContent: '<b>请问确认是否离开页面，您可能会丢失尚未保存的数据。</b>',
        nzOnOk: () => {
          observer.next(true);
        },
        nzOnCancel: () => {
          observer.next(false);
        },
      });
    });
    return obs;
  }
```

# 使用 NG-ZORRO 的 tabs 结合路由复用策略实现动态 tab

> 通过实现了小周给的这篇文章，亲手实验了一下，[https://www.jianshu.com/p/c8ed7db7dd0f?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation]。
> 路由复用策略: [https://zhuanlan.zhihu.com/p/29823560]

- 在 app.component.html 中的主要结构

```html
<!-- 头部分 -->
<app-header></app-header>
<!-- 菜单栏 -->
<app-sidebar></app-sidebar>
<div class="content">
  <!-- ng-zorro的组件写的自定义组件 -->
  <app-tab>
    <router-outlet></router-outlet>
  </app-tab>
</div>
```

- app-route.module.ts

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
export const ROUTES: Routes = [
  {
    path: 'page1',
    loadChildren: './pages/page1/page1.module#Page1Module',
    data: {
      title: ' 页面1',
      isRemove: true
    }
  },
  {
    path: 'page2',
    loadChildren: './pages/page2/page2.module#Page2Module',
    data: {
      title: '页面2',
      isRemove: true
    }
  },
  {
    path: 'page3',
    loadChildren: './pages/page3/page3.module#Page3Module',
    data: {
      title: '页面3',
      isRemove: true
    }
  }
];
@NgModule({
  imports: [
    // 因为是根路由，所以使用forRoot
    RouterModule.forRoot(ROUTES)
  ],
  exports: [RouterModule]
})
export class AppRouterModule {}
```

- tab.component.html

```html
<nz-tabset
  style="margin-left: -1px;"
  [nzAnimated]="true"
  [nzSelectedIndex]="currentIndex"
  [nzShowPagination]="true"
  (nzSelectChange)="nzSelectChange($event)"
  [nzType]="'card'"
>
  <nz-tab *ngFor="let menu of menuList" [nzTitle]="nzTabHeading">
    <ng-template #nzTabHeading>
      <div>
        {{menu.title}}
        <i
          *ngIf="menu.isRemove"
          (click)="closeUrl(menu.url)"
          class="anticon anticon-cross"
        ></i>
      </div>
    </ng-template>
  </nz-tab>
</nz-tabset>
<div class="tab-content">
  <!--路由的内容会被显示在这里-->
  <ng-content></ng-content>
</div>
```

- tab.component.ts

```typescript
import { Component } from '@angular/core';
import { ActivatedRoute, NavigationEnd, Router } from '@angular/router';
import { Title } from '@angular/platform-browser';
import { SimpleReuseStrategy } from '../../service/SimpleReuseStrategy';

// 这里按原网站引入这三个的话会报错
// import 'rxjs/add/operator/filter';
// import 'rxjs/add/operator/map';
// import 'rxjs/add/operator/mergeMap';
// 正确引入方法
import { map } from 'rxjs/operators';
import { filter } from 'rxjs/operators';
import { mergeMap } from 'rxjs/operators';
@Component({
  selector: 'app-tab',
  templateUrl: './tab.component.html',
  styleUrls: ['./tab.component.css']
})
export class TabComponent {
  // 路由列表
  menuList = [];
  // 当前选择的tab index
  currentIndex = -1;
  constructor(
    private router: Router,
    private activatedRoute: ActivatedRoute,
    private titleService: Title
  ) {
    // 原网站写法报错
    this.router.events
      .filter(event => event instanceof NavigationEnd)
      .map(() => this.activatedRoute)
      .map(route => {
        while (route.firstChild) {
          route = route.firstChild;
        }
        return route;
      })
      .filter(route => route.outlet === 'primary')
      .mergeMap(route => route.data)
      .subscribe(event => {
        // 路由data的标题
        const menu = { ...event };
        menu.url = this.router.url;
        const url = menu.url;
        this.titleService.setTitle(menu.title); // 设置网页标题
        const exitMenu = this.menuList.find(info => info.url === url);
        if (!exitMenu) {
          // 如果不存在那么不添加，
          this.menuList.push(menu);
        }
        this.currentIndex = this.menuList.findIndex(p => p.url === url);
      });
    // 现在的写法通过管道筛选
    this.router.events
      .pipe(
        filter(event => event instanceof NavigationEnd),
        map(() => this.activatedRoute),
        map(route => {
          while (route.firstChild) {
            route = route.firstChild;
          }
          return route;
        }),
        filter(route => route.outlet === 'primary'),
        mergeMap(route => route.data)
      )
      .subscribe(event => {
        // 路由data的标题
        console.log(event);
        const menu = { ...event };
        menu.url = this.router.url;
        const url = menu.url;
        this.titleService.setTitle(menu.title); // 设置网页标题
        const exitMenu = this.menuList.find(info => info.url === url);
        if (!exitMenu) {
          // 如果不存在那么不添加，
          this.menuList.push(menu);
        }
        this.currentIndex = this.menuList.findIndex(p => p.url === url);
      });
  }

  // 关闭选项标签
  closeUrl(url: string) {
    // 当前关闭的是第几个路由
    const index = this.menuList.findIndex(p => p.url === url);
    // 如果只有一个不可以关闭
    if (this.menuList.length === 1) {
      return;
    }
    this.menuList.splice(index, 1);
    // 删除复用
    // delete SimpleReuseStrategy.handlers[module];
    SimpleReuseStrategy.deleteRouteSnapshot(url);
    // 如果当前删除的对象是当前选中的，那么需要跳转
    if (this.currentIndex === index) {
      // 显示上一个选中
      let menu = this.menuList[index - 1];
      if (!menu) {
        // 如果上一个没有下一个选中
        menu = this.menuList[index];
      }
      // 跳转路由
      this.router.navigate([menu.url]);
    }
  }
  /**
   * tab发生改变
   */
  nzSelectChange($event) {
    this.currentIndex = $event.index;
    const menu = this.menuList[this.currentIndex];
    // 跳转路由
    this.router.navigate([menu.url]);
  }
}
```

- sidebar.component.html

```html
<div class="sidebar">
  <ul nz-menu [nzMode]="'inline'" style="width: 240px;">
    <li nz-submenu>
      <span title><i class="anticon anticon-appstore"></i>系统管理</span>
      <ul>
        <li nz-menu-item (click)="tabs('page1')">页面1</li>
        <li nz-menu-item (click)="tabs('page2')">页面2</li>
        <li nz-menu-item (click)="tabs('page3')">页面3</li>
      </ul>
    </li>
  </ul>
</div>
```

- sidebar.component.ts

```typescript
import { Component, OnInit } from '@angular/core';
import { Router } from '@angular/router';
@Component({
  selector: 'app-sidebar',
  templateUrl: './sidebar.component.html',
  styleUrls: ['./sidebar.component.css']
})
export class SidebarComponent implements OnInit {
  constructor(private router: Router) {}
  ngOnInit() {}
  /**
   * 路由方式添加tab
   * @param data
   */
  // 按照菜单切换相应的路由
  tabs(data) {
    this.router.navigate([data]);
  }
}
```
- page1、page2、page3的目录结构

> 以上就是这种通过路由懒加载和NG-ZORRO的tabs实现tabs的路由切换, 理解原理后，可以自己实践一下会有自己的体会，原文有些写法版本有点老，我都做了标注。
