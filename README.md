# TrueNAS SCALE App Dataset Helper

**TL;DR** One Bash script that creates (or ensures) ZFS datasets for your apps under the “Apps” preset, applies the right NFSv4 ACLs (`apps:apps`), supports `--dry-run`, and remembers your pool/root defaults.

---

## Features

- Uses the **middleware CLI `midclt`** – matches how SCALE itself manages storage.
- **Idempotent** – skips datasets that already exist.  
- **`--dry-run`** – preview JSON payloads & ACL decisions with zero changes.  
- **`--force‑acl`** – re‑apply ACL & ownership to existing datasets.  
- Saves your preferred **pool** and **root dataset** in a dot‑file next to the script.  
- Easy-to-read logs with clear messages.

---

## Requirements

* TrueNAS SCALE (Electric Eel +)
* Root / sudo
* `jq` (preinstalled on SCALE)

---

## Download & Run

```bash
# download
curl -Lo create_app_dataset.sh \
  https://raw.githubusercontent.com/mostafawahied/create-app-dataset/main/create_app_dataset.sh

# make executable
chmod +x create_app_dataset.sh

# test with dry‑run
sudo ./create_app_dataset.sh --dry-run immich config data upload

# actual run
sudo ./create_app_dataset.sh immich config data upload
```

(You can place the script anywhere, e.g. `/mnt/tank/scripts/`.)

---

## Options

* `-p, --pool <name>` Override ZFS pool (and save it in a dot-file next to the script).
* `-r, --root <name>` Override parent root dataset (e.g. `apps-config` and save it in a dot-file next to the script).
* `-f, --force-acl` Re‑apply ACL & ownership even on existing datasets.
* `--dry-run` Preview everything, change nothing.
* `-h, --help` Usage message.

---

## Examples

```bash
# from the script's dir

# dry‑run first
# create app with no sub-datasets
sudo ./create_app_dataset.sh --dry-run portracker
# actual run
sudo ./create_app_dataset.sh portracker
# create app + sub-datasets
sudo ./create_app_dataset.sh portracker config data
# different pool & root, also saved for next time in .create_app_dataset.conf
sudo ./create_app_dataset.sh -p tank -r appdata portracker
# fix permissions on an existing app tree
sudo ./create_app_dataset.sh --force-acl portracker config data
# add child datasets to an existing app dataset, if you already have portracker created
# (e.g. portracker/config, portracker/data)
sudo ./create_app_dataset.sh portracker config data
# or if you already have portracker/config, this will add data as another sub-dataset.
sudo ./create_app_dataset.sh portracker config data
```

---

## Configuration File

A file named `.create_app_dataset.conf` lives next to the script:

```bash
POOL_NAME="tank"
PARENT_DATASET_ROOT="apps-config"
```

If the script directory isn’t writable, it won’t save; you can use `-p/-r` flags, configure it manually in `.create_app_dataset.conf`, or adjust permissions (`chmod 755 /path/to/scripts`).

---

## Notes & Warnings

* Always run a **dry‑run** first.
* Script is **SCALE‑only**; it will not work on TrueNAS CORE since it's based on FreeBSD.
* ACLs/ownership default to `apps:apps`. Tweak manually if your use‑case differs.

---

## License

MIT – see [LICENSE](LICENSE).
