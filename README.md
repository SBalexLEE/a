# Part 1: Setting up Cassandra
# Part 2: Import Data into Cassandra


To import the access logs into Cassandra, we created one keyspace, mini_proj_3, and a table, access_log, in Cassandra. After importing the data, we ran simple query to imported data. 

### 1. Create the Keyspace
```
CREATE KEYSPACE mini_proj_3
    WITH replication = {'class': 'SimpleStrategy', 'replication_factor' : 1};
```

### 2. Use keyspace
```
use mini_proj_3;
```

### 3. Create access_log table

```
CREATE TABLE access_log (
	ip text,
	other_1 text,
	other_2 text,
	time text,
	time2 text,
	web_item text,
	other_3 text,
	other_4 text,	
	PRIMARY KEY(IP, time, web_item));
```

### 4. Copy data to table	
```
COPY access_log (IP , other_1 , other_2 , time , time2 , web_item , other_3, other_4) FROM '/home/ubuntu/dom/access_log' WITH DELIMITER=' ';
```

### 5. Get missing access_logs that didn't get copied in first round
```
ALTER TABLE access_log ADD other_5 text;
COPY access_log (IP , other_1 , other_2 , other_5, time , time2 , web_item , other_3, other_4) FROM '/home/ubuntu/dom/access_log' WITH DELIMITER=' ';	
```
### 6. Drop unneeded columns
```
ALTER table access_log DROP other_1;
ALTER table access_log DROP other_2;
ALTER table access_log DROP other_3;
ALTER table access_log DROP other_4;
ALTER table access_log DROP other_5;
ALTER table access_log DROP time2;
```
### 7. Create a new table for website items as first primary key to answer questions
```
CREATE TABLE website_item (
	ip text, 
	time text,
	other_1 text, 
	web_item text, 
	other_2 text, 
	PRIMARY KEY(web_item,ip,time,other_1,other_2));
```
### 8. Copy data from access_log and remove extra data from website item
```
COPY access_log (ip,time,web_item) TO 'web_item.csv' with DELIMITER = ' ' and QUOTE = ' ';
```
### 9. Copy back to table
```
COPY website_item (ip,time,other_1, web_item, other_2) FROM 'web_item.csv' WITH DELIMITER = ' ' and QUOTE = '"' and ESCAPE = '*';
```

### 10. View tables
```
SELECT * FROM access_log LIMIT 20;
SELECT * FROM website_item LIMIT 20;
```
![image](https://github.com/SBalexLEE/a/blob/main/Picture2.png)

![image](https://github.com/SBalexLEE/cloud_project3/blob/main/Picture7.png)

# Part 3: Operate Data in Cassandra 

### 1. Problem 1
```
SELECT count(*) FROM website_item WHERE web_item = '/assets/img/release-schedule-logo.png\';
```
![image](https://github.com/SBalexLEE/a/blob/main/Picture3.png)


### 2. Problem 2
```
SELECT count(*) FROM access_log WHERE ip = '10.207.188.188';
```
![image](https://github.com/SBalexLEE/a/blob/main/Picture4.png)

For porblem 3 & 4 we used the Cassandra Python driver (https://github.com/datastax/python-driver) to run from our vm cc-project-7 

### 3. Problem 3
```
python3 part3_prob3.py
```

![image](https://github.com/SBalexLEE/a/blob/main/Picture5.png)

### 4. Problem 4
```
python3 part3_prob4.py
```

![image](https://github.com/SBalexLEE/a/blob/main/Picture6.png)
