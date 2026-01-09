---
title: 'Tutorial: Tic-Tac-Toe'
---

<Intro>

Энэ хичээлийн явцад чи жижиг tic-tac-toe тоглоом хийж бүтээнэ. Уг хичээл нь React-ийн өмнөх мэдлэг шаарддаггүй. Хичээлээр сурах аргууд нь ямар ч React аппликейшн хийхэд суурь болдог бөгөөд үүнийг бүрэн ойлгосноор чи React-ийг гүнзгий ойлгох болно.

</Intro>

<Note>

Энэ хичээл нь хийж үзэнгээ сурахыг илүүд үздэг, мөн богино хугацаанд бодитоор ямар нэг зүйл бүтээж үзэхийг хүсдэг хүмүүст зориулагдсан. Харин ойлголт бүрийг алхам алхмаар сурмаар байвал [Describing the UI.](/learn/describing-the-ui)

</Note>

Хичээл нь хэд хэдэн хэсэгт хуваагдсан:

- [Хичээлийн орчныг бэлтгэх](#setup-for-the-tutorial) Энэ нь хичээлийг дагаж хийхэд зориулсан эхлэл суурийг чамд өгнө.
- [Тойм](#overview) Чи React-ийн үндсэн ойлголтуудыг сурна: компонентийг, props болон state.
- [Тоглоомыг дуусгах](#completing-the-game) чи React хөгжүүлэлтэд ашиглагддаг хамгийн түгээмэл аргуудыг сурна.
- [Цагийг буцаан хянах боломж нэмэх](#adding-time-travel) React-ийн өвөрмөц давуу талуудыг илүү гүнзгий ойлгоход тусална.

### Чи юу бүтээж байгаа вэ? {/*what-are-you-building*/}

Энэ хичээлээр чи **React ашиглан интерактив Tic-Tac-Toe тоглоом** бүтээнэ.

Төгсгөлд нь энэ нь хэрхэн харагдахыг эндээс үзэж болно:

<Sandpack>

```js App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

Хэрвээ кодыг одоогоор ойлгож чадахгүй байвал эсвэл кодын синтакст танил бус байвал санаа зовох хэрэггүй! Энэ хичээлийн зорилго нь React болон түүний синтаксыг ойлгоход туслах юм.

Хичээлийг үргэлжлүүлэхээсээ өмнө дээрх tic-tac-toe тоглоомыг үзэхийг зөвлөж байна. Тоглоомд анзаарах нэг онцлог нь тоглоомын самбарын баруун талд дугаарлагдсан жагсаалт байх явдал юм. Энэ жагсаалт нь тоглоомд болсон бүх хөдөлгөөний түүхийг харуулж, тоглоом урагшилж байх тусам шинэчлэгддэг.

Төгсгөлд нь тоглоомыг туршиж үзсэний дараа хуудас доош гүйлгэж үргэлжлүүлээрэй. Энэ хичээлд чи энгийн загвараас эхлэн тоглоом бүтээх болно. Дараагийн алхам нь тоглоомыг бүтээж эхлэхэд зориулсан орчныг бэлтгэх юм.

## Хичээлийн орчныг бэлтгэх {/setup-for-the-tutorial/}

Доорх жив код editor дээр баруун дээд буланд байгаа Fork товчийг дарж CodeSandbox вэбсайтаар шинэ таб дээр editor -ийг нээ. CodeSandbox нь браузерээсээ код бичиж, хэрэглэгчид хэрхэн харагдахыг урьдчилан харах боломжийг олгодог. Шинэ таб дээр хоосон дөрвөлжин болон энэ хичээлийн эхний код харагдах болно.
<Sandpack>

```js App.js
export default function Square() {
  return <button className="square">X</button>;
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

<Note>

Мөн чи энэ хичээлийг өгөгдсөн компьютертээ хөгжүүлэлтийн орчинд дагаж хийж болно. Үүний тулд:

1. Суулгах багаж [Node.js](https://nodejs.org/en/)
2. Өмнө нээсэн CodeSandbox таб дээр баруун дээд буланд байгаа товчийг дарж цэсийг нээж, File > Export to ZIP-г сонгон файлуудыг өөрийн компьютерт татаж авна
3. Архивыг задлаад терминал нээж, задласан хавтас руу cd командаар орно
4. Хамааралтай багцуудыг суулгахын тулд 'npm install' гүйцэтгэнэ.[Node modules]
5. 'npm start' командыг ажиллуулж орчныг локал сервер дээр эхлүүлж, кодыг браузерээс харах зааврыг дагана

Хэрвээ асуудал гарвал санаа зовох хэрэггүй! Эхлээд онлайн хувилбараар дагаж, дараа нь локал орчинд дахин оролдож болно.

</Note>

## Тойм {/*overview*/}

Одоо орчныг бэлдсэн тул, React-ийн тоймыг үзье!

## Эхний кодыг шалгах {/inspecting-the-starter-code/}

CodeSandbox дээр чи гурван үндсэн хэсгийг харах болно:

![CodeSandbox дахь эхний код](../images/tutorial/react-starter-code-codesandbox.png)

1.App.js, index.js, styles.css зэрэг файлууд болон public нэртэй хавтас бүхий Файлууд хэсэг
2.Сонгосон файлын эх кодыг харах код редактор хэсэг.[Code editor] 
3.Бичсэн код хэрхэн харагдахыг үзүүлэх браузер хэсэг[Browser]

Файлууд хэсэгт App.js файл сонгогдсон байх ёстой. Код редактор дээр тус файлын агуулга дараах байдалтай байна:

```jsx
export default function Square() {
  return <button className="square">X</button>;
}
```

Браузер хэсэгт дараах байдлаар X тэмдэгттэй дөрвөлжин харагдаж байх ёстой:

![X-ээр дүүрсэн дөрвөлжин](../images/tutorial/x-filled-square.png)

Одоо эхний кодын файлуудыг харцгаая.

#### `App.js` {/*appjs*/}

App.js дахь код нь компонент үүсгэдэг. React-д компонент гэдэг нь дагалдах кодын хэсэг бөгөөд хэрэглэгчийн интерфэйсийн нэг хэсгийг төлөөлдөг. Компонентуудыг аппликейшнд UI элементийг дүрслэх, удирдах, шинэчлэх зорилгоор ашигладаг.

```js {1}
export default function Square() {
  return <button className="square">X</button>;
}
```

Эхний мөрөнд Square нэртэй функц тодорхойлсон байна. export JavaScript түлхүүр үг нь энэ функцийг энэ файлын гаднаас ашиглах боломжтой болгодог. default түлхүүр үг нь бусад файлуудад энэ файлын гол функц нь энэ гэдгийг зааж өгдөг.

```js {2}
export default function Square() {
  return <button className="square">X</button>;
}
```

Хоёр дахь мөр нь button буцааж өгдөг. return JavaScript түлхүүр үг нь функц руу дуудагдсаны дараа буцаах утгыг заадаг. <button> нь JSX элемент юм. JSX элемент гэдэг нь JavaScript код болон HTML тагийг хослуулж, юу харуулахыг тодорхойлдог. className="square" нь button-ийн шинж чанар буюу prop бөгөөд CSS-д хэрхэн загварлахыг хэлдэг.X нь товч дотор харуулж буй текст, харин </button> нь JSX элементийг хааж, дараагийн агуулгыг товч дотор оруулахгүй гэдгийг заана.

#### `styles.css` {/*stylescss*/}

CodeSandbox-ийн Файлууд хэсгээс styles.css файлыг дар. Энэ файл нь React аппликейшнийхээ загварыг тодорхойлдог.Эхний хоёр CSS сонгогч (* болон body) нь аппликейшний томоохон хэсгүүдийн загварыг тодорхойлдог бол .square сонгогч нь className нь square гэж тогтоосон компонент бүрийн загварыг тодорхойлдог. Чиний кодонд энэ нь App.js дахь Square компонентын товчийг зааж байна.
#### `index.js` {/*indexjs*/}

CodeSandbox-ийн Файлууд хэсгээс index.js файлыг дар. Энэ файлыг хичээлийн явцад засварлах шаардлагагүй, гэхдээ энэ файл нь App.js дахь чиний бүтээсэн компонентыг веб браузерт холбох гүүр юм.

```jsx
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';
```

1–5-р мөрүүд нь бүх шаардлагатай хэсгүүдийг нэгтгэдэг:

*React
*React-ийг веб браузерт холбоход ашигладаг сан (React DOM)
*Компонентуудын загварууд
*App.js файлд чи бүтээсэн компонент

Файлын үлдсэн хэсэг нь бүх хэсгүүдийг нэгтгэж, эцсийн бүтээгдэхүүнийг public хавтсанд байгаа index.html руу шиддэг.

### Самбарыг бүтээх {/*building-the-board*/}

Одоо App.js руу буцъя. Чи хичээлийн үлдсэн хугацааг ихэнхдээ энд өнгөрүүлэх болно.

Одоогоор самбар нь зөвхөн нэг дөрвөлжин байна, гэхдээ чи есөн дөрвөлжин хэрэгтэй! Хэрвээ зөвхөн нэгийг нь хуулж наах замаар хоёр дөрвөлжин хийх гэж оролдвол:

```js {2}
export default function Square() {
  return <button className="square">X</button><button className="square">X</button>;
}
```

Чи дараах алдааг авна:

<ConsoleBlock level="error">

/src/App.js: Adjacent JSX elements must be wrapped in an enclosing tag. Did you want a JSX fragment `<>...</>`?

</ConsoleBlock>

React компонент нь нэг JSX элемент буцаах ёстой бөгөөд хоёр товч зэрэг зэрэг байрлах олон JSX элементийг шууд буцааж болохгүй. Үүнийг засахын тулд фрагмент (<> ба </>) ашиглан олон ойр JSX элементийг дараах байдлаар боож өгч болно:

```js {3-6}
export default function Square() {
  return (
    <>
      <button className="square">X</button>
      <button className="square">X</button>
    </>
  );
}
```

Одоо чи дараах зүйлийг харах ёстой:

![Хоёр X-ээр дүүрсэн дөрвөлжин](../images/tutorial/two-x-filled-squares.png)

Сайхан байна! Одоо чи хуулж хэд хэдэн удаа наах замаар есөн дөрвөлжин нэмэхэд л болно, мөн...

![Есөн X-ээр дүүрсэн дөрвөлжин](../images/tutorial/nine-x-filled-squares.png)

Өө, үгүй! Дөрвөлжин бүгд нэг мөрөнд гарч байна, самбарын сүлжээ шиг биш байна. Үүнийг засахын тулд дөрвөлжин бүрийг div дотор мөр болгон бүлэглэж, зарим CSS класс нэмэх хэрэгтэй. Мөн энэ үеэр дөрвөлжин бүрт дугаар өгч, хаана харуулж байгааг нь тодорхой болгоно.

App.js файлын Square компонент-ыг дараах байдлаар шинэчилнэ үү:

```js {3-19}
export default function Square() {
  return (
    <>
      <div className="board-row">
        <button className="square">1</button>
        <button className="square">2</button>
        <button className="square">3</button>
      </div>
      <div className="board-row">
        <button className="square">4</button>
        <button className="square">5</button>
        <button className="square">6</button>
      </div>
      <div className="board-row">
        <button className="square">7</button>
        <button className="square">8</button>
        <button className="square">9</button>
      </div>
    </>
  );
}
```

styles.css файлд тодорхойлсон CSS нь board-row className-тэй div-үүдийг загварладаг. Одоо компонентуудыг стильтэй div-д бүлэглэснээр, чиний Tic-Tac-Toe самбар бэлэн болсон:

![1–9 хүртэл дугаарласан Tic-Tac-Toe самбар](../images/tutorial/number-filled-board.png)

Гэхдээ одоо нэг асуудал гарч байна. Square нэртэй компонент чинь оролцоогоор квадрат биш болсон байна. Үүнийг засахын тулд нэрийг Board гэж өөрчилнө:

```js {1}
export default function Board() {
  //...
}
```

Энэ үеэр чиний код дараах байдлаар харагдах ёстой:

<Sandpack>

```js
export default function Board() {
  return (
    <>
      <div className="board-row">
        <button className="square">1</button>
        <button className="square">2</button>
        <button className="square">3</button>
      </div>
      <div className="board-row">
        <button className="square">4</button>
        <button className="square">5</button>
        <button className="square">6</button>
      </div>
      <div className="board-row">
        <button className="square">7</button>
        <button className="square">8</button>
        <button className="square">9</button>
      </div>
    </>
  );
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

<Note>

Пссст… Энэ их код бичих шаардлагатай шүү! Санаа зоволтгүй, энэ хуудаснаас хуулж нааж болно. Гэхдээ бага зэрэг сорилт хүсэж байвал, заавал өөрөө нэг удаа гараар бичсэн кодыг л хуулж авахыг зөвлөж байна.

</Note>

### Props ашиглан өгөгдөл дамжуулах {/passing-data-through-props/}

Дараагийн алхамд, хэрэглэгч дөрвөлжин дээр дарсан үед дөрвөлжний утгыг хоосноос "X" болгож өөрчлөх хэрэгтэй. Одоогоор самбарыг ингэж бүтээсэн бол дөрвөлжин бүрийг шинэчлэх кодыг 9 удаа хуулж наах шаардлагатай болно (есөн дөрвөлжин тутамд нэг удаа)! Харин хуулж наахын оронд, React-ийн компонентын архитектур нь дагаж ашиглах боломжтой компонент үүсгэж, дахин давтагдах, замбараагүй кодоос зайлсхийх боломжийг олгодог.

Юуны өмнө, Board компонент дахь анхны дөрвөлжинг тодорхойлсон мөрийг (<button className="square">1</button>) шинэ Square компонент руу хуулж орно:

```js {1-3}
function Square() {
  return <button className="square">1</button>;
}

export default function Board() {
  // ...
}
```

Дараа нь Board компонентыг JSX синтакс ашиглан шинэ Square компонентыг дүрслэх байдлаар шинэчилнэ:

```js {5-19}
// ...
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
    </>
  );
}
```

Анхаар: браузерийн div-үүдээс ялгаатай нь, чиний өөрийн компонентууд болох Board болон Square нь том үсгээр эхлэх ёстой.

Одоо харцгаая:

![Нэг X-ээр дүүрсэн самбар](../images/tutorial/board-filled-with-ones.png)

Өө, үгүй! Өмнөх дугаарлагдсан дөрвөлжингуудыг алджээ. Одоо дөрвөлжин бүр "1" гэж гарч байна. Үүнийг засахын тулд props ашиглаж, эх компонент (Board)–аас хүүхэд компонент (Square) руу дөрвөлжин бүрийн утгыг дамжуулна.
Square компонентыг Board-аас дамжуулж буй value prop-ыг уншдаг байдлаар шинэчилнэ:

```js {1}
function Square({ value }) {
  return <button className="square">1</button>;
}
```

`function Square({ value })` Энэ нь Square компонент руу value нэртэй prop дамжуулж болно гэдгийг зааж өгч байна.

Одоо дөрвөлжин бүрийн дотор "1" биш, дамжуулсан value-г харуулахыг хүсэж байна. Ингэж хийж болно:

```js {2}
function Square({ value }) {
  return <button className="square">value</button>;
}
```

Өө, энэ бол хүссэн үр дүн биш байна:

![Дамжуулсан value-гаар дүүрсэн самбар](../images/tutorial/board-filled-with-value.png)

Чи компонентынхаа value нэртэй JavaScript хувьсагчийг дүрслэхийг хүсэж байна, "value" гэсэн текстийг биш. JSX-д JavaScript руу шилжихийн тулд тэгш бөгж ({}) хэрэгтэй. JSX-д value-г ингэж оруулна:

```js {2}
function Square({ value }) {
  return <button className="square">{value}</button>;
}
```

Одоогоор чи хоосон самбар харах ёстой:

![Хоосон самбар](../images/tutorial/empty-board.png)

Энэ нь Board компонент өөрийн дүрсэлж буй Square бүрт value prop-ыг дамжуулээгүйтэй холбоотой. Үүнийг засахын тулд Board компонентын dүрслэж буй Square бүрт value prop нэмнэ:

```js {5-7,10-12,15-17}
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square value="1" />
        <Square value="2" />
        <Square value="3" />
      </div>
      <div className="board-row">
        <Square value="4" />
        <Square value="5" />
        <Square value="6" />
      </div>
      <div className="board-row">
        <Square value="7" />
        <Square value="8" />
        <Square value="9" />
      </div>
    </>
  );
}
```

Одоо чи дахин дугаарласан сүлжээтэй самбар харах ёстой:

![1–9 хүртэл дугаарласан Tic-Tac-Toe самбар](../images/tutorial/number-filled-board.png)

Чиний шинэчилсэн код дараах байдлаар харагдах ёстой:

<Sandpack>

```js App.js
function Square({ value }) {
  return <button className="square">{value}</button>;
}

export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square value="1" />
        <Square value="2" />
        <Square value="3" />
      </div>
      <div className="board-row">
        <Square value="4" />
        <Square value="5" />
        <Square value="6" />
      </div>
      <div className="board-row">
        <Square value="7" />
        <Square value="8" />
        <Square value="9" />
      </div>
    </>
  );
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

### Интерактив компонент үүсгэх {/making-an-interactive-component/}

Square компонент дээр дарвал X гаргахыг хийцгээе. Square дотор handleClick нэртэй функц зарлаж, дараа нь Square-ээс буцааж буй button JSX элементэд onClick шинж чанарыг нэмнэ:

```js {2-4,9}
function Square({ value }) {
  function handleClick() {
    console.log('clicked!');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```

Одоо дөрвөлжин дээр дарвал CodeSandbox-ийн Browser хэсгийн доор байгаа Console таб-д "clicked!" гэж бичигдэж харагдах ёстой. Дөрвөлжин дээр хэд дахин дарсан ч "clicked!" дахин бичигдэх ба шинэ мөр нэмэгдэхгүй. Харин анхны "clicked!" бичлэгийн хажууд тоо нэмэгдэх байдлаар харагдана.

<Note>

Хэрвээ чи хичээлийг локал хөгжүүлэлтийн орчинд дагаж байгаа бол браузерийн Console-ийг нээх хэрэгтэй. Жишээ нь, Chrome браузер ашиглаж байвал Console-ийг дараах товчлолоор нээж болно:
Windows/Linux: Shift + Ctrl + J
macOS: Option + ⌘ + J

</Note>

Дараагийн алхамд, Square компонент дарсан гэдгээ "санаж", "X" тэмдэгтэй болгохыг хүсэж байна. Юу санах гэж байгааг компонентууд state ашиглаж хийдэг.

React нь useState нэртэй тусгай функц өгдөг бөгөөд үүнийг компонент дотроос дуудахдаа юмс "санах" боломжтой болдог. Одоо Square-ийн одоо байгаа утгыг state-д хадгалж, дөрвөлжин дээр дарвал утгыг өөрчилөх байдлаар ашиглана.

Файлын эхэнд useState-г импорт хийнэ. Square компонентын value prop-ыг устгаад**, оронд нь Square-ийн эхэнд **useState-г дуудах шинэ мөр нэмнэ**. Энэ нь **value` нэртэй state хувьсагчийг буцаах болно:

```js {1,3,4}
import { useState } from 'react';

function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    //...
```

'value' нь утгыг хадгалдаг бол 'setValue' нь тухайн утгыг өөрчлөхөд ашиглах функц юм.useState руу дамжуулсан 'null' нь state хувьсагчийн эхний утгыг заадаг тул энд 'value' эхэндээ 'null' утгатай байна.

Одоо 'Square' компонент props авчрахгүй болсон тул, Board компонентын дүрсэлсэн есөн Square бүрээс 'value' prop-ыг устгана:

```js {6-8,11-13,16-18}
// ...
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
    </>
  );
}
```

Одоо 'Square' дээр дарвал "X" гарч ирэхээр өөрчилнө. 'console.log("clicked!");' -г орлуулж 'setValue('X');' -г ашиглана. Ингэснээр Square компонент дараах байдлаар харагдана:

```js {5}
function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    setValue('X');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```

'onClick' обработчикт энэ 'set' функц-ийг дуудаж байгаа нь React-д тэр 'Square'-ийг дахин render хийхийг зааж байгаа хэрэг юм. Шинэчлэгдсэний дараа 'Square'-ийн value 'X' болно, тэгээд тоглоомын самбар дээр "X" харагдана. Дөрвөлжин дээр дар, "X" гарч ирнэ:

![Самбарт X тэмдэгүүдийг нэмэх](../images/tutorial/tictac-adding-x-s.gif)

Дөрвөлжин бүр өөрийн state-тэй: тус бүрийн 'value' нь бусад дөрвөлжингоос бүрэн хараат бус. Компонентын 'set' функцийг дуудахад React нь автомат хүүхэд компонентуудыг ч шинэчилж өгдөг.

Дээрх өөрчлөлтүүдийг хийсний дараа чиний код дараах байдлаар харагдана:

<Sandpack>

```js App.js
import { useState } from 'react';

