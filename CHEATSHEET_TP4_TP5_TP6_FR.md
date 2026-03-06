# 🔥 CHEATSHEET PARE-FEU - TP4, TP5, TP6 (FRANÇAIS)

## ⚡ RACCOURCIS RAPIDES - COMMANDS ESSENTIELLES

---

## TP4 - PARE-FEU LOCAL (WINDOWS FIREWALL)

### Installation IIS Web Server
```
Server Manager → Manage → Add Roles and Features
→ Next → Cocher "Web Server (IIS)"
→ Role Services: Cocher
   ✓ Default Document (pour servir index.html)
   ✓ HTTP Logging (logs d'accès HTTP)
   ✓ Static Content (fichiers statiques)
   ✓ ASP.NET / .NET Extensibility (si sites ASP.NET)
→ Install
```
**Pourquoi:** Port 80 (HTTP) needed pour test accès web distant

---

### Installation IIS FTP Server
```
Server Manager → Manage → Add Roles and Features
→ Web Server (IIS) → Role Services
→ FTP Server → Cocher:
   ✓ FTP Service (service FTP port 21)
   ✓ FTP Extensibility (optional)
→ Install
```
**Pourquoi:** FTP = protocol non-sécurisé, test avant/après blocage

---

### Activer Remote Desktop (RDP)
```
Start Menu (right-click) → System
→ Remote settings (gauche)
→ Remote tab:
   ✓ Allow remote connections to this computer
→ OK
```
**Pourquoi:** Port 3389 = Remote Desktop Protocol, test connexion distance

---

### TEST AVANT BLOCAGE - Connexion RDP
```
Admin Machine-1:
Start → Type "Remote Desktop Connection" → Entrée

Computer: 10.10.1.16 (IP Web Server)
User: Administrator
Password: admin@123

Click Connect
```
**Résultat:** Connexion réussie ✅ (aucune règle firewall bloque)

---

### BLOQUER REMOTE DESKTOP

#### Étape 1: Accéder à Windows Firewall
```
Web Server:
Control Panel → System and Security → Windows Firewall
→ Click "Use recommended settings"
```
**Pourquoi:** Active blocage par défaut (DENY incoming)

---

#### Étape 2: Ouvrir Advanced Settings
```
Windows Firewall → Left panel:
Click "Advanced Settings"
```
**Résultat:** "Windows Firewall with Advanced Security" s'ouvre

---

#### Étape 3: BLOQUER RULE 1 - Remote Desktop-Shadow (TCP-In)
```
Inbound Rules (left panel)
→ Search/Find "Remote Desktop-Shadow (TCP-In)"
→ Double-click
→ Select radio button: "Block the connection"
→ OK
```
**Pourquoi:** Shadow = monitoring remote sessions, bloquer pour sécurité

---

#### Étape 4: BLOQUER RULE 2 - Remote Desktop-User Mode (TCP-In)
```
Inbound Rules
→ Find "Remote Desktop-User Mode (TCP-In)"
→ Double-click
→ "Block the connection"
→ OK
```
**Pourquoi:** Port 3389/TCP = main RDP protocol

---

#### Étape 5: BLOQUER RULE 3 - Remote Desktop-User Mode (UDP-In)
```
Inbound Rules
→ Find "Remote Desktop-User Mode (UDP-In)"
→ Double-click
→ "Block the connection"
→ OK
```
**Pourquoi:** 3389/UDP = alternative RDP (faster but less reliable)

---

### TEST APRÈS BLOCAGE RDP
```
Admin Machine-1:
Remote Desktop Connection
→ Computer: 10.10.1.16
→ Connect

❌ ERREUR: "Remote Desktop can't connect to the remote computer"
```
**Résultat:** Connexion échouée (firewall bloque port 3389)

---

### TESTER FTP AVANT BLOCAGE
```
Admin Machine-1 Command Prompt:
ftp 10.10.1.16
Username: Administrator
Password: admin@123
```
**Résultat:**
```
Connected to 10.10.1.16.
220 Microsoft FTP Service
User logged in. ✅
```

