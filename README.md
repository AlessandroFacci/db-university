# DB University

### 05/10/2023

Modellizzare la struttura di un database per memorizzare tutti i dati riguardanti una università:

- sono presenti diversi Dipartimenti (es.: Lettere e Filosofia, Matematica, Ingegneria ecc.);
- ogni Dipartimento offre più Corsi di Laurea (es.: Civiltà e Letterature Classiche, Informatica, Ingegneria Elettronica ecc..)
- ogni Corso di Laurea prevede diversi Corsi (es.: Letteratura Latina, Sistemi Operativi 1, Analisi Matematica 2 ecc.);
- ogni Corso può essere tenuto da diversi Insegnanti;
- ogni Corso prevede più appelli d'Esame;
- ogni Studente è iscritto ad un solo Corso di Laurea;
- ogni Studente può iscriversi a più appelli di Esame;
- per ogni appello d'Esame a cui lo Studente ha partecipato, è necessario memorizzare il voto ottenuto, anche se non sufficiente.
  Pensiamo a quali entità (tabelle) creare per il nostro database e cerchiamo poi di stabilirne le relazioni. Infine, andiamo a definire le colonne e i tipi di dato di ogni tabella.

Utilizzare https://www.diagrams.net/ per la creazione dello schema.
Esportare quindi il diagramma in jpg e caricarlo nella repo. (modificato)

### 06/10/2023

### QUERY 1

1. Selezionare tutti gli studenti nati nel 1990 (160)

```
SELECT *
FROM `students`
WHERE `date_of_birth` LIKE "1990-%";
```

### QUERY 2

2. Selezionare tutti i corsi che valgono più di 10 crediti (479)

```
SELECT *
FROM `courses`
WHERE `cfu` > 10;
```

### QUERY 3

3. Selezionare tutti gli studenti che hanno più di 30 anni

```
SELECT *
FROM `students`
WHERE `date_of_birth` <= '1993-10-06';


SELECT *
FROM `students`
WHERE TIMESTAMPDIFF(YEAR, `date_of_birth`, CURDATE()) > 30;
```

### QUERY 4

4. Selezionare tutti i corsi del primo semestre del primo anno di un
   qualsiasi corso di laurea (286)

```
SELECT *
FROM `courses`
WHERE `period` LIKE 'I semestre' AND
	  `year` LIKE 1;
```

### QUERY 5

5. Selezionare tutti gli appelli d'esame che avvengono nel pomeriggio (dopo le 14) del 20/06/2020 (21)

```
SELECT *
FROM `exams`
WHERE `date` LIKE '2020-06-20' AND
       `hour` >= '14:00:00';

```

### QUERY 6

6. Selezionare tutti i corsi di laurea magistrale (38)

```
SELECT *
FROM `degrees`
WHERE `level` LIKE 'magistrale';
```

### QUERY 7

7. Da quanti dipartimenti è composta l'università? (12)

```
SELECT
COUNT(`id`)
FROM `departments`;
```

### QUERY 8

8. Quanti sono gli insegnanti che non hanno un numero di telefono? (50)

```
SELECT
COUNT(`id`)
FROM `teachers`
WHERE `phone` IS NULL;
```

### 09/10/2023

### EX - Query con GROUP BY

### QUERY 1

1. Contare quanti iscritti ci sono stati ogni anno

```
SELECT YEAR(`enrolment_date`) AS "anno_iscrizione", COUNT(*) AS "numero_studenti"
FROM `students`
GROUP BY YEAR (`enrolment_date`);
```

### QUERY 2

2. Contare gli insegnanti che hanno l'ufficio nello stesso edificio

```
SELECT `office_address` AS "edificio", COUNT(`id`) AS "numero_insegnanti"
FROM `teachers`
GROUP BY `office_address`;
```

### QUERY 3

3. Calcolare la media dei voti di ogni appello d'esame

```
SELECT `exam_id` AS "appello_d'esame", AVG(`vote`) AS "media_voto"
FROM `exam_student`
GROUP BY `exam_id`;
```

### QUERY 4

4. Contare quanti corsi di laurea ci sono per ogni dipartimento

```
SELECT `department_id` AS "dipartimento", COUNT(`id`) AS "numero_di_corsi"
FROM `degrees`
GROUP BY `department_id`;
```

### EX - Query con JOIN.pdf

### QUERY 1

1. Selezionare tutti gli studenti iscritti al Corso di Laurea in Economia

```
SELECT `students`.*, `degrees`.`name`
FROM `degrees`
INNER JOIN `students`
ON `degrees`.`id` = `students`.`degree_id`
WHERE `degrees`.`name` = "Corso di Laurea in Economia";
```

### QUERY 2

2. Selezionare tutti i Corsi di Laurea Magistrale del Dipartimento di Neuroscienze

```
SELECT `degrees`.*, `departments`.`name` AS "nome_dipartimento"
FROM `departments`
INNER JOIN `degrees`
ON `departments`.`id` = `degrees`.`department_id`
WHERE `degrees`.`level` = "magistrale" AND
	   `departments`.`name` = "Dipartimento di Neuroscienze";
```

### QUERY 3

3. Selezionare tutti i corsi in cui insegna Fulvio Amato (id=44)

```
SELECT `teachers`.*, `course_teacher`.`course_id`
FROM `teachers`
INNER JOIN `course_teacher`
ON `teachers`.`id` = `course_teacher`.`teacher_id`
WHERE `teachers`.`id` = 44;
```

### QUERY 4

4. Selezionare tutti gli studenti con i dati relativi al corso di laurea a cui sono iscritti e il relativo dipartimento, in ordine alfabetico per cognome e nome

```
SELECT `students`.`surname`, `students`.`name`, `degrees`.`name` AS "corso_di_laurea", `departments`.`name` AS "dipartimento"
FROM `departments`
INNER JOIN `degrees`
ON `departments`.`id` = `degrees`.`department_id`
INNER JOIN `students`
ON `degrees`.`id` = `students`.`degree_id`
ORDER BY `students`.`surname` ASC,  `students`.`name` ASC;
```

### QUERY 5

5. Selezionare tutti i corsi di laurea con i relativi corsi e insegnanti

```
SELECT `degrees`.`name` AS "corso_di_laurea", `courses`.`name` AS "corso", `teachers`.`surname`, `teachers`.`name`
FROM `teachers`
INNER JOIN`course_teacher`
ON `teachers`.`id` = `course_teacher`.`teacher_id`
INNER JOIN `courses`
ON `courses`.`id` = `course_teacher`.`course_id`
INNER JOIN `degrees`
ON `degrees`.`id` = `courses`.`degree_id`;
```

### QUERY 6

6. Selezionare tutti i docenti che insegnano nel Dipartimento di Matematica (54)

```
SELECT `teachers`.`id`, `teachers`.`surname`, `teachers`.`name`, `departments`.`name` AS "dipartimento"
FROM `teachers`
INNER JOIN`course_teacher`
ON `teachers`.`id` = `course_teacher`.`teacher_id`
INNER JOIN `courses`
ON `courses`.`id` = `course_teacher`.`course_id`
INNER JOIN `degrees`
ON `degrees`.`id` = `courses`.`degree_id`
INNER JOIN `departments`
ON `departments`.`id` = `degrees`.`department_id`
WHERE `departments`.`name` = "Dipartimento di Matematica"
GROUP BY `teachers`.`id`;
```

### QUERY 7

7. BONUS: Selezionare per ogni studente il numero di tentativi sostenuti per ogni esame, stampando anche il voto massimo. Successivamente, filtrare i tentativi con voto minimo 18.

```

```
