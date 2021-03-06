#############
Release Notes
#############

6.2.8
=====

Fixes
-----

* Significantly improved the rate at which the transaction logs in a remote region can pull data from the primary region. `(PR #2307) <https://github.com/apple/foundationdb/pull/2307>`_ `(PR #2323) <https://github.com/apple/foundationdb/pull/2323>`_.
* The ``system_kv_size_bytes`` status field could report a size much larger than the actual size of the system keyspace. `(PR #2305) <https://github.com/apple/foundationdb/pull/2305>`_.

6.2.7
=====

Performance
-----------

* A new transaction log spilling implementation is now the default.  Write bandwidth and latency will no longer degrade during storage server or remote region failures. `(PR #1731) <https://github.com/apple/foundationdb/pull/1731>`_.
* Storage servers will locally throttle incoming read traffic when they are falling behind. `(PR #1447) <https://github.com/apple/foundationdb/pull/1477>`_.
* Use CRC32 checksum for SQLite pages. `(PR #1582) <https://github.com/apple/foundationdb/pull/1582>`_.
* Added a 96-byte fast allocator, so storage queue nodes use less memory. `(PR #1336) <https://github.com/apple/foundationdb/pull/1336>`_.
* Improved network performance when sending large packets. `(PR #1684) <https://github.com/apple/foundationdb/pull/1684>`_.
* Spilled data can be consumed from transaction logs more quickly and with less overhead. `(PR #1584) <https://github.com/apple/foundationdb/pull/1584>`_.
* Clients no longer talk to the cluster controller for failure monitoring information.  `(PR #1640) <https://github.com/apple/foundationdb/pull/1640>`_.
* Reduced the number of connection monitoring messages between clients and servers. `(PR #1768) <https://github.com/apple/foundationdb/pull/1768>`_.
* Close connections which have been idle for a long period of time. `(PR #1768) <https://github.com/apple/foundationdb/pull/1768>`_.
* Each client connects to exactly one coordinator, and at most five proxies. `(PR #1909) <https://github.com/apple/foundationdb/pull/1909>`_.
* Ratekeeper will throttle traffic when too many storage servers are not making versions durable fast enough. `(PR #1784) <https://github.com/apple/foundationdb/pull/1784>`_.
* Storage servers recovering a memory storage engine will abort recovery if the cluster is already healthy.  `(PR #1713) <https://github.com/apple/foundationdb/pull/1713>`_.
* Improved how the data distribution algorithm balances data across teams of storage servers. `(PR #1785) <https://github.com/apple/foundationdb/pull/1785>`_.
* Lowered the priority for data distribution team removal, to avoid prioritizing team removal work over splitting shards. `(PR #1853) <https://github.com/apple/foundationdb/pull/1853>`_.
* Made the storage cache eviction policy configurable, and added an LRU policy. `(PR #1506) <https://github.com/apple/foundationdb/pull/1506>`_.
* Improved the speed of recoveries on large clusters at ``log_version >= 4``. `(PR #1729) <https://github.com/apple/foundationdb/pull/1729>`_.
* Log routers will prefer to peek from satellites at ``log_version >= 4``. `(PR #1795) <https://github.com/apple/foundationdb/pull/1795>`_.
* In clusters using a region configuration, clients will read from the remote region if all of the servers in the primary region are overloaded. [6.2.3] `(PR #2019) <https://github.com/apple/foundationdb/pull/2019>`_.
* Significantly improved the rate at which the transaction logs in a remote region can pull data from the primary region. [6.2.4] `(PR #2101) <https://github.com/apple/foundationdb/pull/2101>`_.
* Raised the data distribution priority of splitting shards because delaying splits can cause hot write shards. [6.2.6] `(PR #2234) <https://github.com/apple/foundationdb/pull/2234>`_.

Fixes
-----

