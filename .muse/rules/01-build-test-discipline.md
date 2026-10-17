# 01 — Build-test discipline

Never push what has not been tested. Build on this machine, install it,
run it, watch it work — then GitHub sees it, once, when green.

Test every fix on every corner. Fix properly, never patch and move on.
Re-test after each new fix until everything works.

Never stack CI runs: cancel queued and pending, fire ONE fresh run. If a
run fails, read the log and fix the root cause on the spot. CI is not a
debugger.

Announce the build path at the start of every build so the choice is
visible instead of discovered later.
