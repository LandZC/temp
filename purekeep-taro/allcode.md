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
│   │   ├── Drawer.tsx
│   │   └── Icon.tsx
│   └── business/
│       ├── DevTools.tsx
│       ├── EditorPanel.scss
│       ├── EditorPanel.tsx
│       ├── LedgerDetailModal.scss
│       ├── LedgerDetailModal.tsx
│       ├── LedgerList.scss
│       ├── LedgerList.tsx
│       ├── MonthPicker.tsx
│       ├── PKKeyboard.scss
│       ├── PKKeyboard.tsx
│       ├── TimePicker.scss
│       └── TimePicker.tsx
├── constants/
│   ├── categories.ts
│   └── quotes.ts
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

## 所有文件代码

### src/app.config.ts

```typescript
export default {
  pages: [
    'pages/index/index'
  ],
  window: {
    backgroundTextStyle: 'light',
    navigationBarBackgroundColor: '#fff',
    navigationBarTitleText: 'PureKeep',
    navigationBarTextStyle: 'black'
  }
}
```

### src/app.scss

```scss
/* 全局样式重置 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: #F8F9FA;
  color: #333;
}

/* 全局变量 */
:root {
  --pk-primary: #7A9E9F;
  --pk-secondary: #A8C3C4;
  --pk-income: #52C41A;
  --pk-expense: #FF4D4F;
  --pk-text-main: #333333;
  --pk-text-sub: #666666;
  --pk-text-light: #999999;
  --pk-border: #E8E8E8;
  --pk-bg: #F8F9FA;
  --pk-white: #FFFFFF;
}

/* 通用样式 */
.pk-container {
  width: 100%;
  max-width: 750px;
  margin: 0 auto;
  padding: 0 20px;
}

.pk-btn {
  display: inline-block;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
}

.pk-btn-primary {
  background-color: var(--pk-primary);
  color: white;
}

.pk-btn-primary:hover {
  background-color: var(--pk-secondary);
}

.pk-btn-danger {
  background-color: var(--pk-expense);
  color: white;
}

.pk-btn-danger:hover {
  background-color: #FF7875;
}

/* 动画 */
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slideUp {
  from {
    transform: translateY(100%);
  }
  to {
    transform: translateY(0);
  }
}

.fade-in {
  animation: fadeIn 0.3s ease;
}

.slide-up {
  animation: slideUp 0.3s ease;
}
```

### src/app.ts

```typescript
import Taro from '@tarojs/taro'

Taro.initPxTransform({ designWidth: 750, deviceRatio: { 640: 2.34 / 2, 750: 1, 828: 1.81 / 2 } })

if (process.env.NODE_ENV === 'development') {
  console.log('PureKeep 开发环境')
}
```

### src/index.html

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>PureKeep</title>
  <link rel="stylesheet" href="app.scss">
</head>
<body>
  <div id="app"></div>
  <script src="app.ts"></script>
</body>
</html>
```

### src/components/base/Navbar.scss

```scss
.pk-navbar {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  background: #fff;
  z-index: 1000;
  box-shadow: 0 1px 8px rgba(0, 0, 0, 0.08);

  &__content {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 20px;
  }

  &__title {
    flex: 1;
    text-align: center;
    font-size: 32px;
    font-weight: 600;
    color: var(--pk-text-main);
  }

  &__menu-btn {
    width: 44px;
    height: 44px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;

    .bar {
      width: 24px;
      height: 2px;
      background: var(--pk-text-main);
      margin-bottom: 6px;
      transition: all 0.3s ease;

      &:last-child {
        margin-bottom: 0;
      }
    }
  }

  &__right {
    display: flex;
    align-items: center;
  }
}
```

### src/components/base/Navbar.tsx

```typescript
import { ReactNode } from 'react';
import { View, Text } from '@tarojs/components';
import { useNavInfo } from '../../hooks/useNavInfo';
import { Icon } from './Icon'; // 引入 Icon 组件
import './Navbar.scss';

interface NavbarProps {
  title?: string;
  onMenuClick?: () => void;
  rightAction?: ReactNode;
  leftIcon?: 'menu' | 'close'; // 新增属性
}

export const Navbar = ({ title = 'PureKeep', onMenuClick, rightAction, leftIcon = 'menu' }: NavbarProps) => {
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
        {/* 左侧按钮：根据模式渲染 汉堡 或 关闭 */}
        {onMenuClick && (
          <View className="pk-navbar__menu-btn" onClick={onMenuClick}>
            {leftIcon === 'menu' ? (
              <>
                <View className="bar" />
                <View className="bar" />
              </>
            ) : (
              /* 渲染退出(X)图标 */
              <Icon name="close" size={24} color="#333" />
            )}
          </View>
        )}

        <Text className="pk-navbar__title">{title}</Text>

        {/* 🔥 新增：右侧操作区 (绝对定位或 Flex 布局) */}
        {rightAction && (
          <View className="pk-navbar__right" style={{ position: 'absolute', right: menuButtonWidth + 10, height: '100%', display: 'flex', alignItems: 'center' }}>
            {rightAction}
          </View>
        )}
      </View>
    </View>
  );
};
```

### src/components/base/PKButton.scss

```scss
.pk-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0 20px;
  height: 44px;
  border-radius: 22px;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  border: none;

  &:active {
    transform: scale(0.96);
  }

  &--primary {
    background-color: var(--pk-primary);
    color: white;
  }

  &--secondary {
    background-color: #F0F0F0;
    color: var(--pk-text-main);
  }

  &--danger {
    background-color: var(--pk-expense);
    color: white;
  }

  &--disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
}
```

### src/components/base/PKButton.tsx

```typescript
import { ReactNode } from 'react';
import { View, Text } from '@tarojs/components';
import './PKButton.scss';

interface PKButtonProps {
  type?: 'primary' | 'secondary' | 'danger';
  disabled?: boolean;
  onClick?: () => void;
  children: ReactNode;
}

export const PKButton = ({
  type = 'primary',
  disabled = false,
  onClick,
  children
}: PKButtonProps) => {
  return (
    <View
      className={`pk-button pk-button--${type} ${disabled ? 'pk-button--disabled' : ''}`}
      onClick={disabled ? undefined : onClick}
    >
      <Text>{children}</Text>
    </View>
  );
};
```

### src/components/base/CustomModal.scss

```scss
.pk-modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;

  &__content {
    background-color: white;
    border-radius: 16px;
    width: 90%;
    max-width: 500px;
    max-height: 80vh;
    overflow-y: auto;
  }

  &__header {
    padding: 24px;
    border-bottom: 1px solid var(--pk-border);
    display: flex;
    align-items: center;
    justify-content: space-between;

    &-title {
      font-size: 28px;
      font-weight: 600;
      color: var(--pk-text-main);
    }

    &-close {
      width: 40px;
      height: 40px;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
    }
  }

  &__body {
    padding: 24px;
  }

  &__footer {
    padding: 16px 24px;
    border-top: 1px solid var(--pk-border);
    display: flex;
    gap: 16px;
    justify-content: flex-end;
  }
}
```

### src/components/base/CustomModal.tsx

```typescript
import { ReactNode } from 'react';
import { View, Text } from '@tarojs/components';
import { Icon } from './Icon';
import './CustomModal.scss';

interface CustomModalProps {
  visible: boolean;
  title: string;
  children: ReactNode;
  footer?: ReactNode;
  onClose: () => void;
}

export const CustomModal = ({
  visible,
  title,
  children,
  footer,
  onClose
}: CustomModalProps) => {
  if (!visible) return null;

  return (
    <View className="pk-modal">
      <View className="pk-modal__content">
        <View className="pk-modal__header">
          <Text className="pk-modal__header-title">{title}</Text>
          <View className="pk-modal__header-close" onClick={onClose}>
            <Icon name="close" size={24} color="#999" />
          </View>
        </View>
        <View className="pk-modal__body">
          {children}
        </View>
        {footer && (
          <View className="pk-modal__footer">
            {footer}
          </View>
        )}
      </View>
    </View>
  );
};
```

### src/components/base/Drawer.scss

```scss
.pk-drawer {
  position: fixed;
  top: 0;
  right: -300px;
  width: 300px;
  height: 100vh;
  background-color: white;
  box-shadow: -2px 0 8px rgba(0, 0, 0, 0.1);
  z-index: 1000;
  transition: right 0.3s ease;

  &.open {
    right: 0;
  }

  &__overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.5);
    z-index: 999;
  }

  &__header {
    padding: 24px;
    border-bottom: 1px solid var(--pk-border);
    display: flex;
    align-items: center;
    justify-content: space-between;

    &-title {
      font-size: 28px;
      font-weight: 600;
      color: var(--pk-text-main);
    }

    &-close {
      width: 40px;
      height: 40px;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
    }
  }

  &__content {
    padding: 24px;
  }
}
```

### src/components/base/Drawer.tsx

```typescript
import { ReactNode } from 'react';
import { View, Text } from '@tarojs/components';
import { Icon } from './Icon';
import './Drawer.scss';

