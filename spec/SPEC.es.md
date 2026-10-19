# Protocolo de aprobación por lotes para agentes de IA (Plan-Preview-Commit)

**Autor:** Angel Gallego · ORCID 0009-0002-9459-4374
**Versión:** 0.1.0 (borrador) · **Fecha:** 2026-10-18 · **Estado:** borrador autopublicado, sin revisión por pares
**Licencias:** texto bajo CC BY 4.0; esquemas y código bajo Apache-2.0
**Convención de evidencia:** [Con fuente] · [Entrenamiento] (conocimiento general, a verificar) · [Propuesta] · [Hipótesis]
**English version:** [SPEC.en.md](SPEC.en.md)

> **Aviso de estatus.** Esta es una especificación propia, no un estándar de la industria. No incluye validación empírica publicada. Las afirmaciones de novedad se limitan a lo descrito en la sección 10.

---

## 0. Convenciones
Las palabras DEBE, DEBERÍA y PUEDE tienen el sentido habitual de las especificaciones técnicas. Las referencias a terceros se citan como contexto o antecedente; no implican afiliación ni aval.

## 1. Resumen
Los agentes de IA ejecutan acciones con efectos reales sobre los sistemas de una organización. La aprobación humana acción por acción produce fatiga y habituación; la aprobación en bloque sin lectura elimina la supervisión. Este borrador especifica un protocolo en el que el agente propone un lote de acciones, un humano lo revisa como unidad mediante un paquete de vista previa y la ejecución queda ligada de forma verificable a lo aprobado. Define el ciclo de vida del lote, el contenido del paquete, requisitos de granularidad para planes compuestos, un registro de decisiones y requisitos del operador humano con métricas de supervisión. No incluye validación empírica; los antecedentes se declaran en la sección 3.

## 2. Problema y alcance
**2.1 Problema.** La supervisión humana de agentes se degrada con el volumen: la literatura reporta habituación del revisor y fatiga de aprobación (sección 3). Las soluciones comunes aprueban acción por acción o en bloque sobre ítems homogéneos.

**2.2 Qué cubre.** Un protocolo para que un agente proponga un lote de acciones, un humano lo revise como unidad y la ejecución quede ligada de forma verificable a lo aprobado.

**2.3 Qué no cubre.** No es un "sistema operativo organizacional"; ese concepto ya está ocupado por terceros. No sustituye la gestión de identidades, el cumplimiento legal ni un marco jurídico-probatorio. No define el modelo de IA ni el transporte (MCP u otro).

## 3. Trabajo relacionado y posicionamiento
Estado de lectura: **V** = verificado directamente por el autor al redactar; **R** = solo resumen, pendiente de lectura completa.

