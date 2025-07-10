## cgroup ?

### 개요
Control Group을 의미합니다. Linux 커널은 기본적으로 모든 프로세스에 대해 리소스 사용 정보를 수집합니다. cgroup은 리눅스 커널 기능이며, 프로세스들을 cgroup으로 묶어 cgroup별로 리소스를 할당/제한이 가능합니다.


---

### 리소스
제한 가능한 자원은 다음과 같습니다.
- CPU
- Memory
- Network
- Block I/O : HDD, SSD, USB 등 Block Device 'I/O 속도' 제한이 가능합니다.
- Device


---
### 경로
리눅스 OS 내에서는 모든 것들이 파일로 구현됩니다. 따라서, cgroup도 디렉터리/파일로 구현됩니다. 시스템 내 `/sys/fs/cgroup` 경로에서 확인이 가능합니다.


---

### Version
cgroup은 버전이 v1, v2로 나뉩니다. 버전에 따라서, `/sys/fs/cgroup/` 하위 디렉터리 구조가 다릅니다.

`cgroup v1`은 `/sys/fs/cgroup/{cpu, memory, ...}`와 같이 `cgroup` 디렉터리 하위에 리소스별로 디렉터리가 존재합니다. 

반면, `cgroup v2`는 `/sys/fs/cgroup` 단일 트리 구조를 가집니다. 하나의 통합 디렉터리(`cgroup`) 에서 `controller` 파일을 통해 리소스를 제어하기 때문입니다.

- `cgruop v1`
```bash
# REHL 7.x
> ls -al /sys/fs/cgroup

total 0
dr-xr-xr-x 6 root root  0 Jun 10 14:13 blkio
lrwxrwxrwx 1 root root 11 Jun 10 14:13 cpu -> cpu,cpuacct
lrwxrwxrwx 1 root root 11 Jun 10 14:13 cpuacct -> cpu,cpuacct
dr-xr-xr-x 6 root root  0 Jun 10 14:13 cpu,cpuacct
dr-xr-xr-x 3 root root  0 Jun 10 14:13 cpuset
dr-xr-xr-x 6 root root  0 Jun 10 14:13 devices
dr-xr-xr-x 3 root root  0 Jun 10 14:13 freezer
dr-xr-xr-x 3 root root  0 Jun 10 14:13 hugetlb
dr-xr-xr-x 6 root root  0 Jun 10 14:13 memory
lrwxrwxrwx 1 root root 16 Jun 10 14:13 net_cls -> net_cls,net_prio
dr-xr-xr-x 3 root root  0 Jun 10 14:13 net_cls,net_prio
lrwxrwxrwx 1 root root 16 Jun 10 14:13 net_prio -> net_cls,net_prio
dr-xr-xr-x 3 root root  0 Jun 10 14:13 perf_event
dr-xr-xr-x 6 root root  0 Jun 10 14:13 pids
dr-xr-xr-x 3 root root  0 Jun 10 14:13 rdma
dr-xr-xr-x 6 root root  0 Jun 10 14:13 systemd
```