---

### BLOQUER FTP - RULE 1: FTP Server (FTP Traffic-In)
```
Web Server:
Windows Firewall with Advanced Security
→ Inbound Rules
→ Find "FTP Server (FTP Traffic-In)"
→ Double-click
→ "Block the connection"
→ OK
```
**Pourquoi:** Port 21/TCP = standard FTP (credentials en clair = risque)

---

### BLOQUER FTP - RULE 2: FTP Server Passive
```
Inbound Rules
→ Find "FTP Server Passive (FTP Passive Traffic-In)"
→ Double-click
→ "Block the connection"
→ OK
```
**Pourquoi:** Mode passif = firewall traversal (ports éphémères)

---

### BLOQUER FTP - RULE 3: FTP Server Secure
```
Inbound Rules
→ Find "FTP Server Secure (FTP SSL Traffic-In)"
→ Double-click
→ "Block the connection"
→ OK
```
**Pourquoi:** FTPS = FTP over SSL, aussi bloquer pour cohérence

---

### TEST APRÈS BLOCAGE FTP
```
Admin Machine-1 Command Prompt:
ftp 10.10.1.16

❌ RÉSULTAT: "Connection timed out"
```
**Résultat:** FTP bloqué par firewall

---

### RÉACTIVER FTP (Si needed)
```
Web Server:
Windows Firewall → Advanced Settings
→ Inbound Rules
→ "FTP Server (FTP Traffic-In)"
→ Properties
→ Select "Allow the connection"
→ OK
```
**Pourquoi:** Pour démontrer qu'on peut allow après deny

---

---

## TP5 - PARE-FEU RÉSEAU (pfSENSE) - BLOCAGE DOMAINES

### ACCÉDER À pfSENSE WEB INTERFACE
```
Any machine on LAN:
Browser → https://10.10.1.1
→ Click "Advanced"
→ "Proceed to 10.10.1.1 (unsafe)"
→ Sign In page:
   Username: admin
   Password: pfsense
→ SIGN IN
```
**Pourquoi:** IP 10.10.1.1 = default gateway (pfSense)

---

### TROUVER IP DE DOMAINE (Machine-1)
```
Command Prompt:
ping rediff.com

Résultat:
PING rediff.com [84.53.185.208]
Reply from 84.53.185.208: bytes=32 time=19ms
```
**Pourquoi:** Firewall bloque par IP, pas par domain name

---

### CRÉER ALIAS (Groupe de domaines/IPs)

#### Étape 1: Naviguer à Aliases
```
pfSense Dashboard:
Firewall (top menu) → Aliases
→ IP tab
→ Click "+ Add"
```
**Pourquoi:** Alias = groupe pour 1 seule règle au lieu de 100

---

#### Étape 2: Configurer Alias Propriétés
```
Name: BlockedWebsites
  (LETTERS & NUMBERS ONLY, NO SPACES)
Description: Restrict the access of unwanted websites
Type: Host(s)
```

---

#### Étape 3: Ajouter Premier Domaine
```
IP or FQDN: www.rediff.com
Description: Site Url
```

---

#### Étape 4: Ajouter Premier IP
```
Click "Add Host" button

IP or FQDN: 84.53.185.208
Description: Site IP address
```

---

#### Étape 5: Sauvegarder Alias
```
Click "Save" button
→ Click "Apply Changes" button

Message: "The changes have been applied successfully"
```

---

### CRÉER RÈGLE PARE-FEU (Bloquer Alias)

#### Étape 1: Naviguer à Rules
```
pfSense Dashboard:
Firewall → Rules → LAN tab
Click "Add" (up arrow) button
```
**Pourquoi:** Rules appliquent les blocs

---

