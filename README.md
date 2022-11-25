# test
1st project only for test

## To install
c'est just un test

Test d'image

![Cover](https://github.com/Redwass/test/blob/main/figures/image.jpeg)


## 4. Query

Here is a list of SSB queries using Cypher Language Request

##### Q1.1

```sql
profile optional match (d:date{D_YEAR:1993})<-[r:order_date]-(l:lineorder)
where 1<= l.LO_DISCOUNT <=3
and l.LO_QUANTITY < 25
return sum(l.LO_REVENUE);
```