- `cgroup v2`
```bash
# REHL 9.x
> ls -al

total 0
-r--r--r--.  1 root root 0 Jul 10 11:06 cgroup.controllers
-rw-r--r--.  1 root root 0 Jul 10 11:22 cgroup.max.depth
-rw-r--r--.  1 root root 0 Jul 10 11:22 cgroup.max.descendants
-rw-r--r--.  1 root root 0 Jul 10 11:06 cgroup.procs
-r--r--r--.  1 root root 0 Jul 10 11:22 cgroup.stat
-rw-r--r--.  1 root root 0 Jul 10 11:10 cgroup.subtree_control
-rw-r--r--.  1 root root 0 Jul 10 11:22 cgroup.threads
-r--r--r--.  1 root root 0 Jul 10 11:22 cpuset.cpus.effective
-r--r--r--.  1 root root 0 Jul 10 11:22 cpuset.cpus.isolated
-r--r--r--.  1 root root 0 Jul 10 11:22 cpuset.mems.effective
-r--r--r--.  1 root root 0 Jul 10 11:22 cpu.stat
drwxr-xr-x.  2 root root 0 Jul 10 11:06 dev-hugepages.mount
drwxr-xr-x.  2 root root 0 Jul 10 11:06 dev-mqueue.mount
drwxr-xr-x.  2 root root 0 Jul 10 11:06 init.scope
-r--r--r--.  1 root root 0 Jul 10 11:22 io.stat
-r--r--r--.  1 root root 0 Jul 10 11:22 memory.numa_stat
--w-------.  1 root root 0 Jul 10 11:22 memory.reclaim
-r--r--r--.  1 root root 0 Jul 10 11:22 memory.stat
-r--r--r--.  1 root root 0 Jul 10 11:22 misc.capacity
-r--r--r--.  1 root root 0 Jul 10 11:22 misc.current
drwxr-xr-x.  2 root root 0 Jul 10 11:06 sys-fs-fuse-connections.mount
drwxr-xr-x.  2 root root 0 Jul 10 11:06 sys-kernel-config.mount
drwxr-xr-x.  2 root root 0 Jul 10 11:06 sys-kernel-debug.mount
drwxr-xr-x.  2 root root 0 Jul 10 11:06 sys-kernel-tracing.mount
drwxr-xr-x. 24 root root 0 Jul 10 11:25 system.slice
drwxr-xr-x.  3 root root 0 Jul 10 11:09 user.slice
```

현재 시스템의 `cgroup` 버전은 다음과 방법으로도 확인이 가능합니다.

- `cgroup v1`
```bash
> mount | grep 

tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd)
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls,net_prio)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpu,cpuacct)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/rdma type cgroup (rw,nosuid,nodev,noexec,relatime,rdma)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
```

- `cgroup v2`
```bash
> mount | grep 

cgroup2 on /sys/fs/cgroup type cgroup2 (rw,nosuid,nodev,noexec,relatime,seclabel,nsdelegate,memory_recursiveprot)
```


---

### cgroup 내 프로세스 등록 방식 비교 (v1 : v2)

| 버전        | 프로세스 등록 파일     |
| --------- | -------------- |
| cgroup v1 | `tasks`        |
| cgroup v2 | `cgroup.procs` |

cgroup 버전별 해당하는 파일에 등록하려는 프로세스 PID를 추가하면, 해당 cgroup에 지정한 프로세스가 등록됩니다.

- `cgroupv2`
```bash
> cat /sys/fs/cgroup/cgroup.procs
2
3
4
...
5038
5067
5091
```

- cgroup에 PID 추가
```bash
> echo "<PID>" | sudo tee /sys/fs/cgroup/cgroup.procs
```


#tip 
참고로, PID를 등록할 때, `>>` 와 `sudo tee` 모두 사용 가능합니다. 하지만 권장되는 방법은 `tee` 입니다. 

`>>`는 일반 사용자 권한으로 실행되므로, 사용자가 `cgroup.procs` 파일 쓰기 권한이 없으면 실패할 수 있습니다. 또한, 이미 등록돼 있는 PID를 다시 append 했을 경우, 오류가 발생할 수 있습니다. 

반면, `tee`는 기본적으로 root 권한으로 파일을 열기 때문에 권한 오류가 발생하지 않습니다. 더불어 이미 등록돼 있는 내용을 중복하여 등록할 경우, 에러가 없이 쓰기 동작이 무시됩니다.


---

### `cgroupv1` - subsystem

`cgroupv1`은 `cgroup` 디렉터리 내 `subsytem`이라 불리는 각 리소스별 디렉터리를 통해 리소스를 제어합니다. 종류는 다음과 같습니다.

