#### dpdk-redis
--------------
Fork from official redis-3.0.5, and run on the dpdk user space TCP/IP stack(ANS"Acceleted Network Stack"). For detail function, please refer to redis official website(http://redis.io//).

#### build and install
--------------
*  Download latest dpdk version from [dpdk website](http://dpdk.org/), and build dpdk
```
$ make config T=x86_64-native-linuxapp-gcc
$ make install T=x86_64-native-linuxapp-gcc
$ export RTE_SDK=/home/mytest/dpdk
$ export RTE_TARGET=x86_64-native-linuxapp-gcc
```
*  Download ANS following the [ANS wiki](https://github.com/opendp/dpdk-ans/wiki/Compile-APP-with-ans), buld ans and startup ans
```
$ git clone https://github.com/ansyun/dpdk-ans.git
$ export RTE_ANS=/home/mytest/dpdk-ans
$ ./install_deps.sh
$ cd ans
$ make
$ sudo ./build/ans -c 0x2 -n 1  -- -p 0x1 --config="(0,0,1)"
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
$ git clone https://github.com/ansyun/dpdk-redis.git
$ make
$ sudo ./src/redis-server redis.conf
start init ans
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
CPU:Intel(R) Xeon(R) CPU E5-2609 v4 @ 1.70GHz.
NIC:Ethernet controller: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection (rev 01) 
ANS run on a lcore.

root@ubuntu:~/src/dpdk-redis# ./src/redis-benchmark -h 10.0.0.2 -p 6379 -n 100000 -c 50 -q
PING_INLINE: 138888.89 requests per second
PING_BULK: 141242.94 requests per second
SET: 140449.44 requests per second
GET: 141043.72 requests per second
INCR: 141442.72 requests per second
LPUSH: 141043.72 requests per second
LPOP: 140449.44 requests per second
SADD: 141643.06 requests per second
SPOP: 141843.97 requests per second
LPUSH (needed to benchmark LRANGE): 141442.72 requests per second
LRANGE_100 (first 100 elements): 48192.77 requests per second
LRANGE_300 (first 300 elements): 14330.75 requests per second
LRANGE_500 (first 450 elements): 10405.83 requests per second
LRANGE_600 (first 600 elements): 7964.95 requests per second
MSET (10 keys): 107758.62 requests per second

```
#### Notes
-------
- Shall use the same gcc version to compile your application.
- In order to improve ANS performance, you shall isolate ANS'lcore from kernel by isolcpus and isolcate interrupt from ANS's lcore by update /proc/irq/default_smp_affinity file.
- You shall include dpdk libs as below way because mempool lib has __attribute__((constructor, used)) in dpdk-16.07 version, otherwise your application would coredump.
```
   $(RTE_ANS)/librte_anssock/librte_anssock.a \
  -L$(RTE_SDK)/$(RTE_TARGET)/lib \
  -Wl,--whole-archive -Wl,-lrte_mbuf -Wl,-lrte_mempool -Wl,-lrte_ring -Wl,-lrte_eal -Wl,--no-whole-archive -Wl,-export-dynamic \

```

#### Support
-------
For free support, please use ans team mail list at anssupport@163.com, or QQ Group:86883521, or https://dpdk-ans.slack.com.
