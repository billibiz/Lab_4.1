# Лабораторная работа 4.1 Чтение данных из файла и их обработка. Запись результатов обработки в файл
## Цель работы: 
Научиться работать с файлами, выполнять их чтение, запись и
манипуляцию, а также освоить методы сериализации и десериализации данных с
использованием популярных форматов хранения, таких как CSV и JSON.

## Задачи работы:
- Изучить структуру файловой системы, свойства файлов, виды путей к
файлам (абсолютный и относительный).
- Освоить операции с файлами в Python: открытие, чтение, запись, закрытие.
- Разобраться с файловыми объектами и их основными свойствами и
методами.
- Изучить методы работы с файлами: чтение файла целиком, построчное
чтение, запись строк в файл.
- Познакомиться с концепцией сериализации и десериализации данных.
- Изучить популярные форматы сериализации: CSV и JSON.
- Научиться использовать модуль pickle для сериализации данных в Python.
- Решить практические задачи по обработке файлов и сериализации данных

**Вариант 20**

**Выполнила: Хлебова Е.М.**

**Группа: АБП-231**

## Тема Простые текстовые файлы

### № 4.1.1

С клавиатуры в одной строке вводится произвольное количество
вещественных чисел. Запишите их в файл, расположив каждое число на отдельной
строке.
```
def load_data():
    data = input("Введите вещественные числа через пробел: ")
    return [float(x) for x in data.split()]
def save_data(values, filename):
    with open(filename, 'w') as file:
        for value in values:
            file.write(str(value) + '\n')
try:
    filename = input("Введите имя файла для сохранения: ")
    values = load_data()
    save_data(values, filename)
    print(f"Данные успешно сохранены в файл {filename}")

except ValueError as err:
    print("Числа были введены с ошибкой.")

except (OSError, IOError) as err:
    print("Ошибка при сохранении файла с данными:", err)

except Exception as err:
    print("Произошла ошибка!")
    print("Тип:", type(err))
    print("Описание:", err)
```

<img width="637" height="746" alt="image" src="https://github.com/user-attachments/assets/c9e7bb2b-22d3-4d3a-9b9e-287f8629227f" />


### № 4.1.2

Дан файл, полученный на выходе задачи № 4.1.1:
- загрузите список чисел;
- вычислите их сумму и максимум и допишите их в файл.
Выполнив программу несколько раз, убедитесь, что новые значения учитываются
при подсчете.

Если файл прочитать не удается, программа должна прекратить чтение и сообщить
об этом пользователю.
```
def load_data(filename):
    """Загрузить список вещественных чисел из файла 'filename'.

    Функция не обрабатывает исключения."""
    numbers = []
    with open(filename, 'r') as file:
        for line in file:
            numbers.append(float(line.strip()))
    return numbers


def append_to_file(values, filename):
    with open(filename, 'a') as file:
        for value in values:
            file.write(str(value) + '\n')


try:
    filename = input("Введите имя файла: ")
    values = load_data(filename)

    total = sum(values)
    maximum = max(values)

    append_to_file([total, maximum], filename)
    print(f"Сумма: {total} и максимум: {maximum} дописаны в файл")

except FileNotFoundError as err:
    print("Указанный файл не существует.")

except (IOError, ValueError) as err:
    print("Ошибка при чтении/сохранении файла с данными:", err)

except Exception as err:
    print("Произошла ошибка!")
    print("Тип:", type(err))
    print("Описание:", err)
```

<img width="463" height="703" alt="image" src="https://github.com/user-attachments/assets/8b996bdd-df90-452b-a1e4-6de3d90bb105" />


### № 4.1.3
Измените задачу № 4.1.2 так, чтобы вне зависимости от ошибок чтения файла,
программа выполняла подсчет суммы и максимума.

