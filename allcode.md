# PureKeep 项目代码汇总

## 目录树

```
src/
├── components/
│   ├── base/
│   │   ├── Navbar.scss
│   │   ├── Navbar.tsx
│   │   ├── PKButton.scss
│   │   ├── PKButton.tsx
│   │   ├── CustomModal.scss
│   │   ├── CustomModal.tsx
│   │   ├── Drawer.scss
│   │   └── Drawer.tsx
│   └── business/
│       ├── DevTools.tsx
│       ├── EditorPanel.scss
│       ├── EditorPanel.tsx
│       ├── LedgerList.scss
│       ├── LedgerList.tsx
│       ├── PKKeyboard.scss
│       ├── PKKeyboard.tsx
│       └── TimePicker.tsx
├── constants/
│   └── categories.ts
├── hooks/
│   └── useNavInfo.ts
├── pages/
│   └── index/
│       ├── index.config.ts
│       ├── index.scss
│       └── index.tsx
├── store/
│   └── useLedgerStore.ts
├── styles/
│   ├── animations.scss
│   └── variables.scss
├── types/
│   └── ledger.d.ts
├── utils/
│   ├── currency.ts
│   ├── date.ts
│   ├── group.ts
│   └── id.ts
├── app.config.ts
├── app.scss
├── app.ts
└── index.html
```

## 根目录文件

### src/app.ts

```typescript
import { PropsWithChildren } from 'react'
import { useLaunch } from '@tarojs/taro'
import './app.scss'

function App({ children }: PropsWithChildren<any>) {

  useLaunch(() => {
    console.log('App launched.')
  })

  // children 是将要会渲染的页面
  return children
}

export default App
```

### src/app.scss

```scss
@import "./styles/variables.scss";
@import "./styles/animations.scss"; /* 新增 */

page {
background-color: var(--pk-bg);
color: var(--pk-text-main);
font-family: -apple-system, BlinkMacSystemFont, 'Helvetica Neue', Helvetica,
Segoe UI, Arial, Roboto, 'PingFang SC', 'miui', 'Hiragino Sans GB', 'Microsoft Yahei',
sans-serif;
font-size: 28px; /* Taro 默认以 750px 设计稿为准，这里对应 14px */
line-height: 1.5;
box-sizing: border-box;
}

/* 简单的 Reset */
view, text, image, navigator {
box-sizing: border-box;
}

/* 追加到 app.scss */
input {
font-family: inherit; /* 继承全局设置的莫兰迪字体栈 */
}
```

### src/app.config.ts

```typescript
export default defineAppConfig({ 
  pages: [ 
    'pages/index/index' 
  ], 
  window: { 
    backgroundTextStyle: 'light', 
    navigationBarBackgroundColor: '#F9F7F5', // 与背景色一致 
    navigationBarTitleText: 'PureKeep', 
    navigationBarTextStyle: 'black', 
    navigationStyle: 'custom' // <--- 关键！开启自定义导航栏模式 
  } 
  })
```

### src/index.html

```html
<!DOCTYPE html>
<html>
<head>
  <meta content="text/html; charset=utf-8" http-equiv="Content-Type">
  <meta content="width=device-width,initial-scale=1,user-scalable=no" name="viewport">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-touch-fullscreen" content="yes">
  <meta name="format-detection" content="telephone=no,address=no">
  <meta name="apple-mobile-web-app-status-bar-style" content="white">
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" >
  <title>purekeepv1</title>
  <script><%= htmlWebpackPlugin.options.script %></script>
</head>
<body>
  <div id="app"></div>
</body>
</html>
```

## components/base 目录

### src/components/base/Navbar.tsx

```typescript
import { View, Text } from '@tarojs/components'; 
 import { useNavInfo } from '../../hooks/useNavInfo'; 
 import './Navbar.scss'; 

 interface NavbarProps { 
 title?: string; 
 onMenuClick?: () => void; // 🔥 新增回调 
 } 

export const Navbar = ({ title = 'PureKeep', onMenuClick }: NavbarProps) => { 
 const { statusBarHeight, navContentHeight, navHeight, menuButtonWidth } = useNavInfo(); 

 return ( 
 <View className="pk-navbar" style={{ height: `${navHeight}px` }}> 
 <View style={{ height: `${statusBarHeight}px` }} /> 
 <View 
 className="pk-navbar__content" 
 style={{ 
 height: `${navContentHeight}px`, 
 paddingRight: `${menuButtonWidth}px` 
 }} 
 > 
 {/* 🔥 左侧菜单按钮 (三道杠) */} 
 {onMenuClick && ( 
 <View className="pk-navbar__menu-btn" onClick={onMenuClick}> 
 <View className="bar" /> 
 <View className="bar" /> 
 </View> 
 )} 

 <Text className="pk-navbar__title">{title}</Text> 
 </View> 
 </View> 
 ); 
 };
```

### src/components/base/Navbar.scss

```scss
.pk-navbar { 
 position: fixed; 
 top: 0; 
 left: 0; 
 width: 100%; 
 z-index: 1000; 
 background-color: var(--pk-bg); /* 与页面背景融合 */ 

 /* 只有页面滚动时才需要加这行阴影，初期先不加，保持极其干净 */ 
 /* box-shadow: 0 1px 0 rgba(0,0,0,0.05); */ 

 &__content { 
 display: flex; 
 align-items: center; 
 padding-left: 32px; /* 左侧边距 */ 

 /* 在 .pk-navbar__content 内部添加 */ 
 .pk-navbar__menu-btn { 
 width: 40px; 
 height: 40px; 
 display: flex; 
 flex-direction: column; 
 justify-content: center; 
 gap: 6px; 
 margin-right: 20px; /* 标题左侧间距 */ 
 padding: 5px; /* 增大点击热区 */ 

 .bar { 
 width: 28px; 
 height: 3px; 
 background-color: var(--pk-text-main); 
 border-radius: 2px; 
 } 
 } 
 } 

 &__title { 
 font-size: 34px; /* 类似原生标题大小 */ 
 font-weight: 600; 
 color: var(--pk-text-main); 
 } 
 }
```

### src/components/base/PKButton.tsx

```typescript
import { View, Text } from '@tarojs/components';
import classNames from 'classnames';
import './PKButton.scss';

interface PKButtonProps {
children: React.ReactNode;
onClick?: () => void;
type?: 'primary' | 'secondary' | 'danger' | 'ghost'; // 变体
size?: 'sm' | 'md' | 'lg';
fullWidth?: boolean;
disabled?: boolean;
}

export const PKButton = ({
children,
onClick,
type = 'primary',
size = 'md',
fullWidth = false,
disabled = false,
}: PKButtonProps) => {

// 使用 classNames 动态合并类名
const btnClass = classNames('pk-btn', `pk-btn--${type}`, `pk-btn--${size}`, {
'pk-btn--full': fullWidth,
'pk-btn--disabled': disabled,
});

return (
<View
className={btnClass}
onClick={!disabled ? onClick : undefined}
hoverClass="pk-btn--hover"
hoverStayTime={100}
>
<Text className="pk-btn__text">{children}</Text>
</View>
);
};
```

