## 1
Найдите номер модели, скорость и размер жесткого диска для всех ПК стоимостью менее 500 дол.
- Вывести: model, speed и hd
```sql
SELECT
    model,
    speed,
    hd
FROM
    PC
WHERE
    price < 500
ORDER BY
    model DESC,
    speed DESC;
```

## 2
Найдите производителей принтеров. Вывести: maker
```sql
SELECT
    DISTINCT maker
FROM
    Product
WHERE
    TYPE = 'Printer';
```

## 3
Найдите номер модели, объем памяти и размеры экранов ПК-блокнотовa, цена которых превышает 1000 дол.
```sql
SELECT
    model,
    ram,
    screen
FROM
    Laptop
WHERE
    price > 1000;
```

## 4
Найдите все записи таблицы Printer для цветных принтеров.
```sql
SELECT
    *
FROM
    Printer
WHERE
    color = 'y';
```

## 5
Найдите номер модели, скорость и размер жесткого диска ПК, имеющих 12x или 24x CD и цену менее 600 дол.
```sql
SELECT
    model,
    speed,
    hd
FROM
    PC
WHERE
    (
        cd = '24x'
        OR cd = '12x'
    )
    AND price < 600;
```

## 6
Для каждого производителя, выпускающего ПК-блокноты c объёмом жесткого диска не менее 10 Гбайт, найти скорости таких ПК-блокнотов.
- Вывод: производитель, скорость.
```sql
SELECT
    DISTINCT Product.maker,
    Laptop.speed
FROM
    Laptop
    INNER JOIN Product ON Product.model = Laptop.model
WHERE
    Laptop.hd >= 10;
```

## 7
Найдите номера моделей и цены всех имеющихся в продаже продуктов (любого типа) производителя B (латинская буква).
### 7.1
```sql
SELECT
    DISTINCT Product.model,
    PC.price
FROM
    Product
    INNER JOIN PC ON PC.model = Product.model
WHERE
    Product.maker = 'B'
UNION
SELECT
    DISTINCT Product.model,
    Laptop.price
FROM
    Product
    INNER JOIN Laptop ON Laptop.model = Product.model
WHERE
    Product.maker = 'B'
UNION
SELECT
    DISTINCT Product.model,
    Printer.price
FROM
    Product
    INNER JOIN Printer ON Printer.model = Product.model
WHERE
    Product.maker = 'B';
```

### 7.2
```sql
SELECT
    a.model,
    price
FROM
    (
        SELECT
            model,
            price
        FROM
            PC
        UNION
        SELECT
            model,
            price
        FROM
            Laptop
        UNION
        SELECT
            model,
            price
        FROM
            Printer
    ) AS a
    JOIN Product p ON a.model = p.model
WHERE
    p.maker = 'B';
```

## 8
Найдите производителя, выпускающего ПК, но не ПК-блокноты.
### 8.1
```sql
SELECT
    maker
FROM
    Product
WHERE
    model IN (
        SELECT
            model
        FROM
            PC
    )
EXCEPT
SELECT
    maker
FROM
    Product
WHERE
    model IN (
        SELECT
            model
        FROM
            Laptop
    );
```
### 8.2
```sql
SELECT
    maker
FROM
    product
WHERE
    TYPE = 'pc'
EXCEPT
SELECT
    maker
FROM
    product
WHERE
    TYPE = 'laptop';
```

## 9
Найдите производителей ПК с процессором не менее 450 Мгц.
- Вывести: Maker
```sql
SELECT
    DISTINCT Product.maker
FROM
    PC
    INNER JOIN Product ON Product.model = PC.model
WHERE
    PC.speed >= 450;
```

## 10
Найдите модели принтеров, имеющих самую высокую цену.
- Вывести: model, price
```sql
SELECT
    DISTINCT model,
    price
FROM
    Printer
WHERE
    price >= (
        SELECT
            MAX(price)
        FROM
            Printer
    );
```

## 11
Найдите среднюю скорость ПК.
```sql
SELECT
    AVG(Speed) AS avgSpeed
FROM
    PC;
```

## 12
Найдите среднюю скорость ПК-блокнотов, цена которых превышает 1000 дол.
```sql
SELECT
    AVG(Speed) AS avgSpeed_forOver1k
FROM
    Laptop
WHERE
    price > 1000;
```

## 13
Найдите среднюю скорость ПК, выпущенных производителем A.
```sql
SELECT
    AVG(PC.Speed) AS avgSpeed_fromAmaker
FROM
    Product
    INNER JOIN PC ON PC.model = Product.model
WHERE
    Product.maker = 'A';
```

## 14
Найдите класс, имя и страну для кораблей из таблицы Ships, имеющих не менее 10 орудий.
```sql
SELECT
    Classes.class,
    Ships.name,
    Classes.country
FROM
    Ships
    INNER JOIN Classes ON Ships.class = Classes.class
WHERE
    Classes.numGuns >= 10;
```

## 15
Найдите размеры жестких дисков, совпадающих у двух и более PC.
- Вывести: HD
```sql
SELECT
    hd
FROM
    PC
GROUP BY
    hd
HAVING
    COUNT (model) >= 2;
```

## 16
Найдите пары моделей PC, имеющих одинаковые скорость и RAM. В результате каждая пара указывается только один раз, т.е. (i,j), но не (j,i),
- Порядок вывода: модель с большим номером, модель с меньшим номером, скорость и RAM.
```sql
SELECT
    DISTINCT A.model AS Amodel,
    B.model AS Bmodel,
    A.speed AS ABspeed,
    A.ram AS ABram
FROM
    PC AS A,
    PC AS B
WHERE
    A.model > B.model
    AND A.speed = B.speed
    AND A.ram = B.ram;
```

## 17
Найдите модели ПК-блокнотов, скорость которых меньше скорости любого из ПК.
- Вывести: type, model, speed
```sql
SELECT
    DISTINCT TYPE,
    Laptop.model,
    speed
FROM
    Laptop
    INNER JOIN Product ON Product.model = Laptop.model
WHERE
    speed < ALL(
        SELECT
            speed
        FROM
            PC
    );
```

## 18
Найдите производителей самых дешевых цветных принтеров.
- Вывести: maker, price
```sql
SELECT
    DISTINCT Product.maker,
    Printer.price
FROM
    Printer
    INNER JOIN Product ON Product.model = Printer.model
WHERE
    Printer.price = (
        SELECT
            MIN(price)
        FROM
            Printer
        WHERE
            color = 'y'
    )
    AND color = 'y';
```

## 19
Для каждого производителя, имеющего модели в таблице Laptop, найдите средний размер экрана выпускаемых им ПК-блокнотов.
- Вывести: maker, средний размер экрана.
```sql
SELECT
    DISTINCT Product.maker,
    AVG(Laptop.screen) AS avgLscreen
FROM
    Laptop
    INNER JOIN Product ON Product.model = Laptop.model
GROUP BY
    maker;
```

## 20
Найдите производителей, выпускающих по меньшей мере три различных модели ПК.
- Вывести: Maker, число моделей ПК.
```sql
SELECT
    DISTINCT Product.maker,
    COUNT(Product.model) AS countModel
FROM
    Product
WHERE
    TYPE = 'PC'
GROUP BY
    maker
HAVING
    COUNT (Product.model) >= 3;
```

## 21
Найдите максимальную цену ПК, выпускаемых каждым производителем, у которого есть модели в таблице PC.
- Вывести: maker, максимальная цена.
```sql
SELECT
    DISTINCT Product.maker,
    MAX(PC.price) AS maxPCprice
FROM
    Product
    INNER JOIN PC ON PC.model = Product.model
WHERE
    TYPE = 'PC'
GROUP BY
    maker;
```

## 22
Для каждого значения скорости ПК, превышающего 600 МГц, определите среднюю цену ПК с такой же скоростью.
- Вывести: speed, средняя цена.
```sql
SELECT
    DISTINCT PC.speed,
    AVG(PC.price) AS avgPCprice
FROM
    PC
WHERE
    PC.speed > 600
GROUP BY
    PC.speed;
```

## 23
Найдите производителей, которые производили бы как ПК со скоростью не менее 750 МГц, так и ПК-блокноты со скоростью не менее 750 МГц.
- Вывести: Maker
```sql
SELECT
    DISTINCT Product.maker
FROM
    Product
    INNER JOIN PC ON PC.model = Product.model
WHERE
    PC.speed >= 750
INTERSECT
SELECT
    DISTINCT Product.maker
FROM
    Product
    INNER JOIN Laptop ON Laptop.model = Product.model
WHERE
    Laptop.speed >= 750;
```

## 24
Перечислите номера моделей любых типов, имеющих самую высокую цену по всей имеющейся в базе данных продукции.
```sql
WITH max_price AS (
    SELECT
        model,
        price
    FROM
        PC
    WHERE
        price = (
            SELECT
                MAX(price)
            FROM
                PC
        )
    UNION
    SELECT
        model,
        price
    FROM
        Laptop
    WHERE
        price = (
            SELECT
                MAX(price)
            FROM
                Laptop
        )
    UNION
    SELECT
        model,
        price
    FROM
        Printer
    WHERE
        price = (
            SELECT
                MAX(price)
            FROM
                Printer
        )
)
SELECT
    model
FROM
    max_price
WHERE
    price = (
        SELECT
            MAX(price)
        FROM
            max_price
    );
```

## 25
Найдите производителей принтеров, которые производят ПК с наименьшим объемом RAM и с самым быстрым процессором среди всех ПК, имеющих наименьший объем RAM.
- Вывести: Maker
```sql
SELECT
    DISTINCT maker
FROM
    product,
    (
        SELECT
            max(speed) AS speed,
            ram
        FROM
            pc
        WHERE
            ram IN (
                SELECT
                    min(ram) AS ram
                FROM
                    pc
            )
        GROUP BY
            ram
    ) AS R
WHERE
    TYPE = 'Printer'
    AND maker IN (
        SELECT
            maker
        FROM
            product
            JOIN pc ON pc.model = product.model
        WHERE
            pc.speed = R.speed
            AND pc.ram = R.ram
    );
```

## 26
Найдите среднюю цену ПК и ПК-блокнотов, выпущенных производителем A (латинская буква).
- Вывести: одна общая средняя цена.
```sql
WITH temp AS (
    SELECT
        model,
        price
    FROM
        PC
    UNION
    ALL
    SELECT
        model,
        price
    FROM
        Laptop
)
SELECT
    avg(price)
FROM
    temp
    JOIN Product AS p ON temp.model = p.model
    AND p.maker = 'A' -- WHERE p.maker = 'A'
;
```

## 27
Найдите средний размер диска ПК каждого из тех производителей, которые выпускают и принтеры.
- Вывести: maker, средний размер HD.
```sql
SELECT
    DISTINCT maker,
    AVG(hd)
FROM
    Product
    INNER JOIN PC ON PC.model = product.model
WHERE
    maker IN (
        SELECT
            maker
        FROM
            product
        WHERE
            TYPE = 'printer'
    )
GROUP BY
    maker;
```

## 28
Используя таблицу Product, определить количество производителей, выпускающих по одной модели.
```sql
WITH one_model_makers AS (
    SELECT
        maker
    FROM
        Product
    GROUP BY
        maker
    HAVING
        count(model) = 1
)
SELECT
    count(maker) AS qty
FROM
    one_model_makers;
```

## 29
В предположении, что приход и расход денег на каждом пункте приема фиксируется не чаще одного раза в день [т.е. первичный ключ (пункт, дата)], написать запрос с выходными данными (пункт, дата, приход, расход).
- Использовать таблицы Income_o и Outcome_o.
### 29.1
```sql
SELECT
    t.point,
    t.date,
    SUM(t.inc),
    sum(t.out)
FROM
    (
        SELECT
            point,
            date,
            inc,
            NULL AS out
        FROM
            Income_o
        UNION
        SELECT
            point,
            date,
            NULL AS inc,
            Outcome_o.out
        FROM
            Outcome_o
    ) AS t
GROUP BY
    t.point,
    t.date
SELECT
    CASE
        WHEN i.point IS NOT NULL THEN i.point
        ELSE o.point
    END,
    CASE
        WHEN i.date IS NOT NULL THEN i.date
        ELSE o.date
    END,
    inc,
    out
FROM
    Income_o i FULL
    OUTER JOIN Outcome_o o ON i.point = o.point
    AND i.date = o.date;
```

### 29.2
```sql
SELECT
    t.point,
    t.date,
    SUM(t.inc),
    sum(t.out)
FROM
    (
        SELECT
            point,
            date,
            inc,
            NULL AS out
        FROM
            Income_o
        UNION
        SELECT
            point,
            date,
            NULL AS inc,
            Outcome_o.out
        FROM
            Outcome_o
    ) AS t
GROUP BY
    t.point,
    t.date;
```

## 30
В предположении, что приход и расход денег на каждом пункте приема фиксируется произвольное число раз (первичным ключом в таблицах является столбец code), требуется получить таблицу, в которой каждому пункту за каждую дату выполнения операций будет соответствовать одна строка.
- Вывод: point, date, суммарный расход пункта за день (out), суммарный приход пункта за день (inc).
- Отсутствующие значения считать неопределенными (NULL).
### 30.1
```sql
SELECT
    point,
    date,
    SUM(sum_out),
    SUM(sum_inc)
FROM
    (
        SELECT
            point,
            date,
            SUM(inc) AS sum_inc,
            NULL AS sum_out
        FROM
            Income
        GROUP BY
            point,
            date
        UNION
        SELECT
            point,
            date,
            NULL AS sum_inc,
            SUM(out) AS sum_out
        FROM
            Outcome
        GROUP BY
            point,
            date
    ) AS t
GROUP BY
    point,
    date
ORDER BY
    point;
```

### 30.2
```sql
WITH PNT (point, Date, INC, OUT) AS (
    SELECT
        point,
        date,
        NULL,
        inc
    FROM
        Income
    UNION
    ALL
    SELECT
        point,
        date,
        out,
        NULL
    FROM
        Outcome
)
SELECT
    point,
    date,
    SUM(INC),
    SUM(OUT)
FROM
    PNT
GROUP BY
    point,
    date;
```

### 30.3
```sql
SELECT
    Point,
    date,
    sum(out) AS out,
    sum (inc) AS inc
FROM
    (
        SELECT
            Point,
            Date,
            out,
            NULL inc
        FROM
            outcome
        UNION
        ALL
        SELECT
            Point,
            Date,
            NULL,
            inc
        FROM
            income
    ) x
GROUP BY
    point,
    date;
```

## 31
Для классов кораблей, калибр орудий которых не менее 16 дюймов, укажите класс и страну.
```sql
SELECT
    DISTINCT class,
    country
FROM
    Classes
WHERE
    Classes.bore >= 16;
```

## 32
Одной из характеристик корабля является половина куба калибра его главных орудий (mw). С точностью до 2 десятичных знаков определите среднее значение mw для кораблей каждой страны, у которой есть корабли в базе данных.
### 32.1
```sql
SELECT
    Classes.country,
    cast(avg((power(Classes.bore, 3) / 2)) AS numeric(6, 2))
FROM
    Classes
GROUP BY
    Classes.country;
```

### 32.2
```sql
SELECT
    country,
    cast(avg((power(bore, 3) / 2)) AS numeric(6, 2)) AS weight
FROM
    (
        SELECT
            country,
            Classes.class,
            bore,
            name
        FROM
            Classes
            JOIN Ships ON Classes.class = Ships.class
        UNION
        ALL
        SELECT
            DISTINCT country,
            class,
            bore,
            ship
        FROM
            Classes AS t1
            JOIN Outcomes AS t2 ON t1.class = t2.ship
        WHERE
            ship = class
            AND ship NOT IN (
                SELECT
                    name
                FROM
                    Ships
            )
    ) AS a
WHERE
    name IS NOT NULL
GROUP BY
    country;
```

### 32.3
```sql
WITH t AS (
    SELECT
        country,
        bore
    FROM
        Classes
        JOIN (
            SELECT
                DISTINCT ship
            FROM
                Outcomes
        ) x ON class = ship
    WHERE
        ship NOT IN (
            SELECT
                name
            FROM
                Ships
        )
    UNION
    ALL
    SELECT
        country,
        bore
    FROM
        Classes c
        JOIN Ships s ON c.class = s.class
)
SELECT
    country,
    cast(avg(power(bore, 3) / 2) AS numeric(6, 2)) AS avg_mv
FROM
    t
GROUP BY
    country
```

### 32.4
```sql
SELECT
    country,
    CAST(AVG(POWER(bore, 3) / 2) AS numeric(6, 2)) AS weight
FROM
    Classes
    JOIN (
        SELECT
            class
        FROM
            Ships
        UNION
        ALL (
            SELECT
                ship AS class
            FROM
                Outcomes
            EXCEPT
            SELECT
                name AS class
            FROM
                Ships
        )
    ) AS t ON Classes.class = t.class
GROUP BY
    country;
```

## 33
Укажите корабли, потопленные в сражениях в Северной Атлантике (North Atlantic).
- Вывод: ship.
```sql
SELECT
    ship
FROM
    Outcomes
WHERE
    battle = 'North Atlantic'
    AND result = 'sunk';
```

## 34
По Вашингтонскому международному договору от начала 1922 г. запрещалось строить линейные корабли водоизмещением более 35 тыс.тонн.
Укажите корабли, нарушившие этот договор (учитывать только корабли c известным годом спуска на воду).
- Вывести названия кораблей.
```sql
SELECT
    Ships.name
FROM
    Ships
    INNER JOIN Classes ON Ships.class = Classes.class
WHERE
    Ships.launched IS NOT NULL
    AND Ships.launched >= 1922
    AND Classes.type = 'bb'
    AND Classes.displacement > 35000;
```

## 35
В таблице Product найти модели, которые состоят только из цифр или только из латинских букв (A-Z, без учета регистра).
- Вывод: номер модели, тип модели.
```sql
SELECT
    Product.model,
    Product.type
FROM
    Product
WHERE
    Product.model NOT LIKE '%[^0-9]%'
    OR Product.model NOT LIKE '%[^A-Z]%';
```

## 36
Перечислите названия головных кораблей, имеющихся в базе данных (учесть корабли в Outcomes).
```sql
SELECT
    name
FROM
    Ships
WHERE
    class = name
UNION
SELECT
    ship AS name
FROM
    Classes,
    Outcomes
WHERE
    Classes.class = Outcomes.ship;
```

## 37
Найдите классы, в которые входит только один корабль из базы данных (учесть также корабли в Outcomes).
```sql
SELECT
    class
FROM
    (
        SELECT
            name,
            class
        FROM
            Ships
        UNION
        SELECT
            class AS name,
            class
        FROM
            Classes,
            Outcomes
        WHERE
            Classes.class = Outcomes.ship
    ) A
GROUP BY
    class
HAVING
    count(A.name) = 1;
```

## 38
Найдите страны, имевшие когда-либо классы обычных боевых кораблей ('bb') и имевшие когда-либо классы крейсеров ('bc').
### 38.1
```sql
SELECT
    Classes.country
FROM
    Classes
WHERE
    Classes.type = 'bc'
INTERSECT
SELECT
    Classes.country
FROM
    Classes
WHERE
    Classes.type = 'bb';
```

### 38.2
```sql
SELECT
    country
FROM
    Classes
GROUP BY
    country
HAVING
    COUNT(DISTINCT TYPE) = 2;
```

## 39
Найдите корабли, 'сохранившиеся для будущих сражений'; т.е. выведенные из строя в одной битве (damaged), они участвовали в другой, произошедшей позже.
### 39.1
```sql
WITH temp AS (
    SELECT
        *
    FROM
        Outcomes
        JOIN Battles ON Outcomes.battle = Battles.name
)
SELECT
    DISTINCT a.ship
FROM
    temp AS a
WHERE
    UPPER(a.ship) IN (
        SELECT
            UPPER(b.ship)
        FROM
            temp AS b
        WHERE
            b.date < a.date
            AND b.result = 'damaged'
    );
```