```bash
> ls -al /sys/fs/cgroup/

dr-xr-xr-x 6 root root  0 Jun 10 14:13 blkio
lrwxrwxrwx 1 root root 11 Jun 10 14:13 cpu -> cpu,cpuacct
lrwxrwxrwx 1 root root 11 Jun 10 14:13 cpuacct -> cpu,cpuacct
dr-xr-xr-x 6 root root  0 Jun 10 14:13 cpu,cpuacct
dr-xr-xr-x 3 root root  0 Jun 10 14:13 cpuset
dr-xr-xr-x 6 root root  0 Jun 10 14:13 devices
dr-xr-xr-x 3 root root  0 Jun 10 14:13 freezer
dr-xr-xr-x 3 root root  0 Jun 10 14:13 hugetlb
dr-xr-xr-x 6 root root  0 Jun 10 14:13 memory
lrwxrwxrwx 1 root root 16 Jun 10 14:13 net_cls -> net_cls,net_prio
dr-xr-xr-x 3 root root  0 Jun 10 14:13 net_cls,net_prio
lrwxrwxrwx 1 root root 16 Jun 10 14:13 net_prio -> net_cls,net_prio
dr-xr-xr-x 3 root root  0 Jun 10 14:13 perf_event
dr-xr-xr-x 6 root root  0 Jun 10 14:13 pids
dr-xr-xr-x 3 root root  0 Jun 10 14:13 rdma
dr-xr-xr-x 6 root root  0 Jun 10 14:13 systemd
```

