# Ansible Play Book Layers
How-to-Decide-What-Goes-in-Each-Line-

| Line Purpose | Decide Based On                                           |
| ------------ | --------------------------------------------------------- |
| `name:`      | What is the task trying to achieve?                       |
| `hosts:`     | Where do you want this task to run (inventory group)?     |
| `become:`    | Do you need sudo/root privileges for the task?            |
| `vars:`      | Are there values that may change (e.g., ports, users)?    |
| `tasks:`     | What actual operations are needed? (install, start, copy) |
| `notify:`    | Should the task trigger another action (e.g., restart)?   |
| `handlers:`  | What to do when a task triggers a change?                 |



Let’s clarify the **purpose of `become`**, where it should be placed, and **why it’s sometimes needed—and sometimes not**.

---

### 🔹 What is `become` in Ansible?

`become: yes` is used to **run a task or playbook with elevated privileges**, typically as the `root` user (similar to using `sudo`).

---

### ✅ Where Should `become` Be Placed?

You can place `become: yes` in **three valid places**, depending on scope:

| Scope                         | Where to place                   | Example |
| ----------------------------- | -------------------------------- | ------- |
| 🔄 Play-level (for all tasks) | Directly under the `hosts:` line |         |

```yaml
- name: Install nginx
  hosts: web
  become: yes
  tasks:
    ...
```

\| 🔁 Task-level (specific task only) | Inside a task |

```yaml
- name: Restart nginx
  become: yes
  service:
    name: nginx
    state: restarted
```

\| 🌍 Global setting (not recommended unless consistent) | In `ansible.cfg` or inventory |

```ini
[web]
10.0.0.1 ansible_user=ubuntu ansible_become=true
```

---

### ❓ Why Is `become` Needed?

Because **some actions require root privileges**, such as:

* Installing packages (`apt`, `yum`)
* Starting/stopping services (`service`, `systemd`)
* Modifying system files (`copy`, `template` to `/etc/`, `/var/www/`, etc.)

If you run without `become: yes`, those tasks will fail with permission errors.

---

### ❓ Why Might `become` Not Be Needed?

* You're only **reading** system info (e.g., gathering facts)
* You're doing **user-level tasks** (like copying to home dir)
* You're on a system where the Ansible user is already `root`
* You're testing syntax or logic only (`--check`, `--syntax-check`)

---

### ✅ Recap Example: With and Without `become`

```yaml
---
- name: Install nginx
  hosts: web
  become: yes     # required here to install package
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present

    - name: Copy welcome page
      copy:
        src: files/index.html
        dest: /var/www/html/index.html
```

Without `become: yes`, both tasks above would likely fail on permission denied errors.

---

### 📌 Final Tip:

If you're not sure whether a task needs `become`, ask:

> "Would I need to use `sudo` for this command on the terminal?"

If yes → use `become: yes`.