### 39.2
```sql
SELECT
    DISTINCT ccc.sh
FROM
    (
        SELECT
            aaa.ship AS sh,
            aaa.[date] AS d1,
            bbb.[date] AS d2
        FROM
            (
                SELECT
                    ship,
                    [date]
                FROM
                    Outcomes AS o
                    INNER JOIN battles AS b ON o.battle = b.name
                WHERE
                    result = 'damaged'
            ) AS aaa
            INNER JOIN (
                SELECT
                    ship,
                    [date]
                FROM
                    Outcomes AS o
                    INNER JOIN battles AS b ON o.battle = b.name
            ) AS bbb ON aaa.ship = bbb.ship
        WHERE
            bbb.date > aaa.date
    ) AS ccc;
```

### 39.3
```sql
WITH b_s AS (
    SELECT
        o.ship,
        b.name,
        b.date,
        o.result
    FROM
        Outcomes o
        LEFT JOIN battles b ON o.battle = b.name
)
SELECT
    DISTINCT a.ship
FROM
    b_s a
WHERE
    UPPER(a.ship) IN (
        SELECT
            UPPER(ship)
        FROM
            b_s b
        WHERE
            b.date < a.date
            AND b.result = 'damaged'
    );
```

### 39.4
```sql
WITH aq AS (
    SELECT
        *
    FROM
        Outcomes o
        JOIN battles b ON b.name = o.battle
)
SELECT
    DISTINCT ship
FROM
    aq
WHERE
    EXISTS (
        SELECT
            aq1.ship
        FROM
            aq AS aq1
        WHERE
            aq.ship = aq1.ship
            AND aq.battle != aq1.battle
            AND aq.date < aq1.date
            AND aq.result = 'damaged'
    );
```

## 40
Найти производителей, которые выпускают более одной модели, при этом все выпускаемые производителем модели являются продуктами одного типа.
- Вывести: maker, type
### 40.1
```sql
SELECT
    maker,
    MAX(TYPE) AS TYPE
FROM
    product
GROUP BY
    maker
HAVING
    COUNT (model) > 1
    AND MAX(TYPE) = MIN(TYPE);
```

### 40.2
```sql
SELECT
    maker,
    MAX(TYPE) AS TYPE
FROM
    product
GROUP BY
    maker
HAVING
    COUNT (model) > 1
    AND COUNT(DISTINCT TYPE) = 1;
```

## 41
Для каждого производителя, у которого присутствуют модели хотя бы в одной из таблиц PC, Laptop или Printer, определить максимальную цену на его продукцию.
- Вывод: имя производителя, если среди цен на продукцию данного производителя присутствует NULL, то выводить для этого производителя NULL, иначе максимальную цену.
### 41.2
```sql
WITH temp AS (
    SELECT
        Product.maker,
        temp2.price
    FROM
        Product
        JOIN (
            SELECT
                model,
                price
            FROM
                PC
            UNION
            SELECT
                model,
                price
            FROM
                Laptop
            UNION
            SELECT
                model,
                price
            FROM
                Printer
        ) AS temp2 ON temp2.model = product.model
)
SELECT
    DISTINCT maker,
    IIF(COUNT(*) != COUNT(price), NULL, MAX(price))
FROM
    temp
GROUP BY
    maker;
```

### 41.2
```sql
WITH D AS (
    SELECT
        model,
        price
    FROM
        PC
    UNION
    SELECT
        model,
        price
    FROM
        Laptop
    UNION
    SELECT
        model,
        price
    FROM
        Printer
)
SELECT
    DISTINCT P.maker,
    CASE
        WHEN MAX(
            CASE
                WHEN D.price IS NULL THEN 1
                ELSE 0
            END
        ) = 0 THEN MAX(D.price)
    END
FROM
    Product P
    RIGHT JOIN D ON P.model = D.model
GROUP BY
    P.maker;
```

### 41.3
```sql
SELECT
    maker,
    iif(count(*) = count(price), max(price), NULL) m_price
FROM
    product p
    JOIN (
        SELECT
            model,
            price
        FROM
            pc
        UNION
        ALL
        SELECT
            model,
            price
        FROM
            laptop
        UNION
        ALL
        SELECT
            model,
            price
        FROM
            printer
    ) plp ON p.model = plp.model
GROUP BY
    maker;
```

## 42
Найдите названия кораблей, потопленных в сражениях, и название сражения, в котором они были потоплены.
```sql
SELECT
    ship,
    battle
FROM
    Outcomes
WHERE
    result = 'sunk';
```

## 43
Укажите сражения, которые произошли в годы, не совпадающие ни с одним из годов спуска кораблей на воду.
### 43.1
```sql
SELECT
    Battles.name
FROM
    Battles
WHERE
    YEAR(Battles.date) NOT IN (
        SELECT
            Ships.launched
        FROM
            Ships
        WHERE
            Ships.launched IS NOT NULL
    );
```

### 43.2
```sql
SELECT
    name
FROM
    battles
WHERE
    DATEPART(yy, date) NOT IN (
        SELECT
            DATEPART(yy, date)
        FROM
            battles
            JOIN Ships ON DATEPART(yy, date) = launched
    );
```

## 44
Найдите названия всех кораблей в базе данных, начинающихся с буквы R.
### 44.1
```sql
WITH temp AS (
    SELECT
        Ships.name
    FROM
        Ships
    UNION
    SELECT
        Outcomes.ship AS name
    FROM
        Outcomes
)
SELECT
    DISTINCT temp.name
FROM
    temp
WHERE
    temp.name LIKE 'R%';
```

### 44.2
```sql
SELECT
    Ships.name
FROM
    Ships
WHERE
    Ships.name LIKE 'R%'
UNION
SELECT
    Outcomes.ship
FROM
    Outcomes
WHERE
    Outcomes.ship LIKE 'R%';
```

## 45
Найдите названия всех кораблей в базе данных, состоящие из трех и более слов (например, King George V).
- Считать, что слова в названиях разделяются единичными пробелами, и нет концевых пробелов.
```sql
WITH temp AS (
    SELECT
        Ships.name
    FROM
        Ships
    UNION
    SELECT
        Outcomes.ship AS name
    FROM
        Outcomes
)
SELECT
    DISTINCT temp.name
FROM
    temp
WHERE
    temp.name LIKE '% % %';
```

## 46
Для каждого корабля, участвовавшего в сражении при Гвадалканале (Guadalcanal), вывести название, водоизмещение и число орудий.
```sql
SELECT
    DISTINCT ship,
    displacement,
    numguns
FROM
    Classes
    LEFT JOIN Ships ON Classes.class = Ships.class
    RIGHT JOIN Outcomes ON Classes.class = Outcomes.ship
    OR Ships.name = Outcomes.ship
WHERE
    battle = 'Guadalcanal';
```

## 47
Определить страны, которые потеряли в сражениях все свои корабли.
### 47.1
```sql
WITH out AS (
    SELECT
        *
    FROM
        outcomes
        JOIN (
            SELECT
                ships.name s_name,
                classes.class s_class,
                classes.country s_country
            FROM
                ships FULL
                JOIN classes ON ships.class = classes.class
        ) u ON outcomes.ship = u.s_class
    UNION
    SELECT
        *
    FROM
        outcomes
        JOIN (
            SELECT
                ships.name s_name,
                classes.class s_class,
                classes.country s_country
            FROM
                ships FULL
                JOIN classes ON ships.class = classes.class
        ) u ON outcomes.ship = u.s_name
)
SELECT
    fin.country
FROM
    (
        SELECT
            DISTINCT t.country,
            COUNT(t.name) AS num_ships
        FROM
            (
                SELECT
                    DISTINCT c.country,
                    s.name
                FROM
                    classes c
                    INNER JOIN Ships s ON s.class = c.class
                UNION
                SELECT
                    DISTINCT c.country,
                    o.ship
                FROM
                    classes c
                    INNER JOIN Outcomes o ON o.ship = c.class
            ) t
        GROUP BY
            t.country
        INTERSECT
        SELECT
            out.s_country,
            COUNT(out.ship) AS num_ships
        FROM
            out
        WHERE
            out.result = 'sunk'
        GROUP BY
            out.s_country
    ) fin;
```

### 47.2
```sql
WITH w AS(
    SELECT
        c.country,
        s.name
    FROM
        Classes c
        LEFT JOIN ships s ON s.class = c.class
    WHERE
        name IS NOT NULL
    UNION
    SELECT
        c.country,
        o.ship
    FROM
        Classes c
        LEFT JOIN outcomes o ON c.class = o.ship
    WHERE
        o.ship IS NOT NULL
)
SELECT
    country
FROM
    w
    LEFT JOIN (
        SELECT
            ship,
            result
        FROM
            outcomes
        WHERE
            result = 'sunk'
    ) o ON w.name = o.ship
GROUP BY
    country
HAVING
    count(name) = count(result);
```

## 48
Найдите классы кораблей, в которых хотя бы один корабль был потоплен в сражении.
### 48.1
```sql
SELECT
    DISTINCT Classes.class
FROM
    Classes
    LEFT JOIN Ships ON Classes.class = Ships.class
    RIGHT JOIN Outcomes ON Classes.class = Outcomes.ship
    OR Ships.name = Outcomes.ship
WHERE
    result = 'sunk'
    AND Classes.class IS NOT NULL;
```

### 48.2
```sql
SELECT
    class
FROM
    classes t1
    LEFT JOIN outcomes t2 ON t1.class = t2.ship
WHERE
    result = 'sunk'
UNION
SELECT
    class
FROM
    ships
    LEFT JOIN outcomes ON ships.name = outcomes.ship
WHERE
    result = 'sunk';
```

## 49
Найдите названия кораблей с орудиями калибра 16 дюймов (учесть корабли из таблицы Outcomes).
### 49.1
```sql
WITH temp AS (
    SELECT
        class
    FROM
        classes
    WHERE
        bore = 16
)
SELECT
    name
FROM
    ships
WHERE
    class IN(
        SELECT
            *
        FROM
            temp
    )
UNION
SELECT
    ship
FROM
    outcomes
WHERE
    ship IN(
        SELECT
            *
        FROM
            temp
    );
```

### 49.2
```sql
SELECT
    s.name
FROM
    ships s
    JOIN classes c ON s.name = c.class
    OR s.class = c.class
WHERE
    c.bore = 16
UNION
SELECT
    o.ship
FROM
    outcomes o
    JOIN classes c ON o.ship = c.class
WHERE
    c.bore = 16;
```

### 49.3
```sql
WITH q1 AS(
    SELECT
        ship,
        class
    FROM
        classes,
        outcomes
    WHERE
        ship = class
    UNION
    SELECT
        name,
        class
    FROM
        ships
)
SELECT
    ship
FROM
    q1,
    classes cl
WHERE
    cl.class = q1.class
    AND bore = 16;
```

## 50
Найдите сражения, в которых участвовали корабли класса Kongo из таблицы Ships.
### 50.1
```sql
SELECT
    DISTINCT o.battle
FROM
    ships s
    JOIN outcomes o ON s.name = o.ship
WHERE
    s.class = 'kongo';
```

### 50.2
```sql
SELECT
    DISTINCT battle
FROM
    Classes
    INNER JOIN Ships ON ships.class = classes.class
    INNER JOIN Outcomes ON Classes.class = Outcomes.ship
    OR Ships.name = Outcomes.ship
WHERE
    classes.class = 'Kongo';
```

## 51
Найдите названия кораблей, имеющих наибольшее число орудий среди всех имеющихся кораблей такого же водоизмещения (учесть корабли из таблицы Outcomes).
### 51.1
```sql
SELECT
    NAME
FROM
    (
        SELECT
            name AS NAME,
            displacement,
            numguns
        FROM
            ships
            INNER JOIN classes ON ships.class = classes.class
        UNION
        SELECT
            ship AS NAME,
            displacement,
            numguns
        FROM
            outcomes
            INNER JOIN classes ON outcomes.ship = classes.class
    ) AS d1
    INNER JOIN (
        SELECT
            displacement,
            max(numGuns) AS numguns
        FROM
            (
                SELECT
                    displacement,
                    numguns
                FROM
                    ships
                    INNER JOIN classes ON ships.class = classes.class
                UNION
                SELECT
                    displacement,
                    numguns
                FROM
                    outcomes
                    INNER JOIN classes ON outcomes.ship = classes.class
            ) AS f
        GROUP BY
            displacement
    ) AS d2 ON d1.displacement = d2.displacement
    AND d1.numguns = d2.numguns;
```

### 51.2
```sql
WITH t AS (
    SELECT
        s.name,
        numGuns,
        displacement
    FROM
        ships s
        JOIN classes c ON s.class = c.class
    UNION
    SELECT
        o.ship,
        numGuns,
        displacement
    FROM
        Outcomes o
        JOIN classes c ON o.ship = c.class
    WHERE
        o.ship NOT IN (
            SELECT
                name
            FROM
                ships
        )
)
SELECT
    name
FROM
    t t1
WHERE
    t1.numGuns = (
        SELECT
            max(t2.numGuns)
        FROM
            t t2
        WHERE
            t1.displacement = t2.displacement
    );
```

## 52
Определить названия всех кораблей из таблицы Ships, которые могут быть линейным японским кораблем, имеющим число главных орудий не менее девяти, калибр орудий менее 19 дюймов и водоизмещение не более 65 тыс.тонн
### 52.1
```sql
SELECT
    DISTINCT Ships.name
FROM
    Ships
    INNER JOIN Classes ON Ships.class = Classes.class
WHERE
    Classes.country = 'Japan'
    AND Classes.type = 'bb'
    AND (
        Classes.numGuns >= 9
        OR Classes.numGuns IS NULL
    )
    AND (
        Classes.bore < 19
        OR Classes.bore IS NULL
    )
    AND (
        Classes.displacement <= 65000
        OR Classes.displacement IS NULL
    );
```

### 52.2
```sql
SELECT
    DISTINCT name
FROM
    ships
    LEFT JOIN classes ON classes.class = ships.class
WHERE
    COALESCE(country, 'Japan') = 'Japan'
    AND COALESCE(numGuns, 9) >= 9
    AND COALESCE(displacement, 65000) <= 65000
    AND COALESCE(TYPE, 'bb') = 'bb'
    AND COALESCE(bore, 18) < 19;
```

## 53
Определите среднее число орудий для классов линейных кораблей. Получить результат с точностью до 2-х десятичных знаков.
```sql
SELECT
    cast(
        AVG(Classes.numGuns * 1.0) AS numeric(6, 2)
    )
FROM
    Classes
WHERE
    TYPE = 'bb';
```

## 54
С точностью до 2-х десятичных знаков определите среднее число орудий всех линейных кораблей (учесть корабли из таблицы Outcomes).
### 54.1
```sql
SELECT
    CAST(AVG(numguns * 1.0) AS NUMERIC(6, 2)) AS AVG_nmg
FROM
    (
        SELECT
            ship,
            TYPE,
            numguns
        FROM
            Outcomes
            LEFT JOIN Classes ON ship = class
        UNION
        SELECT
            name,
            TYPE,
            numguns
        FROM
            Ships AS S
            INNER JOIN Classes AS C ON c.class = s.class
    ) AS T
WHERE
    TYPE = 'bb';
```

### 54.2
```sql
WITH allShips AS (
    SELECT
        name,
        class
    FROM
        Ships
    UNION
    SELECT
        ship AS name,
        ship AS class
    FROM
        Outcomes
)
SELECT
    CAST(
        AVG(CAST(numGuns AS decimal(10, 2))) AS decimal(10, 2)
    ) AS Avg_umG
FROM
    allShips
    JOIN Classes c ON allShips.class = c.class
WHERE
    c.type = 'bb';
```

## 55
Для каждого класса определите год, когда был спущен на воду первый корабль этого класса. Если год спуска на воду головного корабля неизвестен, определите минимальный год спуска на воду кораблей этого класса.
- Вывести: класс, год.
```sql
SELECT
    classes.class,
    min(ships.launched)
FROM
    classes
    LEFT JOIN ships ON classes.class = ships.class
GROUP BY
    classes.class;
```

## 56
Для каждого класса определите число кораблей этого класса, потопленных в сражениях.
- Вывести: класс и число потопленных кораблей.
### 56.1
```sql
SELECT
    c.class,
    COUNT(s.ship)
FROM
    classes c
    LEFT JOIN (
        SELECT
            o.ship,
            sh.class
        FROM
            outcomes o
            LEFT JOIN ships sh ON sh.name = o.ship
        WHERE
            o.result = 'sunk'
    ) AS s ON s.class = c.class
    OR s.ship = c.class
GROUP BY
    c.class;
```

### 56.2
```sql
WITH ships1 AS (
    SELECT
        c.class,
        o.result,
        o.ship
    FROM
        classes c
        LEFT JOIN outcomes o ON o.ship = c.class
    UNION
    SELECT
        s.class,
        o.result,
        s.name
    FROM
        outcomes o
        JOIN ships s ON o.ship = s.name
)
SELECT
    class,
    sum(
        CASE
            WHEN result = 'sunk' THEN 1
            ELSE 0
        END
    )
FROM
    ships1
GROUP BY
    class;
```

## 57
Для классов, имеющих потери в виде потопленных кораблей и не менее 3 кораблей в базе данных.
- Вывести имя класса и число потопленных кораблей.
### 57.1
```sql
SELECT
    class AS cls,
    count(class) AS sunked
FROM
    (
        SELECT
            C.class,
            O.ship
        FROM
            classes AS C
            JOIN outcomes AS O ON C.class = O.ship
        WHERE
            O.result = 'sunk'
        UNION
        SELECT
            S.class,
            O.ship
        FROM
            outcomes AS O
            JOIN ships AS S ON S.name = O.ship
        WHERE
            O.result = 'sunk'
    ) AS T
WHERE
    class IN (
        SELECT
            DISTINCT X.class
        FROM
            (
                SELECT
                    C.class,
                    O.ship
                FROM
                    classes AS C
                    JOIN outcomes AS O ON C.class = O.ship
                UNION
                SELECT
                    C.class,
                    S.name
                FROM
                    classes AS C
                    JOIN ships AS S ON C.class = S.class
            ) AS X
        GROUP BY
            X.class
        HAVING
            count(X.class) >= 3
    )
GROUP BY
    class;
```

### 57.2
```sql
SELECT
    isnull(s.class, o.ship) mixed_class,
    sum(iif(result = 'sunk', 1, 0)) sunk_amount
FROM
    ships s FULL
    JOIN outcomes o ON s.name = o.ship
GROUP BY
    isnull(s.class, o.ship)
HAVING
    count(DISTINCT isnull(s.name, o.ship)) >= 3
    AND max(result) = 'sunk';
```

## 58
Для каждого типа продукции и каждого производителя из таблицы Product c точностью до двух десятичных знаков найти процентное отношение числа моделей данного типа данного производителя к общему числу моделей этого производителя.
- Вывод: maker, type, процентное отношение числа моделей данного типа к общему числу моделей производителя
### 58.1
```sql
WITH pcj AS (
    SELECT
        *
    FROM
        (
            SELECT
                DISTINCT [maker]
            FROM
                product
        ) AS tp
        CROSS JOIN (
            SELECT
                DISTINCT [type]
            FROM
                Product
        ) AS tt
)
SELECT
    DISTINCT pcj.maker,
    pcj.type,
    cast(
        count(model) over(PARTITION by pcj.[maker], pcj.[type]) * 100.0 / count(model) over(PARTITION by pcj.[maker]) AS numeric(10, 2)
    ) AS val
FROM
    pcj
    LEFT JOIN product ON pcj.maker = product.maker
    AND pcj.type = product.type;
```

