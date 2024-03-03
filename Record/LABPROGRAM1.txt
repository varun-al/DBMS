SQL> CREATE TABLE BRANCH(
  2  BRANCH_ID VARCHAR(10),
  3  BANK_NAME VARCHAR(15),
  4  BRANCH_NAME VARCHAR(20),
  5  ASSETS INT NOT NULL,
  6  PRIMARY KEY(BRANCH_ID)
  7  );
Table created.

SQL> DESC BRANCH;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 BRANCH_ID                                 NOT NULL VARCHAR2(10)
 BANK_NAME                                          VARCHAR2(15)
 BRANCH_NAME                                        VARCHAR2(20)
 ASSETS                                    NOT NULL NUMBER(38)

SQL> CREATE TABLE CUSTOMER(
  2  CUSTOMER_ID VARCHAR(10),
  3  CUSTOMER_NAME VARCHAR(20),
  4  CUSTOMER_AGE INT,
  5  CUSTOMER_ADDRESS VARCHAR(20),
  6  CUSTOMER_PHONE INT,
  7  PRIMARY KEY(CUSTOMER_ID)
  8  );

Table created.

SQL> DESC CUSTOMER;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 CUSTOMER_ID                               NOT NULL VARCHAR2(10)
 CUSTOMER_NAME                                      VARCHAR2(20)
 CUSTOMER_AGE                                       NUMBER(38)
 CUSTOMER_ADDRESS                                   VARCHAR2(20)
 CUSTOMER_PHONE                                     NUMBER(38)

SQL> CREATE TABLE ACCOUNT(
  2  ACC_NO INT,
  3  BRANCH_ID VARCHAR(10),
  4  ACCOUNT_TYPE VARCHAR(10),
  5  ACCOUNT_BALANCE INT,
  6  CUSTOMER_ID VARCHAR(10),
  7  PRIMARY KEY(ACC_NO),
  8  FOREIGN KEY (BRANCH_ID) REFERENCES BRANCH(BRANCH_ID) ON DELETE CASCADE,
  9  FOREIGN KEY(CUSTOMER_ID) REFERENCES CUSTOMER(CUSTOMER_ID) ON DELETE CASCADE
 10  );
Table created.

SQL> DESC ACCOUNT;
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 ACC_NO                                    NOT NULL NUMBER(38)
 BRANCH_ID                                          VARCHAR2(10)
 ACCOUNT_TYPE                                       VARCHAR2(10)
 ACCOUNT_BALANCE                                    NUMBER(38)
 CUSTOMER_ID                                        VARCHAR2(10)

SQL> CREATE TABLE LOAN(
  2  LOAN_NUMBER VARCHAR2(5),
  3  BRANCH_ID VARCHAR(10),
  4  AMOUNT INT,
  5  CUSTOMER_ID VARCHAR(10),
  6  PRIMARY KEY(LOAN_NUMBER),
  7  FOREIGN KEY (BRANCH_ID) REFERENCES BRANCH(BRANCH_ID) ON DELETE CASCADE,
  8  FOREIGN KEY(CUSTOMER_ID) REFERENCES CUSTOMER(CUSTOMER_ID) ON DELETE CASCADE);

Table created.
SQL> DESC LOAN;
 SQL> desc loan
 Name                                      Null?    Type
 ----------------------------------------- -------- ----------------------------
 LOAN_NUMBER                               NOT NULL VARCHAR2(5)
 BRANCH_ID                                          VARCHAR2(10)
 AMOUNT                                             NUMBER(38)
 CUSTOMER_ID                                        VARCHAR2(10)



 INSERT INTO BRANCH VALUES('B1',' CANARA','MANGALURU',60000000)

SQL> SELECT * FROM BRANCH;

BRANCH_ID  BANK_NAME       BRANCH_NAME              ASSETS
---------- --------------- -------------------- ----------
B1          CANARA         MANGALURU              60000000
B2         BANK OF BARODA  MANGALURU              70000000
B3         CANARA          KASARAGOD              50000000
B4         SBI             BENGALURU              30000000
B5         UNION BANK      DELHI                  20000000