```
def load_data(filename):
    numbers = []
    with open(filename, 'r') as file:
        for line in file:
            try:
                numbers.append(float(line.strip()))
            except ValueError:
                continue
    return numbers


def append_to_file(values, filename):
 filename (str): имя файла.
    with open(filename, 'a') as file:
        for value in values:
            file.write(str(value) + '\n')


try:
    filename = input("Введите имя файла: ")
    values = load_data(filename)

    if values:  # Проверяем, что есть корректные числа
        total = sum(values)
        maximum = max(values)

        append_to_file([total, maximum], filename)
        print(f"Сумма: {total} и максимум: {maximum} дописаны в файл")
    else:
        print("В файле нет корректных чисел для обработки")

except FileNotFoundError as err:
    print("Указанный файл не существует.")

except IOError as err:
    print("Ошибка при чтении/сохранении файла с данными:", err)

except Exception as err:
    print("Произошла ошибка!")
    print("Тип:", type(err))
    print("Описание:", err)
```
<img width="493" height="772" alt="image" src="https://github.com/user-attachments/assets/05f61613-abe5-46ed-b09e-30418b5e24e2" />

### № 4.1.4

В файле записано стихотворение. Выведите его на экран, а также укажите,
каких слов в нем больше: начинающихся на гласную или на согласную букву
(регистр не учитывается)?

```
def load_text(filename):
    """Загрузить стихотворение из файла 'filename'.

    Функция не обрабатывает исключения."""
    with open(filename, 'r', encoding='utf-8') as file:
        return file.read()


def letters_stats(text):
    vowels = "аеёиоуыэюяaeiou"
    consonants = "бвгджзйклмнпрстфхцчшщbcdfghjklmnpqrstvwxyz"

    vowel_count = 0
    consonant_count = 0

    for char in text.lower():
        if char in vowels:
            vowel_count += 1
        elif char in consonants:
            consonant_count += 1

    return {
        "гласные": vowel_count,
        "согласные": consonant_count
    }

try:
    filename = input("Введите имя файла: ")
    text = load_text(filename)

    stats = letters_stats(text)
    print(stats)

except FileNotFoundError:
    print("Ошибка: файл не найден.")

except UnicodeDecodeError:
    print("Ошибка: неверная кодировка файла.")

except Exception as err:
    print(f"Произошла ошибка: {err}")
```
<img width="419" height="776" alt="image" src="https://github.com/user-attachments/assets/a552154c-bb30-42ed-bc10-c96856a80b19" />

### № 4.1.5
Информация о занятости мест в зрительном зале кинотеатра хранится в
текстовом файле, где строка обозначает ряд, столбец - место (0 - свободно, 1 - занято).
Напишите программу, которая позволит пользователю увидеть количество
свободных мест, а также, введя номер ряда и места, получить информацию -
свободно оно или нет.

```
# Задание task_04-01_05.
# Вариант 20
# Выполнила: Хлебова Е.М.
# Группа: АБП-231

class NoSuchSeatError(Exception):
    def __init__(self, message):
        super().__init__(message)


def load_seats(filename):
    seats = []
    with open(filename, 'r') as file:
        for line in file:
            row = [int(seat) for seat in line.strip().split()]
            seats.append(row)
    return seats


def vacant_seats(seats):
    count = 0
    for row in seats:
        count += row.count(0)
    return count


def is_seat_vacant(seats, n_p, m_p):
    if n_p < 0 or n_p >= len(seats):
        raise NoSuchSeatError(f"Ряда {n_p + 1} не существует")
    
    if m_p < 0 or m_p >= len(seats[n_p]):
        raise NoSuchSeatError(f"Места {m_p + 1} в ряду {n_p + 1} не существует")
    
    return seats[n_p][m_p] == 0

try:
    filename = input("Введите имя файла: ")
    seats = load_seats(filename)

    count = vacant_seats(seats)
    print("Всего свободных мест: {}".format(count))

    n_p, m_p = [int(item) for item in
                input("Введите ряд и место через пробел: ").split()]

    print("Место свободно:",
          "Да" if is_seat_vacant(seats, n_p - 1, m_p - 1) else "Нет")

except FileNotFoundError:
    print("Ошибка: файл не найден.")

except ValueError:
    print("Ошибка: введите два целых числа (ряд и место).")

except NoSuchSeatError as err:
    print(f"Ошибка: {err}")

except Exception as err:
    print(f"Произошла непредвиденная ошибка: {err}")
```
<img width="680" height="728" alt="image" src="https://github.com/user-attachments/assets/46ccef4f-e44f-4c45-b420-7807b4ccba66" />
<img width="578" height="535" alt="image" src="https://github.com/user-attachments/assets/f3e1ba7d-f810-42dc-ae66-a94881c459d4" />


