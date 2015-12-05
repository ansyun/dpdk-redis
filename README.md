####dpdk-redis
--------------
Fork from official redis-3.0.5, and run on the dpdk user space TCP/IP stack(NETDP). For detail function, please refer to redis official website(http://redis.io//).

####build and install
--------------
*  Download latest dpdk version from [dpdk website](http://dpdk.org/), and build dpdk
```
$ make config T=x86_64-native-linuxapp-gcc
$ make install T=x86_64-native-linuxapp-gcc
$ export RTE_SDK=/home/mytest/dpdk
$ export RTE_TARGET=x86_64-native-linuxapp-gcc
```
*  Download opendp following the [opendp wiki](https://github.com/opendp/dpdk-odp/wiki/Compile-APP-with-netdp), buld opendp and startup opendp
```
$ git clone https://github.com/opendp/dpdk-odp.git
$ export RTE_ODP=/home/mytest/dpdk-odp
$ make
$ sudo ./build/opendp -c 0x1 -n 1  -- -p 0x1 --config="(0,0,0)"
EAL: Detected lcore 0 as core 0 on socket 0
EAL: Detected lcore 1 as core 1 on socket 0
EAL: Support maximum 128 logical core(s) by configuration.
EAL: Detected 2 lcore(s)
EAL: VFIO modules not all loaded, skip VFIO support...
EAL: Setting up physically contiguous memory...
EAL: Ask a virtual area of 0x400000 bytes
EAL: Virtual area found at 0x7fdf90c00000 (size = 0x400000)
EAL: Ask a virtual area of 0x15400000 bytes
```
*  Download dpdk-redis, build dpdk-redis and startup dpdk-redis
```
$ git clone https://github.com/opendp/dpdk-redis.git
$ make
$ sudo ./src/redis-server redis.conf
start init opendp
EAL: Detected lcore 0 as core 0 on socket 0
EAL: Detected lcore 1 as core 1 on socket 0
EAL: Support maximum 128 logical core(s) by configuration.
...
EAL: Mapped segment 15 of size 0x200000
EAL: memzone_reserve_aligned_thread_unsafe(): memzone <RG_MP_log_history> already exists
RING: Cannot reserve memory
EAL: TSC frequency is ~2794463 KHz
EAL: WARNING: cpu flags constant_tsc=yes nonstop_tsc=no -> using unreliable clock cycles !
EAL: Master lcore 0 is ready (tid=fdb07940;cpuset=[0])
USER8: netdpsock lcore id 0
USER8: netdpsock any lcore id 0xffffffff
18210:M 28 Nov 19:24:56.938 * Increased maximum number of open files to 150032 (it was originally set to 1024).
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 3.0.5 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 18210
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           http://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

```
* Performance Testing 
```
====ENV=== 
CPU:Intel(R) Xeon(R) CPU E5-2430 0 @ 2.20GHz.
NIC:Intel Corporation 82576 Gigabit Network Connection (rev 01) 
OPENDP run on a lcore.

root@h163:~/dpdk-redis# ./src/redis-benchmark -h 2.2.2.2  -p 6379 -n 100000 -c 50 -q
PING_INLINE: 76687.12 requests per second
PING_BULK: 77459.34 requests per second
SET: 74183.98 requests per second
GET: 75815.01 requests per second
INCR: 76687.12 requests per second
LPUSH: 74794.31 requests per second
LPOP: 74349.44 requests per second
SADD: 75757.57 requests per second
SPOP: 76569.68 requests per second
LPUSH (needed to benchmark LRANGE): 75075.07 requests per second
LRANGE_100 (first 100 elements): 44385.27 requests per second
LRANGE_300 (first 300 elements): 19267.82 requests per second
LRANGE_500 (first 450 elements): 13757.05 requests per second
LRANGE_600 (first 600 elements): 9047.32 requests per second
MSET (10 keys): 61538.46 requests per second

```

####Support
-------
For free support, please use netdp team mail list at zimeiw@163.com. or QQ Group:86883521
