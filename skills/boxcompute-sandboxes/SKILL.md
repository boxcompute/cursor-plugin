---
name: boxcompute-sandboxes
description: Create and drive BoxCompute sandbox VMs from Cursor. Use when the
  user wants to run commands in a clean cloud VM, test code on Linux, spin up a
  disposable environment, edit files on a remote VM, or share a public preview
  URL for something running on a VM.
---

# BoxCompute Sandboxes

This plugin connects Cursor to the hosted BoxCompute MCP server at
`https://mcp.boxcompute.ai/mcp`. Its tools create and drive disposable Linux
VMs ("sandboxes"). Authentication is OAuth: the first tool call sends the user
through BoxCompute sign-in. Users with an API key instead can add an
`Authorization: Bearer ${env:BOXCOMPUTE_API_KEY}` header to the server in
`~/.cursor/mcp.json`.

## When to use

- "Run this on a fresh Linux machine" or any task that should not touch the
  user's local environment.
- Testing a build, script, or install path on a clean VM.
- Running something long-lived and sharing it: `preview_create` returns a
  one-hour public HTTPS URL for a VM port.

## How to work

1. Call `sandbox_create` for a new VM (small, large, xl or xxl; internet on or
   off; xl and xxl need a Starter plan). It waits for the boot and returns a
   ready VM. Use `sandbox_list` / `sandbox_get` first when the user may
   already have one.
2. Run commands with `command_run` (bash, up to 60 seconds per call, 48 KiB of
   captured output). For work that outlives that, use `process_start`, then
   poll with `process_get` / `process_output` (durable background commands up
   to 15 minutes), and `process_cancel` to stop one.
3. Read and edit files with `file_read`, `file_list`, `file_write` (2 MiB,
   creates parents) and `file_edit` (SHA-guarded replace). Paths stay under
   `/workspace`.
4. To show the user a web app or service, start it on the VM and call
   `preview_create` for its port; share the returned URL. `preview_list` and
   `preview_close` manage existing previews.
5. Leave things tidy: `sandbox_stop` sleeps a VM and keeps `/workspace`,
   `sandbox_delete` removes the VM and its disk. A stopped VM is woken
   automatically by the next command or file tool call, but the wake counts
   against that call's time budget, so the first call after a sleep may need a
   retry.

Tool availability follows the signed-in account's scopes: read-only keys see
only the read tools. Every call is audited against the account.