interface DrawerProps {
  visible: boolean;
  onClose: () => void;
  children?: ReactNode;
}

export const Drawer = ({
  visible,
  onClose,
  children
}: DrawerProps) => {
  if (!visible) return null;

  return (
    <>
      <View className="pk-drawer__overlay" onClick={onClose} />
      <View className={`pk-drawer ${visible ? 'open' : ''}`}>
        <View className="pk-drawer__header">
          <Text className="pk-drawer__header-title">设置</Text>
          <View className="pk-drawer__header-close" onClick={onClose}>
            <Icon name="close" size={24} color="#999" />
          </View>
        </View>
        <View className="pk-drawer__content">
          {children || (
            <Text style={{ fontSize: 24, color: '#999', textAlign: 'center' }}>
              功能开发中...
            </Text>
          )}
        </View>
      </View>
    </>
  );
};
```

### src/components/base/Icon.tsx

```typescript
import { View, Text } from '@tarojs/components';

interface IconProps {
  name: string;
  size?: number;
  color?: string;
}

export const Icon = ({ name, size = 20, color = '#333' }: IconProps) => {
  // 简单的图标实现，实际项目中可以使用 SVG 或字体图标
  const getIconContent = () => {
    switch (name) {
      case 'close':
        return '×';
      case 'check':
        return '✓';
      case 'menu':
        return '☰';
      case 'sun':
        return '☀️';
      case 'moon':
        return '🌙';
      default:
        return '●';
    }
  };

  return (
    <View
      style={{
        fontSize: size,
        color: color,
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'center'
      }}
    >
      {getIconContent()}
    </View>
  );
};
```

### src/components/business/DevTools.tsx

```typescript
import { View, Text, Button } from '@tarojs/components';
import { useLedgerStore } from '../../store/useLedgerStore';

export const DevTools = () => {
  const { clearAllLedgers, addTestData } = useLedgerStore();

  return (
    <View style={{ padding: 20 }}>
      <Text style={{ fontSize: 24, fontWeight: 'bold', marginBottom: 20 }}>开发工具</Text>
      <Button
        style={{ marginBottom: 10 }}
        onClick={() => {
          if (confirm('确定要清空所有数据吗？')) {
            clearAllLedgers();
            alert('数据已清空');
          }
        }}
      >
        清空所有数据
      </Button>
      <Button
        onClick={() => {
          addTestData();
          alert('测试数据已添加');
        }}
      >
        添加测试数据
      </Button>
    </View>
  );
};
```

### src/components/business/EditorPanel.scss

```scss
.pk-editor {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: white;
  border-top-left-radius: 32px;
  border-top-right-radius: 32px;
  box-shadow: 0 -4px 20px rgba(0, 0, 0, 0.1);
  z-index: 999;
  transform: translateY(100%);
  transition: transform 0.3s ease;

  &.open {
    transform: translateY(0);
  }

  &__header {
    padding: 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid var(--pk-border);

    &-title {
      font-size: 28px;
      font-weight: 600;
      color: var(--pk-text-main);
    }

    &-close {
      width: 40px;
      height: 40px;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
    }
  }

  &__content {
    padding: 20px;
  }

  &__row {
    margin-bottom: 32px;

    &-label {
      font-size: 24px;
      color: var(--pk-text-sub);
      margin-bottom: 12px;
    }
  }

  &__amount {
    font-size: 48px;
    font-weight: bold;
    color: var(--pk-text-main);
    font-family: 'DIN Alternate', sans-serif;
  }

  &__type {
    display: flex;
    gap: 16px;

    &-btn {
      flex: 1;
      padding: 16px;
      border: 2px solid var(--pk-border);
      border-radius: 12px;
      text-align: center;
      cursor: pointer;

      &.active {
        border-color: var(--pk-primary);
        background-color: rgba(122, 158, 159, 0.1);
      }

      &-text {
        font-size: 24px;
        font-weight: 500;
      }

      &-exp {
        color: var(--pk-expense);
      }

      &-inc {
        color: var(--pk-income);
      }
    }
  }

  &__category {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;

    &-item {
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 16px;
      border-radius: 12px;
      cursor: pointer;

      &.active {
        background-color: rgba(122, 158, 159, 0.1);
      }

      &-icon {
        width: 60px;
        height: 60px;
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        margin-bottom: 8px;

        &-dot {
          width: 16px;
          height: 16px;
          border-radius: 50%;
        }
      }

      &-name {
        font-size: 20px;
        color: var(--pk-text-main);
      }
    }
  }

  &__note {
    width: 100%;
    padding: 16px;
    border: 1px solid var(--pk-border);
    border-radius: 12px;
    font-size: 24px;
    resize: none;
  }

  &__footer {
    padding: 20px;
    border-top: 1px solid var(--pk-border);
  }
}
```

### src/components/business/EditorPanel.tsx

```typescript
import { useState, useEffect } from 'react';
import { View, Text, Textarea } from '@tarojs/components';
import { Icon } from '../base/Icon';
import { PKButton } from '../base/PKButton';
import { useLedgerStore } from '../../store/useLedgerStore';
import { LedgerItem } from '../../types/ledger';
import { CATEGORIES } from '../../constants/categories';
import './EditorPanel.scss';

interface EditorPanelProps {
  visible: boolean;
  onClose: () => void;
  editItem?: LedgerItem | null;
}

export const EditorPanel = ({
  visible,
  onClose,
  editItem
}: EditorPanelProps) => {
  const { addLedger, updateLedger } = useLedgerStore();
  
  const [amount, setAmount] = useState('');
  const [type, setType] = useState(1); // 1: 支出, 2: 收入
  const [categoryId, setCategoryId] = useState('');
  const [note, setNote] = useState('');
  const [date, setDate] = useState(new Date());

  useEffect(() => {
    if (editItem) {
      setAmount(editItem.amount.toString());
      setType(editItem.type);
      setCategoryId(editItem.categoryId);
      setNote(editItem.note || '');
      setDate(new Date(editItem.date));
    } else {
      resetForm();
    }
  }, [editItem]);

  const resetForm = () => {
    setAmount('');
    setType(1);
    setCategoryId('');
    setNote('');
    setDate(new Date());
  };

  const handleSave = () => {
    if (!amount || !categoryId) {
      return;
    }

    const ledgerData = {
      amount: parseFloat(amount),
      type,
      categoryId,
      note,
      date: date.toISOString()
    };

    if (editItem) {
      updateLedger(editItem._id, ledgerData);
    } else {
      addLedger(ledgerData);
    }

    onClose();
  };

  if (!visible) return null;

  return (
    <View className={`pk-editor ${visible ? 'open' : ''}`}>
      <View className="pk-editor__header">
        <Text className="pk-editor__header-title">{editItem ? '编辑账单' : '记一笔'}</Text>
        <View className="pk-editor__header-close" onClick={onClose}>
          <Icon name="close" size={24} color="#999" />
        </View>
      </View>
      
      <View className="pk-editor__content">
        <View className="pk-editor__row">
          <Text className="pk-editor__row-label">金额</Text>
          <Text className="pk-editor__amount">¥{amount || '0'}</Text>
        </View>

        <View className="pk-editor__row">
          <Text className="pk-editor__row-label">类型</Text>
          <View className="pk-editor__type">
            <View
              className={`pk-editor__type-btn ${type === 1 ? 'active' : ''}`}
              onClick={() => setType(1)}
            >
              <Text className="pk-editor__type-btn-text pk-editor__type-btn-exp">支出</Text>
            </View>
            <View
              className={`pk-editor__type-btn ${type === 2 ? 'active' : ''}`}
              onClick={() => setType(2)}
            >
              <Text className="pk-editor__type-btn-text pk-editor__type-btn-inc">收入</Text>
            </View>
          </View>
        </View>

        <View className="pk-editor__row">
          <Text className="pk-editor__row-label">分类</Text>
          <View className="pk-editor__category">
            {CATEGORIES.filter(cat => cat.type === type).map(cat => (
              <View
                key={cat.id}
                className={`pk-editor__category-item ${categoryId === cat.id ? 'active' : ''}`}
                onClick={() => setCategoryId(cat.id)}
              >
                <View className="pk-editor__category-item-icon" style={{ backgroundColor: `${cat.color}20` }}>
                  <View className="pk-editor__category-item-icon-dot" style={{ backgroundColor: cat.color }} />
                </View>
                <Text className="pk-editor__category-item-name">{cat.name}</Text>
              </View>
            ))}
          </View>
        </View>

        <View className="pk-editor__row">
          <Text className="pk-editor__row-label">备注</Text>
          <Textarea
            className="pk-editor__note"
            value={note}
            onChange={(e) => setNote(e.detail.value)}
            placeholder="添加备注"
            placeholderStyle={{ color: '#999' }}
          />
        </View>
      </View>

      <View className="pk-editor__footer">
        <PKButton type="primary" onClick={handleSave}>
          {editItem ? '更新' : '保存'}
        </PKButton>
      </View>
    </View>
  );
};
```

### src/components/business/LedgerDetailModal.scss

```scss
.pk-detail {
  padding: 10px 0 20px;

  &__amount-box {
    display: flex;
    justify-content: center;
    align-items: baseline;
    margin-bottom: 40px;

    /* 新的统一类名 */
    .val-unified {
      font-family: 'DIN Alternate', sans-serif;
      font-size: 64px; /* 大字号 */
      font-weight: 600;
      line-height: 1;

      &.exp { color: var(--pk-text-main); }
      &.inc { color: var(--pk-income); }
    }
  }

  &__row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 24px 0;
    border-bottom: 1px solid rgba(0,0,0,0.03);

    &:last-child { border-bottom: none; }

    .label {
      font-size: 28px;
      color: var(--pk-text-sub);
    }

    .val-wrap {
      display: flex;
      align-items: center;
      gap: 12px;
      .dot { width: 16px; height: 16px; border-radius: 50%; }
    }

    .val {
      font-size: 28px;
      color: var(--pk-text-main);
      text-align: right;
      flex: 1;
      margin-left: 30px;

      /* 备注为空时的样式 */
      &.empty {
        color: #CCC; /* 浅灰色 */
        font-style: italic; /* 可选：斜体 */
      }
    }
  }

  &__actions {
    margin-top: 50px;
    display: flex;
    gap: 20px;

    .action-btn {
      flex: 1;
      height: 80px;
      display: flex;
      align-items: center;
      justify-content: center;
      border-radius: 12px;
      font-size: 30px;
      font-weight: 600;

      &.delete {
        background: #FFF1F0;
        color: #FF4D4F;
      }

      &.edit {
        background: var(--pk-primary);
        color: #fff;
      }

      &:active { opacity: 0.8; }
    }
  }
}

