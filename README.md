# 介绍

> PageModel是一个通过配置渲染简单页面的组件

# 模型介绍

> config整理配置由搜索栏、表格、表单以及其他全局配置组成。

## Config

```js
    type Config<MODEL = AnyObject, EXTENDS = any> = {
      // 模块名称
      name?: string;
  
      // 是否在config准备完毕后直接获取数据
      getImmediate?: boolean;
      // 是否反射
      reflect?: boolean;
      // 全局设置尺寸
      size?: Size;
      // 基请求地址，增删改查接口基于此url构建restful风格地址
      url?: string;
      // 删除接口、详情接口参数构建方式，path参与路径，params参数请求
      urlMode?: UrlMode;
      
      // 是否自动添加新增按钮
      isAutoAddButton?: boolean;

      // 获取地址
      getUrl?: string;
      // 获取方法
      getMethod?: AjaxMethod;
      // 获取的其他参数
      otherParams?: AnyObject;
      // 获取的数据拿取方式（处理不同请求返回数据列表的方法）
      getReqResultProcessFn?: GetReqResultProcessFn<PageModelManager>;
      
      // 请求参数回调处理
      getDataCallback?: (this: PageModelManager, data: MODEL[]) => ExtendKeys<MODEL>[];

      // 树形数据配置
      treeProps?: TreeProps;

      // 删除地址
      delUrl?: string;
      // 删除参数的rowKey键名称
      delKey?: keyof MODEL;
      // 删除方法
      delMethod?: AjaxMethod;
      // 删除参数
      delParams?: AnyObject | ((this: PageModelManager, data: MODEL) => AnyObject);
      // 删除的数据拿取方式
      delReqResultProcessFn?: RequestProcessFn;
  
      // 更新地址
      updUrl?: string;
      // 更新方法
      updMethod?: AjaxMethod;
      // 更新的数据拿取方式
      updReqResultProcessFn?: RequestProcessFn;
  
      // 新增地址
      addUrl?: string;
      // 新增方法
      addMethod?: AjaxMethod;
      // 新增的数据拿取方式
      addReqResultProcessFn?: RequestProcessFn;
      // 新增按钮的样式
      addButton?: Pick<Button, 'text' | 'props'>;
  
      // 详情地址
      detailUrl?: string;
      // 详情参数的rowKey键名称
      detailKey?: keyof MODEL;
      // 详情方法
      detailMethod?: string;
      // 详情的数据拿取方式
      detailReqResultProcessFn?: RequestProcessFn;
      // 是否使用详情
      useDetail?: boolean;
      
      // 导入地址
      importUrl?: string;
      // 导入方法
      importMethod?: AjaxMethod;
      // 导入文件名称
      importFileKey?: string;
      // 导入参数
      importParams?: AnyObject | ((this: any, data: MODEL) => AnyObject);

      // 导出地址
      exportUrl?: string;
      // 导出的模式 后端构建 get 或者 前端构建 build
      exportMode?: ExportMode;
      // 导出参数
      exportParams?: AnyObject | ((this: any, data: MODEL) => AnyObject);
  
      // 补丁，可以对config进行处理
      patches?: Patch[];
  
      // 顶部布局参数，包含tab栏等
      tabs?: Tabs;
      
      // 顶部按钮数据
      action?: Action;
      
      // 搜索表单
      searchForm?: SearchForm<MODEL, SearchFormManager>;
  
      // 表格
      table: Table<MODEL, TableManager>;
  
      // 是否存在表单
      hasForm?: boolean;
  
      // 表单配置
      form?: Form<MODEL, FormManager<MODEL>>;
    }
```

### name
> 名称会用于表单标题的描述

### getImmediate
> 页面会在准备好之后立即获取列表，默认为true

### size
> 页面尺寸配置 large | default | small

### url
> 配置url后，如果增删改查的地址未配置，会自动使用该url

### urlMode
> api的请求参数构建模式，path | params

#### path
> 删除接口和详情接口会被自动构建成路径参数模式，如 /activity/2

#### params
> 参数正常参与请求

### isAutoAddButton
> 当存在addUrl时，自动为页面添加新增按钮

