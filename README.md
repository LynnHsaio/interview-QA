# 面試題目

## 1.

### Q1.

```javascript
function sortUserName(users) {
  return users.sort((a, b) => {
    const keyA =
      (a.firstName || "") + (a.lastName || "") + (a.customerID || "");
    const keyB =
      (b.firstName || "") + (b.lastName || "") + (b.customerID || "");
    return keyA.localeCompare(keyB, "en", { numeric: true });
  });
}
```

### Q2.

('systemAnalytics' > 'engineer' > 'productOwner' > 'freelancer' > 'student')

```javascript
function sortByType(users) {
  const order = {
    systemAnalytics: 1,
    engineer: 2,
    productOwner: 3,
    freelancer: 4,
    student: 5,
  };

  return users.sort((a, b) => {
    return (order[a.profession] || 99) - (order[b.profession] || 99);
  });
}
```

## 2.

### Q1.

```css
.container .shop-list li.item {
  color: green; /* 第一個規則，設置為 green */
}

.container .shop-list .item {
  color: blue; /* 第二個規則，設置為 blue */
}
```

因為第一個選擇器（`.container .shop-list li.item`）比第二個選擇器（`.container .shop-list .item`）多了 `li` 標籤，權重更高，所以覆蓋了第二個選擇器的樣式。

**解決方法：**

方法一：將選擇器改為相同形式

```css
.container .shop-list li.item {
  color: blue;
}
```

方法二：針對第一個列表（`<ol>`）

```css
.container ol.shop-list li.item {
  color: blue;
}
```

### Q2.

使用 `:nth-child()` 偽類選擇器：

```css
.container .shop-list li.item:nth-child(odd) {
  background-color: #f0f0f0; /* 奇數行背景色 */
}
```

## 3.

```javascript
function getUniqueNumber(items) {
  // 1. 建立一個新的 Set 物件，Set 會自動去除重複的元素
  const uniqueSet = new Set(items);

  // 2. 將 Set 轉換回陣列 (Array)
  const uniqueArray = Array.from(uniqueSet);

  // 3. 輸出結果
  console.log(uniqueArray);
}

getUniqueNumber(items);
// 輸出結果: [1, 5, 2, 3, 4, 7, 8, 9, 0, 6]
```

## 4.

### 什麼是虛擬 DOM？它解決了什麼問題？

虛擬 DOM 是 JavaScript 物件，用來描述真實 DOM 的結構。  
如果沒有虛擬 DOM，直接大量、頻繁地操作真實 DOM 會導致畫面卡頓，效率低下。  
使用虛擬 DOM 可以先在記憶體中進行修改，再批次更新真實 DOM，提高效能。

## 5.

### void

- **意思：** 代表函式有正常結束，只是沒有回傳任何有意義的值
- **常見用法：** 用在「執行完就結束，但不回傳東西」的函式

```typescript
function logMessage(msg: string): void {
  console.log(msg);
}
```

這個函式會執行 `console.log`，然後結束，但它實際上會隱含回傳 `undefined`。

### never

- **意思：** 代表函式永遠不會有正常的結束點
- **常見用法：** 用在「這個函式不是拋出錯誤就是進入無窮迴圈」的情況

```typescript
// 永遠拋出錯誤
function throwError(msg: string): never {
  throw new Error(msg);
}

// 無窮迴圈
function infiniteLoop(): never {
  while (true) {}
}
```

這兩個函式都不可能正常「跑到最後並回傳」。

## 6.

**框架型網站：**

- 注重維護、擴展、可組件化
- 開發成本稍高，但長期看效率高

**非框架型網站：**

- 開發快速、初期簡單
- 但大型專案容易混亂，維護成本高

## 7.

### 為什麼「Switch Person」後 `tasks` 沒有如預期切換？

因為當按下 switch person 按鈕時，會觸發更新狀態，重新渲染組件。在渲染過程中，React 會比對新的 react element 和舊的 fiber tree，發現不管是 `<TaskCounter name="Alice" />` 或 `<TaskCounter name="Bob" />` 都是在相同的位置，型別都一樣是 `TaskCounter`，所以 React 會視為是同一個組件，只是 props 不一樣。

因此 `TaskCounter` 組件不會被銷毀重建，而是沿用同一個組件實例，所以原本的狀態會被保留，導致 `tasks` 狀態被共用。

### 如何修正？

把 `tasks` 狀態提升到父層集中管理：

```javascript
function TaskManager() {
  const [currentPerson, setCurrentPerson] = useState("Alice");
  const [tasks, setTasks] = useState({ Alice: 0, Bob: 0 });

  const handleTaskComplete = () => {
    setTasks((prev) => ({
      ...prev,
      [currentPerson]: prev[currentPerson] + 1,
    }));
  };

  const switchPerson = () => {
    setCurrentPerson((prev) => (prev === "Alice" ? "Bob" : "Alice"));
  };

  return (
    <div>
      <TaskCounter
        name={currentPerson}
        tasks={tasks[currentPerson]}
        onTaskComplete={handleTaskComplete}
      />
      <button onClick={switchPerson}>Switch Person</button>
    </div>
  );
}

function TaskCounter({ name, tasks, onTaskComplete }) {
  return (
    <>
      <h1>
        {name}'s tasks: {tasks}
      </h1>
      <button onClick={onTaskComplete}>Complete Task</button>
    </>
  );
}
```

