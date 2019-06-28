# DMS方法总结

<a name="iLDUd"></a>
# 四个页面
approval审批<br />seal盖章<br />draftnew拟稿<br />translate翻译
<a name="7Dkd6"></a>
## 主要方法
<a name="ziX9Y"></a>
#### 1、getDraftLists()
此方法用于左侧事项列表的获取。<br />查询参数
```typescript
searchParms = {
    volumeNumber: undefined, // 卷号
    partyName: undefined, // 当事人
    yearSelected: undefined, // 年份
    categorySelected: undefined, // 类型
    caseNumberSelected: undefined, // 卷号
    entityId: undefined //事项id，正常由路由带入
};
```

使用场景：<br />1）地址栏带值，初始化word插件时调用。<br />2）搜索栏键入enter或者点击搜索<br />注意点：<br />1）此方法获取数据是卷，若路由传入某单个事项需要做判断，将其作为选中事项，并载入其数据，即15-26行代码。

```typescript
getDraftLists() {
    for (const item of Object.keys(this.searchParms)) {
      if (this.searchParms[item]) {
        this.searchParms[item] = this.searchParms[item].trim();
      }
    }
    this.restclient.request(
      'nbm.DraftApproveService',
      'collection',
      'queryCaseListInDraftApprove',
      this.searchParms
    ).then(res => {
      if (res.code === 1) {
        this.draftdata = res.result;
        if (this.searchParms.entityId) { // 若路由传入事项id则判断事项是卷中第几个
          for (let i; i < res.result.list.length; i++) {
            if (res.result.list[i].id === this.searchParms.entityId) {
              const param = {
                entityId: res.result.list[i].id
              };
              this.getDraftDetail(param); // 查询单独一个事项
              this.resetQuery(); // 重置查询参数
              return;
            }
          }
        }
        this.getRecord(); // 获取打印记录
        // console.log(res.result);
        if (this.draftdata.details.doc && this.draftdata.details.doc['fileId']) {
          const docid = this.draftdata.details.doc['fileId'];
          this.officeobj.openDoc(docid, 'Editing');
          if (this.draftdata.details.entity.properties.status === 'Approved') {
            this.hasSub = true;
          } else {
            this.hasSub = false;
          }
        }
      } else {
        this.alertbox.showAlert('error', res.description);
        this.draftdata.list = [];
        this.draftdata.details.approves = [];
        this.draftdata.details.entity = {
          id: undefined,
          $displays: {
            notary: undefined,
            acceptDate: undefined,
            caseType: undefined,
            expectedCertificateDate: undefined
          },
          properties: {
            status: undefined,
            caseNumber: undefined,
          }
        };
        // this.isShowDoc = false;
        // this.notice.warning('', res.description);
        // this.showAlert('warning', res.description);
      }

    }).catch(err => {
      console.log(err);
    });
    this.resetQuery(); // 重置查询参数
  }
```


<a name="5GAP5"></a>
#### 2、getDraftDetail(param)
此方法用于左侧事项列表中详细数据的获取。

```typescript
getDraftDetail(param) {
    this.restclient.request(
      'nbm.DraftApproveService',
      'collection',
      'queryCaseListInDraftApprove',
      param
    ).then(res => {
      if (res.code === 1) {
        this.draftdata = res.result;
        // console.log(res.result);
        this.getRecord();
        if (this.draftdata.details.doc && this.draftdata.details.doc['fileId']) {
          this.docTemplateId = this.draftdata.details.doc.docTemplateId;
          const docid = this.draftdata.details.doc['fileId'];
          this.officeobj.openDoc(docid, 'Editing');
          if (this.draftdata.details.entity.properties.status === 'Approved') {
            this.hasSub = true;
          } else {
            this.hasSub = false;
            this.isAutodate = 'Auto'; // 重置盖章等选项数据
            this.isAutoseal = 'Auto';
            this.approveNote = '通过';
          }
        }
        // console.log(res);
      } else {
        this.alertbox.showAlert('error', res.description);
      }
    }).catch(err => {
      console.log(err);
    });
  }
```

<a name="WEqEv"></a>
#### 3、saveword() 、specialsaveword()、approvalDone()
等调用word保存的方法需要在回调函数返回flag之后进行下一步的操作。

```typescript
saveword() {
    let flag = false;
    const reqParams = {
      caseId: this.draftdata.details.entity['id']
    };
    const fileId = this.draftdata.details.doc['fileId'];
    const fileName = this.draftdata.details.doc['fileName'];
    const _this = this;
    this.officeobj.saveDoc('/word/collection/ntkoSave', reqParams, fileId, fileName, () => {
      flag = true;
    });
    if (!flag) {
      run();
    }
    function run() {
      setTimeout(() => {
        if (flag) {
          _this.alertbox.showAlert('success', '保存成功！');
          return;
        } else {
          run();
        }
      }, 1000);
    }
  }
```