function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    setValue('X');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}

export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
    </>
  );
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

### React Developer Tools {/react-developer-tools/}

React DevTools нь чамд React компонентуудын props болон state-ийг шалгах боломжийг олгодог. CodeSandbox-д браузер хэсгийн доор React DevTools таб-ыг олох боломжтой:

![CodeSandbox дахь React DevTools](../images/tutorial/codesandbox-devtools.png)

Дэлгэц дээрх тодорхой компонентыг шалгахын тулд React DevTools-ийн баруун дээд буланд байгаа товчийг ашиглана:

![React DevTools ашиглан хуудас дээрх компонентуудыг сонгож шалгах](../images/tutorial/devtools-select.gif)

<Note>

React DevTools-ийг локал хөгжүүлэлтэд зориулан [Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en), [Firefox](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/), and [Edge](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil) хөтчийн нэмэлт өргөтгөл хэлбэрээр ашиглах боломжтой. Үүнийг суулгаснаар React ашигласан вэб сайтуудын "Browser Developer Tools" хэсэгт Components цэс нэмэгдэж харагдах болно.

</Note>

## Тоглоомыг дуусгах {/completing-the-game/}
Энэ хүртэл танд tic-tac-toe тоглоомыг бүтээх үндсэн бүх хэсгүүд бэлэн боллоо. Тоглоомыг бүрэн дуусгахын тулд одоо хөлөг дээр "X" болон "O"-г ээлжлэн байрлуулж, ялагчийг тодруулах арга хэрэгтэй.

