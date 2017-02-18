Users pass options to RocksDB through _Options_ class. Other than setting options in the _Options_ class, there are two other ways to set it:

1. get an option class from an [[option file|RocksDB-Options-File]].
2. get it from an option string by calling
3. get it from a string map

### Option String

To get an option from a string, call helper function `GetColumnFamilyOptionsFromString()` or `GetDBOptionsFromString()` with a string containing the information. There is also a special `GetBlockBasedTableOptionsFromString()` and `GetPlainTableOptionsFromString()` to get table specific option. 

An example of an option string will like this:
```
write_buffer_size=10;max_write_buffer_number=16;plain_table_factory={user_key_len=66;bloom_bits_per_key=20;};arena_block_size=1024
```
Each option will be given as `<option_name>:<option_value>` separated by `;`. To find the list of options supported, check [the section below](#Find the supported options in option string and option map)

### Options map
Similarly, users can get option classes from a string map, by calling helper function `GetColumnFamilyOptionsFromMap()`, `GetDBOptionsFromMap()`, `GetBlockBasedTableOptionsFromMap()` or `GetPlainTableOptionsFromMap()`. The string to string map is passed in, which maps from the option name and the option value, as a plain text string. An example of string map for an option is like this:
```
  std::unordered_map<std::string, std::string> cf_options_map = {
      {"write_buffer_size", "1"},
      {"max_write_buffer_number", "2"},
      {"min_write_buffer_number_to_merge", "3"},
      {"max_write_buffer_number_to_maintain", "99"},
      {"compression", "kSnappyCompression"},
      {"compression_per_level",
       "kNoCompression:"
       "kSnappyCompression:"
       "kZlibCompression:"
       "kBZip2Compression:"
       "kLZ4Compression:"
       "kLZ4HCCompression:"
       "kXpressCompression:"
       "kZSTD:"
       "kZSTDNotFinalCompression"},
      {"bottommost_compression", "kLZ4Compression"},
      {"compression_opts", "4:5:6:7"},
      {"num_levels", "8"},
      {"level0_file_num_compaction_trigger", "8"},
      {"level0_slowdown_writes_trigger", "9"},
      {"level0_stop_writes_trigger", "10"},
      {"target_file_size_base", "12"},
      {"target_file_size_multiplier", "13"},
      {"max_bytes_for_level_base", "14"},
      {"level_compaction_dynamic_level_bytes", "true"},
      {"max_bytes_for_level_multiplier", "15.0"},
      {"max_bytes_for_level_multiplier_additional", "16:17:18"},
      {"max_compaction_bytes", "21"},
      {"soft_rate_limit", "1.1"},
      {"hard_rate_limit", "2.1"},
      {"hard_pending_compaction_bytes_limit", "211"},
      {"arena_block_size", "22"},
      {"disable_auto_compactions", "true"},
      {"compaction_style", "kCompactionStyleLevel"},
      {"verify_checksums_in_compaction", "false"},
      {"compaction_options_fifo", "23"},
      {"max_sequential_skip_in_iterations", "24"},
      {"inplace_update_support", "true"},
      {"report_bg_io_stats", "true"},
      {"compaction_measure_io_stats", "false"},
      {"inplace_update_num_locks", "25"},
      {"memtable_prefix_bloom_size_ratio", "0.26"},
      {"memtable_huge_page_size", "28"},
      {"bloom_locality", "29"},
      {"max_successive_merges", "30"},
      {"min_partial_merge_operands", "31"},
      {"prefix_extractor", "fixed:31"},
      {"optimize_filters_for_hits", "true"},
  };
```
To find the list of options supported, check [the section below](#Find the supported options in option string and option map)

### Find the supported options in option string and option map
In both of option string and option map, option name maps the variable names in the target class, `DBOptions`, `ColumnFamilyOptions`, `BlockBasedTableOptions`, or `PlainTableOptions`. For `DBOptions` and `ColumnFamilyOptions`, you can find the list of them and their descriptions in two respective classes in the source file [[options.h|https://github.com/facebook/rocksdb/blob/master/include/rocksdb/options.h]] of the source code of your release. For the other two options, you can find them in file [[table.h|https://github.com/facebook/rocksdb/blob/master/include/rocksdb/table.h]]

Note, although most of the options in the option class are supported in the option string, there are exceptions. You can find the list of supported options in variable `db_options_type_info`, `cf_options_type_info` and `block_based_table_type_info` in the source file [[util/options_helper.h|https://github.com/facebook/rocksdb/blob/master/util/options_helper.h]] of the source code of your release.

If the option is a plug-in class, e.g. comparators, compaction filter, and merge operators, the option usually is not supported through the option string or string map. Users need to set those non-supported options in program, after the option is constructed.