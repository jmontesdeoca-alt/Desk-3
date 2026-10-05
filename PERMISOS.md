# Matriz de permisos

Generada con `npm run permisos` (en `server/`) a partir de `server/policies.json` y de las reglas heredadas de los esquemas.
Es lo que el **servidor** aplica; el menú de la interfaz solo oculta lo que no corresponde.

**Cómo leerla.** Cada celda son 4 letras, en este orden: **L**eer · **C**rear · **E**ditar · **B**orrar.

| Símbolo | Significado |
|---|---|
| `L` mayúscula | permitido sobre **todos** los registros |
| `l` minúscula | permitido solo sobre los **propios o de su estructura** (ver abajo) |
| `-` | no permitido |

Ejemplo: `Lc--` = ve todo, crea solo lo propio, no edita ni borra.

**Qué significa "propio" por entidad**
- Pólizas, comisiones, liquidaciones, metas: las del agente vinculado al usuario (y su estructura si el plan la incluye).
- Pagos, renovaciones, documentos: los de las pólizas de ese agente/estructura.
- Clientes: los que el usuario creó o los que tienen una póliza de su agente/estructura.
- CRM (prospectos, leads, actividades): los asignados al usuario.
- Notificaciones y mensajes: los dirigidos al usuario.

**Gerencial y jefatura** se muestran como si estuvieran vinculados a un agente (ven solo su estructura, por eso la `l` minúscula en lo que depende del agente). Sin ese vínculo ven **todo** (`L`).

**Estructura (upline).** Se ve hacia abajo, nunca hacia arriba: cada agente ve lo suyo y lo de todos sus subagentes. En el Nivel 1 (sin estructura) solo lo suyo.

**Seguridad por defecto.** Lo que no tenga regla (ni en esta matriz ni heredada) queda **solo para administradores**.
Los secretos de MFA y los tokens de Google nunca salen del servidor, ni siquiera para un administrador.

## Portal

| Entidad | admin | gerencial | jefatura | operativo | financiero | agente | gestor_ventas |
|---|---|---|---|---|---|---|---|
| AdditionalInfoRequest *(heredada)* | `LCEB` | `L---` | `----` | `LCE-` | `----` | `----` | `----` |
| Agent | `LCEB` | `l---` | `lCE-` | `L---` | `L---` | `l---` | `----` |
| AgentBadge | `LCEB` | `l---` | `l---` | `L---` | `L---` | `l---` | `----` |
| AgentCommissionAssignment | `LCEB` | `l---` | `lCEB` | `----` | `L---` | `l---` | `----` |
| AgentCost | `LCEB` | `l---` | `----` | `----` | `----` | `----` | `----` |
| AgentNegativeBalance | `LCEB` | `l---` | `l---` | `----` | `LCE-` | `l---` | `----` |
| AppConfig | `LCEB` | `L---` | `L---` | `L---` | `L---` | `L---` | `L---` |
| AppSettings *(heredada)* | `LCEB` | `L---` | `L---` | `L---` | `L---` | `L---` | `L---` |
| AuditLog *(heredada)* | `LCEB` | `-C--` | `-C--` | `-C--` | `-C--` | `-C--` | `-C--` |
| AzureDocument | `LCEB` | `l---` | `lCE-` | `LCEB` | `L---` | `l---` | `----` |
| Badge | `LCEB` | `L---` | `L---` | `L---` | `L---` | `L---` | `L---` |
| Branch *(heredada)* | `LCEB` | `L---` | `L---` | `L---` | `L---` | `L---` | `L---` |
| BrandSettings | `LCEB` | `L---` | `L---` | `L---` | `L---` | `L---` | `L---` |
| Claim *(heredada)* | `LCEB` | `L---` | `L---` | `LCE-` | `----` | `----` | `----` |
| Client | `LCEB` | `l---` | `lCE-` | `LCE-` | `L---` | `l---` | `----` |
| Commission | `LCEB` | `l---` | `l---` | `----` | `LCE-` | `l---` | `----` |
| CommissionSettlement | `LCEB` | `l---` | `l---` | `----` | `LCE-` | `l---` | `----` |
| CommissionTable | `LCEB` | `L---` | `L---` | `L---` | `L---` | `----` | `----` |
| CompanyBudget *(heredada)* | `LCEB` | `L---` | `----` | `----` | `----` | `----` | `----` |
| CorporateInvoice *(heredada)* | `LCEB` | `----` | `----` | `LCE-` | `LCE-` | `----` | `----` |
| CorporateMember *(heredada)* | `LCEB` | `----` | `----` | `LCE-` | `----` | `----` | `----` |
| CorporateRatePlan *(heredada)* | `LCEB` | `----` | `----` | `LCE-` | `L---` | `----` | `----` |
| Dependent | `LCEB` | `l---` | `lCE-` | `LCE-` | `L---` | `l---` | `----` |
| Document | `LCEB` | `----` | `L---` | `LCEB` | `L---` | `----` | `----` |
| EmailLog *(heredada)* | `LCEB` | `-C--` | `-C--` | `-C--` | `-C--` | `-C--` | `-C--` |
| Endorsement | `LCEB` | `l---` | `l---` | `LCE-` | `L---` | `l---` | `----` |
| ExchangeRate | `LCEB` | `L---` | `L---` | `L---` | `LCE-` | `L---` | `L---` |
| Incentive *(heredada)* | `LCEB` | `L---` | `L---` | `L---` | `L---` | `L---` | `L---` |
| InsuranceCompany *(heredada)* | `LCEB` | `L---` | `L---` | `L---` | `L---` | `L---` | `L---` |
| InsuranceLine *(heredada)* | `LCEB` | `L---` | `L---` | `L---` | `L---` | `L---` | `L---` |
| Message | `lCeb` | `lCeb` | `lCeb` | `lCeb` | `lCeb` | `lCeb` | `lCeb` |
| Notification | `LCEB` | `lCeb` | `lCeb` | `lCeb` | `lCeb` | `lCeb` | `lCeb` |
| Payment | `LCEB` | `l---` | `l---` | `LCE-` | `LCE-` | `l---` | `----` |
| Policy | `LCEB` | `l---` | `lCE-` | `LCE-` | `L---` | `l---` | `----` |
| PolicyTag *(heredada)* | `LCEB` | `L---` | `L---` | `L---` | `L---` | `L---` | `L---` |
| Product *(heredada)* | `LCEB` | `L---` | `L---` | `L---` | `L---` | `L---` | `L---` |
| Renewal | `LCEB` | `l---` | `lCE-` | `LCE-` | `L---` | `l---` | `----` |
| ReportTemplate *(heredada)* | `LCeb` | `LCeb` | `LCeb` | `LCeb` | `LCeb` | `LCeb` | `LCeb` |
| SalesTarget | `LCEB` | `lCEB` | `l---` | `----` | `----` | `l---` | `----` |
| Task *(heredada)* | `LCEB` | `LCe-` | `LCe-` | `lCe-` | `lCe-` | `lCe-` | `LCE-` |
| User | `LCEB` | `L-e-` | `L-e-` | `L-e-` | `L-e-` | `L-e-` | `L-e-` |
| UserRole | `LCEB` | `l-e-` | `l-e-` | `l-e-` | `l-e-` | `l-e-` | `l-e-` |

