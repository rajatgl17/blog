---
layout: post
title: Writing Complex MongoDB Aggregation Operations in Java (Spring Mongo Template)
description: Converting mongodb javascipt json queries directly to spring mongo
author: Rajat Goel
---
Writing complex MongoDB aggregation operations in Java using Spring's Mongo Template doesn't have to be complicated. By using a custom converter class, you can easily translate JSON-based MongoDB queries into Java code, making your code more maintainable and easier to understand. This approach is particularly useful for complex queries with multiple stages, allowing you to leverage the full power of MongoDB's aggregation framework within your Java applications. Lets see how?

## Example MongoDB Aggregation Operation

    db.users.aggregate(
	  [
	    { $project : { month_joined : { $month : "$joined" } } } ,
	    { $group : { _id : {month_joined:"$month_joined"} , number : { $sum : 1 } } },
	    { $sort : { "_id.month_joined" : 1 } }
	  ]
	)

## Converting the Aggregation to Spring MongoTemplate Java Code
### Step 1: Create a Converter Class

		import org.bson.Document;
		import org.springframework.data.mongodb.core.aggregation.AggregationOperation;
		import org.springframework.data.mongodb.core.aggregation.AggregationOperationContext;

		public class JsonAggregationOperation implements AggregationOperation {
		
		    private String jsonOperation;

		    public JsonAggregationOperation(String jsonOperation) {
			this.jsonOperation = jsonOperation;
		    }

		    @Override
		    public Document toDocument(AggregationOperationContext context) {
			return context.getMappedObject(Document.parse(jsonOperation));
		    }

		}

This class takes a JSON string representing a MongoDB aggregation stage and converts it into a Document that can be used by the MongoTemplate.

### Step 2: Use the Converter Class in Your Service
Next, use this converter class in your service to execute the aggregation operation:

		import org.springframework.beans.factory.annotation.Autowired;
		import org.springframework.data.mongodb.core.MongoTemplate;
		import org.springframework.data.mongodb.core.aggregation.Aggregation;
		import org.springframework.data.mongodb.core.aggregation.AggregationOperation;
		import org.springframework.stereotype.Service;
		import java.util.*;

		@Service
		public class MongoService {

		    @Autowired
		    MongoTemplate mongoTemplate;

		    public void complexAggregationOperation() {
			 List<AggregationOperation> aggOps = new ArrayList<>();
			 aggOps.add(new JsonAggregationOperation("{ $project : { month_joined : { $month : "$joined" } } }"));
			 aggOps.add(new JsonAggregationOperation("{ $group : { _id : {month_joined:"$month_joined"} , number : { $sum : 1 } } }"));
			 aggOps.add(new JsonAggregationOperation("{ $sort : { "_id.month_joined" : 1 } }"));

			 Aggregation aggregation = Aggregation.newAggregation(aggOps)
				.withOptions(Aggregation.newAggregationOptions().allowDiskUse(true).build());
			 List<Object> records = mongoTemplate.aggregate(aggregation, "users", Object.class).getMappedResults();
		    }
		}

That's it. By using the above custom converter class, you can easily translate JSON-based MongoDB queries into Java code, making your code more maintainable and easier to understand.
