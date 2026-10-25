# oracle-tools

Small, single-file tools for Oracle DBAs. No install, no server: open the HTML in a browser or use the hosted copy on GitHub Pages.

| Tool | What it does | Open |
| --- | --- | --- |
| [Oracle Memory Sizer](oracle-memory-sizer/) | Sizes SGA/PGA, `PGA_AGGREGATE_LIMIT`, `vm.nr_hugepages`, memlock, shmmax/shmall and swap from RAM, instance count, `PROCESSES` and workload. Copy-ready SPFILE and sysctl blocks. Every rule tagged DOC (Oracle-documented) or PRACTICE (rule of thumb). | https://arasupathi.github.io/oracle-tools/oracle-memory-sizer/ |

## Companion material

- `linkedin/` — the notebook-style carousels I post on LinkedIn that explain the ideas behind each tool.

## Hosting

GitHub Pages serves this repository from the `main` branch root, so each tool's folder is reachable at `https://arasupathi.github.io/oracle-tools/<tool>/`.

## Licence

MIT. Corrections with a documentation reference are welcome as issues or pull requests.

— Arasu · @arasu_oracle_dba on LinkedIn · [Learn RAC with Arasu](https://arasupathi.github.io/learn-rac-with-arasu/)
