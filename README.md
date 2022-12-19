# mmapbanch

## fio

### Options

- **readwrite**: Type of I/O pattern. Accepted values are:
  
  - **read**:   Sequential reads.
  - **write**:  Sequential writes.
  - **trim**:   Sequential trims (Linux block devices and SCSI character devices only).
  - **randread**: Random reads.
  - **randwrite**: Random writes.
  - **randtrim**: Random trims (Linux block devices and SCSI character devices only).
  - **rw,readwrite**: Sequential mixed reads and writes.
  - **randrw**: Random mixed reads and writes.
  - **trimwrite**: Sequential trim+write sequences. Blocks will be trimmed first,  then  the  same  blocks will  be  written  to.  So  if `io_size=64K` is specified, Fio will trim a total of 64K bytes and also write 64K bytes on the same trimmed blocks. This behaviour will be  consistent  with `number_ios'  or other Fio options limiting the total bytes or number of I/O's
  - **randtrimwrite**: Like trimwrite , but uses random offsets rather than sequential writes.

- **iodepth**:
  Number  of  I/O  units to keep in flight against the file. Note that increasing iodepth beyond 1 will not affect synchronous ioengines (except for small degrees when verify_async is in use).  Even  async engines  may  impose OS restrictions causing the desired depth not to be achieved. This may happen on Linux when using libaio and not setting `direct=1', since buffered I/O is not async on that OS.  Keep an  eye  on  the I/O depth distribution in the fio output to verify that the achieved depth is as expected. Default: 1.

- **io_size**:
  Normally fio operates within the region set by size, which means that the size option sets  both  the region  and size of I/O to be performed. Sometimes that is not what you want. With this option, it is possible to define just the amount of I/O that fio should do. For instance, if size is set  to  20GiB and  io_size is set to 5GiB, fio will perform I/O within the first 20GiB but exit when 5GiB have been done. The opposite is also possible -- if size is set to 20GiB, and io_size is set to 40GiB, then fio will do 40GiB of I/O within the 0..20GiB region. Value can be set as percentage: io_size=N%.  In this case io_size multiplies size= value. In ZBD mode, value can also be set as number of zones using 'z'.

- **blocksize**: The  block  size in bytes used for I/O units. Default: 4096. A single value applies to reads, writes, and trims. Comma-separated values may be specified for reads, writes, and trims. A value  not  terminated in a comma applies to subsequent types. Examples:
  
  - bs=256k        means 256k for reads, writes and trims.
  - bs=8k,32k      means 8k for reads, 32k for writes and trims.
  - bs=8k,32k,     means 8k for reads, 32k for writes, and default for trims.
  - bs=,8k         means default for reads, 8k for writes and trims.
  - bs=,8k,        means default for reads, 8k for writes, and default for trims.

- **numjobs**: Create the specified number of clones of this job. Each clone of job is  spawned  as  an  independent thread  or  process.  May be used to setup a larger number of threads/processes doing the same thing. Each thread is reported separately; to see statistics for all clones as a whole, use  group_reporting in conjunction with new_group.  See --max-jobs. Default: 1.

- **lockmem**: Pin the specified amount of memory with mlock(2). Can be used to simulate a smaller amount  of memory. The amount specified is per worker.

## Results

### Sequential read

```terminal
fio --filesize=10GB --filename=test_file --io_size=10GB --name seq --rw=read --ioengine=mmap --direct=1 --blocksize=1MB --numjobs=72
```

```
Run status group 0 (all jobs):
   READ: bw=21.0GiB/s (22.5GB/s), 298MiB/s-324MiB/s (313MB/s-340MB/s), io=720GiB (773GB), run=31612-34345msec

Disk stats (read/write):
  nvme3n1: ios=254403/0, merge=0/0, ticks=33811/0, in_queue=28, util=92.80%
```

### Rand read

```terminal
fio --filesize=10GB --filename=test_file --io_size=10GB --name rand --rw=randwrite --ioengine=mmap --direct=1 --blocksize=1MB --numjobs=72
```

```
Run status group 0 (all jobs):
  WRITE: bw=1477MiB/s (1549MB/s), 20.5MiB/s-20.7MiB/s (21.5MB/s-21.7MB/s), io=720GiB (773GB), run=493730-499079msec

Disk stats (read/write):
  nvme3n1: ios=2438161/5882998, merge=0/88, ticks=1167365/12400047, in_queue=9262800, util=91.17%
```