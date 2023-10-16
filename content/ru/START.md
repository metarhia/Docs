# Быстрый старт

- Рекомендуем разрабатывать под linux (другие операционные системы
  поддерживаются, но могут быть проблемы, присылайте issue);
- Нам понадобится node.js v12.9.0 или выше;
- Начать проще всего с форка и клонирования на машину репозитория:
[metarhia/Example](https://github.com/metarhia/Example)
- Установим зависимости: `npm i`
- Можем запустить сервер: `node server.js`
- Остановим сервер: `Ctrl + C`

## Разработка API

- Откройте каталог: `application/api/example.1`
  - `application/api` - тут лежат все API доступные по сети;
  - `example` - это название API интерфейса (их может быть много);
  - `1` - это версия интерфейса (их тоже может быть много одновременно);
- Создайте файл: `application/api/example.1/getCity.js`

```js
async ({ cityId }) => {
  if (cityId !== 1) return new Error('Not found');
  return { name: 'Rome', area: 1285, region: 'Lazio' };
};
```

- Теперь можно запустить сервер: `node server.js`
- Откроем браузер и зайдем в DevTools (F12)
- На вкладке Console напишем: `await api.example.getCity({ cityId: 1 });`
- Получим: `metacom.js:18 Uncaught Error: Forbidden`
- Нужно или развернуть базу данных чтобы работала система аутентификации или
  снять ограничения по доступу к этому методу.
- Добавим `access: 'public'`, чтоб получилось так:

```js
({
  access: 'public',

  async method({ cityId }) {
    if (cityId !== 1) return new Error('Not found');
    return { name: 'Rome', area: 1285, region: 'Lazio' };
  },
});
```

- Снова вызовем: `await api.example.getCity({ cityId: 1 });`
- Получим вывод: `{ name: 'Rome', area: 1285, region: 'Lazio' }`
- Теперь добавим проверку контракта:

```js
({
  access: 'public',

  parameters: {
    cityId: 'number',
  },

  async method({ cityId }) {
    if (cityId !== 1) return new Error('Not found');
    return { name: 'Rome', area: 1285, region: 'Lazio' };
  },

  returns: {
    name: 'string',
    area: 'number',
    region: 'string'
  },
});
```

- Попробуем вызвать: `await api.example.getCity({ cityId: '1' });`
- Получим ошибку, ведь мы передали `cityId` типа `string`
- Попробуйте сети отдавать из функции объект неправильной структуры
- Посмотрим более сложный обработчик:
`application/api/example.1/citiesByCountry.js`

```js
async ({ countryId }) => {
  const fields = ['cityId', 'name'];
  const where = { countryId };
  const data = await domain.db.select('City', fields, where);
  return { result: 'success', data };
};
```
