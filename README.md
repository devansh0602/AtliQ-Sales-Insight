# AtliQ-Sales-Insight

## Purpose
To unlock insights that are not visible before for sales team for decision support and automate them to reduced manual time spent in data gathering.

## End Result
An automated dashboard providing quick and latest sales insights in order to support data driven decision making.

## Success criteria
- Dashboard(s) uncovering sales order insights with latest data available. 
* Sales team able to make better decisions nad prove 10% cost savings of total spend.
+ Sales analysts stop data gathering manually in order to save 20% of their business time and reinvest it value added activity.

## Relationships between tables used:
![Screenshot (114)](https://github.com/devansh0602/AtliQ-Sales-Insight/assets/110840898/1aeea11c-f506-4b4e-a987-a87dcbc9bbfe)

## Power Query used while transforming the data:

### Customers table
1. Source = Csv.Document(File.Contents("F:\Data analyst folder\Sales Insights Tables\customers.csv"),[Delimiter=",", Columns=3, Encoding=1252, 
    QuoteStyle=QuoteStyle.None])
2. #"Changed Type" = Table.TransformColumnTypes(Source,{{"Column1", type text}, {"Column2", type text}, {"Column3", type text}}),
3. #"Made first rows as coumn names" = Table.PromoteHeaders(#"Changed Type", [PromoteAllScalars=true])

### Date table
1. Source = Csv.Document(File.Contents("F:\Data analyst folder\Sales Insights Tables\date.csv"),[Delimiter=",", Columns=5, Encoding=1252, 
    QuoteStyle=QuoteStyle.Csv])
2. #"Made first rows as coumn names" = Table.PromoteHeaders(Source, [PromoteAllScalars=true])
3. #"Changed Type" = Table.TransformColumnTypes(#"Made first rows as coumn names",{{"date", type date}, {"cy_date", type date}, {"year", Int64.Type},   
   {"month_name", type text}, {"date_yy_mmm", type date}})

### Markets table
1. Source = Csv.Document(File.Contents("F:\Data analyst folder\Sales Insights Tables\markets.csv"),[Delimiter=",", Columns=3, Encoding=1252, 
    QuoteStyle=QuoteStyle.None])
2. #"Changed Type" = Table.TransformColumnTypes(Source,{{"Column1", type text}, {"Column2", type text}, {"Column3", type text}})
3. #"Made first rows as coumn names" = Table.PromoteHeaders(#"Changed Type", [PromoteAllScalars=true])
4. #"Changed Type1" = Table.TransformColumnTypes(#"Made first rows as coumn names",{{"markets_code", type text}, {"markets_name", type text}, {"zone", type text}})
5. #"Removed unnecessary rows" = Table.SelectRows(#"Changed Type1", each ([markets_name] <> "New York" and [markets_name] <> "Paris"))

### Products table
1. Source = Csv.Document(File.Contents("F:\Data analyst folder\Sales Insights Tables\products.csv"),[Delimiter=",", Columns=2, Encoding=1252, 
    QuoteStyle=QuoteStyle.Csv])
2. #"Changed Type" = Table.TransformColumnTypes(Source,{{"Column1", type text}, {"Column2", type text}}),
3. #"Made first rows as coumn names" = Table.PromoteHeaders(#"Changed Type", [PromoteAllScalars=true])

### Transactions table
1. Source = Csv.Document(File.Contents("F:\Data analyst folder\Sales Insights Tables\transactions.csv"),[Delimiter=",", Columns=7, Encoding=1252, 
    QuoteStyle=QuoteStyle.Csv])
2. #"Made first rows as coumn names" = Table.PromoteHeaders(Source, [PromoteAllScalars=true])
3. #"Changed Type" = Table.TransformColumnTypes(#"Made first rows as coumn names",{{"product_code", type text}, {"customer_code", type text}, {"market_code", type 
     text}, {"order_date", type date}, {"sales_qty", Int64.Type}, {"sales_amount", Int64.Type}, {"currency", type text}})
4. #"Removed rows with -1/0 values in sales_amount" = Table.SelectRows(#"Changed Type", each ([sales_amount] <> -1 and [sales_amount] <> 0))
5. #"Added nrmlsd_sales_amt column" = Table.AddColumn(#"Removed rows with -1/0 values in sales_amount", "nrmlsd_sales_amt", each if [currency] = "INR" then 
     [sales_amount] else [sales_amount]*82)
6. #"Removed INR and USD" = Table.SelectRows(#"Added nrmlsd_sales_amt column", each ([currency] = "INR#(cr)" or [currency] = "USD#(cr)"))
