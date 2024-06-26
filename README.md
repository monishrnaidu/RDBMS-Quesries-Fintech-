# RDBMS-Quesries-Fintech-
A seasoned query list for a data/ business analyst to know in Fintech
# Fintech RDBMS Queries for data analyst and business analyst

## Introduction
* This update provides a collection of queries for the Warehouse Management System, enabling retrieval of various merchant and transaction details. These queries can be used for analysis, reporting, and operational tasks within the system.
 * Transaction success rates and failure reasons
 * Card details (limited for security purposes)
 * Merchant details including contact information, KYC documents, and industry classification
 * Payment gateway configurations and performance
 * Net banking transactions with breakdowns

## Concepts used 
* Filters
* Aggregate Functions
* Join
* Nested query concept 
* Wild card concept 
* Exact Match 
* Group by 
* Order by
* Time stamp function

## Queries 
**To fetch Credit Card Details**
`select id, left(cardnumber,6)
from Table 1
where id in ()`
 
**To fetch merchant identification numbers**
`select merchantid, mid
from warehouse.table1 where mid in ()`
 
**To fetch merchant deatils x payment mode x gross monthly value**
`select merchantid,
paymentmode,
bankname,
sum(amount)
from warehouse.table 1
where txtime > '2021-05-01'
and txStatus = 'SUCCESS'
and merchantid = '7531'
group by bankname, merchantid, paymentmode`
 
**To Find out reason transactions failure reason** 
`select pg,c.merchantid,pg,m.mid,txmsg,transactionid,(100.00*count(case when txStatus = 'SUCCESS' THEN txStatus END)/count(*)) as SR,
count(case when txStatus = 'SUCCESS' then txStatus end) as Success_Count,
count(case when txStatus = 'FAILED' then txStatus end) as Failed_Count,
count(case when txStatus = 'INCOMPLETE' then txStatus end) as Incomplete_Count,
count(case when txStatus = 'PENDING' then txStatus end) as Pending_Count,
count(case when txStatus = 'USER_DROPPED' then txStatus end) as User_Dropped_Count,count(*) as Total_Count
from warehouse.Table 1  c join warehouse.table1 as m on c.credid=m.id join warehouse.merchants t on c.merchantid=t.id
where  txtime >= '2024-04-01 00:00' and m.mid in ('CASH_LEMONNA_SEC','CASH_MARKWOL_SEC','CASH_STOXBOX_SEC')
group BY 1,2,3,4,5,6
order by Total_Count DESC`
 
**MTD Across Net Banking, Cards and GooglePay**
`Select pg,sum(amount)/10000000 as SUCCESS_GMV,(100.00 * SUM(amount) / SUM(SUM(amount)) OVER ()) AS contribution_percentage
FROM warehouse.table 1 as a
LEFT JOIN warehouse.table1 reg ON reg.merchantid = a.merchantid
WHERE
txtime >= date_trunc('month', current_date)
AND txtime < CURRENT_DATE
AND txstatus = 'SUCCESS'
AND paymentmode = 'UPI'
AND regval NOT IN ('5912', '5816', '5122')  and pg like '%upi'
AND reg.regkey = 'mcc' 
group by 1 order by SUCCESS_GMV desc'`
 
**To find the card network VISA, Master, Amex**
`Select *
from  warehouse.table 1
where txstatus = 'SUCCESS'
and txtime >=  current_date - 90
and cardtype in ('Diners', 'diners')
and merchantid in ('131051')`
 
**Date of Incorporation**
`Select merchantid,
max(case when regKey = 'dateOfBirth' then regVal end) as DateofBirth,
max(case when regKey = 'dateOfIncorporation' then regVal end) as dateofincorporation
From warehouse.table1
where merchantid in ({MID})
group by 1`
 
**Configuration details from inception** 
`Select
merchanid,
sum(amount) AS total_amount,
pg
from warehouse.table 1
where
txstatus = 'SUCCESS'
AND txtime >= current_date
AND txtime < current_timestamp
AND paymentmode = 'UPI'
GROUP BY
merchantid, pg;`
 
**Gross Monthly Value via merchant Identifications number**
`select
t.merchantid,
g.name,
gc.identifier,
SUM(t.amount) AS gmv
FROM warehouse.table 1 t
JOIN table1.table1cred gc ON t.credid = gc.productcredid
LEFT JOIN table1.table1 g
ON gc.table1id = g.id
WHERE gc.identifier in ('*********')
and txstatus='SUCCESS'
GROUP BY
t.merchantid,
g.name,
gc.identifier`
 

