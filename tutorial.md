# Notizen zum Tutorial

## [Routinen](https://www.j-breuer.de/blog/programmierung-mysql-3-erste-routine/)

wichtige Schritte:

-   `DELMITER`
-   richtige Datenbank auswählen

### Prozedur mit Übergabeparameter

```sql
USE `test`;
DROP PROCEDURE IF EXISTS `hello_world`;

DELIMITER $$
CREATE PROCEDURE `test`.`hello_world` (par_name VARCHAR(32))
BEGIN
    SELECT CONCAT('Hello ', par_name);
END$$

DELIMITER ;
```

```sql
USE `test`;
CALL hello_world('Sepp');
```

## [Grundkonstrukte](https://www.j-breuer.de/blog/programmierung-mysql-4-grundkonstrukte/)

### Variablen

```sql
DECLARE Variablenname Variablentyp [DEFAULT Standardwert];
```

```sql
DECLARE var_food VARCHAR(32) DEFAULT 'Lasagne';
```

```sql
SET var_food = 'Pizza';
```

#### Verwendung

```sql
USE `test`;
DROP PROCEDURE IF EXISTS `hello_world`;

DELIMITER $$
CREATE PROCEDURE `test`.`hello_world` (par_name VARCHAR(32))
BEGIN
    DECLARE var_output VARCHAR(128);
    SET var_output = CONCAT('Hello ', par_name);
    SELECT var_output;
END$$

DELIMITER ;

CALL hello_world("Otto");
```

#### Werte aus Datenbank speichern

```sql
SELECT Spaltenname INTO Variablenname FROM Tabellenname [WHERE ...];
```

```sql
USE `test`;
DROP PROCEDURE IF EXISTS `hello_world`;

DELIMITER $$
CREATE PROCEDURE `hello_world`(par_id INT)
BEGIN
    DECLARE var_output, var_name VARCHAR(128);
    SELECT test_name INTO var_name FROM test_table WHERE test_id = par_id;
    SET var_output = concat('Hello ', var_name);
    SELECT var_output;
END

DELIMITER ;
CALL hello_world(69);
```

### IF-Abfragen

```sql
IF Bedingung THEN Anweisung(en)
[ELSEIF Bedingung THEN   Anweisung(en)]
[ELSE Anweisung(en)]
END IF;
```

```sql
USE `test`;
DROP PROCEDURE IF EXISTS `hello_world`;

DELIMITER $$
CREATE  PROCEDURE `hello_world`(par_id INT)
BEGIN
    DECLARE var_output, var_name VARCHAR(128);
    SELECT test_name INTO var_name FROM test_table  WHERE test_id = par_id;
    IF var_name IS NULL THEN
    SET var_name = 'Unbekannter';
    END IF;
    SET var_output = concat('Hello ', var_name);
    SELECT var_output;
END
```

### Schleifen

```sql
WHILE Bedingung DO
    Anweisung(en)
END WHILE;
```

## [Cursor](https://www.j-breuer.de/blog/programmierung-mysql-5-cursor/)

```sql
DECLARE Cursorname CURSOR AS SELECT Spaltenname FROM Tabellenname [WHERE...]
```

### Continue Handler

```sql
DECLARE var_done INT DEFAULT 0;
DECLARE CONTINUE HANDLER FOR NOT FOUND SET var_done = 1;
```

### Cursor öffnen

```sql
OPEN Cursorname;
FETCH NEXT FROM Cursorname INTO Variablenname;
```

### Cursor durchlaufen

```sql
WHILE NOT var_done DO
    FETCH Cursorname INTO Variablenname;
END WHILE;
```

### Cursor schließen

```sql
CLOSE Cursorname;
```

### [Vorlage](https://www.j-breuer.de/blog/programmierung-mysql-5-cursor/#:~:text=ein%20kleines%20Template)

```sql
USE `test`;
DELIMITER $$

CREATE PROCEDURE `cursor_example`()
BEGIN

    DECLARE var_col1, var_done INT DEFAULT 0;
    DECLARE myCursor CURSOR FOR SELECT table1.col1  FROM table1;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET  var_done = 1;

    SET var_done = 0; --only required if there is a SELECT ... INTO ... before opening the cursor, which could return no results

    OPEN myCursor;
    FETCH NEXT FROM myCursor INTO var_col1;

    WHILE NOT var_done DO

        UPDATE table2 SET table2.col2 = 0 WHERE table2.col1 = var_col1; --or whatever the loop is  supposed to do

        SET var_done = 0; --only required if there is a SELECT ... INTO ... in the loop, which could return no results
        FETCH myCursor INTO var_col1;

    END WHILE;
    CLOSE myCursor;

END$$

DELIMITER ;
```

## [Trigger](https://www.j-breuer.de/blog/programmieren-mysql-6-trigger/)

```sql
CREATE TRIGGER Triggername Triggerzeitpunkt Triggerereignis ON Tabellenname FOR EACH ROW Triggeranweisungen
```

- Triggerzeitpunkt:  BEFORE, AFTER
- Triggerereignis: DELETE, UPDATE, INSERT
- Triggeranweisungen: eine einzelne Anweisung oder mehrere in BEGIN-END-Block

### DELETE-Trigger

Zugriff auf alten Wert über Pseudotabelle OLD

```sql
DELIMITER $$

CREATE TRIGGER trig_user_delete
BEFORE DELETE ON user
FOR EACH ROW

BEGIN
    DELETE FROM posts where user_id = OLD.id;
END$$
```

### INSERT-Trigger

Zugriff auf neuen Wert über Pseudotabelle NEW

```sql
DELIMITER $$

CREATE TRIGGER trig_user_insert
BEFORE INSERT ON user
FOR EACH ROW
BEGIN

IF NEW.user_name = '' THEN
    SET NEW.user_name = 'anomymous';
END IF;

END$$
```