## CRM

Los roles del CRM se derivan del rol del usuario (admin → admin_crm, gerencial/jefatura → jefatura_crm, agente → agente_crm)
y exigen además que el usuario tenga el acceso al CRM activado. Meta y WhatsApp: solo administradores.

| Entidad | admin_crm | jefatura_crm | agente_crm |
|---|---|---|---|
| AgentAvailability | `LCEB` | `LCEB` | `lceb` |
| AgentGoal | `LCEB` | `LCEB` | `l---` |
| AppointmentRequest | `LCEB` | `LCEB` | `lceb` |
| AppointmentSlot | `LCEB` | `LCEB` | `lceb` |
| AutomationLog | `LCEB` | `LCEB` | `----` |
| AutomationRule | `LCEB` | `LCEB` | `----` |
| CalendarEvent | `LCEB` | `LCEB` | `lceb` |
| EmailOpenTracking | `LCEB` | `LCE-` | `lC--` |
| EmailTemplate | `LCEB` | `LCEB` | `L---` |
| EmailTracking | `LCEB` | `LCE-` | `lC--` |
| FollowUpReminder | `LCEB` | `LCEB` | `lceb` |
| ImportHistory | `LCEB` | `LCEB` | `----` |
| Lead | `LCEB` | `LCEB` | `lce-` |
| LeadActivity | `LCEB` | `LCEB` | `lceb` |
| LeadAssignmentNotification | `LCEB` | `LCEB` | `l-e-` |
| MetaEventLog | `LCEB` | `----` | `----` |
| MetaIntegrationConfig | `LCEB` | `----` | `----` |
| MetaLead | `LCEB` | `----` | `----` |
| MetaLeadAdConfig | `LCEB` | `----` | `----` |
| Pipeline | `LCEB` | `L---` | `l---` |
| Prospect | `LCEB` | `LCEB` | `lce-` |
| RadarConfig | `LCEB` | `LCEB` | `----` |
| RadarRecommendation | `LCEB` | `LCEB` | `l-e-` |
| RoundRobinQueue | `LCEB` | `LCEB` | `----` |
| Tag | `LCEB` | `LCEB` | `LC--` |
| UserCalendarConnection | `LCEB` | `LCEB` | `lceb` |
| WebForm | `LCEB` | `LCE-` | `L---` |
| WhatsAppConfig | `LCEB` | `----` | `----` |
| WorkflowAutomation | `LCEB` | `LCEB` | `----` |
| WorkflowExecution | `LCEB` | `LCEB` | `----` |