### src/components/base/PKButton.scss

```scss
.pk-btn {

	display: flex;

	align-items: center;

	justify-content: center;

	border-radius: var(--pk-radius-md);

	transition: all 0.2s ease;

	// 尺寸

	&--sm { height: 60px; padding: 0 24px; font-size: 24px; }

	&--md { height: 88px; padding: 0 32px; font-size: 32px; font-weight: 600; }

	&--lg { height: 100px; padding: 0 48px; font-size: 36px; font-weight: 600; }

	&--full { width: 100%; }

	// 变体 - Primary (主色)

	&--primary {

		background: var(--pk-primary);

		color: var(--pk-white);

		&.pk-btn--hover { opacity: 0.9; transform: scale(0.98); }

	}

	// 变体 - Secondary (次级)

	&--secondary {

		background: var(--pk-primary-bg);

		color: var(--pk-primary);

		&.pk-btn--hover { background: darken(#E8F1F2, 5%); }

	}

	// 变体 - Danger (删除/支出)

	&--danger {

		background: rgba(212, 165, 165, 0.15); // --pk-expense with opacity

		color: var(--pk-expense);

	}

	// 禁用态

	&--disabled {

		opacity: 0.5;

		pointer-events: none;

		background: #ccc;

		color: #fff;

	}

}
```

### src/components/base/CustomModal.tsx

```typescript
import { View, Text } from '@tarojs/components';
import { ReactNode } from 'react';
import classNames from 'classnames';
import './CustomModal.scss';

interface CustomModalProps {
  visible: boolean;
  title: string;
  children: ReactNode;
  onClose?: () => void;
  onConfirm?: () => void;
}

export const CustomModal = ({ visible, title, children, onClose, onConfirm }: CustomModalProps) => {
  if (!visible) return null;

  return (
    <View className="pk-modal-mask">
      <View className="pk-modal-container anim-enter">
        <View className="pk-modal-header">
          <Text className="title">{title}</Text>
        </View>
        <View className="pk-modal-content">
          {children}
        </View>
        <View className="pk-modal-footer">
          <View className="btn cancel" onClick={onClose}>取消</View>
          <View className="btn confirm" onClick={onConfirm}>确定</View>
        </View>
      </View>
    </View>
  );
};
```

### src/components/base/CustomModal.scss

```scss
.pk-modal-mask {
 position: fixed;
 top: 0; left: 0; right: 0; bottom: 0;
 background: rgba(0, 0, 0, 0.4);
 z-index: 5000;
 display: flex;
 align-items: center;
 justify-content: center;
}

.pk-modal-container {
 width: 600px; /* 宽一点 */
 background: #fff;
 border-radius: var(--pk-radius-lg);
 overflow: hidden;
 box-shadow: 0 10px 30px rgba(0,0,0,0.1);

 .pk-modal-header {
 padding: 30px 0;
 text-align: center;
 border-bottom: 1px solid var(--pk-border);
 .title { font-size: 32px; font-weight: 600; color: var(--pk-text-main); }
 }

 .pk-modal-content {
 padding: 30px;
 min-height: 200px;
 }

 .pk-modal-footer {
 display: flex;
 border-top: 1px solid var(--pk-border);
 height: 100px;

 .btn {
 flex: 1;
 display: flex;
 align-items: center;
 justify-content: center;
 font-size: 32px;
 &:active { background: #f9f9f9; }
 }
 .cancel { color: var(--pk-text-sub); border-right: 1px solid var(--pk-border); }
 .confirm { color: var(--pk-primary); font-weight: 600; }
 }
}
```

### src/components/base/Drawer.tsx

```typescript
import { View } from '@tarojs/components';
import classNames from 'classnames';
import './Drawer.scss';

interface DrawerProps {
 visible: boolean;
 onClose: () => void;
 children?: React.ReactNode;
}

export const Drawer = ({ visible, onClose, children }: DrawerProps) => {
 return (
 <View className={classNames('pk-drawer', { visible })}>
 <View className="pk-drawer__mask" onClick={onClose} />
 <View className="pk-drawer__content">
 {/* 这里未来放置 腾讯云/WebDAV 同步入口 */}
 {children}
 </View>
 </View>
 );
};
```

### src/components/base/Drawer.scss

```scss
.pk-drawer {
 position: fixed;
 top: 0; left: 0; right: 0; bottom: 0;
 z-index: 3000;
 visibility: hidden;
 transition: visibility 0.3s;

 &.visible {
 visibility: visible;
 .pk-drawer__mask { opacity: 1; }
 .pk-drawer__content { transform: translateX(0); }
 }

 &__mask {
 position: absolute;
 top: 0; left: 0; width: 100%; height: 100%;
 background: rgba(0,0,0,0.5);
 opacity: 0;
 transition: opacity 0.3s;
 }

 &__content {
 position: absolute;
 top: 0; left: 0;
 width: 70%; /* 抽屉宽度 */
 height: 100%;
 background: var(--pk-bg);
 transform: translateX(-100%);
 transition: transform 0.3s cubic-bezier(0.23, 1, 0.32, 1);
 padding: 50px 30px;
 box-shadow: 2px 0 10px rgba(0,0,0,0.05);
 }
}
```

## components/business 目录

### src/components/business/DevTools.tsx

```typescript
import { View, Button } from '@tarojs/components';
import Taro from '@tarojs/taro';
import dayjs from 'dayjs';
import { useLedgerStore } from '../../store/useLedgerStore';
import { DEFAULT_CATEGORIES } from '../../constants/categories'; // 引入分类配置

export const DevTools = () => {
  const { batchAddLedgers, clearAll } = useLedgerStore();

  const handleGenerate = () => {
    // 1. 设定时间范围
    const startObj = dayjs('2025-01-01');
    const endObj = dayjs('2026-02-05');
    const startTs = startObj.valueOf();
    const diff = endObj.diff(startObj);

    const mockData = [];

    // 2. 循环生成 100 条
    for (let i = 0; i < 100; i++) {
      // 随机时间
      const randomDate = startTs + Math.floor(Math.random() * diff);

      // 随机分类
      const randomCat = DEFAULT_CATEGORIES[Math.floor(Math.random() * DEFAULT_CATEGORIES.length)];

      // 随机金额 (1.00 - 500.00) -> 存为分 (100 - 50000)
      const randomAmount = Math.floor(Math.random() * 49900) + 100;

      // 随机备注 (30% 概率有备注)
      const hasNote = Math.random() > 0.7;
      const notes = ['全家', '打车回家', '超市采购', '请客吃饭', '水电费', '充话费'];
      const randomNote = hasNote ? notes[Math.floor(Math.random() * notes.length)] : '';

      mockData.push({
        amount: randomAmount,
        type: randomCat.type, // 自动匹配分类的收支类型
        categoryId: randomCat.id,
        note: randomNote,
        date: randomDate,
      });
    }

    // 3. 提交到 Store
    batchAddLedgers(mockData);

    Taro.showToast({ title: '已生成100条数据', icon: 'success' });
  };

  return (
    <View style={{ padding: '20px', borderTop: '1px dashed #ccc', marginTop: '30px' }}>
      <View style={{ marginBottom: '10px', fontSize: '14px', color: '#999', textAlign: 'center' }}>
        🚧 开发测试工具
      </View>
      <Button
        onClick={handleGenerate}
        style={{ marginBottom: '10px', backgroundColor: '#7A9E9F', color: '#fff' }}
      >
        生成 2025-2026 测试数据 (100条)
      </Button>
      <Button
        onClick={clearAll}
        type="warn"
        style={{ backgroundColor: '#D4A5A5', color: '#fff' }}
      >
        清空所有数据
      </Button>
    </View>
  );
};
```

