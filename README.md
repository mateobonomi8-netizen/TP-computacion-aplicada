# Trabajo Práctico Integrador – Computación Aplicada
Universidad de Palermo – 2025

## Integrantes del grupo
- Mateo Bonomi
- Giovanni Feruglio
- Santiago Valle
- Candela Sánchez
- Rozo Cruz
- Nahuel Matías Frutos

---

## 1. Configuración del entorno

- Sistema operativo: **Debian GNU/Linux 11 (Bullseye)**
- Hostname configurado: **TPServer**
- Contraseña del usuario root establecida según consigna: **palermo**
- Acceso root habilitado mediante autenticación SSH con **clave pública** provista por la cátedra.
- Claves utilizadas:
  - Clave privada: `clave_privada.txt`
  - Clave pública: `clave_publica.pub`

---

## 2. Servicios instalados y configurados

### SSH
- Servicio **OpenSSH Server** instalado y habilitado.
- Directorio `/root/.ssh` creado con permisos correctos (700).
- Clave pública provista por la cátedra copiada a `/root/.ssh/authorized_keys`.
- Permisos de `authorized_keys` ajustados a **600**.
- Archivo `/etc/ssh/sshd_config` configurado para permitir:
  - `PermitRootLogin yes`
  - `PubkeyAuthentication yes`
- Conectividad verificada desde Windows mediante:
  ```
  ssh -i clave_privada.txt root@192.168.56.10
  ```

---

### Servidor Web (Apache + PHP)

- Apache2 instalado desde repositorios oficiales de Debian.
- PHP 7.4 y módulos necesarios instalados (`php7.4-mysql`, `php7.4-xml`, `php7.4-mbstring`, etc.).
- Directorio `/www_dir` creado y configurado como raíz del sitio.
- Archivos requeridos:
  - `index.php`
  - `logo.png`
  - `db.sql`
- VirtualHost creado en `/etc/apache2/sites-available/tp.conf`.
- Se reemplazó `000-default.conf`:
  ```
  a2dissite 000-default.conf
  a2ensite tp.conf
  systemctl reload apache2
  ```
- Verificación: `http://192.168.56.10`

---

### Base de Datos (MariaDB)

- Instalación mediante:
  ```
  apt install mariadb-server -y
  ```
- Base de datos importada:
  ```
  mysql -u root < /www_dir/db.sql
  ```
- Conexión correcta desde el sitio PHP.

---

## 3. Configuración de Red

- NAT + Host-Only en VirtualBox.
- Interfaz estática: **enp0s3**
- Configuración en `/etc/network/interfaces`:
  ```
  auto enp0s3
  iface enp0s3 inet static
  address 192.168.56.10
  netmask 255.255.255.0
  gateway 192.168.56.1
  ```

---

## 4. Almacenamiento

- Disco adicional de **10 GB**.
- Particiones:
  - `/dev/sdc1` – 3 GB → `/www_dir`
  - `/dev/sdc2` – 6 GB → `/backup_dir`
- Formateo:
  ```
  mkfs.ext4 /dev/sdc1
  mkfs.ext4 /dev/sdc2
  ```
- Montaje en `/etc/fstab`:
  ```
  /dev/sdc1 /www_dir ext4 defaults 0 0
  /dev/sdc2 /backup_dir ext4 defaults 0 0
  ```
- Archivo persistente:
  ```
  mkdir /proc_dir
  cat /proc/partitions > /proc_dir/particion
  ```

---

## 5. Script de Backup

**Ubicación:** `/opt/scripts/backup_full.sh`

- Recibe ORIGEN y DESTINO.
- Verifica existencia y punto de montaje.
- Incluye `-help`.
- Genera archivos `.tar.gz` con fecha ANSI.
- Ejemplo:
  ```
  /opt/scripts/backup_full.sh /var/log /backup_dir
  ```
- Archivo generado:
  `log_bkp_20251114.tar.gz`

---

## 6. Tareas automáticas (cron)

```
0 0 * * * /opt/scripts/backup_full.sh /var/log /backup_dir
0 23 * * 1,3,5 /opt/scripts/backup_full.sh /www_dir /backup_dir
```

---

## 7. Archivos comprimidos para entrega

| Archivo | Contenido |
|---------|-----------|
| `root.tar.gz` | /root |
| `etc.tar.gz` | /etc |
| `opt.tar.gz` | /opt |
| `proc_dir.tar.gz` | /proc_dir |
| `www_dir.tar.gz` | /www_dir |
| `backup_dir.tar.gz` | /backup_dir |
| `var.tar.gz.part-aa` `ab` `ac` | /var dividido |
| `clave_publica.pub` | Clave SSH |
| `clave_privada.txt` | Clave privada |

---

## 8. Diagrama topológico
![Diagrama Topológico](./diagrama_topologico.jpg)

---

## 9. Estado final

- Todos los servicios funcionan.
- Acceso SSH correcto.
- Sitio web funcional y conectado a MariaDB.
- Particiones montadas correctamente.
- Backups manuales y automáticos funcionando.
- Archivos comprimidos generados según consigna.

---

Universidad de Palermo – Computación Aplicada (2025)  
Grupo: Bonomi, Feruglio, Valle, Sánchez, Rozo Cruz, Frutos
