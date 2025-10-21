# 🔄 IDMMac Update System - Visual Workflow

## 📱 User Experience Flow

```
┌─────────────────────────────────────────────────────────────┐
│  User's Mac Running IDMMac v0.1.0                           │
└─────────────────────────────────────────────────────────────┘
                           │
                           │ (Every 24 hours or manual check)
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  App Checks: https://ahmedsam.com/idmmac/appcast.xml        │
└─────────────────────────────────────────────────────────────┘
                           │
                           │
                ┌──────────┴──────────┐
                │                     │
                ▼                     ▼
        ┌─────────────┐      ┌─────────────┐
        │  No Update  │      │ Update v0.2 │
        │  Available  │      │  Available  │
        └─────────────┘      └─────────────┘
                │                     │
                ▼                     ▼
        "You're up       ┌──────────────────────────────┐
         to date!"       │  📢 Update Dialog Appears    │
                         │                              │
                         │  "Version 0.2.0 available"   │
                         │  • Release notes (HTML)      │
                         │  • [Install Update]          │
                         │  • [Remind Me Later]         │
                         │  • [Skip This Version]       │
                         └──────────────────────────────┘
                                      │
                                      │ User clicks "Install"
                                      ▼
                         ┌──────────────────────────────┐
                         │  🔽 Download Update          │
                         │  Progress bar shows download │
                         └──────────────────────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────────┐
                         │  🔐 Verify EdDSA Signature   │
                         │  Ensures update is authentic │
                         └──────────────────────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────────┐
                         │  🔄 Install & Relaunch       │
                         │  App quits, updates, restarts│
                         └──────────────────────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────────┐
                         │  ✅ Running v0.2.0           │
                         │  Update complete!            │
                         └──────────────────────────────┘
```

---

## 🚀 Developer Release Flow

```
┌─────────────────────────────────────────────────────────────┐
│  1. Develop New Features                                    │
│     • Add dark mode, fix bugs, new features, etc.           │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  2. Update Version Number                                   │
│     • 0.1.0 → 0.2.0 in Xcode                               │
│     • Update release notes                                  │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  3. Build Release                                           │
│     • Xcode: Product → Archive                             │
│     OR                                                      │
│     • ./Tools/release.sh 0.2.0                             │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  4. Sign Update                                             │
│     • ./Tools/sign_update.sh YourApp.app 0.2.0             │
│     • Generates EdDSA signature                            │
│     • Creates IDMMacApp-0.2.0.zip                          │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  5. Update appcast.xml                                      │
│     • Add new <item> at top                                │
│     • Include version, date, release notes                  │
│     • Add signature from step 4                            │
│     • Set download URL                                      │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  6. Upload to Server                                        │
│     • scp IDMMacApp-0.2.0.zip → server/downloads/          │
│     • scp appcast.xml → server/                            │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  7. Test on Clean Mac                                       │
│     • Install old version                                   │
│     • Check for updates                                     │
│     • Verify update works                                   │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  8. Release Complete! 🎉                                    │
│     • Users get notified automatically                      │
│     • One-click updates for everyone                        │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔐 Security Flow

```
┌─────────────────┐
│  Your Mac       │
│  (Developer)    │
└────────┬────────┘
         │
         │ 1. Generate EdDSA key pair (one time)
         ▼
┌─────────────────────────────────────────────────┐
│  Private Key                  Public Key        │
│  (Keychain)                   (Info.plist)      │
│  🔒 NEVER SHARE               ✅ Embed in app   │
└────────┬────────────────────────┬───────────────┘
         │                        │
         │ 2. Sign update         │ 3. Embedded in
         │    with private key    │    distributed app
         ▼                        ▼
┌─────────────────┐      ┌────────────────────┐
│  IDMMac-0.2.0   │      │  User's Mac        │
│  + Signature    │      │  Running IDMMac    │
└────────┬────────┘      └─────────┬──────────┘
         │                         │
         │ 4. Upload to server     │ 5. Download update
         ▼                         ▼
┌──────────────────────────────────────────────┐
│  ahmedsam.com/idmmac/                        │
│  • IDMMacApp-0.2.0.zip                       │
│  • appcast.xml (with signature)              │
└──────────────────────┬───────────────────────┘
                       │
                       │ 6. Verify signature
                       ▼
              ┌─────────────────┐
              │  Public Key     │
              │  verifies that  │
              │  Private Key    │
              │  signed it      │
              └────────┬────────┘
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
    ┌──────────┐            ┌──────────────┐
    │ ✅ Valid │            │ ❌ Invalid   │
    │ Install  │            │ Reject!      │
    └──────────┘            └──────────────┘
