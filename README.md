# Report
Setting up Cassandra: 

We were given a set of three VMs, we used the key to SSH into the VMs, made sure that correct versions of java and python were installed on the machines, then we generated keys and put those into authorized users folder such that all the VMs can SSH into each other without specifying the key each time, and then we went on to install Cassandra from the documentation, 

 

We added the keyrings using the following command 

 
```
echo "deb [signed-by=/etc/apt/keyrings/apache-cassandra.asc] https://debian.cassandra.apache.org 41x main" | sudo tee -a /etc/ 
```
 

Then we added the keys from the URL using the CURL command, 
```
curl -o /etc/apt/keyrings/apache-cassandra.asc https://downloads.apache.org/cassandra/KEYS 
```
 

And now we install Cassandra onto our VMs, 
```
sudo apt install cassandra 
```
 

We also specify the IPs in the 
```
/etc/hosts file, 
```
 

 

Now that we have Cassandra installed on our VMs, we go on to configure the cluster by specifying the requirements in the /etc/Cassandra/Cassandra.yaml file by modifying the timeouts and setting the seeds, IP addresses accordingly  

 

 

Now that we have cassandra installed and setup, we will load and test with a toy database,  

We first stop the service using the following command, 
```
sudo service cassandra stop 
```
And now we start the service on all the three nodes, 
```
sudo service cassandra start 

 ```

We can check the status of nodes using the following command, 
```
nodetool status
```

# Part 2
Since the data given to us is in a Simple Log Format, we upload it into the cluster using the scp command at 500 Kbps and then we preprocess it into a CSV using the python script included, now that we have the CSV, we need to load this data into cassandra, for that we wil need to create a keyspace and table which has same columns as that of the csv, we start cassandra and create a keyspace and table with corresponding attributes, 

We run the script using python3 as follows
```
python3 script.py
```
This python script will take in the raw data and give out a proper csv that can be loaded into Cassandra using dsbulk


To load the data to the tables we use the following command
```
dsbulk load –url /home/ubuntu/resized_small_log.csv -k accesslogs –t logs –header true –h ‘CC1,CC2’ --connector.csv.maxCharsPerColumn 20000 
```
Generically,
```
dsbulk load –url <path_to_csv> -k <keyspacename> –t <tablename> –header true –h ‘<seeds_ip1>,<seeds_ip2>’ --connector.csv.maxCharsPerColumn 20000 
```
This will load the data into the tables 
![alt text](https://github.com/rohith1125/report/blob/main/ss.png)
# Part 3

Query 1:
Since we are using like we need to create a SASI index which would optimize the query,
```sql
CREATE INDEX url_index ON accesstogs. logs (url) USING 'org.apache.cassandra.index . sasi.SASIIndex'
WITH OPTIONS ={
mode': 'PREFIX'}
```
```sql
select count(*) from accesslogs.logs where url like '/administrator/index.php' ; 
```
 Query 2:
 Since we are using like we need to create a SASI index which would optimize the query,
```sql
CREATE INDEX ip_address ON accesslogs. logs (ip_address) USING 'org.apache.cassandra.index . sasi.SASIIndex'
WITH OPTIONS ={
mode': 'PREFIX'};
```
```sql
select from accesslogs.logs where ip_address like '96.32.128.51’ ; 
```
Query 3:
We would use cassandra driver to write a python script, 
The images and screenshots are included in the word/pdf provided in the package
```
python3 three.py
```

Query 4:
We would use cassandra driver to write a python script, 
```
python3 four.py
```

Query 5:
We create SASI index as follows,
```sql
CREATE CUSTOM INDEX user_agent_sasl ON accesslogs. logs (user_agent) USING 'org.apache.cassandra.lndex.sas1.SASIIndex' W:
TH OPTIONS = (
' mode': 'CONTAINS' ,
' analyzer_ctass• : .org . apache. cassandra.index. sasi . analyzer. StandardAna1yzer' ,
'case sensitive' :
' false '
```
```sql
SELECT COUNT(*) FROM accesslogs.logs WHERE user_agent LIKE ‘Mozilla/%’
```

Query 6:
We would use cassandra driver to write a python script, 
```
python3 sixone.py
```

Query 7:

```sql
SELECT COUNT(*) FROM accesslogs.logs WHERE response_size <=404 ALLOW FILTERING;
```

Query 8:
We would use cassandra driver to write a python script, 
```
python3 eight.py
```

