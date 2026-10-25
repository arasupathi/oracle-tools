# Oracle Memory Sizer

A single-file, dependency-free calculator for sizing Oracle Database memory on Linux.

Enter the host (RAM, instance count, `PROCESSES`, storage type, HugePages yes/no) and the workload (OLTP / mixed / DW, optional database size, In-Memory size). It returns:

- the **SGA / PGA split** per instance and the host budget behind it
- `PGA_AGGREGATE_LIMIT` capped so that **SGA + limit + overhead ≤ RAM** (the 2× default often oversubscribes a large host)
- `vm.nr_hugepages`, `memlock`, `kernel.shmmax`, `kernel.shmall`, swap size
- a copy-ready **SPFILE block** and **sysctl / limits.conf block**
- the **ASMM vs AMM verdict** with the reasons, and a page-table estimate for running without HugePages

Every line is tagged **DOC** (Oracle-documented rule) or **PRACTICE** (practitioner rule of thumb), so you know which numbers to defend from the documentation and which to adjust to your workload.

## Use it

Open `index.html` in any browser, or use the hosted copy at https://arasupathi.github.io/oracle-tools/oracle-memory-sizer/

No build, no server, no network calls (the only external resource is a Google Font with a system-font fallback).


## What it assumes

| Item | Rule | Tag |
| --- | --- | --- |
| Kernel, slab, page cache | 4% of RAM on ASM, 8% on filesystem storage | PRACTICE |
| Grid Infrastructure + ASM | 2–10 GB | PRACTICE |
| Process overhead | 10 MB × PROCESSES × instances (binary, stack, malloc heap, outside SGA and PGA) | PRACTICE |
| Agents, backup, sshd | ≤ 10 GB | PRACTICE |
| Headroom | 5% of RAM, kept free even when every instance hits its PGA limit | PRACTICE |
| SGA : PGA split | OLTP 75/25, mixed 65/35, DW 45/55 | PRACTICE |
| `PGA_AGGREGATE_LIMIT` | MIN(2 × target, RAM − ΣSGA − overhead − headroom) | PRACTICE |
| `vm.nr_hugepages` | ⌈ΣSGA_MAX_SIZE / 2 MB⌉ + 5 per instance | DOC (MOS 401749.1 method) |
| memlock | ≥ 90% of RAM when HugePages are used, and ≥ the pool | DOC (Installation Guide for Linux) |
| swap | 1.5 × RAM (≤ 2 GB), = RAM (2–16 GB), 16 GB (> 16 GB) | DOC (Installation Guide for Linux) |
| Transparent HugePages | `never` | DOC |
| AMM and HugePages | mutually exclusive; `MEMORY_TARGET` must be 0 | DOC (DBA Reference for Linux, MOS 749851.1) |
| Verdict | ASMM + HugePages everywhere; AMM only when SGA + PGA < 4 GB and no HugePages | PRACTICE |

The output is a starting point. Confirm it against `v$pgastat` (`maximum PGA allocated`), `v$db_cache_advice`, `v$pga_target_advice` and the alert log's *Large Pages Information* block after a representative week.

## Companion material

- `../linkedin/Oracle_Memory_Layer_Carousel.pdf` — the 14-page notebook carousel explaining the memory layer: Linux pages and page tables, SGA/PGA, the HugePages arithmetic, swap vs paging, AMM vs ASMM, and a 755 GB server sized with and without HugePages.

## Sources

- Oracle Database Installation Guide for Linux — swap table, memlock, Transparent HugePages
- Oracle Database Administrator's Reference for Linux and UNIX — HugePages, `USE_LARGE_PAGES`, AMM incompatibility
- Oracle Database Reference — `PGA_AGGREGATE_LIMIT` default, `SGA_MAX_SIZE`, `USE_LARGE_PAGES`
- Oracle Database Performance Tuning Guide — initial PGA sizing (80% rule, OLTP 20% / DSS 50%)
- MOS 401749.1 (`hugepages_settings.sh`), MOS 749851.1 (HugePages and AMM), MOS 947152.1 (granule sizes)

## Licence

MIT. Use it, fork it, correct it — pull requests with a documentation reference are welcome.

— Arasu · @arasu_oracle_dba on LinkedIn
