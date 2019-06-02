# BD
БД
Многотабличные запросы:
1 Вывести все имена и фамилии студентов, и название хобби, которым занимается этот студент.

select s1.NAME, s1.SURNAME, t2.name
from Students$ s1 left join (select SH.N_Z as N_z, name
 from Hobbies$ H, Students_hobbies$ SH
 where SH.HOBBY_ID=H.ID) t2 on s1.N_Z = t2.N_z
 
 2 Вывести информацию о студенте, занимающимся хобби самое продолжительное время.
 
 select S.*
from Students$ S, Students_hobbies$ SH
where S.N_Z = SH.N_Z and SH.DATE_FINISH is null and months_between(sysdate, SH.DATE_START) = (
 select max(months_between(sysdate, DATE_START)) as m
 from Students_hobbies$
 where DATE_FINISH is null
)

4 Вывести фамилию, имя, зачетку, дату рождения, название хобби и длительность в месяцах, для всех завершенных хобби.

select S.name, S.surname, S.N_Z, S.Date_birth, H.name, months_between(sysdate, SH.DATE_START) as DLITELNOST
from Students$ S, Students_hobbies$ SH, Hobbies$ H
where S.N_Z = SH.N_Z and H.ID=SH.hobby_id and SH.DATE_FINISH is not null

8 Найти все хобби, которыми увлекаются студенты, имеющие максимальный балл.

select distinct H.name
from Students$ S, Students_hobbies$ SH, Hobbies$ H
where S.N_Z = SH.N_Z and H.ID=SH.hobby_id and S.SCORE = (select max(Score) from Students$)

9 Найти все действующие хобби, которыми увлекаются троечники 2-го курса.

select distinct H.name
from Students$ S, Students_hobbies$ SH, Hobbies$ H
where S.N_Z = SH.N_Z and H.ID=SH.hobby_id and S.SCORE between 3 and 4 and S.N_GROUP like '2%' and SH.Date_finish is null

16 Вывести ИД самого популярного хобби.

select hobby_id
from Students_hobbies$
group by hobby_id
having count(*) = (select max(count(*)) from Students_hobbies$ group by hobby_id)

18 Вывести ИД 3х хобби с максимальным риском.

select id
from hobbies$
where RISK = (select max(risk) from hobbies$) and rownum < 4

25 Представление: самое популярное хобби среди всех студентов.

create or replace view v1 as
select hobby_id 
from Students_hobbies$ 
group by hobby_id 
having count(*) = (select max(count(*)) from Students_hobbies$ group by hobby_id)

39 Выведите всех студентов, которые занимались или занимаются хотя бы 1 хобби. Выведите на экран Имя, Фамилию, Названию хобби, а также надпись «занимается», если студент продолжает заниматься хобби в данный момент или «закончил», если уже не занимает.

select S.name, s.Surname, H.name, 
case 
 when date_finish is null 
 then 'занимается' 
 else 'закончил' 
end as Status
from Students$ S, Students_hobbies$ SH, Hobbies$ H
where S.N_Z = SH.N_Z and H.ID=SH.hobby_id

26 Создать обновляемое представление.

create or replace view v2 as
select N_Z, Score
from Students$

#2 Задания на изменение/удаление/добавление
 2_1 Удалите всех студентов с неуказанной датой рождения
 
 delete from Students$ where DATE_BIRTH is null
 
 2_2 Измените дату рождения всех студентов, с неуказанной датой рождения на 01-01-1999
 
 update Students$ set Date_birth='01-01-1999' where DATE_BIRTH is null
 
 2_3 Удалите из таблицы студента с номером зачётки 21
 
 delete from Students$ where N_Z=21
  
  2_6 Удалите все завершенные хобби студентов

delete from Students_hobbies$ where Date_finish is not null

2_7 Добавьте студенту с n_z 4 хобби с id 5. date_start - '15-11-2009, date_finish - null

insert into Students_hobbies$(n_z,hobby_id, date_start) values(4,5,'11-15-2009')