### src/components/business/EditorPanel.tsx

```typescript
import { View, Text, ScrollView, Input } from '@tarojs/components';
import Taro from '@tarojs/taro';
import { useState, useEffect } from 'react';
import classNames from 'classnames';
import dayjs from 'dayjs';
import { PKKeyboard } from './PKKeyboard';
import { TimePicker } from './TimePicker'; // 引入新组件
import { DEFAULT_CATEGORIES } from '../../constants/categories';
import { useLedgerStore } from '../../store/useLedgerStore';
import { formatTime } from '../../utils/date';
import './EditorPanel.scss';

interface EditorPanelProps {
  visible: boolean;
  onClose: () => void;
}

export const EditorPanel = ({ visible, onClose }: EditorPanelProps) => {
  const { addLedger, draft, saveDraft, clearDraft } = useLedgerStore();

  const [closing, setClosing] = useState(false);
  const [type, setType] = useState<1 | 2>(1);
  const [amountStr, setAmountStr] = useState('');
  const [selectedCatId, setSelectedCatId] = useState(''); // 🔥 需求#8: 默认为空，强制用户选择
  const [note, setNote] = useState('');
  const [date, setDate] = useState(Date.now()); // 新增日期状态

  // UI 状态
  const [isNoteFocused, setIsNoteFocused] = useState(false);
  const [showTimePicker, setShowTimePicker] = useState(false); // 🔥 需求#4: 控制弹窗

  // 初始化：处理草稿与重置
  useEffect(() => {
    if (visible) {
      if (draft) {
        // 🔥 需求#6: 恢复草稿
        setType(draft.type);
        setAmountStr(draft.amountStr);
        setSelectedCatId(draft.categoryId);
        setNote(draft.note);
        setDate(draft.date);
        console.log('📝 恢复草稿');
      } else {
        // 全新开始
        setAmountStr('');
        setNote('');
        setDate(Date.now());
        setSelectedCatId(''); // 确保为空
      }
    }
  }, [visible, draft]);

  // 统一关闭逻辑 (包含保存草稿)
  const handleCloseWrapper = () => {
    // 🔥 需求#5: 如果输入框聚焦，先失焦，不关闭面板
    if (isNoteFocused) {
      setIsNoteFocused(false);
      return;
    }

    // 保存草稿 (只有当有输入金额或选择了分类时才存，避免存空数据)
    if (amountStr || selectedCatId) {
      saveDraft({
        amountStr,
        type,
        categoryId: selectedCatId,
        note,
        date
      });
    } else {
      clearDraft();
    }

    setClosing(true);
    setTimeout(() => {
      setClosing(false);
      onClose();
    }, 200);
  };

  const triggerHaptic = () => {
    Taro.vibrateShort({ type: 'light' }).catch(() => {});
  };

  // 🔥 需求#7: 限制9位
  const handlePress = (val: string) => {
    triggerHaptic();
    if (val === '.' && amountStr.includes('.')) return;
    if (val === '.' && amountStr === '') { setAmountStr('0.'); return; }
    if (amountStr.includes('.') && amountStr.split('.')[1].length >= 2) return;
    // 长度限制
    if (amountStr.replace('.', '').length >= 9) return;

    setAmountStr(prev => prev + val);
  };

  const handleDelete = () => {
    triggerHaptic();
    setAmountStr(prev => prev.slice(0, -1));
  };

  const handleConfirm = () => {
    // 1. 金额校验
    const amountVal = parseFloat(amountStr);
    if (!amountStr || isNaN(amountVal) || amountVal === 0) return;

    // 2. 🔥 需求#8: 必选分类校验
    if (!selectedCatId) {
      Taro.showToast({ title: '请选择一个分类', icon: 'none' });
      return;
    }

    addLedger({
      amount: Math.round(amountVal * 100),
      type,
      categoryId: selectedCatId,
      note: note.trim(),
      date: date // 使用选中的时间
    });

    // 提交成功，清空草稿
    clearDraft();

    // 🔥 需求#1: 弱提示并关闭
    Taro.showToast({ title: '记账成功', icon: 'success', duration: 1500 });

    // 关闭面板 (这里不走 handleCloseWrapper，因为已经 clearDraft 了，直接关即可)
    setClosing(true);
    setTimeout(() => {
      setClosing(false);
      onClose();
    }, 200);
  };

  if (!visible && !closing) return null;

  const currentCategories = DEFAULT_CATEGORIES.filter(c => c.type === type);
  const isValidAmount = amountStr && parseFloat(amountStr) > 0;

  return (
    <View className="pk-editor">
      <View
        className={classNames('pk-editor__mask', closing ? 'anim-exit' : 'anim-enter')}
        onClick={handleCloseWrapper} // 点击遮罩走智能关闭逻辑
      />

      <View className={classNames('pk-editor__panel', closing ? 'panel-exit' : 'panel-enter')}>

        {/* 顶部操作栏 */}
        <View className="pk-editor__header">
          <View className="left-action">
            {/* 支出/收入切换 */}
            <View className="pk-editor__tabs">
              <View className={classNames('pk-tab', { active: type === 1 })} onClick={() => setType(1)}>支出</View>
              <View className={classNames('pk-tab', { active: type === 2 })} onClick={() => setType(2)}>收入</View>
            </View>

            {/* 🔥 需求#4: 时间显示与触发 */}
            <View className="date-tag" onClick={() => setShowTimePicker(true)}>
              <Text>{dayjs(date).format('MM/DD HH:mm')}</Text>
            </View>
          </View>

          <View className="pk-editor__amount">
            <Text className="symbol">¥</Text>
            <Text className="value">{amountStr || '0.00'}</Text>
          </View>
        </View>

        {/* 分类选择区 */}
        <ScrollView scrollX className="pk-editor__cats" showScrollbar={false}>
          <View className="pk-editor__cats-inner">
            {currentCategories.map(cat => (
              <View
                key={cat.id}
                className={classNames('pk-cat-item', { active: selectedCatId === cat.id })}
                onClick={() => { triggerHaptic(); setSelectedCatId(cat.id); }}
              >
                <View className="pk-cat-icon" style={{ color: cat.color }}>
                  <View style={{width: 20, height: 20, borderRadius: '50%', border: '2px solid currentColor'}} />
                </View>
                <Text className="pk-cat-name">{cat.name}</Text>
              </View>
            ))}
          </View>
        </ScrollView>

        {/* 备注输入 */}
        <View className="pk-editor__note-area">
          <View className={classNames('pk-note-bubble', { 'focused': isNoteFocused, 'has-value': note.length > 0 })}>
            <View className="icon">📝</View>
            <Input
              className="input"
              value={note}
              onInput={e => setNote(e.detail.value)}
              onFocus={() => setIsNoteFocused(true)}
              onBlur={() => setIsNoteFocused(false)}
              placeholder="添加备注..."
              placeholderClass="placeholder"
              cursorSpacing={20}
              focus={isNoteFocused} // 受控 focus
            />
          </View>
        </View>

        <PKKeyboard
          onPress={handlePress}
          onDelete={handleDelete}
          onConfirm={handleConfirm}
          disabled={!isValidAmount}
        />
      </View>

      {/* 嵌入时间选择弹窗 */}
      <TimePicker
        visible={showTimePicker}
        initialDate={date}
        onClose={() => setShowTimePicker(false)}
        onConfirm={(ts) => { setDate(ts); setShowTimePicker(false); }}
      />
    </View>
  );
};
```

