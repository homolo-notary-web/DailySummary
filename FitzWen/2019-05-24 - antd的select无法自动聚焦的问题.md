#### ng-zorro-antd下拉框无法自动聚焦的问题

##### 问题：ng-zorro-antd的下拉选择框在自动打开下拉选项的时候（即设置nzOpen为true）时，同时使用nzShowSearch时无法聚焦，导致无法输入选择

##### 解决方法：使用select的focus聚焦方法
- html
```
  <nz-select
    [nzOpen]="open"
    style="width: 100%;"
    #selectElement
    nzShowSearch
    nzPlaceHolder="input search text"
    >
    <nz-option *ngFor="let o of listOfOption" [nzLabel]="o.text" [nzValue]="o.value"></nz-option>
  </nz-select>
```
- ts
```
 import { NzSelectComponent } from 'ng-zorro-antd';

 @ViewChild('selectElement') selectElement:NzSelectComponent;

 this.selectElement.focus();
```