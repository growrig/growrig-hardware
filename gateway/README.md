# GrowRig Gateway

*Specialized router for growing needs — separation of the grow network from the
home network.*

**Status:** Concept

## Problem

Grow gear is a security and reliability liability on a home LAN: cheap Wi-Fi plugs
and cameras with questionable firmware, chatty cloud connections, and a setup that
must *never* drop (a stalled controller can cook a crop). Most growers can't build
a VLAN, a firewall policy, and failover by hand. They need that isolation as a
plug-in box.

## Approach

A small router that creates a dedicated, isolated **grow network** — its own SSID
and subnet, firewalled off from the home LAN, with sane defaults for grow devices,
local-first operation, and optional cellular failover so the grow stays online.

## Draft spec

- **Platform:** OpenWrt-based on a low-power router SoC (or a Pi CM5 + managed
  switch carrier)
- **Isolation:** separate grow VLAN/SSID; default-deny between grow LAN and home
  LAN; per-device firewall profiles
- **Radios:** Wi-Fi 6 for grow devices; wired uplink to home router; optional
  Thread border router
- **Resilience:** optional LTE/5G failover dongle; local DNS/NTP so the grow keeps
  running with WAN down
- **Management:** zero-config pairing with the [Hub](../hub/); block/allow cloud
  access per device; traffic visibility
- **Ports:** 2.5 GbE to Hub, a couple of LAN ports for wired grow gear

## Why it matters

Trust and uptime. It lets a grower add sketchy-but-cheap devices without exposing
their home network, and keeps the grow alive when the internet isn't.

## Open questions

- Standalone router vs. a mode/add-on that also lives inside the Hub.
- How opinionated the firewall defaults should be vs. grower control.
- Whether to bundle the Thread/Matter border router here or in the Hub.
- Cellular failover: built-in modem vs. bring-your-own dongle.

## Related

Sits between the home LAN and the [Hub](../hub/); protects every device in this repo.
