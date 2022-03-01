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

## Declared record and table types, dynamic sql, and iteration of immediate execution
```
declare
  type r is record (
   cust_id       number
   , org_name    varchar2(255)
   , create_date varchar2(60)
   , status_code varchar2(5)
  );

  TYPE T IS TABLE OF R;
  MYROWS T; -- plural because it's a table not a record variable

  v_sql       varchar2(32767);
begin
    v_sql := LinkQuery('select cust_id, org_name, create_date, status_code from cust@CDB where cust_id not in (select cdbcustomerid from psp_customer) order by create_date desc');
    dbms_output.put_line(v_sql);
    EXECUTE IMMEDIATE v_sql BULK COLLECT INTO MYROWS;
    DBMS_OUTPUT.PUT_LINE(' number of records =' || MYROWS.count());
    DBMS_OUTPUT.PUT_LINE('=========================');
    
    for i in 1..MYROWS.count 
    LOOP
        DBMS_OUTPUT.PUT_LINE('cust_id: ' || MYROWS(i).cust_id || ' create_date: ' || MYROWS(i).create_date || ' status_code: ' || MYROWS(i).status_code || ' org_name: ' || MYROWS(i).org_name);
    END LOOP;
    
end;
```
## Inserting records from multiple sources
```
-- simple example
INSERT INTO security_answer (userid, question_id, answer_hash, created_date)
  (SELECT userid,
          question_id,
          answer_hash,
          CREATED_DATE
     FROM (SELECT 'D91CA898D8937C7CE0530AAE1EAC2A80' USERID FROM DUAL),
          (select question_id, answer_hash from security_answer where userid='CF4C6242C1EB7AB7E0530BAE1FACAC58'),
          (SELECT sysdate created_date FROM DUAL));

-- complicated example
INSERT INTO AUDIT_TRANSACTION (ID,
                          AUDIT_ID,
                          CUSTOMER_ID,
                          AUTH_CODE,
                          AUDIT_STATUS,
                          AUDIT_STATUS_DESC,
                          created_by,
                          created_date)
(SELECT ID,
      AUDIT_ID,
      CUSTOMER_ID,
      AUTH_CODE,
      AUDIT_STATUS,
      AUDIT_STATUS_DESC,
      CREATED_BY,
      CREATED_DATE
 FROM (SELECT SYS_GUID () ID FROM DUAL),
      (SELECT vAuditId AUDIT_ID FROM DUAL),
      (SELECT cursor_customer.motor_carrier_id
                 CUSTOMER_ID
         FROM DUAL),
      (SELECT auth_code AS auth_code
         FROM (  SELECT auth_code
                   FROM driver_record_transaction
                  WHERE     TRUNC (request_date) >=
                               TRUNC (
                                  pAuditPeriodStartDate)
                        AND TRUNC (request_date) <=
                               TRUNC (pAuditPeriodEndDate)
                        AND motor_carrier_id =
                               cursor_customer.motor_carrier_id
               ORDER BY DBMS_RANDOM.VALUE)
        WHERE ROWNUM <= pMaxTransPerCust),
      (SELECT 1 AUDIT_STATUS FROM DUAL),
      (SELECT 'Open' AUDIT_STATUS_DESC FROM DUAL),
      (SELECT pCreatedBy CREATED_BY FROM DUAL),
      (SELECT SYSDATE CREATED_DATE FROM DUAL));

```
