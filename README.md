# Homelab DevOps

Локальный стенд для изучения DevOps: **3 VM Ubuntu Server**, управление с **Linux-bastion** (не с Windows).

## Цель

| Этап | Технологии |
|------|------------|
| Сейчас | Linux, Git, SSH, Ansible, systemd, hardening |
| Далее | OpenTofu (создание VM — обсудить с ментором) |
| Потом | Docker (только запуск), Kubernetes |

---

## Архитектура (3 VM)

```
                    Internet
                        |
              Bridged Adapter (NAT)
          +-----------+-----------+-----------+
          |           |           |           |
       bastion      app01        db01      (Windows host)
     control node   compute      base
          |           |           |
          +----- Host-Only 192.168.56.0/24 -----+
                (SSH Ansible, app → PostgreSQL)
```

| VM | Роль | Что на ней |
|----|------|------------|
| **bastion** | control node | Git, Ansible, OpenTofu (позже). **Сюда** VS Code Remote SSH |
| **app01** | compute | Python-приложение через **systemd** (`homelab-app.service`) |
| **db01** | base | **PostgreSQL**, слушает только host-only IP |

### Сеть (2 NIC на каждой VM)

| Интерфейс | Тип в VirtualBox | Зачем |
|-----------|------------------|--------|
| `enp0s3` (пример) | **Bridged** | Интернет (apt, git) |
| `enp0s8` (пример) | **Host-only** | Связь между VM (`192.168.56.0/24`) |

> Имена интерфейсов проверь: `ip -br a`. В inventory указываем **host-only IP** как `ansible_host`.

---

## Структура проекта

```
homelab-devops/
├── apps/demo-app/          # готовое приложение (stdlib, без правок)
├── ansible/
│   ├── inventory/
│   │   ├── hosts
│   │   └── group_vars/   # рядом с inventory (Ansible подхватывает отсюда)
│   ├── playbooks/site.yml
│   ├── requirements.yml    # ansible-galaxy collections
│   └── roles/
│       ├── common/         # базовые пакеты
│       ├── hardening/      # SSH, UFW, timezone, sysctl
│       ├── postgresql/     # БД на db01
│       └── app/            # systemd на app01
└── README.md
```

---

## Быстрый старт (на bastion)

```bash
git clone https://github.com/KopteloF/homelab-devops.git
cd homelab-devops
git checkout feature/homelab-stack   # или main после merge

# 1) Подставь свои IP и пользователя в ansible/inventory/hosts
nano ansible/inventory/hosts

# 2) SSH по ключам без пароля
ssh app01 'hostname'
ssh db01  'hostname'

# 3) Коллекции Ansible (один раз)
cd ansible
ansible-galaxy collection install -r requirements.yml

# 4) Dry-run
ansible servers -m ping
ansible-playbook playbooks/site.yml --check --diff \
  -e postgres_password='CHANGE_ME_STRONG'

# 5) Прогон
ansible-playbook playbooks/site.yml \
  -e postgres_password='CHANGE_ME_STRONG'
```

---

## Проверка после прогона

```bash
# Приложение (systemd)
ssh app01 'systemctl status homelab-app --no-pager'
curl http://192.168.56.12:8000/health    # host-only IP app01 (подставь свой)

# PostgreSQL (только с app01)
ssh db01 "sudo -u postgres psql -c '\\l'"

# Hardening
ssh app01 'sudo ufw status numbered'
ssh db01  'sudo ufw status numbered'
```

---

## Git-воркфлоу

- `feature/hardening` — SSH + UFW
- `feature/homelab-stack` — PostgreSQL + app + systemd + схема сети
- Merge через **Pull Request** в `main`

---

## Статус

- [x] SSH, Git, Ansible (3 VM)
- [x] Hardening (роль)
- [x] Сетевая схема (README)
- [x] PostgreSQL на db01 (Ansible)
- [x] Приложение через systemd на app01
- [ ] OpenTofu — **вопрос на созвоне** (VirtualBox на Windows vs bastion)
- [ ] Docker
- [ ] Kubernetes

---

## OpenTofu (отложено до созвона)

> «Стенд готов руками. Хочу заменить создание VM на OpenTofu, но VirtualBox на Windows,
> а Ansible — с Linux-bastion. Какой способ ты рекомендуешь: provider VirtualBox,
> libvirt/KVM, Vagrant?»