// 执行注意：覆盖默认底栏
.pk-modal-footer {
  display: none;
}
```

### src/components/business/LedgerDetailModal.tsx

```typescript
import { View, Text } from '@tarojs/components';
import Taro from '@tarojs/taro';
import dayjs from 'dayjs';
import { LedgerItem } from '../../types/ledger';
import { CustomModal } from '../base/CustomModal';
import { getCategoryById } from '../../utils/group';
import { formatCurrency } from '../../utils/currency';
import { useLedgerStore } from '../../store/useLedgerStore';
import classNames from 'classnames';
import './LedgerDetailModal.scss'; // 后面会给样式

interface LedgerDetailModalProps {
  visible: boolean;
  ledger: LedgerItem | null;
  onClose: () => void;
  onEdit: (item: LedgerItem) => void;
}

export const LedgerDetailModal = ({ visible, ledger, onClose, onEdit }: LedgerDetailModalProps) => {
  const { deleteLedger } = useLedgerStore();

  if (!ledger) return null;

  const cat = getCategoryById(ledger.categoryId);
  // 预处理符号和金额
  const symbol = ledger.type === 1 ? '-' : '+';
  const amountTxt = formatCurrency(ledger.amount).replace('¥', '');

  // 备注显示逻辑
  const displayNote = ledger.note && ledger.note.trim().length > 0 ? ledger.note : '无';
  const isNoteEmpty = displayNote === '无';

  const handleDelete = () => {
    Taro.showModal({
      title: '确认删除',
      content: '删除后无法恢复，确定吗？',
      confirmColor: '#FF4D4F',
      success: (res) => {
        if (res.confirm) {
          deleteLedger(ledger._id);
          Taro.showToast({ title: '已删除', icon: 'success' });
          onClose();
        }
      }
    });
  };

  return (
    <CustomModal visible={visible} title="账单详情" onClose={onClose} footer={null}>
      <View className="pk-detail">
        <View className="pk-detail__amount-box">
          {/* 统一渲染：符号+金额，样式一致 */}
          <Text className={`val-unified ${ledger.type === 1 ? 'exp' : 'inc'}`}>
            {symbol}{amountTxt}
          </Text>
        </View>

        <View className="pk-detail__row">
          <Text className="label">分类</Text>
          <View className="val-wrap">
            <View className="dot" style={{ background: cat.color }} />
            <Text className="val">{cat.name}</Text>
          </View>
        </View>

        <View className="pk-detail__row">
          <Text className="label">时间</Text>
          <Text className="val">{dayjs(ledger.date).format('YYYY年MM月DD日 HH:mm')}</Text>
        </View>

        {/* 始终渲染备注行 */}
        <View className="pk-detail__row">
          <Text className="label">备注</Text>
          <Text className={classNames('val', { 'empty': isNoteEmpty })}>
            {displayNote}
          </Text>
        </View>

        <View className="pk-detail__actions">
          <View className="action-btn delete" onClick={handleDelete}>删除</View>
          <View className="action-btn edit" onClick={() => onEdit(ledger)}>编辑</View>
        </View>
      </View>
    </CustomModal>
  );
};
```

### src/components/business/LedgerList.scss

```scss
.pk-list {
  margin-bottom: 20px;

  &__group {
    margin-bottom: 32px;
  }

  &__header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 16px 0;
    border-bottom: 1px solid var(--pk-border);

    .left {
      display: flex;
      align-items: center;

      .date {
        font-size: 24px;
        font-weight: 600;
        color: var(--pk-text-main);
        margin-right: 12px;
      }

      .label {
        font-size: 20px;
        color: var(--pk-text-light);
      }
    }

    .right {
      display: flex;
      gap: 20px;

      .income {
        font-size: 20px;
        color: var(--pk-income);
      }

      .expense {
        font-size: 20px;
        color: var(--pk-expense);
      }
    }
  }

  &__items {
    margin-top: 16px;
  }
}

