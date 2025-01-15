На этой странице описаны командные договоренности относительно форматирования, последовательности и других правил, применяемых при работе с CSS-стилями

## Рекомендации по наименованию классов
Для более читаемой HTML-разметки и CSS-стилей, рекомендуется применять следующие паттерны наименования классов:

 ### 1. Внешние и внутренние обертки:
 Когда нужно что-то обернуть в дополнительный тэг для решения той или иной задачи используется слово **wrapper**
```html
<article class="something-wrapper">
	<ul class="something-list">...</ul>
	
	<button>...</button>
</article>
```
Реже случается так, что нужно несколько оберток. Для внутренней обертки используется слово **inner**
```html
<article class="something-wrapper">
	<section class="something-inner">
		<ul class="something-list">...</ul>
		
		<button>...</button>
	</section>
</article>
```

 ### 2. Списки:	
```html
<ul class="something-list">
	<li class="something-item"></li>
</ul>
```
Развернутый пример применения:
```html
<ul class="member-list">
	<li class="member-item">
		<article class="member">
			<h4 class="member-name">name</h4>
			
			<p class="member-desription">member description</p>
		</article>
	</li>
</ul>
```
### 3. Кнопки:
Если кнопка подлежит персональной стилизации (имеется в виду, что мы будем описывать ее стили), то рекомендуется указать в названии класса действие кнопки, которая она выполняет. Например: ```.close-button, .reply-button, .cancel-button```

### 4. Summary:
Много примеров не описано, однако имеющиеся, как самые используемые, призваны показать, что наименования классов должны быть легко читаемы и вызывать правильные ассоциации **название <-> применение**.

## Порядок свойств
При описании новых стилей и, по возможности, при редактировании старых, команда опирается на следующий порядок свойств:
```css
.some-class {
  /* Позиционирование */
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: 100;

  /* Блочная модель */
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 10px;
  max-width: 100px;
  min-width: 100px;
  width: 100px
  max-height: 100px;
  min-height: 100px;
  height: 100px;
  ...

  /* Отступы */
  box-sizing: border-box;
  margin: 10px;
  padding: 10px;

  /* Типографика */
  font-family: "Arial", sans-serif;
  font-style: normal;
  font-size: 13px;
  line-height: 20px;
  font-weight: 700;
  text-align: center;
  color: #333333;

  /* Оформление */
  background-color: #f5f5f5;
  border: 1px solid #e5e5e5;
  border-radius: 3px;
  opacity: 1;
  ...

  /* Анимация */
  transition: color 1s;
  ...

  /* Разное */
  cursor: pointer;
  will-change: auto;
  ...
}
```

## БЭМ
Подавляющая часть приложения написана без применения БЭМ, в связи с чем не видится смысла продолжать его использовать, внедрять.  **При описании стилей новых компонентов и редактировании старых эта методология не применяется.**

## !important и ::ng-deep
Строго не рекомендуется прибегать к использованию !important и ::ng-deep. 
В подавляющем большинстве случаев **!important** и **::ng-deep** можно заменить, повысив приоритет стиля. Более подробно в [спецификации](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity).

Случается, что повышением приоритета задачу не решить. Тогда есть большая вероятность, что поможет применение свойств к компоненту в файле **shared-styles.scss** (в контексте нашего приложения, конечно).

Например:
```css
// shared-styles.scss
dct-dialog {
	border-radius: var(--mds-border-radius-xs);
}
```
## Использования токенов
В проекте есть deprecated SCSS переменные, которые более не используются при разработке новых компонентов. Вместо них мы используем т.н. токены, а именно CSS переменные, которые продиктованы нам UI-kit'ом, а именно библиотекой **@mds/tokens**. 

При написании свойств, а именно margin, padding, font, color, background-color, border, border-radius, box-shadow, требуется использовать именно эти токены *(быстро найти их можно в devtools -> Элементы -> Стили -> :root (скрол в самый низ)*