---
tags:
  - Info
  - Monitooring
---

# Süsteemide monitooring

Õpid jälgima masinaid ja teenuseid mõõdikute, alarmide, logide ja jälgede kaudu ning leidma rikke põhjuse andmete põhjal. Kursus jätkab DevOps Lite'i: kasutad samu kolme masinat, Ansible'it, Docker Compose'i ja Giti.

[Alusta esimesest kohtumisest](k01/lecture.md){ .md-button .md-button--primary }

| | |
|---|---|
| **Maht** | 5 kohtumist × 4 akadeemilist tundi ja 58 akadeemilist tundi iseseisvat tööd. Loeng on kuni 30 minutit, ülejäänu on praktikum. |
| **Eeldused** | DevOps Lite või samaväärne: Linuxi käsurida, SSH, Ansible'i playbook, Docker Compose, Git |
| **Keskkond** | kooli Proxmoxis kolm AlmaLinux 9 VM-i: vm1 on control node, kus on Ansible, Git ja sinu repositoorium. vm3-s töötab Docker. Ühendus VS Code Remote-SSH kaudu, kodust VPN-iga |
| **Küsimused ja teated** | [github.com/hkhk-automation/its25-arutelu/discussions](https://github.com/hkhk-automation/its25-arutelu/discussions) |

??? abstract "Kursuse õpiväljundid"
    Kursuse lõpuks oskad:

    1. paigaldada mõõdikute kogumise koodina ja kontrollida, et andmed jõuavad kohale;
    2. koostada päringuid ja töölaua, mille järgi saab masinate seisundi kohta otsustada;
    3. seadistada alarmi ja kättesaadavuse kontrolli ning saada teavituse;
    4. otsida logisid siltide ja sisu järgi;
    5. leida ühe päringu teekond läbi mitme teenuse;
    6. uurida riket mõõdikute, logide ja jälgede põhjal ning kontrollida taastumist.

## Kohtumised

| | Teema | Tööriistad |
|---|---|---|
| **[K1](k01/lecture.md)** | Mõõdikud: server küsib, exporter vastab, töölaud | node_exporter Ansible'iga, Prometheus ja Grafana Docker Compose'iga, PromQL |
| **K2** | Alarmid ja kättesaadavus | Prometheuse reeglid, Alertmanager, Blackbox exporter |
| **K3** | Logid sildi järgi | Loki, Grafana Alloy |
| **K4** | Päringu teekond | OpenTelemetry, Jaeger, näidisrakendus |
| **K5** | Rike: avastamine, põhjus ja taastumise kontroll | kõik koos |

Iga kohtumine jätkab eelmise tulemust: K1-s paigaldatud Prometheus ja Grafana jäävad vm3-s tööle ja K2 lisab neile alarmid.

## Esitamine

| | |
|---|---|
| **Kuhu** | iga praktikum on Classroom 50 ülesanne; link tuleb kohtumisel ja sellest tekib sulle oma repositoorium |
| **Tähtaeg** | kirjas Classroom 50-s |
| **Reegel** | repositooriumisse ei lähe paroole, võtmeid ega tokeneid |