## 8.

### 問題分析

```javascript
const TodoList = () => {
  const [todos, setTodos] = useState([
    { id: 1, text: "學習 React", completed: false, studyPoint: 3 },
    { id: 2, text: "建立專案", completed: false, studyPoint: 1 },
  ]);

  // todos是陣列，所以無法以物件的方式被解構
  const { id, text, studyPoint } = todos;

  const [basePoints, setBasePoints] = useState(3);
  const [sumPoints, setSumPoints] = useState(0);

  const toggleTodo = (id) => {
    // find方法會回傳原本的物件，而不是新的物件
    // 所以直接修改todo物件的completed屬性，原本的todo會改變，違反Immutability不可變性原則
    const todo = todos.find((t) => t.id === id);
    todo.completed = !todo.completed;
    setTodos(todos);
  };

  const handleStudyPointsChange = (e) => {
    // basePoints是狀態，無法被呼叫，應該使用setBasePoints來呼叫
    basePoints(e.target.value);
    // value是未定義的，如果他是想要原本的sumPoints加上新的studyPoint，應該使用函式做為參數
    // 像是setSumPoints(prevState => prevState + parseInt(e.target.value))
    setSumPoints(parseInt(value) + parseInt(e.target.value));
  };

  return (
    <div>
      {/* 這裡應該用map迴圈來將整個todos陣列的課程顯示出來 */}
      <p>課程名稱: {text}</p>
      <label>學習點數: </label>
      <input
        type="number"
        value={studyPoint}
        onChange={handleStudyPointsChange}
      />
      <p>總累積點數: {sumPoints}</p>
      <button onClick={() => toggleTodo(id)}>節選課程</button>
    </div>
  );
};
```

### 改善作法

```javascript
const TodoList2 = () => {
  const [todos, setTodos] = useState([
    { id: 1, text: "學習 React", completed: false, studyPoint: 3 },
    { id: 2, text: "建立專案", completed: false, studyPoint: 1 },
  ]);

  const toggleTodo = (id) => {
    setTodos((prevTodos) =>
      prevTodos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  const updateStudyPoint = (id, newPoint) => {
    setTodos((prevTodos) =>
      prevTodos.map((todo) =>
        todo.id === id ? { ...todo, studyPoint: parseInt(newPoint) || 0 } : todo
      )
    );
  };

  // 計算總點數
  const calculateTotalPoints = () => {
    return todos.reduce((total, todo) => {
      return total + (todo.completed ? todo.studyPoint : 0);
    }, 0);
  };

  return (
    <div>
      <h2>課程清單</h2>
      {todos.map((todo) => (
        <div key={todo.id}>
          <p>課程名稱: {todo.text}</p>
          <label>學習點數: </label>
          <input
            type="number"
            value={todo.studyPoint}
            onChange={(e) => updateStudyPoint(todo.id, e.target.value)}
            disabled={todo.completed}
          />
          <p>狀態: {todo.completed ? "已完成" : "未完成"}</p>
          <button onClick={() => toggleTodo(todo.id)}>
            {todo.completed ? "取消完成" : "標記完成"}
          </button>
        </div>
      ))}

      <h3>總累積點數: {calculateTotalPoints()}</h3>
    </div>
  );
};
```

## 9.

### 問題分析

`GrandchildComponent` 和 `ChildComponent` 同樣顯示 `name` 與 `age`，邏輯重疊，顯得冗餘。如果要保留 `GrandchildComponent`，會有 props drilling 問題，因為 `name` 和 `age` 必須一層層傳下去，維護性比較差。

### 解決方案：使用 Context

```javascript
import React, { createContext, useContext, useState } from "react";

const UserContext = createContext();

function ParentComponent() {
  const [name, setName] = useState("Naro");
  const [age, setAge] = useState(12);

  return (
    <UserContext.Provider value={{ name, age }}>
      <ChildComponent />
    </UserContext.Provider>
  );
}

function ChildComponent() {
  const { name, age } = useContext(UserContext);

  return (
    <div>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
      <GrandchildComponent />
    </div>
  );
}

function GrandchildComponent() {
  const { name, age } = useContext(UserContext);

  return (
    <div>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
    </div>
  );
}
```

## 10.

```javascript
import { useRef } from "react";

function SearchButton({ onClick }) {
  return <button onClick={onClick}>Search</button>;
}

function SearchInput({ inputRef }) {
  return <input ref={inputRef} />;
}

export default function Page() {
  // 1. 使用 useRef 來取得 input 元素的參考
  const inputRef = useRef(null);

  const handleClick = () => {
    // 2. 點擊按鈕時讓 input 獲得焦點
    inputRef.current?.focus();
  };

  return (
    <>
      <nav>
        <SearchButton onClick={handleClick} />
      </nav>
      <SearchInput inputRef={inputRef} />
    </>
  );
}
```

---