### src/components/business/EditorPanel.scss

```scss
.pk-editor {
 position: fixed;
 top: 0; left: 0; right: 0; bottom: 0;
 z-index: 2000;
 display: flex;
 flex-direction: column;
 justify-content: flex-end;

 &__mask {
 position: absolute;
 top: 0; left: 0; width: 100%; height: 100%;
 background: rgba(0, 0, 0, 0.2); /* 稍微调淡一点遮罩，配合动画更清爽 */
 backdrop-filter: blur(4px);
 }

 &__panel {
 position: relative;
 z-index: 2001;
 background: var(--pk-bg);
 border-radius: var(--pk-radius-lg) var(--pk-radius-lg) 0 0;
 overflow: hidden;
 box-shadow: 0 -4px 20px rgba(0, 0, 0, 0.05);

 /* --- 核心修改：设置变换原点 --- */
 /* 计算公式：
 X轴: 100% - (按钮Right边距 + 按钮半径)
 Y轴: 100% - (按钮Bottom边距 + 按钮半径 - 面板自身可能存在的偏移)
 这里目测估算一个大概位置，视觉上会感觉是从按钮里出来的
 */
 transform-origin: calc(100% - 95px) calc(100% - 105px);

 /* 优化：为了防止动画过程中内容溢出变丑，
 我们可以给面板加一个临时的圆角过渡，但在 CSS 中做 scale
 通常保持默认形状变化就很好看了。
 */
 }

  &__header {
    padding: 30px 32px 10px;
    display: flex;
    justify-content: space-between;
    align-items: flex-end;

    .left-action {
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    .date-tag {
      background: #F2F4F5;
      padding: 6px 16px;
      border-radius: 20px;
      font-size: 24px;
      color: var(--pk-text-main);
      align-self: flex-start;
      display: flex;
      align-items: center;
      &:active { opacity: 0.7; }
    }
  }

  &__tabs {
    display: flex;
    gap: 20px;
    /* 移除之前的 flex 布局属性，交给父级控制 */

    .pk-tab {
      font-size: 30px;
      color: var(--pk-text-sub);
      transition: all 0.2s;
      &.active {
        color: var(--pk-text-main);
        font-weight: 600;
        transform: scale(1.05);
      }
    }
  }

  &__amount {
    font-family: 'DIN Alternate', monospace; /* 数字字体 */
    color: var(--pk-primary);

    .symbol { font-size: 30px; margin-right: 4px; }
    .value { font-size: 60px; line-height: 1; font-weight: bold; }
  }

  /* 分类区 */
  &__cats {
    width: 100%;
    height: 160px;

    &-inner {
      display: flex;
      padding: 20px 32px;
      gap: 40px;
    }

    .pk-cat-item {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 10px;
      opacity: 0.6;
      transition: all 0.2s;

      &.active {
        opacity: 1;
        transform: translateY(-5px);
        .pk-cat-icon { background: var(--pk-white); box-shadow: 0 4px 10px rgba(0,0,0,0.05); }
      }
    }

    .pk-cat-icon {
      width: 80px;
      height: 80px;
      background: rgba(255,255,255,0.5);
      border-radius: 30px; /* 超椭圆 */
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .pk-cat-name { font-size: 24px; color: var(--pk-text-main); }
  }

  /* 原来的 __note 样式删掉或替换为下面的 __note-area */ 
 &__note-area {
 padding: 0 32px 20px; /* 下方留出一点距离给键盘 */ 
 display: flex;
 align-items: center;
 }

 .pk-note-bubble {
 display: flex;
 align-items: center;
 background: #F2F4F5; /* 默认灰色背景 */ 
 padding: 12px 20px;
 border-radius: 30px; /* 胶囊圆角 */ 
 transition: all 0.3s ease;
 width: 100%; /* 默认撑满，或者你可以设置 auto */ 
 border: 1px solid transparent;

 .icon {
 font-size: 24px;
 margin-right: 10px;
 opacity: 0.5;
 }

 .input {
 flex: 1;
 font-size: 28px;
 color: var(--pk-text-main);
 height: 40px;
 min-height: 40px;
 }

 .placeholder {
 color: #BBB;
 }

 /* 🔥 聚焦态：背景变亮，加边框 */ 
 &.focused {
 background: #FFF;
 border-color: var(--pk-primary);
 box-shadow: 0 2px 8px rgba(122, 158, 159, 0.15);

 .icon { opacity: 1; }
 }

 /* 🔥 有值态：保持一点高亮感 */ 
 &.has-value {
 background: #F9FCFC;
 color: var(--pk-text-main);
 }
 }
}
```

### src/components/business/LedgerList.tsx