## Төлөвийг дээшлүүлэх (Lifting state up) {/lifting-state-up/}
Одоогийн байдлаар 'Square' компонент бүр тоглоомын төлөвийн (state) тодорхой хэсгийг хадгалж байна. Tic-tac-toe тоглоомонд ялагчийг шалгахын тулд 'Board' компонент нь 9 өөр 'Square' компонентын төлөвийг ямар нэг байдлаар мэдэх шаардлагатай болно.

Та үүнийг хэрхэн шийдэх вэ? Эхлээд та 'Board' компонент нь 'Square' бүрээс өөрийнх нь төлөвийг "асуух" хэрэгтэй гэж бодож магадгүй. Хэдийгээр React дээр ийм байдлаар ажиллах боломжтой ч код нь ойлгоход хэцүү, алдаа гарах магадлал өндөр, мөн бүтцийг нь өөрчлөхөд (refactor) төвөгтэй болдог тул бид үүнийг зөвлөдөггүй. Үүний оронд хамгийн зөв арга бол тоглоомын төлөвийг 'Square' бүр дээр биш, харин эцэг компонент болох 'Board' дээр хадгалах юм. 'Board' компонент нь 'Square' бүрт юу харуулахыг нь "prop"-оор дамжуулж хэлж өгнө (яг л өмнө нь тоо дамжуулсантай адил).

**Олон хүүхэд компонентоос өгөгдөл цуглуулах эсвэл хоёр хүүхэд компонентыг хооронд нь харилцуулахын тулд дундын төлөвийг (state) тэдгээрийн эцэг компонент дээр зарлаж өгөөрэй. Эцэг компонент нь тухайн төлөвийг буцаагаад хүүхдүүд рүүгээ "props"-оор дамжуулж болно. Энэ нь хүүхэд компонентуудыг хоорондоо болон эцэг компоненттойгоо ижил мэдээлэлтэй (sync) байлгахад тусалдаг.

React компонентын бүтцийг сайжруулах (refactor) үед төлөвийг эцэг компонент руу дээшлүүлэх нь түгээмэл байдаг.

Одоо үүнийг туршиж үзье. Board компонентыг засаж, 9 нүдэнд харгалзах 9 ширхэг "null" утга бүхий squares нэртэй state хувьсагчийг зарлаарай:

```js {3}
// ...
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    // ...
  );
}
```

'Array(9).fill(null)' нь есөн элементтэй массив үүсгэж, элемент бүрийг 'null' утгаар дүүргэнэ. Үүнийг 'useState()'-ээр ороож өгснөөр тухайн массиваар анхны утга нь тохируулагдсан squares нэртэй state хувьсагч зарлагдаж байгаа юм. Массивын индекс бүр нь хөлөг дээрх нэг нүдний утгатай харгалзана. Дараа нь та хөлгийг дүүргэх үед squares массив иймэрхүү харагдах болно:

```jsx
['O', null, 'X', 'X', 'X', 'O', 'O', null, null]
```

Одоо таны 'Board' компонент өөрийн рендерлэж буй 'Square' бүрт 'value' нэртэй 'prop'-ыг дамжуулах шаардлагатай:

```js {6-8,11-13,16-18}
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} />
        <Square value={squares[1]} />
        <Square value={squares[2]} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} />
        <Square value={squares[4]} />
        <Square value={squares[5]} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} />
        <Square value={squares[7]} />
        <Square value={squares[8]} />
      </div>
    </>
  );
}
```

Дараа нь, та 'Board' компонентоос 'value' нэртэй 'prop'-ыг хүлээн авахын тулд 'Square' компонентыг засах болно. Үүний тулд 'Square' компонент өөрийн 'value' утгыг 'state'-ээр хянаж байсныг болон товчлуурын 'onClick' 'prop'-ыг устгах шаардлагатай:

```js {1,2}
function Square({value}) {
  return <button className="square">{value}</button>;
}
```

Энэ үед танд хоосон 'tic-tac-toe' хөлөг харагдах ёстой:

![Хоосон самбар](../images/tutorial/empty-board.png)

