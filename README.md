# 🛡️ Live Incident Response – 4Geeks 2025

> Proyecto final de respuesta a incidentes (Live IR) sobre un servidor Linux comprometido.  
> Enfoque: **inspección en vivo sin apagar el sistema**, contención, erradicación y hardening.

## 🎯 Objetivos
- Identificar la intrusión y documentar hallazgos.
- Contener y erradicar persistencia maliciosa sin perder disponibilidad.
- Restaurar la integridad del sistema y aplicar hardening básico.
- Entregar informe con evidencias y recomendaciones.

## 🧭 Estructura del repositorio
- `docs/` – Informe final, capturas, línea temporal, IOCs y comandos.
- `evidence/` – Hashes de evidencias y (si aplica) muestras anonimizadas.
- `scripts/` – Notas y utilidades (opcional).

## 📝 Resumen ejecutivo
- **Vector inicial**: Fuerza bruta SSH → login exitoso con `sysadmin`.
- **IPs atacante**: `192.168.1.103` (bruteforce), `192.168.1.50` (login), `192.168.1.100` (payload host).
- **Persistencia**: usuarios `reports` (usado) y `hacker` (en espera), cronjob efímero `sys-maintenance`, servicios `vsftpd` y `wazuh-agent`.
- **Artefactos**: `/home/reports/install.sh`, `/home/reports/chat.txt`, `/tmp/secrets.tgz` (contiene `etc/passwd`).
- **Acciones clave**: eliminación de usuarios y cron, borrar payload, purgar `vsftpd` y `wazuh-agent`, endurecer SSH, actualizar paquetes, UFW mínimo, **Fail2ban (opcional)**.

## 📄 Informe
- Informe con capturas

## 🧪 Fases y hallazgos (breve)
1. **Fase 1 – Detección (Live)**  
   - Logs `auth.log`: Accepted/Failed/Invalid → confirmación de fuerza bruta y acceso.  
   - Usuarios anómalos: `reports`, `hacker`.  
   - Servicios: `vsftpd`, `wazuh-agent`.  
   - Artefactos: `install.sh` (descarga payload de 192.168.1.100), `secrets.tgz`.

2. **Fase 2 – Contención & Erradicación**  
   - `userdel -r reports/hacker`.  
   - Eliminar cron `sys-maintenance` y payload en `/tmp/.temp`.  
   - Custodia de evidencia y `sha256sum`.  
   - `apt purge vsftpd wazuh-agent`.

3. **Fase 3 – Recuperación & Hardening**  
   - `apt update && apt upgrade`.  
   - SSH endurecido: sin root login, preferencia por llaves.  
   - UFW: deny incoming, allow outgoing; abrir solo 22/80; limit 22/tcp.  
   - **Opcional**: Fail2ban para SSH (3 intentos → 1h ban).


## 🔐 Recomendaciones
- Mantener el sistema actualizado (parches de seguridad).
- Usar **llaves SSH** y deshabilitar `PermitRootLogin`.
- Rotar credenciales periódicamente.
- Reducir la superficie de ataque (eliminar servicios innecesarios).
- Centralizar logs y monitoreo (SIEM/IDS) cuando aplique.
- Configurar alertas (Fail2ban + correo).
- Realizar auditorías periódicas y pruebas de restauración de backups.

## ⚠️ Ética y Alcance
Todo el material es con fines académicos. No se incluyen datos personales ni secretos reales.  

