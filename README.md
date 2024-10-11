```
mkdir -p ~/minio/data
docker-compose -f minio-compose.yml up -d
```
Теперь MinIO Server доступен по адресу http://127.0.0.1:9000

- Создаём хранилище и добавляем в яндекс бакет объекты.
- Создаём ключ для доступа к бакету. В бакете: Access Keys → Create Access Key. Сохраняем Access Key и Secret Key, они потребуются позже.
- Создаём ключи для доступа к бакету: Сервисные аккаунты → Выбрать сервисный аккаунт → Создать новый ключ → Создать статический ключ доступа → Создать. Сохраняем идентификатор и секретный ключ.
- Устанавливаем rclone на ВМ:
```
curl https://rclone.org/install.sh | sudo bash
```
Настраиваем конфиг rsync для работы с хранилищами:
```
nano ~/.config/rclone/rclone.conf
```
```
[yandex-s3]
type = s3
provider = AWS
access_key_id = <Access Key Яндекс облака>
secret_access_key = <Secret Key Яндекс облака>
region = ru-central1
endpoint = storage.yandexcloud.net

[minio]
type = s3
provider = Minio
env_auth = false
access_key_id = <Access Key MinIO>
secret_access_key = <Secret Key MinIO>
region = ru-central1
endpoint = http://127.0.0.1:9000
```
Синхронизируем:
```
rclone sync yandex-s3:<имя бакета в Яндекс Облаке> minio:<имя бакета в MinIO>
```
Посмотреть список файлов можно:
```
rclone ls <yandex-s3>:<имя бакета>
rclone ls <minio>:<имя бакета>
```
Создаем в кроне задачу:
```
crontab -e
```
```
0 * * * * rclone sync yandex-s3:<имя бакета в Яндекс Облаке> minio:<имя бакета в MinIO>
```