Тэгээд таны код иймэрхүү харагдах ёстой:

<Sandpack>

```js App.js
import { useState } from 'react';

function Square({ value }) {
  return <button className="square">{value}</button>;
}

export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} />
        <Square value={squares[1]} />
        <Square value={squares[2]} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} />
        <Square value={squares[4]} />
        <Square value={squares[5]} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} />
        <Square value={squares[7]} />
        <Square value={squares[8]} />
      </div>
    </>
  );
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

'Square' бүр одоо хоосон нүдэнд зориулсан 'null', эсвэл 'X', 'O' утгуудын аль нэгийг 'value' 'prop'-оор хүлээн авах болно.

Дараа нь, та 'Square' дээр дарахад юу болохыг өөрчлөх шаардлагатай. Одоо 'Board' компонент аль нүднүүд дүүрсэн байгааг хянаж байгаа. Тиймээс 'Square' нь 'Board'-ын 'state'-ийг шинэчлэх арга замыг үүсгэх хэрэгтэй. 'State' нь зөвхөн түүнийг тодорхойлсон компонентдоо хувийн (private) байдаг тул та 'Square' дотроос 'Board'-ын 'state'-ийг шууд шинэчлэх боломжгүй.

Үүний оронд, та 'Board' компонентоос 'Square' компонент руу нэг функц дамжуулж, нүдэн дээр дарах үед 'Square' тухайн функцийг дууддаг байхаар тохируулна. Та 'Square' компонент дээр дарахад дуудагдах функцээс эхлэх бөгөөд тэрхүү функцээ 'onSquareClick' гэж нэрлэх болно:

```js {3}
function Square({ value }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

Дараа нь, та 'onSquareClick' функцийг 'Square' компонентын 'props' руу нэмэх болно:

```js {1}
function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

Одоо та 'onSquareClick' 'prop'-ыг 'Board' компонент доторх 'handleClick' гэж нэрлэх функцтэй холбох болно. 'onSquareClick'-ийг 'handleClick'-тэй холбохын тулд эхний 'Square' компонентын 'onSquareClick' 'prop'-т тухайн функцийг дамжуулж өгнө:

```js {7}
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={handleClick} />
        //...
  );
}
```

Эцэст нь, та хөлгийн 'state'-ийг хадгалж буй 'squares' массивыг шинэчлэхийн тулд 'Board' компонент дотор 'handleClick' функцийг тодорхойлно:

```js {4-8}
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick() {
    const nextSquares = squares.slice();
    nextSquares[0] = "X";
    setSquares(nextSquares);
  }

  return (
    // ...
  )
}
```

'handleClick' функц нь JavaScript-ийн 'slice()' массивын аргыг ашиглан 'squares' массивын хуулбарыг ('nextSquares') үүсгэнэ. Дараа нь 'handleClick' нь 'nextSquares' массивын эхний ('[0]' индекс) нүдэнд 'X' утгыг нэмж шинэчилнэ.

'setSquares' функцийг дуудсанаар React-д компонентын төлөв (state) өөрчлөгдсөнийг мэдэгдэнэ. Энэ нь 'squares' төлөвийг ашиглаж буй компонент ('Board') болон түүний хүүхэд компонентуудыг (хөлгийг бүрдүүлж буй 'Square' компонентууд) дахин рендерлэх (re-render) үйлдлийг эхлүүлнэ.

<Note>

JavaScript нь 'closures'-ийг дэмждэг бөгөөд [closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)энэ нь дотоод функц (жишээ нь: 'handleClick') өөрийн гадаад функцэд (жишээ нь: 'Board') тодорхойлогдсон хувьсагч болон функцүүд рүү хандах боломжтой гэсэн үг юм. 'handleClick' болон 'setSquares' хоёулаа 'Board' функц дотор тодорхойлогдсон учраас 'handleClick' функц нь 'squares' төлөвийг (state) уншиж, 'setSquares' аргыг дуудах боломжтой байдаг.

</Note>

Одоо та хөлөг дээр 'X' нэмэх боломжтой боллоо... гэхдээ зөвхөн зүүн дээд талын нүдэнд л нэмэгдэж байна. Учир нь таны 'handleClick' функц зүүн дээд талын нүдний индекс болох ('0') утгыг шинэчлэхээр хатуу кодлогдсон (hardcoded) байна. Одоо 'handleClick' функцийг дурын нүдийг шинэчлэх боломжтой болгож өөрчилье. 'handleClick' функцэд шинэчлэх нүдний индексийг хүлээн авах 'i' гэсэн аргумент нэмж өгөөрэй:

```js {4,6}
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    nextSquares[i] = "X";
    setSquares(nextSquares);
  }

  return (
    // ...
  )
}
```

Дараа нь, та тухайн 'i' аргументыг 'handleClick' рүү дамжуулах шаардлагатай болно. Та JSX дотор шууд 'onSquareClick={handleClick(0)}' гэж тохируулахыг оролдож болох ч, энэ нь ажиллахгүй:

```jsx
<Square value={squares[0]} onSquareClick={handleClick(0)} />
```

Энэ нь яагаад ажиллахгүй байгаагийн шалтгаан нь энэ юм. 'handleClick(0)' дуудлага нь 'board' компонентыг рендерлэх (rendering) процессын нэг хэсэг болно. 'handleClick(0)' нь 'setSquares' функцийг дуудсанаар 'board' компонентын төлөвийг (state) өөрчлөх тул таны 'board' компонент бүхэлдээ дахин рендерлэгдэх болно. Гэвч энэ үед 'handleClick(0)' дахин ажиллах бөгөөд энэ нь төгсгөлгүй давталтад (infinite loop) хүргэнэ:

<ConsoleBlock level="error">

Too many re-renders. React limits the number of renders to prevent an infinite loop.

</ConsoleBlock>

Яагаад энэ асуудал өмнө нь гараагүй вэ?

Та өмнө нь 'onSquareClick={handleClick}' гэж дамжуулахдаа 'handleClick' функцийг өөрийг нь 'prop' болгон дамжуулж байсан. Та түүнийг дуудаагүй байсан! Харин одоо та тухайн функцийг шууд дуудаж байна — 'handleClick(0)' доторх хаалтыг анзаараарай — тийм ч учраас энэ нь хэтэрхий эрт ажиллаад байгаа юм. Та хэрэглэгчийг дарах хүртэл 'handleClick'-ийг дуудахыг хүсэхгүй байгаа шүү дээ!

Та үүнийг 'handleClick(0)'-ийг дууддаг 'handleFirstSquareClick' гэх мэт функц, 'handleClick(1)'-ийг дууддаг 'handleSecondSquareClick' гэх мэт функцүүд үүсгэх замаар засч болох юм. Тэгээд эдгээр функцүүдээ 'onSquareClick={handleFirstSquareClick}' гэх мэтээр 'prop' болгон дамжуулна (дуудах биш). Энэ нь төгсгөлгүй давталтын асуудлыг шийдэх болно.

Гэвч есөн өөр функц тодорхойлж, тус бүрт нь нэр өгөх нь хэтэрхий нуршуу (verbose) хэрэг болно. Үүний оронд дараах байдлаар хийцгээе:

```js {6}
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        // ...
  );
}
```

Шинэ () => бичиглэлийг анзаараарай. Энд () => handleClick(0) нь arrow function (сумт функц) бөгөөд энэ нь функц тодорхойлох илүү товч арга юм. Нүдэн дээр дарах үед => "сумны" ард байгаа код ажиллаж, handleClick(0)-ийг дуудах болно.

Одоо та бусад найман нүдийг өөрийнх нь индекс бүхий 'arrow function'-оос handleClick-ийг дууддаг байхаар шинэчлэх хэрэгтэй. handleClick дуудлага бүрийн аргумент нь харгалзах нүдний индекстэй тохирч байгаа эсэхийг шалгаарай:

```js {6-8,11-13,16-18}
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
};
```

Одоо та хөлгийн аль ч нүдэн дээр дарж 'X' тэмдэглэгээг дахин нэмэх боломжтой боллоо:

![Гэхдээ одоогоор нүдэн дээр дарах бүрт зөвхөн 'X' л гарч ирж байгаа.](../images/tutorial/tictac-adding-x-s.gif)

Гэхдээ энэ удаад төлөвийн бүх удирдлагыг (state management) 'Board' компонент хариуцаж байгаа юм!

Таны код иймэрхүү харагдах ёстой:

<Sandpack>

```js App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    nextSquares[i] = 'X';
    setSquares(nextSquares);
  }

  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

Одоо таны төлөвийн удирдлага 'Board' компонент дотор байгаа тул эцэг 'Board' компонент нь хүүхэд 'Square' компонентууд руу 'props'-ыг дамжуулж, тэдгээрийг зөв харуулах боломжийг олгож байна. 'Square' дээр дарах үед хүүхэд 'Square' компонент нь эцэг 'Board' компонентоосоо хөлгийн төлөвийг (state) шинэчлэхийг хүсдэг. 'Board'-ын төлөв өөрчлөгдөхөд 'Board' компонент болон түүний бүх хүүхэд 'Square' компонентууд автоматаар дахин рендерлэгддэг. Бүх нүдний төлөвийг 'Board' компонент дотор хадгалах нь ирээдүйд ялагчийг тодорхойлох боломжийг олгоно.

