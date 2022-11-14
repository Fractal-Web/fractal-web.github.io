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

   А не вот так:

```ts
function ComponentName(...props): FunctionComponent<IProps> {
	function someFunction() {
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

6. Разбитие импортов и интерфейсов на логические блоки с пометками в виде комментариев. Порядок не важен, главное чтобы при бросании взгляда на комментарий, сразу было понятно, что снизу импортируется. Все библиотечные импорты производятся в самом верху файла:
   
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
import MegaButton from '../components/generic/megaButton';

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
<h3>Названия</h3>
<table>
<tr><th>Заголовок</th> <th>Стиль</th> <th>Пример/Синтаксис</th></tr>
<tr><td>| Название .ts, .tsx, .css, .html файла</td> <td>| lowerCamelCase</td> <td>| fileComponentName</td></tr>
<tr><td>| Название компонента</td> <td>| lowerCamelCase</td> <td>| componentName</td></tr>
<tr><td>| Функция / метод</td> <td>| lowerCamelCase</td> <td>| functionName()</td></tr>
<tr><td>| CSS класс</td> <td>| kebab-case</td> <td>| container-object-property</td></tr>
</table>




<h1 style="color: #44f8da">Документация незавершена и допилится в ближайшее время</h1>
