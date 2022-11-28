# test
1st project only for test

## To install
c'est just un test

Test d'image

![Cover](https://github.com/Redwass/test/blob/main/figures/gssb.jpg)


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
