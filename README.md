

#  Monitoring & Log Collection — G5 Games Test Project

##  Описание

Система мониторинга и сбора логов, разработанная в рамках тестового задания G5 Games.

### Компоненты:
- **Grafana** (визуализация метрик)
- **Prometheus** (или VictoriaMetrics, по выбору)
- **Node Exporter** (базовые метрики хоста)
- **Filebeat** (агрегатор логов контейнеров)

---

## Как развернуть

###  Требования

- Ubuntu 24.04
- Docker & Docker Compose
- Ansible

---

### Шаги установки

#### 1. Клонируйте репозиторий

```bash
git clone https://github.com/<your-username>/test-monitoring-g5.git
cd test-monitoring-g5
````

#### 2. Поднимите мониторинг

```bash
docker-compose up -d
```

Проверьте:

* Prometheus: [http://localhost:9090](http://localhost:9090)
* Grafana: [http://localhost](http://localhost)

  * Логин: `admin`
  * Пароль: `admin`

---

### Установка Filebeat (через Ansible)

>  Выполняется на **хост-системе Ubuntu**, где работает Docker (не внутри контейнера).

#### 1. Настройте `inventory.ini`

Создайте файл `ansible/inventory.ini`:

```ini
[all]
localhost ansible_connection=local
```

#### 2. Установите log-коллектор:

```bash
cd ansible
ansible-playbook -i inventory.ini install_log_collector.yml
```

---

##  Логи

После запуска:

* Все логи контейнеров сохраняются в:

  ```bash
  /var/log/test/containers.log
  ```
* Пример строки:

  ```
  {"container":{"name":"grafana"},"message":"Update check succeeded"}
  ```

---

##  Метрики

Prometheus собирает метрики от:

* самого себя (`prometheus`)
* node\_exporter

 Метрики можно смотреть в **Grafana > Explore** или создать Dashboard

Примеры метрик:

* `up{job="prometheus"}`
* `node_cpu_seconds_total`
* `node_memory_MemAvailable_bytes`

---

##  Тестирование на macOS (dev-only)

На macOS используется дополнительный контейнер `filebeat`, собирающий логи Docker-контейнеров изнутри:

```bash
docker-compose up -d --build
tail -f filebeat/output/containers.log
```

 В продакшн-версии используется systemd + Ansible (см. выше)

---

##  Автоматизация (опционально)

*Здесь можно добавить `scripts/deploy_all.sh`, если решишь писать*

---

##  Проверка

На целевой Ubuntu 24.04:

* Grafana будет доступна на `http://<ip>:80` (если переопределить порт в docker-compose)
* В `/var/log/test` должны быть логи от контейнеров
* В Grafana должны отображаться метрики

---

## Автор

Тимур Овчинников
[ovchinnikov.timvic@gmail.com](mailto:ovchinnikov.timvic@gmail.com)

---