Хэрэглэгч 'X' тэмдэг нэмэхийн тулд хөлгийн зүүн дээд талын нүдэн дээр дарах үед юу болдгийг товчхон дүгнэе:

1.Зүүн дээд талын нүдэн дээр дарахад тухайн button-ы Square-ээс onClick проп-оор хүлээн авсан функц ажиллана. Square компонент нь уг функцийг Board-оос onSquareClick проп-оор дамжуулж авсан байдаг. Board компонент уг функцийг JSX дотор шууд тодорхойлсон бөгөөд энэ нь handleClick функцийг 0 гэсэн аргументтайгаар дууддаг.
2.handleClick функц нь хүлээн авсан аргументыг (0) ашиглан squares массивын эхний элементийг null-ээс X болгон шинэчилнэ.
3.Board компонентын squares төлөв (state) шинэчлэгдсэн тул Board болон түүний бүх хүүхэд компонентууд дахин рендерлэгдэнэ. Үүний үр дүнд 0 индекс бүхий Square компонентын value проп null-ээс X болж өөрчлөгдөнө.

Эцэст нь хэрэглэгч зүүн дээд талын нүдэн дээр дарсны дараа тухайн нүд хоосон байснаа 'X' тэмдэгтэй болж өөрчлөгдөхийг харах болно

<Note>

DOM '<button>' элементийн 'onClick' аттрибут нь React-ийн хувьд онцгой утгатай байдаг, учир нь энэ нь суулгагдсан компонент юм. 'Square' шиг өөрөө үүсгэсэн компонентуудын хувьд нэршил нь таны дур мэдлийн хэрэг. Та 'Square'-ийн 'onSquareClick' проп эсвэл 'Board'-ын 'handleClick' функцэд ямар ч нэр өгч болох бөгөөд код яг ижилхэн ажиллах болно. React-д үйл явдлыг илэрхийлж буй проп-д 'onSomething' нэр ашиглах, харин тэдгээр үйл явдлыг боловсруулж буй функцийн тодорхойлолтод 'handleSomething' нэр ашиглах нь тогтсон хэвшил юм.

</Note>

### Яагаад хувиршгүй байдал чухал вэ {/*why-immutability-is-important*/}

'handleClick' дотор та 'squares' массивыг шууд өөрчлөхийн оронд '.slice()' ашиглан хуулбар үүсгэж байгааг анзаараарай. Үүний шалтгааныг тайлбарлахын тулд бид 'immutability' буюу хувиршгүй байдал болон яагаад үүнийг сурах нь чухал болох талаар ярилцах хэрэгтэй.

Өгөгдлийг өөрчлөхөд ерөнхийдөө хоёр арга байдаг. Эхний арга нь өгөгдлийн утгыг шууд өөрчлөх замаар өгөгдлийг 'mutate' хийх юм. Хоёр дахь арга нь өгөгдлийг хүссэн өөрчлөлтүүдийг агуулсан шинэ хуулбараар солих юм. Хэрэв та 'squares' массивыг 'mutate' хийсэн бол иймэрхүү харагдах байсан:

```jsx
const squares = [null, null, null, null, null, null, null, null, null];
squares[0] = 'X';
// Now `squares` is ["X", null, null, null, null, null, null, null, null];
```

Тэгээд 'squares' массивыг 'mutate' хийхгүйгээр өгөгдлийг өөрчилбөл иймэрхүү харагдах байсан:

```jsx
const squares = [null, null, null, null, null, null, null, null, null];
const nextSquares = ['X', null, null, null, null, null, null, null, null];
// Now `squares` is unchanged, but `nextSquares` first element is 'X' rather than `null`
```

Үр дүн нь ижилхэн боловч 'mutating' хийхгүй байснаар (суурь өгөгдлийг шууд өөрчлөхгүй байх) та хэд хэдэн давуу талыг олж авна.

'Immutability' нь цогц үйлдлүүдийг хэрэгжүүлэхэд илүү хялбар болгодог. Энэхүү хичээлийн дараагийн хэсэгт та тоглоомын түүхийг эргэн харах болон өмнөх нүүдлүүд рүү 'jump back' хийх боломжтой 'time travel' функцийг хэрэгжүүлэх болно. Энэ функц нь зөвхөн тоглоомд зориулагдаагүй бөгөөд тодорхой үйлдлүүдийг буцаах (undo) болон дахин хийх (redo) чадвар нь аппликейшнуудад түгээмэл байдаг шаардлага юм. Өгөгдлийг шууд 'mutation' хийхээс зайлсхийснээр та өгөгдлийн өмнөх хувилбаруудыг бүрэн хэвээр үлдээж, дараа нь тэдгээрийг дахин ашиглах боломжтой болно.

Мөн 'immutability'-ийн өөр нэг давуу тал бий. Ерөнхийдөө, эцэг компонентын төлөв (state) өөрчлөгдөхөд бүх хүүхэд компонентууд автоматаар дахин рендерлэгддэг. Үүнд тухайн өөрчлөлтөд өртөөгүй хүүхэд компонентууд ч багтана. Хэдийгээр дахин рендерлэх үйл явц нь хэрэглэгчид өөрт нь шууд мэдэгдэхгүй ч (та үүнээс идэвхтэйгээр зайлсхийх гэж оролдох хэрэггүй!), гүйцэтгэлийг сайжруулах зорилгоор өөрчлөлтөд өртөөгүй нь тодорхой байгаа модны зарим хэсгийг дахин рендерлэхгүй алгасахыг хүсэж болох юм. 'Immutability' нь компонентуудад өөрсдийнх нь өгөгдөл өөрчлөгдсөн эсэхийг харьцуулж шалгахад маш хялбар (зардал багатай) болгож өгдөг. 'React' компонентыг хэзээ дахин рендерлэхээ хэрхэн сонгодог талаар та илүү ихийг эндээс сурч болно:
[the `memo` API reference](/reference/react/memo).

### Taking turns {/*taking-turns*/}

Одоо энэ 'tic-tac-toe' тоглоомын нэг томоохон дутагдлыг засах цаг боллоо: хөлөг дээр 'O' тэмдэглэгээ хийх боломжгүй байна.

Та эхний нүүдлийг анхны утгаар нь 'X' байхаар тохируулна. Үүнийг хянахын тулд 'Board' компонентэд өөр нэг 'state' нэмж оруулцгаая:

```js {2}
function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  // ...
}
```

Тоглогч нүүдэл хийх бүрд, дараагийн ээлжинд аль тоглогч нүүхийг шийдэхийн тулд 'xIsNext' (boolean утга) солигдож, тоглоомын төлөв хадгалагдах болно. Та 'Board' компонентын 'handleClick' функцийг шинэчилж 'xIsNext' утгыг эсрэгээр нь солих (flip) үйлдлийг оруулна:

```js {7,8,9,10,11,13}
export default function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = "X";
    } else {
      nextSquares[i] = "O";
    }
    setSquares(nextSquares);
    setXIsNext(!xIsNext);
  }

  return (
    //...
  );
}
```

Одоо та өөр өөр нүднүүд дээр дарахад тэдгээр нь байх ёстой ёсоороо 'X' болон 'O' болж ээлжлэн солигдох болно!

Гэхдээ түр хүлээнээрэй, нэг асуудал байна. Нэг нүдэн дээрээ олон удаа дарж үзээрэй:

![O overwriting an X](../images/tutorial/o-replaces-x.gif)

'X' нь 'O'-оор дарагдаж бичигдээд байна! Хэдийгээр энэ нь тоглоомыг сонирхолтой болгож болох ч, одоохондоо бид үндсэн дүрмийг баримтлах болно.

Та нүдийг 'X' эсвэл 'O'-оор тэмдэглэхдээ тухайн нүдэнд өмнө нь 'X' эсвэл 'O' утга байгаа эсэхийг түрүүлж шалгахгүй байна. Үүнийг 'returning early' буюу эрт буцаах замаар шийдэж болно. Та нүд хэдийн бөглөгдсөн эсэхийг шалгана. Хэрэв нүд хэдийн дүүрсэн байвал, 'handleClick' функц хөлөгийн төлөвийг (state) шинэчлэхээс нь өмнө 'return' хийж эрт дуусгах болно.

```js {2,3,4}
function handleClick(i) {
  if (squares[i]) {
    return;
  }
  const nextSquares = squares.slice();
  //...
}
```

Одоо та зөвхөн хоосон нүднүүдэд 'X' эсвэл 'O' нэмэх боломжтой боллоо! Энэ үе шатанд таны код иймэрхүү харагдах ёстой:

<Sandpack>

