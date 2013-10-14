# 1. Setup

All of the benchmarks are run on the same machine. Here are the details of the test setup:

* 12 CPUs, HT enabled -> 24 vCPUs reported, 2 sockets X 6 cores/socket with X5650 @ 2.67GHz
* 2 FusionIO devices in SW RAID 0 that can do ~200k 4kb read/second at peak
* Machine has 144 GB of RAM
* Operating System Linux 2.6.38.4
* 1G rocksdb block cache
* snappy compression enabled
* 1 Billion keys, each key is of size 10 bytes, each value is 800 bytes

# 2. Bulk Load

Compare the time taken to load 1B keys into the database.

    rocksdb took 2:20 hours
    leveldb took greater than 2 days (did not finish)

Here are the command that were run to load the database into rocksdb

    #echo "Bulk load database into L0...."
    bpl=10485760;overlap=10;mcz=2;del=300000000;levels=2;ctrig=10000000; delay=10000000; stop=10000000; wbn=30; mbc=20; mb=1073741824;wbs=268435456; dds=1; sync=0; r=1000000000; t=1; vs=800; bs=65536; cs=1048576; of=500000; si=1000000; ./db_bench --benchmarks=fillrandom --disable_seek_compaction=1 --mmap_read=0 --statistics=1 --histogram=1 --num=$r --threads=$t --value_size=$vs --block_size=$bs --cache_size=$cs --bloom_bits=10 --cache_numshardbits=4 --open_files=$of --verify_checksum=1 --db=/data/mysql/leveldb/test --sync=$sync --disable_wal=1 --compression_type=zlib --stats_interval=$si --compression_ratio=50 --disable_data_sync=$dds --write_buffer_size=$wbs --target_file_size_base=$mb --max_write_buffer_number=$wbn --max_background_compactions=$mbc --level0_file_num_compaction_trigger=$ctrig --level0_slowdown_writes_trigger=$delay --level0_stop_writes_trigger=$stop --num_levels=$levels --delete_obsolete_files_period_micros=$del --min_level_to_compress=$mcz --max_grandparent_overlap_factor=$overlap --stats_per_interval=1 --max_bytes_for_level_base=$bpl --memtablerep=vector --use_existing_db=0 --disable_auto_compactions=1 --source_compaction_factor=10000000
    #echo "Running manual compaction to do a global sort map-reduce style...."
    bpl=10485760;overlap=10;mcz=2;del=300000000;levels=2;ctrig=10000000; delay=10000000; stop=10000000; wbn=30; mbc=20; mb=1073741824;wbs=268435456; dds=1; sync=0; r=1000000000; t=1; vs=800; bs=65536; cs=1048576; of=500000; si=1000000; ./db_bench --benchmarks=compact --disable_seek_compaction=1 --mmap_read=0 --statistics=1 --histogram=1 --num=$r --threads=$t --value_size=$vs --block_size=$bs --cache_size=$cs --bloom_bits=10 --cache_numshardbits=4 --open_files=$of --verify_checksum=1 --db=/data/mysql/leveldb/test --sync=$sync --disable_wal=1 --compression_type=zlib --stats_interval=$si --compression_ratio=50 --disable_data_sync=$dds --write_buffer_size=$wbs --target_file_size_base=$mb --max_write_buffer_number=$wbn --max_background_compactions=$mbc --level0_file_num_compaction_trigger=$ctrig --level0_slowdown_writes_trigger=$delay --level0_stop_writes_trigger=$stop --num_levels=$levels --delete_obsolete_files_period_micros=$del --min_level_to_compress=$mcz --max_grandparent_overlap_factor=$overlap --stats_per_interval=1 --max_bytes_for_level_base=$bpl --memtablerep=vector --use_existing_db=1 --disable_auto_compactions=1 --source_compaction_factor=10000000


Here are the command(s) for loading the data into leveldb:

    echo "Bulk load database ...."
    wbs=268435456; r=1000000000; t=1; vs=800; cs=1048576; of=500000; ./db_bench --benchmarks=fillrandom --num=$r --threads=$t --value_size=$vs --cache_size=$cs --bloom_bits=10 --open_files=$of --db=/data/mysql/leveldb/test --compression_ratio=50 --write_buffer_size=$wbs --use_existing_db=0