### № 4.1.6
Дан файл с результатами голосования на выборах в виде:
1 3 14 10 2 3 -1 ...
где номер (через пробел) определяет голос за партию из списка:

1. Партия №1.
2. Партия №2.
3. Партия №3.
4. Партия №4.
5. Партия №5.
-1. Испорченный бланк.


Если при чтении файла какое-либо значение не распознается (не является целым
числом) или партии с таким номером не существует, считается, что бланк испорчен
(-1).
Подведите итоги выборов, выведя на экран список партий в соответствии с
убыванием количества полученных голосов и их процентным соотношением:

1. Партия №2 | 1111 | 58.21%
2. Партия №4 | 999 | 38.14%

...

```
CORRUPTED = -1

parties = {
    1: "Партия №1", 2: "Партия №2", 3: "Партия №3",
    4: "Партия №4", 5: "Партия №5", CORRUPTED: "Испорчено"
}


def load_votes(filename):
    votes = []
    with open(filename, 'r') as file:
        content = file.read()
        for vote in content.split():
            try:
                votes.append(int(vote))
            except ValueError:
                votes.append(CORRUPTED)
    return votes


def parties_votes(parties, votes):
    results = {name: 0 for name in parties.values()}
    
    for vote in votes:
        if vote in parties:
            results[parties[vote]] += 1
        else:
            results[parties[CORRUPTED]] += 1
    
    return results


def print_results(votes_for_p):
    valid_votes = {party: count for party, count in votes_for_p.items() 
                   if party != "Испорчено"}
    total_valid = sum(valid_votes.values())
    
    sorted_parties = sorted(valid_votes.items(), key=lambda x: x[1], reverse=True)
    
    for i, (party, votes) in enumerate(sorted_parties, 1):
        percentage = (votes / total_valid) * 100 if total_valid > 0 else 0
        print(f"{i}. {party} | {votes:>4} | {percentage:>6.2f}%")
    
    if votes_for_p["Испорчено"] > 0:
        print(f"\nИспорченных бюллетеней: {votes_for_p['Испорчено']}")

try:
    filename = input("Введите имя файла: ")
    votes = load_votes(filename)
    votes_for_p = parties_votes(parties, votes)
    print_results(votes_for_p)

except FileNotFoundError:
    print("Ошибка: файл не найден.")

except Exception as err:
    print(f"Произошла ошибка: {err}")
```
<img width="522" height="675" alt="image" src="https://github.com/user-attachments/assets/0f69d8cb-76b9-49bb-b7d4-9b65b95e77b5" />
<img width="679" height="635" alt="image" src="https://github.com/user-attachments/assets/b973c433-e36b-4cb7-a5e5-ec36f2fbcdbf" />

## Тема Сериализация и десериализация
### № 4.1.7
На сайте Всемирного банка (WB) в разделе Data доступна экономическая
статистика о валовом внутреннем продукте (ВВП) на душу населения в долларах
США 1.

Используя подготовленный CSV-файл (GDP per capita (current US$)): CSV-файл
Всемирного банка), реализуйте:
- загрузку данных;
- поиск государства по названию, а также государства с максимальным,
минимальным ВВП на душу населения;
- сохранение данных в новый CSV-файл с фильтром по определенному
условию (например, топ-10 государств по объему ВВП на душу
населения).

***Совет***

*В исходном файле для ряда государств значения ВВП на душу населения не известны - эти
государства должны быть пропущены при загрузке*

