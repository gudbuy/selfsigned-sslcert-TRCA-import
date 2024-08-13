### 1. Генерация ключевой пары

Сначала нужно создать приватный ключ:

```bash
openssl genpkey -algorithm RSA -out private.key
```

### 2. Создание запроса на сертификат (CSR)

Затем нужно создать запрос на сертификат, указав IP-адрес в качестве альтернативного имени субъекта (SAN):

```bash
openssl req -new -key private.key -out request.csr -subj "/CN=<IP_ADDRESS>"
```

### 3. Создание файла конфигурации для расширений X.509

Создайте файл конфигурации, например `san.cnf`, с содержимым:

```conf
[ req ]
default_bits       = 2048
distinguished_name = req_distinguished_name
req_extensions     = req_ext
prompt             = no

[ req_distinguished_name ]
commonName         = <IP_ADDRESS>

[ req_ext ]
subjectAltName     = @alt_names

[ alt_names ]
IP.1               = <IP_ADDRESS>
```

Замените `<IP_ADDRESS>` на ваш фактический IP-адрес.

### 4. Генерация самоподписанного сертификата

Используя созданный запрос и конфигурационный файл, создайте самоподписанный сертификат:

```bash
openssl x509 -req -in request.csr -signkey private.key -out certificate.crt -days 365 -extfile san.cnf -extensions req_ext
```
