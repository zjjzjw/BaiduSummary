


python commands/import.py --mode=icafebug

SELECT cursor_table.id AS cursor_table_id, cursor_table.cursor_name AS cursor_table_cursor_name, cursor_table.cursor_value AS cursor_table_cursor_value, cursor_table.type AS cursor_table_type 
FROM cursor_table 
WHERE cursor_table.type='bug' AND cursor_table.cursor_name='icafe_bug_last_modified_time'
 LIMIT 1

 465024,465023,465022