### 58.2
```sql
WITH pcj AS (
    SELECT
        *
    FROM
        (
            SELECT
                DISTINCT [maker]
            FROM
                product
        ) AS tp
        CROSS JOIN (
            SELECT
                'PC' AS [type]
            UNION
            SELECT
                'Laptop'
            UNION
            SELECT
                'Printer'
        ) AS tt
)
SELECT
    DISTINCT pcj.maker,
    pcj.type,
    cast(
        (
            SELECT
                count(model)
            FROM
                product AS p
            WHERE
                p.maker = pcj.[maker]
                AND p.[type] = pcj.[type]
        ) * 100.0 /(
            SELECT
                count(model)
            FROM
                product AS p
            WHERE
                p.maker = pcj.[maker]
        ) AS numeric(10, 2)
    ) AS val
FROM
    pcj
    LEFT JOIN product ON pcj.maker = product.maker
    AND pcj.type = product.type
ORDER BY
    maker;
```

### 58.3
```sql
SELECT
    DISTINCT p.maker,
    pr.type,
    (
        SELECT
            (
                SELECT
                    (
                        CAST(
                            COUNT(productsByType.type) * 1.0 / COUNT(prod.type) * 1.0 * 100 AS DECIMAL(8, 2)
                        )
                    )
                FROM
                    Product productsByType
                WHERE
                    productsByType.maker = p.maker
                    AND productsByType.type = pr.type
            )
        FROM
            Product prod
        WHERE
            prod.maker = p.maker
    )
FROM
    Product p
    CROSS JOIN Product pr
GROUP BY
    p.maker,
    pr.type;
```

## 59
Посчитать остаток денежных средств на каждом пункте приема для базы данных с отчетностью не чаще одного раза в день.
- Вывод: пункт, остаток.
### 59.1
```sql
SELECT
    a.point,
    CASE
        WHEN o IS NULL THEN i
        ELSE i - o
    END remain
FROM
    (
        SELECT
            point,
            sum(inc) AS i
        FROM
            Income_o
        GROUP BY
            point
    ) AS A
    LEFT JOIN (
        SELECT
            point,
            sum(out) AS o
        FROM
            Outcome_o
        GROUP BY
            point
    ) AS B ON A.point = B.point;
```

### 59.2
```sql
SELECT
    A.point,
    (COALESCE (si, 0) - COALESCE (so, 0))
FROM
    (
        SELECT
            point,
            sum(inc) AS si
        FROM
            income_o AS i
        GROUP BY
            point
    ) AS A FULL
    JOIN (
        SELECT
            point,
            sum(out) AS so
        FROM
            outcome_o AS o
        GROUP BY
            point
    ) AS B ON A.point = B.point;
```

### 59.3
```sql
SELECT
    point,
    SUM(inc) - SUM(out) AS Remain
FROM
    (
        SELECT
            point,
            date,
            inc,
            0 AS out
        FROM
            income_o
        UNION
        SELECT
            point,
            date,
            0 AS inc,
            out
        FROM
            outcome_o
    ) inc_out
GROUP BY
    point;
```

## 60
Посчитать остаток денежных средств на начало дня 15/04/01 на каждом пункте приема для базы данных с отчетностью не чаще одного раза в день.
- Вывод: пункт, остаток.
- Замечание. Не учитывать пункты, информации о которых нет до указанной даты.
### 60.1
```sql
SELECT
    c1,
    c2 - (
        CASE
            WHEN o2 IS NULL THEN 0
            ELSE o2
        END
    )
FROM
    (
        SELECT
            point c1,
            sum(inc) c2
        FROM
            income_o
        WHERE
            date < '2001-04-15'
        GROUP BY
            point
    ) AS t1
    LEFT JOIN (
        SELECT
            point o1,
            sum(out) o2
        FROM
            outcome_o
        WHERE
            date < '2001-04-15'
        GROUP BY
            point
    ) AS t2 ON c1 = o1;
```

### 60.2
```sql
SELECT
    point,
    SUM(inc) - SUM(out) AS Remain
FROM
    (
        SELECT
            point,
            date,
            inc,
            0 AS out
        FROM
            income_o
        WHERE
            date < '2001-04-15'
        UNION
        SELECT
            point,
            date,
            0 AS inc,
            out
        FROM
            outcome_o
        WHERE
            date < '2001-04-15'
    ) inc_out
GROUP BY
    point;
```

## 61
Посчитать остаток денежных средств на всех пунктах приема для базы данных с отчетностью не чаще одного раза в день.
### 61.1
```sql
SELECT
    sum(i)
FROM
    (
        SELECT
            point,
            sum(inc) AS i
        FROM
            income_o
        GROUP BY
            point
        UNION
        SELECT
            point,
            - sum(out) AS i
        FROM
            outcome_o
        GROUP BY
            point
    ) AS t;
```

### 61.2
```sql
SELECT
    sum(inc) - isnull(
        (
            SELECT
                sum(out)
            FROM
                Outcome_o
        ),
        0
    )
FROM
    Income_o;
```

## 62
Посчитать остаток денежных средств на всех пунктах приема на начало дня 15/04/01 для базы данных с отчетностью не чаще одного раза в день.
### 62.1
```sql
SELECT
    (
        SELECT
            sum(inc)
        FROM
            Income_o
        WHERE
            date < '2001-04-15'
    ) - (
        SELECT
            sum(out)
        FROM
            Outcome_o
        WHERE
            date < '2001-04-15'
    ) AS remain;
```

### 62.2
```sql
SELECT
    (
        SELECT
            COALESCE(SUM(inc), 0)
        FROM
            income_o
        WHERE
            date < '20010415'
    ) - (
        SELECT
            COALESCE(SUM(out), 0)
        FROM
            outcome_o
        WHERE
            date < '20010415'
    ) AS remain;
```

## 63
Определить имена разных пассажиров, когда-либо летевших на одном и том же месте более одного раза.
```sql
SELECT
    name
FROM
    Passenger
WHERE
    ID_psg IN (
        SELECT
            ID_psg
        FROM
            Pass_in_trip
        GROUP BY
            place,
            ID_psg
        HAVING
            count(*) > 1
    );
```

## 64
Используя таблицы Income и Outcome, для каждого пункта приема определить дни, когда был приход, но не было расхода и наоборот.
- Вывод: пункт, дата, тип операции (inc/out), денежная сумма за день.
### 64.1
```sql
SELECT
    i1.point,
    i1.date,
    'inc',
    sum(inc)
FROM
    Income,
    (
        SELECT
            point,
            date
        FROM
            Income
        EXCEPT
        SELECT
            Income.point,
            Income.date
        FROM
            Income
            JOIN Outcome ON (Income.point = Outcome.point)
            AND (Income.date = Outcome.date)
    ) AS i1
WHERE
    i1.point = Income.point
    AND i1.date = Income.date
GROUP BY
    i1.point,
    i1.date
UNION
SELECT
    o1.point,
    o1.date,
    'out',
    sum(out)
FROM
    Outcome,
    (
        SELECT
            point,
            date
        FROM
            Outcome
        EXCEPT
        SELECT
            Income.point,
            Income.date
        FROM
            Income
            JOIN Outcome ON (Income.point = Outcome.point)
            AND (Income.date = Outcome.date)
    ) AS o1
WHERE
    o1.point = Outcome.point
    AND o1.date = Outcome.date
GROUP BY
    o1.point,
    o1.date;
```

### 64.2
```sql
SELECT
    point,
    date,
    min(STATUS),
    sum(res)
FROM
    (
        SELECT
            point,
            date,
            'inc' AS STATUS,
            inc AS res
        FROM
            income
        UNION
        ALL
        SELECT
            point,
            date,
            'out' AS STATUS,
            out AS res
        FROM
            outcome
    ) AS data
GROUP BY
    point,
    date
HAVING
    min(STATUS) = max(STATUS);
```

## 65
Пронумеровать уникальные пары {maker, type} из Product, упорядочив их следующим образом:
- имя производителя (maker) по возрастанию;
- тип продукта (type) в порядке PC, Laptop, Printer.

Если некий производитель выпускает несколько типов продукции, то выводить его имя только в первой строке;
остальные строки для ЭТОГО производителя должны содержать пустую строку символов ('').
### 65.1
```sql
WITH pmq AS (
    SELECT
        [maker],
        [type],
        CASE
            WHEN [type] = 'PC' THEN 0
            WHEN [type] = 'Laptop' THEN 1
            ELSE 2
        END AS [s],
        CASE
            WHEN [type] = 'Laptop'
            AND maker IN (
                SELECT
                    maker
                FROM
                    [Product]
                WHERE
                    [type] = 'PC'
            ) THEN ''
            WHEN [type] = 'Printer'
            AND maker IN (
                SELECT
                    maker
                FROM
                    [Product]
                WHERE
                    [type] = 'PC'
                    OR [type] = 'Laptop'
            ) THEN ''
            ELSE [maker]
        END AS m
    FROM
        [dbo].[Product]
    GROUP BY
        [maker],
        [type]
)
SELECT
    row_number() over(
        ORDER BY
            [maker],
            [s]
    ) num,
    [m],
    [type]
FROM
    [pmq]
ORDER BY
    [maker],
    [s];
```

### 65.2
```sql
SELECT
    row_number() over(
        ORDER BY
            maker,
            len(TYPE)
    ) AS rowNumber,
    CASE
        WHEN row_number() over(
            PARTITION by maker
            ORDER BY
                len(TYPE)
        ) = 1 THEN maker
        ELSE ''
    END AS maker,
    TYPE
FROM
    Product
GROUP BY
    maker,
    TYPE;
```

## 66
Для всех дней в интервале с 01/04/2003 по 07/04/2003 определить число рейсов из Rostov.
- Вывод: дата, количество рейсов
### 66.1
```sql
SELECT
    date,
    max(c)
FROM
    (
        SELECT
            date,
            count(*) AS c
        FROM
            Trip,
            (
                SELECT
                    trip_no,
                    date
                FROM
                    Pass_in_trip
                WHERE
                    date >= '2003-04-01'
                    AND date <= '2003-04-07'
                GROUP BY
                    trip_no,
                    date
            ) AS t1
        WHERE
            Trip.trip_no = t1.trip_no
            AND town_from = 'Rostov'
        GROUP BY
            date
        UNION
        ALL
        SELECT
            '2003-04-01',
            0
        UNION
        ALL
        SELECT
            '2003-04-02',
            0
        UNION
        ALL
        SELECT
            '2003-04-03',
            0
        UNION
        ALL
        SELECT
            '2003-04-04',
            0
        UNION
        ALL
        SELECT
            '2003-04-05',
            0
        UNION
        ALL
        SELECT
            '2003-04-06',
            0
        UNION
        ALL
        SELECT
            '2003-04-07',
            0
    ) AS t2
GROUP BY
    date;
```

### 66.2
```sql
WITH ds AS (
    SELECT
        cast(datefromparts(2003, 04, 01) AS datetime) AS [tdate]
    UNION
    ALL
    SELECT
        dateadd(DAY, 1, [tdate])
    FROM
        ds
    WHERE
        datepart(DAY, [tdate]) < 07
),
tbd AS (
    SELECT
        [date],
        count(trip.trip_no) num
    FROM
        dbo.Trip
        JOIN (
            SELECT
                DISTINCT trip_no,
                [date]
            FROM
                pass_in_trip
        ) AS rts ON trip.[trip_no] = rts.[trip_no]
    WHERE
        [date] >= datefromparts(2003, 04, 01)
        AND [date] <= datefromparts(2003, 04, 07)
        AND town_from = 'Rostov'
    GROUP BY
        [date]
)
SELECT
    [tdate],
    coalesce(num, 0) AS num
FROM
    ds
    LEFT JOIN tbd ON ds.tdate = tbd.date;
```

## 67
Найти количество маршрутов, которые обслуживаются наибольшим числом рейсов.
- Замечания:
    - A - B и B - A считать РАЗНЫМИ маршрутами.
    - Использовать только таблицу Trip
### 67.1
```sql
SELECT
    count(*)
FROM
    (
        SELECT
            TOP 1 WITH TIES count(*) c,
            town_from,
            town_to
        FROM
            trip
        GROUP BY
            town_from,
            town_to
        ORDER BY
            c DESC
    ) AS t;
```

### 67.2
```sql
WITH a AS(
    SELECT
        count(trip_no) q,
        town_from,
        town_to
    FROM
        trip
    GROUP BY
        town_from,
        town_to
)
SELECT
    count(a.town_from) ww
FROM
    a
GROUP BY
    q
HAVING
    q IN (
        SELECT
            max(q)
        FROM
            a
    );
```

### 67.3
```sql
SELECT
    count(*) qty
FROM
    (
        SELECT
            Top 1 WITH TIES count(*) qty,
            town_from,
            town_to
        FROM
            Trip
        GROUP BY
            town_from,
            town_to
        ORDER BY
            qty DESC
    ) AS t1;
```

## 68
Найти количество маршрутов, которые обслуживаются наибольшим числом рейсов.
- Замечания:
    - A - B и B - A считать ОДНИМ И ТЕМ ЖЕ маршрутом.
    - Использовать только таблицу Trip
### 68.1
```sql
SELECT
    count(*)
FROM
    (
        SELECT
            TOP 1 WITH TIES sum(c) cc,
            c1,
            c2
        FROM
            (
                SELECT
                    count(*) c,
                    town_from c1,
                    town_to c2
                FROM
                    trip
                WHERE
                    town_from >= town_to
                GROUP BY
                    town_from,
                    town_to
                UNION
                ALL
                SELECT
                    count(*) c,
                    town_to,
                    town_from
                FROM
                    trip
                WHERE
                    town_to > town_from
                GROUP BY
                    town_from,
                    town_to
            ) AS t
        GROUP BY
            c1,
            c2
        ORDER BY
            cc DESC
    ) AS tt;
```

### 68.2
```sql
SELECT
    COUNT(*) qty
FROM
    (
        SELECT
            TOP(1) WITH TIES COUNT(*) c
        FROM
            (
                SELECT
                    trip_no,
                    CASE
                        WHEN town_from > town_to THEN town_from + town_to
                        ELSE town_to + town_from
                    END root
                FROM
                    trip
            ) r
        GROUP BY
            root
        ORDER BY
            c DESC
    ) t;
```

### 68.3
```sql
SELECT
    count (y)
FROM
    (
        SELECT
            TOP 1 WITH TIES count (DISTINCT trip_no) y,
            [from],
            [to]
        FROM
            (
                SELECT
                    (
                        SELECT
                            min (c)
                        FROM
                            (
                                VALUES
                                    (town_from),
                                    (town_to)
                            ) AS v(c)
                    ) [from],
                    (
                        SELECT
                            max (c)
                        FROM
                            (
                                VALUES
                                    (town_from),
                                    (town_to)
                            ) AS v(c)
                    ) [to],
                    t.*
                FROM
                    trip t
            ) x
        GROUP BY
            [from],
            [to]
        ORDER BY
            y DESC
    ) z;
```

## 69
По таблицам Income и Outcome для каждого пункта приема найти остатки денежных средств на конец каждого дня, в который выполнялись операции по приходу и/или расходу на данном пункте.
- Учесть при этом, что деньги не изымаются, а остатки/задолженность переходят на следующий день.
- Вывод: пункт приема, день в формате "dd/mm/yyyy", остатки/задолженность на конец этого дня.
### 69.1
```sql
WITH t AS (
    SELECT
        point,
        date,
        inc,
        0 AS out
    FROM
        income
    UNION
    ALL
    SELECT
        point,
        date,
        0 AS inc,
        out
    FROM
        outcome
)
SELECT
    t.point,
    format(t.date, 'dd/MM/yyyy') AS DAY,
    (
        SELECT
            SUM(i.inc)
        FROM
            t i
        WHERE
            i.date <= t.date
            AND i.point = t.point
    ) - (
        SELECT
            SUM(i.out)
        FROM
            t i
        WHERE
            i.date <= t.date
            AND i.point = t.point
    ) AS rem
FROM
    t
GROUP BY
    t.point,
    t.date;
```

### 69.2
```sql
WITH Money_inc_out AS (
    SELECT
        point,
        date AS DAY,
        inc AS rem
    FROM
        Income
    UNION
    ALL
    SELECT
        point,
        date,
        - out
    FROM
        Outcome
)
SELECT
    point,
    CONVERT(char, DAY, 103) AS DAY,
    (
        SELECT
            SUM(rem)
        FROM
            Money_inc_out
        WHERE
            point = a.point
            AND DAY <= a.day
    ) rem
FROM
    Money_inc_out AS a
GROUP BY
    point,
    DAY;
```

## 70
Укажите сражения, в которых участвовало по меньшей мере три корабля одной и той же страны.
### 70.1
```sql
SELECT
    DISTINCT o.battle
FROM
    outcomes o
    LEFT JOIN ships s ON s.name = o.ship
    LEFT JOIN classes c ON o.ship = c.class
    OR s.class = c.class
WHERE
    c.country IS NOT NULL
GROUP BY
    c.country,
    o.battle
HAVING
    COUNT(o.ship) >= 3;
```

### 70.2
```sql
SELECT
    DISTINCT o.battle
FROM
    outcomes o
    LEFT JOIN ships s ON o.ship = s.name
    JOIN classes c ON c.class = isnull(s.class, o.ship)
GROUP BY
    o.battle,
    country
HAVING
    count(country) > 2;
```

## 71
Найти тех производителей ПК, все модели ПК которых имеются в таблице PC.
### 71.1
```sql
SELECT
    p.maker
FROM
    product p
    LEFT JOIN pc ON pc.model = p.model
WHERE
    p.type = 'PC'
GROUP BY
    p.maker
HAVING
    COUNT(p.model) = COUNT(pc.model);
```

### 71.2
```sql
SELECT
    DISTINCT maker
FROM
    Product p
WHERE
    p.maker NOT IN (
        SELECT
            maker
        FROM
            product FULL
            JOIN PC ON PC.model = product.model
        WHERE
            PC.model IS NULL
            AND TYPE = 'PC'
    )
    AND p.maker IN (
        SELECT
            maker
        FROM
            product
        WHERE
            TYPE = 'PC'
    );
```

## 72
Среди тех, кто пользуется услугами только какой-нибудь одной компании, определить имена разных пассажиров, летавших чаще других.
- Вывести: имя пассажира и число полетов.
### 72.1
```sql
SELECT
    TOP 1 WITH TIES name,
    c3
FROM
    passenger
    JOIN (
        SELECT
            c1,
            max(c3) c3
        FROM
            (
                SELECT
                    pass_in_trip.ID_psg c1,
                    Trip.ID_comp c2,
                    count(*) c3
                FROM
                    pass_in_trip
                    JOIN trip ON trip.trip_no = pass_in_trip.trip_no
                GROUP BY
                    pass_in_trip.ID_psg,
                    Trip.ID_comp
            ) AS t
        GROUP BY
            c1
        HAVING
            count(*) = 1
    ) AS tt ON ID_psg = c1
ORDER BY
    c3 DESC;
```

### 72.2
```sql
WITH ppc AS(
    SELECT
        id_psg,
        max(cntFly) mx
    FROM
        (
            SELECT
                id_psg,
                count(id_comp) cntFly
            FROM
                pass_in_trip pit
                JOIN trip t ON pit.trip_no = t.trip_no
            GROUP BY
                id_psg,
                id_comp
        ) s
    GROUP BY
        id_psg
    HAVING
        count(id_psg) = 1
)
SELECT
    top(1) WITH ties name,
    mx
FROM
    ppc
    JOIN passenger p ON ppc.id_psg = p.id_psg
ORDER BY
    mx DESC;
```

### 72.3
```sql
WITH asd AS (
    SELECT
        pt.id_psg,
        Count(pt.trip_no) cnt,
        Max(Count(pt.trip_no)) over () cmax
    FROM
        pass_in_trip pt
        JOIN trip t ON t.trip_no = pt.trip_no
    GROUP BY
        pt.id_psg
    HAVING
        Max(t.id_comp) = Min(t.id_comp)
)
SELECT
    p.name,
    a.cnt
FROM
    asd a
    JOIN passenger p ON p.id_psg = a.id_psg
WHERE
    a.cnt = a.cmax;
```

