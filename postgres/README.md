### Каталог для настройки мониторинга PostgreSQL

Предполагается, что уже есть сервер с установленным и запущенным Prometheus и Grafana

1. Установить на сервер с PostgreSQL docker и docker-compose
2. В postgressql добавить пользователя для мониторинга (права должны быть Superuser) изадать ему пароль
3. Проверить настройки pg_hba.conf, чтобы можно было подключаться к базе не только по peer, а также по md5
4. Скачать файл docker-compose.yml. В нем настроен запуск контейнера с экспортером метрик PostgreSQL.
5. В каталоге создать `.env` файл. В него прописать два параметра `POSTGRES_USER` и `POSTGRES_PASSWORD`
6. Запустить контейнер с экспортером метрик
7. В файле prometheus.yml добавить job для сбора метрик с экспортера PostgreSQL. Если у вас несколько серверов PostgreSQL, то добавить либо несколько job, либо использовать relabel_configs для разделения метрик по серверам

```yaml
- job_name: 'BIKO: postgres_exporter'
  static_configs:
    - targets:
        - 111.111.11.11:9187
        - 222.222.22.22:9187
        - 123.123.11.11:9187
  relabel_configs:
    - source_labels: [__address__]
      regex: '111\.111\.11\.11:9187'
      target_label: instance
      replacement: 'Server1'
    - source_labels: [__address__]
      regex: '222\.222\.22\.22:9187'
      target_label: instance
      replacement: 'Server2'
    - source_labels: [__address__]
      regex: '123\.123\.11\.11:9187'
      target_label: instance
      replacement: 'Server3'
```

7. В Grafana добавить дашборд для мониторинга PostgreSQL (за основу взята схема с портала Grafana и доработана)
