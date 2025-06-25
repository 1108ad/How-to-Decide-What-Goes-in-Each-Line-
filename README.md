# How-to-Decide-What-Goes-in-Each-Line-

| Line Purpose | Decide Based On                                           |
| ------------ | --------------------------------------------------------- |
| `name:`      | What is the task trying to achieve?                       |
| `hosts:`     | Where do you want this task to run (inventory group)?     |
| `become:`    | Do you need sudo/root privileges for the task?            |
| `vars:`      | Are there values that may change (e.g., ports, users)?    |
| `tasks:`     | What actual operations are needed? (install, start, copy) |
| `notify:`    | Should the task trigger another action (e.g., restart)?   |
| `handlers:`  | What to do when a task triggers a change?                 |
