---
layout: post
title: Writing complex mongo aggreation operation in Java (Spring Mongo Template)
---

Take eg of. any MongoDB aggregation operation like:

    db.users.aggregate(
	  [
	    { $project : { month_joined : { $month : "$joined" } } } ,
	    { $group : { _id : {month_joined:"$month_joined"} , number : { $sum : 1 } } },
	    { $sort : { "_id.month_joined" : 1 } }
	  ]
	)

To simply convert it into Spring Mongo Template Java code:
1. Create a converter class

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


2. Use above converter class to convert JS directly to Java mongo aggragte operation:

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