```typescript
import { View, Text } from '@tarojs/components';
import { useMemo } from 'react';
import { LedgerItem } from '../../types/ledger';
import { groupLedgersByDay, getCategoryById } from '../../utils/group';
import { formatCurrency } from '../../utils/currency';
import { formatTime } from '../../utils/date';
import './LedgerList.scss';

interface LedgerListProps {
  ledgers: LedgerItem[];
}

export const LedgerList = ({ ledgers }: LedgerListProps) => {
  const groups = useMemo(() => groupLedgersByDay(ledgers), [ledgers]);

  if (ledgers.length === 0) {
    return (
      <View className="pk-empty">
        <View className="pk-empty__dot" />
        <Text>暂无账单，开启极简生活</Text>
      </View>
    );
  }

  return (
    <View className="pk-list">
      {groups.map(group => (
        <View key={group.dateStr} className="pk-list__group">

          {/* 日头 (Sticky) */}
          <View className="pk-list__header sticky-header">
            <View className="left">
              <Text className="date">{group.dateStr}</Text>
              <Text className="label">{group.dayLabel}</Text>
            </View>
            <View className="right">
              {/* 🔥 需求#2: 移除硬编码的 '收', '支' 文字，保持极简，只保留符号和颜色区分 */}
              {group.totalIncome > 0 && <Text className="income">+{formatCurrency(group.totalIncome)}</Text>}
              {group.totalExpense > 0 && <Text className="expense">-{formatCurrency(group.totalExpense)}</Text>}
            </View>
          </View>

          {/* 账目列表 */}
          <View className="pk-list__items">
            {group.items.map(item => {
              const cat = getCategoryById(item.categoryId);

              // 🔥 需求#3: 核心显示逻辑修改
              // 有备注显示 "分类 | 备注"，无备注显示 "分类"
              const hasNote = item.note && item.note.trim().length > 0;
              const displayTitle = hasNote ? `${cat.name} | ${item.note}` : cat.name;

              return (
                <View key={item._id} className="pk-item">
                  <View className="pk-item__icon" style={{ backgroundColor: `${cat.color}20` }}>
                    {/* 暂时用圆点代替图标，后续你可替换为 SVG */}
                    <View style={{ width: 10, height: 10, borderRadius: '50%', backgroundColor: cat.color }} />
                  </View>

                  <View className="pk-item__content">
                    <View className="pk-item__info">
                      {/* 标题 */}
                      <Text className="pk-item__title">{displayTitle}</Text>
                      {/* 副标题：只显示具体时间 */}
                      <Text className="pk-item__desc">{formatTime(item.date)}</Text>
                    </View>

                    <View className="pk-item__amount-wrap">
                      <Text className={`amount ${item.type === 1 ? 'exp' : 'inc'}`}>
                        {item.type === 1 ? '-' : '+'}{formatCurrency(item.amount).replace('¥', '')}
                      </Text>
                    </View>

                    <View className="divider" />
                  </View>
                </View>
              );
            })}
          </View>
        </View>
      ))}
    </View>
  );
};
```

### src/components/business/LedgerList.scss

```scss
/* 保持 pk-empty 不变 */
.pk-empty {
  padding-top: 200px;
  display: flex;
  flex-direction: column;
  align-items: center;
  color: var(--pk-text-sub);

  &__dot {
    width: 20px; height: 20px;
    background: var(--pk-border);
    border-radius: 50%;
    margin-bottom: 20px;
  }
}

.pk-list {
  &__group {
    margin-bottom: 24px;
    /* 避免 sticky header 遮挡上一组的内容，虽然有背景色，但保持间距更好 */
    position: relative;
  }

  &__header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 16px 32px; /* 增加一点内边距，因为现在是吸顶的 */
    color: var(--pk-text-sub);
    font-size: 24px;

    /* 核心：Sticky 吸顶实现 */
    &.sticky-header {
      position: sticky;
      /* 这里使用了我们在 index.tsx 里注入的变量，如果未注入则默认 0 */
      top: var(--pk-nav-height, 0px);
      z-index: 10;
      background-color: var(--pk-bg); /* 必须有背景色，否则文字会透过去 */

      /* 可选：加个极其微弱的阴影增加层次感 */
      // box-shadow: 0 1px 2px rgba(0,0,0,0.02);
    }

    .left {
      display: flex;
      gap: 10px;
      .date { font-weight: bold; color: var(--pk-text-main); }
    }

    .right {
      display: flex;
      gap: 16px;
      .income { color: var(--pk-income); }
      .expense { color: var(--pk-text-sub); }
    }
  }

  &__items {
    background: #fff;
    border-radius: var(--pk-radius-md);
    padding: 0 24px;
    box-shadow: 0 2px 12px rgba(0,0,0,0.02);
    margin: 0 32px; /* 列表卡片左右缩进 */
  }
}

.pk-item {
  display: flex;
  align-items: center;
  height: 120px; /* 高度增加，适应双行 */

  &__icon {
    width: 72px; height: 72px; /* 图标稍大一点点 */
    border-radius: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 24px;
    flex-shrink: 0;
  }

  &__content {
    flex: 1;
    height: 100%;
    display: flex;
    align-items: center; /* 垂直居中 */
    justify-content: space-between;
    position: relative;
    overflow: hidden; /* 防止溢出 */

    /* 下划线 */
    .divider {
      position: absolute;
      bottom: 0; right: 0; left: 0;
      height: 1px;
      background: var(--pk-bg);
      opacity: 0.6;
    }
  }

  /* 新增：中间文本信息区 */
  &__info {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: center;
    overflow: hidden; /* 关键：为了让 text-overflow 生效 */
    margin-right: 20px; /* 与金额保持距离 */
  }

  &__title {
    font-size: 30px;
    color: var(--pk-text-main);
    font-weight: 500;
    margin-bottom: 6px;

    /* 单行截断 */
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    max-width: 400px; /* 限制最大宽度，防止挤压金额 */
  }

  &__desc {
    font-size: 22px;
    color: var(--pk-text-sub);

    /* 单行截断 */
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  /* 金额区 */
  &__amount-wrap {
    flex-shrink: 0; /* 防止金额被压缩 */
    text-align: right;

    .amount {
      font-size: 32px;
      font-weight: 600;
      font-family: 'DIN Alternate', sans-serif;

      &.exp { color: var(--pk-text-main); }
      &.inc { color: var(--pk-income); }
    }
  }

  /* 最后一个元素的分割线隐藏 */
  &:last-child .divider { display: none; }
}
```

### src/components/business/PKKeyboard.tsx

```typescript
import { View } from '@tarojs/components'; 
 import classNames from 'classnames'; 
 import './PKKeyboard.scss';

 const DEL_ICON = '←'; 

 interface PKKeyboardProps { 
 onPress: (val: string) => void; 
 onDelete: () => void; 
 onConfirm: () => void; 
 confirmText?: string; 
 disabled?: boolean; // 🔥 新增：接收禁用状态 
 } 

export const PKKeyboard = ({ 
 onPress, 
 onDelete, 
 onConfirm, 
 confirmText = '完成', 
 disabled = false, // 🔥 默认 false 
 }: PKKeyboardProps) => { 
 
 const renderKey = (val: string, label?: string) => ( 
 <View 
 className="pk-kb__key" 
 hoverClass="pk-kb__key--hover" 
 hoverStayTime={50} 
 onClick={() => onPress(val)} 
 > 
 {label || val} 
 </View> 
 );
 
 return ( 
 <View className="pk-kb"> 
 <View className="pk-kb__main"> 
 <View className="pk-kb__row"> 
 {renderKey('1')} {renderKey('2')} {renderKey('3')} 
 </View> 
 <View className="pk-kb__row"> 
 {renderKey('4')} {renderKey('5')} {renderKey('6')} 
 </View> 
 <View className="pk-kb__row"> 
 {renderKey('7')} {renderKey('8')} {renderKey('9')} 
 </View> 
 <View className="pk-kb__row"> 
 {renderKey('.')} {renderKey('0')} {renderKey('00')} 
 {/* '00' 是为了填补空缺，或者你可以把0做宽一点 */} 
 </View> 
 </View> 
 
 <View className="pk-kb__actions"> 
 {/* 删除键 */} 
 <View 
 className="pk-kb__key pk-kb__key--del" 
 hoverClass="pk-kb__key--hover" 
 onClick={onDelete} 
 > 
 {DEL_ICON} 
 </View> 
 
 {/* 🔥 确认键逻辑升级 */}
 <View 
 className={classNames('pk-kb__key pk-kb__key--confirm', { 'disabled': disabled })}
 hoverClass={!disabled ? "pk-kb__key--hover-primary" : "none"}
 onClick={!disabled ? onConfirm : undefined}
 >
 {confirmText}
 </View> 
 </View> 
 </View> 
 ); 
 };
```