* During an upgrade, the multi-version client now persists database default options and transaction options that aren't reset on retry (e.g. transaction timeout). In order for these options to function correctly during an upgrade, a 6.2 or later client should be used as the primary client. `(PR #1767) <https://github.com/apple/foundationdb/pull/1767>`_.
* If a cluster is upgraded during an ``onError`` call, the cluster could return a ``cluster_version_changed`` error. `(PR #1734) <https://github.com/apple/foundationdb/pull/1734>`_.
* Data distribution will now pick a random destination when merging shards in the ``\xff`` keyspace. This avoids an issue with backup where the write-heavy mutation log shards could concentrate on a single process that has less data than everybody else. `(PR #1916) <https://github.com/apple/foundationdb/pull/1916>`_.
* Setting ``--machine_id`` (or ``-i``) for an ``fdbserver`` process now sets ``locality_machineid`` in addition to ``locality_zoneid``. `(PR #1928) <https://github.com/apple/foundationdb/pull/1928>`_.
* File descriptors opened by clients and servers set close-on-exec, if available on the platform. `(PR #1581) <https://github.com/apple/foundationdb/pull/1581>`_.
* ``fdbrestore`` commands other than ``start`` required a default cluster file to be found but did not actually use it. `(PR #1912) <https://github.com/apple/foundationdb/pull/1912>`_.
* Unneeded network connections were not being closed because peer reference counts were handled improperly. `(PR #1768) <https://github.com/apple/foundationdb/pull/1768>`_.
* In very rare scenarios, master recovery would restart because system metadata was loaded incorrectly. `(PR #1919) <https://github.com/apple/foundationdb/pull/1919>`_.
* Ratekeeper will aggressively throttle when unable to fetch the list of storage servers for a considerable period of time. `(PR #1858) <https://github.com/apple/foundationdb/pull/1858>`_.
* Proxies could become overloaded when all storage servers on a team fail. [6.2.1] `(PR #1976) <https://github.com/apple/foundationdb/pull/1976>`_.
* Proxies could start too few transactions if they didn't receive get read version requests frequently enough. [6.2.3] `(PR #1999) <https://github.com/apple/foundationdb/pull/1999>`_.
* The ``fileconfigure`` command in ``fdbcli`` could fail with an unknown error if the file did not contain a valid JSON object. `(PR #2017) <https://github.com/apple/foundationdb/pull/2017>`_.
* Configuring regions would fail with an internal error if the cluster contained storage servers that didn't set a datacenter ID. `(PR #2017) <https://github.com/apple/foundationdb/pull/2017>`_.
* Clients no longer prefer reading from servers with the same zone ID, because it could create hot shards. [6.2.3] `(PR #2019) <https://github.com/apple/foundationdb/pull/2019>`_.
* Data distribution could fail to start if any storage servers had misconfigured locality information. This problem could persist even after the offending storage servers were removed or fixed. [6.2.5] `(PR #2110) <https://github.com/apple/foundationdb/pull/2110>`_.
* Data distribution was running at too high of a priority, which sometimes caused other roles on the same process to stall. [6.2.5] `(PR #2170) <https://github.com/apple/foundationdb/pull/2170>`_.
* Loading a 6.1 or newer ``fdb_c`` library as a secondary client using the multi-version client could lead to an infinite recursion when run with API versions older than 610. [6.2.5] `(PR #2169) <https://github.com/apple/foundationdb/pull/2169>`_
* Using C API functions that were removed in 6.1 when using API version 610 or above now results in a compilation error. [6.2.5] `(PR #2169) <https://github.com/apple/foundationdb/pull/2169>`_
* Coordinator changes could fail to complete if the database wasn't allowing any transactions to start. [6.2.6] `(PR #2191) <https://github.com/apple/foundationdb/pull/2191>`_
* Status would report incorrect fault tolerance metrics when a remote region was configured and the primary region lost a storage replica. [6.2.6] `(PR #2230) <https://github.com/apple/foundationdb/pull/2230>`_
* The cluster would not change to a new set of satellite transaction logs when they become available in a better satellite location. [6.2.6] `(PR #2241) <https://github.com/apple/foundationdb/pull/2241>`_.
* The existence of ``proxy`` or ``resolver`` class processes prevented ``stateless`` class processes from being recruited as proxies or resolvers. [6.2.6] `(PR #2241) <https://github.com/apple/foundationdb/pull/2241>`_.
* Committing transactions larger than 1 MB could cause the proxy to stall for up to a second. [6.2.6] `(PR #2250) <https://github.com/apple/foundationdb/pull/2250>`_.
* The cluster controller could become saturated in clusters with large numbers of connected clients using TLS. [6.2.6] `(PR #2252) <https://github.com/apple/foundationdb/pull/2252>`_.
* Backup and DR would not share a mutation stream if they were started on different versions of FoundationDB. Either backup or DR must be restarted to resolve this issue. [6.2.6] `(PR #2202) <https://github.com/apple/foundationdb/pull/2202>`_.
* Don't track batch priority GRV requests in latency bands. [6.2.7] `(PR #2279) <https://github.com/apple/foundationdb/pull/2279>`_.
* Transaction log processes used twice their normal memory when switching spill types. [6.2.7] `(PR #2256) <https://github.com/apple/foundationdb/pull/2256>`_.
* Under certain conditions, cross region replication could stall for 10 minute periods. [6.2.7] `(PR #1818) <https://github.com/apple/foundationdb/pull/1818>`_ `(PR #2276) <https://github.com/apple/foundationdb/pull/2276>`_.
* When dropping a remote region from the configuration after processes in the region have failed, data distribution would create teams from the dead servers for one minute. [6.2.7] `(PR #2286) <https://github.com/apple/foundationdb/pull/1818>`_.