### getUrl
> 获取列表的接口地址，如果未设置，会使用配置的url

### getMethod
> 调用获取列表的接口地址的方法

### otherParams
> 请求列表数据时的额外参数，通常用于其他页面进来的场景

### getDataCallback
> 获取数据后的回调函数，可对数据进行处理，需要返回新列表
``` js
...
getDataCallback(list) {
  return list.map(item => {
    // 一些处理逻辑...
    return item
  })
}
...
```

### delUrl
> 删除数据的接口地址，如果未设置，会使用配置的url
### delMethod
> 删除接口地址的方法
### delKey
> 调用删除接口地址的参数字段名称，如id，配置delUrl后必须配置

### updUrl
> 更新数据的接口地址，如果未设置，会使用配置的url
### updMethod
> 更新接口地址的方法

### addUrl
> 新增数据的接口地址，如果未设置，会使用配置的url
### addMethod
> 新增接口地址的方法
### addButton
> 可对新增按钮进行配置
``` js

addButton: {
  text: "新增活动",
  props: {
    type: 'primary'
  }
}

```

### useDetail
> 使用详情模式进行表单数据绑定
### detailUrl
> 详情数据的接口地址，如果未设置，会使用配置的url
### detailMethod
> 详情接口地址的方法
### detailKey
> 调用详情接口地址的参数字段名称，如id，配置detailUrl后必须配置


### patches
> 支持对使用中的配置进行二次处理
``` js

patches: [
  function(config: Pagemodel.Config) {
    // 必须返回一个Pagemodel.Config
    return config
  }
]

```

### tabs
> 为组件添加一个tab栏，基于el-tabs

``` js

type OrderStatusEnum = {
  未处理,
  处理中,
  已处理,
}

tabs: {
  // 可选
  props: {
    // 配置eltabs的参数
    // 如 type: 'card'
  },
  // 根据panes构建el-tab-pane
  panes: [
    {
      label: '未处理订单',
      value: {
        status: OrderStatusEnum.未处理
      }
    },
    {
      label: '处理中订单',
      value: {
        status: OrderStatusEnum.处理中
      }
    },
    {
      label: '已处理订单',
      value: {
        status: OrderStatusEnum.已处理
      }
    }
  ]
}

```

### action
> 顶部栏的其他自定义功能按钮

``` js
...
action: {
  // 每个els的子项都是一个el-button
  els: [
    {
      text: '导入',
      props: {
        type: 'primary'
      },
      // 自定义事件，可以监听page-model组件的该事件进行处理
      event: 'import'
    }
  ]
}
...

const onImport = () => {
  // 导入的逻辑
}

<template>
  <page-model 
    :config="config"
    @import="onImport"
  />
</template>

```

### searchForm
> 搜索表单是page-model组件配置中重要的一项配置

```js 

searchForm: {
  // 搜索表单由el-form渲染
  // 每个els的子项都会渲染成一个el-form-item
  // 子项的eType属性会决定该组件的渲染形态
  // 常用的搜索表单子组件包括el-input、el-select、el-date-picker等
  els: [
    {
      // 组件类型为 el-input
      eType: 'el-input',
      // 搜索项的标签
      label: '活动名称',
      // 字段名称
      prop: 'activityName',
      // 组件el-input的props
      props: {
        placeholder: '活动名称',
      },
      // 组件el-input的事件
      events: {
        // 监听值的改变
        change() {
          // ...
        }
      },
      // 组件el-input的样式
      style: {
        // width: '200px' 配置input的宽度
      }
    }
  ]
}

```
### table
> PageModel中的表格基于el-table进行渲染

``` js
// table.els中的每一项的el都会渲染为el-table-column

table: {
  els: [
    {
      // 表头名称
      label: '活动名称',
      // 表头字段
      prop: 'activityName',
      // 表格列宽度
      width: 120,
      // 表格列最小宽度
      minWidth: 120,
      // 表格列的属性（可选）
      props: {
        // 超出宽度后显示省略
        showOverflowTooltip: true
      }
    }
  ]
}

```

