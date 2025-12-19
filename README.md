🌐 Live Documentation  
https://techdirectconfig.ernerlin.com


# Dell-TechDirect-Configuration-guide
A practical, end-to-end implementation guide for configuring Dell TechDirect and deploying SupportAssist Enterprise at scale using Microsoft Intune. This repository documents the full deployment workflow, prerequisites, architecture considerations, and operational lessons learned from real-world enterprise environments.  Designed for Endpoint, Modern Workplace, and Infrastructure Engineers looking to automate firmware updates, improve device health visibility, and standardise Dell fleet management through Intune.

# Dell SupportAssist Enterprise Deployment via Microsoft Intune

<p align="center">
  <img src="docs/images/dell-supportassist-banner.png" alt="Dell SupportAssist" width="600"/>
</p>

<p align="center">
  <strong>Automated deployment of Dell SupportAssist for Business PCs using Microsoft Intune Win32 App packaging</strong>
</p>

<p align="center">
  <a href="#-project-overview">Overview</a> •
  <a href="#-objectives">Objectives</a> •
  <a href="#-architecture">Architecture</a> •
  <a href="#-prerequisites">Prerequisites</a> •
  <a href="#-implementation-phases">Implementation</a> •
  <a href="#-step-by-step-deployment-guide">Deployment Guide</a>
