
## 1. Utworzenie strony HTML



Zawartość pliku:

```html
<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <title>Laboratorium 12</title>
</head>
<body>
    <h1>Laboratorium 12</h1>
    <p>Imię i nazwisko: Natalia Sosińska</p>
    <p>Serwer nginx działa poprawnie.</p>
</body>
</html>
```


---

## 2. Utworzenie sieci mostkowej

```bash
docker network create --driver bridge lab12net
```

<img width="1133" height="231" alt="Zrzut ekranu 2026-06-5 o 13 52 09" src="https://github.com/user-attachments/assets/c1c77f26-cd93-491c-9644-e3bbb0004bcf" />

Sprawdzenie utworzonej sieci:

```bash
docker network ls
```

Sieć `lab12net` jest własną siecią typu `bridge`, do której zostały podłączone kontenery.


<img width="853" height="273" alt="Zrzut ekranu 2026-06-5 o 13 39 28" src="https://github.com/user-attachments/assets/c43eb42c-c5bb-457a-8d75-c30aee5ee6e5" />


---

## 3. Uruchomienie kontenerów nginx

### Kontener web1

```bash
docker run -d \
  --name web1 \
  --network lab12net \
  -p 8081:80 \
  --mount type=bind,source="$HOME/lab12/html/index.html",target=/usr/share/nginx/html/index.html,readonly \
  --mount type=bind,source="$HOME/lab12/web1-logs",target=/var/log/nginx \
  nginx:latest
```

Serwer `web1` jest dostępny pod adresem:

```text
http://localhost:8081
```

### Kontener web2

```bash
docker run -d \
  --name web2 \
  --network lab12net \
  -p 8082:80 \
  --mount type=bind,source="$HOME/lab12/html/index.html",target=/usr/share/nginx/html/index.html,readonly \
  --mount type=bind,source="$HOME/lab12/web2-logs",target=/var/log/nginx \
  nginx:latest
```

Serwer `web2` jest dostępny pod adresem:

```text
http://localhost:8082
```

### Kontener web3

```bash
docker run -d \
  --name web3 \
  --network lab12net \
  -p 8083:80 \
  --mount type=bind,source="$HOME/lab12/html/index.html",target=/usr/share/nginx/html/index.html,readonly \
  --mount type=bind,source="$HOME/lab12/web3-logs",target=/var/log/nginx \
  nginx:latest
```

Serwer `web3` jest dostępny pod adresem:

```text
http://localhost:8083
```
<img width="1133" height="679" alt="Zrzut ekranu 2026-06-5 o 13 50 29" src="https://github.com/user-attachments/assets/873df0bb-5bd7-45cf-8742-9561a438eb36" />
---

## 4. Sprawdzenie działających kontenerów

```bash
docker ps
```

W wyniku powinny być widoczne trzy kontenery:

- `web1`,
- `web2`,
- `web3`.

Każdy kontener powinien mieć przekierowany port:

- `8081:80`,
- `8082:80`,
- `8083:80`.

<img width="1070" height="245" alt="Zrzut ekranu 2026-06-5 o 13 40 09" src="https://github.com/user-attachments/assets/a8347f0f-0cc7-410c-8294-9aa2e93a4361" />

---

## 5. Sprawdzenie działania stron

Sprawdzenie działania stron z poziomu terminala:

```bash
curl http://localhost:8081
curl http://localhost:8082
curl http://localhost:8083
```

Każde polecenie powinno zwrócić zawartość strony HTML.

Strony można również sprawdzić w przeglądarce:

```text
http://localhost:8081
http://localhost:8082
http://localhost:8083
```

<img width="1077" height="371" alt="Zrzut ekranu 2026-06-5 o 13 41 12" src="https://github.com/user-attachments/assets/4831841d-6a23-41e6-bd39-51e20ee3d3be" />
<img width="1077" height="371" alt="Zrzut ekranu 2026-06-5 o 13 41 22" src="https://github.com/user-attachments/assets/8926d7d0-693d-4a94-9e15-e30b02165852" />
<img width="1077" height="371" alt="Zrzut ekranu 2026-06-5 o 13 41 29" src="https://github.com/user-attachments/assets/e0dddaad-7bb2-4ffd-9b22-3ccf36905513" />

---

## 6. Sprawdzenie podłączenia kontenerów do sieci

```bash
docker network inspect lab12net
```

W sekcji `Containers` powinny być widoczne kontenery:

- `web1`,
- `web2`,
- `web3`.

<img width="1119" height="931" alt="Zrzut ekranu 2026-06-5 o 13 42 40" src="https://github.com/user-attachments/assets/61b7a99f-009a-4d58-b6f7-d15972312b57" />

---

## 7. Sprawdzenie zamontowanych wolumenów

```bash
docker inspect web1
docker inspect web2
docker inspect web3
```

Wynik powinien potwierdzać, że plik:

```text
~/lab12/html/index.html
```

jest zamontowany jako:

```text
/usr/share/nginx/html/index.html
```

z uprawnieniami tylko do odczytu, czyli `readonly`.

Katalogi logów są zamontowane jako:

```text
/var/log/nginx
```

<img width="1119" height="455" alt="Zrzut ekranu 2026-06-5 o 13 44 38" src="https://github.com/user-attachments/assets/25e0e97a-de65-4901-a022-2bf98b059d42" />

---

## 8. Sprawdzenie zapisanych logów

Najpierw wygenerowano ruch na serwerach:

```bash
curl http://localhost:8081
curl http://localhost:8082
curl http://localhost:8083
```

Następnie sprawdzono pliki logów:

```bash
ls -la ~/lab12/web1-logs
ls -la ~/lab12/web2-logs
ls -la ~/lab12/web3-logs
```

Odczyt końcowych wpisów z logów:

```bash
tail ~/lab12/web1-logs/access.log
tail ~/lab12/web2-logs/access.log
tail ~/lab12/web3-logs/access.log
```

Wpisy w plikach `access.log` potwierdzają, że serwery poprawnie obsłużyły zapytania HTTP, a logi zostały zapisane w systemie macierzystym.

<img width="1294" height="371" alt="Zrzut ekranu 2026-06-5 o 13 46 16" src="https://github.com/user-attachments/assets/dc506ab0-91f7-48b7-8cd7-043764c17f82" />

---