#### 特殊单元格渲染
``` js
// el可以支持render函数进行渲染内容
// 当需要使用到渲染函数时，vue文件需要开启jsx或者tsx进行支持

<script setup lang='tsx'>
  ...

  table: {
    els: [
      {
        // 表头名称
        label: '活动名称',
        // 渲染函数
        // row为该行的数据模型
        renderFn(row) {
          if (row.status) {
            return (
              <span>{ row.activityName }</span>
            )
          } else {
            return (
              // 可以是
              <my-custom-component row={ row } />
            )
          }
        }
      }
    ]
  }

  ...

</script>

```

#### operate 表格操作栏
> 表格的操作栏是表格重要的一列
``` js

// 每一项操作栏的子项都是一个el-button
// 按钮类型有编辑按钮、删除按钮、拷贝按钮、自定义按钮

table: {
  operate: {
    els: [
      {
        text: '编辑',
        isEdit: true,
        props: {
          type: 'primary'
        }
      },
      {
        text: '删除',
        isDel: true,
        props: {
          type: 'danger'
        }
      },
      {
        text: '自定义',
        props: {
          type: 'danger'
        },
        // 自定义事件，可监听page-model
        event: 'custom-row-event'
      }
    ]
  }
}

const onCustomRowEvent = async (row: Model) => {
  // ...
  // 对列数据进行逻辑处理。例如启用活动
  await processdRequest.put('/activity/enabled', {
    activityId: row.id
  })
  // ...
}

<template>
  <page-model 
    :config="config"
    @custom-row-event="onCustomRowEvent"
  />
</template>

```

#### 表格的属性和事件

``` js

table: {
  // 配置el-table的props
  props: {
    border: true,
    stripe: true
  },
  // el-table的事件监听
  events: {
    select() {
      // 一些逻辑...
    }
  }
}

```

#### 表格的多选

``` js

table: {
  // 开启多选功能
  selectable: true,
  // 监听多选事件
  // 也可以通过组件的方法获取当前多选的数据
  events: {
    selectionChange(selections) {
      currentSelections = selections
    }
  },
  // 配置多选所需要的操作
  selectableButtons: [
    {
      text: '批量删除',
      event: 'multDelete',
      props: {
        type: 'primary',
      }
    }
  ]
}

```

#### 表格的按钮事件处理

``` js

// 第一种情况，操作栏按钮

// 操作栏按钮需要在子项配置event属性，可以在page-model组件监听该事件
table: {
  operate: {
    els: [
      {
        text: '设置',
        event: 'setting'
      }
    ]
  }
}

const onSetting = (row) => {
  // 处理事件逻辑
}

<page-model 
  :config="config"
  @setting="onSetting"
/>


// 第二种情况，表格渲染元素的按钮
// 在单元格中，可以监听jsx的事件，直接调用自己定义的函数
// 也可以通过renderFn中的this找到pagemodel组件向外提交一个自定义事。此处不进行说明
import { useRouter } from 'vue-router'

const router = useRouter()

const onSetting = (row) => {
  // 跳转其他页面
  router.push(`/pages/setting?pId=${row.id}`)
}

table: {
  els: [
    {
      label: '设置',
      renderFn(row) {
        
        // 监听按钮点击事件
        const onClick = () => {
          onSetting(row)
        }

        return (
          <el-button onClick={ onClick }>设置</el-button>
        )
      }
    }
  ]
}

```

### form表单
> form的配置决定新增和编辑表单的内容和样式

#### 表单的显示模式
> 表单分为两种显示模式， dialog | fullpage

```js

// dialog模式通常用于表单子项较少的情况进行显示，由el-dialog进行弹出显示
// dialog模式下，可以配置dialogProps进行弹窗的基础配置

form: {
  mode: 'dialog',
  dialogProps: {
    // 控制弹窗的宽度
    width: '500px'
  },
  els: [
    ...
  ]
}

// fullpage模式通常用于表单子项较多，需要进行基础排版的情况
// 整个表单页面会铺满page-model页面，有较大的空间进行显示
form: {
  mode: 'fullpage',
  els: [
    {
      eType: 'el-input',
      label: '活动名称',
      prop: 'activityName',
      // 通过el-col的span属性控制排版
      col: {
        span: 12
      },
      style: {
        width: '100%'
      }
    }
  ]
}
```