Status
------

* Added ``run_loop_busy`` to the ``processes`` section to record the fraction of time the run loop is busy. `(PR #1760) <https://github.com/apple/foundationdb/pull/1760>`_.
* Added ``cluster.page_cache`` section to status. In this section, added two new statistics ``storage_hit_rate`` and ``log_hit_rate`` that indicate the fraction of recent page reads that were served by cache. `(PR #1823) <https://github.com/apple/foundationdb/pull/1823>`_.
* Added transaction start counts by priority to ``cluster.workload.transactions``. The new counters are named ``started_immediate_priority``, ``started_default_priority``, and ``started_batch_priority``. `(PR #1836) <https://github.com/apple/foundationdb/pull/1836>`_.
* Remove ``cluster.datacenter_version_difference`` and replace it with ``cluster.datacenter_lag`` that has subfields ``versions`` and ``seconds``. `(PR #1800) <https://github.com/apple/foundationdb/pull/1800>`_.
* Added ``local_rate`` to the ``roles`` section to record the throttling rate of the local ratekeeper `(PR #1712) <http://github.com/apple/foundationdb/pull/1712>`_.
* Renamed ``cluster.fault_tolerance`` fields ``max_machines_without_losing_availability`` and ``max_machines_without_losing_data`` to ``max_zones_without_losing_availability`` and ``max_zones_without_losing_data`` `(PR #1925) <https://github.com/apple/foundationdb/pull/1925>`_.
* ``fdbcli`` status now reports the configured zone count. The fault tolerance is now reported in terms of the number of zones unless machine IDs are being used as zone IDs. `(PR #1924) <https://github.com/apple/foundationdb/pull/1924>`_.
* ``connected_clients`` is now only a sample of the connected clients, rather than a complete list. `(PR #1902) <https://github.com/apple/foundationdb/pull/1902>`_.
* Added ``max_protocol_clients`` to the ``supported_versions`` section, which provides a sample of connected clients which cannot connect to any higher protocol version. `(PR #1902) <https://github.com/apple/foundationdb/pull/1902>`_.
* Clients which connect without specifying their supported versions are tracked as an ``Unknown`` version in the ``supported_versions`` section. [6.2.2] `(PR #1990) <https://github.com/apple/foundationdb/pull/1990>`_.
* Add ``coordinator`` to the list of roles that can be reported for a process. [6.2.3] `(PR #2006) <https://github.com/apple/foundationdb/pull/2006>`_.
* Added ``worst_durability_lag_storage_server`` and ``limiting_durability_lag_storage_server`` to  the ``cluster.qos`` section, each with subfields ``versions`` and ``seconds``. These report the durability lag values being used by ratekeeper to potentially limit the transaction rate. [6.2.3] `(PR #2003) <https://github.com/apple/foundationdb/pull/2003>`_.
* Added ``worst_data_lag_storage_server`` and ``limiting_data_lag_storage_server`` to  the ``cluster.qos`` section, each with subfields ``versions`` and ``seconds``. These are meant to replace ``worst_version_lag_storage_server`` and ``limiting_version_lag_storage_server``, which are now deprecated. [6.2.3] `(PR #2003) <https://github.com/apple/foundationdb/pull/2003>`_.
* Added ``system_kv_size_bytes`` to the ``cluster.data`` section to record the size of the system keyspace. [6.2.5] `(PR #2170) <https://github.com/apple/foundationdb/pull/2170>`_.

Bindings
--------

