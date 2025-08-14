# _vuetest

這是一個以 Vue 3 建立的便利貼應用，支援多張便利貼（Sticky Notes），每張便利貼可自訂顏色，並可編輯、勾選完成狀態、刪除事項。資料會儲存在瀏覽器的 localStorage，重整畫面後依然保留使用者內容。



##	Features

- 代辦事項新增與刪除  
- 點兩下事項可進行編輯  
- 勾選事項代表完成狀態  
- 刪除已完成事項  
- 點擊日期可修改便利貼日期  
- 支援新增多張便利貼，每張可設定不同背景色  
- 資料持久化儲存在 localStorage  

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint