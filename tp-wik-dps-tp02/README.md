# WIK-DPS-TP02

## Contexte

Ce projet consiste à créer des images Docker pour une API développée précédemment (WIK-DPS-TP01). L'objectif est de créer deux images Docker optimisées, avec les consignes suivantes :

- **OneLayer** : Une image Docker avec une seule couche pour exécuter l'API.
- **TwoLayer** : Une image Docker avec deux couches : une pour la construction de l'API et une autre pour l'exécution (sans les sources).

Les images doivent utiliser un utilisateur spécifique pour exécuter le serveur web, et l'optimisation des images doit être faite en limitant le nombre de layers pour accélérer le temps de build.

## Consignes du TP

1. **Utilisation d'un utilisateur spécifique** : L'image doit exécuter le serveur web avec un utilisateur non privilégié. Vous devez créer un utilisateur dans le Dockerfile et lui attribuer les permissions appropriées pour exécuter l'API.

2. **Optimisation des layers** :
   - **OneLayer** : L'image doit être optimisée en une seule couche. Toutes les dépendances et fichiers doivent être copiés et construits dans cette couche.
   - **TwoLayer** : L'image doit être optimisée en deux stages :
     - Le premier stage construit l'API avec les sources.
     - Le deuxième stage copie uniquement les fichiers nécessaires pour l'exécution de l'API, sans les sources, afin de minimiser la taille de l'image finale.

3. **Minimisation du temps de build** : L'optimisation doit également prendre en compte l'ordre des layers. Par exemple, les dépendances qui ne changent pas souvent doivent être copiées avant les fichiers sources, afin que Docker puisse mettre en cache ces étapes et accélérer les builds suivants.

## Commandes à exécuter

### Build des images Docker

#### 1. Image OneLayer (un seul stage)
Cette image Docker contient un seul stage pour exécuter l'API avec toutes les dépendances et sources nécessaires.

```bash
docker build -t typescript-api-one -f OneLayer/Dockerfile .
```

#### 2. Image TwoLayer (deux stages)
Cette image Docker est construite en deux étapes :
- **Build Stage** : Construction de l'API avec toutes les sources.
- **Run Stage** : Exécution de l'API, sans les sources.

```bash
docker build -t typescript-api-two -f TwoLayer/Dockerfile .
```

## Scan des images Docker avec Trivy

Trivy est un scanner de vulnérabilités qui peut être utilisé pour analyser les images Docker. Après avoir construit les images, vous pouvez les scanner pour détecter des vulnérabilités de sécurité et des secrets exposés.

### 1. Scanner l'image OneLayer

Pour scanner l'image **typescript-api-one**, utilisez la commande suivante :

```bash
sudo trivy image typescript-api-one
```

Cela analysera l'image et affichera les vulnérabilités trouvées ainsi que les secrets potentiellement exposés.

### 2. Scanner l'image TwoLayer

Pour scanner l'image **typescript-api-two**, utilisez la commande suivante :

```bash
sudo trivy image typescript-api-two
```

Cela permettra de vérifier les vulnérabilités et les secrets dans cette image également.

## Résultat Scan Trivy :
### 1 Layer :
```
zeykii@zeykii:~/tp-wik-dps-tp02/OneLayer$ sudo trivy image typescript-api-one
2025-01-27T15:48:05+01:00       INFO    [vulndb] Need to update DB
2025-01-27T15:48:05+01:00       INFO    [vulndb] Downloading vulnerability DB...
2025-01-27T15:48:05+01:00       INFO    [vulndb] Downloading artifact...        repo="mirror.gcr.io/aquasec/trivy-db:2"
58.90 MiB / 58.90 MiB [-----------------------------------------------------------------------] 100.00% 4.43 MiB p/s 13s
2025-01-27T15:48:19+01:00       INFO    [vulndb] Artifact successfully downloaded       repo="mirror.gcr.io/aquasec/trivy-db:2"
2025-01-27T15:48:19+01:00       INFO    [vuln] Vulnerability scanning is enabled
2025-01-27T15:48:19+01:00       INFO    [secret] Secret scanning is enabled
2025-01-27T15:48:19+01:00       INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2025-01-27T15:48:19+01:00       INFO    [secret] Please see also https://aquasecurity.github.io/trivy/v0.58/docs/scanner/secret#recommendation for faster secret detection
2025-01-27T15:48:34+01:00       INFO    Detected OS     family="debian" version="12.9"
2025-01-27T15:48:34+01:00       INFO    [debian] Detecting vulnerabilities...   os_version="12" pkg_num=88
2025-01-27T15:48:34+01:00       INFO    Number of language-specific files       num=1
2025-01-27T15:48:34+01:00       INFO    [node-pkg] Detecting vulnerabilities...
2025-01-27T15:48:34+01:00       WARN    Using severities from other vendors for some vulnerabilities. Read https://aquasecurity.github.io/trivy/v0.58/docs/scanner/vulnerability#severity-selection for details.

typescript-api-one (debian 12.9)

Total: 75 (UNKNOWN: 0, LOW: 57, MEDIUM: 16, HIGH: 1, CRITICAL: 1)

┌────────────────────┬─────────────────────┬──────────┬──────────────┬───────────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│      Library       │    Vulnerability    │ Severity │    Status    │   Installed Version   │ Fixed Version │                            Title                             │
├────────────────────┼─────────────────────┼──────────┼──────────────┼───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ apt                │ CVE-2011-3374       │ LOW      │ affected     │ 2.6.1                 │               │ It was found that apt-key in apt, all versions, do not       │
│                    │                     │          │              │                       │               │ correctly...                                                 │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2011-3374                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ bash               │ TEMP-0841856-B18BAF │          │              │ 5.2.15-2+b7           │               │ [Privilege escalation possible to other user than root]      │
│                    │                     │          │              │                       │               │ https://security-tracker.debian.org/tracker/TEMP-0841856-B1- │
│                    │                     │          │              │                       │               │ 8BAF
                                                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ bsdutils           │ CVE-2022-0563       │          │              │ 1:2.38.1-5+deb12u3    │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563                    │
├────────────────────┼─────────────────────┤          ├──────────────┼───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ coreutils          │ CVE-2016-2781       │          │ will_not_fix │ 9.1-1                 │               │ coreutils: Non-privileged session can escape to the parent   │
│                    │                     │          │              │                       │               │ session in chroot                                            │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2016-2781                    │
│                    ├─────────────────────┤          ├──────────────┤                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2017-18018      │          │ affected     │                       │               │ coreutils: race condition vulnerability in chown and chgrp   │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2017-18018                   │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ gcc-12-base        │ CVE-2022-27943      │          │              │ 12.2.0-14             │               │ binutils: libiberty/rust-demangle.c in GNU GCC 11.2 allows   │
│                    │                     │          │              │                       │               │ stack exhaustion in demangle_const                           │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-27943                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-4039       │          │              │                       │               │ gcc: -fstack-protector fails to guard dynamic stack          │
│                    │                     │          │              │                       │               │ allocations on ARM64                                         │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-4039                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ gpgv               │ CVE-2022-3219       │          │              │ 2.2.40-1.1            │               │ gnupg: denial of service issue (resource consumption) using  │
│                    │                     │          │              │                       │               │ compressed packets                                           │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-3219                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libapt-pkg6.0      │ CVE-2011-3374       │          │              │ 2.6.1                 │               │ It was found that apt-key in apt, all versions, do not       │
│                    │                     │          │              │                       │               │ correctly...                                                 │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2011-3374                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libblkid1          │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563                    │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libc-bin           │ CVE-2025-0395       │ MEDIUM   │              │ 2.36-9+deb12u9        │               │ glibc: buffer overflow in the GNU C Library's assert()       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2025-0395                    │
│                    ├─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2010-4756       │ LOW      │              │                       │               │ glibc: glob implementation can cause excessive CPU and       │
│                    │                     │          │              │                       │               │ memory consumption due to...                                 │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2010-4756                    │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2018-20796      │          │              │                       │               │ glibc: uncontrolled recursion in function                    │
│                    │                     │          │              │                       │               │ check_dst_limits_calc_pos_1 in posix/regexec.c               │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2018-20796                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010022    │          │              │                       │               │ glibc: stack guard protection bypass                         │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010022                 │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010023    │          │              │                       │               │ glibc: running ldd on malicious ELF leads to code execution  │
│                    │                     │          │              │                       │               │ because of...                                                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010023                 │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010024    │          │              │                       │               │ glibc: ASLR bypass using cache of thread stack and heap      │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010024                 │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010025    │          │              │                       │               │ glibc: information disclosure of heap addresses of           │
│                    │                     │          │              │                       │               │ pthread_created thread                                       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010025                 │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-9192       │          │              │                       │               │ glibc: uncontrolled recursion in function                    │
│                    │                     │          │              │                       │               │ check_dst_limits_calc_pos_1 in posix/regexec.c               │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-9192                    │
├────────────────────┼─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│ libc6              │ CVE-2025-0395       │ MEDIUM   │              │                       │               │ glibc: buffer overflow in the GNU C Library's assert()       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2025-0395                    │
│                    ├─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2010-4756       │ LOW      │              │                       │               │ glibc: glob implementation can cause excessive CPU and       │
│                    │                     │          │              │                       │               │ memory consumption due to...                                 │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2010-4756                    │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2018-20796      │          │              │                       │               │ glibc: uncontrolled recursion in function                    │
│                    │                     │          │              │                       │               │ check_dst_limits_calc_pos_1 in posix/regexec.c               │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2018-20796                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010022    │          │              │                       │               │ glibc: stack guard protection bypass                         │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010022                 │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010023    │          │              │                       │               │ glibc: running ldd on malicious ELF leads to code execution  │
│                    │                     │          │              │                       │               │ because of...                                                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010023                 │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010024    │          │              │                       │               │ glibc: ASLR bypass using cache of thread stack and heap      │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010024                 │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010025    │          │              │                       │               │ glibc: information disclosure of heap addresses of           │
│                    │                     │          │              │                       │               │ pthread_created thread                                       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010025                 │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-9192       │          │              │                       │               │ glibc: uncontrolled recursion in function                    │
│                    │                     │          │              │                       │               │ check_dst_limits_calc_pos_1 in posix/regexec.c               │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-9192                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libgcc-s1          │ CVE-2022-27943      │          │              │ 12.2.0-14             │               │ binutils: libiberty/rust-demangle.c in GNU GCC 11.2 allows   │
│                    │                     │          │              │                       │               │ stack exhaustion in demangle_const                           │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-27943                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-4039       │          │              │                       │               │ gcc: -fstack-protector fails to guard dynamic stack          │
│                    │                     │          │              │                       │               │ allocations on ARM64                                         │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-4039                    │
├────────────────────┼─────────────────────┼──────────┼──────────────┼───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libgcrypt20        │ CVE-2024-2236       │ MEDIUM   │ fix_deferred │ 1.10.1-3              │               │ libgcrypt: vulnerable to Marvin Attack                       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-2236                    │
│                    ├─────────────────────┼──────────┼──────────────┤                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2018-6829       │ LOW      │ affected     │                       │               │ libgcrypt: ElGamal implementation doesn't have semantic      │
│                    │                     │          │              │                       │               │ security due to incorrectly encoded plaintexts...            │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2018-6829                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libgnutls30        │ CVE-2011-3389       │          │              │ 3.7.9-2+deb12u3       │               │ HTTPS: block-wise chosen-plaintext attack against SSL/TLS    │
│                    │                     │          │              │                       │               │ (BEAST)
                                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2011-3389                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libmount1          │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563                    │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libpam-modules     │ CVE-2024-10041      │ MEDIUM   │              │ 1.5.2-6+deb12u1       │               │ pam: libpam: Libpam vulnerable to read hashed password       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-10041                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-22365      │          │              │                       │               │ pam: allowing unprivileged user to block another user        │
│                    │                     │          │              │                       │               │ namespace
                                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-22365                   │
├────────────────────┼─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│ libpam-modules-bin │ CVE-2024-10041      │          │              │                       │               │ pam: libpam: Libpam vulnerable to read hashed password       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-10041                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-22365      │          │              │                       │               │ pam: allowing unprivileged user to block another user        │
│                    │                     │          │              │                       │               │ namespace
                                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-22365                   │
├────────────────────┼─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│ libpam-runtime     │ CVE-2024-10041      │          │              │                       │               │ pam: libpam: Libpam vulnerable to read hashed password       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-10041                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-22365      │          │              │                       │               │ pam: allowing unprivileged user to block another user        │
│                    │                     │          │              │                       │               │ namespace
                                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-22365                   │
├────────────────────┼─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│ libpam0g           │ CVE-2024-10041      │          │              │                       │               │ pam: libpam: Libpam vulnerable to read hashed password       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-10041                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-22365      │          │              │                       │               │ pam: allowing unprivileged user to block another user        │
│                    │                     │          │              │                       │               │ namespace
                                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-22365                   │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libsmartcols1      │ CVE-2022-0563       │ LOW      │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libstdc++6         │ CVE-2022-27943      │          │              │ 12.2.0-14             │               │ binutils: libiberty/rust-demangle.c in GNU GCC 11.2 allows   │
│                    │                     │          │              │                       │               │ stack exhaustion in demangle_const                           │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-27943                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-4039       │          │              │                       │               │ gcc: -fstack-protector fails to guard dynamic stack          │
│                    │                     │          │              │                       │               │ allocations on ARM64                                         │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-4039                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libsystemd0        │ CVE-2013-4392       │          │              │ 252.33-1~deb12u1      │               │ systemd: TOCTOU race condition when updating file            │
│                    │                     │          │              │                       │               │ permissions and SELinux security contexts...                 │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2013-4392                    │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31437      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ modify a...                                                  │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31437                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31438      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ truncate a...                                                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31438                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31439      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ modify the...                                                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31439                   │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libtinfo6          │ CVE-2023-50495      │ MEDIUM   │              │ 6.4-4                 │               │ ncurses: segmentation fault via _nc_wrap_entry()             │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-50495                   │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libudev1           │ CVE-2013-4392       │ LOW      │              │ 252.33-1~deb12u1      │               │ systemd: TOCTOU race condition when updating file            │
│                    │                     │          │              │                       │               │ permissions and SELinux security contexts...                 │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2013-4392                    │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31437      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ modify a...                                                  │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31437                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31438      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ truncate a...                                                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31438                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31439      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ modify the...                                                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31439                   │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libuuid1           │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563                    │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ login              │ CVE-2023-4641       │ MEDIUM   │              │ 1:4.13+dfsg1-1+b1     │               │ shadow-utils: possible password leak during passwd(1) change │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-4641                    │
│                    ├─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2007-5686       │ LOW      │              │                       │               │ initscripts in rPath Linux 1 sets insecure permissions for   │
│                    │                     │          │              │                       │               │ the /var/lo ......                                           │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2007-5686                    │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-29383      │          │              │                       │               │ shadow: Improper input validation in shadow-utils package    │
│                    │                     │          │              │                       │               │ utility chfn                                                 │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-29383                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-56433      │          │              │                       │               │ shadow-utils: Default subordinate ID configuration in        │
│                    │                     │          │              │                       │               │ /etc/login.defs could lead to compromise                     │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-56433                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ TEMP-0628843-DBAD28 │          │              │                       │               │ [more related to CVE-2005-4890]                              │
│                    │                     │          │              │                       │               │ https://security-tracker.debian.org/tracker/TEMP-0628843-DB- │
│                    │                     │          │              │                       │               │ AD28
                                                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ mount              │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563                    │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ ncurses-base       │ CVE-2023-50495      │ MEDIUM   │              │ 6.4-4                 │               │ ncurses: segmentation fault via _nc_wrap_entry()             │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-50495                   │
├────────────────────┤                     │          │              │                       ├───────────────┤
                                                    │
│ ncurses-bin        │                     │          │              │                       │               │
                                                    │
│                    │                     │          │              │                       │               │
                                                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ passwd             │ CVE-2023-4641       │          │              │ 1:4.13+dfsg1-1+b1     │               │ shadow-utils: possible password leak during passwd(1) change │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-4641                    │
│                    ├─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2007-5686       │ LOW      │              │                       │               │ initscripts in rPath Linux 1 sets insecure permissions for   │
│                    │                     │          │              │                       │               │ the /var/lo ......                                           │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2007-5686                    │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-29383      │          │              │                       │               │ shadow: Improper input validation in shadow-utils package    │
│                    │                     │          │              │                       │               │ utility chfn                                                 │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-29383                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-56433      │          │              │                       │               │ shadow-utils: Default subordinate ID configuration in        │
│                    │                     │          │              │                       │               │ /etc/login.defs could lead to compromise                     │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-56433                   │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ TEMP-0628843-DBAD28 │          │              │                       │               │ [more related to CVE-2005-4890]                              │
│                    │                     │          │              │                       │               │ https://security-tracker.debian.org/tracker/TEMP-0628843-DB- │
│                    │                     │          │              │                       │               │ AD28
                                                    │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ perl-base          │ CVE-2023-31484      │ HIGH     │              │ 5.36.0-7+deb12u1      │               │ perl: CPAN.pm does not verify TLS certificates when          │
│                    │                     │          │              │                       │               │ downloading distributions over HTTPS...                      │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31484                   │
│                    ├─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2011-4116       │ LOW      │              │                       │               │ perl: File:: Temp insecure temporary file handling           │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2011-4116                    │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31486      │          │              │                       │               │ http-tiny: insecure TLS cert default                         │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31486                   │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ sysvinit-utils     │ TEMP-0517018-A83CE6 │          │              │ 3.06-4                │               │ [sysvinit: no-root option in expert installer exposes        │
│                    │                     │          │              │                       │               │ locally exploitable security flaw]                           │
│                    │                     │          │              │                       │               │ https://security-tracker.debian.org/tracker/TEMP-0517018-A8- │
│                    │                     │          │              │                       │               │ 3CE6
                                                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ tar                │ CVE-2005-2541       │          │              │ 1.34+dfsg-1.2+deb12u1 │               │ tar: does not properly warn the user when extracting setuid  │
│                    │                     │          │              │                       │               │ or setgid...                                                 │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2005-2541                    │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ TEMP-0290435-0B57B5 │          │              │                       │               │ [tar's rmt command may have undesired side effects]          │
│                    │                     │          │              │                       │               │ https://security-tracker.debian.org/tracker/TEMP-0290435-0B- │
│                    │                     │          │              │                       │               │ 57B5
                                                    │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ util-linux         │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...                                    │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563                    │
├────────────────────┤                     │          │              │                       ├───────────────┤
                                                    │
│ util-linux-extra   │                     │          │              │                       │               │
                                                    │
│                    │                     │          │              │                       │               │
                                                    │
│                    │                     │          │              │                       │               │
                                                    │
├────────────────────┼─────────────────────┼──────────┼──────────────┼───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ zlib1g             │ CVE-2023-45853      │ CRITICAL │ will_not_fix │ 1:1.2.13.dfsg-1       │               │ zlib: integer overflow and resultant heap-based buffer       │
│                    │                     │          │              │                       │               │ overflow in zipOpenNewFileInZip4_6                           │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-45853                   │
└────────────────────┴─────────────────────┴──────────┴──────────────┴───────────────────────┴───────────────┴──────────────────────────────────────────────────────────────┘
2025-01-27T15:48:34+01:00       INFO    Table result includes only package filenames. Use '--format json' option to get the full path to the package file.

Node.js (node-pkg)

Total: 1 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 1, CRITICAL: 0)

┌────────────────────────────┬────────────────┬──────────┬────────┬───────────────────┬───────────────┬───────────────────────────────────────────────────┐
│          Library           │ Vulnerability  │ Severity │ Status │ Installed Version │ Fixed Version │                       Title                       │
├────────────────────────────┼────────────────┼──────────┼────────┼───────────────────┼───────────────┼───────────────────────────────────────────────────┤
│ cross-spawn (package.json) │ CVE-2024-21538 │ HIGH     │ fixed  │ 7.0.3             │ 7.0.5, 6.0.6  │ cross-spawn: regular expression denial of service │
│                            │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2024-21538        │
└────────────────────────────┴────────────────┴──────────┴────────┴───────────────────┴───────────────┴───────────────────────────────────────────────────┘
```