.pk-item {
  display: flex;
  align-items: center;
  padding: 20px 0;
  border-bottom: 1px solid var(--pk-border);

  &:last-child {
    border-bottom: none;
  }

  &__icon {
    width: 60px;
    height: 60px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 16px;
  }

  &__content {
    flex: 1;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  &__info {
    flex: 1;

    &-title {
      font-size: 24px;
      color: var(--pk-text-main);
      margin-bottom: 8px;
    }

    &-desc {
      font-size: 20px;
      color: var(--pk-text-light);
    }
  }

  &__amount-wrap {
    .amount {
      font-size: 24px;
      font-weight: 500;

      &.exp {
        color: var(--pk-text-main);
      }

      &.inc {
        color: var(--pk-income);
      }
    }
  }

  &.selection-mode {
    padding-left: 16px;
  }
}

.pk-checkbox {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  border: 2px solid var(--pk-border);
  margin-right: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
  background: #fff;

  &.checked {
    background: var(--pk-primary);
    border-color: var(--pk-primary);
  }
}

.pk-empty {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 100px 0;

  &__dot {
    width: 80px;
    height: 80px;
    border-radius: 50%;
    background-color: #F0F0F0;
    margin-bottom: 24px;
  }

  text {
    font-size: 24px;
    color: var(--pk-text-light);
  }
}
```

### src/components/business/LedgerList.tsx

```typescript
import { View, Text } from '@tarojs/components';
import { useMemo } from 'react';
import classNames from 'classnames';
import { LedgerItem } from '../../types/ledger';
import { groupLedgersByDay, getCategoryById } from '../../utils/group';
import { formatCurrency } from '../../utils/currency';
import { formatTime } from '../../utils/date';
import { Icon } from '../base/Icon';
import './LedgerList.scss';

interface LedgerListProps {
  ledgers: LedgerItem[];
  onItemClick?: (item: LedgerItem) => void;
  // 🔥 新增多选相关 Props
  selectionMode?: boolean;
  selectedIds?: string[];
  onLongPress?: (item: LedgerItem) => void;
  onSelect?: (item: LedgerItem) => void;
  onGroupToggle?: (ids: string[]) => void;
}

export const LedgerList = ({
  ledgers,
  onItemClick,
  selectionMode = false,
  selectedIds = [],
  onLongPress,
  onSelect,
  onGroupToggle
}: LedgerListProps) => {
  const groups = useMemo(() => groupLedgersByDay(ledgers), [ledgers]);

  if (ledgers.length === 0) {
    return (
      <View className="pk-empty">
        <View className="pk-empty__dot" />
        <Text>暂无账单，开启极简生活</Text>
      </View>
    );
  }

  // 辅助：判断该组是否全选
  const isGroupSelected = (groupItems: LedgerItem[]) => {
    if (groupItems.length === 0) return false;
    return groupItems.every(item => selectedIds.includes(item._id));
  };

  // 处理点击逻辑
  const handleClick = (item: LedgerItem) => {
    if (selectionMode) {
      onSelect && onSelect(item);
    } else {
      onItemClick && onItemClick(item);
    }
  };

  return (
    <View className="pk-list">
      {groups.map(group => {
        const allSelected = isGroupSelected(group.items);

        return (
          <View key={group.dateStr} className="pk-list__group">
            <View className="pk-list__header sticky-header">
              <View className="left" style={{ display: 'flex', alignItems: 'center', gap: '10px' }}>
                {/* 关键逻辑：多选模式下的日期 Checkbox */}
                {selectionMode && (
                  <View
                    className={classNames('pk-checkbox-group', { checked: allSelected })}
                    onClick={() => {
                      const groupIds = group.items.map(i => i._id);
                      onGroupToggle && onGroupToggle(groupIds);
                    }}
                  >
                    {allSelected && <Icon name="check" size={14} color="#FFF" />}
                  </View>
                )}

                <Text className="date">{group.dateStr}</Text>
                <Text className="label">{group.dayLabel}</Text>
              </View>
              <View className="right">
                {group.totalIncome > 0 && (
                  <Text className="income">收 {formatCurrency(group.totalIncome).replace('¥', '')}</Text>
                )}
                {group.totalExpense > 0 && (
                  <Text className="expense">支 {formatCurrency(group.totalExpense).replace('¥', '')}</Text>
                )}
              </View>
            </View>

            <View className="pk-list__items">
              {group.items.map(item => {
                const cat = getCategoryById(item.categoryId);
                const hasNote = item.note && item.note.trim().length > 0;
                const displayTitle = hasNote ? `${cat.name} | ${item.note}` : cat.name;

                // 判断选中状态
                const isSelected = selectedIds.includes(item._id);
                // 关键变更：支出显示负号
                const symbol = item.type === 1 ? '-' : '+';
                const amountTxt = formatCurrency(item.amount).replace('¥', '');

                return (
                  <View
                    key={item._id}
                    className={classNames('pk-item', { 'selection-mode': selectionMode })}
                    onClick={() => handleClick(item)}
                    onLongPress={() => onLongPress && onLongPress(item)} // 🔥 绑定长按
                  >
                    {/* 🔥 新增：多选模式下的 Checkbox */}
                    {selectionMode && (
                      <View className={classNames('pk-checkbox', { checked: isSelected })}>
                        {isSelected && <Icon name="check" size={20} color="#FFF" />}
                      </View>
                    )}

                    <View className="pk-item__icon" style={{ backgroundColor: `${cat.color}20` }}>
                      <View style={{ width: 10, height: 10, borderRadius: '50%', backgroundColor: cat.color }} />
                    </View>

                    <View className="pk-item__content">
                      <View className="pk-item__info">
                        <Text className="pk-item__title">{displayTitle}</Text>
                        <Text className="pk-item__desc">{formatTime(item.date)}</Text>
                      </View>

                      <View className="pk-item__amount-wrap">
                        <Text className={`amount ${item.type === 1 ? 'exp' : 'inc'}`}>
                          {/* 强制显示符号 */}
                          {symbol}{amountTxt}
                        </Text>
                      </View>
                      <View className="divider" />
                    </View>
                  </View>
                );
              })}
            </View>
          </View>
        );
      })}
    </View>
  );
};
```

### src/components/business/MonthPicker.tsx

```typescript
import { View, Text, Picker } from '@tarojs/components';
import { CustomModal } from '../base/CustomModal';

interface MonthPickerProps {
  visible: boolean;
  selectedMonth: number; // 格式：202602
  onClose: () => void;
  onConfirm: (month: number) => void;
}

export const MonthPicker = ({
  visible,
  selectedMonth,
  onClose,
  onConfirm
}: MonthPickerProps) => {
  // 生成近24个月的选项
  const generateMonths = () => {
    const months = [];
    const now = new Date();
    
    for (let i = 23; i >= 0; i--) {
      const date = new Date(now.getFullYear(), now.getMonth() - i, 1);
      const year = date.getFullYear();
      const month = date.getMonth() + 1;
      const value = year * 100 + month;
      const label = `${year}年${month}月`;
      months.push({ value, label });
    }
    
    return months;
  };

  const months = generateMonths();
  const currentValue = selectedMonth.toString();

  const handleChange = (e) => {
    const value = parseInt(e.detail.value);
    onConfirm(value);
  };

  return (
    <CustomModal
      visible={visible}
      title="选择月份"
      onClose={onClose}
      footer={null}
    >
      <View style={{ padding: '20px 0' }}>
        <Picker
          mode="selector"
          range={months.map(m => m.label)}
          rangeKey="label"
          value={months.findIndex(m => m.value === selectedMonth)}
          onChange={handleChange}
        >
          <View style={{ padding: '20px', textAlign: 'center', fontSize: 24 }}>
            {months.find(m => m.value === selectedMonth)?.label}
          </View>
        </Picker>
      </View>
    </CustomModal>
  );
};
```

### src/components/business/PKKeyboard.scss

```scss
.pk-keyboard {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: white;
  border-top: 1px solid var(--pk-border);
  z-index: 999;

  &__row {
    display: flex;

    &-item {
      flex: 1;
      height: 80px;
      display: flex;
      align-items: center;
      justify-content: center;
      border-right: 1px solid var(--pk-border);
      border-bottom: 1px solid var(--pk-border);
      font-size: 32px;
      font-weight: 500;
      cursor: pointer;

      &:nth-child(3n) {
        border-right: none;
      }

      &:last-child {
        background-color: #F5F5F5;
      }

      &:active {
        background-color: #F0F0F0;
      }
    }
  }
}
```

### src/components/business/PKKeyboard.tsx

```typescript
import { View, Text } from '@tarojs/components';
import './PKKeyboard.scss';

interface PKKeyboardProps {
  onKeyPress: (key: string) => void;
}

export const PKKeyboard = ({ onKeyPress }: PKKeyboardProps) => {
  const keys = [
    ['1', '2', '3'],
    ['4', '5', '6'],
    ['7', '8', '9'],
    ['.', '0', 'del']
  ];

  return (
    <View className="pk-keyboard">
      {keys.map((row, rowIndex) => (
        <View key={rowIndex} className="pk-keyboard__row">
          {row.map((key) => (
            <View
              key={key}
              className="pk-keyboard__row-item"
              onClick={() => onKeyPress(key)}
            >
              <Text>{key === 'del' ? '←' : key}</Text>
            </View>
          ))}
        </View>
      ))}
    </View>
  );
};
```

### src/components/business/TimePicker.scss

```scss
.pk-time-picker {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: white;
  border-top-left-radius: 16px;
  border-top-right-radius: 16px;
  box-shadow: 0 -4px 20px rgba(0, 0, 0, 0.1);
  z-index: 999;

  &__header {
    padding: 16px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid var(--pk-border);

    &-btn {
      font-size: 24px;
      padding: 8px 16px;
      cursor: pointer;

      &.confirm {
        color: var(--pk-primary);
        font-weight: 600;
      }
    }
  }

  &__content {
    padding: 20px;
  }
}
```

### src/components/business/TimePicker.tsx

```typescript
import { View, Text, Picker } from '@tarojs/components';
import dayjs from 'dayjs';
import './TimePicker.scss';

interface TimePickerProps {
  visible: boolean;
  value: Date;
  onClose: () => void;
  onConfirm: (date: Date) => void;
}

export const TimePicker = ({
  visible,
  value,
  onClose,
  onConfirm
}: TimePickerProps) => {
  if (!visible) return null;

  const currentDate = dayjs(value);
  const dateString = currentDate.format('YYYY-MM-DD');
  const timeString = currentDate.format('HH:mm');

  const handleDateChange = (e) => {
    const newDate = dayjs(e.detail.value + ' ' + timeString);
    onConfirm(newDate.toDate());
  };

  const handleTimeChange = (e) => {
    const newDate = dayjs(dateString + ' ' + e.detail.value);
    onConfirm(newDate.toDate());
  };

  return (
    <View className="pk-time-picker">
      <View className="pk-time-picker__header">
        <Text className="pk-time-picker__header-btn" onClick={onClose}>取消</Text>
        <Text className="pk-time-picker__header-btn confirm" onClick={onClose}>确定</Text>
      </View>
      <View className="pk-time-picker__content">
        <Picker
          mode="date"
          value={dateString}
          start="2020-01-01"
          end="2030-12-31"
          onChange={handleDateChange}
        >
          <View style={{ padding: '16px', fontSize: 24 }}>{dateString}</View>
        </Picker>
        <Picker
          mode="time"
          value={timeString}
          onChange={handleTimeChange}
        >
          <View style={{ padding: '16px', fontSize: 24 }}>{timeString}</View>
        </Picker>
      </View>
    </View>
  );
};
```

### src/constants/categories.ts

```typescript
export interface Category {
  id: string;
  name: string;
  color: string;
  type: 1 | 2; // 1: 支出, 2: 收入
}

export const CATEGORIES: Category[] = [
  // 支出分类
  {
    id: 'food',
    name: '餐饮',
    color: '#FF6B6B',
    type: 1
  },
  {
    id: 'transport',
    name: '交通',
    color: '#4ECDC4',
    type: 1
  },
  {
    id: 'shopping',
    name: '购物',
    color: '#45B7D1',
    type: 1
  },
  {
    id: 'entertainment',
    name: '娱乐',
    color: '#96CEB4',
    type: 1
  },
  {
    id: 'medical',
    name: '医疗',
    color: '#FECA57',
    type: 1
  },
  {
    id: 'education',
    name: '教育',
    color: '#FF9FF3',
    type: 1
  },
  {
    id: 'housing',
    name: '住房',
    color: '#54A0FF',
    type: 1
  },
  {
    id: 'other_expense',
    name: '其他',
    color: '#95A5A6',
    type: 1
  },
  
  // 收入分类
  {
    id: 'salary',
    name: '工资',
    color: '#1DD1A1',
    type: 2
  },
  {
    id: 'bonus',
    name: '奖金',
    color: '#10AC84',
    type: 2
  },
  {
    id: 'investment',
    name: '投资',
    color: '#00B894',
    type: 2
  },
  {
    id: 'other_income',
    name: '其他',
    color: '#00CEC9',
    type: 2
  }
];
```

### src/constants/quotes.ts

```typescript
export const QUOTES = [
  "简约生活，品质人生",
  "花钱容易，赚钱难，省着点花",
  "每一分钱都有它的价值",
  "理财从记账开始",
  "量入为出，理性消费",
  "省钱就是赚钱",
  "小小记账，大大收获",
  "记录生活，管理财富",
  "花钱要三思，记账要坚持",
  "财富积累从点滴开始"
];
```

### src/hooks/useNavInfo.ts

```typescript
import { useMemo } from 'react';
import Taro from '@tarojs/taro';

export const useNavInfo = () => {
  return useMemo(() => {
    const info = Taro.getSystemInfoSync();
    const statusBarHeight = info.statusBarHeight;
    const navContentHeight = 44;
    const navHeight = statusBarHeight + navContentHeight;
    const menuButtonWidth = info.screenWidth - info.windowWidth;

    return {
      statusBarHeight,
      navContentHeight,
      navHeight,
      menuButtonWidth
    };
  }, []);
};
```

### src/pages/index/index.config.ts

```typescript
export default {
  navigationBarTitleText: 'PureKeep',
  usingComponents: {}
};
```

### src/pages/index/index.scss

```scss
.main-content {
  padding-left: 32px;
  padding-right: 32px;
}

.overview-card {
  margin: 20px 0 40px;
  display: flex;
  flex-direction: column; /* 垂直布局 */
  gap: 16px;

  /* Row 1: 顶部栏 */
  .card-header-row {
    display: flex;
    justify-content: space-between;
    align-items: center;

    .month-picker {
      display: inline-flex;
      align-items: center;
      background: #F2F4F5;
      padding: 8px 20px;
      border-radius: 30px;
      transition: opacity 0.2s;

      &:active { opacity: 0.7; }

      .curr-month {
        color: var(--pk-text-main);
        font-size: 26px; /* 稍微调小适配顶部 */
        font-weight: 600;
        margin-right: 10px;
      }

      .arrow-down {
        width: 0; height: 0;
        border-left: 6px solid transparent;
        border-right: 6px solid transparent;
        border-top: 8px solid var(--pk-text-sub);
        opacity: 0.8;
        transform: translateY(2px);
      }
    }

    .greeting-box {
      .hello {
        display: flex;
        align-items: center;
        gap: 6px;
        .text { font-size: 24px; color: var(--pk-text-sub); }
      }
    }
  }

  /* Row 2: 支出数据 */
  .expense-display-row {
    display: flex;
    align-items: flex-end; /* 底部对齐，实现基线对齐效果 */
    padding-left: 8px; /* 微调左间距 */

    .left-stack {
      display: flex;
      flex-direction: column;
      align-items: flex-end; /* 靠右对齐：让文字和符号右侧对齐 */
      margin-right: 16px;
      margin-bottom: 8px; /* 微调基线高度 */
      flex-shrink: 0; /* 防止被挤压 */

      .label {
        font-size: 22px;
        color: var(--pk-text-sub);
        opacity: 0.8;
        margin-bottom: 4px;
      }

      .symbol {
        font-size: 32px; /* 中等字号 */
        color: var(--pk-text-main);
        font-weight: 600;
        font-family: 'DIN Alternate';
        line-height: 1;
      }
    }

    .right-amount-container {
      flex: 1;
      position: relative;
      overflow: hidden;

      .amount-scroll {
        width: 100%;
        white-space: nowrap;
      }

      .amount-val {
        font-size: 64px; /* 约等于左侧 (22+4+32=58px) 的 110%?
        用户要求是 "文字+符号" 的 75%?
        左侧总高约 60px。60 * 0.75 = 45px 显得太小。
        按视觉平衡调整：保持 60px 左右，视觉上看起来更协调。
        这里设为 64px，DIN 字体通常偏小，实际视觉高度约 50px */
        font-weight: bold;
        color: var(--pk-text-main);
        font-family: 'DIN Alternate';
        line-height: 1;
      }

      /* 渐变遮罩 */
      .fade-mask {
        position: absolute;
        top: 0; right: 0; bottom: 0;
        width: 40px;
        background: linear-gradient(to right, transparent, var(--pk-bg));
        pointer-events: none;
      }
    }
  }
}

/* FAB 样式提取 */
.fab-btn {
  position: fixed;
  bottom: 60px; /*稍微往上提一点 */
  right: 40px;
  width: 128px; /* 增大尺寸 */
  height: 128px; /* 增大尺寸 */
  border-radius: 64px;
  background: var(--pk-primary);
  box-shadow: 0 8px 20px rgba(122, 158, 159, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  font-size: 70px; /* 图标也放大 */
  font-weight: 300;
  z-index: 100;
  transition: transform 0.1s;

  &:active { transform: scale(0.9); }
}

/* 添加到文件末尾 */
.footer-quote {
  padding: 60px 0 100px; /* 上下留白 */
  text-align: center;

  text {
    font-size: 24px;
    font-weight: 300; /* 极细 */
    color: #C5C5C5; /* 极浅灰，不干扰视线 */
    letter-spacing: 2px;
    font-family: serif; /* 衬线体更有文学感 */
    font-style: italic;
  }
}

/* 批量操作条样式重构 */
.batch-delete-bar {
  position: fixed;
  bottom: 40px;
  left: 32px;
  right: 32px;
  height: 100px;
  z-index: 999;
  transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);

  /* 容器背景，变成一个白色悬浮条，带阴影 */
  .batch-inner {
    width: 100%;
    height: 100%;
    background: #FFF;
    border-radius: 50px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 8px; /* 内边距 */
    box-shadow: 0 8px 30px rgba(0, 0, 0, 0.12);
  }

  /* 左侧全选 */
  .select-all-btn {
    display: flex;
    align-items: center;
    gap: 12px;
    padding-left: 24px;
    height: 100%;

    .circle {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      border: 2px solid var(--pk-border);
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all 0.2s;

      &.checked {
        background: var(--pk-text-main); /* 全选用深色，区分删除红 */
        border-color: var(--pk-text-main);
      }
    }

    text {
      font-size: 28px;
      color: var(--pk-text-main);
      font-weight: 500;
    }
  }

  /* 右侧删除 */
  .delete-btn {
    height: 100%;
    padding: 0 48px;
    background: #FF4D4F;
    border-radius: 42px; /* 稍微小一点圆角以适配内嵌 */
    display: flex;
    align-items: center;
    justify-content: center;

    text {
      color: #fff;
      font-size: 28px;
      font-weight: 600;
    }

    &:active { opacity: 0.9; transform: scale(0.98); }
  }
}

/* 日期分组头的复选框样式 */
.pk-checkbox-group {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border: 2px solid var(--pk-border);
  margin-right: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
  background: #fff;

  &.checked {
    background: var(--pk-text-main); /* 使用主文字色，低调 */
    border-color: var(--pk-text-main);
    animation: popIn 0.2s;
  }
}

@keyframes popIn {
  0% { transform: scale(0.8); }
  50% { transform: scale(1.1); }
  100% { transform: scale(1); }
}
```

### src/pages/index/index.tsx

```typescript
import Taro from '@tarojs/taro';
import { View, Text, ScrollView } from '@tarojs/components';
import { useState, useMemo } from 'react';
import { useNavInfo } from '../../hooks/useNavInfo';
import { Navbar } from '../../components/base/Navbar';
import { Drawer } from '../../components/base/Drawer'; // 引入 Drawer
import { EditorPanel } from '../../components/business/EditorPanel';
import { LedgerList } from '../../components/business/LedgerList';
import { LedgerDetailModal } from '../../components/business/LedgerDetailModal';
import { MonthPicker } from '../../components/business/MonthPicker'; // 引入新组件
import { LedgerItem } from '../../types/ledger';
import { useLedgerStore } from '../../store/useLedgerStore';
import { formatAmount } from '../../utils/currency';
import { QUOTES } from '../../constants/quotes';
import dayjs from 'dayjs'; // 确保引入 dayjs
import { Icon } from '../../components/base/Icon'; // 引入 Icon
import './index.scss';

export default function Index() {
  const { navHeight } = useNavInfo();

  // 🔥 1. 使用新版 Store 的选择器和状态
  const {
    getCurrentMonthLedgers,
    getCurrentMonthExpense,
    selectedMonth,
    setSelectedMonth,
    batchDeleteLedgers, // 🔥 引入批量删除
  } = useLedgerStore();

  // 🔥 2. 响应式获取当前月份的数据 (Store 更新时组件会自动重渲染)
  const currentLedgers = getCurrentMonthLedgers();
  const totalExpense = getCurrentMonthExpense();

  // 🔥 3. 需求 #11: 默认开启记账 (初始值设为 true)
  const [isEditorOpen, setIsEditorOpen] = useState(true);

  // 🔥 状态：控制侧边栏
  const [isDrawerOpen, setIsDrawerOpen] = useState(false);

  // 新增状态
  // 详情弹窗选中的 Item
  const [detailItem, setDetailItem] = useState<LedgerItem | null>(null);
  // 正在编辑的 Item (传给 EditorPanel)
  const [editingItem, setEditingItem] = useState<LedgerItem | null>(null);
  
  // 🔥 新增：多选模式状态
  const [isSelectionMode, setIsSelectionMode] = useState(false);
  const [selectedIds, setSelectedIds] = useState<string[]>([]);
  
  // 🔥 新增：MonthPicker 显示状态
  const [showMonthPicker, setShowMonthPicker] = useState(false);

  // 辅助：将数字月份 (202602) 转为 Picker 需要的字符串格式 ("2026-02")
  const pickerValue = `${Math.floor(selectedMonth / 100)}-${String(selectedMonth % 100).padStart(2, '0')}`;
  const displayMonth = `${Math.floor(selectedMonth / 100)}年${String(selectedMonth % 100).padStart(2, '0')}月`;

  // 辅助：处理月份选择确认
  const handleMonthConfirm = (monthVal: number) => {
    setSelectedMonth(monthVal);
    setShowMonthPicker(false);
  };

  // 处理日期选择
  const handleMonthChange = (e) => {
    const val = e.detail.value; // 格式 "2026-05"
    const newMonth = parseInt(val.replace('-', '')); // 转为 202605
    setSelectedMonth(newMonth);
  };

  // 3. 处理点击列表项
  const handleItemClick = (item: LedgerItem) => {
    setDetailItem(item); // 打开详情弹窗
  };

  // 1. 长按触发多选
  const handleLongPress = (item: LedgerItem) => {
    if (!isSelectionMode) {
      Taro.vibrateShort({ type: 'medium' });
      setIsSelectionMode(true);
      setSelectedIds([item._id]);
    }
  };

  // 2. 多选模式下的点击选择
  const handleSelect = (item: LedgerItem) => {
    const isSelected = selectedIds.includes(item._id);
    if (isSelected) {
      const newIds = selectedIds.filter(id => id !== item._id);
      setSelectedIds(newIds);
      // 如果减到 0，自动退出多选模式？(可选，这里保留模式)
      if (newIds.length === 0) setIsSelectionMode(false);
    } else {
      setSelectedIds([...selectedIds, item._id]);
    }
    Taro.vibrateShort({ type: 'light' });
  };

  // 3. 全选/取消全选
  const handleToggleSelectAll = () => {
    if (selectedIds.length === currentLedgers.length) {
      setSelectedIds([]); // 全不选
    } else {
      setSelectedIds(currentLedgers.map(i => i._id)); // 全选
    }
  };

  // 4. 批量删除执行
  const handleBatchDelete = () => {
    if (selectedIds.length === 0) return;

    Taro.showModal({
      title: '确认删除',
      content: `确定要删除这 ${selectedIds.length} 条记录吗？`,
      confirmColor: '#FF4D4F',
      success: (res) => {
        if (res.confirm) {
          batchDeleteLedgers(selectedIds);
          setIsSelectionMode(false);
          setSelectedIds([]);
          Taro.showToast({ title: '已删除', icon: 'success' });
        }
      }
    });
  };

  // 5. 退出多选模式
  const exitSelectionMode = () => {
    setIsSelectionMode(false);
    setSelectedIds([]);
  };

  // 4. 处理从详情页点击"编辑"
  const handleStartEdit = (item: LedgerItem) => {
    setDetailItem(null); // 关闭详情
    setEditingItem(item); // 设置编辑对象
    setIsEditorOpen(true); // 打开编辑器
  };

  // 5. 处理 FAB 点击 (新增模式)
  const handleFabClick = () => {
    setEditingItem(null); // 清空编辑对象，确保是新增模式
    setIsEditorOpen(true);
  };

  // 6. 随机获取一句语录 (使用 useMemo 避免每次渲染都变)
  const randomQuote = useMemo(() => {
    const idx = Math.floor(Math.random() * QUOTES.length);
    return QUOTES[idx];
  }, []);

  // 🔥 新增：简单的问候语逻辑
  const greetingData = useMemo(() => {
    const hour = new Date().getHours();
    const isDay = hour >= 6 && hour < 18;
    let text = '晚上好';
    if (hour < 6) text = '夜深了';
    else if (hour < 12) text = '上午好';
    else if (hour < 18) text = '下午好';

    return {
      text,
      icon: isDay ? 'sun' : 'moon' as const,
      dateStr: dayjs().format('MM月DD日 dddd') // 例如：02月07日 星期六
    };
  }, []);

  return (
    <View
      className='index'
      style={{ '--pk-nav-height': `${navHeight}px` } as React.CSSProperties}
    >
      {/* Navbar 适配 */}
      <Navbar
        title={isSelectionMode ? `已选 ${selectedIds.length} 项` : 'PureKeep'}
        onMenuClick={isSelectionMode ? exitSelectionMode : () => setIsDrawerOpen(true)} // 多选时左侧变"返回/退出"
        rightAction={
          isSelectionMode ? (
            <View onClick={handleToggleSelectAll} style={{ fontSize: '14px', color: '#4A4A4A', padding: '10px' }}>
              {selectedIds.length === currentLedgers.length ? '取消全选' : '全选'}
            </View>
          ) : null
        }
      />

      <View
        className="main-content"
        style={{ paddingTop: `${navHeight}px`, paddingBottom: '200px' }}
      >
        {/* 重构：Overview Card */}
        <View className="overview-card">

          {/* Row 1: 顶部工具栏 (月份选择 + 问候) */}
          <View className="card-header-row">
            <View className="month-picker" onClick={() => setShowMonthPicker(true)}>
              <Text className="curr-month">{displayMonth}</Text>
              <View className="arrow-down" />
            </View>

            <View className="greeting-box">
              <View className="hello">
                <Icon name={greetingData.icon} size={14} color="#999" />
                <Text className="text">{greetingData.text}</Text>
              </View>
            </View>
          </View>

          {/* Row 2: 核心支出数据 (左右布局) */}
          <View className="expense-display-row">
            {/* 左侧堆叠：标签 + 符号 */}
            <View className="left-stack">
              <Text className="label">本月支出</Text>
              <Text className="symbol">¥</Text>
            </View>

            {/* 右侧：超大金额滚动区 */}
            <View className="right-amount-container">
              <ScrollView scrollX className="amount-scroll" showScrollbar={false}>
                <Text className="amount-val">{formatAmount(totalExpense)}</Text>
              </ScrollView>
              {/* 渐变遮罩 (CSS实现) */}
              <View className="fade-mask" />
            </View>
          </View>

        </View>

        {/* List 传入多选 Props */}
        <LedgerList
          ledgers={currentLedgers}
          onItemClick={handleItemClick}
          selectionMode={isSelectionMode}
          selectedIds={selectedIds}
          onLongPress={handleLongPress}
          onSelect={handleSelect}
        />

        {/* 3. 在 List 下方添加语录 */}
        <View className="footer-quote">
          <Text>{randomQuote}</Text>
        </View>

      </View>

      {/* FAB 按钮：多选模式下隐藏 */}
      <View
        className="fab-btn"
        style={{
          transform: isEditorOpen || isSelectionMode ? 'scale(0)' : 'scale(1)', // 多选时也隐藏
          opacity: isEditorOpen || isSelectionMode ? 0 : 1,
          transition: 'all 0.2s ease'
        }}
        onClick={handleFabClick}
      >
        +
      </View>

      {/* 🔥 新增：底部批量删除条 */}
      <View
        className="batch-delete-bar"
        style={{
          transform: isSelectionMode ? 'translateY(0)' : 'translateY(200%)', // 动画进出
        }}
      >
        <View className="delete-btn" onClick={handleBatchDelete}>
          <Text>删除 ({selectedIds.length})</Text>
        </View>
      </View>

      {/* EditorPanel 传入 editItem */}
      <EditorPanel
        visible={isEditorOpen}
        onClose={() => setIsEditorOpen(false)}
        editItem={editingItem} // 传入
      />

      {/* 挂载详情弹窗 */}
      <LedgerDetailModal
        visible={!!detailItem}
        ledger={detailItem}
        onClose={() => setDetailItem(null)}
        onEdit={handleStartEdit}
      />

      {/* 🔥 挂载 Drawer (预留同步入口) */}
      <Drawer visible={isDrawerOpen} onClose={() => setIsDrawerOpen(false)} />

      {/* 挂载新的 MonthPicker */}
      <MonthPicker
        visible={showMonthPicker}
        selectedMonth={selectedMonth}
        onClose={() => setShowMonthPicker(false)}
        onConfirm={handleMonthConfirm}
      />
    </View>
  );
}
```

### src/store/useLedgerStore.ts

```typescript
import { create } from 'zustand';
import { LedgerItem } from '../types/ledger';
import { generateId } from '../utils/id';

interface LedgerStore {
  // 状态
  ledgers: LedgerItem[];
  selectedMonth: number; // 格式：202602
  
  // 选择器
  getCurrentMonthLedgers: () => LedgerItem[];
  getCurrentMonthExpense: () => number;
  getCurrentMonthIncome: () => number;
  
  // 操作
  addLedger: (data: Omit<LedgerItem, '_id'>) => void;
  updateLedger: (id: string, data: Partial<LedgerItem>) => void;
  deleteLedger: (id: string) => void;
  batchDeleteLedgers: (ids: string[]) => void;
  setSelectedMonth: (month: number) => void;
  clearAllLedgers: () => void;
  addTestData: () => void;
}

// 获取当前月份的数字格式 (例如：202602)
const getCurrentMonth = (): number => {
  const now = new Date();
  return now.getFullYear() * 100 + (now.getMonth() + 1);
};

// 从本地存储加载数据
const loadFromStorage = (): LedgerItem[] => {
  try {
    const data = localStorage.getItem('purekeep-ledgers');
    return data ? JSON.parse(data) : [];
  } catch (error) {
    console.error('Failed to load ledgers from storage:', error);
    return [];
  }
};

// 保存数据到本地存储
const saveToStorage = (ledgers: LedgerItem[]) => {
  try {
    localStorage.setItem('purekeep-ledgers', JSON.stringify(ledgers));
  } catch (error) {
    console.error('Failed to save ledgers to storage:', error);
  }
};

export const useLedgerStore = create<LedgerStore>((set, get) => ({
  // 初始状态
  ledgers: loadFromStorage(),
  selectedMonth: getCurrentMonth(),
  
  // 选择器
  getCurrentMonthLedgers: () => {
    const { ledgers, selectedMonth } = get();
    const year = Math.floor(selectedMonth / 100);
    const month = selectedMonth % 100;
    
    return ledgers.filter(ledger => {
      const date = new Date(ledger.date);
      return date.getFullYear() === year && date.getMonth() + 1 === month;
    }).sort((a, b) => new Date(b.date).getTime() - new Date(a.date).getTime());
  },
  
  getCurrentMonthExpense: () => {
    const currentLedgers = get().getCurrentMonthLedgers();
    return currentLedgers
      .filter(ledger => ledger.type === 1) // 1 表示支出
      .reduce((sum, ledger) => sum + ledger.amount, 0);
  },
  
  getCurrentMonthIncome: () => {
    const currentLedgers = get().getCurrentMonthLedgers();
    return currentLedgers
      .filter(ledger => ledger.type === 2) // 2 表示收入
      .reduce((sum, ledger) => sum + ledger.amount, 0);
  },
  
  // 操作
  addLedger: (data) => {
    const newLedger: LedgerItem = {
      ...data,
      _id: generateId()
    };
    
    const updatedLedgers = [...get().ledgers, newLedger];
    set({ ledgers: updatedLedgers });
    saveToStorage(updatedLedgers);
  },
  
  updateLedger: (id, data) => {
    const updatedLedgers = get().ledgers.map(ledger => 
      ledger._id === id ? { ...ledger, ...data } : ledger
    );
    set({ ledgers: updatedLedgers });
    saveToStorage(updatedLedgers);
  },
  
  deleteLedger: (id) => {
    const updatedLedgers = get().ledgers.filter(ledger => ledger._id !== id);
    set({ ledgers: updatedLedgers });
    saveToStorage(updatedLedgers);
  },
  
  batchDeleteLedgers: (ids) => {
    const updatedLedgers = get().ledgers.filter(ledger => !ids.includes(ledger._id));
    set({ ledgers: updatedLedgers });
    saveToStorage(updatedLedgers);
  },
  
  setSelectedMonth: (month) => {
    set({ selectedMonth: month });
  },
  
  clearAllLedgers: () => {
    set({ ledgers: [] });
    saveToStorage([]);
  },
  
  addTestData: () => {
    const testData: Omit<LedgerItem, '_id'>[] = [
      {
        amount: 35.5,
        type: 1,
        categoryId: 'food',
        note: '午餐',
        date: new Date(Date.now() - 86400000).toISOString()
      },
      {
        amount: 120,
        type: 1,
        categoryId: 'transport',
        note: '打车',
        date: new Date(Date.now() - 172800000).toISOString()
      },
      {
        amount: 5000,
        type: 2,
        categoryId: 'salary',
        note: '月薪',
        date: new Date(Date.now() - 259200000).toISOString()
      }
    ];
    
    const newLedgers = testData.map(data => ({
      ...data,
      _id: generateId()
    }));
    
    const updatedLedgers = [...get().ledgers, ...newLedgers];
    set({ ledgers: updatedLedgers });
    saveToStorage(updatedLedgers);
  }
}));
```

### src/styles/animations.scss

```scss
/* 淡入动画 */
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

/* 滑入动画 */
@keyframes slideUp {
  from {
    transform: translateY(100%);
  }
  to {
    transform: translateY(0);
  }
}

@keyframes slideDown {
  from {
    transform: translateY(-100%);
  }
  to {
    transform: translateY(0);
  }
}

@keyframes slideInRight {
  from {
    transform: translateX(100%);
  }
  to {
    transform: translateX(0);
  }
}

/* 弹出动画 */
@keyframes popIn {
  0% {
    transform: scale(0.8);
    opacity: 0;
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

/* 抖动动画 */
@keyframes shake {
  0%, 100% {
    transform: translateX(0);
  }
  25% {
    transform: translateX(-5px);
  }
  75% {
    transform: translateX(5px);
  }
}

/* 呼吸动画 */
@keyframes breathe {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.7;
  }
}

/* 应用动画的类 */
.fade-in {
  animation: fadeIn 0.3s ease;
}

.slide-up {
  animation: slideUp 0.3s ease;
}

.slide-down {
  animation: slideDown 0.3s ease;
}

.slide-in-right {
  animation: slideInRight 0.3s ease;
}

.pop-in {
  animation: popIn 0.3s ease;
}

.shake {
  animation: shake 0.5s ease;
}

.breathe {
  animation: breathe 2s infinite;
}
```

### src/styles/variables.scss

```scss
/* 主题变量 */
:root {
  // 主色调
  --pk-primary: #7A9E9F;
  --pk-secondary: #A8C3C4;
  
  // 功能色
  --pk-income: #52C41A;
  --pk-expense: #FF4D4F;
  
  // 文本色
  --pk-text-main: #333333;
  --pk-text-sub: #666666;
  --pk-text-light: #999999;
  
  // 背景色
  --pk-bg: #F8F9FA;
  --pk-white: #FFFFFF;
  
  // 边框色
  --pk-border: #E8E8E8;
  
  // 阴影
  --pk-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  --pk-shadow-lg: 0 4px 16px rgba(0, 0, 0, 0.15);
  
  // 圆角
  --pk-radius-sm: 4px;
  --pk-radius-md: 8px;
  --pk-radius-lg: 16px;
  --pk-radius-full: 9999px;
  
  // 间距
  --pk-space-xs: 4px;
  --pk-space-sm: 8px;
  --pk-space-md: 16px;
  --pk-space-lg: 24px;
  --pk-space-xl: 32px;
  
  // 字体大小
  --pk-font-xs: 12px;
  --pk-font-sm: 14px;
  --pk-font-md: 16px;
  --pk-font-lg: 20px;
  --pk-font-xl: 24px;
  --pk-font-xxl: 32px;
  --pk-font-xxxl: 48px;
  
  // 字体粗细
  --pk-font-light: 300;
  --pk-font-regular: 400;
  --pk-font-medium: 500;
  --pk-font-semibold: 600;
  --pk-font-bold: 700;
  
  // 过渡
  --pk-transition-fast: 0.15s ease;
  --pk-transition-normal: 0.3s ease;
  --pk-transition-slow: 0.5s ease;
}

/* 暗色模式变量 */
@media (prefers-color-scheme: dark) {
  :root {
    --pk-primary: #9AC1C2;
    --pk-secondary: #7A9E9F;
    --pk-text-main: #F0F0F0;
    --pk-text-sub: #B0B0B0;
    --pk-text-light: #808080;
    --pk-bg: #1A1A1A;
    --pk-white: #2A2A2A;
    --pk-border: #3A3A3A;
  }
}
```

### src/types/ledger.d.ts

```typescript
export interface LedgerItem {
  _id: string;
  amount: number;
  type: 1 | 2; // 1: 支出, 2: 收入
  categoryId: string;
  note?: string;
  date: string; // ISO 日期字符串
}
```

### src/utils/currency.ts

```typescript
/**
 * 格式化金额
 * @param amount 金额
 * @returns 格式化后的金额字符串
 */
export const formatCurrency = (amount: number): string => {
  return `¥${amount.toFixed(2)}`;
};

/**
 * 格式化金额（不带货币符号）
 * @param amount 金额
 * @returns 格式化后的金额字符串
 */
export const formatAmount = (amount: number): string => {
  return amount.toFixed(2);
};

/**
 * 解析金额字符串
 * @param value 金额字符串
 * @returns 解析后的金额
 */
export const parseAmount = (value: string): number => {
  const parsed = parseFloat(value);
  return isNaN(parsed) ? 0 : parsed;
};
```

### src/utils/date.ts

```typescript
import dayjs from 'dayjs';

/**
 * 格式化时间
 * @param date 日期字符串或Date对象
 * @returns 格式化后的时间字符串 (HH:mm)
 */
export const formatTime = (date: string | Date): string => {
  return dayjs(date).format('HH:mm');
};

/**
 * 格式化日期
 * @param date 日期字符串或Date对象
 * @param format 格式化模板
 * @returns 格式化后的日期字符串
 */
export const formatDate = (date: string | Date, format: string = 'YYYY-MM-DD'): string => {
  return dayjs(date).format(format);
};

/**
 * 获取当前月份的开始和结束日期
 * @param year 年份
 * @param month 月份 (1-12)
 * @returns 包含开始和结束日期的对象
 */
export const getMonthRange = (year: number, month: number) => {
  const startDate = dayjs(`${year}-${month}-01`);
  const endDate = startDate.endOf('month');
  
  return {
    start: startDate.toISOString(),
    end: endDate.toISOString()
  };
};

/**
 * 获取相对时间描述
 * @param date 日期字符串或Date对象
 * @returns 相对时间描述
 */
export const getRelativeTime = (date: string | Date): string => {
  const now = dayjs();
  const target = dayjs(date);
  const diffDays = now.diff(target, 'day');
  
  if (diffDays === 0) {
    return '今天';
  } else if (diffDays === 1) {
    return '昨天';
  } else if (diffDays === 2) {
    return '前天';
  } else if (diffDays < 7) {
    return `${diffDays}天前`;
  } else {
    return target.format('MM-DD');
  }
};
```

### src/utils/group.ts

```typescript
import { LedgerItem } from '../types/ledger';
import { CATEGORIES } from '../constants/categories';
import dayjs from 'dayjs';

/**
 * 按天分组账单
 * @param ledgers 账单列表
 * @returns 按天分组的账单数据
 */
export const groupLedgersByDay = (ledgers: LedgerItem[]) => {
  const groups: Record<string, {
    dateStr: string;
    dayLabel: string;
    items: LedgerItem[];
    totalIncome: number;
    totalExpense: number;
  }> = {};

  ledgers.forEach(ledger => {
    const date = dayjs(ledger.date);
    const dateStr = date.format('YYYY-MM-DD');
    const dayLabel = getDayLabel(date);

    if (!groups[dateStr]) {
      groups[dateStr] = {
        dateStr,
        dayLabel,
        items: [],
        totalIncome: 0,
        totalExpense: 0
      };
    }

    groups[dateStr].items.push(ledger);
    
    if (ledger.type === 1) {
      groups[dateStr].totalExpense += ledger.amount;
    } else {
      groups[dateStr].totalIncome += ledger.amount;
    }
  });

  // 按日期降序排序
  return Object.values(groups).sort((a, b) => 
    new Date(b.dateStr).getTime() - new Date(a.dateStr).getTime()
  );
};

/**
 * 获取日期标签
 * @param date 日期对象
 * @returns 日期标签
 */
const getDayLabel = (date: dayjs.Dayjs): string => {
  const today = dayjs();
  const yesterday = today.subtract(1, 'day');
  
  if (date.isSame(today, 'day')) {
    return '今天';
  } else if (date.isSame(yesterday, 'day')) {
    return '昨天';
  } else {
    return date.format('dddd'); // 星期几
  }
};

/**
 * 根据ID获取分类信息
 * @param id 分类ID
 * @returns 分类信息
 */
export const getCategoryById = (id: string) => {
  return CATEGORIES.find(cat => cat.id === id) || CATEGORIES[0];
};

/**
 * 按分类统计金额
 * @param ledgers 账单列表
 * @returns 按分类统计的数据
 */
export const getCategoryStats = (ledgers: LedgerItem[]) => {
  const stats: Record<string, {
    category: typeof CATEGORIES[0];
    amount: number;
  }> = {};

  ledgers.forEach(ledger => {
    if (!stats[ledger.categoryId]) {
      stats[ledger.categoryId] = {
        category: getCategoryById(ledger.categoryId),
        amount: 0
      };
    }
    stats[ledger.categoryId].amount += ledger.amount;
  });

  return Object.values(stats).sort((a, b) => b.amount - a.amount);
};
```

### src/utils/id.ts

```typescript
/**
 * 生成唯一ID
 * @returns 唯一ID字符串
 */
export const generateId = (): string => {
  return Date.now().toString(36) + Math.random().toString(36).substr(2);
};

/**
 * 生成短ID
 * @returns 短ID字符串
 */
export const generateShortId = (): string => {
  return Math.random().toString(36).substr(2, 9);
};
```