INSERT INTO CUSTOMER VALUES('C1','RAVI',22,'MANGALURU',8745263);

CUSTOMER_I CUSTOMER_NAME        CUSTOMER_AGE CUSTOMER_ADDRESS     CUSTOMER_PHONE
---------- -------------------- ------------ -------------------- --------------
C1         RAVI                           22 MANGALURU                   8745263
C2         ASHA                           26 DELHI                      98745641
C3         VARUN                          23 KASARGOD                   78954623
C4         ARPITHA                        22 MANGALURU                   9856325
C5         SACHIN                         23 BENGALORE                  78541365

INSERT INTO ACCOUNT VALUES(123,'B1','SAVINGS',10000,'C1')

SQL> SELECT * FROM ACCOUNT;

    ACC_NO BRANCH_ID  ACCOUNT_TY ACCOUNT_BALANCE CUSTOMER_I
---------- ---------- ---------- --------------- ----------
       123 B1         SAVINGS              10000 C1
       456 B5         RECURRING            20000 C2
       789 B1         SAVINGS              30000 C1
      1122 B2         FD                    5000 C3
      1334 B1         SAVINGS              10000 C4
      1234 B3         FD                   90000 C5
      5876 B4         RECURRING            80000 C3



  INSERT INTO LOAN VALUES('L1','B1',500000,'C1');

LOAN_ BRANCH_ID      AMOUNT CUSTOMER_I
----- ---------- ---------- ----------
L1    B1             500000 C1
L2    B2              50000 C2
L3    B3              40000 C3
L4    B2             565000 C4
L5    B4             955000 C5
L6    B5              20000 C2


QUERY 1
Find all the customers who have at least one account at the “Mangaluru” branch.

SELECT C.CUSTOMER_ID,C.CUSTOMER_NAME FROM CUSTOMER C,ACCOUNT A,BRANCH B
WHERE B.BRANCH_NAME='MANGALURU'AND B.BRANCH_ID=A.BRANCH_ID 
AND A.CUSTOMER_ID=C.CUSTOMER_ID;

CUSTOMER_I CUSTOMER_NAME
---------- --------------------
C1         RAVI
C1         RAVI
C3         VARUN
C4         ARPITHA

QUERY 2
Find all the customers who have an account at all the branches located in a specific city.

SQL>  SELECT C.CUSTOMER_ID,C.CUSTOMER_NAME,A.ACCOUNT_BALANCE FROM 
      CUSTOMER C,ACCOUNT A
      WHERE C.CUSTOMER_ID=A.CUSTOMER_ID AND 
      ACCOUNT_BALANCE=(SELECT MAX(ACCOUNT_BALANCE)FROM ACCOUNT);

CUSTOMER_I CUSTOMER_NAME        ACCOUNT_BALANCE
---------- -------------------- ---------------
C5         SACHIN                         90000

QUERY 3

Retrieve the Customer name and loan amount of a customer who borrowed a loan more than 5,00,000.

SQL> SELECT C.CUSTOMER_NAME C,L.AMOUNT FROM CUSTOMER C,LOAN L 
     WHERE C.CUSTOMER_ID=L.CUSTOMER_ID AND AMOUNT>500000;

C                        AMOUNT
-------------------- ----------
ARPITHA                  565000
SACHIN                   955000

QUERY 4

Retrieve the details of bank branch with maximum and minimum assets among the various branches.

SQL> SELECT BANK_NAME,BRANCH_NAME,ASSETS FROM BRANCH
  2  WHERE ASSETS=(SELECT MAX(ASSETS)FROM BRANCH)
  3  UNION
  4  SELECT BANK_NAME,BRANCH_NAME,ASSETS FROM BRANCH
  5  WHERE ASSETS=(SELECT MIN(ASSETS)FROM BRANCH)
  6  ;

BANK_NAME       BRANCH_NAME              ASSETS
--------------- -------------------- ----------
BANK OF BARODA  MANGALURU              70000000
UNION BANK      DELHI                  20000000

QUERY 5

Demonstrate how you delete all account tuples at every branch located in a specific city.

DELETE FROM BRANCH WHERE BRANCH_NAME='DELHI';
