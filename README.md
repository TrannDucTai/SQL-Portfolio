# SQL-Portfolio
-- vấn đề 1 tìm ra số lượng sản phẩm và combo có trong mỗi giỏ hàng 
-- vấn đề 2 tìm ra số lượng combo có khả năng mua nhiều nhất 
WITH cte_1 AS (
    SELECT
        salesorderid,
        productid
    FROM
        sales.salesorderdetail
),
cte_2 AS (
    SELECT
        a.SalesOrderID,
        a.productID AS p1,
        B.ProductID AS p2
    FROM
        cte_1 AS a
        JOIN cte_1 AS b ON a.SalesOrderID = b.SalesOrderID
    WHERE
        a.ProductID < b.ProductID and a.ProductID-b.ProductID <>0
      
)
	select	p1
			,p2
	, COUNT(*) AS CNT 
	from cte_2 
	GROUP BY p1,p2
	order by CNT desc
-- vấn đề 3 : chọn ra các cặp hàng hóa ở những rổ có số lượng cặp từ 2 đến 10 
with cte_1 as (
  select 
    SalesOrderID, 
    count (distinct ProductID) as Num_product  
  from 
    sales.SalesOrderDetail 
  group by 
    SalesOrderID 
  having count ( distinct productID) between 2 and 10 

), 
cte_2 as (
  select 
    a.SalesOrderID, 
    a.ProductID as p1, 
    b.ProductID as p2 
  from 
    sales.SalesOrderDetail as a 
    join sales.SalesOrderDetail as b on a.SalesOrderID = b.SalesOrderID 
  where 
    a.ProductID < b.ProductID 
    and a.ProductID - b.ProductID <> 0
) 
select top 10
  c.p1, 
  c.p2, 
  count (*) as count_combo 
--into bangnhap
from 
  cte_2 as c 
  join cte_1 as d on c.SalesOrderID = d.SalesOrderID 
group by 
  c.p1, 
  c.p2 
order by 
  count(*) desc
-- vấn đề 4 : tìm xem từ tất cả các giỏ hàng, cặp sản phầm nào được xuất hiện nhiều nhất 
with cte_1 as (
  select 
    distinct a.SalesOrderID, 
    c.Name, 
    c.ProductSubcategoryID as productid 
  from 
    sales.SalesOrderDetail as a 
    join Production.Product as b on a.ProductID = b.ProductID 
    join Production.ProductSubcategory as c on b.ProductSubcategoryID = c.ProductSubcategoryID 
    join Production.ProductCategory as d on d.ProductCategoryID = c.ProductCategoryID
) 
select 
  ct1.name as name1, 
  ct2.name as name2, 
  count(*) as count 
from 
  cte_1 as ct1 
  join cte_1 as ct2 on ct1.SalesOrderID = ct2.SalesOrderID 
  and ct1.ProductID < ct2.ProductID 
group by 
  ct1.name, 
  ct2.name 
order by 
  count desc
