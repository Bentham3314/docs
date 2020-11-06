# tuned
sysctl/udevなどをまとめて管理できるツール.  
`/etc/sysctl.conf` に記載するのではなく、 `tuned` に統一する方がイマドキっぽい.  

## Env:
- CentOS7  
- CentOS8  

```
# tuned-adm list
# tuned-adm active
# tuned-adm profile <profile>
```

```
[root@t3/999]# tuned-adm list
Available profiles:
- balanced                    - General non-specialized tuned profile
- desktop                     - Optimize for the desktop use-case
- hpc-compute                 - Optimize for HPC compute workloads
- latency-performance         - Optimize for deterministic performance at the cost of increased power consumption
- network-latency             - Optimize for deterministic performance at the cost of increased power consumption, focused on low latency network performance
- network-throughput          - Optimize for streaming network throughput, generally only necessary on older CPUs or 40G+ networks
- powersave                   - Optimize for low power consumption
- throughput-performance      - Broadly applicable tuning that provides excellent performance across a variety of common server workloads
- virtual-guest               - Optimize for running inside a virtual guest
- virtual-host                - Optimize for running KVM guests
Current active profile: virtual-guest
[root@t3/1000]#
[root@t3/1000]# tuned-adm active
Current active profile: virtual-guest
[root@t3/1001]#
```

## Custom Profile
`/usr/lib/tuned/` 以下に色々あるので参考にする.  
設定と適用は以下:  

```
# cp -ipa /usr/lib/tuned/throughput-performance/ /etc/tuned/custom_server/
# vi /etc/tuned/custom_server/tuned.conf
# tuned-adm profile custom_server   // Profile activate
```

#### 参考:
- /usr/lib/tuned/virtual-guest/tuned.conf  

```
grep -v ^# /usr/lib/tuned/virtual-guest/tuned.conf

[main]
summary=Optimize for running inside a virtual guest
include=throughput-performance

[sysctl]
vm.dirty_ratio = 30

vm.swappiness = 30
```

- /usr/lib/tuned/throughput-performance/tuned.conf  

```
grep -v ^# /usr/lib/tuned/throughput-performance/tuned.conf

[main]
summary=Broadly applicable tuning that provides excellent performance across a variety of common server workloads

[cpu]
governor=performance
energy_perf_bias=performance
min_perf_pct=100

[disk]
readahead=>4096

[sysctl]
kernel.sched_min_granularity_ns = 10000000

kernel.sched_wakeup_granularity_ns = 15000000

vm.dirty_ratio = 40

vm.dirty_background_ratio = 10


vm.swappiness=10
```