### src/components/business/PKKeyboard.scss

```scss
.pk-kb { 
 background-color: var(--pk-white); 
 padding-bottom: env(safe-area-inset-bottom); /* 适配 iPhone 底部横条 */ 
 display: flex; 
 height: 480px; /* 固定高度 */ 
 border-top: 1px solid var(--pk-border); 

 &__main { 
 flex: 3; /* 占 3/4 宽度 */ 
 display: flex; 
 flex-direction: column; 
 } 

 &__actions { 
 flex: 1; /* 占 1/4 宽度 */ 
 display: flex; 
 flex-direction: column; 
 border-left: 1px solid var(--pk-border); 
 } 

 &__row { 
 flex: 1; 
 display: flex; 
 border-bottom: 1px solid var(--pk-border); 
 &:last-child { border-bottom: none; } 
 } 

 &__key { 
 flex: 1; 
 display: flex; 
 align-items: center; 
 justify-content: center; 
 font-size: 40px; 
 font-weight: 500; 
 color: var(--pk-text-main); 
 border-right: 1px solid var(--pk-border); 
 background: #fff; 
 transition: background 0.1s; 

 &:last-child { border-right: none; } 

 /* 按下效果 */ 
 &--hover { background-color: #f0f0f0; } 

 /* 删除键 */ 
 &--del { 
 flex: 1; /* 高度占 1/4 (对应左边的一行) */ 
 border-bottom: 1px solid var(--pk-border); 
 color: var(--pk-text-main); 
 } 

 /* 确认键 */
 &--confirm {
 flex: 3; /* 高度占 3/4 */
 background-color: var(--pk-primary);
 color: #fff;
 font-size: 34px;
 transition: all 0.2s; /* 添加过渡 */

 &.pk-kb__key--hover-primary {
 opacity: 0.9;
 }

 /* 🔥 新增：禁用态样式 */
 &.disabled {
 background-color: #E0E0E0; /* 灰色 */
 color: #999;
 pointer-events: none; /* 禁止点击 */
 }
 } 
 } 
}
```

### src/components/business/TimePicker.tsx

```typescript
import { View, PickerView, PickerViewColumn } from '@tarojs/components';
import { useState, useEffect, useMemo } from 'react';
import dayjs from 'dayjs';
import { CustomModal } from '../base/CustomModal';

interface TimePickerProps {
 visible: boolean;
 initialDate: number; // 时间戳
 onClose: () => void;
 onConfirm: (ts: number) => void;
}

export const TimePicker = ({ visible, initialDate, onClose, onConfirm }: TimePickerProps) => {
 const [value, setValue] = useState<number[]>([0, 0, 0, 0, 0]);

 // 生成数据源
 const years = useMemo(() => Array.from({ length: 11 }, (_, i) => 2020 + i), []); // 2020-2030
 const months = useMemo(() => Array.from({ length: 12 }, (_, i) => i + 1), []);
 const days = useMemo(() => Array.from({ length: 31 }, (_, i) => i + 1), []);
 const hours = useMemo(() => Array.from({ length: 24 }, (_, i) => i), []);
 const minutes = useMemo(() => Array.from({ length: 60 }, (_, i) => i), []);

 // 初始化回显
 useEffect(() => {
 if (visible) {
 const d = dayjs(initialDate);
 const yIdx = years.indexOf(d.year());
 const mIdx = months.indexOf(d.month() + 1);
 const dIdx = days.indexOf(d.date());
 const hIdx = hours.indexOf(d.hour());
 const minIdx = minutes.indexOf(d.minute());
 setValue([
 yIdx >= 0 ? yIdx : 0,
 mIdx >= 0 ? mIdx : 0,
 dIdx >= 0 ? dIdx : 0,
 hIdx >= 0 ? hIdx : 0,
 minIdx >= 0 ? minIdx : 0
 ]);
 }
 }, [visible, initialDate]);

 const handleConfirm = () => {
 const year = years[value[0]];
 const month = months[value[1]];
 const day = days[value[2]];
 const hour = hours[value[3]];
 const minute = minutes[value[4]];

 // 简单容错：如果当前月只有28/30天，dayjs 会自动处理溢出 (如 2月31日 -> 3月3日)，
 // 为了更精确体验，这里最好依赖 dayjs 的解析
 const newDate = dayjs(`${year}-${month}-${day} ${hour}:${minute}`).valueOf();
 onConfirm(newDate);
 };

 return (
 <CustomModal visible={visible} title="选择时间" onClose={onClose} onConfirm={handleConfirm}>
 <PickerView indicatorStyle="height: 50px;" style={{ width: '100%', height: '200px' }} value={value} onChange={(e) => setValue(e.detail.value)}>
 <PickerViewColumn>
 {years.map(y => <View key={y} style={{ lineHeight: '50px', textAlign: 'center' }}>{y}年</View>)}
 </PickerViewColumn>
 <PickerViewColumn>
 {months.map(m => <View key={m} style={{ lineHeight: '50px', textAlign: 'center' }}>{m}月</View>)}
 </PickerViewColumn>
 <PickerViewColumn>
 {days.map(d => <View key={d} style={{ lineHeight: '50px', textAlign: 'center' }}>{d}日</View>)}
 </PickerViewColumn>
 <PickerViewColumn>
 {hours.map(h => <View key={h} style={{ lineHeight: '50px', textAlign: 'center' }}>{h < 10 ? '0' + h : h}时</View>)}
 </PickerViewColumn>
 <PickerViewColumn>
 {minutes.map(m => <View key={m} style={{ lineHeight: '50px', textAlign: 'center' }}>{m < 10 ? '0' + m : m}分</View>)}
 </PickerViewColumn>
 </PickerView>
 </CustomModal>
 );
};
```

## constants 目录

### src/constants/categories.ts

```typescript
import { TransactionType } from '../types/ledger';

export interface CategoryOption {
  id: string;
  name: string;
  type: TransactionType;
  icon: string; // SVG XML String
  color: string;
}

// 简单的 SVG 字符串 (这里我放几个 TDesign 风格的路径)
const SVG_FOOD = `<svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M12 21C16.9706 21 21 16.9706 21 12C21 7.02944 16.9706 3 12 3C7.02944 3 3 7.02944 3 12C3 16.9706 7.02944 21 12 21Z" stroke="currentColor" stroke-width="1.5"/><path d="M12 7V12L15 15" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>`;
// 注意：为了代码简洁，这里我用同一个圆圈图标代替演示。
// 真实开发时，你去 TDesign 官网复制 <svg> 内容替换这里即可。