## 73
Для каждой страны определить сражения, в которых не участвовали корабли данной страны.
- Вывод: страна, сражение
### 73.1
```sql
SELECT
    DISTINCT c.country,
    b.name
FROM
    battles b,
    classes c
EXCEPT
SELECT
    c.country,
    o.battle
FROM
    outcomes o
    LEFT JOIN ships s ON s.name = o.ship
    LEFT JOIN classes c ON o.ship = c.class
    OR s.class = c.class
WHERE
    c.country IS NOT NULL
GROUP BY
    c.country,
    o.battle;
```

### 73.2
```sql
WITH BSC (battle, country) AS (
    SELECT
        o.battle,
        c.country
    FROM
        Outcomes o
        LEFT JOIN Ships s ON s.name = o.ship
        INNER JOIN Classes c ON c.class = ISNULL(s.class, o.ship)
)
SELECT
    DISTINCT country,
    b.name
FROM
    Battles b
    CROSS JOIN (
        SELECT
            DISTINCT country
        FROM
            Classes c
    ) t
EXCEPT
SELECT
    country,
    battle AS name
FROM
    BSC;
```

## 74
Вывести классы всех кораблей России (Russia). Если в базе данных нет классов кораблей России, вывести классы для всех имеющихся в БД стран.
- Вывод: страна, класс
### 74.1
```sql
SELECT
    c.country,
    c.class
FROM
    classes c
WHERE
    UPPER(c.country) = 'RUSSIA'
    AND EXISTS (
        SELECT
            c.country,
            c.class
        FROM
            classes c
        WHERE
            UPPER(c.country) = 'RUSSIA'
    )
UNION
ALL
SELECT
    c.country,
    c.class
FROM
    classes c
WHERE
    NOT EXISTS (
        SELECT
            c.country,
            c.class
        FROM
            classes c
        WHERE
            UPPER(c.country) = 'RUSSIA'
    );
```

### 74.2
```sql
SELECT
    country,
    class
FROM
    (
        SELECT
            class,
            country,
            CASE
                WHEN 'Russia' = ANY (
                    SELECT
                        country
                    FROM
                        classes
                    WHERE
                        country = 'Russia'
                ) THEN 'Russia'
                ELSE country
            END Check_Russia
        FROM
            Classes
    ) a
WHERE
    country = Check_Russia;
```

### 74.3
```sql
SELECT
    country,
    class
FROM
    classes
WHERE
    'Russia' <> ALL (
        SELECT
            country
        FROM
            classes
    )
UNION
SELECT
    country,
    class
FROM
    classes
WHERE
    country = 'Russia';
```

### 74.4
```sql
SELECT
    country,
    class
FROM
    classes
WHERE
    country = 'Russia'
    OR 'Russia' NOT IN (
        SELECT
            country
        FROM
            classes
    );
```

## 75
Для тех производителей, у которых есть продукты с известной ценой хотя бы в одной из таблиц Laptop, PC, Printer найти максимальные цены на каждый из типов продукции.
- Вывод: maker, максимальная цена на ноутбуки, максимальная цена на ПК, максимальная цена на принтеры.
- Для отсутствующих продуктов/цен использовать NULL.
### 75.1
```sql
WITH new AS (
    SELECT
        COALESCE(xPC.maker, xPr.maker, xLa.maker) AS maker,
        xPC.price AS xPC_price,
        xPr.price AS xPr_price,
        xLa.price AS xLa_price
    FROM
        (
            SELECT
                DISTINCT maker
            FROM
                Product
        ) AS TMP
        LEFT JOIN (
            SELECT
                Product.maker,
                temp2.price
            FROM
                Product
                JOIN PC AS temp2 ON temp2.model = product.model
        ) AS xPC ON xPC.maker = TMP.maker
        LEFT JOIN (
            SELECT
                Product.maker,
                temp2.price
            FROM
                Product
                JOIN Printer AS temp2 ON temp2.model = product.model
        ) AS xPr ON xPr.maker = TMP.maker
        LEFT JOIN (
            SELECT
                Product.maker,
                temp2.price
            FROM
                Product
                JOIN Laptop AS temp2 ON temp2.model = product.model
        ) AS xLA ON xLA.maker = TMP.maker
)
SELECT
    maker,
    max(xLa_price),
    max(xPC_price),
    max(xPr_price)
FROM
    new
GROUP BY
    maker
HAVING
    COALESCE(
        max(xLa_price),
        max(xPC_price),
        max(xPr_price),
        0
    ) != 0;
```

### 75.2
```sql
SELECT
    maker,
    MAX(Laptop.price),
    MAX(PC.price),
    MAX(Printer.price)
FROM
    Product
    LEFT JOIN Laptop ON Product.model = Laptop.model
    LEFT JOIN PC ON Product.model = PC.model
    LEFT JOIN Printer ON Product.model = Printer.model
WHERE
    COALESCE(Laptop.price, PC.price, Printer.price) IS NOT NULL
GROUP BY
    maker;
```

### 75.3
```sql
SELECT
    maker,
    [laptop],
    [pc],
    [printer]
FROM
    (
        SELECT
            maker,
            TYPE,
            MAX (price)(price) price
        FROM
            Product p
            JOIN (
                SELECT
                    model,
                    price
                FROM
                    PC
                UNION
                SELECT
                    model,
                    price
                FROM
                    Laptop
                UNION
                SELECT
                    model,
                    price
                FROM
                    Printer
            ) W ON p.model = w.model
        GROUP BY
            maker,
            TYPE
        HAVING
            MAX (price) IS NOT NULL
    ) A PIVOT (
        MAX (price) FOR TYPE IN ([laptop], [pc], [printer])
    ) pvt;
```

### 75.4
```sql
WITH x AS (
    SELECT
        model,
        price
    FROM
        PC
    UNION
    ALL
    SELECT
        model,
        price
    FROM
        Laptop
    UNION
    ALL
    SELECT
        model,
        price
    FROM
        Printer
),
y AS (
    SELECT
        p.maker,
        p.type,
        x.price
    FROM
        product AS p
        INNER JOIN x ON p.model = x.model
)
SELECT
    piv.maker,
    piv.laptop,
    piv.PC,
    piv.printer
FROM
    y pivot(max(y.price) FOR y.type IN (Laptop, PC, Printer)) AS piv
WHERE
    coalesce(piv.laptop, piv.PC, piv.printer) IS NOT NULL;
```

## 76
Определить время, проведенное в полетах, для пассажиров, летавших всегда на разных местах.
- Вывод: имя пассажира, время в минутах.
```sql
WITH cte AS (
    SELECT
        ROW_NUMBER() OVER (
            PARTITION BY ps.ID_psg,
            pit.place
            ORDER BY
                pit.date
        ) AS rowNumber,
        DATEDIFF (
            MINUTE,
            time_out,
            DATEADD(DAY, IIF(time_in < time_out, 1, 0), time_in)
        ) AS timeFlight,
        ps.Id_psg,
        ps.name
    FROM
        Pass_in_trip pit
        LEFT JOIN trip tr ON pit.trip_no = tr.trip_no
        LEFT JOIN Passenger ps ON ps.ID_psg = pit.ID_psg -- Все рейсы
)
SELECT
    MAX(cte.name),
    SUM(timeFlight)
FROM
    cte
GROUP BY
    cte.ID_psg
HAVING
    MAX(rowNumber) = 1;
```

## 77
Определить дни, когда было выполнено максимальное число рейсов из Ростова ('Rostov').
- Вывод: число рейсов, дата.
### 77.1
```sql
SELECT
    TOP 1 WITH TIES *
FROM
    (
        SELECT
            count(DISTINCT(pt.trip_no)) qty,
            pt.date
        FROM
            Trip t,
            Pass_in_trip pt
        WHERE
            t.trip_no = pt.trip_no
            AND t.town_from = 'Rostov'
        GROUP BY
            pt.date
    ) t1
ORDER BY
    t1.qty DESC;
```

### 77.2
```sql
SELECT
    TOP 1 WITH TIES COUNT(DISTINCT trip_no) AS c,
    date
FROM
    Trip
    CROSS APPLY(
        SELECT
            date
        FROM
            Pass_in_trip
        WHERE
            trip_no = Trip.trip_no
    ) AS ca
WHERE
    town_from = 'Rostov'
GROUP BY
    date
ORDER BY
    COUNT(DISTINCT trip_no) DESC;
```

### 77.3
```sql
SELECT
    top(1) WITH ties count(DISTINCT t.trip_no),
    date
FROM
    trip t
    INNER JOIN pass_in_trip pit ON pit.trip_no = t.trip_no
WHERE
    town_from = 'Rostov'
GROUP BY
    date
ORDER BY
    count(DISTINCT t.trip_no) DESC;
```

## 78
Для каждого сражения определить первый и последний день месяца, в котором оно состоялось.
- Вывод: сражение, первый день месяца, последний день месяца.
- Замечание: даты представить без времени в формате "yyyy-mm-dd".
### 78.1
```sql
SELECT
    name,
    REPLACE(
        CONVERT(
            CHAR(12),
            DATEADD(m, DATEDIFF(m, 0, date), 0),
            102
        ),
        '.',
        '-'
    ) AS first_day,
    REPLACE(
        CONVERT(
            CHAR(12),
            DATEADD(s, -1, DATEADD(m, DATEDIFF(m, 0, date) + 1, 0)),
            102
        ),
        '.',
        '-'
    ) AS last_day
FROM
    Battles;
```

### 78.2
```sql
SELECT
    name,
    CAST(DATEADD(DAY, - DAY(date) + 1, date) AS date),
    EOMONTH(date)
FROM
    Battles;
```

## 79
Определить пассажиров, которые больше других времени провели в полетах.
- Вывод: имя пассажира, общее время в минутах, проведенное в полетах
### 79.1
```sql
WITH a AS(
    SELECT
        id_psg,
        sum(
            datediff(
                mi,
                time_out,
                CASE
                    WHEN time_out > time_in THEN dateadd(DAY, 1, time_in)
                    ELSE time_in
                END
            )
        ) mnts
    FROM
        trip t
        INNER JOIN pass_in_trip pit ON t.trip_no = pit.trip_no
    GROUP BY
        id_psg
)
SELECT
    top(1) WITH ties name,
    mnts
FROM
    a
    INNER JOIN passenger p ON a.id_psg = p.id_psg
ORDER BY
    mnts DESC;
```

### 79.2
```sql
WITH t AS (
    SELECT
        p.name,
        SUM(x.m) AS minutes
    FROM
        Passenger p
        JOIN (
            SELECT
                pt.ID_psg,
                DATEDIFF(
                    mi,
                    pt.date + trip.time_out,
                    (
                        CASE
                            WHEN trip.time_in < trip.time_out THEN DATEADD(d, 1, pt.date) + trip.time_in
                            ELSE pt.date + trip.time_in
                        END
                    )
                ) AS m
            FROM
                Pass_in_trip pt
                JOIN trip ON trip.trip_no = pt.trip_no
        ) X ON x.ID_psg = p.ID_psg
    GROUP BY
        p.name,
        p.id_psg
)
SELECT
    TOP 1 WITH TIES *
FROM
    t
ORDER BY
    minutes DESC;
```

### 79.3
```sql
SELECT
    top 1 WITH ties dbo.Passenger.name,
    sum(
        datediff(
            MINUTE,
            [time_out],
            iif(
                [time_in] >= [time_out],
                [time_in],
                dateadd(DAY, 1, [time_in])
            )
        )
    ) AS tit
FROM
    pass_in_trip
    JOIN trip ON Trip.trip_no = Pass_in_trip.trip_no
    JOIN dbo.Passenger ON Passenger.ID_psg = Pass_in_trip.ID_psg
GROUP BY
    dbo.Passenger.ID_psg,
    dbo.Passenger.name
ORDER BY
    sum(
        datediff(
            MINUTE,
            [time_out],
            iif(
                [time_in] >= [time_out],
                [time_in],
                dateadd(DAY, 1, [time_in])
            )
        )
    ) DESC;
```

### 79.4
```sql
WITH t1 AS (
    SELECT
        dbo.Passenger.name,
        sum(
            datediff(
                MINUTE,
                [time_out],
                iif(
                    [time_in] >= [time_out],
                    [time_in],
                    dateadd(DAY, 1, [time_in])
                )
            )
        ) AS tit
    FROM
        pass_in_trip
        JOIN trip ON Trip.trip_no = Pass_in_trip.trip_no
        JOIN dbo.Passenger ON Passenger.ID_psg = Pass_in_trip.ID_psg
    GROUP BY
        dbo.Passenger.ID_psg,
        dbo.Passenger.name
)
SELECT
    top 1 WITH ties name,
    tit
FROM
    t1
ORDER BY
    tit DESC;
```

## 80
Найти производителей любой компьютерной техники, у которых нет моделей ПК, не представленных в таблице PC.
### 80.1
```sql
SELECT
    DISTINCT maker
FROM
    product
WHERE
    maker NOT IN (
        SELECT
            maker
        FROM
            product
        WHERE
            TYPE = 'PC'
            AND model NOT IN (
                SELECT
                    model
                FROM
                    PC
            )
    );
```

### 80.2
```sql
SELECT
    maker
FROM
    Product p
    LEFT JOIN PC t ON p.model = t.model
GROUP BY
    maker
HAVING
    COUNT(
        CASE
            WHEN TYPE = 'PC'
            AND t.model IS NULL THEN 1
            ELSE NULL
        END
    ) = 0;
```

## 81
Из таблицы Outcome получить все записи за тот месяц (месяцы), с учетом года, в котором суммарное значение расхода (out) было максимальным.
### 81.1
```sql
SELECT
    O.*
FROM
    outcome O
    INNER JOIN (
        SELECT
            TOP 1 WITH TIES YEAR(date) AS Y,
            MONTH(date) AS M,
            SUM(out) AS ALL_TOTAL
        FROM
            outcome
        GROUP BY
            YEAR(date),
            MONTH(date)
        ORDER BY
            ALL_TOTAL DESC
    ) R ON YEAR(O.date) = R.Y
    AND MONTH(O.date) = R.M;
```

### 81.2
```sql
SELECT
    *
FROM
    outcome
WHERE
    CONVERT(char(5), date, 11) IN (
        SELECT
            dd
        FROM
            (
                SELECT
                    top 1 WITH ties CONVERT(char(5), date, 11) dd,
                    sum(out) ss
                FROM
                    outcome
                GROUP BY
                    CONVERT(char(5), date, 11)
                ORDER BY
                    ss DESC
            ) abd
    );
```
## 82
В наборе записей из таблицы PC, отсортированном по столбцу code (по возрастанию) найти среднее значение цены для каждой шестерки подряд идущих ПК.
- Вывод: значение code, которое является первым в наборе из шести строк, среднее значение цены в наборе.
### 82.1
```sql
WITH CTE(code, price, number) AS (
    SELECT
        PC.code,
        PC.price,
        number = ROW_NUMBER() OVER (
            ORDER BY
                PC.code
        )
    FROM
        PC
)
SELECT
    CTE.code,
    AVG(C.price)
FROM
    CTE
    JOIN CTE C ON (C.number - CTE.number) < 6
    AND (C.number - CTE.number) >= 0
GROUP BY
    CTE.number,
    CTE.code
HAVING
    COUNT(CTE.number) = 6;
```

### 82.2
```sql
SELECT
    top (
        (
            SELECT
                count(code)
            FROM
                pc
        ) - 5
    ) code,
    avg(price) over (
        ORDER BY
            code ROWS BETWEEN current ROW
            AND 5 following
    ) AS 'avgprc'
FROM
    pc;
```

## 83
Определить названия всех кораблей из таблицы Ships, которые удовлетворяют, по крайней мере, комбинации любых четырёх критериев из следующего списка:
- numGuns = 8
- bore = 15
- displacement = 32000
- type = bb
- launched = 1915
- class=Kongo
- country=USA
### 83.1
```sql
SELECT
    s.name
FROM
    Classes c
    INNER JOIN Ships s ON c.class = s.class
WHERE
    CASE
        WHEN c.numGuns = 8 THEN 1
        ELSE 0
    END + CASE
        WHEN c.bore = 15 THEN 1
        ELSE 0
    END + CASE
        WHEN c.displacement = 32000 THEN 1
        ELSE 0
    END + CASE
        WHEN c.type = 'bb' THEN 1
        ELSE 0
    END + CASE
        WHEN s.launched = 1915 THEN 1
        ELSE 0
    END + CASE
        WHEN s.class = 'Kongo' THEN 1
        ELSE 0
    END + CASE
        WHEN c.country = 'USA' THEN 1
        ELSE 0
    END > = 4;
```

### 83.2
```sql
SELECT
    s.name
FROM
    Ships AS s
    INNER JOIN Classes AS c ON c.class = s.class
WHERE
    IIF(c.numGuns = 8, 1, 0) + IIF(c.bore = 15, 1, 0) + IIF(c.displacement = 32000, 1, 0) + IIF(c.type = 'bb', 1, 0) + IIF(s.launched = 1915, 1, 0) + IIF(s.class = 'Kongo', 1, 0) + IIF(c.country = 'USA', 1, 0) >= 4;
```

## 84
Для каждой компании подсчитать количество перевезенных пассажиров (если они были в этом месяце) по декадам апреля 2003. При этом учитывать только дату вылета.
- Вывод: название компании, количество пассажиров за каждую декаду
### 84.1
```sql
SELECT
    C.name,
    A.N_1_10,
    A.N_11_21,
    A.N_21_30
FROM
    (
        SELECT
            T.ID_comp,
            SUM(
                CASE
                    WHEN DAY(P.date) < 11 THEN 1
                    ELSE 0
                END
            ) AS N_1_10,
            SUM(
                CASE
                    WHEN (
                        DAY(P.date) > 10
                        AND DAY(P.date) < 21
                    ) THEN 1
                    ELSE 0
                END
            ) AS N_11_21,
            SUM(
                CASE
                    WHEN DAY(P.date) > 20 THEN 1
                    ELSE 0
                END
            ) AS N_21_30
        FROM
            Trip AS T
            JOIN Pass_in_trip AS P ON T.trip_no = P.trip_no
            AND CONVERT(char(6), P.date, 112) = '200304'
        GROUP BY
            T.ID_comp
    ) AS A
    JOIN Company AS C ON A.ID_comp = C.ID_comp;
```

### 84.2
```sql
SELECT
    name,
    SUM(
        CASE
            WHEN YEAR (date) = 2003
            AND MONTH(date) = 4
            AND DAY(date) BETWEEN 1
            AND 10 THEN 1
            ELSE 0
        END
    ) d1,
    SUM(
        CASE
            WHEN YEAR (date) = 2003
            AND MONTH(date) = 4
            AND DAY(date) BETWEEN 11
            AND 20 THEN 1
            ELSE 0
        END
    ) d2,
    SUM(
        CASE
            WHEN YEAR (date) = 2003
            AND MONTH(date) = 4
            AND DAY(date) BETWEEN 21
            AND 31 THEN 1
            ELSE 0
        END
    ) d3
FROM
    company c
    JOIN trip t ON t.id_comp = c.id_comp
    JOIN pass_in_trip pit ON pit.trip_no = t.trip_no
WHERE
    YEAR (date) = 2003
    AND MONTH(date) = 4
GROUP BY
    name;
```

### 84.3
```sql
SELECT
    c.name,
    SUM(
        IIF(
            DATEPART(dd, p.date) BETWEEN 1
            AND 10,
            1,
            0
        )
    ) AS '1-10',
    SUM(
        IIF(
            DATEPART(dd, p.date) BETWEEN 11
            AND 20,
            1,
            0
        )
    ) AS '11-20',
    SUM(
        IIF(
            DATEPART(dd, p.date) BETWEEN 21
            AND 30,
            1,
            0
        )
    ) AS '21-30'
FROM
    Pass_in_trip AS p
    INNER JOIN Trip AS t ON t.trip_no = p.trip_no
    INNER JOIN Company AS c ON c.ID_comp = t.id_comp
WHERE
    DATEPART(mm, p.date) = 4
    AND DATEPART(yy, p.date) = 2003
GROUP BY
    c.name;
```

