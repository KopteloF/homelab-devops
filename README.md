# Homelab DevOps

Локальный стенд для изучения DevOps-инструментов и автоматизации инфраструктуры.

## Цель проекта

Изучение практик:

- Linux
- Git
- SSH
- Ansible
- OpenTofu (Terraform)
- Docker
- Systemd
- Kubernetes (на следующем этапе)

---

## Архитектура

```
Windows 11
      │
VS Code Remote SSH
      │
+----------------+
|    bastion     |
+----------------+
   │         │
SSH│         │SSH
   │         │
+--------+ +--------+
| app01  | | db01   |
+--------+ +--------+
```

---

## Виртуальные машины

| Имя | Назначение |
|------|------------|
| bastion | Управление инфраструктурой |
| app01 | Сервер приложения |
| db01 | Сервер базы данных |

---

## Структура проекта

```
ansible/
├── inventory/
├── playbooks/
├── roles/
│   ├── common/
│   └── hardening/     # SSH, UFW, timezone, sysctl
├── group_vars/
├── requirements.yml   # ansible-galaxy collections
└── ansible.cfg
```

---

## Проверка подключения

```bash
ansible all -m ping
```

---

## Запуск

```bash
cd ansible

# один раз на bastion
ansible-galaxy collection install -r requirements.yml

ansible-playbook playbooks/site.yml
```

Перед первым hardening: **проверь IP** в `inventory/hosts` (host-only подсеть) и что вход по SSH-ключу работает без пароля.

---

## Статус

- [x] SSH
- [x] Git
- [x] Ansible
- [x] Hardening (роль: ssh + ufw + timezone + sysctl)
- [ ] OpenTofu
- [ ] Systemd deployment
- [ ] Docker
- [ ] Kubernetes
