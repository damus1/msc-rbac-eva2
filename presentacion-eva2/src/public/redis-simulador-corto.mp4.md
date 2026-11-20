<div class="flex-between items-center mb-1">
  <div class="eyebrow">Bloque 5 · Datos</div>
  <span class="chip bg-slate-100 text-xs font-semibold text-ink-soft border border-slate-200"><div class="i-mdi-database-outline mr-1 text-brand-red" /> Modelo relacional</span>
</div>

# <span class="i-mdi-database-cog-outline align-middle mr-2 text-brand-navy" /> Datos: qué guardamos y qué reglas cumplen

<div class="grid grid-cols-[1.1fr_1fr] gap-4 mt-2">

<div class="card p-3">

```mermaid
flowchart LR
  classDef red fill:#FEE2E2,stroke:#E11927,stroke-width:2px,color:#991B1B;
  classDef navy fill:#E0E7FF,stroke:#0A2540,stroke-width:2px,color:#0A2540;
  classDef dark fill:#1E293B,stroke:#0F172A,stroke-width:2px,color:#FFFFFF;
  P["Colaborador<br/>(desde AD)"]:::navy --> CU["Cuenta<br/>(clave en hash)<br/>pendiente · activa ·<br/>bloqueada · desactivada"]:::navy
  P --> A["Perfil asignado<br/>en una versión"]:::red
  P --> E["Excepción<br/>con vencimiento"]:::red
  AP["Aplicación<br/>(catálogo de las 37)"]:::navy --> PE
  A --> PE[("Permisos<br/>listos")]:::dark
  E --> PE
  PE --> B[("Bitácora<br/>inmutable")]:::dark
```

</div>

<div class="flex flex-col gap-2 text-xs">
  <div class="card p-2.5 border-l-3 border-brand-red"><b class="text-brand-red-dark">Cambio de perfil: ¿a todos o a algunos?</b><br />Cada cambio crea una versión nueva; TI elige a quién aplicarla, en una sola transacción.</div>
  <div class="card p-2.5 border-l-3 border-brand-navy"><b class="text-brand-navy-dark">Clonar copia perfiles, no excepciones</b><br />Las excepciones son personales y las aprueba otro administrador TI.</div>
  <div class="card p-2.5 border-l-3 border-brand-crimson"><b class="text-brand-crimson">Nada cambia sin registro</b><br />El cambio y su anotación en la bitácora se guardan juntos o no se guarda nada.</div>
  <div class="card p-2.5 border-l-3 border-slate-400"><b>Base real</b><br />Partimos del sistema actual; el profesor nos enviará el esquema real para ajustar.</div>
</div>

</div>

<!--
Guion (Bloque 5):
"El modelo de datos parte del sistema actual, donde a cada persona se le asignan los permisos de cada aplicación uno por uno. Lo que faltaba era el perfil: un paquete de permisos que se asigna de una vez. En simple: el colaborador viene del directorio y tiene una cuenta con su clave guardada de forma irreversible; esa cuenta puede estar pendiente, activa, bloqueada o desactivada. Además tiene perfiles y, si corresponde, excepciones con fecha de término. Con eso, y con el catálogo de las 37 aplicaciones, se calcula la lista de permisos lista para entregar.
Hay tres reglas importantes. Cuando se cambia un perfil se crea una versión nueva, y TI decide si aplicarla a todos o solo a algunos, en una sola transacción, como acordamos con el cliente. Al clonar se copian los perfiles, no las excepciones, que las aprueba otro administrador. Y nada cambia sin quedar registrado. En el informe están el diagrama completo, el diccionario de datos y diez reglas de integridad."
-->

---

<div class="flex-between items-center mb-1">
  <div class="eyebrow">Bloque 5 · Datos</div>
  <span class="chip bg-slate-100 text-xs font-semibold text-ink-soft border border-slate-200"><div class="i-mdi-lightning-bolt-outline mr-1 text-brand-red" /> Modelo no relacional</span>
</div>

# <span class="i-mdi-flash-outline align-middle mr-2 text-brand-red" /> ¿Por qué también Redis?