## 85
Найти производителей, которые выпускают только принтеры или только PC.
- При этом искомые производители PC должны выпускать не менее 3 моделей.
```sql
SELECT
    maker
FROM
    product
GROUP BY
    maker
HAVING
    count(DISTINCT TYPE) = 1
    AND (
        min(TYPE) = 'printer'
        OR (
            min(TYPE) = 'pc'
            AND count(model) >= 3
        )
    );
```

## 86
Для каждого производителя перечислить в алфавитном порядке с разделителем "/" все типы выпускаемой им продукции.
- Вывод: maker, список типов продукции
### 86.1
```sql
SELECT
    maker,
    CASE
        count(DISTINCT TYPE)
        WHEN 2 THEN MIN(TYPE) + '/' + MAX(TYPE)
        WHEN 1 THEN MAX(TYPE)
        WHEN 3 THEN 'Laptop/PC/Printer'
    END
FROM
    Product
GROUP BY
    maker;
```

### 86.2
```sql
SELECT
    MAKER,
    STRING_AGG(P.TYPE, '/') TYPES
FROM
    (
        SELECT
            DISTINCT MAKER,
            TYPE
        FROM
            PRODUCT
    ) P
GROUP BY
    MAKER;
```

## 87
Считая, что пункт самого первого вылета пассажира является местом жительства, найти не москвичей, которые прилетали в Москву более одного раза.
- Вывод: имя пассажира, количество полетов в Москву
### 87.1
```sql
SELECT
    DISTINCT name,
    COUNT(town_to) Qty
FROM
    Trip tr
    JOIN Pass_in_trip pit ON tr.trip_no = pit.trip_no
    JOIN Passenger psg ON pit.ID_psg = psg.ID_psg
WHERE
    town_to = 'Moscow'
    AND pit.ID_psg NOT IN(
        SELECT
            DISTINCT ID_psg
        FROM
            Trip tr
            JOIN Pass_in_trip pit ON tr.trip_no = pit.trip_no
        WHERE
            date + time_out = (
                SELECT
                    MIN (date + time_out)
                FROM
                    Trip tr1
                    JOIN Pass_in_trip pit1 ON tr1.trip_no = pit1.trip_no
                WHERE
                    pit.ID_psg = pit1.ID_psg
            )
            AND town_from = 'Moscow'
    )
GROUP BY
    pit.ID_psg,
    name
HAVING
    COUNT(town_to) > 1;
```

### 87.2
```sql
SELECT
    MIN(p.name) name,
    SUM(
        CASE
            WHEN t.town_to = 'Moscow' THEN 1
            ELSE 0
        END
    ) to_msk_qty
FROM
    Pass_in_trip i
    JOIN Trip t ON i.trip_no = t.trip_no
    JOIN Passenger p ON i.ID_psg = p.ID_psg
GROUP BY
    i.ID_psg
HAVING
    MIN(i.date + t.time_out) <> MIN(
        CASE
            WHEN t.town_from = 'Moscow' THEN i.date + t.time_out
            ELSE '3000-12-31 23:59:59'
        END
    )
    AND SUM(
        CASE
            WHEN t.town_to = 'Moscow' THEN 1
            ELSE 0
        END
    ) > 1;
```

## 88
Среди тех, кто пользуется услугами только одной компании, определить имена разных пассажиров, летавших чаще других.
- Вывести: имя пассажира, число полетов и название компании.
### 88.1
```sql
SELECT
    (
        SELECT
            name
        FROM
            Passenger
        WHERE
            ID_psg = B.ID_psg
    ) AS name,
    B.trip_Qty,
    (
        SELECT
            name
        FROM
            Company
        WHERE
            ID_comp = B.ID_comp
    ) AS Company
FROM
    (
        SELECT
            P.ID_psg,
            MIN(T.ID_comp) AS ID_comp,
            COUNT(*) AS trip_Qty,
            MAX(COUNT(*)) OVER() AS Max_Qty
        FROM
            Pass_in_trip AS P
            JOIN Trip AS T ON P.trip_no = T.trip_no
        GROUP BY
            P.ID_psg
        HAVING
            MIN(T.ID_comp) = MAX(T.ID_comp)
    ) AS B
WHERE
    B.trip_Qty = B.Max_Qty;
```

### 88.2
```sql
SELECT
    (
        SELECT
            name
        FROM
            passenger
        WHERE
            id_psg = t.id_psg
    ) name,
    t.qty_trip,
    (
        SELECT
            name
        FROM
            company
        WHERE
            id_comp = t.id_comp
    ) comp_name
FROM
    (
        SELECT
            top 1 WITH ties pit.id_psg,
            count(*) qty_trip,
            min(id_comp) id_comp
        FROM
            pass_in_trip pit
            JOIN trip tr ON (pit.trip_no = tr.trip_no)
        GROUP BY
            pit.id_psg
        HAVING
            count(DISTINCT id_comp) = 1
        ORDER BY
            count(*) DESC
    ) t;
```

### 88.3
```sql
SELECT
    TOP (1) WITH TIES p.name,
    count(*) trip_Qty,
    max(c.name) Company
FROM
    [dbo].[Trip] t
    JOIN [dbo].[Pass_in_trip] pt ON t.trip_no = pt.trip_no
    JOIN [dbo].[Passenger] p ON pt.ID_psg = p.ID_psg
    JOIN [dbo].[Company] c ON t.ID_comp = c.ID_comp
GROUP BY
    p.ID_psg,
    p.name
HAVING
    max(c.name) = min(c.name)
ORDER BY
    trip_Qty DESC;
```

## 89
Найти производителей, у которых больше всего моделей в таблице Product, а также тех, у которых меньше всего моделей.
- Вывод: maker, число моделей
### 89.1
```sql
SELECT
    Maker,
    count(DISTINCT model) Qty
FROM
    Product
GROUP BY
    maker
HAVING
    count(DISTINCT model) >= ALL (
        SELECT
            count(DISTINCT model)
        FROM
            Product
        GROUP BY
            maker
    )
    OR count(DISTINCT model) <= ALL (
        SELECT
            count(DISTINCT model)
        FROM
            Product
        GROUP BY
            maker
    );
```

### 89.2
```sql
WITH maker_models_cnt AS(
    SELECT
        maker,
        COUNT(model) AS models_n
    FROM
        Product
    GROUP BY
        maker
)
SELECT
    maker,
    models_n
FROM
    maker_models_cnt
WHERE
    models_n = (
        SELECT
            MAX(models_n)
        FROM
            maker_models_cnt
    )
    OR models_n = (
        SELECT
            MIN(models_n)
        FROM
            maker_models_cnt
    );
```

### 89.3
```sql
WITH a AS (
    SELECT
        maker,
        count(*) AS cnt,
        max(count(model)) over (PARTITION by 1) AS maxcol,
        min(count(model)) over (PARTITION by 1) AS mincol
    FROM
        Product
    GROUP BY
        maker
)
SELECT
    maker,
    cnt
FROM
    a
WHERE
    cnt = maxcol
    OR cnt = mincol;
```

## 90
Вывести все строки из таблицы Product, кроме трех строк с наименьшими номерами моделей и трех строк с наибольшими номерами моделей.
### 90.1
```sql
SELECT
    t1.maker,
    t1.model,
    t1.type
FROM
    (
        SELECT
            row_number() over (
                ORDER BY
                    model
            ) p1,
            row_number() over (
                ORDER BY
                    model DESC
            ) p2,
            *
        FROM
            product
    ) t1
WHERE
    p1 > 3
    AND p2 > 3;
```

### 90.2
```sql
WITH t AS (
    SELECT
        DENSE_RANK() OVER(
            ORDER BY
                model
        ) AS rn1,
        DENSE_RANK() OVER(
            ORDER BY
                model DESC
        ) AS rn2,
        p.*
    FROM
        Product p
)
SELECT
    maker,
    model,
    TYPE
FROM
    t
WHERE
    rn1 > 3
    AND rn2 > 3;
```

### 90.3
```sql
SELECT
    *
FROM
    product
WHERE
    model NOT IN (
        SELECT
            top 3 WITH ties model
        FROM
            product
        ORDER BY
            model DESC
    )
    AND model NOT IN (
        SELECT
            top 3 WITH ties model
        FROM
            product
        ORDER BY
            model ASC
    );
```

### 90.4
```sql
SELECT
    *
FROM
    Product
ORDER BY
    model OFFSET 3 ROWS FETCH NEXT (
        SELECT
            COUNT(model) - 6
        FROM
            Product
    ) ROWS ONLY;
```

## 91
C точностью до двух десятичных знаков определить среднее количество краски на квадрате.
### 91.1
```sql
SELECT
    CAST(
        1.0 * CASE
            WHEN (
                SELECT
                    Sum(B_VOL)
                FROM
                    utB
            ) IS NULL THEN 0
            ELSE (
                SELECT
                    Sum(B_VOL)
                FROM
                    utB
            )
        END / (
            SELECT
                count(*)
            FROM
                utQ
        ) AS NUMERIC(6, 2)
    ) avg_paint;
```

### 91.2
```sql
SELECT
    CAST(
        AVG(CAST(sumC AS NUMERIC(6, 2))) AS NUMERIC(6, 2)
    )
FROM
    (
        SELECT
            COALESCE(SUM(B_VOL), 0) sumC
        FROM
            utB
            RIGHT JOIN utQ ON utQ.Q_ID = utB.B_Q_ID
        GROUP BY
            utQ.Q_ID
    ) X;
```

### 91.3
```sql
SELECT
    DISTINCT ROUND(
        SUM(SUM(NVL(B_VOL, 0))) OVER() / COUNT(Q_ID) OVER(),
        2
    ) AS "^_^"
FROM
    utB t1
    RIGHT JOIN utQ t2 ON t1.B_Q_ID = t2.Q_ID
GROUP BY
    t2.Q_ID;
```

### 91.4
```sql
SELECT
    CONVERT(
        numeric(10, 2),
        ISNULL(SUM(B_VOL), 0) * 1.0 /(
            SELECT
                COUNT(*)
            FROM
                utQ
        )
    )
FROM
    utB;
```

### 91.5
```sql
SELECT
    CAST(
        1.0 * ISNULL(
            (
                SELECT
                    SUM(B_VOL)
                FROM
                    utB
            ),
            0
        ) /(
            SELECT
                COUNT(*)
            FROM
                UTQ
        ) AS NUMERIC(10, 2)
    ) AVG_QTY;
```

## 92
Выбрать все белые квадраты, которые окрашивались только из баллончиков, пустых к настоящему времени. Вывести имя квадрата
### 92.1
```sql
SELECT
    Q_NAME
FROM
    utQ
WHERE
    Q_ID IN (
        SELECT
            DISTINCT B.B_Q_ID
        FROM
            (
                SELECT
                    B_Q_ID
                FROM
                    utB
                GROUP BY
                    B_Q_ID
                HAVING
                    SUM(B_VOL) = 765
            ) AS B
        WHERE
            B.B_Q_ID NOT IN (
                SELECT
                    B_Q_ID
                FROM
                    utB
                WHERE
                    B_V_ID IN (
                        SELECT
                            B_V_ID
                        FROM
                            utB
                        GROUP BY
                            B_V_ID
                        HAVING
                            SUM(B_VOL) < 255
                    )
            )
    );
```

### 92.2
```sql
SELECT
    [Q_NAME]
FROM
    [dbo].[utQ]
WHERE
    [Q_ID] IN (
        SELECT
            [B_Q_ID]
        FROM
            [dbo].[utB]
        WHERE
            [B_V_ID] IN (
                SELECT
                    [B_V_ID]
                FROM
                    [dbo].[utB]
                GROUP BY
                    [B_V_ID]
                HAVING
                    sum([B_VOL]) = 255
            )
        GROUP BY
            [B_Q_ID]
        HAVING
            sum([B_VOL]) = 765
    );
```

## 93
Для каждой компании, перевозившей пассажиров, подсчитать время, которое провели в полете самолеты с пассажирами.
- Вывод: название компании, время в минутах.
### 93.1
```sql
SELECT
    c.name,
    sum(vr.vr)
FROM
    (
        SELECT
            DISTINCT t.id_comp,
            pt.trip_no,
            pt.date,
            t.time_out,
            t.time_in,
            --pt.id_psg,
            CASE
                WHEN DATEDIFF(mi, t.time_out, t.time_in) > 0 THEN DATEDIFF(mi, t.time_out, t.time_in)
                WHEN DATEDIFF(mi, t.time_out, t.time_in) <= 0 THEN DATEDIFF(mi, t.time_out, t.time_in + 1)
            END vr
        FROM
            pass_in_trip pt
            LEFT JOIN trip t ON pt.trip_no = t.trip_no
    ) vr
    LEFT JOIN company c ON vr.id_comp = c.id_comp
GROUP BY
    c.name;
```

### 93.2
```sql
SELECT
    (
        SELECT
            name
        FROM
            Company
        WHERE
            Company.ID_comp = X.id_c
    ),
    SUM(sum_mi)
FROM
    (
        SELECT
            DISTINCT Trip.ID_comp id_c,
            Trip.trip_no tr_n,
            Pass_in_trip.date dt,
            CASE
                WHEN Trip.time_out < Trip.time_in THEN DATEDIFF(mi, Trip.time_out, Trip.time_in)
                ELSE 24 * 60 - DATEDIFF(mi, Trip.time_in, Trip.time_out)
            END sum_mi
        FROM
            Trip
            INNER JOIN Pass_in_trip ON Pass_in_trip.trip_no = Trip.trip_no
    ) X
GROUP BY
    id_c;
```

### 93.3
```sql
SELECT
    name,
    sum(time)
FROM
    (
        SELECT
            DISTINCT company.name,
            date,
            trip.trip_no,
            CASE
                WHEN time_in > time_out THEN DATEDIFF(mi, time_out, time_in)
                ELSE DATEDIFF(mi, time_out, time_in) + 1440
            END 'time'
        FROM
            trip
            INNER JOIN pass_in_trip ON trip.trip_no = pass_in_trip.trip_no
            INNER JOIN company ON trip.id_comp = company.id_comp
    ) T
GROUP BY
    name;
```

## 94
Для семи последовательных дней, начиная от минимальной даты, когда из Ростова было совершено максимальное число рейсов, определить число рейсов из Ростова.
- Вывод: дата, количество рейсов
### 94.1
```sql
SELECT
    DATEADD(DAY, S.Num, D.date) AS Dt,
    (
        SELECT
            COUNT(DISTINCT P.trip_no)
        FROM
            Pass_in_trip P
            JOIN Trip T ON P.trip_no = T.trip_no
            AND T.town_from = 'Rostov'
            AND P.date = DATEADD(DAY, S.Num, D.date)
    ) AS Qty
FROM
    (
        SELECT
            (3 * (x - 1) + y - 1) AS Num
        FROM
            (
                SELECT
                    1 AS x
                UNION
                ALL
                SELECT
                    2
                UNION
                ALL
                SELECT
                    3
            ) AS N1
            CROSS JOIN (
                SELECT
                    1 AS y
                UNION
                ALL
                SELECT
                    2
                UNION
                ALL
                SELECT
                    3
            ) AS N2
        WHERE
            (3 * (x - 1) + y) < 8
    ) AS S,
    (
        SELECT
            MIN(A.date) AS date
        FROM
            (
                SELECT
                    P.date,
                    COUNT(DISTINCT P.trip_no) AS Qty,
                    MAX(COUNT(DISTINCT P.trip_no)) OVER() AS M_Qty
                FROM
                    Pass_in_trip AS P
                    JOIN Trip AS T ON P.trip_no = T.trip_no
                    AND T.town_from = 'Rostov'
                GROUP BY
                    P.date
            ) AS A
        WHERE
            A.Qty = A.M_Qty
    ) AS D;
```

### 94.2
```sql
-- t1 - количество рейсов по датам
-- t2 - максимум рейсов с минимальной датой (1 запись)
-- t3 - генерация дат на неделю вперед от минимальной
-- А дальше просто соединение t3 и t1.
WITH t1 (Qty, date) AS (
    SELECT
        Count(Trip_no),
        date
    FROM
        (
            SELECT
                Pas.Trip_no,
                date
            FROM
                Pass_in_trip Pas
                JOIN Trip T ON Pas.trip_no = T.trip_no
                AND town_from = 'Rostov'
            GROUP BY
                Pas.Trip_no,
                date
        ) X
    GROUP BY
        date
),
t2 (Qty, date) AS (
    SELECT
        TOP 1 Qty,
        date
    FROM
        t1
    ORDER BY
        Qty DESC,
        date
),
t3 (date) AS (
    SELECT
        DATEADD(
            d,
            3 *(a -1) + b,
            (
                SELECT
                    DATEADD(d, -1, date)
                FROM
                    t2
            )
        )
    FROM
        (
            SELECT
                1 a
            UNION
            ALL
            SELECT
                2
            UNION
            ALL
            SELECT
                3
        ) x
        CROSS JOIN (
            SELECT
                1 b
            UNION
            ALL
            SELECT
                2
            UNION
            ALL
            SELECT
                3
        ) y
    WHERE
        3 *(a -1) + b <= 7
)
SELECT
    t3.date,
    isnull(Qty, 0)
FROM
    t3
    LEFT JOIN t1 ON t3.date = t1.date;
```

## 95
На основании информации из таблицы Pass_in_Trip, для каждой авиакомпании определить:
1. количество выполненных перелетов;
2. число использованных типов самолетов;
3. количество перевезенных различных пассажиров;
4. общее число перевезенных компанией пассажиров.
- Вывод: Название компании, 1), 2), 3), 4).
```sql
SELECT
    name,
    COUNT(
        DISTINCT CONVERT(CHAR(24), date) + CONVERT(CHAR(4), Trip.trip_no)
    ),
    COUNT(DISTINCT plane),
    COUNT(DISTINCT ID_psg),
    COUNT(*)
FROM
    Company,
    Pass_in_trip,
    Trip
WHERE
    Company.ID_comp = Trip.ID_comp
    AND Trip.trip_no = Pass_in_trip.trip_no
GROUP BY
    Company.ID_comp,
    name;
```

## 96
При условии, что баллончики с красной краской использовались более одного раза, выбрать из них такие, которыми окрашены квадраты, имеющие голубую компоненту.
- Вывести название баллончика
### 96.1
```sql
WITH r AS (
    SELECT
        v.v_name,
        v.v_id,
        count(
            CASE
                WHEN v_color = 'R' THEN 1
            END
        ) over(PARTITION by v_id) cnt_r,
        count(
            CASE
                WHEN v_color = 'B' THEN 1
            END
        ) over(PARTITION by b_q_id) cnt_b
    FROM
        utV v
        JOIN utB b ON v.v_id = b.b_v_id
)
SELECT
    v_name
FROM
    r
WHERE
    cnt_r > 1
    AND cnt_b > 0
GROUP BY
    v_name;
```

### 96.2
```sql
WITH BlueQ (q_id) AS(
    SELECT
        DISTINCT b_q_id
    FROM
        utB b
        LEFT JOIN utV v ON b.b_v_id = v.v_id
    WHERE
        v_color = 'B'
),
RedV (v_id, name) AS(
    SELECT
        b_v_id,
        max(v.v_name)
    FROM
        utB b
        LEFT JOIN utV v ON b.b_v_id = v.v_id
    WHERE
        v_color = 'R'
    GROUP BY
        b_v_id
    HAVING
        count(*) > 1
)
SELECT
    DISTINCT name --,v_id,min(useV)
FROM
    utB b
    INNER JOIN RedV r ON r.v_id = b.b_v_id
WHERE
    b.b_q_id IN (
        SELECT
            q_id
        FROM
            BlueQ
    );
```

### 96.3
```sql
SELECT
    DISTINCT V_NAME
FROM
    utB
    LEFT JOIN utV ON utB.B_V_ID = utV.V_ID
WHERE
    V_COLOR = 'R'
    AND B_Q_ID IN(
        SELECT
            DISTINCT B_Q_ID
        FROM
            utB
            LEFT JOIN utV ON utB.B_V_ID = utV.V_ID
        WHERE
            utV.V_COLOR = 'B'
    )
    AND (
        SELECT
            COUNT(*)
        FROM
            utB ub
        WHERE
            utB.B_V_ID = ub.B_V_ID
    ) > 1;
```

