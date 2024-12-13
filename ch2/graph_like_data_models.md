# Graph-Like Data Models
- if many-to-many relationships are common => relational model can handle simple cases
- but as the connection within data become more complex, it becomes more natural to start modeling your data as graph
- graph: `verticies` and `edges`
- typcial examples: 
    - social graphs
    - web graph
    - road or rail networks
- graphs are not limited to such homogeneous data
    - provide a consistent way of storing completely different types of objects in a single datastore
- ex: social network or a gnealogical database
    - two people, Lucy from Idaho and Alain from Beaune, France => they are married and living in London
- propoerty graph model and triple-store model (model)
- Cypher, SPARQL, and Datalog (declarative query language)

## Property Graphs
- each vertex consists of:
    - a unique identifier
    - a set of outgoing edges
    - a set of incoming edges
    - a collection of properties (key-value pairs)
- each edge consist of:
    - a unique identifier
    - the vertex at which the edge starts (tail vertex)
    - the vertex at which the edge ends (head vertex)
    - a label to describe the kind of relationship between the two vertices
    - a collection of properties (key-value pairs)
- graph stores as consisting of two relational tables:
    - verticies and edges
    - representing a property graph using a relational schema
    ```
    CREATE TABLE verticies (
        vertex_id integer PRIMARY KEY,
        properties json
    );
    CREATE TABLE edges (
        edge_id integer PRIMARY KEY,
        tail_vertex integer REFERENCES vertices (vertex_id),
        head_vertex integer REFERENCES vertices (vertex_id),
        label text,
        properties json
    );
    CREATE INDEX edges_tails on edges (tail_vertex);
    CREATE INDEX edges_heads on edges (head_vertex);
    ```
    