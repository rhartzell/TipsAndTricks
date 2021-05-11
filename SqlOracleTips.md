# Oracle Sql Tips and Examples

## Boiler plate cursor loop
```
DECLARE
    cursor verifications is (select id from verification where passed_activation_checks=0);   
BEGIN
   FOR rec IN verifications LOOP
    dbms_output.put_line(rec.id);
   END LOOP;

END;
```
## SQL Query with a running total column
```
SELECT usercount, 
       yearcreated, 
       Sum(usercount) OVER (ORDER BY yearcreated) AS total 
FROM  (SELECT Count(*) usercount, 
       Extract(year FROM createdate) yearcreated 
       FROM   ora_aspnet_membership 
       GROUP  BY Extract(year FROM createdate) 
       ORDER  BY Extract(year FROM createdate));
```

## Nested CASE statements
```
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
```