**To fetch details from Card Bank Identification Number** 
`select mid
from warehouse.Table 1 cf
join warehouse.table1 mpc
on cf.credid = [mpc.id](http://mpc.id/) where [cf.id](http://cf.id/)=********
select id,merchantid from CFTransaction
where paymentmode like '%CARD%'
and txtime > '2021-08-30'
and left(cardnumber,6) = 416090;`
 
**To fetch GooglePay volumes **
`select bankname,count(*)
from Table 1 where txtime > '2021-07-13'
and paymentmode = 'UPI'
and txStatus = 'SUCCESS'
and merchantid = 15315
group by bankname`
 
  
**To fetch Payment Gateway, Payment modes, Volumes**
select
 `a.merchantid,
 a.paymentmode,
[ a.pg](http://a.pg/)
 sum(a.amount) as gmv,
 b.mid,
 b.pgname,
 b.isactive,
 b.addedon
 from warehouse.table 1 a
 left join warehouse.table1 b
 on a.merchantid = b.merchantid
 where a.paymentmode = 'UPI' and b.pgname in ('yesupi','indusupi','iciciupi') and b.isactive = '1' and a.txtime >= '2021-09-01' and a.txStatus = 'SUCCESS' and a.merchantid = '116092'
 group by a.merchantid, a.paymentmode, b.mid, b.pgname, b.addedon, b.isactive, [a.pg](http://a.pg/)`

**To fetch Payment Gateway** 
`select
r.merchantid,
p.paymentGroup,
p.display,
r.fixedFee,
r.tdr,
r.pg1,
r.pg2
from warehouse.PaymentModeRates as r join warehouse.PaymentModes as p on r.modeId = p.id
where r.merchantid in ()`
 
**To fetch Success Rate of transactions**
`Select
merchantid,
pg,
paymentmode,
cardtype,
count(case when txStatus = 'SUCCESS' then txStatus end) as Success_Count,
count(case when txStatus = 'USER_DROPPED' then txStatus end) as User_Dropped_Count,count(*) as Total_Count,
(100.00*count(case when txStatus = 'SUCCESS' THEN txStatus END)/count(*)) as SR
from warehouse.table 1
where  txtime >= '2021-09-14 00:00'
and merchantid in ('48428','89648')
group by merchantid,pg,paymentmode,cardtype 
order by Total_Count DESC`
 
**To fetch Payment Failure Error message** 
`Select
merchantid,
paymentmode,
pg,
txStatus,
txMsg as MESSAGE,
count(*) from warehouse.Table 1
where txStatus <> 'SUCCESS'
and txTime >= '2021-08-01 00:00:00'
and txTime <= '2021-08-31 00:00:00'
and merchantid=120995
group by 1,2,3,4,5
order by 5 desc`
 
 
**Refund Details**
`select merchantid from TransactionRefunds
where addedOn >= '2022-01-01' and refundNote = ('triggered by Shopify') group by merchantidConversion rate - 0.95% - visa , master - 0.30%, rupay -1.95%`
 
**Merchant Detail**
`select merchantid,
max(case when regKey = 'merchantName' then regVal end) as merchantName,
max(case when regKey = 'regName' then regVal end) as legalname,
max(case when regKey = 'merchantType' then regVal end) as category,
max(case when regKey = 'mcc' then regVal end) as mcc,
max(case when regKey = 'website' then regVal end) as website,
max(case when regKey = 'companyPan' then regVal end) as companyPan,
max(case when regKey = 'gstin' then regVal end) as gstin,
max(case when regKey = 'address' then regVal end) as address,
max(case when regKey = 'city' then regVal end) as city,
max(case when regKey = 'state' then regVal end) as state,
max(case when regKey = 'postalCode' then regVal end) as postalCode,
max(case when regKey = 'merchantType' then regVal end) as merchantType,
max(case when regKey = 'industryType' then regVal end) as industryType,
max(case when regKey = 'addressLine1' then regVal end) as addressLine1,
max(case when regKey = 'addressLine2' then regVal end) as addressLine2,
max(case when regKey = 'cityGST' then regVal end) as cityGST,
max(case when regKey = 'stateGST' then regVal end) as stateGST,
max(case when regKey = 'cin' then regVal end) as cin,
max(case when regKey = 'ownerPan' then regVal end) as ownerPan,
max(case when regKey = 'ownerAadhar' then regVal end) as ownerAadhar,
max(case when regKey = 'accountNumber' then regVal end) as accountNumber,
max(case when regKey = 'ifscCode' then regVal end) as ifscCode,
max(case when regKey = 'accountHolder' then regVal end) as accountHolder,
max(case when regKey = 'invoiceGSTIN' then regVal end) as invoiceGSTIN,
max(case when regKey = 'bankName' then regVal end) as bankName,
max(case when regKey = 'accountType' then regVal end) as accountType
from warehouse.Table1 join warehouse.Merchants on Table1.merchantid = Merchants.id
where Table1.merchantid in ('72515','2739')
group by 1;`
 

