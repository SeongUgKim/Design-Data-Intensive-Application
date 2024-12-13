# Query Language for Data
- relational model => new way of querying data: SQL
	- declarative query language
- specify the pattern of the data you want
	- what conditions the results must meet
	- how you want the data to be transformed
	- but not how to achieve that goal
- hides implementation details of the DB engine
- parallel execution
	- better chance of getting faster in parallel execution b/c specify only the pattern of the results
## MapReduce Querying
- `MapReduce`: programming model for processing large amounts of data in bulk across many machines 
- limited form of MapReduce is supported by some NoSQL as a mechanism for performing read-only queries
- neither a declarative query language nor a imperative query API
	- but in between: logic of the query is expressed with snippets of code (called repeatedly by the processing framework)
- based on the `map` (`collect`) and `reduce` (`fold` or `inject`) functions that exist in many functional programming languages
- ex: add an observation record to DB every time you see animals in the ocean => want to generate a report saying how many sharks you have sighted per month
- PostgreSQL:
    ```
    SELECT date_trunc('month', observation_timestamp) AS observation_month,
        sum(num_animuals) AS total_animals
    FROM observations
    WHERE family = 'Sharks'
    GROUP BY observation_month
    ```
- MongoDB's MapReduce feature:
    ```
    db.observations.mapReduce(
        function map() { // 2
            var year this.observationTimestamp.getFullYear();
            var month = this.observationTimestamp.getMonth() + 1;
            emit(year + "_" + month, this.numAnimals); // 3
        },
        function reduce(key, values) { // 4
            return Array.sum(values); // 5
        },
        {
            query: {family: "Sharks"}, // 1
            out: "monthlySharkReport" // 6
        }
    );
    ```
    - 1. the filter to consider only shark species can be specified declaratively
    - 2. `map` function is called once for every document that matches `query` with `this` set to the document object
    - 3. `map` function emits a key (a string consisting of year and month) and a value (the number of animals in that observation)
    - 4. the key-value pairs emitted by `map` are grouped by key 
        - for all key-value pairs with the same key, the `reduce` function is called once
    - 5. the `reduce` function adds up the number of animals from all observatoins in a particular month
    - 6. final output is written to the collection `monthlySharReport`
- `map` and `reduce` functions are restricted
    - pure functions => only use the data that is passed to them as input => cannot perform additoinal database queries and must not have any side effects
- MapReduce: low-level programming model for distributted execution on a cluster of a machines
    - Higher-level query languages like SQL can be implemented as a pipeline of MapReduce operations