#### Étape 2: Configurer Action & Interface
```
Action: Block (deny traffic)
Disabled: ☐ (leave unchecked)
Interface: LAN (internal network)
Address Family: IPv4
Protocol: TCP/UDP (web protocols)
```

---

#### Étape 3: Configurer Source
```
Source section:
Source: any (any source = all computers)
  (dropdown menu)
```

---

#### Étape 4: Configurer Destination
```
Destination section:
Destination: Single host or alias (dropdown)
→ Type: BlockedWebsites (our alias)
Destination Port Range: any (all ports)
```

---

#### Étape 5: Ajouter Description
```
Extra Options section:
Description: Restrict access to unwanted Websites
```

---

#### Étape 6: Sauvegarder & Appliquer
```
Click "Save"
→ Page Firewall/Rules appears
→ Click "Apply Changes"

Status: "The firewall rules are now reloading"
```

---

### TEST BLOCAGE - BEFORE
```
Web Server Browser:
Type: http://www.rediff.com
→ Press Enter

✅ RÉSULTAT: Website loads
```

---

### TEST BLOCAGE - AFTER
```
Web Server Browser:
Type: http://www.rediff.com
→ Press Enter

❌ RÉSULTAT: "This site can't be reached"
ERR_CONNECTION_REFUSED
```

---

---

## TP6 - PARE-FEU RÉSEAU (pfSENSE) - BLOCAGE PORTS

### BLOCAGE PORT HTTP (Port 80)

#### Étape 1: Naviguer à Rules
```
pfSense Dashboard:
Firewall → Rules → LAN tab
Click "Add" (up arrow)
```

---

#### Étape 2: Configurer Action
```
Action: Reject
  (Reject = "connection refused" message FAST)
  (Block = silent drop SLOW)
Interface: LAN
Address Family: IPv4
Protocol: TCP/UDP
```
**Pourquoi Reject:** User gets instant feedback (better UX)

---

#### Étape 3: Configurer Source
```
Source section:
Source: any (dropdown)
```

---

#### Étape 4: Configurer Destination & Port
```
Destination section:
Destination: any (dropdown)
Destination Port Range: HTTP (80) (dropdown)
```

---

#### Étape 5: Ajouter Description
```
Extra Options:
Description: Rule for Rejecting any website using http (80) port
```

---

#### Étape 6: Sauvegarder
```
Scroll down
Click "Save"
→ Firewall/Rules/LAN page
→ Click "Apply Changes"
```

---

### TEST BLOCAGE HTTP

#### BEFORE
```
Web Server Browser:
Type: http://certifiedhacker.com/
→ Press Enter

✅ RÉSULTAT: Website loads
```

---

#### AFTER
```
Web Server Browser:
Type: http://certifiedhacker.com/
→ Press Enter

❌ RÉSULTAT: "This site can't be reached"
ERR_CONNECTION_REFUSED
```

---

---

## TP6 AVANCÉ - RÈGLES BASÉES SUR TEMPS (SCHEDULES)

### CRÉER SCHEDULE (Horaire de travail)

#### Étape 1: Naviguer à Schedules
```
pfSense Dashboard:
Firewall → Schedules
Click "+ Add"
```

---

#### Étape 2: Remplir Schedule Information
```
Schedule Name: WorkingHours
  (LETTERS & NUMBERS ONLY, NO SPACES)
Description: Normal Working Hours
```

---

#### Étape 3: Sélectionner Mois & Jours
```
Month: June_21 (example)

Date Calendar:
Click individual date (e.g., 30)
OR
Click day header (Mon, Tue, etc.) = recurring weekly
```

---

#### Étape 4: Définir Heure de Début & Fin
```
Time section:
Start Hrs: 1
Start Mins: 15
Stop Hrs: 23
Stop Mins: 59

⚠️ IMPORTANT: Cannot cross midnight
Full day = 0:00 to 23:59
```
**Pourquoi:** Firewall rules opèrent dans cadre 24h

---

#### Étape 5: Ajouter Description de Plage
```
Time range description: Work day
```

