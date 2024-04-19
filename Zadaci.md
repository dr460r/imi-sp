## Zadaci

### 1. За сваког запосленог приказати назив сектора у којем ради. Приказати и запослене који у оквиру фирме нису у неком сектору, као и секторе у којима нема запослених (табеле employees и departments).
### 2. Написати упит за приказ наслова, описа, типа и уметника из базе (табеле d_songs и d_types).
### 3. Креирати упит који враћа презиме, плату, платне разреде. Изаберите плату између најниже и највише зараде (табеле employees и job_grades).
### 4. Приказати ИД региона и назив региона за Америку (табеле countries и regions).
### 5. Креирати упит којим се приказује ИД запослених, име, презиме, ИД менаџер, менаџерово име и презиме за сваког запосленог  (табеле employees и employees).
### 6. Креирати упит којим се приказује ИД менаџера, ИД одсека, одсек, име и презиме свих запослених који раде у одсецима 80, 90, 110 и 190 (табеле employees и departments).
### 7. Приказати све запослене, укључујући и оне који немају менаџера. Поређати растући поредак на основу ИД запосленог (табеле employees и employees).
### 8. Приказати све називе сектора који се налазе у граду Торонто (табеле locations и departments).
### 9. Креирати упит који приказује све запослене који раде у граду Оxфорд  (табеле employees и departments).
### 10. Приказати телефоне и мејл адресе запослених који раде у граду Оxфорд  (табеле employees и departments).

<div class="page"/>

## Rešenja

### 1. За сваког запосленог приказати назив сектора у којем ради. Приказати и запослене који у оквиру фирме нису у неком сектору, као и секторе у којима нема запослених (табеле employees и departments).

_Full Outer Join_

```sql
SELECT e.first_name, e.last_name, d.department_name
FROM employees e FULL JOIN departments d
  ON e.department_id = d.department_id
```
### 2. Написати упит за приказ наслова, описа, типа и уметника из базе (табеле d_songs и d_types).

_Dekartov proizvod + Filter_

```sql
SELECT s.title, s.description, t.description, s.artist
FROM d_songs s, d_types t
WHERE s.type_code = t.code
```

_Inner Join_

```sql
SELECT s.title, s.description, t.description, s.artist
FROM d_songs s JOIN d_types t
  ON s.type_code = t.code
```

### 4. Приказати ИД региона и назив региона за Америку (табеле countries и regions).

_Dekartov proizvod + Filter_

```sql
SELECT c.country_name, r.region_id, r.region_name
FROM countries c, regions r
WHERE c.region_id = r.region_id 
  AND c.country_id = 'US'
```

_Inner Join_

```sql
SELECT c.country_name, r.region_id, r.region_name
FROM countries c JOIN regions r
  ON c.region_id = r.region_id 
WHERE c.country_id = 'US'
```

### 5. Креирати упит којим се приказује ИД запослених, име, презиме, ИД менаџер, менаџерово име и презиме за сваког запосленог  (табеле employees и employees).

_Dekartov proizvod + Filter_

```sql
SELECT e.employee_id, e.first_name, e.last_name, 
       m.employee_id, m.first_name, m.last_name
FROM employees e, employees m
WHERE e.manager_id(+) = m.employee_id
```

_Left Outer Join_

```sql
SELECT e.employee_id, e.first_name, e.last_name, 
       m.employee_id, m.first_name, m.last_name
FROM employees e LEFT JOIN employees m
  ON e.manager_id = m.employee_id
```

_Hijerarhijski upit_

```sql
SELECT employee_id, first_name, last_name, 
       PRIOR employee_id, PRIOR first_name, PRIOR last_name
FROM employees
START WITH manager_id is NULL
CONNECT BY manager_id = PRIOR employee_id
```


### 6. Креирати упит којим се приказује ИД менаџера, ИД одсека, одсек, име и презиме свих запослених који раде у одсецима 80, 90, 110 и 190 (табеле employees и departments).

_Dekartov proizvod + Filter_

```sql
SELECT d.manager_id, d.department_id, d.department_name,
       e.first_name, e.last_name
FROM employees e, departments d
WHERE e.department_id = d.department_id
  AND d.department_id IN (80, 90, 110, 190)
```

_Inner Join_

```sql
SELECT d.manager_id, d.department_id, d.department_name,
       e.first_name, e.last_name
FROM employees e JOIN departments d
  ON e.department_id = d.department_id
WHERE d.department_id IN (80, 90, 110, 190)
```

Ako zelimo da dobijemo ime menadzera umesto id:

```sql
SELECT m.first_name, m.last_name, 
       e.department_id, d.department_name,
       e.first_name, e.last_name
FROM employees e 
  JOIN departments d ON e.department_id = d.department_id
  JOIN employees m ON d.manager_id = m.employee_id
WHERE d.department_id IN (80, 90, 110, 190)
```


### 7. Приказати све запослене, укључујући и оне који немају менаџера. Поређати растући поредак на основу ИД запосленог (табеле employees и employees).

```sql
SELECT e.employee_id, e.first_name, e.last_name
FROM employees e LEFT JOIN employees m 
  ON e.manager_id = m.employee_id
ORDER BY e.employee_id;
```

### 8. Приказати све називе сектора који се налазе у граду Торонто (табеле locations и departments).

```sql
SELECT d.department_name
FROM departments d JOIN locations l
  ON d.location_id = l.location_id
WHERE l.city = 'Toronto';
```

### 9. Креирати упит који приказује све запослене који раде у граду Оxфорд  (табеле employees и departments).

```sql
SELECT e.first_name, e.last_name
FROM employees e
  JOIN departments d ON e.department_id = d.department_id
  JOIN locations l ON d.location_id = l.location_id
WHERE l.city = 'Oxford';
```

### 10. Приказати телефоне и мејл адресе запослених који раде у граду Оxфорд  (табеле employees и departments).

```sql
SELECT e.first_name, e.last_name, e.phone_number, e.email
FROM employees e
  JOIN departments d ON e.department_id = d.department_id
  JOIN locations l ON d.location_id = l.location_id
WHERE l.city = 'Oxford';
```