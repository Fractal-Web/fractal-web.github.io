<h1>Правила по форматированию кода</h1>
Форматирование кода - одна из важнейших деталей, которые требуют к себе тщательного внимания, особенно когда речь касается работы в команде.

Пренебрегать ими, по крайней мере в нашем коллективе, строго запрещается. А по сему читаем ниже и мотаем на ус.


<h2>TypeScript файлы</h2>
Для более быстрого форматирования, можно установить себе Prettier и использовать прикреплённый к проекту конфиг, а в настройках поставить форматирование после каждого Ctrl + S. Но иногда Prettier или любой другой форматтер допускает ошибки или же делает код ещё более нечитаемым и тут в ход идут наши ручки. 

Перед тем, как отправить коммит, мы тщательно проходимся по каждому файлу и проверяем, чтобы соблюдались следующие правила:

1. Табуляция с размерностью 4 пробела. Настроить это можно здесь, а там, где форматирование не достаёт - проставить ручками.

   ![](https://github.com/Fractal-Web/Fractal-Web-Documentations/blob/84e165a78a3caf82cd975332efae2aefa4ea859a/Media/Formatting_code_rules/vvJqaN20Wr.gif)
   ![[vvJqaN20Wr.gif]]

   Для особо пытливых есть замечательное расширение <a href="https://marketplace.visualstudio.com/items?itemName=shardulm94.trailing-spaces">Trailing Spaces</a>, которое подсвечивает пробелы красным и не даёт вам пропустить эти моменты в коммит.


2. Все функции пишутся в одном стиле, как <a href="https://learn.javascript.ru/arrow-functions-basics">стрелочные функции</a>, даже функциональные компоненты:

```ts
const ComponentName: FunctionComponent<IProps> = (...props) => {
	const someFunction = () => {
		//some code
	};

	return (
		<div className='component-container'>
			//some code
		</div>
	);
};
```



3. Использование одинарных кавычек вместо двойных. В импортах, в пропах, в значениях и в текстовых блоках. Везде, где нужно использовать кавычки - мы используем одинарные.


4. Пробелы внутри фигурных и квадратных скобок в импортах, пропах, функциях и массивах:

```ts
import React, { FunctionComponent, useRef, useState } from 'react';

const ComponentName = ({ prop, props2 }) => {
	return (
		<div className='component-container'>
			<button onClick={ () => handleClick(prop) }>
				Нажми на меня
			</button>

			<h3 className={[ 'className', 'className-block' ]}
				Я что, заголовок?
			</h3>
		</div>
	);
};
```


5. Пробелы внутри вложенных вызовов:

```ts
alert( pow(a, b) );
```


6. Функции с одиночным вызовом пишутся в одну строку без использования брекетов:

```ts
const functionName = (arg, arg2) => return arg * arg2;
```


7. При декларации типов использовать <b>interface</b> вместо <b>types</b>:

```ts
interface IProps {
	prop: string;
	funcProp: () => void;
}
```


8. Декларировать все созданные переменные, избегая типа <b>any</b>:

```ts
const name: string = "Artem";
const age: number = 19;
```


9. Стараться по максимому использовать <a href="https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Template_literals">интерполяцию</a> вместо функций в одну строку, где это может сократить и упростить код.

<h2>Нейминг всего и вся</h2>
<table>
<tr><th>Заголовок</th> <th>Стиль</th> <th>Пример/Синтаксис</th></tr>
<tr><td>| Название .ts, .tsx, .css, .html файла</td> <td>| lowerCamelCase</td> <td>| fileComponentName.tsx</td></tr>
<tr><td>| Функция / Метод</td> <td>| lowerCamelCase</td> <td>| functionNameLikeThis()</td></tr>
<tr><td>| Переменная</td> <td>| lowerCamelCase</td> <td>| variableNameLikeThis</td></tr>
<tr><td>| Классы / Компоненты</td> <td>| UpperCamelCase</td> <td>| ComponentNameLikeThis()</td></tr>
<tr><td>| Enums / Interfaces / Types</td> <td>| UpperCamelCase</td> <td>| EnumNameLikeThis</td></tr>
<tr><td>| CSS класс</td> <td>| kebab-case</td> <td>| container-object-property</td></tr>
</table>


<h2>Импорты</h2>
1. Разбитие импортов и интерфейсов на логические блоки с пометками в виде комментариев. Порядок не важен, главное чтобы при бросании взгляда на комментарий, сразу было понятно, что снизу импортируется. Все библиотечные импорты производятся в самом верху файла:

```ts
import React, { FunctionComponent, useRef, useState } from 'react';
import { useMediaQuery } from 'react-responsive';

// Styles
import './InDevelopment.css';
import './generic/GlobalStyles.css';

// Media
import '../assets/image.png';
import '../assets/video.mp4';

// Components
import MegaButton from '../components/generic/megaButton.tsx';

// Types
interface ComponentNameProps {
	age: number;
	name: string;
}


const ComponentName: FunctionComponent<ComponentNameProps> = ({
	age,
	name
}) => {
	return (
		//some code
	);
};
```

2. Импорт файлов требуется делать с указанием расширения файла в обязательном порядке, за избежанием ошибок при билде и компиляции:

```ts
import ComponentName from '../components/Component.tsx';
```


<h3>Работа с пропами</h3>
1. В местах, где нужно прокинуть много пропов, стоит сделать интерфейс с типами этих пропов, а сами пропы прокидывать как props={props}.
   Внутри компонента их деконструировать:
```ts
const ComponentName = ( { props: { props: PropsInterface } ) => {
	//some code
}
```