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
│   └── common/
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

ansible-playbook playbooks/site.yml
```

---

## Статус

- [x] SSH
- [x] Git
- [x] Ansible
- [ ] OpenTofu
- [ ] Systemd deployment
- [ ] Docker
- [ ] Kubernetes
