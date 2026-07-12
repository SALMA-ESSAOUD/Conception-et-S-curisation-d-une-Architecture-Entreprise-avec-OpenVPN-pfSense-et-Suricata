# 🔐 Conception et Sécurisation d'une Architecture Entreprise
### avec OpenVPN, pfSense et Suricata

[![pfSense](https://img.shields.io/badge/pfSense-CE%202.8.1-blue)]()
[![Suricata](https://img.shields.io/badge/Suricata-7.0.8-orange)]()
[![OpenVPN](https://img.shields.io/badge/OpenVPN-2.6-green)]()
[![Ubuntu](https://img.shields.io/badge/Ubuntu%20Server-24.04%20LTS-purple)]()

> Projet réalisé dans le cadre du module **Sécurité des Systèmes et Réseaux**  
> Filière **SIT2** — Faculté des Sciences et Techniques de Marrakech (FSTM), Université Cadi Ayyad  
> Année universitaire 2025/2026

**Réalisé par :** Essaoud Salma  
**Encadrant :** M. Charafeddine Ait Zaouiat

---

## 📋 Description du projet

Ce projet consiste à concevoir, déployer et sécuriser une infrastructure réseau d'entreprise simulée sous **VirtualBox**, intégrant :

- 🔒 Un accès distant sécurisé pour les employés via **VPN (OpenVPN)**
- 🖥️ Un serveur Linux interne **durci** (hardening)
- 🛡️ Un système de **détection et de prévention d'intrusions (IDS/IPS)** avec Suricata
- 🧱 Un **firewall** (pfSense) appliquant le principe du moindre privilège
- ⚔️ Des tests d'intrusion réels depuis une machine Kali Linux pour valider l'efficacité des protections

## 🏗️ Architecture

L'infrastructure repose sur **4 machines virtuelles** réparties sur deux réseaux distincts :

| Machine | OS | Rôle | Réseau |
|---|---|---|---|
| pfSense | pfSense CE 2.8.1 | Firewall + OpenVPN + IDS/IPS | WAN + LAN |
| Ubuntu Server | Ubuntu Server 24.04 LTS | Serveur interne sécurisé | LAN (192.168.10.0/24) |
| Windows Client | Windows 10/11 | Client OpenVPN distant | WAN (192.168.11.0/24) |
| Kali Linux | Kali Linux | Machine attaquante | WAN (192.168.11.0/24) |

### Plan d'adressage

| Réseau | Plage | Description |
|---|---|---|
| WAN | 192.168.11.0/24 | Simule Internet |
| LAN | 192.168.10.0/24 | Réseau interne de l'entreprise |
| VPN | 10.8.0.0/24 | Tunnel OpenVPN Client-to-Site |

## 📚 Sommaire du projet

1. **Partie 1 — Mise en place de l'architecture**  
   Configuration VirtualBox, adressage IP, vérification de connectivité entre les VMs.

2. **Partie 2 — Déploiement OpenVPN**  
   PKI (CA + certificats), serveur VPN Remote Access SSL/TLS, export de la configuration client, tests de connexion.

3. **Partie 3 — Durcissement du serveur Ubuntu**  
   Mise à jour système, suppression des services inutiles, désactivation IPv6, `sysctl` réseau, UFW, sécurisation GRUB, PAM (expiration mots de passe, complexité, `pam_faillock`), SSH (clés ED25519, port non-standard, `PermitRootLogin no`).

4. **Partie 4 — Services sur le serveur Ubuntu**  
   Apache2, OpenSSH, vsftpd (FTP/SFTP), tous accessibles uniquement via le tunnel VPN.

5. **Partie 5 — Déploiement IDS/IPS Suricata**  
   Installation sur pfSense, mode **Inline IPS**, règles de détection personnalisées (scan Nmap, brute force SSH, ICMP flood...).

6. **Partie 6 — Simulation d'attaques depuis Kali Linux**  
   - Scan réseau SYN (`nmap -sS`)
   - Détection de services (`nmap -sV`)
   - Brute force SSH (`Hydra`)
   - Ping flood (ICMP Flood)  
   
   Chaque attaque est analysée via **Wireshark** et confrontée aux **alertes Suricata**.

7. **Partie 7 — Firewalling pfSense**  
   Règles WAN et OpenVPN appliquant le principe du moindre privilège (blocage ICMP, SSH via VPN uniquement, blocage de l'IP attaquante).

## 🛡️ Résultats des tests de sécurité

| Test | Outil | Détection (Suricata) | Blocage |
|---|---|---|---|
| Scan SYN | `nmap -sS` | SID 9000001 | UFW |
| Scan de services | `nmap -sV` | SID 9000005 | UFW |
| Brute force SSH | Hydra | SID 9000003 | PAM faillock |
| Ping flood | `ping -f` | SID 9000004 | pfSense (ICMP block) |
| Accès SSH direct | `ssh` port 22 | — | Règle firewall WAN |
| Scan multi-ports | `nmap` | SID 9000007 | UFW |

✅ **Toutes les attaques simulées ont été détectées et bloquées.**

## 🧰 Technologies utilisées

- **pfSense CE 2.8.1** — Firewall / Routeur / Serveur VPN
- **OpenVPN** — VPN Client-to-Site (SSL/TLS)
- **Suricata 7.0.8** — IDS/IPS en mode Inline
- **Ubuntu Server 24.04 LTS** — Serveur applicatif durci
- **Apache2, OpenSSH, vsftpd** — Services réseau
- **UFW, PAM (pwquality, faillock)** — Sécurité système
- **Kali Linux** — Tests d'intrusion (Nmap, Hydra)
- **Wireshark** — Analyse de trafic réseau
- **VirtualBox** — Virtualisation de l'infrastructure

## 📄 Documentation

Le rapport complet du projet (PDF) détaille chaque étape avec captures d'écran, commandes et explications :  
📎 [`Conception-et-S-curisation-d-une-Architecture-Entreprise-avec-OpenVPN-pfSense-et-Suricata.pdf`](./Conception-et-S-curisation-d-une-Architecture-Entreprise-avec-OpenVPN-pfSense-et-Suricata.pdf)

## 🎯 Conclusion

Ce projet met en œuvre une **défense en profondeur** combinant plusieurs couches de sécurité complémentaires (pfSense + Suricata + UFW + PAM + SSH par clés), illustrant une architecture réaliste et adaptée aux besoins d'une PME souhaitant :
- protéger ses ressources internes,
- permettre un accès distant sécurisé à ses employés,
- détecter et bloquer les attaques externes en temps réel.

---

*Projet académique réalisé dans un environnement de test isolé (VirtualBox). Les adresses IP et configurations sont propres à l'environnement de laboratoire.*
