# ДЗ #7 Custom Resource Definitions. Operators (лекция #8)

## Проверка

### Заполнил базу созданного mysql-instance:
```bash
export MYSQLPOD=$(kubectl get pods -l app=mysql-instance -o jsonpath="{.items[*].metadata.name}")
kubectl exec -it $MYSQLPOD -- mysql -u root -potuspassword -e "CREATE TABLE test ( id smallint unsigned not null auto_increment, name varchar(20) not null, constraint pk_example primary key (id) );" otus-database
kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "INSERT INTO test ( id, name ) VALUES ( null, 'some data' );" otus-database
kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "INSERT INTO test ( id, name ) VALUES ( null, 'some data-2' );" otus-database
```

### Проверил, содержимое таблицы
```bash
export MYSQLPOD=$(kubectl get pods -l app=mysql-instance -o jsonpath="{.items[*].metadata.name}")
kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
```

## Необходимая информация для PR

### Вывод команд
```bash
kubectl get jobs
NAME                         COMPLETIONS   DURATION   AGE
backup-mysql-instance-job    1/1           2s         3m37s
restore-mysql-instance-job   1/1           47s        2m45s
```

### Содержимое БД
```
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+
```