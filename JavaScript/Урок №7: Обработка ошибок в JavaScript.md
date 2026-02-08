# Урок №7: Обработка ошибок в JavaScript

## Введение
**Обработка ошибок** — критически важная часть программирования. Без правильной обработки ошибок приложение становится нестабильным, а пользователи сталкиваются с неожиданными сбоями. JavaScript предоставляет несколько механизмов для обработки ошибок.

Статья с подробный объяснением и примерами про обработку ошибок.
[Обработка ошибок, "try..catch"](https://learn.javascript.ru/try-catch)

## 1. Конструкция `try...catch`<!-- {"fold":true} -->

### Базовый синтаксис
```javascript
try {
  // Код, который может вызвать ошибку
  const result = riskyOperation();
  console.log(result);
} catch (error) {
  // Обработка ошибки
  console.error('Произошла ошибка:', error.message);
}
```

### Как это работает
1. Код внутри `try` выполняется
2. Если ошибки нет — блок `catch` игнорируется
3. Если возникает ошибка — выполнение переходит в блок `catch`
4. Переменная `error` содержит объект ошибки

### Пример
```javascript
try {
  let user = JSON.parse('{неправильный JSON}');
  console.log(user.name);
} catch (err) {
  console.log('Ошибка парсинга JSON:', err.message);
  // Ошибка парсинга JSON: Unexpected token 'н', "неправильный JSON" is not valid JSON
}
```

### Блок `finally`
```javascript
try {
  // Пытаемся что-то сделать
  openFile();
  processFile();
} catch (error) {
  // Обрабатываем ошибку
  console.error(error);
} finally {
  // Этот код выполнится ВСЕГДА
  // Независимо от того, была ошибка или нет
  closeFile();
}
```

**Важно:** Блок `finally` выполняется даже при `return` или `throw` в `try` или `catch`.

## 2. Объект Error<!-- {"fold":true} -->

### Стандартные типы ошибок в JavaScript

1. **Error** — базовая ошибка
2. **SyntaxError** — синтаксическая ошибка
3. **ReferenceError** — ссылка на несуществующую переменную
4. **TypeError** — ошибка типа (например, вызов не функции)
5. **RangeError** — значение вне допустимого диапазона

### Выбрасывание ошибок

#### Оператор `throw`
```javascript
function divide(a, b) {
  if (b === 0) {
    throw new Error('Деление на ноль невозможно');
  }
  return a / b;
}

try {
  console.log(divide(10, 0));
} catch (error) {
  console.error(error.message); // Деление на ноль невозможно
}
```


## Нюансы и лучшие практики<!-- {"fold":true} -->

### 1. Не перехватывайте все ошибки без разбора
```javascript
// Плохо
try {
  // весь код
} catch (error) {
  // просто логируем
}

// Хорошо
try {
  // код, где ожидается конкретная ошибка
} catch (error) {
  if (error instanceof SpecificError) {
    // обрабатываем специфически
  } else {
    // пробрасываем дальше
    throw error;
  }
}
```

### 2. Используйте информацию об ошибках
```javascript
function handleError(error) {
  console.error('Имя ошибки:', error.name);
  console.error('Сообщение:', error.message);
  console.error('Стек вызовов:', error.stack);
  
  if (error.code) {
    console.error('Код ошибки:', error.code);
  }
}
```

### 3. Всегда закрывайте ресурсы в `finally`
```javascript
let connection;
try {
  connection = openDatabaseConnection();
  // работа с БД
} catch (error) {
  console.error('Ошибка БД:', error);
} finally {
  if (connection) {
    connection.close();
  }
}
```

## Практические задачи на урок

### Задача: Валидация пользовательского ввода<!-- {"fold":true} -->
Напишите функцию `calculateSquareRoot(number)`, которая вычисляет квадратный корень числа.
Требования:
1. Если передано отрицательное число — выбрасывайте ошибку
2. Если передано не число — выбрасывайте ошибку
3. Используйте разные типы ошибок для разных случаев
4. Напишите код, который использует эту функцию с обработкой ошибок

##### Решение
```javascript
function calculateSquareRootSimple(number) {
  // Проверка на тип данных
  if (typeof number !== 'number' || isNaN(number)) {
    const error = new Error('Ожидается число');
    error.name = 'NotANumberError';
    throw error;
  }
  
  // Проверка на отрицательное число
  if (number < 0) {
    const error = new Error('Число не может быть отрицательным');
    error.name = 'NegativeNumberError';
    throw error;
  }
  
  // Вычисление квадратного корня
  return Math.sqrt(number);
}

// Использование
try {
  console.log(calculateSquareRootSimple(25)); // 5
  console.log(calculateSquareRootSimple(-9)); // Ошибка
} catch (error) {
  console.error(`Ошибка: ${error.name} - ${error.message}`);
}
```
### 
## Домашнее задание

### Задача: "Калькулятор с обработкой ошибок"<!-- {"fold":true} -->
Создайте функцию `calculator(a, b, operation)`, которая выполняет
арифметические операции: '+', '-', '*', '/'.

Требования:
1. Проверяйте, что a и b — числа
2. Проверяйте, что operation — одна из допустимых операций
3. Для деления проверяйте, что b ≠ 0
4. Напишите пример использования с обработкой всех типов ошибок

### Задача: "Валидатор формы"<!-- {"fold":true} -->

Задача: Создайте функцию `validateForm(data)`, которая:
1. Принимает объект с данными формы: { email: string, password: string, age: number }
2. Проверяет каждое поле независимо, собирая все ошибки в массив
3. Для каждой проверки используйте отдельный try...catch
4. Если есть хотя бы одна ошибка — выбрасывайте общую ошибку FormError со всеми сообщениями

Требования к валидации:
* Email: должен содержать символ @ и точку после него
* Password: минимум 8 символов, должен содержать цифру
* Age: число от 18 до 120

```javascript
Ошибки формы:
1. Email должен содержать @ и точку
2. Пароль должен быть минимум 8 символов и содержать цифру
3. Возраст должен быть от 18 до 120 лет
```

### Задача: "Умный парсер данных"<!-- {"fold":true} --> 

**Задача является сложной и не обязательной**

**Задача:** Создайте функцию `parseUserData(jsonString)`, которая:
1. Пытается распарсить JSON строку в объект
2. Проверяет наличие обязательных полей: `name` (строка) и `age` (число от 0 до 120)
3. Если JSON некорректный — выбрасывает `SyntaxError`
4. Если отсутствуют обязательные поля или они неверного типа — выбрасывает `ValidationError`
5. В блоке `finally` всегда логирует время завершения операции
6. Используйте вложенные try...catch для разных типов ошибок

```javascript
// Пример работы функции
try {
  const user = parseUserData('{"name": "Anna", "age": 25}');
  console.log('Пользователь:', user); // Успех
} catch (error) {
  if (error instanceof SyntaxError) {
    console.log('Ошибка синтаксиса JSON:', error.message);
  } else if (error.name === 'ValidationError') {
    console.log('Ошибка валидации:', error.message);
  }
}
```