**Monthly Transactions  report** 
`select a.merchantid,a.paymentmode,
max(case when regkey = 'merchantName' then regval end) as Name,
max(case when regkey = 'merchantType' then regval end) as Category,
max(case when regkey = 'mcc' then regval end) as mcc,
max(case when regkey = 'website' then regval end) as website,
a.pg,
a.mid,
a.date,
a.gmv,
a.txns
from
(select t.merchantid,
paymentmode,
pg,
pgc.mid as mid,
date(txtime) as date,
sum(amount) as gmv,
count(amount) as txns
from warehouse.table 1 t join warehouse.table1 pgc on t.credid = pgc.id
where txstatus = 'SUCCESS'
and txtime > DATEADD(day, -400, CAST(CAST(GETDATE() AS DATE) AS DATETIME))
and pg like ('%kotak%')
and pgc.mid = ('****')
group by 1, 2, 3, 4, 5) a join warehouse.table1 reg on reg.merchantid = a.merchantid
group by 1, 2, 7, 8, 9, 10, 11;`
 
 
**Terminals Testing report**
`select
distinct(merchantid),
max (case when pgname = 'cybyesapi' then date(addedon) end) as cybyesapi_addedon,
max(case when pgname = 'yescard' then date(addedon) end) as yescard,
max (case when pgname = 'sbicard' then date(addedon) end) as sbicard_addedon,
max (case when pgname = 'imslcard' then date(addedon) end) as imslcard_addedon,
max (case when pgname = 'hdfcfss' then date(addedon) end) as hdfcfss_addedon,
max (case when pgname = 'kotakcard' then date(addedon) end) as kotakcard_addedon,
max (case when pgname = 'kotakcardv2' then date(addedon) end) as kotakcardv2_addedon,
max (case when pgname = 'cybhdfcapi' then date(addedon) end) as cybhdfcapi_addedon,
max (case when pgname = 'paynext' then date(addedon) end) as paynext_addedon,
max (case when pgname = 'bobcard' then date(addedon) end) as bobcard_addedon,
max (case when pgname = 'cybhdfc' then date(addedon) end) as cybhdfc_addedon,
max (case when pgname = 'sfpdirectcard' then date(addedon) end) as safexpay_addedon,
max (case when pgname = 'techprocesscard' then date(addedon) end) as techprocesscard_addedon,
max (case when pgname = 'hdfcnb' then date(addedon) end) as HDFC,
max (case when pgname = 'icicinb' then date(addedon) end) as ICICI,
max (case when pgname = 'sbinb' then date(addedon) end) as SBI,
max (case when pgname = 'kotaknb' then date(addedon) end) as Kotak,
max (case when pgname = 'kotaknbv2' then date(addedon) end) as Kotakv2,
max (case when pgname = 'yesnb' then date(addedon) end) as YES,
max (case when pgname = 'yesnbv2' then date(addedon) end) as YESv2,
max (case when pgname = 'idfcnb' then date(addedon) end) as IDFC,
max (case when pgname = 'federalnbv2' then date(addedon) end) as FEDERAL,
max (case when pgname = 'billdesknb' then date(addedon) end) as Billdesk,
max(case when pgname = 'yesupi' then date(addedon) end) as yes,
max(case when pgname = 'iciciupi' then date(addedon) end) as icici,
max(case when pgname = 'indusupi' then date(addedon) end) as indus
from warehouse.table1
where isactive = 1
and id not in (select distinct id from warehouse.table1 where mid IN
((select distinct mid from warehouse.table1 where merchantid = 32  and isactive = 1) UNION
(select distinct mid from warehouse.table1 where id in (select distinct credid from warehouse.masterpgcreds where isactive = 1))
) UNION (select 0) )
and merchantid = ('1848')
group by 1;`
 