| Tema | Fuente | Estado | Relación |
|---|---|---|---|
| Marco homónimo de orquestación gobernada (22 esquemas JSON, suite de conformidad) | [AI Orchestration Framework (aof-framework), v1.0 LTS, 5-sep-2026, Apache-2.0](https://github.com/aof-framework/aof) | V | Antecedente en orquestación; no trata el vínculo preview↔commit |
| Aprobación ligada a la acción, con fallo cerrado | [Microsoft Agent Governance Toolkit, ADR 0030](https://github.com/microsoft/agent-governance-toolkit/blob/main/docs/adr/0030-action-bound-approval-protocol.md) | R | Antecedente directo del vínculo verificable (5.4) |
| Aprobaciones pendientes devueltas juntas | [OpenAI Agents SDK, human-in-the-loop](https://openai.github.io/openai-agents-js/guides/human-in-the-loop/); [LangChain HITL](https://docs.langchain.com/oss/python/langchain/human-in-the-loop) | R | Implementan aprobación; no definen el paquete de vista previa |
| Principio "Intent, plan, preview, commit" | [N. Bertino, *AI's affordance problem*](https://www.nicbertino.com/blog/ai-affordance/) | V | Principio de diseño, sin protocolo ni esquema |
| "Plan-preview-commit" como funcionalidad prevista | [TDMCP, hoja de ruta](https://pantani.xyz/tdmcp/roadmap) | V | Descrita como aún no implementada |
| Seudonimización reversible en servidor MCP | [dbi services, feb-2026](https://www.dbi-services.com/blog/rag-mcp-skills-three-paradigms-for-llms-talking-to-your-database-and-why-governance-changes-everything/) | R | Patrón con esquema SQL de referencia |
| Seudonimización reversible, prototipo | [daslabhq/pii-proxy (MIT)](https://github.com/daslabhq/pii-proxy) | V | Prototipo; sin soporte MCP documentado |
| Proxy LLM con enmascarado y desenmascarado | [LiteLLM + Presidio](https://docs.litellm.ai/docs/proxy/guardrails/pii_masking_v2) | R | Implementación disponible |
| Revisión por lotes como mitigación de fatiga | Mitchell et al., [arXiv:2608.23642](https://arxiv.org/abs/2608.23642) | R | Base académica de la revisión por lotes |
| Habituación del revisor | Yu et al., [arXiv:2606.22721](https://arxiv.org/abs/2606.22721); Turan, [arXiv:2606.08919](https://arxiv.org/abs/2606.08919) | R | Fundamento de los requisitos del operador (5.9) |
| Aprobación engañosa ("Loopjacking") | Arun Kumar, [arXiv:2609.21081](https://arxiv.org/abs/2609.21081) | R | Amenaza que motiva el vínculo verificable |
| Trazas para IA generativa | [OpenTelemetry GenAI semantic conventions](https://github.com/open-telemetry/semantic-conventions-genai) | R | Posible base del registro (5.7) |
| Contexto macro "AI(-first) operating system" | [WEF/Kearney, jun-2026](https://www.weforum.org/publications/ai-jp-2097); [McKinsey, oct-2026](https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-ai-operating-system-a-new-way-for-ceos-to-run-the-enterprise) | V | Solo contexto |

## 4. Modelo de referencia en capas
Modelo conceptual en el que el protocolo ocupa la capa de orquestación y depende de las demás.

| Capa | Función | Relación con el protocolo |
|---|---|---|
| 0. Operador humano | Competencias para delegar, revisar y rechazar | Requisitos del aprobador (5.9) |
| 1. Ejecución aislada | Permisos explícitos y aislamiento | Donde se ejecuta el commit |
| 2. Orquestación | Conector de herramientas (p. ej., MCP) y protocolo | **Núcleo de este documento** |
| 3. Memoria y registro | Estado compartido y registro de decisiones | Destino del registro (5.7) |

## 5. Núcleo del protocolo
**5.1 Actores.** Agente proponente · Operador/aprobador · Ejecutor · Registro. La función de proponer DEBE estar separada de la de aprobar.

**5.2 Ciclo de vida del lote.** Plan → Vista previa → Aprobación → Commit → Registro. Estados terminales: rechazado · expirado · invalidado por cambio · ejecutado · ejecutado parcialmente y compensado.

**5.3 Paquete de vista previa (contenido mínimo conceptual).**

| Elemento | Propósito |
|---|---|
| Identificador y versión del lote | Trazabilidad |
| Tipo de lote (homogéneo o compuesto) | Define las reglas de 5.5 |
| Lista ordenada de acciones propuestas | Qué se hará, en lenguaje comprensible |
| Efectos esperados y marca de irreversibles | Informar el riesgo |
| Excepciones resaltadas | Evitar aprobación sin lectura |
| Dependencias entre acciones | Orden de ejecución y semántica de fallo |
| Huella verificable del conjunto | Base del vínculo (5.4) |
| Vigencia | Evitar uso de aprobaciones antiguas |

**5.4 Vínculo verificable entre vista previa y commit.**
El commit DEBE ejecutar exactamente lo aprobado. Cualquier cambio posterior a la aprobación DEBE invalidarla. La aprobación DEBE expirar. Ante fallo o duda, el comportamiento DEBE ser cerrado (no ejecutar). El método de huella, firma y expiración se define en la v0.2. [Hipótesis] Este vínculo ya tiene antecedentes (sección 3); la contribución de este documento es incorporarlo como requisito del protocolo de lotes.

**5.5 Granularidad del lote y planes compuestos.**
- *Lote homogéneo:* ítems independientes del mismo tipo (p. ej., N registros del mismo tipo).
- *Lote compuesto:* acciones con dependencias entre sí (p. ej., crear un elemento, crear otros bajo él y asignarlos).
- El paquete DEBE declarar su tipo. En un lote compuesto, DEBE expresar el grafo de dependencias, el orden de commit y la semántica de fallo (todo o nada, o parcial con compensación).
- La aprobación recae sobre el conjunto. Si el aprobador rechaza o edita ítems, DEBE generarse un paquete nuevo con huella nueva.
- El paquete DEBE resaltar las excepciones: valores atípicos, acciones irreversibles y las de mayor impacto.
- **Niveles de riesgo.** Cada perfil de dominio DEBE poder declarar acciones que nunca se aprueban en lote y exigen aprobación individual. [Con fuente: guías de aprobación citadas en informes de investigación, solo resumen] Hay posturas que desaconsejan la aprobación en bloque para las acciones de mayor riesgo; el protocolo lo admite como configuración.
- *Tamaño máximo:* DEBERÍA existir un límite configurable. No se fija un valor; se determinará con datos.
- [Hipótesis] En los productos revisados predomina la aprobación en bloque de ítems homogéneos; el plan compuesto con dependencias no apareció como caso documentado.

**5.6 Reversión y compensación.** Qué se puede deshacer, qué no y cómo se registra. Se desarrolla en la v0.2.

**5.7 Registro de decisiones.** Campos mínimos conceptuales: identidad del revisor, marca de tiempo, duración de la revisión, decisión (aprobado, editado, rechazado), código de motivo, diferencia entre lo propuesto y lo ejecutado y huella del lote. [Con fuente: guías de aprobación citadas en informes de investigación; pendiente de lectura directa] El esquema formal se publica en la v0.2.

**5.8 Protección de datos (módulo opcional).** Tokenización o seudonimización reversible antes de enviar datos al modelo. Es seudonimización, no anonimización. El mapa de equivalencias es dato sensible y DEBE protegerse (cifrado en reposo y acceso restringido). Las implementaciones deben considerar la normativa de datos personales de su jurisdicción.

**5.9 Requisitos del operador humano (Capa 0).**
- *Competencias mínimas (declaradas, no certificación):* formular el "qué" de una delegación con claridad; leer un paquete de vista previa; identificar excepciones y efectos irreversibles; rechazar o editar con motivo; reconocer el sesgo de automatización y la fatiga.
- *Entrenamiento:* [Propuesta] práctica con paquetes de ejemplo que contengan errores plantados.
- *Calibración continua:* [Propuesta] inyección periódica de paquetes de control con error conocido, para medir la vigilancia real del revisor.
- *Métricas de supervisión (definiciones):*
  - tasa de rechazo y de edición;
  - tiempo de revisión por ítem y por lote;
  - tasa de detección de errores plantados;
  - deriva por habituación: variación de la tasa de detección a lo largo de una sesión o de varias;
  - carga: número de lotes e ítems por sesión.
- *Alertas:* revisiones demasiado rápidas respecto del tamaño del lote; sesiones prolongadas.
- *Umbrales:* no se fijan. Se determinarán empíricamente.
- *Alcance:* no certifica personas; es un requisito de proceso.
- *Uso de las métricas:* sirven para calibrar el sistema y el entrenamiento, no para evaluar a las personas. En cualquier estudio se reportan agregadas y anonimizadas, con consentimiento informado y sin identificación individual.
- [Hipótesis] En las búsquedas realizadas no se halló un marco que trate estos requisitos como componente normativo de una arquitectura operativa; no es prueba de que no exista.

## 6. Conformidad
Niveles de conformidad y pruebas mínimas: en desarrollo. Deben definir qué demuestra una implementación para declararse conforme.

## 7. Perfiles de dominio (anexos informativos)
Cada perfil define los objetos del dominio y los riesgos propios, sin cambiar el núcleo. **Estado: planificados; sin evidencia de uso.**

**7.1 Oficinas de gestión de proyectos.** Objetos: proyecto, módulo, tarea, cronograma. Riesgos: alteración masiva de planes, asignaciones erróneas, inconsistencias de fechas.
**7.2 Agencias de marketing.** Objetos: campaña, pieza, calendario, publicación, presupuesto. Riesgos: publicaciones irreversibles, gasto publicitario, coherencia de marca.
**7.3 Plantilla para nuevos perfiles.** Objetos · acciones permitidas · acciones irreversibles · acciones que exigen aprobación individual · campos adicionales del paquete · métricas del operador.

## 8. Modelo de amenazas
Aprobación engañosa (lo mostrado difiere de lo ejecutado) · habituación del revisor · aprobación en bloque sin lectura · reutilización de aprobaciones expiradas · fuga del mapa de seudónimos · inyección de instrucciones en el contenido del lote. Las mitigaciones por amenaza se desarrollan en la v0.2.

## 9. Validación empírica
**Estado actual: sin resultados publicados.** Diseño previsto:
- Entorno local reproducible con datos sintéticos generados con semilla fija y publicados.
- Línea base manual frente al protocolo.
- Varias corridas por condición, con intervalos de confianza.
- Errores plantados para medir la vigilancia del operador.
- Resultados, configuración de modelos y registros crudos se publicarán juntos.
- Estudios con personas: participación voluntaria con consentimiento informado y retiro sin consecuencias; datos anonimizados y reporte agregado; además de las métricas de proceso, medidas de percepción (fricción, carga, confianza). No son evaluativos.

## 10. Limitaciones y declaración de novedad acotada
Formulación permitida: "En las búsquedas realizadas no se encontró una publicación previa que integre estos elementos". No se afirma que los componentes individuales sean nuevos, que no existan antecedentes ni que haya casos aplicados en organizaciones. Varias fuentes de la sección 3 se leyeron solo en resumen y las búsquedas no fueron exhaustivas.

## 11. Gobernanza del proyecto
Versionado semántico · licencias (texto CC BY 4.0; esquemas y código Apache-2.0) · contribuciones mediante issues · cita mediante `CITATION.cff` · el proyecto usa este nombre descriptivo y evita siglas.

## 12. Hoja de ruta
- [ ] v0.2: método de huella y expiración (5.4); esquemas JSON del paquete y del registro.
- [ ] v0.2: selección y validación de instrumentos de percepción (fricción, carga, confianza, emociones) para estudios con personas.
- [ ] Lectura completa de las fuentes marcadas R y BibTeX.
- [ ] Niveles de conformidad y pruebas.
- [ ] Mitigaciones del modelo de amenazas.
- [ ] Sandbox reproducible con datos sintéticos y resultados medidos.
- [ ] Perfiles de dominio: de borrador a evidencia.
- [ ] Implementación de referencia, sandbox y evidencia en repositorios aparte, enlazados desde aquí.
- [ ] Revisión externa.

## 13. Referencias
Las fuentes se enlazan en la sección 3. Pendiente: lista BibTeX con las fuentes leídas completas.