export const DEFAULT_CATEGORIES: CategoryOption[] = [
  { id: 'food', name: '餐饮', type: 1, icon: SVG_FOOD, color: '#FF9C6E' },
  { id: 'transport', name: '交通', type: 1, icon: SVG_FOOD, color: '#5CDBD3' },
  { id: 'shopping', name: '购物', type: 1, icon: SVG_FOOD, color: '#FF85C0' },
  { id: 'ent', name: '娱乐', type: 1, icon: SVG_FOOD, color: '#95DE64' },
  { id: 'home', name: '居住', type: 1, icon: SVG_FOOD, color: '#FFC069' },
  { id: 'medical', name: '医疗', type: 1, icon: SVG_FOOD, color: '#85A5FF' },
  { id: 'salary', name: '工资', type: 2, icon: SVG_FOOD, color: '#9FBB96' },
  { id: 'bonus', name: '奖金', type: 2, icon: SVG_FOOD, color: '#FFD666' },
];
```

## hooks 目录

### src/hooks/useNavInfo.ts

```typescript
import Taro from '@tarojs/taro'; 
 import { useMemo } from 'react'; 

export const useNavInfo = () => { 
 return useMemo(() => { 
 const systemInfo = Taro.getSystemInfoSync(); 
 // 获取胶囊按钮位置 (右的三个点) 
 const menuButton = Taro.getMenuButtonBoundingClientRect(); 

 // 状态栏高度 
 const statusBarHeight = systemInfo.statusBarHeight || 20; 

 // 导航栏内容高度 = (胶囊顶部 - 状态栏) * 2 + 胶囊高度 
 // 解释：确保内容垂直居中于胶囊 
 const navContentHeight = (menuButton.top - statusBarHeight) * 2 + menuButton.height; 

 // 总高度 
 const navHeight = statusBarHeight + navContentHeight; 

 return { 
 statusBarHeight, 
 navContentHeight, 
 navHeight, 
 menuButtonWidth: systemInfo.windowWidth - menuButton.right + menuButton.width, // 右侧胶囊占用的宽度 
 }; 
 }, []); 
 };
```

## pages/index 目录

### src/pages/index/index.tsx

```typescript
import { View, Text, Picker } from '@tarojs/components';
import { useState } from 'react';
import { useNavInfo } from '../../hooks/useNavInfo';
import { Navbar } from '../../components/base/Navbar';
import { Drawer } from '../../components/base/Drawer'; // 引入 Drawer
import { EditorPanel } from '../../components/business/EditorPanel';
import { LedgerList } from '../../components/business/LedgerList';
import { DevTools } from '../../components/business/DevTools';
import { useLedgerStore } from '../../store/useLedgerStore';
import { formatAmount } from '../../utils/currency';
import './index.scss';

export default function Index() {
  const { navHeight } = useNavInfo();

  // 🔥 1. 使用新版 Store 的选择器和状态
  const {
    getCurrentMonthLedgers,
    getCurrentMonthExpense,
    selectedMonth,
    setSelectedMonth
  } = useLedgerStore();

  // 🔥 2. 响应式获取当前月份的数据 (Store 更新时组件会自动重渲染)
  const currentLedgers = getCurrentMonthLedgers();
  const totalExpense = getCurrentMonthExpense();

  // 🔥 3. 需求 #11: 默认开启记账 (初始值设为 true)
  const [isEditorOpen, setIsEditorOpen] = useState(true);

  // 🔥 状态：控制侧边栏
  const [isDrawerOpen, setIsDrawerOpen] = useState(false);

  // 辅助：将数字月份 (202602) 转为 Picker 需要的字符串格式 ("2026-02")
  const pickerValue = `${Math.floor(selectedMonth / 100)}-${String(selectedMonth % 100).padStart(2, '0')}`;
  const displayMonth = `${Math.floor(selectedMonth / 100)}年${String(selectedMonth % 100).padStart(2, '0')}月`;

  // 处理日期选择
  const handleMonthChange = (e) => {
    const val = e.detail.value; // 格式 "2026-05"
    const newMonth = parseInt(val.replace('-', '')); // 转为 202605
    setSelectedMonth(newMonth);
  };

  return (
    <View
      className='index'
      style={{ '--pk-nav-height': `${navHeight}px` } as React.CSSProperties}
    >
      {/* 1. 传入 onMenuClick */}
      <Navbar title="PureKeep" onMenuClick={() => setIsDrawerOpen(true)} />

      <View
        className="main-content"
        style={{ paddingTop: `${navHeight}px`, paddingBottom: '200px' }}
      >
        {/* 月度总览卡片 */}
        <View className="overview-card">
          {/* 🔥 4. 需求 #1: 增加 Picker 实现按月筛选 */}
          <Picker mode="date" fields="month" value={pickerValue} onChange={handleMonthChange}>
            <View className="month-picker">
              <Text className="curr-month">{displayMonth}</Text>
              <View className="arrow-down" />
            </View>
          </Picker>

          <View className="expense-hero">
            <Text className="symbol">¥</Text>
            <Text className="val">{formatAmount(totalExpense)}</Text>
          </View>
          <Text className="label">本月支出</Text>
        </View>

        <LedgerList ledgers={currentLedgers} />

      </View>

      <View
        className="fab-btn"
        style={{
          transform: isEditorOpen ? 'scale(0)' : 'scale(1)',
          opacity: isEditorOpen ? 0 : 1,
          transition: 'all 0.2s ease'
        }}
        onClick={() => setIsEditorOpen(true)}
      >
        +
      </View>

      {/* 开发工具暂留，不想要可以注释掉 */}
      {/* <DevTools /> */}

      <EditorPanel
        visible={isEditorOpen}
        onClose={() => setIsEditorOpen(false)}
      />

      {/* 🔥 挂载 Drawer (预留同步入口) */}
      <Drawer visible={isDrawerOpen} onClose={() => setIsDrawerOpen(false)}>
        <View style={{ padding: '20px' }}>
          <Text style={{ fontSize: '32px', fontWeight: 'bold', marginBottom: '20px', display: 'block' }}>设置</Text>

          <View style={{ marginTop: '40px', padding: '20px', background: '#F2F4F5', borderRadius: '12px' }}>
            <Text style={{ color: '#999', fontSize: '24px' }}>数据同步 (Coming Soon)</Text>
            <View style={{ marginTop: '10px' }}>
              <Text style={{ fontSize: '28px' }}>☁️ 腾讯云开发 / WebDAV</Text>
            </View>
          </View>
        </View>
      </Drawer>
    </View>
  );
}
```

### src/pages/index/index.scss

```scss
.main-content {
  padding-left: 32px;
  padding-right: 32px;
}