* Add a transaction size limit as both a database option and a transaction option. `(PR #1725) <https://github.com/apple/foundationdb/pull/1725>`_.
* Added a new API to get the approximated transaction size before commit, e.g., ``fdb_transaction_get_approximate_size`` in the C binding. `(PR #1756) <https://github.com/apple/foundationdb/pull/1756>`_.
* C: ``fdb_future_get_version`` has been renamed to ``fdb_future_get_int64``. `(PR #1756) <https://github.com/apple/foundationdb/pull/1756>`_.
* C: Applications linking to ``libfdb_c`` can now use ``pkg-config foundationdb-client`` or ``find_package(FoundationDB-Client ...)`` (for cmake) to get the proper flags for compiling and linking. `(PR #1636) <https://github.com/apple/foundationdb/pull/1636>`_.
* Go: The Go bindings now require Go version 1.11 or later.
* Go: Finalizers could run too early leading to undefined behavior. `(PR #1451) <https://github.com/apple/foundationdb/pull/1451>`_.
* Added a transaction option to control the field length of keys and values in debug transaction logging in order to avoid truncation. `(PR #1844) <https://github.com/apple/foundationdb/pull/1844>`_.
* Added a transaction option to control the whether ``get_addresses_for_key`` includes a port in the address. This will be deprecated in api version 700, and addresses will include ports by default. [6.2.4] `(PR #2060) <https://github.com/apple/foundationdb/pull/2060>`_.
* Python: ``Versionstamp`` comparisons didn't work in Python 3. [6.2.4] `(PR #2089) <https://github.com/apple/foundationdb/pull/2089>`_.

Features
--------

* Added the ``cleanup`` command to ``fdbbackup`` which can be used to remove orphaned backups or DRs. [6.2.5] `(PR #2170) <https://github.com/apple/foundationdb/pull/2170>`_.
* Added the ability to configure ``satellite_logs`` by satellite location. This will overwrite the region configure of ``satellite_logs`` if both are present. [6.2.6] `(PR #2241) <https://github.com/apple/foundationdb/pull/2241>`_.

Other Changes
-------------

* Added the primitives for FDB backups based on disk snapshots. This provides an ability to take a cluster level backup based on disk level snapshots of the storage, tlogs and coordinators. `(PR #1733) <https://github.com/apple/foundationdb/pull/1733>`_.
* Foundationdb now uses the flatbuffers serialization format for all network messages. `(PR 1090) <https://github.com/apple/foundationdb/pull/1090>`_.
* Clients will throw ``transaction_too_old`` when attempting to read if ``setVersion`` was called with a version smaller than the smallest read version obtained from the cluster. This is a protection against reading from the wrong cluster in multi-cluster scenarios. `(PR #1413) <https://github.com/apple/foundationdb/pull/1413>`_.
* Trace files are now ordered lexicographically. This means that the filename format for trace files has changed. `(PR #1828) <https://github.com/apple/foundationdb/pull/1828>`_.
* Improved ``TransactionMetrics`` log events by adding a random UID to distinguish multiple open connections, a flag to identify internal vs. client connections, and logging of rates and roughness in addition to total count for several metrics. `(PR #1808) <https://github.com/apple/foundationdb/pull/1808>`_.
* FoundationDB can now be built with clang and libc++ on Linux. `(PR #1666) <https://github.com/apple/foundationdb/pull/1666>`_.
* Added experimental framework to run C and Java clients in simulator. `(PR #1678) <https://github.com/apple/foundationdb/pull/1678>`_.
* Added new network options for client buggify which will randomly throw expected exceptions in the client. This is intended to be used for client testing. `(PR #1417) <https://github.com/apple/foundationdb/pull/1417>`_.
* Added ``--cache_memory`` parameter for ``fdbserver`` processes to control the amount of memory dedicated to caching pages read from disk. `(PR #1889) <https://github.com/apple/foundationdb/pull/1889>`_.
* Added ``MakoWorkload``, used as a benchmark to do performance testing of FDB. `(PR #1586) <https://github.com/apple/foundationdb/pull/1586>`_.
* ``fdbserver`` now accepts a comma separated list of public and listen addresses. `(PR #1721) <https://github.com/apple/foundationdb/pull/1721>`_.
* ``CAUSAL_READ_RISKY`` has been enhanced to further reduce the chance of causally inconsistent reads. Existing users of ``CAUSAL_READ_RISKY`` may see increased GRV latency if proxies are distantly located from logs. `(PR #1841) <https://github.com/apple/foundationdb/pull/1841>`_.
* ``CAUSAL_READ_RISKY`` can be turned on for all transactions using a database option. `(PR #1841) <https://github.com/apple/foundationdb/pull/1841>`_.
* Added a ``no_wait`` option to the ``fdbcli`` exclude command to avoid blocking. `(PR #1852) <https://github.com/apple/foundationdb/pull/1852>`_.
* Idle clusters will fsync much less frequently. `(PR #1697) <https://github.com/apple/foundationdb/pull/1697>`_.
* CMake is now the official build system. The Makefile based build system is deprecated.
* The incompatible client list in status (``cluster.incompatible_connections``) may now spuriously include clients that use the multi-version API to try connecting to the cluster at multiple versions.