<div class="grid grid-cols-2 gap-4 mt-3 text-xs">
  <div class="card p-4 border-t-3 border-brand-navy">
    <div class="font-bold text-brand-navy-dark text-sm mb-2 flex items-center gap-1.5"><div class="i-mdi-database" /> SQL Server (relacional)</div>
    <ul class="space-y-1.5 text-ink/85 leading-relaxed">
      <li><b>Fuente de verdad:</b> todo lo oficial vive aquí.</li>
      <li>Reglas, relaciones e historial completo.</li>
      <li>Responde las consultas de siempre de las 37 apps.</li>
    </ul>
  </div>
  <div class="card p-4 border-t-3 border-brand-red">
    <div class="font-bold text-brand-red-dark text-sm mb-2 flex items-center gap-1.5"><div class="i-mdi-flash" /> Redis (no relacional)</div>
    <ul class="space-y-1.5 text-ink/85 leading-relaxed">
      <li>Guarda <b>una ficha lista por persona</b> con sus permisos.</li>
      <li>Se lee de memoria, sin buscar ni cruzar tablas.</li>
      <li>Si cambian los permisos de alguien, <b>se borra su ficha</b> y se rehace en su siguiente consulta.</li>
      <li>Si se borra o se cae, <b>no se pierde nada</b>: se rehace desde SQL.</li>
    </ul>
  </div>
</div>

<div class="grid grid-cols-4 gap-3 mt-3 text-xs">
  <div class="card p-3 text-center"><div class="text-2xl font-extrabold text-brand-red">~300</div><div class="text-ink-soft">usuarios en hora punta</div></div>
  <div class="card p-3 text-center"><div class="text-2xl font-extrabold text-brand-navy">~600</div><div class="text-ink-soft">consultas simultáneas a la API</div></div>
  <div class="card p-3 text-center"><div class="text-2xl font-extrabold text-brand-crimson">10 min</div><div class="text-ink-soft">duración máxima de cada ficha</div></div>
  <div class="card p-3 text-center"><div class="text-2xl font-extrabold text-brand-navy">&lt; 20 ms</div><div class="text-ink-soft">meta de respuesta (P95)</div></div>
</div>

<div class="mt-2 text-[10px] text-ink-soft flex items-center gap-1"><div class="i-mdi-information-outline" /> ~300 y ~600: datos del cliente (entrevista 14-11). 10 min: igual que la sincronización con AD. La meta de &lt; 20 ms se medirá en la EVA 3 sobre la infraestructura real.</div>

<!--
Guion (Bloque 5):
"Usamos dos tipos de almacenamiento y cada uno tiene un motivo. SQL Server es la fuente de verdad: ahí están las reglas, las relaciones y el historial. Redis guarda una ficha por persona con sus permisos ya resueltos, lista para entregarla.
¿Por qué no guardar eso también en SQL? Porque las aplicaciones siempre hacen la misma pregunta, qué permisos tiene esta persona, y Redis la responde desde memoria, sin cruzar tablas. Si cambian los permisos de alguien, se borra su ficha y se rehace en su siguiente consulta. Y si Redis se cae, no perdemos nada, porque todo se rehace desde SQL.
Según la entrevista con el cliente, en hora punta trabajan unas 300 personas a la vez y, como cada una usa varias aplicaciones, llegan unas 600 consultas al mismo tiempo. Cada ficha dura máximo 10 minutos, el mismo plazo de la sincronización con el directorio. La meta es responder en menos de 20 milisegundos; la mediremos en la EVA 3 sobre la infraestructura real.
Para que se entienda mejor, preparamos una simulación."
-->

---

<div class="flex-between items-center mb-1">
  <div class="eyebrow">Bloque 5 · Datos</div>
  <span class="chip bg-slate-100 text-xs font-semibold text-ink-soft border border-slate-200"><div class="i-mdi-play-circle-outline mr-1 text-brand-red" /> Simulación</span>
</div>

# <span class="i-mdi-flash-outline align-middle mr-2 text-brand-red" /> Redis en acción

<div class="card p-2 mt-2">
  <SlidevVideo autoplay="once" controls class="w-full rounded-lg">
    <source src="/redis-simulador-corto.mp4" type="video/mp4" />
  </SlidevVideo>
</div>

<!--
Guion (Bloque 5) · simulación del diseño, datos de ejemplo:
[0:00] "Una aplicación satélite pide los permisos de un usuario a nuestra API. La API revisa Redis y la ficha ya está lista: responde de inmediato, sin tocar SQL Server."
[0:06] "Ahora llega una consulta por otra persona, pero Redis no tiene su ficha. La API lee sus permisos, que ya están calculados en SQL Server, guarda la ficha en Redis por máximo 10 minutos y responde."
[0:14] "El administrador de TI cambia los permisos de alguien. SQL Server guarda el cambio y su bitácora en una sola transacción, y luego se borra la ficha de esa persona en Redis. En su próxima consulta, la ficha se rehace con los permisos nuevos."
[0:23] "¿Y si Redis no responde? La API lo espera como máximo 50 milisegundos, lee directo desde SQL Server, responde sin errores y avisa a TI con una alerta."
-->