```
import csv


class NoSuchCountryError(Exception):
    def __init__(self, message):
        super().__init__(message)


class IllegalArgumentError(ValueError):
    pass


def load_data(filename):
    data = []
    with open(filename, 'r', encoding='utf-8') as file:
        reader = csv.DictReader(file)
        for row in reader:
            if row['gdp'] and row['gdp'].strip():  # Проверяем, что значение существует
                try:
                    data.append({
                        "name": row['name'],
                        "gdp": float(row['gdp'])
                    })
                except ValueError:
                    continue  # Пропускаем некорректные числовые значения
    return data


def search(data, criteria):
    if not data:
        raise NoSuchCountryError("Нет данных для поиска")
    
    # Определение операции
    if criteria == "-max-":
        return max(data, key=lambda x: x["gdp"])
    elif criteria == "-min-":
        return min(data, key=lambda x: x["gdp"])
    else:
        # Поиск по названию страны
        for country in data:
            if country["name"] == criteria:
                return country
        raise NoSuchCountryError(f"Страна '{criteria}' не найдена")


def save_data(filename, data, criteria):
    try:
        method, value = criteria.split("=")
        filtered_data = []

        # Определение операции
        if method == "top":
            x = int(value)
            if x <= 0:
                raise IllegalArgumentError("Значение X должно быть > 0")
            filtered_data = sorted(data, key=lambda x: x["gdp"], reverse=True)[:x]
        elif method == "tail":
            x = int(value)
            if x <= 0:
                raise IllegalArgumentError("Значение X должно быть > 0")
            filtered_data = sorted(data, key=lambda x: x["gdp"])[:x]
        elif method == "greater":
            threshold = float(value)
            filtered_data = sorted([c for c in data if c["gdp"] > threshold], 
                                 key=lambda x: x["gdp"], reverse=True)
        elif method == "less":
            threshold = float(value)
            filtered_data = sorted([c for c in data if c["gdp"] < threshold], 
                                 key=lambda x: x["gdp"])
        else:
            raise IllegalArgumentError("Неизвестный метод")

        # Сохранение в файл
        with open(filename, 'w', newline='', encoding='utf-8') as file:
            writer = csv.DictWriter(file, fieldnames=["name", "gdp"])
            writer.writeheader()
            for country in filtered_data:
                writer.writerow({
                    "name": country["name"],
                    "gdp": country["gdp"]
                })

    except Exception as err:
        raise IllegalArgumentError(
            "Значение параметра 'criteria' может быть "
            "одним из:\n"
            '- "top=X": первые X государств по ВВП на душу населения'
            ' (целое число > 0, по убыванию значения);\n'
            '- "tail=X": последние X государств по ВВП на душу населения'
            ' (целое число > 0, по возрастанию значения);\n'
            '- "greater=X": список государств с ВВП на душу населения, больше'
            ' чем X (вещ. число, по убыванию значения);\n'
            '- "less=X": список государств с ВВП на душу населения, меньше'
            ' чем X (вещ. число, по возрастанию значения).')
try:
    filename = input("Введите имя файла: ")
    # filename = "gdp_per_capita_2016.csv"

    save_filename = input("Введите имя файла для сохранения: ")
    # save_filename = "output.csv"

    data = load_data(filename)
    
    print("\nРезультаты поиска:")
    print("Максимальный ВВП:", search(data, criteria="-max-"))
    print("Минимальный ВВП:", search(data, criteria="-min-"))
    print("Россия:", search(data, criteria="Russian Federation"))

    criteria = input("Введите критерий для сохранения (top=5/tail=5/greater=5000/less=5000): ")
    save_data(save_filename, data, criteria)
    print(f"Данные сохранены в файл {save_filename}")

except FileNotFoundError:
    print("Ошибка: файл не найден.")

except NoSuchCountryError as err:
    print(f"Ошибка поиска: {err}")

except IllegalArgumentError as err:
    print(f"Ошибка аргумента: {err}")

except Exception as err:
    print(f"Произошла непредвиденная ошибка: {err}")
```
<img width="599" height="744" alt="image" src="https://github.com/user-attachments/assets/f0b341f7-19d9-43be-8305-4ebcb0e021b5" />
<img width="574" height="762" alt="image" src="https://github.com/user-attachments/assets/cb884242-6bda-4f1e-834b-a56df40cd57d" />
<img width="637" height="691" alt="image" src="https://github.com/user-attachments/assets/b72bc1d8-c657-4abe-aa2a-17904d4e3d25" />


