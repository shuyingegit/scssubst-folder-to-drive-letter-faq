# FAQ: giving a local folder its own drive letter on Windows

`subst` is one of those built-in commands that solves a small problem extremely well and then surprises you once. These are the questions I get, with the answers I wish someone had told me first. Disclosure: I work on ScsSubst (https://subst.scsoi.com/), so the product mentions are biased — the `subst` behaviour described here is Microsoft's, not ours.

## Does it copy the files?

No. `subst Z: D:\projects\my-thing` does not copy, move or duplicate anything. It creates a second name for an existing local folder, so `Z:\` and `D:\projects\my-thing\` are the same bytes on the same disk. Delete one, you delete the other. If you were expecting a separate copy, that is the wrong tool.

## Does the letter survive a reboot or a log-off?

No — and this is the part people get wrong. A `subst` mapping lives in the **Windows session** that created it. Log off and it is gone; reboot and it is gone.

What that breaks in practice: a service or scheduled task that runs as the system account will not see `Z:` even if your desktop session still has it, because system work runs in Session 0. The same applies to the other way round — a mapping made by a scheduled task is invisible to your desktop.

ScsSubst keeps the list of mappings and can re-apply them at start-up, so the letters come back when **you** sign in again — not at boot, and not for Session 0 services.

## Do I need administrator rights? Does it install a driver?

No to both. `subst` is part of Windows and works with an ordinary user account, and it uses plain Windows APIs — there is no file-system driver involved at all, no kernel component, nothing to install.

## Can I map a network share?

No. `subst` maps a path on a local drive; a UNC path is not a valid target. If the folder you want to give a letter to lives on a NAS or a file server, `subst` is the wrong tool — you want something that actually mounts the remote side (see https://drive.scsoi.com/ for a WebDAV/NAS client that mounts it as a real local disk).

## What are the actual limitations?

- Local folders only, no UNC, no network shares.
- The mapping is per-session, as described above.
- The letter is a convenience for the tools that respect it. Some applications resolve the underlying path on their own (when they display the real folder, for example), so the "shorter path" is not always visible in the UI.
- No permissions, encryption or quotas are added by the mapping. It exposes the folder under a second name, nothing more.

## Why would I want this at all?

The classic reason is a deep folder you keep typing: `D:\work\clients\2026\acme\project\...`. A letter removes the typing. The other reason I care about is isolating a workspace: give one folder per project (or per agent) its own letter, and tools that only accept a drive root have something to point at.

## Where to get the GUI version

The official site and download: https://subst.scsoi.com/

The longer measured write-up, including where `Z:` really points and what it exposes, is here: https://github.com/shuyingegit/scssubst-drive-letter-mapping-guide