#### 表单的属性props
> 可对el-form的props进行配置

```js

// 具体查看element的el-form组件的props属性

form: {
  props: {
    // 控制label的宽度
    labelWidth: '120px'
    ...
  }
}

```

#### initialData表单数据的初始化
> 在使用el-radio-group组件时，通常需要对表单进行数据的初始化，保证单选组件被默认选中，可以使用initialData

```js

form: {
  // 新增表单打开时会默认选中未开始状态
  initialData: {
    status: OrderStatusEnum.未开始
  },
  els: [
    {
      eType: 'el-radio-group',
      label: '状态',
      prop: 'status',
      optionsData: {
        list: [
          {
            label: '未开始',
            value: OrderStatusEnum.未开始
          },
          {
            label: '进行中',
            value: OrderStatusEnum.进行中
          }
        ]
      }
    }
  ]
}

```

#### bindData

```js
form: {
  // 在表单组件渲染前，进行数据的预处理，支持同步和异步
  async bindData(formData) {
    // 处理经纬度
    // 将精度和维度合并成数组字段进行地图的渲染
    formData.location = [formData.lng, formData.lat]

    return formData
  }
}

```

#### beforeSubmit

```js
form: {
  // 在表单组件接口数据提交前，进行数据的预处理，支持同步和异步
  async beforeSubmit(formData) {
    // 将地图的数组数据处理成精度和维度
    [formData.lng, formData.lat] = formData.location
    return formData
  }
}

```

#### required表单校验

```js
form: {
  // required属性可以通过只填写字段进行数据的校验
  // required会被转化成rules进行el-form的校验
  required: [
    'activityName',
    'status'
  ]
}

```

#### els表单成员
``` js

// 表单组件的成员和搜索表单的成员的配置是一样的，基于el生成el-form-item，基于eType去渲染不同的表单组件内容
// 这里不再赘述

```

#### 自定义的表单组件
> 当内置的表单组件，如el-input无法满足需求时，可以自定义表单组件进行渲染
``` js

// 引入自定义的组件
import MyCustomInput from '@/components/MyCustomInput/index.vue'

form: {
  els: [
    {
      // 不再使用eType
      label: '活动名称',
      // 此处row为表单数据formData
      renderFn(row) {

        // 处理事件
        const onUpdateModelValue = (val: string) => {
          row.activityName = val
          // 还可以做一些其他的处理...
        }

        // 将一些复杂的逻辑隐藏在自定义组件中
        return (
          <MyCustomInput 
            modelValue={ row.activityName }
            onUpdate:modelValue={ onUpdateModelValue }
          />
        )
      }
    }
  ]
}

```
#### 表单的子项成员
> 每一个表单子项会负责渲染一块表单内容，当前page-model内置了如下几种表单子组件

##### el-input
``` js
{
  // 类型标记el-input
  eType: 'el-input',
  label: '活动名称',
  prop: 'activityName',
  props: {
    // el-input支持的props
  },
  events: {
    // el-input支持的emits
  }
}
```

##### el-cascader
``` js
{
  // 类型标记el-cascader
  eType: 'el-cascader',
  label: '活动名称',
  prop: 'activityName',
  props: {
    // el-cascader支持的props
    options: [],

    // 需要对组件赋数据源时，例：
    // 添加需要反射的数据源名称
    _reflect: 'cascaderList',
    // 从外部数据源取出数据后的赋值对象key
    _reflectChangeKey: 'options',
    // 显示的名称字段
    label: 'name',
    // 绑定的值字段
    value: 'id',
  },
  events: {
    // el-cascader支持的emits
  }
}
```