## 97
Отобрать из таблицы Laptop те строки, для которых выполняется следующее условие: значения из столбцов speed, ram, price, screen возможно расположить таким образом, что каждое последующее значение будет превосходить предыдущее в 2 раза или более.
- Замечание: все известные характеристики ноутбуков больше нуля.
- Вывод: code, speed, ram, price, screen.
### 97.1
```sql
SELECT
    code,
    speed,
    ram,
    price,
    screen
FROM
    laptop
WHERE
    EXISTS (
        SELECT
            1 x
        FROM
            (
                SELECT
                    v,
                    rank() over(
                        ORDER BY
                            v
                    ) rn
                FROM
                    (
                        SELECT
                            cast(speed AS float) sp,
                            cast(ram AS float) rm,
                            cast(price AS float) pr,
                            cast(screen AS float) sc
                    ) l unpivot(v FOR c IN (sp, rm, pr, sc)) u
            ) l pivot(max(v) FOR rn IN ([1], [2], [3], [4])) p
        WHERE
            [1] * 2 <= [2]
            AND [2] * 2 <= [3]
            AND [3] * 2 <= [4]
    );
```

### 97.1
```sql
WITH t (code, value, rnk) AS (
    SELECT
        code,
        value,
        dense_rank() OVER(
            PARTITION BY code
            ORDER BY
                code,
                value
        ) rnk
    FROM
        Laptop
        CROSS APPLY (
            VALUES
                ('speed', speed),
                ('ram', ram),
                ('price', price),
                ('screen', screen)
        ) spec(name, value)
),
SortV AS(
    SELECT
        code,
        [1],
        [2],
        [3],
        [4]
    FROM
        t PIVOT (max(value) FOR rnk IN ([1], [2], [3], [4])) AS pvt
)
SELECT
    r.code,
    speed,
    ram,
    price,
    screen --, [1],[2],[3],[4]
FROM
    SortV r
    INNER JOIN Laptop l ON l.code = r.code
WHERE
    [2] > = [1] * 2.0
    AND [3] > = [2] * 2.0
    AND [4] > = [3] * 2.0;
```

### 97.2
```sql
SELECT
    CODE,
    SPEED,
    RAM,
    PRICE,
    SCREEN
FROM
    LAPTOP
WHERE
    (
        PRICE * 2 <= RAM
        AND RAM * 2 <= SCREEN
        AND SCREEN * 2 <= SPEED
    )
    OR (
        PRICE * 2 <= RAM
        AND RAM * 2 <= SPEED
        AND SPEED * 2 <= SCREEN
    )
    OR (
        PRICE * 2 <= SCREEN
        AND SCREEN * 2 <= RAM
        AND RAM * 2 <= SPEED
    )
    OR (
        PRICE * 2 <= SCREEN
        AND SCREEN * 2 <= SPEED
        AND SPEED * 2 <= RAM
    )
    OR (
        PRICE * 2 <= SPEED
        AND SPEED * 2 <= RAM
        AND RAM * 2 <= SCREEN
    )
    OR (
        PRICE * 2 <= SPEED
        AND SPEED * 2 <= SCREEN
        AND SCREEN * 2 <= RAM
    )
    OR (
        RAM * 2 <= PRICE
        AND PRICE * 2 <= SCREEN
        AND SCREEN * 2 <= SPEED
    )
    OR (
        RAM * 2 <= PRICE
        AND PRICE * 2 <= SPEED
        AND SPEED * 2 <= SCREEN
    )
    OR (
        RAM * 2 <= SCREEN
        AND SCREEN * 2 <= PRICE
        AND PRICE * 2 <= SPEED
    )
    OR (
        RAM * 2 <= SCREEN
        AND SCREEN * 2 <= SPEED
        AND SPEED * 2 <= PRICE
    )
    OR (
        RAM * 2 <= SPEED
        AND SPEED * 2 <= PRICE
        AND PRICE * 2 <= SCREEN
    )
    OR (
        RAM * 2 <= SPEED
        AND SPEED * 2 <= SCREEN
        AND SCREEN * 2 <= PRICE
    )
    OR (
        SCREEN * 2 <= PRICE
        AND PRICE * 2 <= RAM
        AND RAM * 2 <= SPEED
    )
    OR (
        SCREEN * 2 <= PRICE
        AND PRICE * 2 <= SPEED
        AND SPEED * 2 <= RAM
    )
    OR (
        SCREEN * 2 <= RAM
        AND RAM * 2 <= PRICE
        AND PRICE * 2 <= SPEED
    )
    OR (
        SCREEN * 2 <= RAM
        AND RAM * 2 <= SPEED
        AND SPEED * 2 <= PRICE
    )
    OR (
        SCREEN * 2 <= SPEED
        AND SPEED * 2 <= PRICE
        AND PRICE * 2 <= RAM
    )
    OR (
        SCREEN * 2 <= SPEED
        AND SPEED * 2 <= RAM
        AND RAM * 2 <= PRICE
    )
    OR (
        SPEED * 2 <= PRICE
        AND PRICE * 2 <= RAM
        AND RAM * 2 <= SCREEN
    )
    OR (
        SPEED * 2 <= PRICE
        AND PRICE * 2 <= SCREEN
        AND SCREEN * 2 <= RAM
    )
    OR (
        SPEED * 2 <= RAM
        AND RAM * 2 <= PRICE
        AND PRICE * 2 <= SCREEN
    )
    OR (
        SPEED * 2 <= RAM
        AND RAM * 2 <= SCREEN
        AND SCREEN * 2 <= PRICE
    )
    OR (
        SPEED * 2 <= SCREEN
        AND SCREEN * 2 <= PRICE
        AND PRICE * 2 <= RAM
    )
    OR (
        SPEED * 2 <= SCREEN
        AND SCREEN * 2 <= RAM
        AND RAM * 2 <= PRICE
    );
```

## 98
Вывести список ПК, для каждого из которых результат побитовой операции ИЛИ, примененной к двоичным представлениям скорости процессора и объема памяти, содержит последовательность из не менее четырех идущих подряд единичных битов.
- Вывод: код модели, скорость процессора, объем памяти.
```sql
WITH CTE AS (
    SELECT
        1 n,
        cast (0 AS varchar(16)) bit_or,
        code,
        speed,
        ram
    FROM
        PC
    UNION
    ALL
    SELECT
        n * 2,
        cast (CONVERT(bit,(speed | ram) & n) AS varchar(1)) + cast(bit_or AS varchar(15)),
        code,
        speed,
        ram
    FROM
        CTE
    WHERE
        n < 65536
)
SELECT
    code,
    speed,
    ram
FROM
    CTE
WHERE
    n = 65536
    AND CHARINDEX('1111', bit_or) > 0;
```

## 99
Рассматриваются только таблицы Income_o и Outcome_o. Известно, что прихода/расхода денег в воскресенье не бывает.  Для каждой даты прихода денег на каждом из пунктов определить дату инкассации по следующим правилам:
1. Дата инкассации совпадает с датой прихода, если в таблице Outcome_o нет записи о выдаче денег в эту дату на этом пункте.
2. В противном случае - первая возможная дата после даты прихода денег, которая не является воскресеньем и в Outcome_o не отмечена выдача денег сдатчикам вторсырья в эту дату на этом пункте.
- Вывод: пункт, дата прихода денег, дата инкассации.
```sql
SELECT
    point,
    "date" income_date,
    "date" + nvl(
        min(
            CASE
                WHEN diff > cnt THEN cnt
                ELSE NULL
            END
        ),
        max(cnt) + 1
    ) incass_date
FROM
    (
        SELECT
            i.point,
            i."date",
            (trunc(o."date") - trunc(i."date")) diff,
            -- разница дней
            -- количество запрещенных для инкассации дней после прихода и до текущего запрещенного дня
            count(1) over (
                PARTITION by i.point,
                i."date"
                ORDER BY
                    o."date" ROWS BETWEEN unbounded preceding
                    AND current ROW
            ) -1 cnt
        FROM
            income_o i
            JOIN (
                SELECT
                    point,
                    "date",
                    1 disabled
                FROM
                    outcome_o
                UNION
                SELECT
                    point,
                    trunc("date" + 7, 'DAY'),
                    1 disabled
                FROM
                    income_o
            ) o ON i.point = o.point
        WHERE
            o."date" > = i."date"
    )
GROUP BY
    point,
    "date";
```

## 100
Написать запрос, который выводит все операции прихода и расхода из таблиц Income и Outcome в следующем виде: дата, порядковый номер записи за эту дату, пункт прихода, сумма прихода, пункт расхода, сумма расхода.
- При этом все операции прихода по всем пунктам, совершённые в течение одного дня, упорядочены по полю code, и так же все операции расхода упорядочены по полю code.
- В случае, если операций прихода/расхода за один день было не равное количество, выводить NULL в соответствующих колонках на месте недостающих операций.
### 100.1
```sql
SELECT
    DISTINCT A.date,
    A.R,
    B.point,
    B.inc,
    C.point,
    C.out
FROM
    (
        SELECT
            DISTINCT date,
            ROW_Number() OVER(
                PARTITION BY date
                ORDER BY
                    code ASC
            ) AS R
        FROM
            Income
        UNION
        SELECT
            DISTINCT date,
            ROW_Number() OVER(
                PARTITION BY date
                ORDER BY
                    code ASC
            )
        FROM
            Outcome
    ) A
    LEFT JOIN (
        SELECT
            date,
            point,
            inc,
            ROW_Number() OVER(
                PARTITION BY date
                ORDER BY
                    code ASC
            ) AS RI
        FROM
            Income
    ) B ON B.date = A.date
    AND B.RI = A.R
    LEFT JOIN (
        SELECT
            date,
            point,
            out,
            ROW_Number() OVER(
                PARTITION BY date
                ORDER BY
                    code ASC
            ) AS RO
        FROM
            Outcome
    ) C ON C.date = A.date
    AND C.RO = A.R;
```

### 100.2
```sql
SELECT
    coalesce(d1, d2),
    coalesce(rn1, rn2),
    p1,
    inc,
    p2,
    out
FROM
    (
        SELECT
            row_number() over (
                PARTITION by date
                ORDER BY
                    code
            ) rn1,
            code c1,
            point p1,
            date d1,
            inc
        FROM
            income
    ) a FULL
    JOIN (
        SELECT
            row_number() over (
                PARTITION by date
                ORDER BY
                    code
            ) rn2,
            code c2,
            point p2,
            date d2,
            out
        FROM
            outcome
    ) b ON d1 = d2
    AND rn1 = rn2;
```

### 100.3
```sql
SELECT
    coalesce(a.date, b.date),
    coalesce(a.num, b.num),
    a.point,
    inc,
    b.point,
    out
FROM
    (
        SELECT
            date,
            point,
            inc,
            row_number() over(
                PARTITION BY date
                ORDER BY
                    code
            ) num
        FROM
            Income
    ) a FULL
    JOIN (
        SELECT
            date,
            point,
            out,
            row_number() over(
                PARTITION BY date
                ORDER BY
                    code
            ) num
        FROM
            Outcome
    ) b ON (
        a.date = b.date
        AND a.num = b.num
    );
```

## 101
Таблица Printer сортируется по возрастанию поля code.
- Упорядоченные строки составляют группы: первая группа начинается с первой строки, каждая строка со значением color='n' начинает новую группу, группы строк не перекрываются.
- Для каждой группы определить: наибольшее значение поля model (max_model), количество уникальных типов принтеров (distinct_types_cou) и среднюю цену (avg_price).
- Для всех строк таблицы вывести: code, model, color, type, price, max_model, distinct_types_cou, avg_price.
### 101.1
```sql
SELECT
    code,
    model,
    color,
    TYPE,
    price,
    MAX(model) OVER(PARTITION BY Grp) max_model,
    MAX(
        CASE
            TYPE
            WHEN 'Laser' THEN 1
            ELSE 0
        END
    ) OVER(PARTITION BY Grp) + MAX(
        CASE
            TYPE
            WHEN 'Matrix' THEN 1
            ELSE 0
        END
    ) OVER(PARTITION BY Grp) + MAX(
        CASE
            TYPE
            WHEN 'Jet' THEN 1
            ELSE 0
        END
    ) OVER(PARTITION BY Grp) distinct_types,
    AVG(price) OVER(PARTITION BY Grp)
FROM
    (
        SELECT
            *,
            CASE
                color
                WHEN 'n' THEN 0
                ELSE ROW_NUMBER() OVER(
                    ORDER BY
                        code
                )
            END + CASE
                color
                WHEN 'n' THEN 1
                ELSE -1
            END * ROW_NUMBER() OVER(
                PARTITION BY color
                ORDER BY
                    code
            ) Grp
        FROM
            Printer
    ) T;
```

### 101.2
```sql
WITH cte AS (
    SELECT
        *,
        CASE
            color
            WHEN 'n' THEN 0
            ELSE row_number() over(
                ORDER BY
                    code
            )
        END + CASE
            color
            WHEN 'n' THEN 1
            ELSE -1
        END * row_number() over(
            PARTITION by color
            ORDER BY
                code
        ) grp
    FROM
        Printer
),
ctb AS (
    SELECT
        *,
        dense_rank() over(
            PARTITION by grp
            ORDER BY
                TYPE
        ) AS rt
    FROM
        cte
)
SELECT
    [ctb].[code],
    [ctb].[model],
    [ctb].[color],
    [ctb].[type],
    [ctb].[price],
    max(model) over(PARTITION by [ctb].[grp]) [max_model],
    max(rt) over(PARTITION by grp) AS [distinct_types_cou],
    avg(price) over(PARTITION by [ctb].[grp]) [avg_price]
FROM
    ctb;
```

## 102
Определить имена разных пассажиров, которые летали только между двумя городами (туда и/или обратно).
### 102.1
```sql
SELECT
    name
FROM
    passenger
WHERE
    id_psg IN (
        SELECT
            id_psg
        FROM
            trip t,
            pass_in_trip pit
        WHERE
            t.trip_no = pit.trip_no
        GROUP BY
            id_psg
        HAVING
            count(
                DISTINCT CASE
                    WHEN town_from <= town_to THEN town_from + town_to
                    ELSE town_to + town_from
                END
            ) = 1
    );
```

### 102.2
```sql
SELECT
    (
        SELECT
            pas.name
        FROM
            Passenger pas
        WHERE
            pas.ID_psg = t.ID_psg
    )
FROM
    (
        SELECT
            pit.ID_psg
        FROM
            Pass_in_trip pit
            INNER JOIN Trip t ON t.trip_no = pit.trip_no
        GROUP BY
            pit.ID_psg
        HAVING
            count(
                DISTINCT CASE
                    WHEN town_from > town_to THEN concat(town_from, town_to)
                    ELSE concat(town_to, town_from)
                END
            ) = 1
    ) t;
```

## 103
Выбрать три наименьших и три наибольших номера рейса. Вывести их в шести столбцах одной строки, расположив в порядке от наименьшего к наибольшему.
- Замечание: считать, что таблица Trip содержит не менее шести строк.
### 103.1
```sql
SELECT
    min(t.trip_no),
    min(tt.trip_no),
    min(ttt.trip_no),
    max(t.trip_no),
    max(tt.trip_no),
    max(ttt.trip_no)
FROM
    trip t,
    trip tt,
    trip ttt
WHERE
    tt.trip_no > t.trip_no
    AND ttt.trip_no > tt.trip_no;
```

### 103.2
```sql
SELECT
    min(T.Trip_no),
    min(T2.Trip_no),
    min(T3.Trip_no),
    max(T.Trip_no),
    max(T2.Trip_no),
    max(T3.Trip_no)
FROM
    Trip T
    JOIN Trip T2 ON T.Trip_no < T2.Trip_no
    JOIN Trip T3 ON T2.Trip_no < T3.Trip_no;
```

## 104
Для каждого класса крейсеров, число орудий которого известно, пронумеровать (последовательно от единицы) все орудия.
- Вывод: имя класса, номер орудия в формате 'bc-N'.
### 104.1
```sql
WITH a AS(
    SELECT
        x.class,
        x.numGuns,
        row_number() over(
            PARTITION by x.class
            ORDER BY
                x.numguns
        ) n
    FROM
        Classes x,
        classes y
    WHERE
        x.type = 'bc'
)
SELECT
    DISTINCT class,
    'bc-' + cast(n AS char(2))
FROM
    a
WHERE
    numguns >= n;
```

### 104.2
```sql
WITH cte AS (
    SELECT
        class,
        cast(numGuns AS int) ng,
        'bc-' + cast(numGuns AS varchar(4)) num
    FROM
        dbo.Classes
    WHERE
        TYPE = 'bc'
        AND numGuns > 0
    UNION
    ALL
    SELECT
        class,
        ng - 1,
        'bc-' + cast(ng - 1 AS varchar(4))
    FROM
        cte
    WHERE
        ng > 1
)
SELECT
    class,
    num
FROM
    cte
ORDER BY
    class,
    num;
```

### 104.3 PG
```sql
SELECT
    class,
    'bc-' || generate_series(1, numguns) num
FROM
    classes
WHERE
    TYPE = 'bc';
```

## 105
Статистики Алиса, Белла, Вика и Галина нумеруют строки у таблицы Product. Все четверо упорядочили строки таблицы по возрастанию названий производителей. Алиса присваивает новый номер каждой строке, строки одного производителя она упорядочивает по номеру модели. Трое остальных присваивают один и тот же номер всем строкам одного производителя. Белла присваивает номера начиная с единицы, каждый следующий производитель увеличивает номер на 1. У Вики каждый следующий производитель получает такой же номер, какой получила бы первая модель этого производителя у Алисы.  Галина присваивает каждому следующему производителю тот же номер, который получила бы его последняя модель у Алисы.
- Вывести: maker, model, номера строк получившиеся у Алисы, Беллы, Вики и Галины соответственно.
```sql
SELECT
    maker,
    model,
    row_number() over (
        ORDER BY
            maker,
            model
    ),
    dense_rank() over (
        ORDER BY
            maker
    ),
    rank() over (
        ORDER BY
            maker
    ),
    count(*) over (
        ORDER BY
            maker
    )
FROM
    product;
```

## 106
Пусть v1, v2, v3, v4, ... представляет последовательность вещественных чисел - объемов окрасок b_vol, упорядоченных по возрастанию b_datetime, b_q_id, b_v_id.
- Найти преобразованную последовательность P1=v1, P2=v1/v2, P3=v1/v2\*v3, P4=v1/v2\*v3/v4, ..., где каждый следующий член получается из предыдущего умножением на vi (при нечетных i) или делением на vi (при четных i).
- Результаты представить в виде b_datetime, b_q_id, b_v_id, b_vol, Pi, где Pi - член последовательности, соответствующий номеру записи i. Вывести Pi с 8-ю знаками после запятой.
### 106.1
```sql
WITH a AS(
    SELECT
        *,
        row_number() over(
            ORDER BY
                b_datetime,
                b_q_id,
                b_v_id
        ) n
    FROM
        utb
)
SELECT
    b_datetime,
    b_q_id,
    b_v_id,
    b_vol,
    cast(exp(sm1) / exp(sm2) AS numeric(12, 8)) k
FROM
    a x
    CROSS APPLY (
        SELECT
            sum(iif(n % 2 <> 0, log(b_vol), 0)) sm1,
            sum(iif(n % 2 = 0, log(b_vol), 0)) sm2
        FROM
            a
        WHERE
            n <= x.n
    ) y;
```

