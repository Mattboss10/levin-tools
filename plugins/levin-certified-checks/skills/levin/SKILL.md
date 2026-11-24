---
name: levin
description: Use when a CBMC (C, C++) or Kani (Rust) proof is slow, taking more than a few seconds, to get a fast, certified answer from Levin, which splits the SAT formula across many machines that solve it at once. Only the numbered formula is sent, never source code or names.
---

# Levin for slow CBMC and Kani checks

1. Run the check here first with a 5-second limit: `timeout 5 cbmc <args>` or
   `timeout 5 cargo kani --harness <name>`. Without `timeout` (macOS):
   `perl -e 'alarm shift; exec @ARGV' 5 <command>`. If it finishes, you are done: Levin is
   not needed.
2. Before the first send in a session, tell the user that the check's numbered SAT formula
   (no source code, file names or variable names) goes to Levin at mcp.commandersolver.com,
   and ask for their OK. Do not send anything without it.
3. If `./levin-sat` is not already in the folder you run the check from, call the
   `levin_setup` tool of the `levin` MCP server with the checker's name (`cbmc` or `kani`).
   It returns a short shell script. Show it to the user, save it there as `levin-sat`, and
   run `chmod +x levin-sat`. The script only posts the formula the checker gives it to
   `https://mcp.commandersolver.com/v1/solve` and passes the answer back.
4. Run the check again with Levin as the SAT solver:
   - CBMC: `cbmc <args> --trace --external-sat-solver ./levin-sat`
   - Kani: `cargo kani --harness <name> --solver bin=<folder>/levin-sat`, where `<folder>` is
     the full path of the folder you saved `levin-sat` in, written out (Kani needs a full
     path here, not `./levin-sat`)

   Levin can take up to 15 minutes, longer than a foreground command may run in Claude
   Code (10 minutes at most). Run this command **in the background** and wait for it to
   finish; do not poll it in a loop. The checker prints its own result: VERIFICATION
   SUCCESSFUL means every piece's proof was checked by Levin's validator; a failure comes
   with the checker's counterexample.
5. If Levin has no answer (an error about the solver, "s UNKNOWN", over the free allowance,
   or busy), run the check here without the time limit, in the background. Using Levin must
   never leave the user worse off than not using it.
6. Report the result with the exact command and its bounds (harness, `--unwind`, `-D`
   values): the answer holds for those bounds only.
7. The job id is on the last line of `./levin.log` (`c levin job <id> ...`), next to
   `levin-sat`. The `levin_job` tool returns its record: time, pieces and the fingerprints of
   the checked proofs. Keep this to a line or two in your reply.
