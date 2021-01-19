# Importance of Data Quality

Data quality is becoming more and more important in today's enterprise business world as companies look to leverage the full potential of data. The negative impacts of dirty data can be as minor from time loss doing manual cross checks to major devastating implications such as loss of millions of dollars.

## Bad Data Quality Incidents
The following section describes the impact of bad data quality on an enterprise.

### Incident 1
GM had recalled 2.6 million of its most popular models to replace a defective switch that it had linked to 13 fatalities. In this process GM sent notifications to some families who lost loved ones in fatal crashes. They complained that GM should not have sent them notices to bring in cars for repairs.
In return, GM had to send apologies to families of accident victims who have been notified to bring in cars for replacement of defective ignition switches
GM knew these families and could have easily removed them from their distribution list. If they had better data quality policies, It could have saved them from a negative company image and would have saved them time and money to send another apology to make up for their mistake.

### Incident 2
Vodafone in the past, had sent its customers a ‘Thank You’ message thanking them for their payment. This caused a lot of distress to the customers. Not that money had been deducted from their account nor the customers were expecting to make a payment to their Vodafone account. But they had to go through this, wondering did they have to make a payment and later they realized it was a mistake from Vodafone’s end. Vodafone had many such errors due to bad data quality and if they had better data quality, they could have saved themselves from all this embarrassment.

## Consequences of Bad Data Quality

Some potential consequences of low data quality include:
·   	Low customer satisfaction
·   	Loss of customers
·   	Misguided business decisions
·   	Missed opportunities
·   	Financial inaccuracies and mistakes
·   	Legal and monetary penalties
·   	Negative company image
For example, a dataset comprising of customers which must be contacted for promotions can have the falling data quality issues -
1.  	Invalid address -  street name incorrect
2.  	Missing data - phone numbers, email addresses missing for some contacts as a result we would be unable to contact them for promotions.
3.  	Duplicate contacts - If duplicate contacts are exist, it would be difficult to know which is the right address and we would end up spending time and money on these extra contacts.
4.  	Non-standard format – Inability to automate the process, for example -  building data pipelines.
5.  	Data entry mistakes – Manual mistakes made while entering data.
6.  	Semi-structured data – Various data formats.
7.  	Acceptance of the data by downstream systems – Will the downstream systems such as Bigquery accept the bad data for analysis?

Consider the following data:

<kbd>
<img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/data.png" alt="Data cleanup" width="500" height="100" >
</kbd>

In the above example, what are the potential matches that can duplicate if we search for John or Smith:

There could be

5 records > 10 pairs

Similarly, for 500 records > 124750 pairs

Now think about 5000 records. Hence it is extremely important to have clean data which is transformed using the techniques explained in the next section.

### Solving Data Quality Issues

The following section explains different techniques to solve data quality issues:

1.  	Understand data – nested data file formats, json, avro, parquet, xslx – Expand and extract data from nested data.
2.  	Normalize data patterns – view column profile details and transform to a uniform pattern. For example, date can be of different patters as they may be coming from diff regions, so standardize them.
3.  	Handle inconsistent data – again standardize this using multiple algorithms, functions.
4.  	Validate results – validate your transformed dataset against the target dataset.

### Data Quality Dimensions
The following data quality dimensions must be followed:
1.  	Completeness of the data
Understand what data is missing. For example, address fields can be missing

2.  	Conformity
Understand what data is in nonstandard format. For example, phone numbers can be in varying formats or date can be different for different regions

3.  	Consistency
What values give conflicting information. For example, having same transaction id for 2 different customers.

4.  	Accuracy
What data is incorrect or out of date. For example, an address may have street number missing.

5.  	Duplicates
What data records are repeated? For example, which John smith is the actual one.

6.  	Integrity
 Key attributes missing or not referenced. For example, customer id is missing.