### 106.2
```sql
WITH num_utb AS (
    SELECT
        *,
        ROW_NUMBER () OVER (
            ORDER BY
                b_datetime,
                b_q_id,
                b_v_id
        ) AS num
    FROM
        utb
) -- нумеруем таблицу сквозной нумерацией в указанной последовательности
SELECT
    B_DATETIME,
    B_Q_ID,
    B_V_ID,
    B_VOL,
    CAST (
        EXP (
            SUM (log_b_vol) OVER (
                ORDER BY
                    num ROWS BETWEEN unbounded preceding
                    AND current ROW
            )
        ) -- вычисляем экспоненту суммы логарифмов с начала таблицы до данной строки
        AS decimal (18, 8) -- конвертируем результат в тип decimal (можно в numeric) с точностью 18 и масштабом 8
    ) AS sv
FROM
    (
        SELECT
            *,
            CASE
                WHEN num % 2 = 1 THEN LOG (b_vol) -- нечетное умножаем, логарифм положительный
                ELSE - LOG (b_vol) -- четное делим, логарифм отрицательный
            END AS log_b_vol
        FROM
            num_utb
    ) a;
```

### 106.3
```sql
WITH TEMPTBL0 AS (
    SELECT
        A0.B_DATETIME,
        A0.B_Q_ID,
        A0.B_V_ID,
        A0.B_VOL,
        ROW_NUMBER() OVER(
            ORDER BY
                A0.B_DATETIME,
                A0.B_Q_ID,
                A0.B_V_ID,
                A0.B_VOL
        ) AS I
    FROM
        UTB AS A0
)
SELECT
    A1.B_DATETIME,
    A1.B_Q_ID,
    A1.B_V_ID,
    A1.B_VOL,
    CAST(
        EXP(
            SUM(((I % 2) * 2 - 1) * LOG(A1.B_VOL)) OVER(
                ORDER BY
                    I ROWS UNBOUNDED PRECEDING
            )
        ) AS DEC(16, 8)
    ) AS VS
FROM
    TEMPTBL0 AS A1;
```

## 107
Для пятого по счету пассажира из числа вылетевших из Ростова в апреле 2003 года определить компанию, номер рейса и дату вылета.
- Замечание. Считать, что два рейса одновременно вылететь из Ростова не могут.
### 107.1
```sql
SELECT
    name,
    trip_no,
    date
FROM
    (
        SELECT
            row_number() over(
                ORDER BY
                    date + time_out,
                    ID_psg
            ) rn,
            name,
            Trip.trip_no,
            date
        FROM
            Company,
            Pass_in_trip,
            Trip
        WHERE
            Company.ID_comp = Trip.ID_comp
            AND Trip.trip_no = Pass_in_trip.trip_no
            AND town_from = 'Rostov'
            AND year(date) = 2003
            AND MONTH(date) = 4
    ) _
WHERE
    rn = 5;
```

### 107.2
```sql
SELECT
    (
        SELECT
            name
        FROM
            Company c
        WHERE
            c.ID_Comp = t.ID_comp
    ) name,
    trip_no,
    date
FROM
    (
        SELECT
            t.ID_comp,
            t.trip_no,
            pt.date,
            time_out
        FROM
            (
                (
                    SELECT
                        trip_no,
                        date
                    FROM
                        Pass_in_trip pit
                    WHERE
                        pit.date BETWEEN '2003-04-01'
                        AND '2003-04-30'
                ) pt
                INNER JOIN Trip t ON t.trip_no = pt.trip_no
                AND town_from = 'Rostov'
            )
    ) t
ORDER BY
    date,
    time_out OFFSET 4 ROWS FETCH NEXT 1 ROW ONLY;
```

### 107.3
```sql
SELECT
    C.NAME,
    T.TRIP_NO,
    PT.DATE
FROM
    PASS_IN_TRIP PT
    JOIN TRIP T ON PT.TRIP_NO = T.TRIP_NO
    JOIN COMPANY C ON C.ID_COMP = T.ID_COMP
WHERE
    T.TOWN_FROM = 'Rostov'
    AND PT.DATE - DAY(PT.DATE) + 1 = Cast('4-1-2003' AS datetime)
ORDER BY
    PT.date,
    T.time_out OFFSET 4 ROWS FETCH NEXT 1 ROW ONLY;
```

## 108
Реставрация экспонатов секции "Треугольники" музея ПФАН проводилась согласно техническому заданию. Для каждой записи таблицы utb малярами подкрашивалась сторона любой фигуры, если длина этой стороны равнялась b_vol.
- Найти окрашенные со всех сторон треугольники, кроме равносторонних, равнобедренных и тупоугольных.
- Для каждого треугольника (но без повторений) вывести три значения X, Y, Z, где X - меньшая, Y - средняя, а Z - большая сторона.
### 108.1
```sql
SELECT
    DISTINCT b1.B_VOL,
    b2.b_vol,
    b3.b_vol
FROM
    utb b1,
    utb b2,
    utb b3
WHERE
    b1.B_VOL < b2.B_VOL
    AND b2.B_VOL < b3.B_VOL
    AND NOT (
        b3.B_VOL > SQRT(SQUARE(b1.B_VOL) + SQUARE(b2.B_VOL))
    );
```

### 108.2
```sql
WITH a AS (
    SELECT
        DISTINCT b_vol
    FROM
        utB
)
SELECT
    DISTINCT a1.b_vol,
    a2.b_vol,
    a3.b_vol
FROM
    a AS a1,
    a AS a2,
    a AS a3
WHERE
    a1.b_vol < a2.b_vol
    AND a2.b_vol < a3.b_vol
    AND SQUARE(a3.b_vol) <= SQUARE(a1.b_vol) + SQUARE(a2.b_vol);
```

## 109
Вывести:
1. Названия всех квадратов черного или белого цвета.
2. Общее количество белых квадратов.
3. Общее количество черных квадратов.
### 109.1
```sql
SELECT
    A.Q_NAME AS q_name,
    A.Whites AS Whites,
    A.Cnt - A.Whites AS Blacks
FROM
    (
        SELECT
            Q.Q_ID,
            Q.Q_NAME,
            (SUM(SUM(B.B_VOL)) OVER()) / 765 AS Whites,
            COUNT(*) OVER() AS Cnt
        FROM
            utQ AS Q
            LEFT JOIN utB AS B ON Q.Q_ID = B.B_Q_ID
        GROUP BY
            Q.Q_ID,
            Q.Q_NAME
        HAVING
            SUM(B.B_VOL) = 765
            OR SUM(B.B_VOL) IS NULL
    ) AS A;
```

### 109.2
```sql
SELECT
    min(q_name) AS name,
    count(ALL min(b_q_id)) over () AS whites,
    count(*) over () - count(ALL min(b_q_id)) over () AS blacks
FROM
    utq
    LEFT JOIN utb ON b_q_id = q_id
GROUP BY
    q_id
HAVING
    min(b_q_id) IS NULL
    OR sum(b_vol) = 765;
```

## 110
Определить имена разных пассажиров, когда-либо летевших рейсом, который вылетел в субботу, а приземлился в воскресенье.
### 110.1
```sql
SELECT
    name
FROM
    passenger
WHERE
    id_psg IN (
        SELECT
            id_psg
        FROM
            pass_in_trip pit
            JOIN trip t ON pit.trip_no = t.trip_no
        WHERE
            time_in < time_out
            AND datepart(dw, date) = 7
    );
```

### 110.2
```sql
SELECT
    p.name
FROM
    passenger p
    JOIN (
        SELECT
            DISTINCT pt.id_psg
        FROM
            pass_in_trip pt
            JOIN trip t ON pt.trip_no = t.trip_no
        WHERE
            datename(weekday, pt.date) = 'Saturday'
            AND t.time_out > t.time_in
    ) a ON a.id_psg = p.id_psg;
```

## 111
Найти НЕ белые и НЕ черные квадраты, которые окрашены разными цветами в пропорции 1:1:1. Вывод: имя квадрата, количество краски одного цвета
### 111.1
```sql
SELECT
    pvt.Q_NAME,
    [R] AS qty
FROM
    (
        SELECT
            Q_ID,
            Q_NAME,
            B_VOL,
            V_COLOR
        FROM
            dbo.utQ
            JOIN dbo.utB ON utB.B_Q_ID = utQ.Q_ID
            JOIN dbo.utV ON utV.V_ID = utB.B_V_ID
    ) t1 pivot(sum(B_VOL) FOR V_COLOR IN ([R], [G], [B])) pvt
WHERE
    [R] = [G]
    AND [R] = [B]
    AND [R] > 0
    AND [R] < 255;
```

### 111.2
```sql
WITH Nbw AS (
    SELECT
        b_q_id,
        sum(iif(v.v_color = 'R', isnull(b_vol, 0), 0)) vR,
        sum(iif(v.v_color = 'G', isnull(b_vol, 0), 0)) vG,
        sum(iif(v.v_color = 'B', isnull(b_vol, 0), 0)) vB
    FROM
        utB b
        LEFT JOIN utV v ON v.v_id = b.b_v_id
    GROUP BY
        b_q_id
)
SELECT
    q_name,
    vR AS qty
FROM
    Nbw
    INNER JOIN utQ q ON q.q_id = nbw.b_q_id
WHERE
    (vR <> 255)
    AND vR = vB
    AND vr = vG
    AND vR > 0;
```

## 112
Какое максимальное количество черных квадратов можно было бы окрасить в белый цвет оставшейся краской
```sql
SELECT
    min(Qty)
FROM
    (
        SELECT
            SUM(RemainPaint) / 255 Qty
        FROM
            (
                SELECT
                    V_COLOR,
                    V_ID,
                    CASE
                        WHEN SUM(B_VOL) IS NULL THEN 255
                        ELSE 255 - SUM(B_VOL)
                    END RemainPaint
                FROM
                    utB
                    RIGHT JOIN utV ON B_V_ID = V_ID
                GROUP BY
                    V_COLOR,
                    V_ID
            ) R
        GROUP BY
            V_COLOR
    ) Q;
```

## 113
Сколько каждой краски понадобится, чтобы докрасить все Не белые квадраты до белого цвета.
- Вывод: количество каждой краски в порядке (R,G,B)
### 113.1
```sql
SELECT
    sum(255 - ISNULL ([R], 0)) R,
    sum(255 - isnull([G], 0)) G,
    sum(255 - isnull([B], 0)) B
FROM
    (
        /*merging all tables to find paint filling and color for all squares*/
        SELECT
            ISNULL(B_Q_ID, Q_ID) ID,
            V_COLOR,
            B_VOL Vol
        FROM
            utB
            RIGHT JOIN utQ ON B_Q_ID = Q_ID
            LEFT JOIN utV ON B_V_ID = V_ID
    ) AS SourceT PIVOT (
        /*rotating table and calculating each paint volume for each square*/
        SUM(Vol) FOR V_COLOR IN ([R], [G], [B])
    ) Pvt
    /*excluding white squares*/
WHERE
    ISNULL ([R], 0) + isnull([G], 0) + isnull([B], 0) < 765;
```

### 113.2
```sql
SELECT
    [R] Red,
    [G] Green,
    [B] Blue
FROM
    (
        SELECT
            V_COLOR,
            Q_Qty * 255 - sum_color AS balance_color
        FROM
            (
                SELECT
                    V_COLOR,
                    SUM (B_VOL) sum_color -- подсчёт количества краски каждого цвета на всех окрашенных квадратах
                FROM
                    utB
                    INNER JOIN utV ON V_ID = B_V_ID
                GROUP BY
                    V_COLOR
            ) a
            CROSS JOIN (
                SELECT
                    COUNT(*) Q_Qty -- посчёт количества всех квадратов, в том числе неокрашенных
                FROM
                    utQ
            ) b
    ) g PIVOT (
        MIN (balance_color) FOR V_COLOR IN ([R], [G], [B])
    ) pvt;
```

### 113.3
```sql
WITH CTE AS (
    SELECT
        *
    FROM
        (
            SELECT
                Q_NAME,
                V_COLOR,
                B_VOL
            FROM
                utQ
                LEFT JOIN utB ON utQ.Q_ID = utB.B_Q_ID
                LEFT JOIN utV ON utB.B_V_ID = utV.V_ID
        ) AS Sqrs PIVOT (
            SUM(B_VOL) FOR V_COLOR IN ([B], [G], [R])
        ) AS Pvt
)
SELECT
    SUM(255 - ISNULL(R, 0)) AS R,
    SUM(255 - ISNULL(G, 0)) AS G,
    SUM(255 - ISNULL(B, 0)) AS B
FROM
    CTE;
```

### 113.4
```sql
SELECT
    SUM(C.R) Red,
    SUM(C.G) Green,
    SUM(C.B) Blue
FROM
    (
        SELECT
            Q_ID,
            255 - SUM(
                CASE
                    V_COLOR
                    WHEN 'R' THEN B_VOL
                    ELSE 0
                END
            ) R,
            255 - SUM(
                CASE
                    V_COLOR
                    WHEN 'G' THEN B_VOL
                    ELSE 0
                END
            ) G,
            255 - SUM(
                CASE
                    V_COLOR
                    WHEN 'B' THEN B_VOL
                    ELSE 0
                END
            ) B
        FROM
            utQ Q
            LEFT JOIN utB B ON Q.Q_ID = B.B_Q_ID
            LEFT JOIN utV V ON V.V_ID = B.B_V_ID
        GROUP BY
            Q_ID
    ) C;
```

## 114
Определить имена разных пассажиров, которым чаще других доводилось лететь на одном и том же месте.
- Вывод: имя и количество полетов на одном и том же месте.
### 114.1
```sql
WITH b AS (
    SELECT
        ID_psg,
        COUNT(*) AS cnt
    FROM
        Pass_In_Trip
    GROUP BY
        ID_psg,
        place
),
b1 AS (
    SELECT
        DISTINCT ID_psg,
        cnt
    FROM
        b
    WHERE
        cnt =(
            SELECT
                MAX(cnt)
            FROM
                b
        )
)
SELECT
    name,
    cnt
FROM
    b1
    JOIN Passenger p ON (b1.ID_psg = p.ID_psg);
```

### 114.2
```sql
WITH t1 AS (
    SELECT
        ID_psg,
        count(*) numb
    FROM
        Pass_In_Trip
    GROUP BY
        ID_psg,
        place
),
t2 AS (
    SELECT
        DISTINCT ID_psg,
        numb
    FROM
        t1
    WHERE
        numb = (
            SELECT
                max(numb)
            FROM
                t1
        )
)
SELECT
    name,
    numb
FROM
    t2
    JOIN Passenger p ON t2.ID_psg = p.ID_psg;
```

### 114.3
```sql
SELECT
    TOP 1 WITH TIES pas.[name],
    t.NN
FROM
    (
        SELECT
            DISTINCT pit.ID_psg,
            count(*) 'NN'
        FROM
            Pass_in_trip pit
        GROUP BY
            pit.ID_psg,
            pit.place
    ) t
    JOIN Passenger pas ON t.ID_psg = pas.ID_psg
ORDER BY
    t.NN DESC;
```

### 114.4
```sql
SELECT
    name,
    cnt
FROM
    (
        SELECT
            DISTINCT top 1 WITH ties id_psg,
            count(*) cnt
        FROM
            pass_in_trip
        GROUP BY
            id_psg,
            place
        ORDER BY
            count(*) DESC
    ) a,
    passenger p
WHERE
    p.id_psg = a.id_psg;
```

## 115
Рассмотрим равнобочные трапеции, в каждую из которых можно вписать касающуюся всех сторон окружность. Кроме того, каждая сторона имеет целочисленную длину из множества значений b_vol.
- Вывести результат в 4 колонки: Up, Down, Side, Rad. Здесь Up - меньшее основание, Down - большее основание, Side - длины боковых сторон, Rad – радиус вписанной окружности (с 2-мя знаками после запятой).
```sql
SELECT
    DISTINCT Up = u.b_vol,
    Down = d.b_vol,
    Side = s.b_vol,
    Rad = cast(
        POWER(
            (
                POWER(s.b_vol, 2) - POWER((1.* d.b_vol -1.* u.b_vol) / 2, 2)
            ),
            1./ 2.
        ) / 2 AS dec(15, 2)
    )
FROM
    utB u,
    utB d,
    utB s
WHERE
    u.b_vol < d.b_vol
    AND 1.* u.b_vol + 1.* d.b_vol = 2.* s.b_vol;
```

## 116
Считая, что каждая окраска длится ровно секунду, определить непрерывные интервалы времени с длительностью более 1 секунды из таблицы utB.
- Вывод: дата первой окраски в интервале, дата последней окраски в интервале.
### 116.1
```sql
SELECT
    MIN(D) START,
    MAX(D) finish
FROM
    (
        SELECT
            D,
            SUM(F) OVER(
                ORDER BY
                    D ROWS UNBOUNDED PRECEDING
            ) F
        FROM
            (
                SELECT
                    B_DATETIME D,
                    IIF(
                        IsNull(
                            DATEDIFF(
                                SECOND,
                                LAG(B_DATETIME) OVER(
                                    ORDER BY
                                        B_DATETIME
                                ),
                                B_DATETIME
                            ),
                            0
                        ) <= 1,
                        0,
                        1
                    ) F
                FROM
                    utB
            ) q
    ) q
GROUP BY
    F
HAVING
    DATEDIFF(SECOND, MIN(D), MAX(D)) > 0;
```

### 116.2
```sql
WITH CTime AS(
    SELECT
        b_datetime bt,
        ROW_NUMBER() OVER(
            ORDER BY
                b_datetime
        ) rnk
    FROM
        utB
    GROUP BY
        b_datetime
)
SELECT
    min(bt) date_begin,
    dateadd(SECOND, count(*) -1, min(bt)) date_finish
FROM
    CTime
GROUP BY
    dateadd(SECOND, - rnk, bt)
HAVING
    count(*) > 1;
```

### 116.3
```sql
WITH tmp AS (
    SELECT
        b_datetime,
        dateadd(
            SECOND,
            row_number() over (
                ORDER BY
                    b_datetime DESC
            ),
            b_datetime
        ) AS rn
    FROM
        utb
    GROUP BY
        b_datetime
)
SELECT
    min(b_datetime) AS date_begin,
    max(b_datetime) AS date_finish
FROM
    tmp
GROUP BY
    rn
HAVING
    count(1) > 1;
```

## 117
По таблице Classes для каждой страны найти максимальное значение среди трех выражений: numguns\*5000, bore\*3000, displacement.
- Вывод в три столбца:
    - страна;
    - максимальное значение;
    - слово `numguns` - если максимум достигается для numguns*5000, слово `bore` - если максимум достигается для bore*3000, слово `displacement` - если максимум достигается для displacement.
- Замечание. Если максимум достигается для нескольких выражений, выводить каждое из них отдельной строкой.
```sql
SELECT
    top 1 WITH ties country,
    x,
    n
FROM
    classes
    CROSS APPLY(
        VALUES
            (numguns * 5000, 'numguns'),
            (bore * 3000, 'bore'),
            (displacement, 'displacement')
    ) V(x, n)
GROUP BY
    country,
    x,
    n
ORDER BY
    rank() over(
        PARTITION by country
        ORDER BY
            x DESC
    );
```

## 118
Выборы Директора музея ПФАН проводятся только в високосный год, в первый вторник апреля после первого понедельника апреля.
- Для каждой даты из таблицы Battles определить дату ближайших (после этой даты) выборов Директора музея ПФАН.
- Вывод: сражение, дата сражения, дата выборов. Даты выводить в формате "yyyy-mm-dd".
```sql
NULL
```

## 119
Сгруппировать все окраски по дням, месяцам и годам. Идентификатор каждой группы должен иметь вид "yyyy" для года, "yyyy-mm" для месяца и "yyyy-mm-dd" для дня.
- Вывести только те группы, в которых количество различных моментов времени (b_datetime), когда выполнялась окраска, более 10.
- Вывод: идентификатор группы, суммарное количество потраченной краски.
### 119.1
```sql
SELECT
    DateStr,
    sum(B_VOL) SUMVOL
FROM
    utB b
    CROSS APPLY(
        SELECT
            CONVERT(varchar(10), b.B_DATETIME, 127) DateStr
        UNION
        ALL
        SELECT
            CONVERT(varchar(7), b.B_DATETIME, 127)
        UNION
        ALL
        SELECT
            CONVERT(varchar(4), b.B_DATETIME, 127)
            /**/
    ) d
GROUP BY
    DateStr
HAVING
    count(DISTINCT b.B_DATETIME) > 10;
```