| 서브시스템                             | 설명                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `blkio`                           | 제어 리소스 : Block Device(HDD, SSD 등)의 I/O 대역폭 및 I/O 작업량<br><br>cgroup이 수행할 수 있는 블록 디바이스 I/O 작업량을 제어합니다.<br>I/O 대역폭 및 작업 수를 제한하여, 특정 애플리케이션에서 과도하게 I/O 하는 것을 방지합니다.                                                                                                                                                                           |
| `cpu`                             | 제어 리소스 : CPU 시간<br><br>cgroup에 할당되는 CPU 사용량을 제어합니다. CPU 사용 비율, 가중치를 설정할 수 있습니다.<br>주로 `cpu.shares` 파일을 통해 상대적인 CPU 가중치를 설정하여, 여러 `cgroup` 간에 CPU 시간을 비율적으로 분배합니다.<br>`cpu.cfs_period_us`, `cpu.cfs_quota_us` 파일을 사용하여 특정 시간 동안 사용할 수 있는 CPU 시간을 제한할 수 있습니다. (CPU 대역폭 제어)<br>이를 위해 리눅스 시스템 스케쥴러(CFS, Completely Fair Scheduler 방식)을 사용합니다. |
| `cpuacct (CPU Accounting)`        | 제어 리소스 : CPU 사용량 통계<br><br>특정 cgroup에 속한 프로세스들의 CPU 사용량 정보를 수집합니다. 리소스 제한보다는 모니터링 및 통계 목적으로 사용됩니다.<br>`cpuacct.usage` 파일에서 전체 CPU 사용 시간, `cpuacct.stat` 파일에서 사용자/시스템 CPU 사용 시간 확인이 가능합니다.                                                                                                                                                 |
| `cpuset`                          | 제어 리소스 : CPU 코어, 메모리 노드<br><br>cgroup이 사용할 수 있는 CPU 코어(CPU Affinity)와 메모리 노드(NUMA)를 제어합니다.<br>즉, 특정 애플리케이션/서비스가 특정 CPU 코어에서만 실행되도록 지정 가능합니다.                                                                                                                                                                                              |
| `devices`                         | 제어 리소스 : Device 접근 권한<br><br>cgroup에 속한 프롯세스들이 특정 장치 파일(`/dev/sda, /dev/sdb 등`)에 접근할 수 있는지를 제어합니다. 읽기(r), 쓰기(w), 생성(m) 권한을 세부적으로 설정할 수 있습니다.                                                                                                                                                                                              |
| `freezer`                         | 제어 리소스 : cgroup/프로세스의 실행 상태<br><br>cgroup에 속한 모든 프로세스를 일시 중지(freeze)하거나, 재개할 수 있습니다.<br>시스템 스냅샷 생성, 특정 워크로드의 일시 중단 필요 시 사용 가능합니다. `docker pause` 명령이 `freezer`를 사용합니다.                                                                                                                                                                    |
| `hugetlb`                         | 제어 리소스 : HugePages<br><br>cgroup이 사용할 수 있는 HugePage의 양을 제어합니다.<br>HugePages는 일반적인 4KB Page보다 훨씬 큰 메모리 page를 의미합니다. 데이터베이스나 가상화와 같이 대용량 메모리 사용이 필요한 애플리케이션에서 성능 향상을 위해 사용될 수 있습니다.                                                                                                                                                         |
| `memory`                          | 제어 리소스 : 시스템 메모리 (RAM, Swap)<br><br>cgroup이 사용할 수 있는 총 메모리 크기을 제어합니다.<br>`memory.limit_in_bytes`를 통해 Hard Limit을 설정하거나, `memory.soft_limit_in_bytes`를 통해 Soft Limit을 설정할 수 있습니다. 메모리 부족 상황에서 특정 cgroup 내 프로세스가 먼저 Swap 되거나 정리되게 할 수 있습니다. 또한 swap 사용량 제한, OOM 킬러 설정 등 다양한 메모리 관련 제어가 가능합니다.                                               |
| `net_cls (Network Classifier)`    | 제어 리소스 : 네트워크 패킷에 대한 Class ID 할당<br><br>cgroup에 속한 프로세스들이 생성하는 네트워크 패킷에 특정 Class ID를 할당합니다. Class ID는 네트워크 트래픽 관리 도구에 사용돼, 특정 cgroup에서 발생하는 트래픽에 대한 대역폭 제한, 우선순위 부여 등의 정책을 적용할 수 있습니다.                                                                                                                                                    |
| `net_prio (Network Priority)`     | 제어 리소스 : 네트워크 패킷의 우선순위<br><br>cgroup에 속한 프로세스들이 생성하는 네트워크 트래픽의 우선순위를 직접적으로 조절합니다. `net_prio.prioidx` 파일을 통해 우선순위를 설정하여, 다른 cgroup 프로세스의 트래픽보다 높은 우선순위로 네트워크를 사용할 수 있습니다.                                                                                                                                                                  |
| `perf_event (Performance Events)` | 제어 리소스 : 성능 모니터링 이벤트 사용<br><br>cgroup에 속한 프로세스들이 사용할 수 있는 `perf` 이벤트(하드웨어 성능 카운터, 소프트웨어 이벤트 등)의 수를 제한합니다. 특정 cgroup이 너무 많은 성능 이벤트를 소비하여 다른 cgroup의 분석을 방해하지 않도록 할 수 있습니다.                                                                                                                                                                 |
| `pids`                            | 제어 리소스 : 프로세스 및 쓰레드 수<br><br>cgroup에 속할 수 있는 최대 프로세스 및 쓰레드 수를 제어합니다.<br>fork bomb 과 같은 공격으로부터 시스템을 보호하는데 유용합니다.                                                                                                                                                                                                                           |
| `rdma`                            | 제어 리소스 : RDMA (Remote Direct Memory Access) 관련 리소스<br><br>RDMA는 원격 시스템의 메모리에 직접 접근할 수 있도록 하는 기술입니다.<br>이 컨트롤러는 RDMA 리소스 사용량을 제어합니다. 예를 들어, RDMA 메모리 사용량, RDMA 네트워크 대역폭 등을 제어할 수 있습니다. HPC 환경에서 주로 사용됩니다.                                                                                                                                  |
| `systemd`                         | 제어 리소스 : `systemd`가 관리하는 `Service/Unit Group`<br><br>`systemd`는 자체적으로 cgroup을 사용하여 서비스, 사용자 세션 등의 리소스를 관리합니다. `systemd` 디렉토리는 `systemd`가 생성하고 관리하는 cgroup들을 포함합니다. `systemd`는 해당 디렉토리 아래 각 서비스, 소켓, 마운트 등의 유닛에 대한 cgroup을 자동으로 생성하고 관리합니다. <br>사용자가 직접 이 디렉토리의 파일을 조작하기보다는 `systemd` 명령(예: `systemctl`)을 통해 간접적으로 제어하는 것이 일반적입니다.         |


---

### `cgroupv2` - Controller

`cgroupv2`는 단일 `cgroup` 디렉터리 내에서 `controller`를 통해 리소스를 제어합니다. controller 종류는 다음과 같이 확인 가능합니다.

```bash
> cat /sys/fs/cgroup/cgroup.controllers

cpuset cpu io memory hugetlb pids rdma misc
```