```js App.js
import { useState } from 'react';

function Square({value, onSquareClick}) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

export default function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    if (squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    setSquares(nextSquares);
    setXIsNext(!xIsNext);
  }

  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

### Ялагчийг зарлах {/*declaring-a-winner*/}

Тоглогчид ээлжлэн нүүх боломжтой болсон тул одоо тоглоом дуусаж ялагч тодорсон эсвэл дахин нүүх боломжгүй болсон үеийг харуулах шаардлагатай. Үүний тулд 9 нүд бүхий массивыг хүлээн авч, ялагчийг шалгаад, тохирох 'X', 'O' эсвэл 'null' утгыг буцаадаг 'calculateWinner' гэх туслах функцийг нэмнэ. 'calculateWinner' функцийн талаар хэт их санаа зовох хэрэггүй; энэ нь зөвхөн React-д зориулагдсан зүйл биш юм:

```js App.js
export default function Board() {
  //...
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6]
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

<Note>

'calculateWinner' функцийг 'Board' компонентын өмнө эсвэл ард тодорхойлох нь ялгаагүй. Компонентуудаа засах болгондоо дээш доош гүйлгэх шаардлагагүй байлгах үүднээс үүнийг кодын хамгийн төгсгөлд тавьцгаая.

</Note>

Та тоглогч ялсан эсэхийг шалгахын тулд 'Board' компонентын 'handleClick' функц дотор 'calculateWinner(squares)' функцийг дуудах болно. Хэрэглэгч хэдийн 'X' эсвэл 'O' утгатай нүдэн дээр дарсан эсэхийг шалгахтайгаа зэрэгцүүлэн энэхүү шалгалтыг хийж болно. Аль ч тохиолдолд бид 'return early' буюу эрт буцаахыг хүсэж байна:

```js {2}
function handleClick(i) {
  if (squares[i] || calculateWinner(squares)) {
    return;
  }
  const nextSquares = squares.slice();
  //...
}
```

Тоглогчдод тоглоом дууссаныг мэдэгдэхийн тулд "Ялагч: X" эсвэл "Ялагч: O" гэх мэт текстийг харуулж болно. Үүний тулд та 'Board' компонентэд 'status' хэсгийг нэмнэ. Хэрэв тоглоом дууссан бол уг статус нь ялагчийг харуулах бөгөөд хэрэв тоглоом үргэлжилж байвал дараагийн ээлжинд аль тоглогч нүүхийг харуулах болно:

```js {3-9,13}
export default function Board() {
  // ...
  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = "Winner: " + winner;
  } else {
    status = "Next player: " + (xIsNext ? "X" : "O");
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        // ...
  )
}
```

Баяр хүргэе! Та одоо ажилладаг 'tic-tac-toe' тоглоомтой боллоо. Мөн та 'React'-ийн үндсэн ойлголтуудыг ч бас сурч авлаа. Тиймээс жинхэнэ ялагч нь та юм. Энэ үеийн код иймэрхүү харагдах ёстой:

<Sandpack>

```js App.js
import { useState } from 'react';

function Square({value, onSquareClick}) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

export default function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    setSquares(nextSquares);
    setXIsNext(!xIsNext);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

## Цаг хугацаагаар аялах функцийг нэмэх {/*adding-time-travel*/}

Сүүлийн дасгал болгон тоглоомын өмнөх нүүдлүүд рүү "цаг хугацаагаар буцах" боломжийг бүрдүүлцгээе.

### Нүүдлүүдийн түүхийг хадгалах {/*storing-a-history-of-moves*/}

Хэрэв та squares массивыг өөрчилсөн (mutate) бол цаг хугацаагаар аялах (time travel) функцийг хэрэгжүүлэхэд маш хэцүү байх байсан.

Гэвч та нүүдэл бүрийн дараа slice() ашиглан squares массивын шинэ хуулбарыг үүсгэж, түүнийг өөрчлөгдөшгүй (immutable) мэтээр ашигласан. Энэ нь танд squares массивын өмнөх бүх хувилбаруудыг хадгалах, мөн хэдийн хийгдсэн нүүдлүүдийн хооронд шилжих боломжийг олгоно.

Та өмнөх squares массивуудыг history хэмээх өөр нэг массивт шинэ state хувьсагч болгон хадгалах болно. history массив нь эхний нүүдлээс сүүлчийнх хүртэлх хөлөгийн бүх төлөвийг төлөөлөх бөгөөд иймэрхүү бүтэцтэй байна:

```jsx
[
  // Before first move
  [null, null, null, null, null, null, null, null, null],
  // After first move
  [null, null, null, null, 'X', null, null, null, null],
  // After second move
  [null, null, null, null, 'X', null, null, null, 'O'],
  // ...
]
```

### Төлөвийг (state) дахин дээшлүүлэх {/*lifting-state-up-again*/}

Одоо та өмнөх нүүдлүүдийн жагсаалтыг харуулахын тулд 'Game' нэртэй шинэ дээд түвшний компонент бичих болно. Тоглоомын түүхийг бүхэлд нь агуулсан 'history' төлөвийг та яг тэнд байрлуулна.

'history' төлөвийг 'Game' компонент руу шилжүүлснээр та түүний доод түвшний 'Board' компонентоос 'squares' төлөвийг хасах боломжтой болно. Та 'Square' компонентоос 'Board' компонент руу төлөвийг "дээшлүүлсэнтэй" яг адил одоо 'Board'-оос дээд түвшний 'Game' компонент руу дээшлүүлэх болно. Ингэснээр 'Game' компонент нь 'Board'-ын өгөгдлийг бүрэн хянах бөгөөд 'history'-оос өмнөх нүүдлүүдийг дүрслэхийг 'Board'-д зааварлах боломжтой болно.

Эхлээд, 'export default' бүхий 'Game' компонентыг нэмнэ үү. Үүнд 'Board' компонент болон зарим тэмдэглэгээг (markup) дүрслэхээр оруулна:

```js {1,5-16}
function Board() {
  // ...
}

export default function Game() {
  return (
    <div className="game">
      <div className="game-board">
        <Board />
      </div>
      <div className="game-info">
        <ol>{/*TODO*/}</ol>
      </div>
    </div>
  );
}
```

Та 'function Board() {' тодорхойлолтын өмнөх 'export default' түлхүүр үгсийг хасаж, 'function Game() {' тодорхойлолтын өмнө нэмж байгааг анхаарна уу. Энэ нь таны 'index.js' файлд 'Board' компонентын оронд 'Game' компонентыг дээд түвшний компонент болгон ашиглахыг зааж өгч байгаа юм. 'Game' компонентоос буцааж буй нэмэлт 'div' хэсгүүд нь дараа нь хөлөг дээр нэмэгдэх тоглоомын мэдээлэлд зориулж зай гаргаж байгаа хэрэг юм.

Аль тоглогч дараагийнх болох болон нүүдлийн түүхийг хянахын тулд 'Game' компонентдоо зарим төлөвийг нэмнэ үү:

```js {2-3}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  // ...
```

'[Array(9).fill(null)]' нь дотроо 9 ширхэг 'null' агуулсан массив бүхий ганцхан элементтэй массив байгааг анхаарна уу.

Одоогийн нүүдлийн нүднүүдийг (squares) дүрслэхийн тулд та 'history' массиваас хамгийн сүүлчийн нүднүүдийн массивыг унших хэрэгтэй болно. Үүнд 'useState' ашиглах шаардлагагүй бөгөөд танд үүнийг дүрслэх (rendering) явцад тооцоолоход хангалттай мэдээлэл хэдийн байгаа:

```js {4}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];
  // ...