</p>

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Objectives](#-objectives)
- [Architecture & Design Overview](#-architecture--design-overview)
- [Prerequisites](#-prerequisites)
- [Tools & Technologies Used](#-tools--technologies-used)
- [Implementation Phases](#-implementation-phases)
- [Step-by-Step Deployment Guide](#-step-by-step-deployment-guide)
  - [Part A: Creating the Deployment Package](#part-a-creating-the-deployment-package-using-dell-deployment-package-manager)
  - [Part B: Preparing Files for Intune](#part-b-preparing-files-for-microsoft-intune)
  - [Part C: Configuring in Microsoft Intune](#part-c-configure-in-microsoft-intune)
- [Security & Governance Considerations](#-security--governance-considerations)
- [Testing & Validation](#-testing--validation)
- [Deployment & Maintenance](#-deployment--maintenance)
- [Troubleshooting](#-troubleshooting)
- [Lessons Learned & Future Improvements](#-lessons-learned--future-improvements)
- [Quick Reference](#-quick-reference)

---

## 🎯 Project Overview

### Background

Managing hardware health, diagnostics, and support for a fleet of Dell business computers presents significant operational challenges. Without a centralized solution, IT teams must manually address hardware issues, resulting in increased downtime, reactive support workflows, and reduced productivity across the organization.

### The Problem

Organizations with Dell device fleets face several critical challenges:

- **Reactive Support Model**: Hardware issues are discovered only after failures occur, leading to unexpected downtime
- **Manual Diagnostics**: IT staff must physically access devices to run diagnostics, consuming valuable time and resources
- **Inconsistent Coverage**: Without automated deployment, support agents may not be installed uniformly across all devices
- **Limited Visibility**: No centralized dashboard to monitor fleet health and predict potential failures
- **Support Overhead**: High volume of manual support tickets for issues that could be detected proactively

### The Solution

This project implements an automated, enterprise-wide deployment of **Dell SupportAssist for Business PCs** using **Microsoft Intune** as the deployment platform. The solution provides:

- **Proactive Monitoring**: Automated hardware health monitoring with predictive failure detection
- **Centralized Management**: Fleet-wide visibility through Dell TechDirect console integration
- **Automated Deployment**: Zero-touch installation via Microsoft Intune Win32 app packaging
- **Self-Healing Capabilities**: Automated issue resolution and driver updates
- **Remote Support**: Built-in remote assistance capabilities for IT support staff

### Key Features Deployed

| Feature | Description | Benefit |
|---------|-------------|---------|
| **Hardware Diagnostics** | Comprehensive hardware testing and validation | Early detection of component failures |
| **Predictive Analytics** | AI-driven failure prediction | Prevent downtime before it occurs |
| **Remote Assistance** | Secure remote support capabilities | Faster issue resolution |
| **Automated Updates** | Driver and firmware update management | Consistent system performance |
| **Fleet Monitoring** | Centralized TechDirect dashboard | Complete visibility across all devices |
| **Central Resource Manager** | BIOS password storage for fleet management | Simplified BIOS update deployment |

---

## 🎯 Objectives

### Primary Objectives

1. **Deploy Dell SupportAssist to 100% of Dell business devices** managed by Microsoft Intune
2. **Eliminate manual installation processes** by automating deployment through Intune Win32 app packaging
3. **Ensure proper dependency management** by deploying .NET 8.0 Runtime as a prerequisite
4. **Enable proactive hardware monitoring** across the entire Dell device fleet
5. **Integrate with Dell TechDirect** for centralized fleet management and support

### Success Criteria

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| Deployment Success Rate | ≥ 95% | Intune installation status reports |
| Dell Device Coverage | 100% | TechDirect registered devices count |
| Installation Failures | < 5% | Intune failure reports |
| Time to Deploy (per device) | < 15 minutes | Intune deployment logs |
| Service Running State | 100% | Detection script validation |

### Business Outcomes

- **Reduced Downtime**: Proactive issue detection reduces unplanned hardware failures
- **Lower Support Costs**: Automated diagnostics and remote support reduce ticket volume
- **Improved User Experience**: Faster issue resolution with minimal user disruption
- **Enhanced Visibility**: Complete fleet health monitoring through TechDirect console
- **Compliance Readiness**: Documented deployment and configuration standards

---

## 🏗 Architecture & Design Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Dell TechDirect Portal                            │
│                     (Fleet Management & Monitoring)                         │
└─────────────────────────────────────────────────────────────────────────────┘
                                      ▲
                                      │ Registration & Telemetry
                                      │
┌─────────────────────────────────────────────────────────────────────────────┐
│                              Dell Cloud Services                            │
│                (Updates, Diagnostics, Predictive Analytics)                 │
└─────────────────────────────────────────────────────────────────────────────┘
                                      ▲
                                      │
                    ┌─────────────────┴─────────────────┐
                    │                                   │
                    ▼                                   ▼
┌───────────────────────────────┐     ┌───────────────────────────────────────┐
│     Dell Business Devices     │     │     Dell Business Devices             │
│   ┌─────────────────────┐     │     │   ┌─────────────────────┐             │
│   │   SupportAssist     │     │     │   │   SupportAssist     │             │
│   │   Agent Service     │     │     │   │   Agent Service     │             │
│   └─────────────────────┘     │     │   └─────────────────────┘             │
│   ┌─────────────────────┐     │     │   ┌─────────────────────┐             │
│   │   .NET 8.0 Runtime  │     │     │   │   .NET 8.0 Runtime  │             │
│   └─────────────────────┘     │     │   └─────────────────────┘             │
└───────────────────────────────┘     └───────────────────────────────────────┘
                    ▲                                   ▲
                    │                                   │
                    └─────────────────┬─────────────────┘
                                      │ Intune Management Extension
                                      │
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Microsoft Intune Admin Center                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                         Win32 App Deployment                         │   │
│  │  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐  │   │
│  │  │   .NET 8.0      │───▶│  SupportAssist  │───▶│   Detection &   │  │   │
│  │  │   Runtime       │    │   Application    │    │   Validation    │  │   │
│  │  │   (Dependency)  │    │   (Win32 App)    │    │   (Scripts)     │  │   │
│  │  └─────────────────┘    └─────────────────┘    └─────────────────┘  │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Deployment Workflow

```
┌────────────────────┐    ┌────────────────────┐    ┌────────────────────┐
│   PART A:          │    │   PART B:          │    │   PART C:          │
│   Package Creation │───▶│   Intune Prep      │───▶│   Intune Config    │
│                    │    │                    │    │                    │
│ • Download         │    │ • Create folder    │    │ • Upload package   │
│   Activation File  │    │   structure        │    │ • Configure app    │
│   from TechDirect  │    │ • Copy files       │    │   settings         │
│ • Run Deployment   │    │ • Download         │    │ • Set install      │
│   Package Manager  │    │   IntuneWinAppUtil │    │   command          │
│ • Validate         │    │ • Create           │    │ • Add detection    │
│   activation       │    │   .intunewin       │    │   rules            │
│ • Configure        │    │   package          │    │ • Assign to        │
│   options          │    │                    │    │   groups           │
│ • Generate output  │    │                    │    │                    │
└────────────────────┘    └────────────────────┘    └────────────────────┘
```

### Component Dependencies

```
Dell SupportAssist Installation
│
├── System Requirements
│   ├── Windows 10/11 (64-bit)
│   ├── Dell-manufactured device
│   └── Internet connectivity
│
├── Software Prerequisites
│   └── .NET 8.0 Desktop Runtime (windowsdesktop-runtime-8.0.x-win-x64.exe)
│       └── Must be installed BEFORE SupportAssist
│
├── Installation Components (Generated by Deployment Package Manager)
│   ├── SupportAssistInstaller-x64.msi (Main installer) - ~18.7 MB
│   ├── SupportAssistConfiguration.mst (Configuration transform) - ~20 KB
│   └── Component CAB Files
│       ├── BASE.cab (~18.5 MB) - Base components
│       ├── CORE.cab (~14 MB) - Core functionality
│       ├── FULL.cab (~148 MB) - Full feature set
│       ├── HWDIAGS.cab (~124 MB) - Hardware diagnostics
│       ├── INSIGHTS.cab (~18 MB) - PC Insights/Predictive analytics
│       └── RAAS.cab (~19 MB) - Self Healing Remediation
│
├── Optional Components
│   └── SupportAssistCentralResourceManager.msi (~35 MB) - BIOS password management
│
└── Post-Installation
    ├── SupportAssist Service (SupportAssistAgent)
    ├── System tray application
    ├── Scheduled diagnostic tasks
    └── TechDirect cloud registration
```

### File Structure

```
C:\IntuneApps\SupportAssist\
│
├── Source\                              # Files to be packaged
│   ├── SupportAssistInstaller-x64.msi   (18,777 KB)
│   ├── SupportAssistConfiguration.mst   (20 KB)
│   ├── BASE.cab                         (18,492 KB)
│   ├── CORE.cab                         (13,953 KB)
│   ├── FULL.cab                         (147,834 KB)
│   ├── HWDIAGS.cab                      (124,453 KB)
│   ├── INSIGHTS.cab                     (17,608 KB)
│   └── RAAS.cab                         (18,867 KB)
│
├── Output\                              # Packaged output
│   └── SupportAssistInstaller-x64.intunewin (~400-500 MB)
│
├── Scripts\                             # Deployment scripts
│   ├── Detect-SupportAssist.ps1
│   ├── Require-DellDevice.ps1
│   └── Verify-Installation.ps1
│
└── Logs\                                # Local logs
    └── deployment-log.txt
```

---

## 📋 Prerequisites

### Administrative Requirements

| Requirement | Description |
|-------------|-------------|
| **Microsoft Intune License** | Active Intune subscription with device management capabilities |
| **Intune Administrator Role** | Appropriate permissions to create and deploy Win32 apps |
| **Dell TechDirect Account** | Business account with SupportAssist deployment access |
| **Azure AD Groups** | Security groups for phased deployment targeting |
| **TechDirect Activation File** | Downloaded from TechDirect portal for package generation |

### Technical Requirements

| Component | Minimum Version | Purpose |
|-----------|-----------------|---------|
| **Windows 10/11** | Version 1809+ | Target operating system |
| **PowerShell** | Version 5.1+ | Script execution |
| **IntuneWinAppUtil.exe** | Latest | Win32 app packaging |
| **.NET 8.0 Desktop Runtime** | 8.0.x | SupportAssist dependency |
| **Dell Deployment Package Manager** | Latest | Generate deployment package |

### Network Requirements

| Endpoint | Protocol/Port | Purpose |
|----------|---------------|---------|
| `techdirect.dell.com` | HTTPS/443 | Activation file download |
| `portal.azure.com` | HTTPS/443 | Intune management |
| `*.manage.microsoft.com` | HTTPS/443 | Intune client communication |
| `*.dell.com` | HTTPS/443 | SupportAssist cloud services |

---

## 🛠 Tools & Technologies Used

| Technology | Purpose | Version |
|------------|---------|---------|
| **Microsoft Intune** | Enterprise endpoint management platform | Current |
| **Dell TechDirect** | Fleet management and activation file source | Current |
| **Dell Deployment Package Manager** | Generate SupportAssist deployment package | 4.9.x |
| **IntuneWinAppUtil** | Microsoft Win32 Content Prep Tool | 1.8.5+ |
| **PowerShell** | Scripting and automation | 5.1+ |
| **.NET 8.0 Runtime** | Application framework dependency | 8.0.19 |
| **Windows Installer (MSI)** | Installation technology | 5.0 |

---

## 📦 Implementation Phases

### Phase Overview

| Phase | Duration | Description |
|-------|----------|-------------|
| **Phase 1** | Day 1 | Download activation file from TechDirect |
| **Phase 2** | Day 1 | Generate deployment package using Deployment Package Manager |
| **Phase 3** | Day 1-2 | Deploy .NET 8.0 Runtime prerequisite |
| **Phase 4** | Day 2 | Package SupportAssist for Intune |
| **Phase 5** | Day 2-3 | Configure and upload to Intune |
| **Phase 6** | Day 3-5 | Pilot deployment and validation |
| **Phase 7** | Day 5+ | Production rollout |

---

## 📖 Step-by-Step Deployment Guide

### Part A: Creating the Deployment Package Using Dell Deployment Package Manager

#### Step 1: Download Activation File from TechDirect

Before using the Deployment Package Manager, you must download an activation file from Dell TechDirect.

1. Navigate to [Dell TechDirect](https://techdirect.dell.com)
2. Sign in with your Dell business account
3. Navigate to: **Connect and manage > Manage PC Fleet > Setup and connect > Download > Activation File**
4. Download the activation file to your computer
5. Note the download location for the next step

---

#### Step 2: Launch Deployment Package Manager - Welcome

Open the **Dell SupportAssist for Business PCs - Deployment Package Manager** application.

The Welcome screen displays the wizard navigation with all configuration steps:
- Welcome
- Activation
- Deployment mode
- Software add-ons
- Proxy
- Output
- Summary

**Action:** Click **Next** to proceed.

> 📸 **Screenshot:** `step1-welcome.png`

![Welcome Screen](docs/images/step1-welcome.png)

---

#### Step 3: Activation - Import and Validate Activation File

On the Activation screen:

1. Click **Browse** to locate your downloaded activation file
2. Navigate to where you saved the activation file from TechDirect
3. Select the file and click **Open**
4. Click **Validate activation file**

**If validation fails**, you'll see:
> ⚠️ "Invalid activation file. Make sure to import a valid file before trying again."

Re-download the activation file from TechDirect if this occurs.

> 📸 **Screenshot:** `step2-activation-invalid.png`

![Activation Invalid](docs/images/step2-activation-invalid.png)

---

#### Step 4: Activation - Successful Validation

Once validated successfully, you'll see:

| Field | Example Value |
|-------|---------------|
| **Site** | `ernerlin.com_1` |
| **Activation file valid till** | `Jan 31, 2027` |

A green success banner confirms: **"Activation file was successfully validated."**

**Action:** Click **Next** to proceed.

> 📸 **Screenshot:** `step3-activation-success.png`

![Activation Success](docs/images/step3-activation-success.png)

---

#### Step 5: Software Add-ons - Central Resource Manager

The Software add-ons screen allows you to configure optional components.

**Central Resource Manager 4.9.0.40923**

This component provides:
- Secure storage for BIOS passwords across your PC fleet
- Required for automated BIOS updates via SupportAssist

> **Note:** Central Resource Manager is compatible with only x64-based systems.

**Options:**
- ○ I want to download
- ○ I don't want to download
- ○ I want to update the existing configuration

**Additional component:**
- **Dell Trusted Device 7.1.4 (optional)** - Security verification for Dell devices

> 📸 **Screenshot:** `step4-software-addons.png`

![Software Add-ons](docs/images/step4-software-addons.png)

---

#### Step 6: Configure Central Resource Manager (If Selected)

If you selected **"I want to download"** for Central Resource Manager:

| Field | Description | Example |
|-------|-------------|---------|
| **Enter seed value** | Numeric encryption seed (hidden) | `••••` |
| **Re-enter seed value** | Confirm seed value | `••••` |
| **Enter address** | IP Address, Hostname, or FQDN of CRM server | `ernerlin.com` |

> ⚠️ **Important:** Document your seed value securely - you'll need it for BIOS password management.

**Action:** Click **Next** to proceed.

> 📸 **Screenshot:** `step5-crm-config.png`

![CRM Configuration](docs/images/step5-crm-config.png)

---

#### Step 7: Proxy Configuration (Optional)

Configure proxy settings if your organization uses a proxy server for internet access.

**Expandable sections:**
- **Dell SupportAssist for Business PCs** - Proxy for SupportAssist communication
- **Central Resource Manager** - Proxy for CRM communication

> **Note:** Skip this step if your organization doesn't use a proxy.

**Action:** Click **Next** to proceed.

> 📸 **Screenshot:** `step6-proxy.png`

![Proxy Configuration](docs/images/step6-proxy.png)

---

#### Step 8: Output - Select File Format

Choose the deployment package format:

| Option | Description | Use Case |
|--------|-------------|----------|
| ○ **Windows Installer Setup Transform (MST)** | Creates MSI/MST files | ✅ **Recommended for Intune** |
| ○ **Administrative Template Files (ADMX/ADML)** | Creates Group Policy templates | Group Policy deployment |

**For Microsoft Intune deployment, select: Windows Installer Setup Transform (MST)**

> 📸 **Screenshot:** `step7-output-format.png`

![Output Format](docs/images/step7-output-format.png)

---

#### Step 9: Output - Select Save Location

After selecting MST format:

1. Click **Browse** to choose the save location
2. Select or create a folder (e.g., `C:\IntuneApps\SupportAssist\Source\`)
3. Click **Generate output** to begin package creation

> 📸 **Screenshot:** `step8-output-location.png`

![Output Location](docs/images/step8-output-location.png)

---

#### Step 10: Package Generation Progress

The wizard downloads and prepares all required components:

| Component | Version | Size | Status |
|-----------|---------|------|--------|
| x64-based processor | - | - | Preparing... |
| Hardware Diagnostics Cab (x64) | 4.9.2.48875 | 121.54 MB | ✅ Completed |
| PC Insights Cab (x64) | 4.9.2.48875 | 17.19 MB | ✅ Completed |
| Self Healing Remediation Cab (x64) | 4.9.2.48875 | 18.42 MB | ✅ Completed |
| Central Resource Manager | 4.9.0.40923 | 33.88 MB | ✅ Completed |

> ⏱️ This process takes several minutes depending on internet speed.

> 📸 **Screenshots:** `step9-generation-progress.png`, `step10-generation-complete.png`

![Generation Progress](docs/images/step9-generation-progress.png)
![Generation Complete](docs/images/step10-generation-complete.png)

---

#### Step 11: Summary - Package Successfully Generated

The Summary screen confirms successful generation:

✅ **"Your SupportAssist deployment package was successfully generated."**

**Actions available:**
- Click **View** to open the output folder
- Click **Finish** to close the wizard

> 📸 **Screenshot:** `step11-summary.png`

![Summary](docs/images/step11-summary.png)

---

#### Step 12: Verify Generated Package Contents

Navigate to your output folder and verify all files:

| File Name | Type | Size | Purpose |
|-----------|------|------|---------|
| `BASE.cab` | Cabinet File | 18,492 KB | Base components |
| `CORE.cab` | Cabinet File | 13,953 KB | Core functionality |
| `FULL.cab` | Cabinet File | 147,834 KB | Full feature set |
| `HWDIAGS.cab` | Cabinet File | 124,453 KB | Hardware Diagnostics |
| `INSIGHTS.cab` | Cabinet File | 17,608 KB | PC Insights |
| `RAAS.cab` | Cabinet File | 18,867 KB | Self Healing Remediation |
| `SupportAssistCentralResourceManager.msi` | Windows Installer | 34,695 KB | Central Resource Manager |
| `SupportAssistConfiguration.mst` | MST File | 20 KB | Configuration transform |
| `SupportAssistDeploymentInstructions_X64.txt` | Text Document | 8 KB | Deployment instructions |
| `SupportAssistInstaller-x64.msi` | Windows Installer | 18,777 KB | Main installer |
| `windowsdesktop-runtime-8.0.19-win-x64.exe` | Application | 57,111 KB | .NET 8.0 Runtime |

> 📸 **Screenshot:** `step12-package-contents.png`

![Package Contents](docs/images/step12-package-contents.png)

---

### Part B: Deploy .NET 8.0 Runtime Prerequisite (Standalone Deployment)

Dell SupportAssist requires .NET 8.0 Desktop Runtime. This must be deployed as a **separate Win32 app** before deploying SupportAssist.

---

#### Step 13: Prepare .NET 8.0 Runtime Files

Create folder structure for .NET deployment:

```powershell
# Create .NET 8.0 directory structure
New-Item -Path "C:\IntuneApps\DotNet8\Source" -ItemType Directory -Force
New-Item -Path "C:\IntuneApps\DotNet8\Output" -ItemType Directory -Force

# Copy .NET installer from generated SupportAssist package
$sourcePath = "C:\Users\[YourUsername]\...\SupportAssistDeploymentPackage\SupportAssist\X64"
Copy-Item "$sourcePath\windowsdesktop-runtime-8.0.19-win-x64.exe" -Destination "C:\IntuneApps\DotNet8\Source\"

Write-Host ".NET 8.0 Runtime source prepared" -ForegroundColor Green
```

> 📸 **Screenshot:** `dotnet-step1-source-files.png`

---

#### Step 14: Package .NET 8.0 for Intune

Open Command Prompt as Administrator:

```cmd
cd C:\IntuneApps

IntuneWinAppUtil.exe -c "C:\IntuneApps\DotNet8\Source" -s "windowsdesktop-runtime-8.0.19-win-x64.exe" -o "C:\IntuneApps\DotNet8\Output" -q
```

**Expected output:** `windowsdesktop-runtime-8.0.19-win-x64.intunewin` (~55-60 MB)

> 📸 **Screenshot:** `dotnet-step2-package-created.png`

---

#### Step 15: Upload .NET 8.0 to Intune

1. Navigate to [Microsoft Intune Admin Center](https://intune.microsoft.com)
2. Go to: **Apps** → **All apps** → **+ Add**
3. Select app type: **Windows app (Win32)**
4. Click **Select**
5. Upload: `windowsdesktop-runtime-8.0.19-win-x64.intunewin`
6. Wait for upload to complete

> 📸 **Screenshot:** `dotnet-step3-intune-upload.png`

---

#### Step 16: Configure .NET 8.0 App Information

| Field | Value |
|-------|-------|
| **Name** | Microsoft .NET 8.0 Desktop Runtime (x64) |
| **Description** | Microsoft .NET 8.0 Desktop Runtime required for Dell SupportAssist and other .NET applications. Deployed as enterprise prerequisite. |
| **Publisher** | Microsoft Corporation |
| **App Version** | 8.0.19 |
| **Category** | Computer Management |
| **Show as featured app** | No |
| **Information URL** | https://dotnet.microsoft.com/download/dotnet/8.0 |
| **Developer** | Microsoft Corporation |
| **Owner** | IT Department |
| **Notes** | Required dependency for Dell SupportAssist deployment |

Click **Next** to proceed.

> 📸 **Screenshot:** `dotnet-step4-app-information.png`

---

#### Step 17: Configure .NET 8.0 Program Settings

**Install Command:**

```cmd
windowsdesktop-runtime-8.0.19-win-x64.exe /install /quiet /norestart
```

**Uninstall Command:**

```cmd
windowsdesktop-runtime-8.0.19-win-x64.exe /uninstall /quiet /norestart
```

**Additional Settings:**

| Setting | Value |
|---------|-------|
| Install behavior | System |
| Device restart behavior | Determine behavior based on return codes |

Click **Next** to proceed.

> 📸 **Screenshot:** `dotnet-step5-program-settings.png`

---

#### Step 18: Configure .NET 8.0 Requirements

| Setting | Value |
|---------|-------|
| Operating system architecture | 64-bit |
| Minimum operating system | Windows 10 1809 |
| Disk space required | 200 MB |
| Physical memory required | 512 MB |

Click **Next** to proceed.

> 📸 **Screenshot:** `dotnet-step6-requirements.png`

---

#### Step 19: Configure .NET 8.0 Detection Rules

**Registry Detection (Recommended):**

| Setting | Value |
|---------|-------|
| Rule type | Registry |
| Key path | `HKEY_LOCAL_MACHINE\SOFTWARE\dotnet\Setup\InstalledVersions\x64\sharedhost` |
| Value name | Version |
| Detection method | Version comparison |
| Operator | Greater than or equal to |
| Value | 8.0.0 |
| Associated with 32-bit app | No |

Click **Next** to proceed.

> 📸 **Screenshot:** `dotnet-step7-detection-rules.png`

---

#### Step 20: Configure .NET 8.0 Assignments

**Required Assignment:**

1. Under **Required**, click **+ Add group**
2. Select: **All Devices** or your Dell devices group
3. Configure:
   - End user notifications: **Hide all toast notifications**
   - Availability: **As soon as possible**

> **Note:** Deploy .NET 8.0 to ALL devices (not just Dell) as it's a common prerequisite for many applications.

Click **Next**, review settings, then click **Create**.

> 📸 **Screenshot:** `dotnet-step8-assignments.png`

---

#### Step 21: Verify .NET 8.0 Deployment

Monitor the deployment status:

1. Navigate to: **Apps** → **All apps** → **Microsoft .NET 8.0 Desktop Runtime (x64)**
2. Click **Device install status**
3. Wait for installations to complete on target devices

**Verification Command (on target device):**

```powershell
# Check if .NET 8.0 is installed
dotnet --list-runtimes | Select-String "Microsoft.WindowsDesktop.App 8.0"

# Alternative: Check registry
Get-ItemProperty "HKLM:\SOFTWARE\dotnet\Setup\InstalledVersions\x64\sharedhost" | Select-Object Version
```

> 📸 **Screenshot:** `dotnet-step9-deployment-status.png`

---

### Part C: Prepare SupportAssist Files for Intune

#### Step 22: Create Working Directory Structure

Open PowerShell as Administrator:

```powershell
# Create directory structure for Intune packaging
$basePath = "C:\IntuneApps\SupportAssist"
$folders = @("Source", "Output", "Scripts", "Logs")

foreach ($folder in $folders) {
    $path = Join-Path $basePath $folder
    New-Item -Path $path -ItemType Directory -Force | Out-Null
    Write-Host "Created: $path" -ForegroundColor Green
}

Write-Host "`nFolder structure created successfully!" -ForegroundColor Cyan
```

> 📸 **Screenshot:** `intune-step1-folder-structure.png`

---

#### Step 23: Copy Required Files to Source Folder

Copy the required files from your generated package:

```powershell
# Update source path to match your output location
$sourcePath = "C:\Users\[YourUsername]\OneDrive\Desktop\MY CV\SupportAssistDeploymentPackage\SupportAssist\X64"
$destPath = "C:\IntuneApps\SupportAssist\Source"

# Required files for SupportAssist deployment
$files = @(
    "SupportAssistInstaller-x64.msi",
    "SupportAssistConfiguration.mst",
    "BASE.cab",
    "CORE.cab",
    "FULL.cab",
    "HWDIAGS.cab",
    "INSIGHTS.cab",
    "RAAS.cab"
)

foreach ($file in $files) {
    Copy-Item "$sourcePath\$file" -Destination $destPath -Force
    Write-Host "Copied: $file" -ForegroundColor Green
}

# Verify files
Write-Host "`nVerifying copied files:" -ForegroundColor Cyan
Get-ChildItem $destPath | Format-Table Name, @{N='Size (KB)';E={[math]::Round($_.Length/1KB,0)}} -AutoSize
```

> 📸 **Screenshot:** `intune-step2-files-copied.png`

---

#### Step 24: Download IntuneWinAppUtil

```powershell
# Download Microsoft Win32 Content Prep Tool
$url = "https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool/raw/master/IntuneWinAppUtil.exe"
$output = "C:\IntuneApps\IntuneWinAppUtil.exe"

Invoke-WebRequest -Uri $url -OutFile $output
Write-Host "IntuneWinAppUtil downloaded to: $output" -ForegroundColor Green
```

> 📸 **Screenshot:** `intune-step3-tool-downloaded.png`

---

#### Step 25: Create .intunewin Package

Open Command Prompt as Administrator:

```cmd
cd C:\IntuneApps

IntuneWinAppUtil.exe -c "C:\IntuneApps\SupportAssist\Source" -s "SupportAssistInstaller-x64.msi" -o "C:\IntuneApps\SupportAssist\Output" -q
```

**Parameters:**
- `-c` = Source folder containing all files
- `-s` = Main setup file (MSI)
- `-o` = Output folder for .intunewin
- `-q` = Quiet mode

**Expected output:** `SupportAssistInstaller-x64.intunewin` (~400-500 MB)

> 📸 **Screenshot:** `intune-step4-package-created.png`

---

### Part D: Configure SupportAssist in Microsoft Intune

#### Step 26: Access Intune Admin Center

1. Open your web browser
2. Navigate to: **https://intune.microsoft.com**
3. Sign in with your administrator credentials

> 📸 **Screenshot:** `intune-step5-portal-login.png`

---

#### Step 27: Navigate to Apps Section

1. In the left navigation, click **Apps**
2. Click **All apps**
3. Click the **+ Add** button

> 📸 **Screenshot:** `intune-step6-apps-navigation.png`

---

#### Step 28: Select App Type

1. In the "Select app type" panel on the right
2. Under "App type", click the dropdown
3. Select **Windows app (Win32)**
4. Click **Select**

> 📸 **Screenshot:** `intune-step7-select-app-type.png`

---

#### Step 29: Upload Application Package

1. Click **Select app package file**
2. Click **Browse**
3. Navigate to: `C:\IntuneApps\SupportAssist\Output\`
4. Select: `SupportAssistInstaller-x64.intunewin`
5. Click **OK**
6. Wait for upload to complete (5-10 minutes for ~500 MB file)

> 📸 **Screenshot:** `intune-step8-upload-package.png`

> 📸 **Screenshot:** `intune-step9-upload-progress.png`

---

#### Step 30: Configure App Information

| Field | Value |
|-------|-------|
| **Name** | Dell SupportAssist for Business PCs |
| **Description** | Dell SupportAssist provides proactive and predictive support for Dell business devices. Includes hardware diagnostics, system health monitoring, remote assistance, and automated issue resolution. Managed via Dell TechDirect console. |
| **Publisher** | Dell Inc. |
| **App Version** | 4.9.2.48875 |
| **Category** | Computer Management |
| **Show as featured app** | Yes |
| **Information URL** | https://www.dell.com/support/supportassist |
| **Privacy URL** | https://www.dell.com/learn/us/en/uscorp1/policies-privacy |
| **Developer** | Dell Inc. |
| **Owner** | IT Department |
| **Notes** | Requires .NET 8.0 Runtime. Generated via Deployment Package Manager. |

Click **Next** to proceed.

> 📸 **Screenshot:** `intune-step10-app-information.png`

---

#### Step 31: Configure Program Settings (CRITICAL)

**Install Command:**

```cmd
msiexec /i "SupportAssistInstaller-x64.msi" ADDLOCAL="BASE,CORE,FULL,HWDIAGS,INSIGHTS,RAAS" TRANSFORMS="SupportAssistConfiguration.mst" /norestart /qn /l*v "%TEMP%\SupportAssist_Install.log"
```

**Command Parameters:**

| Parameter | Purpose |
|-----------|---------|
| `/i` | Install mode |
| `ADDLOCAL="..."` | Components to install |
| `TRANSFORMS="..."` | Apply configuration from MST file |
| `/norestart` | Prevent automatic restart |
| `/qn` | Quiet mode (no UI) |
| `/l*v` | Verbose logging |

**Uninstall Command:**

```cmd
wmic product where "name like '%SupportAssist%'" call uninstall /nointeractive
```

**Additional Settings:**

| Setting | Value |
|---------|-------|
| Install behavior | System |
| Device restart behavior | Determine behavior based on return codes |

Click **Next** to proceed.

> 📸 **Screenshot:** `intune-step11-program-settings.png`

---

#### Step 32: Configure Requirements

**Operating System Requirements:**

| Setting | Value |
|---------|-------|
| Operating system architecture | 64-bit |
| Minimum operating system | Windows 10 1809 |
| Disk space required | 600 MB |
| Physical memory required | 512 MB |

> 📸 **Screenshot:** `intune-step12-requirements.png`

**Custom Requirement - Dell Devices Only:**

1. Click **+ Add** under Additional requirement rules
2. Select **Script** as the requirement type
3. Upload the following script

Create `Require-DellDevice.ps1`:

```powershell
try {
    $manufacturer = (Get-WmiObject -Class Win32_ComputerSystem).Manufacturer
    
    if ($manufacturer -like "*Dell*") {
        $model = (Get-WmiObject -Class Win32_ComputerSystem).Model
        Write-Output "Dell device detected"
        Write-Output "Manufacturer: $manufacturer"
        Write-Output "Model: $model"
        exit 0
    } else {
        Write-Output "Not a Dell device"
        Write-Output "Manufacturer: $manufacturer"
        exit 1
    }
} catch {
    Write-Output "Error: $($_.Exception.Message)"
    exit 1
}
```

**Script Configuration:**

| Setting | Value |
|---------|-------|
| Run script as 32-bit process | No |
| Run using logged-on credentials | No |
| Select output data type | String |
| Operator | Equals |
| Value | `Dell device detected` |

Click **Next** to proceed.

> 📸 **Screenshot:** `intune-step13-custom-requirement.png`

---

#### Step 33: Configure Detection Rules

**Registry Detection:**

| Setting | Value |
|---------|-------|
| Rule type | Registry |
| Key path | `HKEY_LOCAL_MACHINE\SOFTWARE\Dell\SupportAssist` |
| Value name | Version |
| Detection method | String comparison |
| Operator | Greater than or equal to |
| Value | 4.0 |

Click **Next** to proceed.

> 📸 **Screenshot:** `intune-step14-detection-rules.png`

---

#### Step 34: Configure Dependencies

Add .NET 8.0 Runtime as a dependency:

1. Click **+ Add** under Dependencies
2. Search for: **Microsoft .NET 8.0 Desktop Runtime (x64)**
3. Select the app you created in Part B
4. Configure:
   - Dependency type: **Detect**
   - Auto-install: **Yes**

This ensures .NET 8.0 is installed before SupportAssist attempts to install.

Click **Next** to proceed.

> 📸 **Screenshot:** `intune-step15-dependencies.png`

---

#### Step 35: Configure Supersedence (Optional)

If replacing an older version of SupportAssist:

1. Click **+ Add**
2. Select the previous SupportAssist app
3. Choose **Uninstall** to remove the old version first

Skip this step if this is a fresh deployment.

Click **Next** to proceed.

> 📸 **Screenshot:** `intune-step16-supersedence.png`

---

#### Step 36: Configure Assignments

**Pilot Deployment:**

1. Under **Required**, click **+ Add group**
2. Select pilot group (e.g., `IT-Pilot-Dell-Devices`)
3. Configure:
   - End user notifications: **Show all toast notifications**
   - Availability: **As soon as possible**

> 📸 **Screenshot:** `intune-step17-assignments.png`

**Production Rollout (after pilot success):**

| Week | Target Group | Device Count |
|------|-------------|--------------|
| Week 1 | IT Department | 10-20 |
| Week 2 | Department A | 50 |
| Week 3 | Department B | 100 |
| Week 4+ | All Dell Devices | Remaining |

Click **Next** to proceed.

---

#### Step 37: Review and Create

1. Review all configuration settings on the summary page
2. Verify the following are correct:
   - App name: Dell SupportAssist for Business PCs
   - Install command is correct
   - Detection rule is configured
   - .NET 8.0 dependency is added
   - Assignments are correct
3. Click **Create**

> 📸 **Screenshot:** `intune-step18-review.png`

---

#### Step 38: Monitor Deployment

1. Navigate to: **Apps** → **All apps** → **Dell SupportAssist for Business PCs**
2. Click **Device install status** to monitor deployment progress
3. Review any failures and check device logs

**Status Meanings:**

| Status | Description |
|--------|-------------|
| Installed | Successfully installed |
| Pending | Waiting to install |
| Failed | Installation failed (check logs) |
| Not applicable | Device doesn't meet requirements (not Dell) |

> 📸 **Screenshot:** `intune-step19-monitor-deployment.png`

> 📸 **Screenshot:** `intune-step20-device-status.png`

---

#### Step 26: Review and Create

1. Review all configuration settings
2. Verify install command is correct
3. Confirm detection rules and dependencies
4. Click **Create**

---

## 🔐 Security & Governance Considerations

### Data Protection

| Aspect | Implementation |
|--------|----------------|
| **Activation File Storage** | Store TechDirect activation file securely; regenerate if compromised |
| **CRM Seed Value** | Document seed value securely for BIOS password recovery |
| **Telemetry Data** | SupportAssist sends hardware diagnostics to Dell; review privacy policy |
| **Network Communication** | All communication uses HTTPS/TLS encryption |

### Access Control

| Control | Implementation |
|---------|----------------|
| **Intune Access** | Limit Win32 app creation to authorized IT administrators |
| **TechDirect Access** | Restrict portal access to support staff |
| **Device Targeting** | Use Azure AD security groups for controlled deployment |

---

## ✅ Testing & Validation

### Verification Commands

```powershell
# Check if SupportAssist is installed
Get-WmiObject -Class Win32_Product | Where-Object { $_.Name -like "*SupportAssist*" }

# Check service status
Get-Service -Name "SupportAssistAgent"

# View installation log
Get-Content "$env:TEMP\SupportAssist_Install.log" -Tail 100

# Verify installation directory
Test-Path "C:\Program Files\Dell\SupportAssist"

# Check Dell manufacturer
Get-WmiObject -Class Win32_ComputerSystem | Select-Object Manufacturer, Model
```

### Validation Checklist

- [ ] Installation shows success in Intune
- [ ] SupportAssistAgent service is running
- [ ] Device appears in TechDirect console
- [ ] Hardware diagnostics can be executed
- [ ] No negative user impact reported

---

## 🚀 Deployment & Maintenance

### Ongoing Maintenance

| Task | Frequency | Responsible |
|------|-----------|-------------|
| Monitor installation status | Daily during rollout | IT Admin |
| Review TechDirect alerts | Weekly | Support Team |
| Update deployment package | Quarterly | IT Admin |
| Renew activation file | Before expiry | IT Admin |
| Review failed installations | Weekly | IT Admin |

---

## 🔧 Troubleshooting

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Installation fails with 1603 | Missing .NET 8.0 | Verify .NET 8.0 dependency is installed |
| "Not a Dell device" | Non-Dell hardware | Expected behavior - no action needed |
| Activation file invalid | Expired or corrupted file | Re-download from TechDirect |
| Service not starting | Incomplete installation | Check install log; reinstall |
| TechDirect not showing device | Registration delay | Wait 24 hours; verify network |

### Log Locations

| Log | Location |
|-----|----------|
| MSI Install Log | `%TEMP%\SupportAssist_Install.log` |
| Intune Management Extension | `%ProgramData%\Microsoft\IntuneManagementExtension\Logs\` |
| SupportAssist Logs | `%ProgramData%\Dell\SupportAssist\Logs\` |

---

## 📚 Lessons Learned & Future Improvements

### Lessons Learned

| Lesson | Recommendation |
|--------|----------------|
| CAB files must be included | Always package all CAB files with MSI |
| Activation file required | Download from TechDirect before using Package Manager |
| .NET dependency critical | Deploy .NET 8.0 as Intune dependency |
| Pilot testing valuable | Always pilot with IT devices first |
| MST format required for Intune | Select "Windows Installer Setup Transform" in Package Manager |

### Future Improvements

- [ ] Implement automated update monitoring
- [ ] Create automated compliance reporting
- [ ] Develop self-service remediation scripts
- [ ] Integration with ServiceNow for alerts

---

## 📋 Quick Reference

### Installation Command

```cmd
msiexec /i "SupportAssistInstaller-x64.msi" ADDLOCAL="BASE,CORE,FULL,HWDIAGS,INSIGHTS,RAAS" TRANSFORMS="SupportAssistConfiguration.mst" /norestart /qn /l*v "%TEMP%\SupportAssistInstall.log"
```

### Verification Commands

```powershell
# Check installation
Get-WmiObject -Class Win32_Product | Where-Object { $_.Name -like "*SupportAssist*" }

# Check service
Get-Service SupportAssistAgent

# Check logs
Get-Content "$env:TEMP\SupportAssistInstall.log" -Tail 50
```

### Deployment Checklist

- [ ] Downloaded activation file from TechDirect
- [ ] Ran Dell Deployment Package Manager
- [ ] Validated activation file successfully
- [ ] Configured Central Resource Manager (if needed)
- [ ] Selected MST output format
- [ ] Generated deployment package
- [ ] Verified all files in output folder
- [ ] Deployed .NET 8.0 Runtime as prerequisite
- [ ] Created .intunewin package
- [ ] Uploaded to Intune
- [ ] Configured install command
- [ ] Set up detection rules
- [ ] Added .NET 8.0 dependency
- [ ] Assigned to pilot group
- [ ] Monitored pilot deployment
- [ ] Verified installation on test devices
- [ ] Expanded to production groups

---

## 📸 Screenshot Reference

### Part A: Dell Deployment Package Manager Screenshots

| Step | Image | Description |
|------|-------|-------------|
| 2 | step1-welcome.png | Deployment Package Manager Welcome screen |
| 3 | step2-activation-invalid.png | Activation validation error |
| 4 | step3-activation-success.png | Activation successful |
| 5 | step4-software-addons.png | Software add-ons selection |
| 6 | step5-crm-config.png | Central Resource Manager configuration |
| 7 | step6-proxy.png | Proxy configuration |
| 8 | step7-output-format.png | Output format selection |
| 9 | step8-output-location.png | Output location selection |
| 10 | step9-generation-progress.png | Package generation in progress |
| 10 | step10-generation-complete.png | Package generation complete |
| 11 | step11-summary.png | Summary success |
| 12 | step12-package-contents.png | Generated package contents |

### Part B: .NET 8.0 Deployment Screenshots

| Step | Image | Description |
|------|-------|-------------|
| 13 | dotnet-step1-source-files.png | .NET source files prepared |
| 14 | dotnet-step2-package-created.png | .NET .intunewin package created |
| 15 | dotnet-step3-intune-upload.png | Upload .NET to Intune |
| 16 | dotnet-step4-app-information.png | .NET app information |
| 17 | dotnet-step5-program-settings.png | .NET program settings |
| 18 | dotnet-step6-requirements.png | .NET requirements |
| 19 | dotnet-step7-detection-rules.png | .NET detection rules |
| 20 | dotnet-step8-assignments.png | .NET assignments |
| 21 | dotnet-step9-deployment-status.png | .NET deployment status |

### Part C & D: SupportAssist Intune Deployment Screenshots

| Step | Image | Description |
|------|-------|-------------|
| 22 | intune-step1-folder-structure.png | Working directory created |
| 23 | intune-step2-files-copied.png | Source files copied |
| 24 | intune-step3-tool-downloaded.png | IntuneWinAppUtil downloaded |
| 25 | intune-step4-package-created.png | .intunewin package created |
| 26 | intune-step5-portal-login.png | Intune Admin Center login |
| 27 | intune-step6-apps-navigation.png | Apps navigation |
| 28 | intune-step7-select-app-type.png | Select Win32 app type |
| 29 | intune-step8-upload-package.png | Upload package file |
| 29 | intune-step9-upload-progress.png | Upload progress |
| 30 | intune-step10-app-information.png | App information configured |
| 31 | intune-step11-program-settings.png | Program settings with install command |
| 32 | intune-step12-requirements.png | OS requirements |
| 32 | intune-step13-custom-requirement.png | Dell device requirement script |
| 33 | intune-step14-detection-rules.png | Detection rules configured |
| 34 | intune-step15-dependencies.png | .NET dependency added |
| 35 | intune-step16-supersedence.png | Supersedence (optional) |
| 36 | intune-step17-assignments.png | Group assignments |
| 37 | intune-step18-review.png | Review and create |
| 38 | intune-step19-monitor-deployment.png | Monitor deployment |
| 38 | intune-step20-device-status.png | Device install status |

---

<p align="center">
  <strong>Document Version:</strong> 2.0 | <strong>Last Updated:</strong> December 2025 | <strong>Author:</strong> IT Department
</p>