**Last 90 days transactions**
`select a.*,
max(case when regkey = 'merchantName' then regval end) as Name,
max(case when regkey = 'merchantType' then regval end) as Category,
max(case when regkey = 'mcc' then regval end) as mcc
from (select t.merchantid,
paymentmode,
pg,
pgc.mid as mid,
sum(amount) as gmv
from warehouse.table 1 t join warehouse.table1 pgc on pgc.id = t.credid
where txstatus = 'SUCCESS'
and paymentmode like '%CARD%'
and date(txtime) >= '2022-06-01'
and date(txtime) >= '2022-07-01'
-- and date(txtime) >= DATEADD(day, -90, CAST(CAST(GETDATE() AS DATE) AS DATETIME))
and t.merchantid in ('174414', '226893')
group by 1, 2, 3, 4) a join warehouse.table1 reg on reg.merchantid = a.merchantid
group by 1, 2, 3, 4, 5;`
 
 
**Transaction level data with merchant details**
`select a.*,
max(case when regkey = 'merchantType' then regval end) as Category,
max(case when regkey = 'merchantName' then regval end) as Name,
max(case when regkey = 'mcc' then regval end) as mcc from
(select merchantid, paymentmode, pg, sum(amount)
from warehouse.table 1
where pg like '%icici%'
and txtime > '2022-06-01'
and txtime < '2022-07-25'
and txStatus = 'SUCCESS'
group by merchantid, paymentmode, pg) a left join warehouse.table1 reg
on reg.merchantid = a.merchantid
group by 1, 2, 3, 4`
 
 
**Net Banking Details**
`select
max(case when regKey = 'merchantType' then regVal end) as category,
max(case when regKey = 'mcc' then regVal end) as mcc,
pmr.merchantid,
pmr.tdr,
pmr.fixedfee,
mpg.pgname,
mpg.mid
from warehouse.table1 mpg
join warehouse.paymentmoderates pmr
on mpg.merchantid = pmr.merchantid
join warehouse.table1 mreg on mpg.merchantid = mreg.merchantid
where pgname like '%nb%'
group by 3, 4, 5, 6, 7
having category in ('Logistics','Online Gaming', 'Jewellery', 'Ecommerce')`
 
 
**Monthly Auditing Data**
`select
max(case when regKey = 'merchantType' then regVal end) as category,
max(case when regKey = 'orgType' then regVal end) as Business_Type,
max(case when regKey = 'mcc' then regVal end) as mcc,
max(case when regKey = 'website' then regVal end) as website,
max(case when regKey = 'merchantName' then regVal end) as merchantName,
max(case when regKey = 'companyPan' then regVal end) as company_pan,
mpg.mid,
mpg.merchantid, 
mpg.pgname,
mpg.isactive,
mpg.param1,
mpg.param3,
mpg.addedon
from warehouse.table1 mpg
join warehouse.table1 mreg
on mpg.merchantid = mreg.merchantid
where mpg.isactive = '1' and
mpg.pgname like '%kotakupi%'
group by 7,8,9,10,11,12,13;`
 
 
**Documents availability in Admin Dashboard**
`select merchantid,
max(case when doctype = 'bizpan' and md.isapproved = 1 then docname end) as CompanyPan,
max(case when doctype = 'gst' and md.isapproved = 1 then docname end) as gstin,
max(case when doctype = 'cheque' and md.isapproved = 1 then docname end) as Cheque,
max(case when doctype = 'ownpan' and md.isapproved = 1 then docname end) as ownerPan,
max(case when doctype = 'ownadd' and md.isapproved = 1 then docname end) as Addressproof,
max(case when doctype = 'cin' and md.isapproved = 1 then docname end) as CIN,
max(case when doctype = 'invoiceMoa' and md.isapproved = 1 then docname end) as MOA,
max(case when doctype = 'invoiceAoa' and md.isapproved = 1 then docname end) as AOA,
max(case when doctype = 'additionalfield2' and md.isapproved = 1 then docname end) as Additional_field_1,
max(case when doctype = 'additionalfield1' and md.isapproved = 1 then docname end) as Additional_field_2
from warehouse.merchantdocs md
where merchantid in ()
group by 1;`