### № 4.1.8
Федеральная служба государственной статистики приводит данные о заболеваемости населения
по основным классам болезней 2.
С 2022 года материалы перенесены rosstat.gov.ru. Заболеваемость населения по
основным классам болезней в 2000-2021 гг.xls
Подготовленный CSV-файл содержит данные о заболеваниях и количестве заболевших за
конкретный период 2000-2021 гг.:
Предоставив пользователю выбрать 2 различных года для сравнения,
сформируйте JSON-файл, который содержит абсолютные значения (для второго
года) и изменения по количеству заболеваний в процентах (округление до 2-х
знаков после запятой). Значения должны быть сгруппированы по
категории Болезнь и идти по убыванию.

```
import csv
import json


class NoSuchYearError(Exception):
    def __init__(self, message):
        super().__init__(message)


def load_data(filename):
    """Загрузить данные по болезням из csv-файла 'filename'."""
    data = []
    # Загрузка файла
    with open(filename, 'r', encoding='utf-8') as file:
        # Сначала прочитаем первую строку чтобы определить разделитель
        first_line = file.readline()
        
        # Определяем разделитель
        if '\t' in first_line:
            delimiter = '\t'
        elif ',' in first_line:
            delimiter = ','
        else:
            delimiter = None  # Будем использовать стандартный разделитель
            
        # Возвращаемся в начало файла
        file.seek(0)
        
        reader = csv.reader(file, delimiter=delimiter)
        for row in reader:
            data.append(row)
    
    print("Заголовки:", data[0])  # Отладочная информация
    print("Первая строка данных:", data[1] if len(data) > 1 else "Нет данных")  # Отладочная информация
    
    # Преобразование строк в числа (заменяем запятые на точки)
    for i in range(1, len(data)):
        for j in range(1, len(data[i])):
            if j < len(data[i]) and data[i][j]:  # Если значение не пустое и существует
                try:
                    # Заменяем запятую на точку для преобразования в float
                    value_str = data[i][j].replace(',', '.')
                    data[i][j] = float(value_str)
                except ValueError:
                    data[i][j] = 0  # Заменяем некорректные значения на 0
            else:
                data[i][j] = 0
    
    return data


def export(filename, data, year_1, year_2):
    # Проверка параметров 'year_1' и 'year_2'
    headers = data[0]
    print(f"Поиск годов {year_1} и {year_2} в заголовках: {headers}")  # Отладочная информация
    
    # Ищем годы в заголовках (как строки)
    year_1_str = str(year_1)
    year_2_str = str(year_2)
    
    if year_1_str not in headers or year_2_str not in headers:
        available_years = [h for h in headers if h.strip().isdigit()]
        raise NoSuchYearError(f"Год {year_1} или {year_2} отсутствует в данных. Доступные годы: {available_years}")
    
    year_1_idx = headers.index(year_1_str)
    year_2_idx = headers.index(year_2_str)
    
    # Определяем индекс колонки "Возраст" (последняя колонка)
    age_idx = len(headers) - 1
    
    # Формирование списков
    adult_data = []
    children_data = []
    
    for i in range(1, len(data)):
        if len(data[i]) <= max(year_1_idx, year_2_idx, age_idx):
            continue  # Пропускаем строки с недостаточным количеством колонок
            
        disease_class = data[i][0]
        year_1_val = data[i][year_1_idx]
        year_2_val = data[i][year_2_idx]
        age_group = data[i][age_idx] if age_idx < len(data[i]) else ""
        
        # Вычисляем процентное изменение (избегаем деления на 0)
        if year_1_val != 0:
            change_percent = ((year_2_val - year_1_val) / year_1_val) * 100
        else:
            change_percent = 0
        
        disease_info = {
            "класс болезни": disease_class,
            "значение": round(year_2_val, 2),
            "изменение": round(change_percent, 2)
        }
        
        # Разделяем по колонке "Возраст"
        if "взросл" in str(age_group).lower():
            adult_data.append(disease_info)
        elif "дет" in str(age_group).lower():
            children_data.append(disease_info)
    
    # Сортировка по убыванию изменений
    adult_data_sorted = sorted(adult_data, key=lambda x: x["изменение"], reverse=True)
    children_data_sorted = sorted(children_data, key=lambda x: x["изменение"], reverse=True)
    
    # Формируем финальную структуру
    res = {
        "Второй год": {
            "Взрослые": [{"класс болезни": item["класс болезни"], "значение": item["значение"]} 
                         for item in adult_data_sorted],
            "Дети": [{"класс болезни": item["класс болезни"], "значение": item["значение"]} 
                     for item in children_data_sorted]
        },
        "Изменения": {
            "Взрослые": [{"класс болезни": item["класс болезни"], "значение": item["изменение"]} 
                         for item in adult_data_sorted],
            "Дети": [{"класс болезни": item["класс болезни"], "значение": item["изменение"]} 
                     for item in children_data_sorted]
        }
    }
    
    # Сохранение в файл
    with open(filename, 'w', encoding='utf-8') as file:
        json.dump(res, file, ensure_ascii=False, indent=2, separators=(',', ': '))

try:
    filename = "medical_stats.csv"
    export_filename = "output.json"

    data = load_data(filename)
    
    # Получаем доступные годы из заголовков
    available_years = [col for col in data[0] if col.strip().isdigit()]
    print("Доступные годы для анализа:", available_years)
    
    if not available_years:
        print("В данных не найдены числовые годы в заголовках")
        print("Все заголовки:", data[0])
    else:
        # Используем первые два доступных года
        year_1, year_2 = int(available_years[0]), int(available_years[1])
        print(f"Автоматически выбраны годы: {year_1} и {year_2}")
        
        # Или запрашиваем у пользователя
        # year_1 = int(input(f"Введите первый год из {available_years}: "))
        # year_2 = int(input(f"Введите второй год из {available_years}: "))

        export(export_filename, data, year_1, year_2)
        print(f"Данные успешно экспортированы в {export_filename}")

except FileNotFoundError:
    print("Ошибка: файл не найден.")

except NoSuchYearError as err:
    print(f"Ошибка года: {err}")

except ValueError as err:
    print(f"Ошибка значения: {err}")

except Exception as err:
    print(f"Произошла непредвиденная ошибка: {err}")
    import traceback
    traceback.print_exc()
```
<img width="715" height="778" alt="image" src="https://github.com/user-attachments/assets/20208474-cdd0-4ea2-bf2d-7481e6a213e5" />
<img width="752" height="736" alt="image" src="https://github.com/user-attachments/assets/a81d8def-68c8-4f1f-b066-79ff1da57d73" />
<img width="629" height="763" alt="image" src="https://github.com/user-attachments/assets/70d02050-70b7-4409-86ec-74f3c452f5db" />
<img width="1332" height="719" alt="image" src="https://github.com/user-attachments/assets/29104c90-0c67-4d5c-8e5e-44d30779a703" />


