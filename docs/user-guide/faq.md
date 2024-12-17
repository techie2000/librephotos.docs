---
title: "❓ FAQ"
excerpt: "Scan always fails after importing few images"
sidebar_position: 7
---

## Proxmox - Scan always fails after importing few images

### Symptom

You get errors like these and you use Proxmox:

```
Thread 0x00007fc8dcf3f640 (most recent call first):
  File "/usr/lib/python3.9/threading.py", line 316 in wait
  File "/usr/lib/python3.9/threading.py", line 574 in wait
  File "/usr/local/lib/python3.9/dist-packages/tqdm/_monitor.py", line 59 in run
  File "/usr/lib/python3.9/threading.py", line 954 in _bootstrap_inner
  File "/usr/lib/python3.9/threading.py", line 912 in _bootstrap

Current thread 0x00007fc998cf8640 (most recent call first):
  File "/usr/local/lib/python3.9/dist-packages/face_recognition/api.py", line 162 in <listcomp>
  File "/usr/local/lib/python3.9/dist-packages/face_recognition/api.py", line 162 in _raw_face_landmarks
  File "/usr/local/lib/python3.9/dist-packages/face_recognition/api.py", line 209 in face_encodings
  File "/code/api/models/photo.py", line 507 in _extract_faces
  File "/code/api/directory_watcher.py", line 168 in handle_new_image
  File "/code/api/directory_watcher.py", line 261 in photo_scanner
  File "/usr/lib/python3.9/multiprocessing/pool.py", line 51 in starmapstar
  File "/usr/lib/python3.9/multiprocessing/pool.py", line 125 in worker
  File "/usr/lib/python3.9/multiprocessing/process.py", line 108 in run
  File "/usr/lib/python3.9/multiprocessing/process.py", line 315 in _bootstrap
  File "/usr/lib/python3.9/multiprocessing/popen_fork.py", line 71 in _launch
  File "/usr/lib/python3.9/multiprocessing/popen_fork.py", line 19 in __init__
  File "/usr/lib/python3.9/multiprocessing/context.py", line 277 in _Popen
  File "/usr/lib/python3.9/multiprocessing/process.py", line 121 in start
  File "/usr/lib/python3.9/multiprocessing/pool.py", line 326 in _repopulate_pool_static
  File "/usr/lib/python3.9/multiprocessing/pool.py", line 337 in _maintain_pool
  File "/usr/lib/python3.9/multiprocessing/pool.py", line 513 in _handle_workers
  File "/usr/lib/python3.9/threading.py", line 892 in run
  File "/usr/lib/python3.9/threading.py", line 954 in _bootstrap_inner
  File "/usr/lib/python3.9/threading.py", line 912 in _bootstrap
```

### Solution:

This is a problem with proxmox, More specifically with the way the CPU was emulated. By default, QEMU in Proxmox uses its own CPU type kvm64, which has a reduced CPU flags set, to guaranteed that it works everywhere. This means that necessary flags like SSE and AVX are absent, which causes the Fatal Python error: Illegal instruction error.

To fix this, one must either manually edit the .conf file and add the flags needed to the kvm64 CPU type, or pick a different method of CPU emulation.

To add the flags, edit the xxx.conf file located in /etc/pve/qemu-server/ where xxx is the ID of the VM. The manual can be found here: https://pve.proxmox.com/wiki/Manual:_qm.conf

If you don't care about live migration and moving VMs between nodes, the much easier option is to just change the CPU type to Host. With the Host option, the VM will match the CPU type of the host machine.

### Steps for Proxmox 6.4:

Select your VM and then Hardware tab.
{% include figure image_path="/assets/images/135711616-14903f44-2bdc-4830-82e9-a29562f75762.png" alt="Select your VM and then Hardware tab" %}
Then double-click on Processors or select it and click Edit:
{% include figure image_path="/assets/images/135711657-3cdc2600-2368-440c-af75-00313c8c8997.png" alt="Then double-click on Processors or select it and click Edit"  %}

## error decoding 'Volumes[0]': invalid spec: :/data: empty section between colons / "The "scanDirectory" variable is not set. Defaulting to a blank string."

This error usually means, that you forgot to rename the librephotos.env to .env

## Why is the scan directory not mounted read-only?

You can set the scan directory as read-only, but then a couple of features will not be available. List of impacted features are:

- Writing back metadata to media files
- Uploading images
- Deleting images

This is not yet handled gracefully, which means, you will probably just get non-descriptive errors, when you are trying to use these features.

## Videos are not playing and showing a 404 error

### Reason 1:
The video may be encoded in h.265 format, which is not supported by your browser. You can enable "Always transcode videos" under Experimental Settings to resolve this issue. On the other hand, this will be quite CPU heavy.

### Reason 2:
The issue could be caused by incorrect file permissions. You can resolve this by adjusting the permissions using the following commands:

```
find . -type d -exec chmod 755 {} +
find . -type f -exec chmod 644 {} +
```

These commands will ensure that directories have permissions set to 755 and files have permissions set to 644, which should resolve any permission-related issues causing the 404 error.






