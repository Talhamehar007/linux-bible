# launchd.plist Cheat Sheet (macOS)

## 📂 File locations
- **User jobs (LaunchAgents)** → `~/Library/LaunchAgents/`  
- **System-wide jobs (LaunchDaemons)** → `/Library/LaunchDaemons/`  
- (Apple’s own) `/System/Library/LaunchAgents/` & `/System/Library/LaunchDaemons/`  

**Permissions:** must be `root:wheel 644` for `/Library/LaunchDaemons/` jobs.  

---

## 🟢 Core Identification
| Key | Type | Description | systemd Equivalent |
|-----|------|-------------|---------------------|
| **Label** | String | Unique job name (used with `launchctl`) | `[Unit] Name` |
| **Program** | String | Path to binary (use absolute path) | `ExecStart=` |
| **ProgramArguments** | Array | Command and args (`["/bin/sh","-c","echo hi"]`) | `ExecStart=` |

---

## 🟢 Working Environment
| Key | Type | Description | Example |
|-----|------|-------------|---------|
| **WorkingDirectory** | String | `cd` into this dir before starting | `/Users/abu` |
| **EnvironmentVariables** | Dict | Custom environment vars | `<key>PATH</key><string>...</string>` |
| **UserName** | String | Run job as this user (LaunchDaemon only) | `abu`, `root`, `_www` |
| **GroupName** | String | Run under this group | `_www` |
| **Umask** | Integer | File permission mask (octal) | `18` = `0022` |

---

## 🟢 Run Conditions & Scheduling
| Key | Type | Description | systemd Equivalent |
|-----|------|-------------|---------------------|
| **RunAtLoad** | Boolean | Start immediately when loaded/booted | `WantedBy=multi-user.target` |
| **KeepAlive** | Bool/Dict | Restart rules. Dict supports conditions (see below) | `Restart=always`, `RestartSec=` |
| **StartInterval** | Integer | Run every N seconds | `OnUnitActiveSec=` |
| **StartCalendarInterval** | Dict/Array | Calendar-like schedule | `OnCalendar=` |
| **ThrottleInterval** | Integer | Min seconds between restarts | `RestartSec=` |
| **Disabled** | Boolean | If true, job won’t run unless enabled manually | `systemctl disable` |

---

## 🟢 KeepAlive (advanced dictionary form)
```xml
<key>KeepAlive</key>
<dict>
  <key>SuccessfulExit</key><false/>         <!-- Restart only if crashed -->
  <key>NetworkState</</key><true/>            <!-- Only run when network up -->
  <key>PathState</key>                      <!-- Path must exist -->
  <dict>
    <key>/Volumes/Passport_1</key><true/>
  </dict>
  <key>OtherJobEnabled</key><array>         <!-- Depend on other job -->
    <string>com.apple.mDNSResponder</string>
  </array>
</dict>
```

Conditions available:
- `true` → Always restart.  
- `SuccessfulExit=false` → Restart on crash only.  
- `NetworkState=true` → Restart only if network is up.  
- `PathState` → Restart only if file/dir exists.  
- `OtherJobEnabled` → Restart only if another job is enabled.  

---

## 🟢 Input / Output
| Key | Type | Description |
|-----|------|-------------|
| **StandardInPath** | String | File/device for stdin |
| **StandardOutPath** | String | Redirect stdout |
| **StandardErrorPath** | String | Redirect stderr |
| **EnablePressuredExit** | Boolean | Allow system to terminate job under pressure (low memory) |

---

## 🟢 Process Behavior
| Key | Type | Description | systemd Equivalent |
|-----|------|-------------|---------------------|
| **ProcessType** | String | Hints to launchd about app type | `Background`, `Interactive`, `Adaptive` |
| **LimitLoadToSessionType** | String/Array | Restrict to session type | `Aqua` (GUI), `Background`, `System` |
| **Sockets** | Dict | Have launchd create sockets and pass them to process | `Socket=` |
| **MachServices** | Dict | Register Mach ports for IPC | none (Apple-specific) |
| **EnableGlobbing** | Boolean | Expand `*` in ProgramArguments | none |
| **EnableTransactions** | Boolean | Job holds system awake until exit | `RefuseManualStart=no` kind of |

---

## 🟢 Security
| Key | Type | Description |
|-----|------|-------------|
| **Umask** | Integer | Default permissions mask |
| **UserName** | String | Drop privileges, run as specific user |
| **GroupName** | String | Run under specific group |
| **RootDirectory** | String | `chroot` before execution |
| **WorkingDirectory** | String | Safe place to run job |

---

## 🟢 Scheduling Examples
**Every 5 minutes**
```xml
<key>StartInterval</key>
<integer>300</integer>
```

**Daily at 3:00 AM**
```xml
<key>StartCalendarInterval</key>
<dict>
  <key>Hour</key><integer>3</integer>
  <key>Minute</key><integer>0</integer>
</dict>
```

**Every Monday at 8:30 AM**
```xml
<key>StartCalendarInterval</key>
<dict>
  <key>Weekday</key><integer>1</integer> <!-- Sunday=0 -->
  <key>Hour</key><integer>8</integer>
  <key>Minute</key><integer>30</integer>
</dict>
```

---

## 🟢 Common Systemd → Launchd mapping
| systemd | launchd |
|---------|----------|
| `After=network.target` | `<KeepAlive><dict><NetworkState><true/></dict>` |
| `RequiresMountsFor=/mnt` | `<KeepAlive><dict><PathState>…</PathState></dict>` |
| `Restart=always` | `<KeepAlive>true</KeepAlive>` |
| `Restart=on-failure` | `<KeepAlive><dict><SuccessfulExit><false/></dict>` |
| `RestartSec=30` | `<ThrottleInterval>30</ThrottleInterval>` |
| `OnCalendar=daily` | `<StartCalendarInterval>…</StartCalendarInterval>` |
| `ExecStart=` | `ProgramArguments` |
| `Environment=` | `EnvironmentVariables` |

---

# ✅ Key Takeaways
- **LaunchAgents** → per-user, start at login (`gui/UID`).  
- **LaunchDaemons** → system-wide, start at boot (`system`).  
- **No direct After=…** → instead, use `KeepAlive` conditions (`NetworkState`, `PathState`).  
- **No RestartSec=…** → use `ThrottleInterval`.  
- **No cron needed** → use `StartInterval` / `StartCalendarInterval`.  