```

Дараа нь 'Game' компонент дотор тоглоомыг шинэчлэхэд 'Board' компонентоос дуудагдах 'handlePlay' функцийг үүсгэнэ үү. 'xIsNext', 'currentSquares' болон 'handlePlay'-г 'Board' компонент руу 'props' болгон дамжуулна уу:

```js {6-8,13}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    // TODO
  }

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
        //...
  )
}
```

'Board' компонентыг хүлээн авч буй 'props'-оороо бүрэн хянагддаг болгоцгооё. 'Board' компонентыг 'xIsNext', 'squares' болон тоглогч нүүдэл хийх үед 'Board'-оос шинэчлэгдсэн нүднүүдийн массивтай дуудаж болох шинэ 'onPlay' функц гэсэн гурван 'props' хүлээн авдаг болгож өөрчилнө үү. Дараа нь 'Board' функц доторх 'useState' дууддаг эхний хоёр мөрийг хас:

```js {1}
function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    //...
  }
  // ...
}
```

Одоо 'Board' компонент доторх 'handleClick' функцэд байгаа 'setSquares' болон 'setXIsNext' дуудлагуудыг өөрийн шинэ 'onPlay' функцийг дуудах ганц дуудлагаар сольж өөрчилнө үү. Ингэснээр хэрэглэгч нүдэн дээр дарахад 'Game' компонент 'Board'-ыг шинэчлэх боломжтой болно:

```js {12}
function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = "X";
    } else {
      nextSquares[i] = "O";
    }
    onPlay(nextSquares);
  }
  //...
}
```

'Board' компонент нь 'Game' компонентоос дамжуулсан 'props'-оор бүрэн хянагддаг болсон. Тоглоомыг дахин ажиллагаатай болгохын тулд та 'Game' компонент дотор 'handlePlay' функцийг хэрэгжүүлэх шаардлагатай.

'handlePlay' дуудагдах үед юу хийх ёстой вэ? 'Board' өмнө нь шинэчлэгдсэн массивыг 'setSquares'-ээр дамжуулдаг байсныг санаарай; харин одоо шинэчлэгдсэн 'squares' массивыг 'onPlay' руу дамжуулж байгаа.

'handlePlay' функц нь дахин дүрслэлийг (re-render) идэвхжүүлэхийн тулд 'Game'-ийн төлөвийг шинэчлэх шаардлагатай боловч танд дуудаж болох 'setSquares' функц байхгүй болсон—та одоо энэ мэдээллийг хадгалахын тулд 'history' төлөвийн хувьсагчийг ашиглаж байгаа. Та шинэчлэгдсэн 'squares' массивыг түүхийн шинэ бүртгэл болгон нэмэх замаар 'history'-г шинэчлэх хэрэгтэй. Мөн 'Board' өмнө нь хийдэг байсан шиг 'xIsNext'-ийг сэлгэх (toggle) шаардлагатай:

```js {4-5}
export default function Game() {
  //...
  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }
  //...
}
```

Энд, '[...history, nextSquares]' нь 'history' доторх бүх элементүүдийг агуулсан, түүний араас 'nextSquares' залгагдсан шинэ массив үүсгэж байна. (Та '...history' хэсгийг... [*spread syntax*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) " 'history' доторх бүх элементүүдийг нэг бүрчлэн тоочих" гэж ойлгож болно.)

Жишээлбэл, хэрэв 'history' нь '[[null,null,null], ["X",null,null]]' бөгөөд 'nextSquares' нь '["X",null,"O"]' бол шинэ '[...history, nextSquares]' массив нь '[[null,null,null], ["X",null,null], ["X",null,"O"]]' болох юм.

Энэ шатанд та төлөвийг 'Game' компонент руу шилжүүлсэн бөгөөд хэрэглэгчийн интерфэйс (UI) бүтцийн өөрчлөлт (refactor) хийхээс өмнөх шигээ бүрэн ажиллагаатай байх ёстой. Энэ үеийн код дараах байдалтай харагдах болно:

<Sandpack>

```js App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{/*TODO*/}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

### Өмнөх нүүдлүүдийг харуулах {/*showing-the-past-moves*/}

Та тоглоомын түүхийг бүртгэж байгаа тул одоо тоглогчид өмнөх нүүдлүүдийн жагсаалтыг харуулах боломжтой.

React-ийн элементүүд (жишээ нь '<button>') нь жирийн JavaScript объектууд тул та тэдгээрийг аппликейшн дотроо дамжуулан ашиглаж болно. React-д олон тооны элементүүдийг дүрслэхийн тулд та React элементүүдээс бүрдсэн массивыг ашиглаж болно.

Танд 'history' нүүдлүүдийн массив 'state'-д хэдийн байгаа тул одоо үүнийг React элементүүдийн массив болгон хувиргах хэрэгтэй. JavaScript-д нэг массивыг нөгөө массив руу хөрвүүлэхийн тулд та... [array `map` method:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

```jsx
[1, 2, 3].map((x) => x * 2) // [2, 4, 6]
```

Та 'history' нүүдлүүдийн түүхийг дэлгэц дээрх товчлуурууд болгон хувиргаж, өмнөх нүүдлүүд рүү "үсрэх" (jump) товчлууруудын жагсаалтыг харуулахын тулд 'map' ашиглах болно. 'Game' компонент доторх 'history' массив дээр 'map' ашиглацгаая:

```js {11-13,15-27,35}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }

  function jumpTo(nextMove) {
    // TODO
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}
```

Таны кодыг доор харуулав. Хөгжүүлэгчийн хэрэгслийн консол (developer tools console) дээр: Warning: Each child in an array or iterator should have a unique "key" prop. Check the render method of `Game`. гэсэн анхааруулга харагдах ёстойг анхаарна уу. Та энэ алдааг дараагийн хэсэгт засах болно.

<Sandpack>

```js App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }

  function jumpTo(nextMove) {
    // TODO
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}

.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

Та 'history' массиваар 'map' функцэд дамжуулсан функц дотроо итераци хийх үед 'squares' аргумент нь 'history'-ийн элемент бүрээр, харин 'move' аргумент нь массивын индекс бүрээр ('0', '1', '2', …) дамжина. (Ихэнх тохиолдолд танд массивын бодит элементүүд хэрэг болдог ч нүүдлийн жагсаалтыг дүрслэхэд танд зөвхөн индексүүд л хэрэг болно.)

Тоглоомын түүхэн дэх нүүдэл бүрт та товчлуур '<button>' агуулсан жагсаалтын элемент '<li>'-ийг үүсгэнэ. Товчлуур нь 'jumpTo' (та хараахан хэрэгжүүлээгүй байгаа) нэртэй функцийг дууддаг 'onClick' боловсруулагчтай байна.

Одоохондоо танд тоглоомонд хийгдсэн нүүдлүүдийн жагсаалт болон хөгжүүлэгчийн хэрэгслийн консол дээр алдаа харагдах ёстой. 'key' алдаа нь юу гэсэн үг болохыг хэлэлцэцгээе.

Түлхүүр сонгох {/picking-a-key/}
Та жагсаалтыг дүрслэх үед React дүрслэгдсэн жагсаалтын элемент бүрийн талаарх зарим мэдээллийг хадгалдаг. Та жагсаалтыг шинэчлэх үед юу өөрчлөгдсөнийг React тодорхойлох шаардлагатай болдог. Та жагсаалтын элементүүдийг нэмсэн, хассан, дахин эрэмбэлсэн эсвэл шинэчилсэн байж болно.

Дараах байдалд шилжиж байна гэж төсөөлөөд үз дээ:

```html
<li>Alexa: 7 tasks left</li>
<li>Ben: 5 tasks left</li>
```

to

```html
<li>Ben: 9 tasks left</li>
<li>Claudia: 8 tasks left</li>
<li>Alexa: 5 tasks left</li>
```

Шинэчлэгдсэн тоонуудаас гадна үүнийг уншиж буй хүн 'Alexa' болон 'Ben'-ийн дарааллыг сольж, 'Claudia'-г 'Alexa' болон 'Ben' хоёрын хооронд оруулсан байна гэж хэлэх байх. Гэсэн хэдий ч React бол компьютерын программ бөгөөд таны юу бодсоныг мэдэх боломжгүй тул жагсаалтын элемент бүрийг бусад ижил түвшний элементүүдээс ялгахын тулд 'key' шинж чанарыг зааж өгөх шаардлагатай. Хэрэв таны өгөгдөл мэдээллийн сангаас (database) ирсэн бол 'Alexa', 'Ben', болон 'Claudia'-ийн мэдээллийн сангийн 'ID'-уудыг түлхүүр (key) болгон ашиглаж болно.

```js {1}
<li key={user.id}>
  {user.name}: {user.taskCount} tasks left
</li>
```

Жагсаалт дахин дүрслэгдэх (re-render) үед React жагсаалтын элемент бүрийн түлхүүрийг авч, өмнөх жагсаалтын элементүүдээс тохирох түлхүүрийг хайдаг. Хэрэв одоогийн жагсаалтад өмнө нь байгаагүй түлхүүр гарч ирвэл React шинэ компонент үүсгэнэ. Хэрэв өмнөх жагсаалтад байсан түлхүүр одоогийн жагсаалтад байхгүй бол React өмнөх компонентыг устгана. Хэрэв хоёр түлхүүр таарч байвал харгалзах компонент нь шилжинэ.

Түлхүүрүүд (keys) нь React-д компонент бүрийн ижилсэл (identity)-ийг хэлж өгдөг бөгөөд энэ нь React-д дахин дүрслэлүүдийн хооронд төлөвийг (state) хадгалах боломжийг олгодог. Хэрэв компонентын түлхүүр өөрчлөгдвөл тухайн компонент устгагдаж, шинэ төлөвтэйгөөр дахин үүсгэгдэх болно.

'key' бол React-ийн тусгай бөгөөд нөөцлөгдсөн шинж чанар юм. Элемент үүсэх үед React 'key' шинж чанарыг ялган авч, түлхүүрийг буцаагдсан элемент дээр шууд хадгалдаг. Хэдийгээр 'key' нь 'props' байдлаар дамжуулагдаж байгаа мэт харагдавч, React аль компонентыг шинэчлэхээ шийдэхийн тулд 'key'-г автоматаар ашигладаг. Компонент өөрийнх нь эцэг компонент ямар 'key' зааж өгснийг мэдэх боломжгүй байдаг.

Динамик жагсаалт үүсгэх бүртээ тохирох түлхүүрүүдийг оноохыг хатуу зөвлөж байна. Хэрэв танд тохиромжтой түлхүүр байхгүй бол өгөгдлийн бүтцээ өөрчлөх талаар бодож үзээрэй.

Хэрэв ямар нэг түлхүүр зааж өгөөгүй бол React алдаа зааж, массивын индексийг анхдагч түлхүүр болгон ашиглах болно. Жагсаалтын элементүүдийг дахин эрэмбэлэх эсвэл элемент нэмэх/хасах үед массивын индексийг түлхүүр болгон ашиглах нь асуудал үүсгэдэг. Ил тодоор 'key={i}' гэж дамжуулах нь алдааны мэдээг арилгах боловч массивын индекстэй ижил асуудлуудыг дагуулдаг тул ихэнх тохиолдолд зөвлөдөггүй.

Түлхүүрүүд нь дэлхий даяар (globally) цор ганц байх албагүй; тэд зөвхөн тухайн компонент болон түүний ижил түвшний элементүүдийн (siblings) дунд цор ганц байхад хангалттай.

### Цаг хугацаагаар аялахыг хэрэгжүүлэх {/*implementing-time-travel*/}

'Tic-tac-toe' тоглоомын түүхэнд өнгөрсөн нүүдэл бүр өөртэйгөө холбогдсон дахин давтагдашгүй ID-тай байдаг: энэ нь нүүдлийн дарааллын дугаар юм. Нүүдлүүд хэзээ ч дахин эрэмбэлэгдэхгүй, устахгүй, эсвэл дундуур нь нэмэгдэхгүй тул нүүдлийн индексийг түлхүүр ('key') болгон ашиглахад аюулгүй.

'Game' функц дотор та түлхүүрийг '<li key={move}>' байдлаар нэмж болох бөгөөд хэрэв та дүрслэгдсэн тоглоомыг дахин ачаалбал React-ийн 'key' алдаа арилах ёстой:

```js {4}
const moves = history.map((squares, move) => {
  //...
  return (
    <li key={move}>
      <button onClick={() => jumpTo(move)}>{description}</button>
    </li>
  );
});
```

<Sandpack>

```js App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }

  function jumpTo(nextMove) {
    // TODO
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}

