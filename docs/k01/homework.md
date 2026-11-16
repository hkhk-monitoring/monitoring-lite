---
tags:
  - Kodutöö
  - Monitooring
  - Ansible
---

# Kodutöö: oma mõõdik node_exporterisse

node_exporter avaldab masina näite, kuid mõnikord on vaja mõõta midagi, mida exporter vaikimisi ei avalda. Kodutöös lisad vm1-sse ja vm2-sse oma mõõdiku: kausta `/var/log` kettaruumikasutuse baitides. Mõõdiku kirjutab skript faili, node_exporter avaldab selle koos teiste mõõdikutega ja Grafana näitab seda töölaual.

Eeldus on praktikumi osad 4, 5 ja 7: node_exporter on paigaldatud playbook'iga, Prometheus küsib mõlemat masinat ja Grafana töötab.

## Osa 1 · Mõõdik failist

node_exporteril on [textfile collector](https://github.com/prometheus/node_exporter#textfile-collector). See loeb kaustast kõik `.prom` laiendiga failid ja avaldab nende sisu aadressil `/metrics`. Kausta annad node_exporterile käivitamisel võtmega `--collector.textfile.directory`. Faili sisu on sama vorming, mida nägid praktikumi osas 3:

```text
# HELP logide_maht_baitides Kausta /var/log kettaruumikasutus baitides.
# TYPE logide_maht_baitides gauge
logide_maht_baitides 48213760
```

Kirjuta skript `konf/logimaht.sh`, mis:

- arvutab kausta `/var/log` kettaruumikasutuse baitides käsuga `du -s -B1 /var/log`;
- kirjutab ülaltoodud kolm rida kõigepealt ajutisse faili samas kaustas, näiteks `logid.prom.$$`. Ajutise faili nimi ei tohi lõppeda `.prom`-iga, sest siis loeks node_exporter ka poolikut faili;
- annab valmis failile õigused `0644` ja nimetab selle ümber nimeks `/var/lib/node_exporter/textfile/logid.prom` (`mv`).

Kui `du` ebaõnnestub, peab skript lõppema veaga ega tohi viimast korrektset faili üle kirjutada. `mv` samas failisüsteemis vahetab faili korraga välja, nii et node_exporter näeb kas vana või uut faili, mitte poolikut.

## Osa 2 · Playbook

Täienda `konf/node_exporter.yml` nii, et see:

- loob kausta `/var/lib/node_exporter/textfile`, omanik `root` ja õigused `0755`;
- lisab unit-faili real `ExecStart` võtme `--collector.textfile.directory=/var/lib/node_exporter/textfile`. Unit-faili muutus teavitab handler'it, mis taaskäivitab node_exporteri;
- kopeerib skripti `/usr/local/bin/logimaht.sh`, omanik `root` ja õigused `0755`;
- käivitab skripti iga 5 minuti järel kasutajana `root` (`ansible.builtin.cron`). Kui `systemctl is-active crond` ei vasta `active`, lisa pakett `cronie` ja teenus `crond`.

Käivita playbook kaks korda. Teisel korral on `changed=0`.

## Osa 3 · Kontrolli, päri ja näita

```bash
for ip in <vm1-IP> <vm2-IP>; do curl -fsS http://$ip:9100/metrics | grep '^logide_maht_baitides'; done
```

??? success "Oodatav tulemus"
    Mõlema masina kohta rida `logide_maht_baitides` arvuga. 5 minuti pärast on arv uuesti arvutatud.

Kirjuta päring, mis näitab mõlema masina `/var/log` kettaruumikasutust mebibaitides ja salvesta tulemus. `<sinu päring>` asemele kirjuta oma päring:

```bash
ssh vm3 "curl -fsS localhost:9090/api/v1/query --data-urlencode 'query=<sinu päring>'" > logid/kodutoo-paring.json
```

??? success "Oodatav tulemus"
    `"status":"success"` ja kaks tulemust, kummalgi eri `instance` ja arv mebibaitides.

Lisa päring Grafana töölauale K1 kolmanda paneelina ja ekspordi töölaud uuesti faili `konf/toolaud.json`.

??? question "Kas värske mõõtmisaeg tõendab värsket mõõtmist?"
    Skript lakkab töötamast, aga viimane `logid.prom` jääb alles. Prometheus kogub mõõdikut edasi iga 15 sekundi järel. Kas see tõendab, et kausta mahtu mõõdeti hiljuti?

    ??? success "Vastus"
        Ei tõenda. Prometheus salvestab kogumise aja, mitte faili kirjutamise aja, ja näitab vana väärtust edasi. Faili vanust näitab node_exporteri mõõdik `node_textfile_mtime_seconds`, mille põhjal saab K2-s teha alarmi.

## Töö esitamine

```bash
git add konf logid
git commit -m "K1 kodutöö: oma mõõdik"
git push
```

Tähtaja näed Classroom 50-s.
