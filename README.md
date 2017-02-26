# Test
GitHub练习
DECLARE
  P_R_DATE VARCHAR2(100) := '2016-09-03'; --开始时间
  P_R_TIME VARCHAR2(100) := '09:24:23';
  P_O_DATE VARCHAR2(100) := '2016-09-03'; --结束时间
  P_O_TIME VARCHAR2(100) := '17:24:23';
  P_R_DATE_D DATE;
  P_O_DATE_D DATE;
  v_out NUMBER ;
  v_out2 NUMBER ;
  CURSOR CUR_DATA IS
    SELECT HOLIDAYDATE --两个日期间所有假期
      FROM (SELECT TO_CHAR(TO_DATE(P_R_DATE, 'yyyy/mm/dd') + LEVEL - 1,
                           'yyyy-mm-dd') HOLIDAYDATE
              FROM DUAL
            CONNECT BY LEVEL <=
                       FLOOR(TO_DATE(P_O_DATE, 'yyyy-mm-dd') -
                             TO_DATE(P_R_DATE, 'yyyy-mm-dd')) + 1
            INTERSECT
            SELECT HBH.HOLIDAYDATE HOLIDAYDATE FROM HRMPUBHOLIDAY HBH) TT;
            
BEGIN 
 v_out := KL_OA_FR_PKG.GET_TIME_1(P_R_DATE, P_R_TIME, P_O_DATE, P_O_TIME) ; 
 DBMS_OUTPUT.PUT_LINE(v_out); 
 FOR rcv_h IN CUR_DATA LOOP
   --初始化开始时间
   P_R_DATE_D := to_date(P_R_DATE,'yyyy-mm-dd'); --转换为date类型计算
   IF P_R_DATE = rcv_h.HOLIDAYDATE THEN --当开始日期是假期时，日期+1
     
     P_R_DATE_D := P_R_DATE_D + 1;
     P_R_TIME := '08:30:00';
   END IF ; 
     
   --初始化结束时间
   P_O_DATE_D := to_date(P_O_DATE,'yyyy-mm-dd'); --转换为date类型计算
   IF P_O_DATE = rcv_h.HOLIDAYDATE THEN --当结束日期是假期时，日期-1
       
     P_O_DATE_D := P_O_DATE_D - 1;
     P_O_TIME := to_char('17:30:00');
   END IF ;
   P_R_DATE := to_char(P_R_DATE_D,'yyyy-mm-dd');
   P_O_DATE := to_char(P_O_DATE_D,'yyyy-mm-dd');
  END LOOP;
  v_out2 := KL_OA_FR_PKG.GET_TIME_1(P_R_DATE, P_R_TIME, P_O_DATE, P_O_TIME) ; 
 DBMS_OUTPUT.PUT_LINE(v_out2); 
  
  DBMS_OUTPUT.PUT_LINE('---------------'); 
  DBMS_OUTPUT.PUT_LINE(P_R_DATE);
  DBMS_OUTPUT.PUT_LINE(P_R_TIME); 
  DBMS_OUTPUT.PUT_LINE(P_O_DATE);
  DBMS_OUTPUT.PUT_LINE(P_O_TIME);
  DBMS_OUTPUT.PUT_LINE('---------------'); 
  DBMS_OUTPUT.PUT_LINE(to_date(P_R_DATE,'yyyy-dd-mm'));
  DBMS_OUTPUT.PUT_LINE(P_R_TIME); 
  DBMS_OUTPUT.PUT_LINE(P_O_DATE);
  DBMS_OUTPUT.PUT_LINE(P_O_TIME);
END;