---

#### Étape 6: Ajouter & Sauvegarder
```
Click "+ Add Time"
→ Appears in "Configured Ranges"
→ Click "Save"

Page redirects to Firewall/Schedules
→ Schedule appears in list
→ ⭕ Symbol = currently active
```

---

### CRÉER RÈGLE AVEC SCHEDULE

#### Étape 1: Créer Règle Normale
```
Firewall → Rules → LAN
Click "Add"

Action: Reject
Interface: LAN
Protocol: TCP/UDP
Source: any
Destination: any
Port: HTTP (80)
Description: Rule for Rejecting HTTP during Working Hours
```

---

#### Étape 2: Accéder Advanced Options
```
Extra Options section:
Click "Display Advanced"
→ Advanced Options appear below
```

---

#### Étape 3: Sélectionner Schedule
```
Advanced Options section:
Schedule: WorkingHours (dropdown)
  (Other options: leave default)
```

---

#### Étape 4: Sauvegarder
```
Scroll down
Click "Save"
→ Click "Apply Changes"
```

---

### TEST SCHEDULE

```
Rule Active: 1:15 AM - 11:59 PM
→ HTTP BLOQUÉ ❌

Outside: 12:00 AM - 1:14 AM
→ HTTP ALLOWED ✅
```

---

---

## 🔴 SUPPRIMER RÈGLES (Cleanup)

### SUPPRIMER RÈGLE pfSENSE
```
Firewall → Rules → LAN
→ Check checkbox next to rule
→ Click "Delete" button
→ Click OK in confirmation
→ Click "Apply Changes"
```

---

### REDÉMARRER pfSENSE
```
Diagnostics (top menu) → Reboot
→ Click "Reboot" button
→ Click OK

Status: "Rebooting... Page will reload in 84 seconds"
→ Wait...
→ Login page appears
```

---

---

## 📋 TABLEAU RÉCAPITULATIF - ACTIONS

| Action | Comportement | Vitesse | Cas d'usage |
|--------|-------------|---------|-----------|
| **Block** | Drop packets silently | LENT (timeout) | Stealth mode |
| **Reject** | "Connection refused" | RAPIDE | Better UX |
| **Allow** | Permit traffic | N/A | Whitelist |

---

## 🎯 QUICK MEMORY TRICKS

### Windows Firewall (TP4)
```
RDP = Port 3389 (3 rules to block)
FTP = Port 21 (3 variants to block)
Remember: SHADOW, USER MODE TCP, USER MODE UDP
```

### pfSense Aliases (TP5)
```
Alias = Group of IPs/Domains
1 Alias = Many items
1 Rule = Many blocked items
```

### pfSense Schedules (TP6)
```
Schedule = Time window
Rule + Schedule = Time-based blocking
Cannot cross midnight (split if needed)
```

---

## ⚠️ COMMON MISTAKES

❌ Block rule before Allow rule → won't work
✅ Allow specific rule FIRST, then block general

❌ Schedule name with SPACES → error
✅ Schedule name = WorkingHours (no spaces)

❌ FTP open but only block port 21
✅ Block FTP, FTP Passive, FTP Secure (all 3)

❌ HTTP rule blocks all web
✅ Only port 80; port 443 (HTTPS) still works

---

## 🔧 TROUBLESHOOTING ULTRA-RAPIDE

**Remote Desktop not connecting?**
```
→ Check 3 RDP rules are Blocked
→ Or set to Allow if you want access
```

**FTP timing out?**
```
→ Check FTP, FTP Passive, FTP Secure = Blocked
```

**Website still loading after pfSense rule?**
```
→ Clear browser cache (Ctrl+Shift+Delete)
→ Restart pfSense (Diagnostics → Reboot)
→ Try again
```

**Schedule rule not working?**
```
→ Check schedule time matches current time
→ Check rule has schedule selected
→ Check "Apply Changes" was clicked
```

---

