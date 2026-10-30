# Yesu Leagbe Ent — Plateforme de gestion de stock et de ventes

> **Le code source de ce projet est privé** (application en production pour une entreprise cliente).
> Ce dépôt est une **vitrine** : il présente le projet, son architecture et les choix techniques, sans code.

**Auteur :** Aymar Dzossou · **Client :** Yesu Leagbe Ent (motos et pièces détachées, Ghana)

## Le projet

Application web de gestion de **stock et de ventes multi-boutiques** pour une entreprise qui vend des
motos et des pièces détachées. Elle remplace le suivi manuel par un système unique, utilisable au
comptoir sur téléphone (même avec une connexion instable) et au bureau pour piloter l'activité.

- **3 rôles** avec des droits distincts : Administrateur, CEO (Directeur) et Manager de boutique.
- **Isolation stricte des données par boutique** : un Manager ne voit jamais les données d'une autre boutique.
- **Interface bilingue** français / anglais (au choix de chaque compte), thème clair et sombre.
- **Installable** sur PC et téléphone (PWA), avec **ventes hors-ligne** rejouées sans doublon au retour du réseau.

## Fonctionnalités

| Domaine | Ce que fait l'application |
|---|---|
| **Ventes** | Vente de motos (par numéro de châssis) et de pièces, reçus PDF (A4 et ticket), annulation soumise à validation |
| **Stock** | Stock de pièces par boutique, alertes de seuil, historique de tous les mouvements |
| **Réceptions** | Réception de marchandises depuis un catalogue de pièces ; référence générée automatiquement ; prix d'achat complété et validé par l'Admin ou le CEO |
| **Catalogue** | Liste des pièces vendues, recherche, ajout et modification par rôle |
| **Finance** | Dépenses par catégories, crédit fournisseur et remboursements, marges et bénéfice net |
| **Pilotage** | Tableaux de bord Admin et CEO, alertes prioritaires, rapports quotidiens PDF, exports CSV |
| **Documents** | Lecture automatique des reçus fournisseurs (OCR), pièces justificatives, documents de châssis |
| **Intégrations** | API publique, webhooks signés, serveur MCP pour agents IA |
| **Traçabilité** | Journal d'audit de toutes les actions sensibles |

## Architecture

```
Navigateur (PWA)  ──Server Actions / Route Handlers──▶  Vercel (Next.js)
  file hors-ligne (IndexedDB)                              │  middleware : CSP + en-têtes
  service worker                                           │  session validée en base
                                                           ▼
                                  actions ─▶ validation ─▶ services ─▶ Prisma ─▶ PostgreSQL
                                                                │
                                                  stockage de fichiers · OCR de documents
```

Principes retenus : actions serveur comme **frontière de sécurité**, logique métier dans une couche de
services testable, **argent en entiers** (jamais de décimaux), **une transaction par opération**,
isolation par boutique appliquée dans la couche d'accès aux données.

## Sécurité

- Double authentification obligatoire pour les rôles sensibles, sessions courtes et révocables.
- Droits et périmètre de boutique revérifiés **côté serveur à chaque action**.
- Limitation du débit (connexion, envois de fichiers, extractions) partagée en base.
- Politique de sécurité du contenu (CSP) avec nonce, en-têtes de durcissement, secrets hors du code.
- Images ré-encodées et nettoyées de leurs métadonnées ; formats acceptés contrôlés côté serveur.
- Un **audit complet de sécurité et de performance** a été mené, suivi de correctifs et de mises à jour de dépendances.

## Fiabilité et qualité

- **520 tests unitaires et d'intégration** sur une vraie base PostgreSQL jetable.
- **69 tests de bout en bout** dans un vrai navigateur (connexion avec double authentification, ventes,
  réceptions, isolation entre boutiques, droits par rôle).
- Intégration continue : typage, lint, tests et build bloquent toute fusion en échec.
- Numérotation des reçus et des références **sans doublon même en accès simultanés** ; ventes idempotentes.
- Migrations de base de données appliquées automatiquement au déploiement de production.

## Stack technique

| | |
|---|---|
| **Front et serveur** | Next.js 16 (App Router), React 19, TypeScript strict, Tailwind CSS 4 |
| **Données** | PostgreSQL, Prisma 7 |
| **Authentification** | Auth.js v5, TOTP (double authentification), bcrypt |
| **Documents et fichiers** | UploadThing, Sharp, pdf-lib, Google Cloud Document AI |
| **Hors-ligne** | PWA : Serwist (service worker), IndexedDB |
| **Tests** | Vitest, Playwright |
| **Déploiement** | Vercel, GitHub Actions |

---

## In English

A **multi-shop stock and sales management platform** for a motorcycle and spare-parts business in Ghana:
three roles (Admin, CEO, shop Manager) with strict per-shop data isolation, bilingual UI, offline-capable
PWA, PDF receipts, supplier-credit tracking, automatic supplier-receipt OCR, public API and MCP server.

Built full-stack with **Next.js 16, React 19, TypeScript, PostgreSQL, Prisma, Auth.js (2FA), Tailwind**,
tested with **520 unit/integration tests and 69 end-to-end tests**, deployed on **Vercel** with CI on GitHub Actions.
Source code is private (production system for a client); this repository is a showcase only.
