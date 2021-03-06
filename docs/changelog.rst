Version History
===============

This is a version history of C++ API interface changes. It does not include internal fixes and changes.

+---------+---------------------------------------------------------------------------------------------------------------------------------------+
| Version | Changes                                                                                                                               |
+=========+=======================================================================================================================================+
| 1.1.6   | - Windows support added (Alpha)                                                                                                       |
+---------+---------------------------------------------------------------------------------------------------------------------------------------+
| 1.1.4   | - Make :cpp:func:`ColumnStoreSystemCatalog::getTable` and :cpp:func:`ColumnStoreSystemCatalogTable::getColumn` case insensitive       |
|         | - Add :cpp:func:`ColumnStoreDriver::setDebug` to enable debugging output to stderr                                                    |
+---------+---------------------------------------------------------------------------------------------------------------------------------------+
| 1.1.1   | - Add :cpp:func:`ColumnStoreBulkInsert::isActive`                                                                                     |
|         | - Make :cpp:func:`ColumnStoreBulkInsert::rollback` fail without error                                                                 |
|         | - Add :cpp:func:`ColumnStoreBulkInsert::resetRow`                                                                                     |
|         | - :cpp:func:`ColumnStoreDateTime::ColumnStoreDateTime` now uses uint32_t for every parameter                                          |
|         | - :cpp:class:`ColumnStoreSystemCatalog` now uses const for the sub-class strings                                                      |
+---------+---------------------------------------------------------------------------------------------------------------------------------------+
| 1.1.0β  | - First beta release                                                                                                                  |
+---------+---------------------------------------------------------------------------------------------------------------------------------------+
