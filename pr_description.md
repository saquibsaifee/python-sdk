🔒 Fix Command Injection vulnerability in dev command

🎯 **What:**
Fixed a command injection vulnerability (CWE-78) in the `mcp dev` command that was caused by using `shell=True` (on Windows) when running the `subprocess.run` command containing arguments from user input.

⚠️ **Risk:**
When `subprocess.run` is executed with `shell=True`, it relies on the shell to interpret the command string (or list, where the first element is passed to `cmd.exe /c`). Since user input like `with_packages` could eventually land within the arguments list being passed to the shell, it could potentially allow command injection on Windows systems. This is especially risky as it directly allows execution of arbitrary commands by maliciously formatted inputs.

🛡️ **Solution:**
Removed `shell=True` from the `subprocess.run` execution in `src/mcp/cli/cli.py` (`mcp dev`), preventing the arguments from being evaluated by a shell. By passing an argument list safely with `shell=False` (which is the default behavior if not specified otherwise), the executable safely receives the user inputs as parameters rather than a string to evaluate. Similarly, modified the internal helper `_get_npx_command` to also run without a shell, handling `FileNotFoundError` robustly since it now attempts actual executable lookup without the shell implicitly handling it.