```

**Why This Matters:**
- 🛡️ **Prevents malicious updates** - Only you can sign updates
- 🔒 **Man-in-the-middle protection** - Signature can't be forged
- ✅ **Users trust updates** - Verified by cryptographic proof

---

## 🌐 Server Architecture

```
┌────────────────────────────────────────────────────────┐
│  ahmedsam.com (Your Web Server)                        │
│                                                         │
│  /idmmac/                                              │
│  ├── appcast.xml          (Update feed - always        │
│  │                         check here first)           │
│  │                                                      │
│  └── downloads/                                        │
│      ├── IDMMacApp-0.1.0.zip    (411 KB)              │
│      ├── IDMMacApp-0.2.0.zip    (412 KB)              │
│      ├── IDMMacApp-0.3.0.zip    (415 KB)              │
│      └── ...                                           │
└────────────────────────────────────────────────────────┘
         ▲                                ▲
         │                                │
         │                                │
┌────────┴────────┐              ┌───────┴────────┐
│ Check for       │              │ Download       │
│ updates         │              │ update ZIP     │
│ (every 24h)     │              │ (when needed)  │
└─────────────────┘              └────────────────┘
         ▲                                ▲
         │                                │
    ┌────┴──────────────────────────────┴────┐
    │  User's Mac Running IDMMac              │
    │  • Checks appcast.xml periodically      │
    │  • Downloads only if update available   │
    │  • Verifies signature before installing │
    └─────────────────────────────────────────┘
```

---

## 📊 Version Comparison Logic

```
Current Version: 0.1.0 (Build 1)
                 ↓
                 Compare
                 ↓
┌────────────────────────────────────────────┐
│  Versions in appcast.xml                   │
├────────────────────────────────────────────┤
│  • 0.3.0 (Build 3)  → ✅ Show this update │
│  • 0.2.0 (Build 2)  → ⏭️  Skip (0.3.0 is newer) │
│  • 0.1.0 (Build 1)  → ❌ Same version     │
│  • 0.0.9 (Build 0)  → ❌ Older            │
└────────────────────────────────────────────┘
                 ↓
           Show latest that's
           higher than current
```

**Version Format**: `MAJOR.MINOR.PATCH` (Semantic Versioning)
- **MAJOR**: Breaking changes (1.0.0, 2.0.0)
- **MINOR**: New features (0.1.0, 0.2.0)
- **PATCH**: Bug fixes (0.1.1, 0.1.2)

---

## 🧪 Testing Environments

### Local Development Testing

```
┌─────────────────────────────────────┐
│  Your Mac                           │
│                                     │
│  http://localhost:8000 ←─ Python   │
│  │                        Server    │
│  ├── test-appcast-UPDATE-          │
│  │   AVAILABLE.xml                 │
│  ├── test-appcast-UP-TO-DATE.xml   │
│  └── test-update.zip                │
└─────────────────────────────────────┘
         ▲
         │ Info.plist SUFeedURL:
         │ http://localhost:8000/test-appcast-...xml
         │
    ┌────┴──────┐
    │  Test App │
    │  (Debug)  │
    └───────────┘
```

### Production Testing

```
┌─────────────────────────────────────┐
│  ahmedsam.com                       │
│                                     │
│  https://ahmedsam.com/idmmac/       │
│  ├── appcast.xml                    │
│  └── downloads/                     │
│      └── IDMMacApp-0.2.0.zip        │
└─────────────────────────────────────┘
         ▲
         │ Info.plist SUFeedURL:
         │ https://ahmedsam.com/idmmac/appcast.xml
         │
    ┌────┴──────────┐
    │  Release App  │
    │  (on test Mac)│
    └───────────────┘
```

---

## 🎯 Quick Command Reference

| Task | Command |
|------|---------|
| **Build & Sign Release** | `./Tools/release.sh 0.2.0` |
| **Sign Update Only** | `./Tools/sign_update.sh App.app 0.2.0` |
| **Start Test Server** | `cd Tools && python3 -m http.server 8000` |
| **Check Current Version** | `defaults read /Applications/IDMMacApp.app/Contents/Info CFBundleShortVersionString` |
| **Validate Appcast XML** | `xmllint --noout Tools/appcast.xml` |
| **Test Production URL** | `curl -I https://ahmedsam.com/idmmac/appcast.xml` |
| **Upload to Server** | `scp file.zip user@ahmedsam.com:/path/` |

---

**See also:**
- 📖 **DEPLOYMENT_GUIDE.md** - Detailed step-by-step instructions
- ⚡ **QUICK_RELEASE.md** - Quick reference for releases
- 📊 **UPDATE_SYSTEM_SUMMARY.md** - Complete configuration overview

