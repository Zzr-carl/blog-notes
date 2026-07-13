# 2026-07-15 博客StarBorder修复+UI改版

## 操作记录

### 1. StarBorder 星光边框修复
**问题：** 之前继承的 StarBorder 流光边框效果不对，蓝色发光看不见。
**原因：** `PostCard.astro` CSS 覆盖层把两个径向渐变椭圆都居中（`left: -50%`），导致发光部分被 `overflow: hidden` 切掉，肉眼不可见。

**修复方案（改用 Codex Desktop）：**
- 颜色从蓝色 `#60a5fa` 改为橙黄色 `#f5a623`
- 定位从百分比改为固定像素（`height: 100px`, `top: -50px`），解决短卡片无效果的问题
- 改用 `radial-gradient(ellipse 45px 7px, ...)` 精确控制光弧形状，横向拉长贴合卡片宽度
- 动画 `5s linear infinite alternate`

**涉及文件：**
- `src/components/layout/PostCard.astro`
- `src/components/effects/StarBorder.tsx`
- `src/components/effects/StarBorder.css`

### 2. 导航栏精简
- 删除右上角音乐播放器入口按钮
- 删除色调切换按钮和主题面板
- 保留：搜索、显示设置、导航菜单按钮

**涉及文件：** `Navbar.astro`、`Layout.astro`

### 3. 音乐播放器改版
- 封面放大（`w-14` → `w-16`），间距增加
- 歌词展开按钮从右上角移到卡片底部全宽（带箭头呼吸动画）
- 音量控制改为 iPhone 风格粗柱形垂直滑块（`w-6 x h-16`，`rounded-full`），支持 Pointer Events 拖拽弹性回弹
- 删除喇叭图标和弹出面板

**涉及文件：** `MusicPlayer.astro`

### 4. 分类页面卡片重设计
- 删除圆形文件夹图标
- 替换为圆点 + 竖线极简设计

**涉及文件：** `categories/index.astro`

### 5. 侧边栏分类进度条
- 每行下方加 2px 进度条（文章数/最大数 × 100%）
- Hover 时毛玻璃效果（`backdrop-blur-md` + 微细描边）

**涉及文件：** `ButtonLink.astro`、`Categories.astro`

### 6. 头像波纹动画
- 头像旋转时 4 圈圆环波纹向外发散
- 错开 0.75s 延迟，3s 循环，`scale(1) → scale(1.6)` + `opacity 0.5 → 0`

**涉及文件：** `Profile.astro`、`ProfileCardSimple.tsx`

## Git 提交记录
| Commit | 说明 |
|--------|------|
| `09c0fb8` | StarBorder 星光边框 + 音乐播放器改版 + 分类卡片极简重设计 |
| `3f47d29` | 分类 widget 进度条设计 + 毛玻璃 hover + 头像波纹动画 |

## 关键技术点
- **StarBorder 动画陷阱**：CSS `animation` 简写省略 duration 会重置为 `0s`，`!important` 会覆盖内联 `animation-duration`，必须在简写中显式写明 duration
- **垂直 slider 方向**：`calcVolume = 1 - (y - rect.top) / rect.height`，fill 从底部向上
- **弹性拖拽**：sigmoid 衰减函数 + `scaleY/scaleX` 变换 + `cubic-bezier(0.34, 1.56, 0.64, 1)` 弹簧回弹
- **波纹动画**：`scale()` + `opacity` + `border-width` 三重渐变，`nth-child` 错开延迟