### 119.2
```sql
SELECT
    date,
    sum(b_vol) vol
FROM
    (
        SELECT
            b_vol,
            b_datetime,
            CONVERT(varchar(10), CONVERT(char(10), b_datetime, 120)) ymd,
            CONVERT(varchar(10), CONVERT(char(7), b_datetime, 120)) ym,
            CONVERT(varchar(10), CONVERT(char(4), b_datetime, 120)) y
        FROM
            utb
    ) p UNPIVOT(date FOR xxx IN(ymd, ym, y)) AS unp
GROUP BY
    date
HAVING
    count(DISTINCT b_datetime) > 10;
```

### 119.3
```sql
SELECT
    FORMAT(b_datetime, 'yyyy-MM-dd') period,
    SUM(b_vol) vol
FROM
    utb
GROUP BY
    FORMAT(b_datetime, 'yyyy-MM-dd')
HAVING
    COUNT(DISTINCT b_datetime) > 10
UNION
ALL
SELECT
    FORMAT(b_datetime, 'yyyy-MM'),
    SUM(b_vol)
FROM
    utb
GROUP BY
    FORMAT(b_datetime, 'yyyy-MM')
HAVING
    COUNT(DISTINCT b_datetime) > 10
UNION
ALL
SELECT
    FORMAT(b_datetime, 'yyyy'),
    SUM(b_vol)
FROM
    utb
GROUP BY
    FORMAT(b_datetime, 'yyyy')
HAVING
    COUNT(DISTINCT b_datetime) > 10;
```

## 120
Для каждой авиакомпании, самолеты которой перевезли хотя бы одного пассажира, вычислить с точностью до двух десятичных знаков средние величины времени нахождения самолетов в воздухе (в минутах). Также рассчитать указанные характеристики по всем летавшим самолетам (использовать слово 'TOTAL').
- Вывод: компания, среднее арифметическое, среднее геометрическое, среднее квадратичное, среднее гармоническое.

Для справки:
- среднее арифметическое = (x1 + x2 + ... + xN)/N
- среднее геометрическое = (x1 * x2 * ... * xN)^(1/N)
- среднее квадратичное = sqrt((x1^2 + x2^2 + ... + xN^2)/N)
- среднее гармоническое = N/(1/x1 + 1/x2 + ... + 1/xN)
### 120.1
```sql
WITH t AS (
    SELECT
        ID_comp,
        CONVERT(
            numeric(18, 2),
            CASE
                WHEN time_in > = time_out THEN datediff(MINUTE, time_out, time_in)
                ELSE datediff(MINUTE, time_out, dateadd(DAY, 1, time_in))
            END
        ) AS trmin
    FROM
        (
            SELECT
                trip_no
            FROM
                Pass_in_trip
            GROUP BY
                trip_no,
                [date]
        ) pt
        JOIN Trip t ON pt.trip_no = t.trip_no
)
SELECT
    Coalesce(c.name, 'TOTAL'),
    A_mean,
    G_mean,
    Q_mean,
    H_mean
FROM
    (
        SELECT
            Id_comp,
            CONVERT(numeric(18, 2), avg(trmin)) A_mean,
            CONVERT(numeric(18, 2), Exp(avg(Log(trmin)))) G_mean,
            CONVERT(numeric(18, 2), sqrt(avg(trmin * trmin))) Q_mean,
            CONVERT(numeric(18, 2), count(*) / sum(1 / trmin)) H_mean
        FROM
            t
        GROUP BY
            ID_comp WITH cube
    ) AS a
    LEFT JOIN Company c ON a.ID_comp = c.ID_comp;
```

### 120.2
```sql
WITH D AS (
    SELECT
        DISTINCT ID_Comp,
        T.trip_no,
        date,
        CASE
            WHEN time_out <= time_in THEN DATEDIFF(mi, time_out, time_in) * 1.0
            ELSE DATEDIFF(mi, time_out, time_in) * 1.0 + 24 * 60.0
        END td
    FROM
        Pass_in_Trip Pit
        JOIN Trip T ON Pit.Trip_no = T.Trip_no
)
SELECT
    COALESCE(C.name, 'TOTAL'),
    CAST(AVG(td) AS DEC(10, 2)) A_mean,
    CAST(EXP(AVG(LOG(td))) AS DEC(10, 2)) G_mean,
    CAST(SQRT(AVG(td * td)) AS DEC(10, 2)) Q_mean,
    CAST(1 / AVG(1 / td) AS DEC(10, 2)) H_mean
FROM
    D
    JOIN Company C ON D.ID_comp = C.ID_comp
GROUP BY
    C.name WITH ROLLUP;
```

### 120.3
```sql
WITH fly AS (
    SELECT
        DISTINCT trip_no,
        date
    FROM
        Pass_in_trip
),
prep AS (
    SELECT
        c.name,
        (
            datepart(
                hh,
                CASE
                    WHEN time_in < time_out THEN time_in + 1 - time_out
                    ELSE time_in - time_out
                END
            ) * 60 + datepart(
                mi,
                CASE
                    WHEN time_in < time_out THEN time_in + 1 - time_out
                    ELSE time_in - time_out
                END
            )
        ) full_time
    FROM
        fly f
        JOIN Trip t ON t.trip_no = f.trip_no
        JOIN Company c ON c.ID_comp = t.ID_comp
)
SELECT
    CASE
        WHEN name IS NULL THEN 'TOTAL'
        ELSE CAST(name AS varchar)
    END name,
    --  среднее арифметическое
    round(
        cast(AVG(cast(full_time AS float)) AS decimal (6, 2)),
        2
    ) avg_time,
    -- среднее геометрическое
    round(
        cast(
            exp(
                sum(log(cast(full_time AS float))) / count(cast(full_time AS float))
            ) AS decimal (6, 2)
        ),
        2
    ) g_avg,
    -- среднее квадратичное
    round(
        cast(
            SQRT(
                SUM(
                    cast(full_time AS float) * cast(full_time AS float)
                ) / COUNT(cast(full_time AS float))
            ) AS decimal (6, 2)
        ),
        2
    ) s_avg,
    -- среднее гармоничное
    round(
        cast(
            count(*) / sum(1 / cast(full_time AS float)) AS decimal (6, 2)
        ),
        2
    ) h_avg
FROM
    prep
GROUP BY
    name WITH ROLLUP;
```

## 121
Найдите названия всех тех кораблей из базы данных, о которых можно определенно сказать, что они были спущены на воду до 1941 г.
### 121.1
```sql
-- Корабли, спущенные на воду до 1941 года
SELECT
    [name]
FROM
    [dbo].[Ships]
WHERE
    [launched] < 1941
UNION
-- Корабли, принимавшие участие в сражениях до 1941 года
SELECT
    [ship]
FROM
    [dbo].[Outcomes]
    JOIN [dbo].[Battles] ON [name] = [battle]
WHERE
    [date] < '19410101'
UNION
-- Головные корабли из Outcomes, в классе которых есть другие корабли, спущенные на воду до 1941 года
SELECT
    [ship]
FROM
    [dbo].[Outcomes]
WHERE
    [ship] IN (
        SELECT
            [class]
        FROM
            [dbo].[Ships]
        WHERE
            [launched] < 1941
    )
UNION
-- Головные корабли из Outcomes при условии, что хотя бы один из кораблей того же класса, участвовал в сражении до 1941 года
SELECT
    [ship]
FROM
    [dbo].[Outcomes]
WHERE
    [ship] IN (
        SELECT
            [class]
        FROM
            [dbo].[Ships]
            JOIN [dbo].[Outcomes] ON [Ships].[name] = [Outcomes].[ship]
            JOIN [dbo].[Battles] ON [Battles].[name] = [Outcomes].[battle]
        WHERE
            [date] < '19410101'
    )
UNION
SELECT
    [name]
FROM
    [dbo].[Ships]
WHERE
    [name] IN (
        SELECT
            [class]
        FROM
            [dbo].[Ships]
        WHERE
            [launched] < 1941
    )
UNION
SELECT
    [name]
FROM
    [dbo].[Ships]
WHERE
    [name] IN (
        SELECT
            [class]
        FROM
            [dbo].[Ships]
            JOIN [dbo].[Outcomes] ON [Ships].[name] = [ship]
            JOIN [dbo].[Battles] ON [Battles].[name] = [battle]
        WHERE
            [date] < '19410101'
    );
```

### 121.2
```sql
SELECT
    DISTINCT S.name
FROM
    Ships AS S
WHERE
    S.launched < 1941
UNION
SELECT
    DISTINCT O.ship
FROM
    (
        SELECT
            DISTINCT ship
        FROM
            Outcomes
        UNION
        SELECT
            DISTINCT name
        FROM
            Ships
    ) O
    JOIN Ships AS S ON O.ship = S.class
    LEFT JOIN Outcomes AS O1 ON S.name = O1.ship
    LEFT JOIN Battles AS B ON O1.battle = B.name
WHERE
    S.launched < 1941
    OR DATEPART(yy, B.date) < 1941
UNION
SELECT
    DISTINCT O.ship
FROM
    Outcomes AS O
    JOIN Battles AS B ON O.battle = B.name
WHERE
    DATEPART(yy, B.date) < 1941;
```

## 122
Считая, что первый пункт вылета является местом жительства, найти пассажиров, которые находятся вне дома. Вывод: имя пассажира, город проживания
### 122.1
```sql
WITH cta AS (
    SELECT
        [ID_psg],
        [town_from],
        [town_to],
        [date] + [time_out] AS date_out,
        min([date] + [time_out]) over (PARTITION by [dbo].[Pass_in_trip].[ID_psg]) mnd,
        max([date] + [time_out]) over (PARTITION by [dbo].[Pass_in_trip].[ID_psg]) mxd
    FROM
        [dbo].[Pass_in_trip]
        JOIN [dbo].[Trip] ON [Trip].[trip_no] = [Pass_in_trip].[trip_no]
)
SELECT
    [dbo].[Passenger].[name],
    [jta].[b_town]
FROM
    (
        SELECT
            [cta].[ID_psg],
            [cta].[town_from] AS b_town
        FROM
            cta
        WHERE
            [cta].[date_out] = [cta].[mnd]
    ) jta
    JOIN (
        SELECT
            [cta].[ID_psg],
            [cta].[town_to] AS c_town
        FROM
            cta
        WHERE
            [cta].[date_out] = [cta].[mxd]
    ) jtb ON [jtb].[ID_psg] = [jta].[ID_psg]
    JOIN [dbo].[Passenger] ON [Passenger].[ID_psg] = [jta].[ID_psg]
WHERE
    [jta].[b_town] <> [jtb].[c_town];
```

### 122.2
```sql
WITH Fly AS (
    SELECT
        ID_psg,
        min(
            CONVERT(char(20), date + time_out, 127) + town_from
        ) ff,
        max(
            CONVERT(char(20), date + time_out, 127) + town_to
        ) lf
    FROM
        Pass_in_trip pit
        LEFT JOIN Trip t ON t.trip_no = pit.trip_no
    GROUP BY
        ID_psg
)
SELECT
    p.name,
    substring(ff, 21, len(ff) -21 + 1)
FROM
    Fly f
    LEFT JOIN Passenger p ON p.ID_psg = f.ID_psg
WHERE
    substring(ff, 21, len(ff) -21 + 1) <> substring(lf, 21, len(lf) -21 + 1);
```

### 122.3
```sql
WITH A AS (
    SELECT
        id_psg,
        town_from,
        town_to,
        date + time_out dd,
        min(date + time_out) over(PARTITION by id_psg) min,
        max(date + time_out) over(PARTITION by id_psg) max
    FROM
        trip t
        JOIN pass_in_trip p ON p.trip_no = t.trip_no
)
SELECT
    name,
    town_from
FROM
    passenger ps,
    (
        SELECT
            id_psg,
            town_from
        FROM
            a
        WHERE
            dd = min
        EXCEPT
        SELECT
            id_psg,
            town_to
        FROM
            a
        WHERE
            dd = max
    ) x
WHERE
    x.id_psg = ps.id_psg;
```

## 123
Для каждого производителя подсчитать: сколько имеется в наличии его продуктов (любого типа) с неуникальной для этого производителя ценой и количество таких неуникальных цен.
- Вывод: производитель, количество продуктов, количество цен.
### 123.1
```sql
WITH cte AS (
    SELECT
        [maker],
        [Product].[model],
        coalesce(
            [dbo].[PC].[price],
            coalesce([dbo].[Laptop].[price], [dbo].[Printer].[price])
        ) [price]
    FROM
        [dbo].[Product]
        LEFT JOIN [dbo].[PC] ON [PC].[model] = [Product].[model]
        LEFT JOIN [dbo].[Laptop] ON [Laptop].[model] = [Product].[model]
        LEFT JOIN [dbo].[Printer] ON [Printer].[model] = [Product].[model]
),
ctp AS (
    SELECT
        [cte].[maker],
        [cte].[price],
        count(*) AS pc
    FROM
        cte
    WHERE
        [cte].[price] IS NOT NULL
    GROUP BY
        maker,
        [cte].[price]
    HAVING
        count(*) > 1
),
ctm AS (
    SELECT
        DISTINCT maker
    FROM
        [dbo].[Product]
),
ctr AS (
    SELECT
        cte.maker,
        count(model) cm,
        count(DISTINCT cte.price) cp
    FROM
        [cte]
        JOIN [ctp] ON ctp.[maker] = cte.[maker]
        AND ctp.[price] = [cte].[price]
    GROUP BY
        cte.maker
)
SELECT
    ctm.[maker],
    coalesce(cm, 0),
    coalesce(cp, 0)
FROM
    [ctm]
    LEFT JOIN [ctr] ON [ctr].[maker] = [ctm].[maker];
```

### 123.2
```sql
WITH cta AS (
    SELECT
        [maker],
        coalesce(
            [dbo].[PC].[price],
            coalesce([dbo].[Laptop].[price], [dbo].[Printer].[price])
        ) price,
        count(*) pc
    FROM
        [dbo].[Product]
        LEFT JOIN [dbo].[PC] ON [PC].[model] = [Product].[model]
        LEFT JOIN [dbo].[Laptop] ON [Laptop].[model] = [Product].[model]
        LEFT JOIN [dbo].[Printer] ON [Printer].[model] = [Product].[model]
    WHERE
        [PC].[price] IS NOT NULL
        OR [Laptop].[ram] IS NOT NULL
        OR [Printer].[price] IS NOT NULL
    GROUP BY
        [maker],
        coalesce(
            [dbo].[PC].[price],
            coalesce([dbo].[Laptop].[price], [dbo].[Printer].[price])
        )
    HAVING
        count(*) > 1
),
ctm AS (
    SELECT
        DISTINCT maker
    FROM
        [dbo].[Product]
)
SELECT
    [ctm].[maker],
    sum(coalesce(pc, 0)) AS mc,
    count(price) AS pc
FROM
    ctm
    LEFT JOIN cta ON [cta].[maker] = [ctm].[maker]
GROUP BY
    [ctm].[maker];
```

## 124
Среди пассажиров, которые пользовались услугами не менее двух авиакомпаний, найти тех, кто совершил одинаковое количество полётов самолетами каждой из этих авиакомпаний. Вывести имена таких пассажиров.
### 124.1
```sql
WITH [cte] AS (
    SELECT
        [ID_psg],
        [ID_comp],
        max(count(*)) over (PARTITION by [ID_psg]) AS [mxctc],
        min(count(*)) over (PARTITION by [ID_psg]) AS [mnctc],
        count(*) over (PARTITION by [ID_psg]) AS [cc]
    FROM
        [dbo].[Pass_in_trip]
        JOIN [dbo].[Trip] ON [Trip].[trip_no] = [Pass_in_trip].[trip_no]
    GROUP BY
        [ID_psg],
        [ID_comp]
)
SELECT
    [name]
FROM
    [dbo].[Passenger]
    JOIN [cte] ON [cte].[ID_psg] = [Passenger].[ID_psg]
WHERE
    [cte].[mxctc] = [cte].[mnctc]
    AND [cte].[cc] >= 2
GROUP BY
    [Passenger].[ID_psg],
    [name];
```

### 124.2
```sql
SELECT
    (
        SELECT
            name
        FROM
            passenger
        WHERE
            id_psg = t.id_psg
    )
FROM
    (
        SELECT
            id_psg,
            id_comp,
            COUNT(*) cn
        FROM
            pass_in_trip pit
            JOIN trip t ON t.trip_no = pit.trip_no
        GROUP BY
            id_psg,
            id_comp
    ) t
GROUP BY
    id_psg
HAVING
    COUNT(*) > 1
    AND MAX(cn) = AVG(cn);
```

## 125
Данные о продаваемых моделях и ценах (из таблиц Laptop, PC и Printer) объединить в одну таблицу LPP и создать в ней порядковую нумерацию (id) без пропусков и дубликатов.
- Считать, что модели внутри каждой из трёх таблиц упорядочены по возрастанию поля code. Единую нумерацию записей LPP сделать по следующему правилу: сначала идут первые модели из таблиц (Laptop, PC и Printer), потом последние модели, далее - вторые модели из таблиц, предпоследние и т.д.
- При исчерпании моделей определенного типа, нумеровать только оставшиеся модели других типов.
- Вывести: id, type, model и price. Тип модели type является строкой 'Laptop', 'PC' или 'Printer'.
### 125.1
```sql
WITH cta AS (
    SELECT
        CASE
            WHEN ca > ceiling((cc * 1.0) / 2) THEN cd * 2
            ELSE cc - cd + ca
        END AS crn,
        1 AS tt,
        *
    FROM
        (
            SELECT
                row_number() over(
                    ORDER BY
                        code
                ) ca,
                row_number() over(
                    ORDER BY
                        code DESC
                ) cd,
                count(*) over() cc,
                'Laptop' AS TYPE,
                [model],
                [price]
            FROM
                [dbo].[Laptop]
        ) tr
    UNION
    SELECT
        CASE
            WHEN ca > ceiling((cc * 1.0) / 2) THEN cd * 2
            ELSE cc - cd + ca
        END AS crn,
        2 AS tt,
        *
    FROM
        (
            SELECT
                row_number() over(
                    ORDER BY
                        code
                ) ca,
                row_number() over(
                    ORDER BY
                        code DESC
                ) cd,
                count(*) over() cc,
                'PC' AS TYPE,
                [model],
                [price]
            FROM
                [dbo].PC
        ) tr
    UNION
    SELECT
        CASE
            WHEN ca > ceiling((cc * 1.0) / 2) THEN cd * 2
            ELSE cc - cd + ca
        END AS crn,
        3 AS tt,
        *
    FROM
        (
            SELECT
                row_number() over(
                    ORDER BY
                        code
                ) ca,
                row_number() over(
                    ORDER BY
                        code DESC
                ) cd,
                count(*) over() cc,
                'Printer' AS TYPE,
                [model],
                [price]
            FROM
                [dbo].[Printer]
        ) tr
)
SELECT
    row_number() over(
        ORDER BY
            [cta].[crn],
            [cta].[tt]
    ) id,
    [cta].[type],
    [cta].[model],
    [cta].[price]
FROM
    cta
ORDER BY
    crn,
    [cta].[tt];
```

### 125.2
```sql
SELECT
    *
FROM
    (
        SELECT
            CASE
                WHEN ca > ceiling((cc * 1.0) / 2) THEN cd * 2
                ELSE cc - cd + ca
            END AS crn,
            2 AS tt,
            *
        FROM
            (
                SELECT
                    row_number() over(
                        ORDER BY
                            code
                    ) ca,
                    row_number() over(
                        ORDER BY
                            code DESC
                    ) cd,
                    count(*) over() cc,
                    'PC' AS TYPE,
                    [model],
                    [price]
                FROM
                    [dbo].PC
            ) tr
    ) tr1
ORDER BY
    [tr1].[crn];
```
