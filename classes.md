# Style guide: классы, абстракции, константы

На этой странице описаны командные договоренности относительно форматирования, последовательности и других правил, применяемых при работе с CSS-стилями

## Стратегия
Во всех новых компонентах используется строго **onPush** стратегия

### ChangeDetectorRef, async pipe
В подавляющем большинстве случаев можно избежать внедрения **ChangeDatectionRef** и использования его методов ```detectChanges()``` и ```markForCheck()```.
Вместо этого рекомендуется хранить и изменять состояние компонента в реактивном виде (RxJs Observables) и применять с помощью async pipe или изменять через @Input()

Актуально на текущий момент, пока мы не перешли на Angular 18 к zoneless и Signals

## Standalone
Мы используем более удобную систему standalone-компонентов, которая пришла на смену модулям. Все новые компоненты должны быть строго standalone

## constructor, inject
Внедрение зависимостей в новых компонентах происходит строго через **inject()**, а не через конструктор

## Порядок, инкапсуляция полей и методов
### Инкапсуляция
1. Всем полям и методам класса должны быть проставлены флаги доступа, если они отличные от **public** и не нужны клиенту. Он должен иметь доступ только к необходимым элементам.

2. Неперезаписывамые поля обязательно должны иметь флаг **readonly**
### Порядок
При объявлении полей используется следующий порядок:
1. Внедренные зависимости
2. Состояние компонента, иные поля
3. Input
4. Output
5. ViewChild
6. HostBinding

Для методов:

 1. constructor
 2. HostListener
 3. Жизненные циклы компонента (ngOnInit, ngOnDestroy и т.д.) в порядке их выполнения фреймворком
 4. Все остальные **public** методы в алфавитном порядке
 5. **protected** методы в алфавитном порядке
 6. **private** методы в алфавитном порядке

### Итоговый пример
```ts

enum SomeState {
	WAITING = 'waiting',
	PROCESSED = 'processed',
}

class Something {
	private readonly store = inject(Store);

	protected readonly someObservable$ = this.store.select(SomeSelector.Something())
		.pipe(untilDestroyed(this));
	protected someState: SomeState = SomeState.WAITING;
	
	@Input() someInput!: ISomeInput;
	
	@Output() readonly someOutput = new EventEmitter<void>();

	@ViewChild(SomeDirective)
	private readonly someDirective!: SomeDirective;

	@HostListener('keydown.esc')
	private onKeydownEsc(): void {}

	public scrollTo(id: string): void {}

	protected onChildComponentClick(event: Event): void {
		// protected потому что используется в template
		this.processSomething();
	}
	
	private processSomething(): void {
		this.someState = SomeState.PROCESSED;
	}
}
```

## Именование полей и методов
### Общее
Все поля, переменные и методы должны иметь читаемые, внятные и развернутые названия, максимально эффективно воспроизводящие цепь **название <-> ассоциация <-> понимание назначения**.
|Неправильно |Правильно |
|-------------------------------|-----------------------------|
| e           | error|
| cdr |changeDetectionRef|
| ctx|context|
| deleted |isDeleted|
Помимо этого, рекомендуется каждый результат выполнения метода/фукнции выносить в отдельную переменную. Например:
```ts
// Неправильно
const res = [...new Set(arr.map(i => i / 1024 > 1 ? 'kb' : 'b'))];

return res;

// правильно
const BYTES_COUNT = 1024;

enum UnitOfMeasurement {
	BYTE = 'b',
	KILOBYTE = 'kb',
}

const unitsOfMeasurement = listOfFileSizes.map(fileSize => {
	const isMoreThanOrEqual1 = fileSize / BYTES_COUNT >= 1;
	
	return isMoreThanOrEqual1 ? UnitOfMeasurement.KILOBYTE: UnitOfMeasurement.BYTE;
});
const uniqueUnitsOfMeasurement = [...new Set(unitsOfMeasurement)];

return uniqueUnitsOfMeasurement;
```
### boolean
Если поле, переменная типа **boolean** или метод возвращает **boolean**, то их название должно быть вопросительным *(префиксы is/are, has/have, does/do, can и т.д.).* Например: ```isAllowed, areItemsProcessed, canEdit```
### Observable 
Если поле, переменная - это **Observable** (или наследник, типа Subject, BehaviourSubject и т.д.), то **в конце** добавляется знак **\$**. Например:  ``` members$, isAllowed$, currentAction$```
### @Output(), EventEmitter
Если поле в декораторе @Output, то его имя **не** должно начинаться с префикса **on**.
|Неправильно |Правильно |
|-------------------------------|-----------------------------|
| onMemberSelect           | memberSelect            |
| onBottomScroll           |bottomScroll            |
| onReplyClick|replyClick|

В то же время, метод, который передается в слушатель (handler), должен начинать свое название с **on**:
```html
<app-component (memberSelect)="onMemberSelect($event)"></app-component>          
```
Или:
```ts
@HostListener('keydown.esc')
private onKeydownEsc(): void {}
```

## Анимация
Будет в следующей версии

## Именование абстракций
### Interface
Все новые интерфейсы должны иметь префикс I: ```IMember, IDatabase, IProcessable, ITransformable```
### Type
Все новые типы дожны иметь префикс T: ```TMember, TDatabase, TContact, TPhone```

## Именование констант
### const
Все новые константы должны быть названы в формате UPPER_SNAKE_CASE (SCREAMING_SNAKE_CASE).
Не касается **readonly** полей и **const** переменных, которые объявлены внутри блоков кода.
### enum
Все новые **enum** должны называться в ***единичном числе***, в формате PascalCase, а его поля должны иметь формат  UPPER_SNAKE_CASE (SCREAMING_SNAKE_CASE).
```ts
export enum SpeedLimit {
	NONE = 0,
	TWENTY = 20,
	FOURTY_FIVE = 45,
	FIFTY_PLUS_ONE = 51,
}

console.log(SpeedLimit.FOURTY_FIVE);

```