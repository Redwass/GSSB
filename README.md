# Graph Star Schema Benchmark (GSSB)
The Star Schema Benchmark, or SSB, was designed to evaluate the performance of database systems for star schema data warehouse queries. The SSB schema is based on the [TPC-H benchmark] (http://www.tpc.org/tpch/), but in a modified form with LINEORDER as a central fact table and dimension tables for customer, part, supplier, and date. The queries are also based on the TPC-H queries, but the number of queries is reduced to 13 and have been chosen to cover as much of the SSB as possible, so that potential users can derive a performance evaluation of the weighted subset they expect to use in practice. They are divided into 4 subgroups according to the difficulty and the number of domensions involved. The idea is that the total number of fact table rows retrieved will be determined by the selectivity (i.e., total Filter Factor FF) of restrictions on dimensions. More details in [Paper Download](https://www.cs.umb.edu/~poneil/StarSchemaB.PDF)

We generated the same ssb schema using the graph approach called Graph Star Schema Benchmark as shown in the figure below. The 13 SSB queries were built using Cypher Language Request (CLR) specific to the Neo4j graph database.

![Cover](https://github.com/Redwass/test/blob/main/figures/gssb.jpg)

## 3. Platform, data and installation. 

To install the GSSB, use the graph database Neo4j version 4.x.x or higher. The data to be downloaded from [Download](https://github.com/Redwass/GSSB/tree/main/gssb_data) is in the CSV's files. The configuration and installation commands on Windows and Linux platforms are as follows:

### Neo4j configuration

This configuration is for 16,0 Go of RAM
#### Java Heap Size: 
dbms.memory.heap.initial_size=4100m

dbms.memory.heap.max_size=4100m

dbms.memory.pagecache.size=11940m

### For windows 
```cmd
neo4j-admin.bat import --database=gssb --nodes=lineorder=C:\gssb_data\lineorder-header.csv,C:\gssb_data\lineorder.csv --nodes=part=C:\gssb_data\part-header.csv,C:\gssb_data\part.csv --relationships=order_part=C:\gssb_data\order_part-header.csv,C:\gssb_data\order_part.csv
```
### For Linux 
```cmd
neo4j-admin import --database=gssb --nodes=lineorder=C:\gssb_data\lineorder-header.csv,C:\gssb_data\lineorder.csv --nodes=part=C:\gssb_data\part-header.csv,C:\gssb_data\part.csv --relationships=order_part=C:\gssb_data\order_part-header.csv,C:\gssb_data\order_part.csv
```

## 4. Queries

Here is a list of SSB queries using Cypher Language Request

##### Q1.1

```cypher
optional match (d:date{D_YEAR:1993})<-[r:order_date]-(l:lineorder)
where 1<= l.LO_DISCOUNT <=3
and l.LO_QUANTITY < 25
return sum(l.LO_REVENUE);
```

##### Q1.2

```cypher
optional match (d:date{D_YEARMONTHNUM:199401})<-[r:order_date]-(l:lineorder)
where 4<= l.LO_DISCOUNT <=6
and 26<= l.LO_QUANTITY <= 35
return sum(l.LO_REVENUE);
```

##### Q1.3

```cypher
optional match (d:date{D_YEAR:1994, D_WEEKNUMINYEAR:6})<-[r:order_date]-(l:lineorder)
where 5<= l.LO_DISCOUNT <=7
and 26<= l.LO_QUANTITY <= 35
return sum(l.LO_REVENUE);
```

##### Q2.1

```cypher
optional match (p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier),(d:date)<-[:order_date]-(l)
where p.P_CATEGORY= "MFGR#12"
and s.S_REGION = "AMERICA" 
return sum(l.LO_REVENUE),d.D_YEAR, p.P_BRAND1
ORDER BY d.D_YEAR, p.P_BRAND;
```

##### Q2.2

```cypher
optional match (p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier),(d:date)<-[:order_date]-(l)
where (p.P_BRAND >= "MFGR#2221" and p.P_BRAND <= "MFGR#2228")
and s.S_REGION = "ASIA" 
return sum(l.LO_REVENUE),d.D_YEAR, p.P_BRAND
ORDER BY d.D_YEAR, p.P_BRAND;
```

##### Q2.3

```cypher
optional match (p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier),(d:date)<-[:order_date]-(l)
where p.P_BRAND1 = "MFGR#2239" 
and s.S_REGION = "EUROPE" 
return sum(l.LO_REVENUE),d.D_YEAR, p.P_BRAND
ORDER BY d.D_YEAR, p.P_BRAND;
```

##### Q3.1

```cypher
optional match (c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(s:supplier)<-[:order_supplier]-(l)
where 1992<= d.D_YEAR <=1997
and c.C_REGION starts with "ASIA"
and s.S_REGION starts with "ASIA" 
return c.C_NATION, s.S_NATION, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC;
```

##### Q3.2

```cypher
optional match (c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(s:supplier)<-[:order_supplier]-(l)
where 1992<= d.D_YEAR <=1997
and c.C_NATION starts with "UNITED STATES" 
and s.S_NATION starts with "UNITED STATES" 
return c.C_CITY, s.S_CITY, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC;
```

##### Q3.3

```cypher
optional match (c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(s:supplier)<-[:order_supplier]-(l)
where 1992<= d.D_YEAR <=1997
and (c.C_CITY = "UNITED KI1" or c.C_CITY = "UNITED KI5") 
and (s.S_CITY = "UNITED KI1" or s.S_CITY = "UNITED KI5") 
return c.C_CITY, s.S_CITY, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC;
```

##### Q3.4

```cypher
optional match (c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(s:supplier)<-[:order_supplier]-(l)
where (c.C_CITY = "UNITED KI1" or c.C_CITY = "UNITED KI5") 
and (s.S_CITY = "UNITED KI1" or s.S_CITY = "UNITED KI5") 
and d.D_YEARMONTH = "Dec1997"
return c.C_CITY, s.S_CITY, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC;
```

##### Q4.1

```cypher
optional match (c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part),(d:date)<-[:order_date]-(l)-[:order_supplier]->(s:supplier)
where c.C_REGION = "AMERICA" 
and (p.P_MFGR = "MFGR#1" or p.P_MFGR = "MFGR#2")
and s.S_REGION = "AMERICA"
return d.D_YEAR, c.C_NATION, (sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as supplycost
ORDER BY d.D_YEAR, c.C_NATION;
```

##### Q4.2

```cypher
optional match (c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part),(d:date)<-[:order_date]-(l)-[:order_supplier]->(s:supplier)
where c.C_REGION starts with "AMERICA"
and (d.D_YEAR = 1997 or d.D_YEAR = 1998)
and (p.P_MFGR = "MFGR#1" or p.P_MFGR = "MFGR#2")
and s.S_REGION = "AMERICA"
return d.D_YEAR, s.S_NATION, p.P_CATEGORY,(sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as profit
ORDER BY d.D_YEAR, s.S_NATION, p.P_CATEGORY;
```

##### Q4.3

```cypher
optional match (c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part),(d:date)<-[:order_date]-(l)-[:order_supplier]->(s:supplier)
where (d.D_YEAR = 1997 or d.D_YEAR = 1998)
and c.C_REGION = "AMERICA"
and p.P_CATEGORY = "MFGR#14"
and s.S_NATION = "UNITED STATES"
return d.D_YEAR, s.S_CITY, p.P_BRAND,(sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as supplycost
ORDER BY d.D_YEAR, s.S_CITY, p.P_BRAND;
```