/* 总览卡片 */
.overview-card {
  margin: 20px 0 40px;

  .month-picker {
    display: inline-flex;
    align-items: center;
    background: #E8EFF0;
    padding: 8px 24px;
    border-radius: 30px;
    margin-bottom: 20px;

    .curr-month {
      color: var(--pk-primary);
      font-size: 26px;
      font-weight: 600;
      margin-right: 10px; /* 给箭头留点空隙 */
    }

    /* 🔥 新增：纯 CSS 画的小三角箭头 */
    .arrow-down {
      width: 0;
      height: 0;
      border-left: 8px solid transparent;
      border-right: 8px solid transparent;
      border-top: 10px solid var(--pk-primary);
      opacity: 0.8;
    }
  }

  .expense-hero {
    color: var(--pk-text-main);
    display: flex;
    align-items: baseline;

    .symbol { font-size: 32px; font-weight: bold; margin-right: 4px; }
    .val { font-size: 72px; font-weight: bold; font-family: 'DIN Alternate'; line-height: 1; }
  }

  .label {
    font-size: 24px;
    color: var(--pk-text-sub);
    margin-top: 8px;
    display: block;
  }
}

/* FAB 样式提取 */
.fab-btn {
  position: fixed;
  bottom: 50px;
  right: 40px;
  width: 110px;
  height: 110px;
  border-radius: 50%;
  background: var(--pk-primary);
  box-shadow: 0 8px 20px rgba(122, 158, 159, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  font-size: 60px;
  font-weight: 300;
  z-index: 100;
  transition: transform 0.1s;

  &:active { transform: scale(0.9); }
}
```

### src/pages/index/index.config.ts

```typescript
export default definePageConfig({
  navigationBarTitleText: '首页'
})
```

## store 目录

### src/store/useLedgerStore.ts

```typescript
import { create } from 'zustand';
import { persist, createJSONStorage, StateStorage } from 'zustand/middleware';
import Taro from '@tarojs/taro';
import dayjs from 'dayjs';
import { LedgerItem, TransactionType, DraftLedger, SyncConfig } from '../types/ledger';
import { generateId } from '../utils/id';
import { getMonthKey } from '../utils/date';

// 1. 封装 Taro Storage 适配器给 Zustand 使用
const taroStorage: StateStorage = {
  getItem: (name) => {
    return Taro.getStorageSync(name) || null;
  },
  setItem: (name, value) => {
    Taro.setStorageSync(name, value);
  },
  removeItem: (name) => {
    Taro.removeStorageSync(name);
  },
};

// 1. 定义新增 Paylaod 类型 (方便复用)
type NewLedgerPayload = Omit<LedgerItem, '_id' | 'bookMonth' | 'createdAt' | 'updatedAt' | 'isDeleted' | 'bookId'>;

interface LedgerState {
  ledgers: LedgerItem[];

  // --- 新增状态 ---
  draft: DraftLedger | null; // 记账草稿
  selectedMonth: number; // 当前选中的月份 (格式: 202602)
  syncConfig: SyncConfig; // 同步配置

  // Actions
  addLedger: (payload: NewLedgerPayload) => void;
  // 🔥 新增：批量添加 (用于测试数据生成)
  batchAddLedgers: (payloads: NewLedgerPayload[]) => void;
  // 🔥 新增：清空所有数据 (方便重测)
  clearAll: () => void;

  deleteLedger: (id: string) => void;
  updateLedger: (id: string, payload: Partial<LedgerItem>) => void;

  // 新增 Actions
  saveDraft: (draft: DraftLedger) => void;
  clearDraft: () => void;
  setSelectedMonth: (month: number) => void;
  updateSyncConfig: (config: Partial<SyncConfig>) => void;

  // --- Selectors ---
  // 修改：不再需要传参，直接获取 selectedMonth 的数据
  getCurrentMonthLedgers: () => LedgerItem[];
  getCurrentMonthExpense: () => number;
}

export const useLedgerStore = create<LedgerState>()(
  persist(
    (set, get) => ({
      ledgers: [],

      // 1. 初始化新状态
      draft: null,
      selectedMonth: getMonthKey(), // 默认为当月
      syncConfig: {
        provider: 'none',
        lastSyncTime: 0
      },

      addLedger: (payload: NewLedgerPayload) => {
        const now = Date.now();
        const newItem: LedgerItem = {
          _id: generateId(),
          bookId: 'default',
          ...payload,
          bookMonth: parseInt(dayjs(payload.date).format('YYYYMM')),
          createdAt: now,
          updatedAt: now,
          isDeleted: false,
        };

        set((state) => ({
          ledgers: [newItem, ...state.ledgers],
          draft: null // 提交成功后，自动清空草稿
        }));

        console.log('✅ [PureKeep] 写入成功:', newItem);
      },

      // 🔥 实现批量写入
      batchAddLedgers: (payloads: NewLedgerPayload[]) => {
        const now = Date.now();
        // 批量构建对象
        const newItems: LedgerItem[] = payloads.map(payload => ({
          _id: generateId(), // 注意：如果不加延迟，短时间循环生成的 ID 可能会冲突，依靠 Random 此时很重要
          bookId: 'default',
          ...payload,
          bookMonth: parseInt(dayjs(payload.date).format('YYYYMM')),
          createdAt: now,
          updatedAt: now,
          isDeleted: false,
        }));

        set((state) => ({
          ledgers: [...newItems, ...state.ledgers]
        }));

        console.log(`✅ [PureKeep] 批量写入 ${newItems.length} 条数据成功`);
      },

      // 🔥 实现清空
      clearAll: () => set({ ledgers: [] }),

      // 软删除
      deleteLedger: (id) => {
        set((state) => ({
          ledgers: state.ledgers.map((item) =>
            item._id === id
              ? { ...item, isDeleted: true, updatedAt: Date.now() }
              : item
          ),
        }));
      },

      updateLedger: (id, payload) => {
        set((state) => ({
          ledgers: state.ledgers.map((item) =>
            item._id === id
              ? { ...item, ...payload, updatedAt: Date.now() }
              : item
          ),
        }));
      },

      // 2. 实现草稿逻辑
      saveDraft: (draft) => set({ draft }),
      clearDraft: () => set({ draft: null }),

      // 3. 实现月份筛选逻辑
      setSelectedMonth: (month) => set({ selectedMonth: month }),

      // 4. 实现同步配置更新
      updateSyncConfig: (config) => set((state) => ({
        syncConfig: { ...state.syncConfig, ...config }
      })),

      // 5. 修改 Selectors (基于 selectedMonth)
      getCurrentMonthLedgers: () => {
        const { ledgers, selectedMonth } = get();
        return ledgers.filter(
          (item) => item.bookMonth === selectedMonth && !item.isDeleted
        );
      },

      getCurrentMonthExpense: () => {
        const { ledgers, selectedMonth } = get();
        return ledgers
          .filter((item) => item.bookMonth === selectedMonth && !item.isDeleted && item.type === 1)
          .reduce((sum, item) => sum + item.amount, 0);
      },
    }),
    {
      name: 'purekeep-storage', // Storage Key
      storage: createJSONStorage(() => taroStorage), // 绑定 Taro Storage
      // 注意：draft 和 selectedMonth 也会被持久化，这是符合需求的
      // (用户重启小程序应看到上次选的月份，和上次没写完的草稿)
    }
  )
);
```