Fixes only impacting 6.2.0+
---------------------------

* Clients could crash when closing connections with incompatible servers. [6.2.1] `(PR #1976) <https://github.com/apple/foundationdb/pull/1976>`_.
* Do not close idle network connections with incompatible servers. [6.2.1] `(PR #1976) <https://github.com/apple/foundationdb/pull/1976>`_.
* In status, ``max_protocol_clients`` were incorrectly added to the ``connected_clients`` list. [6.2.2] `(PR #1990) <https://github.com/apple/foundationdb/pull/1990>`_.
* Ratekeeper ignores the (default 5 second) MVCC window when controlling on durability lag. [6.2.3] `(PR #2012) <https://github.com/apple/foundationdb/pull/2012>`_.
* The macOS client was not compatible with a Linux server. [6.2.3] `(PR #2045) <https://github.com/apple/foundationdb/pull/2045>`_.
* Incompatible clients would continually reconnect with coordinators. [6.2.3] `(PR #2048) <https://github.com/apple/foundationdb/pull/2048>`_.
* Connections were being closed as idle when there were still unreliable requests waiting for a response. [6.2.3] `(PR #2048) <https://github.com/apple/foundationdb/pull/2048>`_.
* The cluster controller would saturate its CPU for a few seconds when sending configuration information to all of the worker processes. [6.2.4] `(PR #2086) <https://github.com/apple/foundationdb/pull/2086>`_.
* The data distributor would build all possible team combinations if it was tracking an unhealthy server with less than 10 teams. [6.2.4] `(PR #2099) <https://github.com/apple/foundationdb/pull/2099>`_.
* The cluster controller could crash if a coordinator was unreachable when compiling cluster status. [6.2.4] `(PR #2065) <https://github.com/apple/foundationdb/pull/2065>`_.
* A storage server could crash if it took longer than 10 minutes to fetch a key range from another server. [6.2.5] `(PR #2170) <https://github.com/apple/foundationdb/pull/2170>`_.
* Excluding or including servers would restart the data distributor. [6.2.5] `(PR #2170) <https://github.com/apple/foundationdb/pull/2170>`_.
* The data distributor could read invalid memory when estimating database size. [6.2.6] `(PR #2225) <https://github.com/apple/foundationdb/pull/2225>`_.
* Status could incorrectly report that backup and DR were not sharing a mutation stream. [6.2.7] `(PR #2274) <https://github.com/apple/foundationdb/pull/2274>`_.

Earlier release notes
---------------------
* :doc:`6.1 (API Version 610) </old-release-notes/release-notes-610>`
* :doc:`6.0 (API Version 600) </old-release-notes/release-notes-600>`
* :doc:`5.2 (API Version 520) </old-release-notes/release-notes-520>`
* :doc:`5.1 (API Version 510) </old-release-notes/release-notes-510>`
* :doc:`5.0 (API Version 500) </old-release-notes/release-notes-500>`
* :doc:`4.6 (API Version 460) </old-release-notes/release-notes-460>`
* :doc:`4.5 (API Version 450) </old-release-notes/release-notes-450>`
* :doc:`4.4 (API Version 440) </old-release-notes/release-notes-440>`
* :doc:`4.3 (API Version 430) </old-release-notes/release-notes-430>`
* :doc:`4.2 (API Version 420) </old-release-notes/release-notes-420>`
* :doc:`4.1 (API Version 410) </old-release-notes/release-notes-410>`
* :doc:`4.0 (API Version 400) </old-release-notes/release-notes-400>`
* :doc:`3.0 (API Version 300) </old-release-notes/release-notes-300>`
* :doc:`2.0 (API Version 200) </old-release-notes/release-notes-200>`
* :doc:`1.0 (API Version 100) </old-release-notes/release-notes-100>`
* :doc:`Beta 3 (API Version 23) </old-release-notes/release-notes-023>`
* :doc:`Beta 2 (API Version 22) </old-release-notes/release-notes-022>`
* :doc:`Beta 1 (API Version 21) </old-release-notes/release-notes-021>`
* :doc:`Alpha 6 (API Version 16) </old-release-notes/release-notes-016>`
* :doc:`Alpha 5 (API Version 14) </old-release-notes/release-notes-014>`