```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}

.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

'jumpTo' функцийг хэрэгжүүлэхийн өмнө 'Game' компонент хэрэглэгч яг аль алхмыг үзэж байгааг хянах шаардлагатай. Үүнийг хийхийн тулд анхны утга нь '0' байх 'currentMove' нэртэй шинэ 'state' хувьсагчийг тодорхойлно уу:

```js {4}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const currentSquares = history[history.length - 1];
  //...
}
```

Дараа нь, 'currentMove'-ийг шинэчлэхийн тулд 'Game' доторх 'jumpTo' функцийг шинэчилнэ үү. Мөн та 'currentMove'-ийг өөрчилж буй тоо тань тэгш байвал 'xIsNext'-ийг 'true' болгож тохируулна.

```js {4-5}
export default function Game() {
  // ...
  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
    setXIsNext(nextMove % 2 === 0);
  }
  //...
}
```

Одоо та нүдэн дээр дарахад дуудагддаг 'Game' компонентын 'handlePlay' функцэд хоёр өөрчлөлт оруулна.

-Хэрэв та 'цаг хугацаагаар ухарч' очоод, тухайн цэгээс шинэ нүүдэл хийвэл та зөвхөн тэр цэг хүртэлх түүхийг хадгалахыг хүснэ. 'history' доторх бүх элементүүдийн араас 'nextSquares'-ийг нэмэхийн оронд ('...' spread синтакс), та үүнийг 'history.slice(0, currentMove + 1)'-ийн бүх элементүүдийн араас нэмэх бөгөөд ингэснээр та хуучин түүхийн зөвхөн тухайн хэсгийг хадгалах болно.

-Нүүдэл хийх бүртээ та 'currentMove'-ийг хамгийн сүүлийн түүхийн бичилт рүү зааж байхаар шинэчлэх хэрэгтэй.

```js {2-4}
function handlePlay(nextSquares) {
  const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
  setHistory(nextHistory);
  setCurrentMove(nextHistory.length - 1);
  setXIsNext(!xIsNext);
}
```

Эцэст нь, та 'Game' компонентыг үргэлж хамгийн сүүлийн нүүдлийг дүрслэхийн оронд, одоо сонгогдсон нүүдлийг дүрсэлдэг (render) болгон өөрчилнө:

```js {5}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const currentSquares = history[currentMove];

  // ...
}
```

Хэрэв та тоглоомын түүхийн аль нэг алхам дээр дарвал, 'tic-tac-toe' хөлөг тухайн алхам хийгдсэний дараа ямар харагдаж байсан болж шууд шинэчлэгдэх ёстой.

<Sandpack>

```js App.js
import { useState } from 'react';

function Square({value, onSquareClick}) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
    setXIsNext(!xIsNext);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
    setXIsNext(nextMove % 2 === 0);
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

### Эцсийн цэвэрлэгээ {/*final-cleanup*/}

Хэрэв та кодыг маш анхааралтай ажиглавал 'currentMove' тэгш байх үед 'xIsNext === true', харин 'currentMove' сондгой байх үед 'xIsNext === false' байгааг анзаарч магадгүй. Өөрөөр хэлбэл, хэрэв та 'currentMove'-ийн утгыг мэдэж байвал 'xIsNext' ямар байх ёстойг үргэлж тодорхойлж чадна гэсэн үг.

Эдгээр хоёр утгыг хоёуланг нь 'state'-д хадгалах шаардлага байхгүй. Үнэндээ, илүүдэл 'state'-ээс үргэлж зайлсхийхийг хичээгээрэй. 'State'-д хадгалах зүйлсээ хялбарчлах нь алдааг (bugs) багасгаж, таны кодыг ойлгоход хялбар болгодог. 'Game' компонентыг 'xIsNext'-ийг тусдаа 'state' хувьсагч болгож хадгалахгүйгээр, харин 'currentMove' дээр үндэслэн тодорхойлдог байхаар өөрчилнө үү:

```js {4,11,15}
export default function Game() {
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }
  // ...
}
```

Танд 'xIsNext' 'state' зарлалт эсвэл 'setXIsNext' дуудлагууд дахин хэрэггүй болно. Одоо, компонентын кодыг бичих явцдаа алдаа гаргасан ч 'xIsNext' нь 'currentMove'-тэй синхрончлол алдагдах (зөрөх) ямар ч боломжгүй боллоо.

### Дүгнэлт {/*wrapping-up*/}

Баяр хүргэе! Та дараах боломжуудтай 'tic-tac-toe' тоглоомыг бүтээлээ:

- 'Tic-tac-toe' тоглох боломжтой,
- Тоглогч хожсон үед үүнийг зааж өгдөг,
- Тоглоомын явцад тоглоомын түүхийг хадгалдаг,
- Тоглогчдод тоглоомын түүхийг эргэн харах болон тоглоомын хөлгийн өмнөх хувилбаруудыг үзэх боломжийг олгодог.

Сайн байна! Одоо танд React хэрхэн ажилладаг талаар боломжийн ойлголттой болсон мэт сэтгэгдэл төрж байгаа байх гэж найдаж байна.

Эцсийн үр дүнг эндээс шалгаарай:

<Sandpack>

```js App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

Хэрэв танд нэмэлт цаг байгаа бол эсвэл шинээр сурсан 'React' чадвараа бататгахыг хүсвэл 'tic-tac-toe' тоглоомыг сайжруулах зарим санаануудыг хүндрэлийн зэрэг нэмэгдэх дарааллаар доор жагсаалаа:

1. Зөвхөн одоогийн нүүдлийн хувьд товчлуурын оронд "Та #... нүүдэл дээр байна" гэсэн бичвэрийг харуулах.
2. Board компонентыг нүднүүдийг (squares) нэг бүрчлэн гараар бичихгүйгээр, хоёр давталт (loop) ашиглан үүсгэдэг болгож дахин бичих.
3. Нүүдлүүдийг өсөх эсвэл буурах дарааллаар эрэмбэлэх боломжтой сэлгэн залгах (toggle) товчлуур нэмэх.
4. Нүүдлүүдийг өсөх эсвэл буурах дарааллаар эрэмбэлэх боломжтой сэлгэн залгах (toggle) товчлуур нэмэх.
5. Нүүдлийн түүхийн жагсаалтад нүүдэл бүрийн байршлыг (мөр, багана) гэсэн форматаар харуулах.

Throughout this tutorial, you've touched on React concepts including elements, components, props, and state. Now that you've seen how these concepts work when building a game, check out [Thinking in React](/learn/thinking-in-react) to see how the same React concepts work when build an app's UI.
