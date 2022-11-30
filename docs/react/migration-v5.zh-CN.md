---
order: 8
title: 从 v4 到 v5
---

本文档将帮助你从 antd `4.x` 版本升级到 antd `5.x` 版本，如果你是 `3.x` 或者更老的版本，请先参考之前的[升级文档](https://4x.ant.design/docs/react/migration-v4-cn)升级到 4.x。

## 升级准备

1. 请先升级到 4.x 的最新版本，按照控制台 warning 信息移除/修改相关的 API。

## 5.0 有哪些不兼容的变化

### 设计规范调整

- 基础圆角调整，由统一的 `2px` 改为四级圆角，分别为 `2px` `4px` `6px` `8px`，分别应用于不同场景，比如默认尺寸的 Button 的圆角调整为了 `6px`。
- 主色调整，由 <div style="display: inline-block; width: 16px; height: 16px; border-radius: 4px; background: #1890ff; vertical-align: text-bottom;"></div> `#1890ff` 改为 <div style="display: inline-block; width: 16px; height: 16px; border-radius: 4px; background: #1677ff; vertical-align: text-bottom;"></div> `#1677ff`。
- 整体阴影调整，由原本的三级阴影调整为两级，分别用于常驻页面的组件（如 Card）和交互反馈（如 Dropdown）。
- 部分组件内间距调整。
- 整体去线框化。

### 技术调整

- 弃用 less，采用 CSS-in-JS，更好地支持动态主题。底层使用 [@ant-design/cssinjs](https://github.com/ant-design/cssinjs) 作为解决方案。
  - 所有 less 文件全部移除，less 变量不再支持透出。
  - 产物中不再包含 css 文件。由于 CSS-in-JS 支持按需引入，原本的 `antd/dist/antd.css` 也已经移除，如果需要重置一些基本样式请引入 `antd/dist/reset.css`。
- 移除 css variables 以及在此之上构筑的动态主题方案。
- 移除 `antd/es/locale` 目录，语言包可到 `antd/locale` 目录下寻找。
- 内置的时间库使用 Dayjs 替代 Moment.js，具体请查看 [使用自定义日期库](/docs/react/use-custom-date-library-cn/)。
- 不再支持 `babel-plugin-import`，CSS-in-JS 本身具有按需加载的能力，不再需要插件支持。

### 兼容性调整

- 不再支持 IE 浏览器。

#### 组件 API 调整

- 组件弹框的 classname API 统一为 `popupClassName`，`dropdownClassName` 等类似 API 都会被替换。

  - AutoComplete 组件
  - Cascader 组件
  - Select 组件
  - TreeSelect 组件
  - TimePicker 组件
  - DatePicker 组件
  - Mentions 组件

  ```diff
    import { Select } from 'antd';

    const App: React.FC = () => (
      <Select
  -     dropdownClassName="my-select-popup"
  +     popupClassName="my-select-popup"
      />
    );

    export default App;
  ```

- 组件弹框的受控可见 API 统一为 `open`，`visible` 等类似 API 都会被替换。

  - Drawer 组件 `visible` 变为 `open`。
  - Modal 组件 `visible` 变为 `open`。
  - Dropdown 组件 `visible` 变为 `open`。
  - Tooltip 组件 `visible` 变为 `open`。
  - Tag 组件 `visible` 已移除。
  - Slider 组件 `tooltip` 相关 API 收敛到 `tooltip` 属性中。
  - Table 组件 `filterDropdownVisible` 变为 `filterDropdownOpen`。

  ```diff
    import { Modal, Tag, Table, Slider } from 'antd';

    const App: React.FC = () => {
      const [visible, setVisible] = useState(true);

      return (
        <>
  -       <Modal visible={visible}>content</Modal>
  +       <Modal open={visible}>content</Modal>

  -       <Tag visible={visible}>tag</Tag>
  +       {visible && <Tag>tag</Tag>}

          <Table
            data={[]}
            columns={[
              {
                title: 'Name',
                dataIndex: 'name',
  -             filterDropdownVisible: visible,
  +             filterDropdownOpen: visible,
              }
            ]}
          />

  -       <Slider tooltipVisible={visible} />
  +       <Slider tooltip={{ open: visible }} />
        </>
      );
    }

    export default App;
  ```

- `getPopupContainer`: 所有的 `getPopupContainer` 都需要保证返回的是唯一的 div。React 18 concurrent 下会反复调用该方法。
- Upload List dom 结构变化。[#34528](https://github.com/ant-design/ant-design/pull/34528)
- Notification
  - 静态方法不再允许在 `open` 中动态设置 `prefixCls` `maxCount` `top` `bottom` `getContainer`，Notification 静态方法现在将只有一个实例。如果需要不同配置，请使用 `useNotification`。
  - `close` 改名为 `destroy`，和 message 保持一致。
- Drawer `style` 和 `className` 迁移至 Drawer 弹层区域上，原属性替换为 `rootClassName` 和 `rootStyle`。
- 4.x 中已经废弃的 `message.warn` 现在被彻底移除，请使用 `message.warning` 代替。

#### 组件重构与移除

- 移除 Comment 组件，移至 `@ant-design/compatible` 中维护。
- 移除 PageHeader 组件，移至 `@ant-design/pro-components` 中维护。

  ```diff
  - import { PageHeader, Comment, Input, Button } from 'antd';
  + import { Comment } from '@ant-design/compatible';
  + import { PageHeader } from '@ant-design/pro-layout';
  + import { Input, Button } from 'antd';

    const App: React.FC = () => (
      <>
        <PageHeader />
        <Comment />
      </>
    );

    export default App;
  ```

- BackTop 组件在 `5.0.0` 中废弃，移至 FloatButton 悬浮按钮中。如需使用，可以从 FloatButton 中引入。

  ```diff
  - import { BackTop } from 'antd';
  + import { FloatButton } from 'antd';

    const App: React.FC = () => (
      <>
  -     <BackTop />
  +     <FloatButton.BackTop />
      </>
    );

    export default App;
  ```

## 开始升级

通过 git 保存你的代码，然后按照上述文档进行依赖安装：

```bash
npm install --save antd@5.x
```

### less 迁移

如果你使用到了 antd 的 less 变量，通过兼容包将 v5 变量转译成 v4 版本，并通过 less-loader 注入：

```js
const { theme } = require('antd/lib');
const { convertLegacyToken } = require('@ant-design/compatible/lib');

const { defaultAlgorithm, defaultSeed } = theme;

const mapToken = defaultAlgorithm(defaultSeed);
const v4Token = convertLegacyToken(mapToken);

// Webpack Config
{
  loader: "less-loader",
  options: {
    lessOptions: {
      modifyVars: v4Token,
    },
  },
}
```

### 移除 babel-plugin-import

从 package.json 中移除 `babel-plugin-import`，并从 `.babelrc` 移除该插件：

```diff
"plugins": [
- ["import", { "libraryName": "antd", "libraryDirectory": "lib"}, "antd"],
]
```

Umi 用户可以在配置文件中关闭：

```diff
// config/config.ts or .umirc
export default {
  antd: {
-   import: true,
  },
};
```

## 遇到问题

如果您在升级过程中遇到了问题，请到 [GitHub issues](https://new-issue.ant.design/) 进行反馈。我们会尽快响应和相应改进这篇文档。