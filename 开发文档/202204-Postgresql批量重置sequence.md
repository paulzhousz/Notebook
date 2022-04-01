## Postgresql批量重置sequence
<p align='center'>2022-04-01</p>

```
DO $$ DECLARE
TABLE_NAME TEXT;
maxid INT;
BEGIN
        FOR TABLE_NAME IN (
        SELECT
            tb.TABLE_NAME 
        FROM
            information_schema.tables AS tb
            INNER JOIN information_schema.COLUMNS AS cols ON tb.TABLE_NAME = cols.TABLE_NAME 
        WHERE
            tb.table_catalog = 'tdmb_new' 
            AND tb.table_schema = 'public' 
            AND cols.COLUMN_NAME = 'id' 
						and tb."table_name" like 'teacherinfomanage%'

        )
        LOOP
        EXECUTE'SELECT MAX(id) +1 FROM ' || TABLE_NAME || ';' INTO maxid;
    IF
        maxid IS NOT NULL THEN
            raise notice '%',
            'set sequence ' || TABLE_NAME || '_id_seq  restart with ' || maxid;
        EXECUTE 'alter sequence ' || TABLE_NAME || '_id_seq  restart with ' || maxid || ';';

    END IF;

END LOOP;

END $$;
```