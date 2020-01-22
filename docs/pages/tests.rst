Тестирование
============


Backend
-------

Frontend
--------
Расположение тестов при условии использования vue-шаблона wemake: frontend/tests/components/*.spec.ts

Положим название тестируемой модели/компонента = YourModel/YourVue

Для генерации моделек, используемых в тестах, реализуем factory в файле tests/fixtures/vuex.ts

Используем тип интересуемой модели в строке .extend и добавляем поля, повторяя структуру модели

export const yourModelFactory = new rosie.Factory()
  .extend<FakerFactoryType & YourModelType, FakerFactoryType>(fakerFactory)
  .attr('id', faker.random.number)
  .attr('text', faker.internet.email)

Импортируем yourModelFactory в YourModel.spec.ts

Обновляем типы сущности вашей модели (назовем yourEntity) и store для глобального окоружения тестов

В подготовке глобального контекста (beforeEach) используем созданную фабрику:
yourEntity = yourModelFactory.build()

Структура поля state повторяет StateType из client/logic/types.ts

Далее во всех тестах где либо используется toBeValidProps либо словарь данных располагается в свойства propsData названием поля (этого словаря), в которое кладется сущность из тестируемого контексте (yourEntity), должно совпадать с названием этого же свойства в самой Vue (YourVue), которая в шаблоне расположена в конце файла после @Component({})
export default class YourVue extends mixins(TypedStoreMixin) {

Или же можно в тестируемом контексте назвать переменную модели так, как в Vue-объекте, тогда явно указывать имя в props не будет необходимости.

Каждый тест должен начинаться с expect.hasAssertions()

Дальше можно считать html-сущности wrapper.findAll('button') и тестировать их количество expect(wrapper.findAll('button')).toHaveLength(1)

Обработка html-логики осуществляется в контексте шаблона YourVue.vue (<template>...</template>)

Или обращаться к элементу с определенным классом, ставя точку перед названием класса

expect(wrapper.find('.id').text().trim()).toStrictEqual(`${textItem.id}`)
expect(wrapper.find('.text').text().trim()).toStrictEqual(textItem.text)

В вышеописанном случае фабрика производит id типа number, а find().text() - string, поэтому применяется шаблон форматирования ```${variableName}``` для преобразования числа в строку

Запуск тестов npm run test:unit

E2E
---
Используем testcafe

Пишем простой скрипт, который получает текстовое значение тега, вызывает действие, меняющее это значение, и сравнивает с предыдущим

Сохраним его в tests/e2e/integration.js

import { Selector } from 'testcafe'

fixture('Getting Started')
  .page('http://127.0.0.1:3000/')

  test('My second test', async t => {
  const initialValue = await Selector('#text-1').innerText;
  await t
     .click('#button-1')
     .click('#reloadEntities')
     .expect(Selector('#text-1').innerText).notEql(initialValue);
});

Обязательно указываем fixture

URL, на котором крутится frontend, тоже

Можно обращаться напрямую к объектам без прохождения html-иерархии от корня

После # указывается значение тега id объекта

Для генерации уникальных id внутри Vue-объекта добавляем binding-тег

:id="textId"

При этом содержимое между кавычек интерпретируется как имя переменной, которая должна быть объявлена ниже в шаблоне в export default class YourVue extends mixins(TypedStoreMixin) {

Например так

readonly textId = `text-${this.yourModel.id}`

Запуск тестов testcafe chrome integration.js