<a name="sjSte"></a>
#### 4、refreshDate()
 提交、撤销后需要刷新左侧列表


<a name="kmZZJ"></a>
#### 5、itemClick()
点击左侧列表，刷新左侧数据，并选定详细显示的数据<br />clickflag只有在完全打开word之后通关回调函数开放。

```typescript
itenClick(item, index) {
  const that = this;
  if (item.id === this.draftdata.details.entity.id) {
    return;
  }
  if (this.clickflag) {
    this.clickflag = false;
  } else {
    return;
  }
  const param = {
    entityId: item.id
  };
  this.draftdata.details.entity = item;
  this.getDraftDetail(param, true);
  this.scrollUp(index); // 列表自动下滚方法
}

scrollUp(index) {
  if (index * 32 > 128) {
    document.getElementById('boxbody').querySelector('.ant-table-body').scrollTo(0, index * 32 - 128);
  } else {
    document.getElementById('boxbody').querySelector('.ant-table-body').scrollTo(0, 0);
  }
}
```

 以上为通用方法。

<a name="75fNs"></a>
## 特殊方法
<a name="RW7Mi"></a>
#### 6、doPrint()
打印方法。打印分为水印纸和普通纸。用于seal盖章和translate翻译。<br />第一步，判断纸张类型，水印纸需要获取水印纸编号，并存入本地。<br />第二步，上传打印记录并打印。

```typescript
// 点击打印
doPrint() {
  if (this.papertype === 'waterpaper') {
    const obj = {
      entityId: this.draftdata.details.entity.id,
      size: this.pagecount * this.printcount, // 页数*打印份数
      oprate:　this.waterpaper.info3, // 加或减
      paperLaber: this.waterpaper.info1, // 前缀　例如Ｉ
      number: this.waterpaper.info2, // 水印纸编号
      time: new Date().getTime()
    };
    if (!this.waterpaper.info2) {
      this.alertbox.showAlert('error', '请填写水印纸编号！');
      return;
    }
    this.restclient.requestGet(
      environment.restServiceUrl + 'inm.WatermarkPaper/collection/submit?' +
      'entityId=' + this.draftdata.details.entity.id + '&size=' +
      this.pagecount * this.printcount + '&oprate=' + this.waterpaper.info3 +
      '&paperLaber=' + this.waterpaper.info1 + '&number=' +
      this.waterpaper.info2
    ).then(res => {
      if (res.code === 1) {
        // console.log(res.result);
        this.waterpaper.info2 = res.result;
        window.localStorage.setItem('waterpapercount', res.result);
        this.printNow();
      } else {
        this.alertbox.showAlert('error', res.description);
      }
    }).catch(err => {
      console.log(err);
    });
  } else if (this.papertype === 'paper') {
    // 普通纸
    this.printNow();
  }
}
// 进行打印记录并打印
printNow() {
  const num = this.printcount.toString();
  const params = {
    caseId: this.draftdata.details.entity.id,
    num: num
  };
  this.restclient.request(
    'inm.CaseService',
    'collection',
    'record',
    params
  ).then(res => {
    if (res.code === 1) {
      // console.log(res.result);
      this.officeobj.docPrint(this.printcount);
      this.gethasPrint();
    } else {
      this.alertbox.showAlert('error', res.description);
    }
  }).catch(err => {
    console.log(err);
  });
}
```

<a name="JgTUw"></a>
#### 7、bookmarkmap()
构造书签数组方法，用于draftnew拟稿和translate翻译，两个页面有一定区别

<a name="UnGJj"></a>
# word组件
1、openDoc 打开word方法<br />
2、setBookmarkValue 设置书签方法<br />
3、saveDoc  保存word 方法<br />
4、wordCallBack  word打开完成回调<br />
_this.docPageNum.emit(_this.pagenum); // 回调抛出页数给父组件<br />word显示区域的菜单方法在html文件中。

<a name="2bPpN"></a>
# widget组件
分为2个部分，拟稿、谈话笔录部分和翻译部分，trans-前缀为翻译客制化的部分。<br />该部分用于word书签的显示和同步变量。<br />3个主要功能，日期组件自动验证；失焦自动同步word书签；书签关键词替换，书签名超过11个汉字，则替换。委托代理人  => 委代；法定代理人 => 法代；
