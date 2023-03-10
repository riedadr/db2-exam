# Exam-Fragen

1. Entwerfen Sie einen Trigger 'tb_user_insert', der immer dann ausgeführt wird, bevor eine Zeile in Tabelle user eingefügt wird. Der Trigger soll sicherstellen, dass immer ein Benutzername gesetzt ist, notfalls wird der Benutzername 'anonymous' genutzt. Erweitern Sie hierzu die vorgegebene Code-Grundstruktur.

    ```sql
    DELIMITER $$

    CREATE TRIGGER tb_user_insert BEFORE INSERT ON `user` FOR EACH ROW
    BEGIN
        IF NEW.username = '' THEN
            SET NEW.username = 'anonymous';
        END IF;
    END $$
    DELIMITER ;
    ```

2. Entwerfen Sie eine SQL-Prozedur 'hey_folks', die einen Namen als Parameter (p_name, max. 32 Zeichen) übernimmt und die beiden Zeichenkette "Hallo" und den Übergabeparameter p_name aneinanderhängt und zurück gibt. Erweitern Sie hierzu die vorgegebene Code-Grundstruktur.

    ```sql
    DELIMITER $$

    CREATE PROCEDURE hey_folks (p_name VARCHAR(32))
    BEGIN
        SELECT CONCAT("Hallo ", p_name)
    END $$
    DELIMITER ;
    ```

3. Entwerfen Sie einen Trigger 'tb_user_delete', der immer dann ausgeführt wird, bevor eine Zeile in Tabelle user gelöscht wird. Im Ergebnis sollen durch den Trigger alle Einträge in Tabelle posts gelöscht werden, die in Spalte user_id die ID des zu löschenden Users (Spalte: id) haben. Erweitern Sie hierzu die vorgegebene Code-Grundstruktur.

    ```sql
    DELIMITER $$

    CREATE TRIGGER tb_user_delete BEFORE DELETE ON `user` FOR EACH ROW
    BEGIN
        DELETE FROM posts WHERE posts.user_id = OLD.id;
    END $$
    DELIMITER ;
    ```

4. Entwerfen Sie eine paramterlose SQL-Prozedur 'hello_world', die die Zeichenkette "Hallo Welt!" zurück gibt. Erweitern Sie hierzu die vorgegebene Code-Grundstruktur.

    ```sql
    DELIMITER $$

    CREATE PROCEDURE hello_world ()
    BEGIN
        SELECT 'Hallo Welt';
    END $$
    DELIMITER ;
    ```