# Intrusion Detection

At this point, it really seemed easy to setup a working Hive environment on top of our existing cluster and it only took a few hours of research and implementation with minimal errors.

But when it came to re-implementing the intrusion detection system, it was even easier. We didn't have to deal with a low-level map-reduce but rather a simple high-level querying language using HiveQL.

## Queries

We have used the same cleaned data that we had in our previous project and simply ran the following queries. The table creation process was a bit tedious but not as much as implementing map-reduce.

We create our initial table holding the data.

```
create external table initial(id INT, dur BIGINT, proto STRING, service STRING, state STRING, spkts INT, dpkts INT, sbytes INT, dbytes BIGINT, rate STRING, sttl INT, dttl INT, sload STRING, dload STRING, sloss INT, dloss INT, sinpkt BIGINT, dinpkt BIGINT, sjit BIGINT, djit BIGINT, swin INT, stcpb STRING, dtcpb STRING, dwin INT, tcprtt STRING, synack STRING, ackdat STRING, smean INT, dmean BIGINT, trans_depth INT, response_body_len INT, ct_srv_src INT, ct_state_ttl INT, ct_dst_ltm INT, ct_src_dport_ltm INT, ct_dst_sport_ltm INT, ct_dst_src_ltm INT, is_ftp_login INT, ct_ftp_cmd INT, ct_flw_http_mthd INT, ct_src_ltm INT, ct_srv_dst INT, is_sm_ips_ports INT, attack_cat STRING, label INT, actual INT ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' location '/datahive/';
```

We then create a new table clustered by the *proto* attribute that is clustered into 3 buckets stored as [Optimized Row Columnar (OCR)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC) format

```
create external table clustered( id INT, dur BIGINT, proto STRING, service STRING, state STRING, spkts INT, dpkts INT, sbytes BIGINT, dbytes BIGINT, rate STRING, sttl INT, dttl INT, sload STRING, dload STRING, sloss INT, dloss INT, sinpkt BIGINT, dinpkt BIGINT, sjit BIGINT, djit BIGINT, swin INT, stcpb STRING, dtcpb STRING, dwin INT, tcprtt STRING, synack STRING, ackdat STRING, smean INT, dmean BIGINT, trans_depth INT, response_body_len INT, ct_srv_src INT, ct_state_ttl INT, ct_dst_ltm INT, ct_src_dport_ltm INT, ct_dst_sport_ltm INT, ct_dst_src_ltm INT, is_ftp_login INT, ct_ftp_cmd INT, ct_flw_http_mthd INT, ct_src_ltm INT, ct_srv_dst INT, is_sm_ips_ports INT, attack_cat STRING, label INT, actual INT ) clustered by (proto) into 3 buckets stored as orc TBLPROPERTIES ('transactional'='true');
```

We then insert our data into the clustered table.

``` 
From initial insert into clustered select *;
```

We then use an over-simplified form of our existing decision tree cases to compute the predictions and update them onto the table.

``` 
UPDATE clustered SET prediction = ( CASE 
	WHEN dur = 0 AND ( proto = 'tcp' or proto = 'udp') AND sbytes < 109 THEN 0
	WHEN dur < 0.16 AND sbytes > 201 AND ( proto = 'tcp' or proto = 'udp') THEN 1
	ELSE 1
END);
```

We finally create the table that will hold our computation for confusion matrice by mapping each label to the prediction.

```
create table results (c String) clustered by (c) into 3 buckets stored as orc TBLPROPERTIES ('transactional'='true');

from clustered insert into results Select concat(label, ',', prediction);
```

Running the commands only took about a minute and yielded the following results:

```
20691    0,0
15684    0,1
7415     1,0
38541    1,1
```

We had a lower accuracy of 54.19% compared to 64.29% on the previous project, but it solely due to our use of more flexible rules when prediction intrusions.
