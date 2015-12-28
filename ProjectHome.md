# Torpor Persistence Abstraction for PHP #

Torpor is an ORM persistence layer abstraction for PHP5+ characterized by:
  * Ease of installation, configuration, and end-user development
  * Just-in-time fetching, bulk loading, read/write-through cache (at the session, machine, and cluster levels), and automatic factories based on data object relationships
  * A data engine plug-in architecture that allows for a variety of different data stores using the same generic interfaces.
  * Lightweight, extensible objectified data entities with support for callbacks and extended object factories (retrieve data elements, records, or recordsets as a user-defined inheriting class atop the rich set of Torpor elements)
  * Highly configurable at both the engine and the persistence store descriptions - XML configuration files describe engine, grid, and column level features and overrides, and all data relationships available between disparate grids (including one to one, one to many, and many to many with or without intermediate mapping grid data)
  * Automatic relationship maintenance in one-to-one and one-to-many criteria sets: add a new related object and the association criteria and any intermediate mapping data is taken care of (but also overrideable and user accessible)

Check out the [Usage Guide](http://www.tricornersoftware.com/Products/Torpor/UsageGuide.html) for more in-depth information on how it works and how to use it.