| 컨트롤러      | 설명                                                                                                                                                                                                                                                          |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cpuset`  | 제어 리소스 : CPU 코어, 메모리 노드<br><br>cgroup이 사용할 수 있는 CPU 코어와 메모리 노드를 제어합니다.<br>즉, 특정 애플리케이션/서비스가 특정 CPU 코어에서만 실행되도록 지정 가능합니다.                                                                                                                                    |
| `cpu`     | 제어 리소스 : CPU 시간<br><br>cgroup에 할당되는 CPU 사용량을 제어합니다. CPU 사용 비율, 가중치를 설정할 수 있습니다.<br>이를 위해 리눅스 시스템 스케쥴러(CFS, Completely Fair Scheduler 방식)을 사용합니다.                                                                                                            |
| `io`      | 제어 리소스 : Block Device(HDD, SSD 등)의 I/O 대역폭 및 I/O 작업량<br><br>cgroup이 수행할 수 있는 블록 디바이스 I/O 작업량을 제어합니다.<br>I/O 대역폭 및 작업 수를 제한하여, 특정 애플리케이션에서 과도하게 I/O 하는 것을 방지합니다.                                                                                             |
| `memory`  | 제어 리소스 : 시스템 메모리 (RAM, Swap)<br><br>cgroup이 사용할 수 있는 총 메모리 크기을 제어합니다.<br>`memory.max`를 통해 Hard Limit을 설정하거나, `memory.high`를 통해 Soft Limit을 설정할 수 있습니다. 메모리 부족 상황에서 특정 cgroup 내 프로세스가 먼저 Swap 되거나 정리되게 할 수 있습니다. 또한, `memory.min`을 통해 최소 메모리 크기를 보장할 수 있습니다. |
| `hugetlb` | 제어 리소스 : HugePages<br><br>cgroup이 사용할 수 있는 HugePage의 양을 제어합니다.<br>HugePages는 일반적인 4KB Page보다 훨씬 큰 메모리 page를 의미합니다. 데이터베이스나 가상화와 같이 대용량 메모리 사용이 필요한 애플리케이션에서 성능 향상을 위해 사용될 수 있습니다.                                                                           |
| `pids`    | 제어 리소스 : 프로세스 및 쓰레드 수<br><br>cgroup에 속할 수 있는 최대 프로세스 및 쓰레드 수를 제어합니다.<br>fork bomb 과 같은 공격으로부터 시스템을 보호하는데 유용합니다.                                                                                                                                             |
| `rdma`    | 제어 리소스 : RDMA (Remote Direct Memory Access) 관련 리소스<br><br>RDMA는 원격 시스템의 메모리에 직접 접근할 수 있도록 하는 기술입니다.<br>이 컨트롤러는 RDMA 리소스 사용량을 제어합니다. 예를 들어, RDMA 메모리 사용량, RDMA 네트워크 대역폭 등을 제어할 수 있습니다. HPC 환경에서 주로 사용됩니다.                                                    |
| `misc`    | 제어 리소스 : 다양한 기타 리소스, 아직 별도의 컨트롤러로 분리되지 않은 리소스<br><br>특정 범주에 속하지 않는 다양한 '기타' 리소스 또는 새로운 유형의 리소스에 대한 제어를 위해 존재하는 컨트롤러입니다. 리눅스 커널 버전과 시스템 구성에 따라 제어하는 구체적인 리소스가 달라질 수 있습니다.                                                                                    |


`cgroupv2` - 기타 파일 종류 및 기능

| 파일명              | 설명                                       |
| ---------------- | ---------------------------------------- |
| `cgroup.subtree` | 자식 `cgroup`에 어떤 리소스 컨트롤러를 활성화할 지 결정하는 파일 |
| `cpu.max`        | CPU 제한 (quota/period)                    |
| `cpu.stat`       | 사용된 시간, throttling 정보 등 제공               |
| `cpu.weight`     | 스케줄링 가중치 (v1의 shares 대체)                 |



---

### 활용 사례

- Kubernetes Pod Resource Request/Limit
	- Pod 생성 시, YAML 명세서에 Resource 제한 가능

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  ...
    resources:
      request:
        cpu: 100m
        memory: 200Mi
      limits:
        cpu: 700m
        memory: 2000Mi
...
```