##### el-checkbox-group
``` js

form: {
  bindData(formData) {
    // 城市列表不存在，默认赋值空数组，保证组件正常渲染
    if (!formData.cityList) {
      formData.cityList = []
    }
    return formData
  },
  els: [
    {
      // 类型标记el-checkbox-group
      eType: 'el-checkbox-group',
      label: '活动城市',
      prop: 'cityList',
      props: {
        // el-checkbox-group支持的props
      },
      events: {
        // el-checkbox-group支持的emits
      },
      // 用optionsData属性为el-checkbox提供数据
      optionsData: {
        // list为渲染的选项的数据
        list: [],
        // 需要对组件赋数据源时，例： 
        // 添加需要反射的数据源名称
        _reflect: 'cityList',
        // 从外部数据源取出数据后的赋值对象key
        _reflectChangeKey: 'list', // 默认_reflectChangeKey为list
        // 显示的名称字段
        label: 'name',
        // 绑定的值字段
        value: 'id',
      }
    }
  ]
}
```

> 注：在组件值modelValue类型为数组或者对象的情况下，通常需要在组件渲染前为表单提供该字段对应的值类型进行初始化，否者可能会报错


##### el-radio-group
``` js
{
  // 类型标记el-radio-group
  eType: 'el-radio-group',
  label: '活动状态',
  prop: 'status',
  props: {
    // el-radio-group支持的props
  },
  events: {
    // el-radio-group支持的emits
  },
  // 用optionsData属性为el-radio提供数据
  optionsData: {
    // list为渲染的选项的数据
    list: [],
    // 需要对组件赋数据源时，例： 
    // 添加需要反射的数据源名称
    _reflect: 'statusList',
    // 从外部数据源取出数据后的赋值对象key
    _reflectChangeKey: 'list', // 默认_reflectChangeKey为list
    // 显示的名称字段
    label: 'name',
    // 绑定的值字段
    value: 'id',
  }
}
```

##### el-select
``` js
{
  // 类型标记el-select
  eType: 'el-select',
  label: '活动状态',
  prop: 'status',
  props: {
    // el-select支持的props
  },
  events: {
    // el-select支持的emits
  },
  // 用optionsData属性为el-option提供数据
  optionsData: {
    // list为渲染的选项的数据
    list: [],
    // 需要对组件赋数据源时，例： 
    // 添加需要反射的数据源名称
    _reflect: 'statusList',
    // 从外部数据源取出数据后的赋值对象key
    _reflectChangeKey: 'list', // 默认_reflectChangeKey为list
    // 显示的名称字段
    label: 'name',
    // 绑定的值字段
    value: 'id',
  }
}
```

##### el-color-picker
``` js
{
  // 类型标记el-color-picker
  eType: 'el-color-picker',
  label: '颜色',
  prop: 'color',
  props: {
    // el-color-picker支持的props
  },
  events: {
    // el-color-picker支持的emits
  }
}
```

##### el-date-picker
``` js
{
  // 类型标记el-date-picker
  eType: 'el-date-picker',
  label: '日期',
  prop: 'activityStartDate',
  props: {
    // el-date-picker支持的props
    // 通常日期组件需要进行值处理
    valueFormat: 'YYYY-MM-DD'
  },
  events: {
    // el-date-picker支持的emits
  }
}
```

##### a-map
``` js
form: {
  bindData(formData) {
    // 为地图组件构建一个由经纬度组成的虚拟字段
    if (formData.lng) {
      formData.location = [formData.lng, formData.lat]
    } else {
      // 不存在经纬度的情况下，提供一个默认的地图所在地
      formData.location = [116, 30]
    }
    return formData
  },
  beforeSubmit(formData) {
    // 表单数据提交前，对虚拟字段进行解构，赋值给经纬度字段
    [formData.lng, formData.lat] = formData.location
    return formData
  },
  els: [
    {
      // 类型标记a-map
      // a-map组件接受的modelValue类型为经纬度数组[number, number]
      eType: 'a-map',
      label: '选择所在地',
      prop: 'location',
      props: {
        // 地图高度
        height: number | string;
        // 地图宽度
        width: number | string;
        // 地图的标记
        markers: any[];
        // 使用搜索功能，需要amap正在使用的key支持搜索
        search: boolean;
        // 点击位置即为中心点位，同时出现marker
        useCenter: boolean;
      }
    }
  ]
}
```

