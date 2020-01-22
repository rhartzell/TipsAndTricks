# Oracle Sql Tips and Examples

## Boilerplate cursor loop
```
DECLARE
  cursor myCursor is (select lastname, userid, age from user);
BEGIN
   FOR myCursor_rec IN myCursor  LOOP
    ...
      dbms_output.put_line('last name: ' || myCursor_rec.lastname);
   END LOOP;
END;
```

## Nested CASE statements
```BEGIN
   OPEN :pResultSet FOR
      SELECT pc.cdbcustomerid,
             CASE
                WHEN (pc.typeid = 1 OR pc.typeid = 2)
                THEN
                   (CASE
                       WHEN    (   pc.motor_passenger_carrier IS NULL
                                OR pc.motor_passenger_carrier =
                                      'Not Specified')
                            OR (   pc.inter_intra_state IS NULL
                                OR pc.inter_intra_state = 'Not Specified')
                            OR (   pc.discovered_psp_via IS NULL
                                OR pc.discovered_psp_via =
                                      'No Information Provided')
                       THEN
                          'No'
                       ELSE
                          'Yes'
                    END)
                ELSE
                   (CASE
                       WHEN    (   pc.discovered_psp_via IS NULL
                                OR pc.discovered_psp_via =
                                      'No Information Provided')
                            OR (pc.isp_estimated_customers = 0)
                            OR (pc.isp_annual_screenings = 0)
                       THEN
                          'No'
                       ELSE
                          'Yes'
                    END)
             END
                AS has_demographic_data
        FROM psp_customer pc
       WHERE pc.cdbcustomerid = 10668;
END;
```