### № 4.1.9
На официальном сайте международной федерации хоккея на
льду доступна информация об участниках прошедших чемпионатов мира (ЧМ).
Используя подготовленный JSON-файл с данными игроков сборной России на ЧМ
2016 г. 3.
```
import json
import csv


class NoSuchFieldError(Exception):
    def __init__(self, message):
        super().__init__(message)


class IllegalArgumentError(ValueError):
    pass


def load_players(filename):

    with open(filename, 'r', encoding='utf-8') as file:
        players = json.load(file)
    for player in players:
        # Год рождения из даты
        birth_date = player["дата_рождения"]
        player["год_рождения"] = int(birth_date.split("-")[0])
        

        height_m = player["рост"] / 100  # переводим см в метры
        weight_kg = player["вес"]
        player["ИМТ"] = weight_kg / (height_m ** 2)

        bmi = player["ИМТ"]
        if bmi <= 16:
            player["ИМТ_категория"] = "Выраженный дефицит массы тела"
        elif bmi <= 18.5:
            player["ИМТ_категория"] = "Недостаточная масса тела"
        elif bmi <= 24.99:
            player["ИМТ_категория"] = "Норма"
        elif bmi <= 30:
            player["ИМТ_категория"] = "Избыточная масса тела"
        elif bmi <= 35:
            player["ИМТ_категория"] = "Ожирение первой степени"
        elif bmi <= 40:
            player["ИМТ_категория"] = "Ожирение второй степени"
        else:
            player["ИМТ_категория"] = "Ожирение третьей степени"
    
    return players


def group_by(players, field):
    res = {}
    
    for player in players:
        if field not in player:
            raise NoSuchFieldError(f"Нет данных для игрока по полю '{field}'")
        
        value = player[field]
        if value in res:
            res[value] += 1
        else:
            res[value] = 1
    
    return res


def save_group_data(filename, group_data, headers):

    if not isinstance(headers, list) or len(headers) != 2:
        raise IllegalArgumentError("Заголовки должны быть списком из 2 элементов")
    
    # Сортируем данные: по убыванию количества, при равенстве - по возрастанию имени
    sorted_data = sorted(group_data.items(), key=lambda x: (-x[1], x[0]))
    
    with open(filename, 'w', newline='', encoding='utf-8') as file:
        writer = csv.writer(file)
        writer.writerow(headers)
        for key, value in sorted_data:
            writer.writerow([key, value])

try:
    filename = "russia_wc_2016.json"
    save_filename = "output.csv"

    players = load_players(filename)
    
    group_data = group_by(players, field="имя")

    save_group_data(save_filename, group_data, headers=["Имя", "Количество"])
    print(f"Данные успешно сохранены в {save_filename}")

except FileNotFoundError:
    print("Ошибка: файл не найден.")

except NoSuchFieldError as err:
    print(f"Ошибка поля: {err}")

except IllegalArgumentError as err:
    print(f"Ошибка аргумента: {err}")

except Exception as err:
    print(f"Произошла непредвиденная ошибка: {err}")
```
<img width="504" height="759" alt="image" src="https://github.com/user-attachments/assets/503dc36c-2af0-4412-b56a-a75ee44fef00" />
<img width="669" height="793" alt="image" src="https://github.com/user-attachments/assets/ea15d860-560c-4273-aae5-4655ce97ce1d" />
<img width="416" height="227" alt="image" src="https://github.com/user-attachments/assets/9ec5e699-ca86-4198-84ec-2922896c76b7" />