##### city-picker
``` js
form: {
  els: [
    {
      // 类型标记city-picker
      // city-picker组件接受的modelValue类型为经纬度数组[number, number]
      eType: 'city-picker',
      label: '省市区',
      // 字段使用城市编码，组件会自动用该值去城市列表中去寻找列表进行绑定
      prop: 'cityCode',
      props: {
        // 省市区等级
        level: 1 | 2 | 3;
      },
      events: {
        change: (citys?: CityItem[] | string[]) => any;
        valueChange: (citys?: CityItem[]) => any;
      }
    }
  ]
}
```

##### editor
``` js
form: {
  els: [
    {
      // 类型标记editor
      eType: 'editor',
      label: '文章',
      // 字段使用城市编码，组件会自动用该值去城市列表中去寻找列表进行绑定
      prop: 'acticle',
      props: {
        // 工具栏
        toolbar: string[],
        // 插件
        plugins: string[],
        // 编辑器高度
        height: number | string,
        // 编辑器宽度
        width: number | string,
        // 使用qiniu上传
        qiniu: boolean;
        // 支持多图
        mult: boolean;
      },
      events: {
        // 上传失败
        uploadError: (file: any, error: Error, result: any) => any;
        // 上传成功
        uploadSuccess:(srcList: any) => any;
      }
    }
  ]
}
```

##### img-upload 图片上传
``` js
form: {
  els: [
    {
      // 类型标记img-upload
      eType: 'img-upload',
      label: '活动图片',
      prop: 'imgs',
      props: {
        // 上传文件的类型
        mode: 'image' | 'video' | 'file' | 'audio';
        // 多图上传时的链接的连接字符串
        sperator: string;
        // 请求地址
        url: string;
        // 提交的额外数据
        data: AnyObject;
        // 文件名称
        name: string;
        accept: string;
        // 允许多文件
        mult: boolean;
        // qiniu上传
        qiniu: boolean;
        // 宽度
        width: number;
        // 高度
        height: number;
        // 是否可拖拽
        drag: boolean;
        // 是否保持上传文件原名称（还是会进行处理）
        saveOriginName: boolean;
        // 上传过程的回调
        onUploadProgress: (index: number, progress: any) => any;
        // 上传成功的回调
        onUploadSuccess: (src: { data: string; file: File }[]) => any;
      },
    }
  ]
}
```


### hasForm 不需要表单
> 当不需要表单时，可以将hasForm置为false，将不会进行表单的渲染

# 外部数据源
> 外部数据源充当表单的数据列表进行渲染是很常见的场景

``` js

// 1 定义数据源
const reflections = reactive({
  list1: []
})

// 2 获取数据源
;(async () => {
  // 获取到列表1
  const result = await processdRequest.get('/list1', {
    pageIndex: 1,
    pageSize: 999
  })
  // 设置进数据源中
  reflections.list1 = result
})()

// 3 将数据源注入page-model
<page-model 
  :config="config"
  :reflections="reflections"
/>

// 4 config配置项和需要使用数据源的子项中进行配置

const config = defineConfig({
  // 开始配置
  reflect: true,
  table: {
    els: []
  },
  searchForm: {
    els: [
      {
        eType: 'el-select',
        prop: 'status',
        optionsData: {
          list: [],
          // 配置数据源中需要匹配的key名称,组件会将数据自动设置到list中去
          _reflect: 'list1',
          // 改变数据列表的名称,在cascader组件中需要配置为options
          _reflectChangeKey: 'list'
        }
      }
    ]
  },
  // 表单组件也可以共享数据源
  form: {
    els: [
      {
        eType: 'el-select',
        prop: 'status',
        optionsData: {
          list: [],
          _reflect: 'list1',
          _reflectChangeKey: 'list'
        }
      }
    ]
  }
})

```

# 组件方法
> page-model组件暴露了一些对内部操作的方法

## refreshTableData
> 刷新列表数据

## resetTableData
> 回到第一页刷新列表数据

## updateTableData
> 更新列表数据

## getTableData
> 获取当前的列表数据

## hideForm
> 隐藏表单

## showForm
> 显示表单

## updateSelection
> 更新多选数据

## getSelection
> 获取多选数据

## updateRadioData
> 更新单选数据

## getRadioData
> 获取单选数据

## clearSelection
> 清除多选数据

## showExport
> 显示导出工具