### 2 Layer :

```
zeykii@zeykii:~/tp-wik-dps-tp02/OneLayer$ sudo trivy image typescript-api-two
2025-01-27T15:49:20+01:00       INFO    [vuln] Vulnerability scanning is enabled
2025-01-27T15:49:20+01:00       INFO    [secret] Secret scanning is enabled
2025-01-27T15:49:20+01:00       INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2025-01-27T15:49:20+01:00       INFO    [secret] Please see also https://aquasecurity.github.io/trivy/v0.58/docs/scanner/secret#recommendation for faster secret detection
2025-01-27T15:49:24+01:00       INFO    Detected OS     family="debian" version="12.9"
2025-01-27T15:49:24+01:00       INFO    [debian] Detecting vulnerabilities...   os_version="12" pkg_num=88
2025-01-27T15:49:24+01:00       INFO    Number of language-specific files       num=1
2025-01-27T15:49:24+01:00       INFO    [node-pkg] Detecting vulnerabilities...
2025-01-27T15:49:24+01:00       WARN    Using severities from other vendors for some vulnerabilities. Read https://aquasecurity.github.io/trivy/v0.58/docs/scanner/vulnerability#severity-selection for details.

typescript-api-two (debian 12.9)

Total: 75 (UNKNOWN: 0, LOW: 57, MEDIUM: 16, HIGH: 1, CRITICAL: 1)

┌────────────────────┬─────────────────────┬──────────┬──────────────┬───────────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│      Library       │    Vulnerability    │ Severity │    Status    │   Installed Version   │ Fixed Version │                            Title
                │
├────────────────────┼─────────────────────┼──────────┼──────────────┼───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ apt                │ CVE-2011-3374       │ LOW      │ affected     │ 2.6.1                 │               │ It was found that apt-key in apt, all versions, do not       │
│                    │                     │          │              │                       │               │ correctly...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2011-3374
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ bash               │ TEMP-0841856-B18BAF │          │              │ 5.2.15-2+b7           │               │ [Privilege escalation possible to other user than root]      │
│                    │                     │          │              │                       │               │ https://security-tracker.debian.org/tracker/TEMP-0841856-B1- │
│                    │                     │          │              │                       │               │ 8BAF
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ bsdutils           │ CVE-2022-0563       │          │              │ 1:2.38.1-5+deb12u3    │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563
                │
├────────────────────┼─────────────────────┤          ├──────────────┼───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ coreutils          │ CVE-2016-2781       │          │ will_not_fix │ 9.1-1                 │               │ coreutils: Non-privileged session can escape to the parent   │
│                    │                     │          │              │                       │               │ session in chroot
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2016-2781
                │
│                    ├─────────────────────┤          ├──────────────┤                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2017-18018      │          │ affected     │                       │               │ coreutils: race condition vulnerability in chown and chgrp   │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2017-18018
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ gcc-12-base        │ CVE-2022-27943      │          │              │ 12.2.0-14             │               │ binutils: libiberty/rust-demangle.c in GNU GCC 11.2 allows   │
│                    │                     │          │              │                       │               │ stack exhaustion in demangle_const
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-27943
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-4039       │          │              │                       │               │ gcc: -fstack-protector fails to guard dynamic stack          │
│                    │                     │          │              │                       │               │ allocations on ARM64
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-4039
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ gpgv               │ CVE-2022-3219       │          │              │ 2.2.40-1.1            │               │ gnupg: denial of service issue (resource consumption) using  │
│                    │                     │          │              │                       │               │ compressed packets
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-3219
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libapt-pkg6.0      │ CVE-2011-3374       │          │              │ 2.6.1                 │               │ It was found that apt-key in apt, all versions, do not       │
│                    │                     │          │              │                       │               │ correctly...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2011-3374
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libblkid1          │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563
                │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libc-bin           │ CVE-2025-0395       │ MEDIUM   │              │ 2.36-9+deb12u9        │               │ glibc: buffer overflow in the GNU C Library's assert()       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2025-0395
                │
│                    ├─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2010-4756       │ LOW      │              │                       │               │ glibc: glob implementation can cause excessive CPU and       │
│                    │                     │          │              │                       │               │ memory consumption due to...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2010-4756
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2018-20796      │          │              │                       │               │ glibc: uncontrolled recursion in function
                │
│                    │                     │          │              │                       │               │ check_dst_limits_calc_pos_1 in posix/regexec.c               │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2018-20796
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010022    │          │              │                       │               │ glibc: stack guard protection bypass
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010022
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010023    │          │              │                       │               │ glibc: running ldd on malicious ELF leads to code execution  │
│                    │                     │          │              │                       │               │ because of...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010023
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010024    │          │              │                       │               │ glibc: ASLR bypass using cache of thread stack and heap      │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010024
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010025    │          │              │                       │               │ glibc: information disclosure of heap addresses of           │
│                    │                     │          │              │                       │               │ pthread_created thread
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010025
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-9192       │          │              │                       │               │ glibc: uncontrolled recursion in function
                │
│                    │                     │          │              │                       │               │ check_dst_limits_calc_pos_1 in posix/regexec.c               │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-9192
                │
├────────────────────┼─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│ libc6              │ CVE-2025-0395       │ MEDIUM   │              │                       │               │ glibc: buffer overflow in the GNU C Library's assert()       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2025-0395
                │
│                    ├─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2010-4756       │ LOW      │              │                       │               │ glibc: glob implementation can cause excessive CPU and       │
│                    │                     │          │              │                       │               │ memory consumption due to...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2010-4756
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2018-20796      │          │              │                       │               │ glibc: uncontrolled recursion in function
                │
│                    │                     │          │              │                       │               │ check_dst_limits_calc_pos_1 in posix/regexec.c               │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2018-20796
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010022    │          │              │                       │               │ glibc: stack guard protection bypass
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010022
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010023    │          │              │                       │               │ glibc: running ldd on malicious ELF leads to code execution  │
│                    │                     │          │              │                       │               │ because of...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010023
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010024    │          │              │                       │               │ glibc: ASLR bypass using cache of thread stack and heap      │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010024
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010025    │          │              │                       │               │ glibc: information disclosure of heap addresses of           │
│                    │                     │          │              │                       │               │ pthread_created thread
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-1010025
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-9192       │          │              │                       │               │ glibc: uncontrolled recursion in function
                │
│                    │                     │          │              │                       │               │ check_dst_limits_calc_pos_1 in posix/regexec.c               │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2019-9192
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libgcc-s1          │ CVE-2022-27943      │          │              │ 12.2.0-14             │               │ binutils: libiberty/rust-demangle.c in GNU GCC 11.2 allows   │
│                    │                     │          │              │                       │               │ stack exhaustion in demangle_const
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-27943
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-4039       │          │              │                       │               │ gcc: -fstack-protector fails to guard dynamic stack          │
│                    │                     │          │              │                       │               │ allocations on ARM64
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-4039
                │
├────────────────────┼─────────────────────┼──────────┼──────────────┼───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libgcrypt20        │ CVE-2024-2236       │ MEDIUM   │ fix_deferred │ 1.10.1-3              │               │ libgcrypt: vulnerable to Marvin Attack
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-2236
                │
│                    ├─────────────────────┼──────────┼──────────────┤                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2018-6829       │ LOW      │ affected     │                       │               │ libgcrypt: ElGamal implementation doesn't have semantic      │
│                    │                     │          │              │                       │               │ security due to incorrectly encoded plaintexts...            │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2018-6829
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libgnutls30        │ CVE-2011-3389       │          │              │ 3.7.9-2+deb12u3       │               │ HTTPS: block-wise chosen-plaintext attack against SSL/TLS    │
│                    │                     │          │              │                       │               │ (BEAST)
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2011-3389
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libmount1          │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563
                │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libpam-modules     │ CVE-2024-10041      │ MEDIUM   │              │ 1.5.2-6+deb12u1       │               │ pam: libpam: Libpam vulnerable to read hashed password       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-10041
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-22365      │          │              │                       │               │ pam: allowing unprivileged user to block another user        │
│                    │                     │          │              │                       │               │ namespace
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-22365
                │
├────────────────────┼─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│ libpam-modules-bin │ CVE-2024-10041      │          │              │                       │               │ pam: libpam: Libpam vulnerable to read hashed password       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-10041
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-22365      │          │              │                       │               │ pam: allowing unprivileged user to block another user        │
│                    │                     │          │              │                       │               │ namespace
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-22365
                │
├────────────────────┼─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│ libpam-runtime     │ CVE-2024-10041      │          │              │                       │               │ pam: libpam: Libpam vulnerable to read hashed password       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-10041
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-22365      │          │              │                       │               │ pam: allowing unprivileged user to block another user        │
│                    │                     │          │              │                       │               │ namespace
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-22365
                │
├────────────────────┼─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│ libpam0g           │ CVE-2024-10041      │          │              │                       │               │ pam: libpam: Libpam vulnerable to read hashed password       │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-10041
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-22365      │          │              │                       │               │ pam: allowing unprivileged user to block another user        │
│                    │                     │          │              │                       │               │ namespace
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-22365
                │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libsmartcols1      │ CVE-2022-0563       │ LOW      │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libstdc++6         │ CVE-2022-27943      │          │              │ 12.2.0-14             │               │ binutils: libiberty/rust-demangle.c in GNU GCC 11.2 allows   │
│                    │                     │          │              │                       │               │ stack exhaustion in demangle_const
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-27943
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-4039       │          │              │                       │               │ gcc: -fstack-protector fails to guard dynamic stack          │
│                    │                     │          │              │                       │               │ allocations on ARM64
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-4039
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libsystemd0        │ CVE-2013-4392       │          │              │ 252.33-1~deb12u1      │               │ systemd: TOCTOU race condition when updating file            │
│                    │                     │          │              │                       │               │ permissions and SELinux security contexts...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2013-4392
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31437      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ modify a...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31437
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31438      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ truncate a...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31438
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31439      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ modify the...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31439
                │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libtinfo6          │ CVE-2023-50495      │ MEDIUM   │              │ 6.4-4                 │               │ ncurses: segmentation fault via _nc_wrap_entry()             │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-50495
                │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libudev1           │ CVE-2013-4392       │ LOW      │              │ 252.33-1~deb12u1      │               │ systemd: TOCTOU race condition when updating file            │
│                    │                     │          │              │                       │               │ permissions and SELinux security contexts...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2013-4392
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31437      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ modify a...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31437
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31438      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ truncate a...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31438
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31439      │          │              │                       │               │ An issue was discovered in systemd 253. An attacker can      │
│                    │                     │          │              │                       │               │ modify the...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31439
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libuuid1           │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563
                │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ login              │ CVE-2023-4641       │ MEDIUM   │              │ 1:4.13+dfsg1-1+b1     │               │ shadow-utils: possible password leak during passwd(1) change │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-4641
                │
│                    ├─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2007-5686       │ LOW      │              │                       │               │ initscripts in rPath Linux 1 sets insecure permissions for   │
│                    │                     │          │              │                       │               │ the /var/lo ......
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2007-5686
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-29383      │          │              │                       │               │ shadow: Improper input validation in shadow-utils package    │
│                    │                     │          │              │                       │               │ utility chfn
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-29383
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-56433      │          │              │                       │               │ shadow-utils: Default subordinate ID configuration in        │
│                    │                     │          │              │                       │               │ /etc/login.defs could lead to compromise
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-56433
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ TEMP-0628843-DBAD28 │          │              │                       │               │ [more related to CVE-2005-4890]
                │
│                    │                     │          │              │                       │               │ https://security-tracker.debian.org/tracker/TEMP-0628843-DB- │
│                    │                     │          │              │                       │               │ AD28
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ mount              │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563
                │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ ncurses-base       │ CVE-2023-50495      │ MEDIUM   │              │ 6.4-4                 │               │ ncurses: segmentation fault via _nc_wrap_entry()             │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-50495
                │
├────────────────────┤                     │          │              │                       ├───────────────┤
                │
│ ncurses-bin        │                     │          │              │                       │               │
                │
│                    │                     │          │              │                       │               │
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ passwd             │ CVE-2023-4641       │          │              │ 1:4.13+dfsg1-1+b1     │               │ shadow-utils: possible password leak during passwd(1) change │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-4641
                │
│                    ├─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2007-5686       │ LOW      │              │                       │               │ initscripts in rPath Linux 1 sets insecure permissions for   │
│                    │                     │          │              │                       │               │ the /var/lo ......
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2007-5686
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-29383      │          │              │                       │               │ shadow: Improper input validation in shadow-utils package    │
│                    │                     │          │              │                       │               │ utility chfn
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-29383
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-56433      │          │              │                       │               │ shadow-utils: Default subordinate ID configuration in        │
│                    │                     │          │              │                       │               │ /etc/login.defs could lead to compromise
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2024-56433
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ TEMP-0628843-DBAD28 │          │              │                       │               │ [more related to CVE-2005-4890]
                │
│                    │                     │          │              │                       │               │ https://security-tracker.debian.org/tracker/TEMP-0628843-DB- │
│                    │                     │          │              │                       │               │ AD28
                │
├────────────────────┼─────────────────────┼──────────┤              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ perl-base          │ CVE-2023-31484      │ HIGH     │              │ 5.36.0-7+deb12u1      │               │ perl: CPAN.pm does not verify TLS certificates when          │
│                    │                     │          │              │                       │               │ downloading distributions over HTTPS...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31484
                │
│                    ├─────────────────────┼──────────┤              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2011-4116       │ LOW      │              │                       │               │ perl: File:: Temp insecure temporary file handling           │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2011-4116
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-31486      │          │              │                       │               │ http-tiny: insecure TLS cert default
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-31486
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ sysvinit-utils     │ TEMP-0517018-A83CE6 │          │              │ 3.06-4                │               │ [sysvinit: no-root option in expert installer exposes        │
│                    │                     │          │              │                       │               │ locally exploitable security flaw]
                │
│                    │                     │          │              │                       │               │ https://security-tracker.debian.org/tracker/TEMP-0517018-A8- │
│                    │                     │          │              │                       │               │ 3CE6
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ tar                │ CVE-2005-2541       │          │              │ 1.34+dfsg-1.2+deb12u1 │               │ tar: does not properly warn the user when extracting setuid  │
│                    │                     │          │              │                       │               │ or setgid...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2005-2541
                │
│                    ├─────────────────────┤          │              │                       ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ TEMP-0290435-0B57B5 │          │              │                       │               │ [tar's rmt command may have undesired side effects]          │
│                    │                     │          │              │                       │               │ https://security-tracker.debian.org/tracker/TEMP-0290435-0B- │
│                    │                     │          │              │                       │               │ 57B5
                │
├────────────────────┼─────────────────────┤          │              ├───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ util-linux         │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                       │               │ and chsh when compiled...
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2022-0563
                │
├────────────────────┤                     │          │              │                       ├───────────────┤
                │
│ util-linux-extra   │                     │          │              │                       │               │
                │
│                    │                     │          │              │                       │               │
                │
│                    │                     │          │              │                       │               │
                │
├────────────────────┼─────────────────────┼──────────┼──────────────┼───────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ zlib1g             │ CVE-2023-45853      │ CRITICAL │ will_not_fix │ 1:1.2.13.dfsg-1       │               │ zlib: integer overflow and resultant heap-based buffer       │
│                    │                     │          │              │                       │               │ overflow in zipOpenNewFileInZip4_6
                │
│                    │                     │          │              │                       │               │ https://avd.aquasec.com/nvd/cve-2023-45853
                │
└────────────────────┴─────────────────────┴──────────┴──────────────┴───────────────────────┴───────────────┴──────────────────────────────────────────────────────────────┘
2025-01-27T15:49:24+01:00       INFO    Table result includes only package filenames. Use '--format json' option to get the full path to the package file.

Node.js (node-pkg)

Total: 1 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 1, CRITICAL: 0)

┌────────────────────────────┬────────────────┬──────────┬────────┬───────────────────┬───────────────┬───────────────────────────────────────────────────┐
│          Library           │ Vulnerability  │ Severity │ Status │ Installed Version │ Fixed Version │                       Title                       │
├────────────────────────────┼────────────────┼──────────┼────────┼───────────────────┼───────────────┼───────────────────────────────────────────────────┤
│ cross-spawn (package.json) │ CVE-2024-21538 │ HIGH     │ fixed  │ 7.0.3             │ 7.0.5, 6.0.6  │ cross-spawn: regular expression denial of service │
│                            │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2024-21538        │
└────────────────────────────┴────────────────┴──────────┴────────┴───────────────────┴───────────────┴───────────────────────────────────────────────────┘
```