### № 4.1.10
Дан телефонный справочник в формате JSON.
Программа должна позволять (предоставлять функции):
- загружать информацию из справочника;
- выполнять поиск контактов по номеру телефона;
- выполнять поиск контактов по имени;
- добавлять контакт;
- удалять контакты по имени;
- удалять номер телефона из контактов;
- сохранять справочник в файл.

```
import json


class IllegalArgumentError(ValueError):
    pass


def load_phonebook(filename):
    with open(filename, 'r', encoding='utf-8') as file:
        return json.load(file)


def save_phonebook(filename, phonebook):
    with open(filename, 'w', encoding='utf-8') as file:
        json.dump(phonebook, file, ensure_ascii=False, indent=2)


def search_by_name(phonebook, name):
    if not isinstance(name, str) or not name.strip():
        raise IllegalArgumentError("Имя должно быть непустой строкой")
    
    name_lower = name.lower()
    return [contact for contact in phonebook 
            if name_lower in contact["имя"].lower()]


def search_by_phone(phonebook, phone_number):
    if not isinstance(phone_number, str) or not phone_number.strip():
        raise IllegalArgumentError("Номер телефона должен быть непустой строкой")
    
    result = []
    for contact in phonebook:
        for phone in contact["телефоны"]:
            if phone_number in phone["номер"]:
                result.append(contact)
                break  # Не добавляем контакт повторно если нашли несколько номеров
    return result


def add_record(phonebook, name, phones):
    if not isinstance(name, str) or not name.strip():
        raise IllegalArgumentError("Имя должно быть непустой строкой")
    
    if not isinstance(phones, list):
        raise IllegalArgumentError("Телефоны должны быть списком")
    
    for phone in phones:
        if not isinstance(phone, dict):
            raise IllegalArgumentError("Каждый телефон должен быть словарем")
        if not phone.get("описание") or not phone.get("номер"):
            raise IllegalArgumentError("Описание и номер телефона не должны быть пустыми")
    
    phonebook.append({
        "имя": name,
        "телефоны": phones
    })
    
    return phonebook


def remove_name(phonebook, name):
    if not isinstance(name, str) or not name.strip():
        raise IllegalArgumentError("Имя должно быть непустой строкой")
    
    name_lower = name.lower()
    return [contact for contact in phonebook 
            if name_lower not in contact["имя"].lower()]


def remove_phone(phonebook, phone_number):
    if not isinstance(phone_number, str) or not phone_number.strip():
        raise IllegalArgumentError("Номер телефона должен быть непустой строкой")
    
    for contact in phonebook:
        contact["телефоны"] = [phone for phone in contact["телефоны"] 
                              if phone_number not in phone["номер"]]
    
    return phonebook


def pretty_print(data):
    print(json.dumps(data, ensure_ascii=False, indent=2))

try:
    filename = "phonebook.json"

    phonebook = load_phonebook(filename)
    # pretty_print(phonebook)

    # Поиск
    search_res = search_by_name(phonebook, "ВА")
    # pretty_print(search_res)
    assert len(search_res) == 3

    search_res = search_by_phone(phonebook, "9")
    # pretty_print(search_res)
    assert len(search_res) == 2

    # Добавление
    assert len(search_by_name(phonebook, "Павлова Алиса")) == 0
    phonebook = add_record(phonebook, "Павлова Алиса", [
        {
            "описание": "мобильный",
            "номер": "+7111000033"
        }
    ])
    assert len(search_by_name(phonebook, "Павлова Алиса")) == 1

    phonebook = add_record(phonebook, "Семенов Семен", [
        {
            "описание": "мобильный",
            "номер": "+7111000043"
        },
        {
            "описание": "мобильный 2",
            "номер": "+7222000333"
        }
    ])
    assert len(search_by_name(phonebook, "Семенов Семен")) == 1

    # Удаление
    phonebook = remove_phone(phonebook, "33")
    assert len(search_by_phone(phonebook, "7111000033")) == 0
    assert len(search_by_phone(phonebook, "7222000333")) == 0

    phonebook = remove_name(phonebook, "Семенов Семен")
    assert len(search_by_name(phonebook, "Семен")) == 0

    # pretty_print(phonebook)

    # save_phonebook(filename, phonebook)

    print("Основные операции работают успешно.")

except FileNotFoundError:
    print("Ошибка: файл телефонной книги не найден")

except IllegalArgumentError as err:
    print(f"Ошибка аргумента: {err}")

except Exception as err:
    print(f"Произошла непредвиденная ошибка: {err}")
```

<img width="690" height="776" alt="image" src="https://github.com/user-attachments/assets/f9ab8b80-7231-4bd1-b21b-7dfdaec23032" />
<img width="638" height="765" alt="image" src="https://github.com/user-attachments/assets/0c3bef58-2b94-4587-bdd9-a56feb31b894" />
<img width="454" height="757" alt="image" src="https://github.com/user-attachments/assets/f8f6c319-8892-4a5d-af8b-fc5e5807d2b8" />
<img width="398" height="164" alt="image" src="https://github.com/user-attachments/assets/ebb9abd8-748d-4220-a18c-9fa5727f9174" />


## Вывод:
В ходе выполнения лабораторной работы были освоены ключевые аспекты работы с файлами в Python, включая чтение и запись данных в форматах CSV и JSON. Реализованы программы для обработки разнообразных данных: от простых числовых наборов до сложных структур телефонных книг и спортивной статистики. Полученные навыки позволяют эффективно управлять данными, обрабатывать исключения и создавать надежное программное обеспечение для работы с файлами.
