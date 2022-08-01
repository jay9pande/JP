export ORACLE_HOME=`cat /etc/oratab|grep -v '^#'|grep -i \n |cut -d : -f2`
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
mkdir p1
cd p1
mkdir admin oradata
cd admin
mkdir pfile diag create
cd ..
cd oradata
mkdir control log archive data
cd
cp /home/oracle/CRD_pfk1  /home/oracle/p1/admin/pfile/initp1.ora
cd

export ORACLE_SID=p1
sqlplus / as sysdba <<EOF
startup pfile='/home/oracle/p1/admin/pfile/initp1.ora' nomount;
create database p1
datafile '/home/oracle/p1/oradata/data/system01.dbf' size 400m
sysaux datafile '/home/oracle/p1/oradata/data/systemaux01.dbf' size 300m
undo tablespace undo01 datafile '/home/oracle/p1/oradata/data/undo01.dbf' size 100m
default temporary tablespace temp01 tempfile '/home/oracle/p1/oradata/data/temp01.dbf' size 100m
logfile group 1 '/home/oracle/p1/oradata/log/log01.log' size 10m,
        group 2 '/home/oracle/p1/oradata/log/log02.log' size 10m;
@?/rdbms/admin/catalog.sql;
@?/rdbms/admin/catproc.sql;
create table kf(id number(10));
create tablespace tbs datafile '/home/oracle/p1/oradata/data/tbs.dbf' size 500m;
alter database close;
alter database archivelog;
shut immediate;
!
export ORACLE_SID=p1
sqlplus / as sysdba   <<EOF
startup pfile='/home/oracle/p1/admin/pfile/initp1.ora';
create spfile from pfile='/home/oracle/p1/admin/pfile/initp1.ora';
EOF
