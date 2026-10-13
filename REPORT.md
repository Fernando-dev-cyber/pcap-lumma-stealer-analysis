# Network Security Incident Report: Lumma Stealer Compromise

**Fecha de Análisis:** 05-Oct-2026  
**Analista:** Fernando  
**Fuente de Evidencia:** Malware-Traffic-Analysis.net (`2026-01-31-traffic-analysis-exercise.pcap`)  
**Severidad:** Crítica  
**Estado:** Confirmado / Requiere Contención  

---

### 1. Resumen Ejecutivo (Executive Summary)
Durante las actividades de monitoreo continuo del SOC, se atendió una alerta de firma emergente (*ET MALWARE Lumma Stealer Victim Fingerprinting Activity*) vinculada con tráfico saliente hacia la IP externa `153.92.1.49` a través del puerto TCP 80. La inspección forense del tráfico de red confirmó el compromiso activo del host Windows identificado internamente como `DESKTOP-ES9F3ML`, evidenciando comunicación persistente de Command and Control (C2) con infraestructura maliciosa.

---

### 2. Identificación del Endpoint Comprometido
| Atributo | Valor Observado | Protocolo / Método de Extracción |
| :--- | :--- | :--- |
| **Dirección IP** | `10.1.21.58` | Filtrado IP conversación C2 |
| **Dirección MAC** | `00:21:5d:c8:0e:f2` | Cabecera Ethernet II en tráfico saliente |
| **Hostname** | `DESKTOP-ES9F3ML` | NetBIOS Name Service (`nbns`) / DHCP Option 12 |
| **Cuenta de Usuario** | `gwyatt` | Negociación Kerberos / SAMR DCE/RPC |
| **Nombre Completo** | `Gabriel Wyatt` | Respuesta SAMR `QueryUserInfo response` (SMB2) |
| **Dominio AD** | `win11office.com` | Parámetros de red Active Directory |

---

### 3. Evidencia de Red y Cadena de Ataque (Timeline & Findings)
1. **Identificación de Host:** El cliente `10.1.21.58` se autenticó en el controlador de dominio `WIN-LU4L24X3UB7` (`10.1.21.2`), registrando el usuario corporativo `gwyatt` (Gabriel Wyatt).
2. **Conexión de Comando y Control (C2):** 
   - La víctima inició solicitudes HTTP hacia la IP externa `153.92.1.49` sobre el puerto TCP 80.
   - En el encabezado HTTP se identificó la resolución del dominio de mando malicioso: `whitepeper.su`.
   - El payload/comportamiento coincide con la fase de fingerprinting y exfiltración de telemetría característica de la familia *Lumma Stealer*.

---

### 4. Filtros Forenses Utilizados - (Wireshark)
- `ip.addr == 153.92.1.49`: Aislamiento de la conversación entre el endpoint y el servidor de mando.
- `nbns or dhcp`: Correlación del nombre de equipo (`DESKTOP-ES9F3ML`) en la capa local.
- `samr.user_name or samr.full_name`: Extracción forense del usuario corporativo y nombre real vía RPC/SMB2.
- `http.host and ip.addr == 153.92.1.49`: Confirmación del dominio C2 en texto plano.

---

### 5. Indicadores de Compromiso (IOCs)
- **Dominio C2:** `whitepeper[.]su`
- **IP C2:** `153.92.1[.]49`
- **Host Afectado:** `DESKTOP-ES9F3ML` (`10.1.21.58`)
- **Cuenta Comprometida:** `gwyatt` (`Gabriel Wyatt`)

---

### 6. Acciones Inmediatas de Mitigación y Contención
1. **Aislamiento de Red:** Desconectar de inmediato la estación `10.1.21.58` (MAC: `00:21:5d:c8:0e:f2`) de la VLAN de oficina para evitar movimientos laterales hacia el controlador de dominio.
2. **Bloqueo Perimetral:** Ingresar regla de bloqueo en Firewall y DNS Sinkhole para `153.92.1.49` y `whitepeper[.]su`.
3. **Gestión de Identidades:** Forzar revocación de sesiones activas, invalidación de tokens Kerberos y reseteo de contraseña para la cuenta `gwyatt`.
4. **Forense de Endpoint:** Ejecutar triage en vivo para extraer procesos en memoria y persistencias dejadas por el ejecutable de Lumma Stealer.