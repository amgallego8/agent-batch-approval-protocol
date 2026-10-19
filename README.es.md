# Protocolo de aprobación por lotes para agentes de IA (Plan-Preview-Commit)

> **Estado: borrador v0.1.0.** Esqueleto de especificación. Es una especificación independiente y autopublicada: **no es un estándar de la industria**, **no ha sido revisada por pares** y **aún no incluye validación empírica publicada**.

English: [README.md](README.md)

## Qué es
Una especificación abierta sobre cómo un humano revisa y aprueba un **lote** de acciones propuestas por un agente de IA, y cómo la ejecución queda **ligada de forma verificable** a exactamente lo que se aprobó.

## Por qué
- Aprobar cada acción del agente una por una produce fatiga y habituación.
- Aprobar en bloque sin leer elimina la supervisión real.
- Esta especificación define una vía intermedia: el agente propone un lote, un humano lo revisa como unidad mediante un paquete de vista previa, y el commit solo puede ejecutar lo aprobado.

## Qué cubre
- Ciclo de vida del lote: Plan → Vista previa → Aprobación → Commit → Registro.
- Contenido del paquete de vista previa.
- Vínculo verificable entre vista previa y commit (requisitos; método previsto para la v0.2).
- Granularidad del lote, incluidos los planes compuestos con dependencias.
- Registro de decisiones (ledger), módulo opcional de seudonimización reversible y requisitos del operador humano con métricas de supervisión.
- Perfiles de dominio informativos (oficinas de gestión de proyectos, agencias de marketing): planificados, sin evidencia de uso.

## Qué no cubre
- No es un "sistema operativo organizacional".
- No sustituye la gestión de identidades, el cumplimiento legal ni un marco jurídico-probatorio.
- No define el modelo de IA ni el transporte (MCP u otros).

## Contenido del repositorio
| Ruta | Contenido |
|---|---|
| `spec/SPEC.es.md` | Especificación (español) |
| `spec/SPEC.en.md` | Specification (English) |
| `LICENSE` | Apache-2.0 (esquemas y código) |
| `LICENSE-docs.md` | Aviso CC BY 4.0 (texto) |
| `CITATION.cff` | Metadatos de cita |

## Trabajo relacionado y alcance de la novedad
Existen antecedentes de cada componente por separado (aprobación humana en SDK de agentes, proxies de seudonimización reversible, registros de decisiones encadenados, aprobación ligada a la acción). Ver la sección 3 de la especificación. La única afirmación es acotada: **en las búsquedas realizadas no se encontró una publicación previa que integre estos elementos.** La ausencia de hallazgos no prueba que no exista.

## Hoja de ruta
- v0.2: método de vínculo y expiración; esquemas JSON del paquete de vista previa y del registro de decisiones.
- Lectura completa de las fuentes marcadas "solo resumen"; BibTeX.
- Niveles de conformidad y pruebas.
- Sandbox reproducible con datos sintéticos y resultados medidos.
- Perfiles de dominio: de borrador a evidencia.
- La implementación de referencia, el sandbox y la evidencia vivirán en repositorios aparte, enlazados desde aquí.

## Licencia
- Esquemas y código: [Apache-2.0](LICENSE).
- Texto y documentación: [CC BY 4.0](LICENSE-docs.md).

## Cómo citar
Ver [CITATION.cff](CITATION.cff). Se añadirá un DOI tras el primer release archivado.

## Autor
Angel Gallego — ORCID [0009-0002-9459-4374](https://orcid.org/0009-0002-9459-4374)

## Contribuir
Se aceptan issues y discusión: terminología, antecedentes omitidos, escenarios de amenaza, propuestas de perfiles de dominio.

## Asistencia de IA
Claude (Anthropic) actuó como copiloto de IA del autor en la investigación, la redacción y la revisión. El autor revisó y aprobó el contenido y es el único responsable de él.

## Aviso
No es asesoría legal. Los proyectos y organizaciones de terceros se citan como contexto o antecedente; sus nombres pertenecen a sus titulares y no se implica afiliación ni aval.
