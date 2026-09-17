# Sistema de reserva de espazos · CFR de Vigo — Guía de uso

*Última actualización: 17/09/2026 (Espazo externo, distinción entre fallo de rede e resposta ambigua do servidor)*

Aplicación web para que o persoal asesor solicite reservas de aulas e recursos do centro, con calendario de ocupación en tempo real, exportación para a cartelería TV e descarga en PDF. Publicada en GitHub Pages e conectada a un Google Sheet como base de datos.

URL en produción: `https://caixadocorreo.github.io/reservas-cfr/`

---

## 1. Acceso

A aplicación está protexida por PIN. Na pantalla de inicio:

- **Código PIN**: o PIN do centro (cámbiase no código, ver sección 8)
- **Iniciais**: as 3 letras que identifican á persoa asesora (primeira letra do nome + primeira letra de cada apelido, en maiúsculas). Exemplo: Ana Martínez García → `AMG`

A relación de iniciais e nomes reais está na folla `Asesoras` do Google Sheet, só accesible internamente.

### Validación das iniciais
A aplicación comproba que as iniciais introducidas correspondan a unha persoa asesora real, contra unha lista de códigos válidos cargada desde `CSV_ASESORAS_URL`.

**Importante — privacidade**: esa URL debe publicar **unicamente a columna de códigos** (columna A da folla `Asesoras`), **nunca** a columna de nomes reais (columna B), xa que calquera CSV publicado en Google Sheets é accesible publicamente por quen teña a ligazón. Se se quere manter a folla `Asesoras` orixinal sen publicar (como ata agora), pódese crear unha folla auxiliar só coa columna de códigos e publicar esa.

Se `CSV_ASESORAS_URL` non está configurada (valor `PENDENTE_URL_ASESORAS`) ou falla a súa carga, a validación **desactívase automaticamente** e só se comproba que se escribiron 3 letras — non se bloquea o acceso a ninguén por un problema de rede ou configuración.

---

## 2. Calendario de ocupación

Vista principal da aplicación. Mostra todas as reservas do sistema con código de cores segundo o estado:

| Cor | Estado |
|---|---|
| 🟠 Laranxa | Pendente de aprobación |
| 🟢 Verde | Aprobada |
| 🔴 Vermello | Rexeitada |

### Vistas dispoñibles
- **Semana** — luns a sábado da semana seleccionada, con navegación anterior/seguinte. Os sábados aparecen en gris claro. Os domingos non se mostran.
- **Mes** — mes completo con navegación anterior/seguinte. Os domingos en gris.
- **Set–Dec** — vista cuadrimestral (setembro a decembro)
- **Xan–Mar** — vista trimestral (xaneiro a marzo)
- **Abr–Xuñ** — vista trimestral (abril a xuño)

### Filtros
Na parte superior do calendario pódese filtrar por **espazo**, por **estado** e, mediante a casa de verificación **"As miñas reservas"**, amosar unicamente as reservas da persoa conectada. Os filtros combínanse entre si.

### Detalle dunha reserva
Premendo en calquera evento do calendario ábrese un panel con todos os datos.

**Cancelar unha reserva propia**: se a reserva é túa (mesmas iniciais que a persoa conectada) e segue **Pendente**, aparece un botón **"Cancelar esta reserva"**. Pide confirmación, márcaa como `Rexeitada` e engade a nota `[Cancelada pola persoa asesora]` en Observacións, para diferenciala dun rexeitamento da persoa responsable. Non é posible cancelar reservas xa `Aprobada` ou `Rexeitada` dende aquí, nin reservas doutra persoa.

### Actualización automática
O calendario recarga os datos do Sheet cada **60 segundos**.

### Exportar a PDF
En todas as vistas aparece o botón **🖨️ Descargar PDF** que abre o diálogo de impresión do navegador en formato A4 apaisado. O PDF inclúe:
- Cabeceira con "Reserva de espazos · CFR de Vigo" e o período
- Eventos con aula, horario, iniciais e código
- Cores por estado conservadas

### Exportar semana para Sesións (cartelería TV)
Na vista **Semana** aparece tamén o botón **📋 Exportar semana para Sesións**, que abre unha xanela cos datos tabulados das reservas Aprobadas (tipo AFI ou Formación PFPP) da semana efectivamente seleccionada nesa vista, listos para copiar (botón **Copiar**) e pegar na folla de Sesións da cartelería TV.

**Importante**: a semana que se exporta é a que marcan as frechas ‹ › **dentro da propia vista Semana**, non a que se estea a ver noutra vista (Mes, Set–Dec, etc.). Se navegaches a outra semana na vista Semana e despois miraches o calendario noutra vista, ao volver a exportar seguirá a usar a última semana navegada na vista Semana. Antes de exportar, comproba que a data que aparece na cabeceira do calendario (ao carón das frechas) coincide coa semana que queres.

---

## 3. Informes de ocupación

Pestana **"📊 Informes"**. Calcúlase enteiramente no navegador a partir das reservas xa cargadas (non fai falta tocar o Sheet nin o Apps Script).

### Período
Despregable con: Este mes, Mes pasado, Este trimestre, Curso actual (setembro–xuño), Ano natural actual, ou **Personalizado** (mostra dous selectores de data). Ao premer **"Xerar informe"** calcúlanse os datos dese rango.

### Contido do informe
- **4 tarxetas de resumo**: reservas totais, Aprobadas, Pendentes, Rexeitadas (con % de taxa de rexeitamento sobre o total).
- **Horas de ocupación por espazo**: gráfico de barras coas horas reservadas en cada aula/espazo, de maior a menor. Só conta reservas `Aprobada` e `Pendente` — as `Rexeitada` non chegaron a ocupar nada.
- **Reservas por tipo de actividade**: gráfico de barras (AFI, Formación PFPP, Reunión interna, Videoconferencia, Docencias compartidas).
- **Reservas por persoa asesora**: táboa coas iniciais e o número de reservas de cada unha.

Os gráficos son barras HTML/CSS sinxelas, sen ningunha libraría externa — coherente co resto da aplicación, que é un único ficheiro sen dependencias de rede.

### Descargar en PDF
Botón **"🖨️ Descargar PDF"** (aparece despois de xerar un informe). Reutiliza o mesmo mecanismo de impresión do navegador que xa usa o calendario (A4 apaisado): xera unha cabeceira co período e agocha os controis do formulario, deixando só o resultado.

---

## 4. Nova reserva

### Paso 1 — Tipo de actividade

**Tipos dispoñibles:**
- **AFI** → activa catálogo, campos de código/título e táboa de sesións múltiples
- **Formación PFPP** → activa catálogo, campos de código/título e táboa de sesións múltiples
- **Reunión interna** → sen código nin sesións múltiples
- **Videoconferencia** → sen código nin sesións múltiples
- **Docencia compartida PFPP** → sen código nin sesións múltiples
- **Docencia compartida AFC** → sen código nin sesións múltiples

Para **AFI** e **Formación PFPP** aparece un despregable de **catálogo** que auto-enche o código e o título. Se a actividade non está no catálogo, escríbese manualmente.

### Paso 2 — Data e horario
Só visible para tipos de sesión única (Reunión interna, Videoconferencia, Docencias compartidas).
- Data (mínimo: hoxe), Turno, Hora inicio, Hora fin, Pausa-café

**Horario predefinido**: selector con catro franxas de tarde habituais (16:30–19:30, 16:30–20:30, 17:00–20:00, 17:00–21:00) que preenche automaticamente Hora inicio, Hora fin e pon o Turno en "Tarde". A opción por defecto "Persoalizado" non toca nada, para poder escribir calquera outro horario (incluídas mañás) a man.

### Paso 3 — Espazos
- Indicar se hai persoas con **mobilidade reducida** — se Si, só se mostran espazos de planta baixa
- Engadir espazos un a un co botón "+ Engadir"

**Espazos dispoñibles:**
| Espazo (nome exacto) | Andar | Accesible |
|---|---|---|
| `Aula 0` | Planta baixa | Si |
| `Salón de actos` | Planta baixa | Si |
| `Polo A` | Planta baixa | Si |
| `Polo B` | Planta baixa | Si |
| `Aula 1` | 1º andar | Non |
| `Aula 2` | 1º andar | Non |
| `Aula 3` | 1º andar | Non |
| `Aula 4` | 1º andar | Non |
| `Aula 5` | 1º andar | Non |
| `Aula 6` | 1º andar | Non |
| `Aula 7` | 2º andar | Non |
| `Aula 8` | 2º andar | Non |
| `Espazo externo` | — | Si (sempre visible) |

**Espazo externo**: para actividades que se desenvolven fóra do CFR por necesidade de instalacións (por exemplo, un pavillón cedido por un centro educativo para iniciación a deportes urbanos). Ao seleccionalo, aparece un campo obrigatorio **"Onde se realiza (centro, enderezo…)"**; ese detalle engádese automaticamente ás Observacións como `[Espazo externo: <texto>]`. Pódese combinar cun espazo do CFR na mesma reserva. Non está suxeito ao filtro de mobilidade reducida nin á detección de conflitos de espazo (por ser un cubo xenérico, non un espazo físico único que se poida dobre-reservar dende aquí), pero **si** conta como unha barra propia nos informes de ocupación (§3).

### Paso 4 — Sesións (só AFI e Formación PFPP)
Táboa onde se engade unha fila por cada día de formación:

| Campo | Contido |
|---|---|
| Data | Data de cada sesión |
| Hora inicio | Hora de comezo |
| Hora fin | Hora de remate |
| Relator/a(s) | Pode variar por sesión. Separar por `;` se hai varias persoas |
| Recursos | Recurso necesario ese día |
| Café | Si ou Non para esa sesión |

Botón **"+ Engadir sesión"** para ir completando. Ao enviar, xérase unha fila no Sheet por cada sesión (mediante unha chamada a un Google Apps Script). O campo Turno de cada sesión non se pide: calcúlase automaticamente a partir da hora de inicio (antes das 14:00 → Mañá; a partir das 14:00 → Tarde).

**Horario predefinido por sesión**: cada fila ten un pequeno selector, enriba do campo Hora inicio, coas mesmas catro franxas de tarde do Paso 2. Ao escoller unha, preenche Hora inicio e Hora fin desa fila; a opción "Persoalizado" (por defecto) deixa escribir calquera outro horario a man.

**Duplicar sesión**: cada fila inclúe un botón **⧉ Duplicar esta sesión**, que engade unha nova fila xusto despois da orixinal copiando todos os seus valores (data, horario, relator/a(s), recursos e café). É útil cando varias sesións comparten o mesmo horario e relator/a e só cambia a data: duplícase a fila e edítase unicamente o que sexa distinto.

**Solapamento entre sesións da mesma reserva**: se dúas ou máis filas da táboa teñen a mesma data cun horario que se solapa entre si (erro típico ao duplicar unha sesión e esquecer cambiar a data), a aplicación **bloquea** a previsualización e o envío ata que se corrixa, indicando cales sesións conflitúan.

**Segundo recurso (caso pouco habitual)**: se unha sesión precisa dous recursos á vez (por exemplo, kit audiovisual de radio e un carro de portátiles), o enlace **"+ outro recurso"** debaixo do selector de Recursos engade un segundo selector. Os dous valores gárdanse xuntos na columna Recursos, separados por `;`.

### Paso 4 — Recursos (só para tipos de sesión única)
Selección de recursos. A lista cárgase desde a folla `Recursos` do Sheet.

**Recursos actuais:**
- Carro 1 (21 portátiles)
- Carro 2 (21 portátiles)
- Carro 3 (30 portátiles)
- Kit audiovisual (Radio)
- Kit audiovisual (Vídeo)

### Paso 5 — Observacións
Campo de texto libre para información adicional.

### Previsualización e envío
Antes de enviar móstrase un resumo completo. Ao confirmar, xérase unha fila no Sheet por cada sesión co estado `Pendente`.

**Validacións de horario**: tanto ao previsualizar coma ao confirmar o envío, a aplicación **bloquea** se falta a data ou o horario nalgunha sesión, se non se engadiu ningunha sesión (AFI/PFPP), ou se a hora de fin non é posterior á hora de inicio.

**Detección de conflitos de espazo**: ao previsualizar, a aplicación comproba se algún dos espazos seleccionados xa ten unha reserva `Pendente` ou `Aprobada` na mesma data cun horario que se solapa, e se é así móstrase un aviso laranxa co detalle do conflito (espazo, data, horario, tipo e persoa da reserva existente). É un **aviso, non un bloqueo**: a persoa pode confirmar igualmente a reserva a pesar do aviso, xa que a decisión final correspóndelle á persoa responsable na aprobación. Como o calendario se actualiza cada 60 segundos, en casos moi puntuais (reservas feitas segundos antes) podería non detectarse un conflito real.

O botón **Confirmar e enviar** desactívase automaticamente mentres dura o envío (amosa "Enviando...") para evitar reservas duplicadas por premer varias veces seguidas.

**Se falla o envío**, a aplicación distingue dous casos e retira da lista as sesións xa enviadas correctamente en ambos:
- **Fallo de conexión real** (a petición nunca chegou ao servidor): é seguro premer "Confirmar e enviar" de novo, xa reintentará só o que falta.
- **Resposta non lexible do servidor**: a petición *si* chegou e puido gardarse igualmente, pero o navegador non recibiu unha resposta que puidese interpretar (pode ocorrer por particularidades das Web Apps de Apps Script). Neste caso o aviso indícao explicitamente e recomenda **comprobar a folla `Reservas` antes de reintentar**, para non duplicar datos que xa estean gardados.

**Aviso por peche accidental**: se se toca calquera campo do formulario de Nova reserva e se intenta pechar ou recargar a pestana sen enviar, o navegador mostra un aviso de confirmación para evitar perder o traballo sen querer.

Se o envío falla por un erro do propio sistema (non por un problema de conexión do navegador), queda **rexistrado automaticamente na folla `Erros`** do Sheet, con data, mensaxe de erro e os datos que se intentaron enviar. Os fallos de rede que non cheguen a chegar ao Apps Script (por exemplo, sen conexión) non poden quedar rexistrados aí, xa que o servidor nunca chega a velos.

Cada fila (sesión) envíase de xeito independente ao Sheet:
- Se todo se envía correctamente, móstrase un aviso de éxito e a aplicación volve ao calendario.
- Se falla o envío dalgunha fila (por exemplo, por un problema puntual de conexión), as filas que **si** se enviaron correctamente retíranse automaticamente da táboa de sesións, e queda un aviso visible (non desaparece só) indicando cantas se enviaron e cantas fallaron. O botón "Confirmar e enviar" volve estar dispoñible para reintentar **unicamente** o que falta, sen risco de duplicar o que xa chegou ao Sheet.

Se os fallos de envío se repiten de forma insistente (non puntual), convén revisar os *logs* de execución do Apps Script: **Extensións → Apps Script → Execucións**.

---

## 5. Xestión de reservas (persoa responsable)

A persoa responsable traballa directamente no **Google Sheet**:

1. Abre a folla `Reservas`
2. Na columna **P (Estado)**, cambia o valor mediante o despregable: `Pendente` → `Aprobada` ou `Rexeitada`
3. Pódese modificar calquera campo antes de aprobar (espazo, horario, etc.)
4. O calendario web reflicte o cambio no seguinte ciclo de recarga (máximo 60 segundos)

**Consello**: usa **Datos → Crear un filtro** para ordenar e filtrar por estado, data ou espazo sen alterar a orde real dos datos nin o CSV publicado.

**Consello**: revisa de cando en vez a folla `Erros` (§6) para detectar reservas que fallaron ao intentar gardarse — a persoa que as intentou enviar puido non decatarse se o fallo non foi definitivo á primeira.

---

## 6. Fonte de datos — Google Sheet

### Folla `Reservas`
| Columna | Campo |
|---|---|
| A | Timestamp |
| B | Asesora (iniciais) |
| C | Data (AAAA-MM-DD) |
| D | Turno |
| E | Hora inicio |
| F | Hora fin |
| G | Espazos (separados por `;`) |
| H | Tipo |
| I | Código |
| J | Título |
| K | Relator/a(s) (separados por `;`) |
| L | Recursos |
| M | Pausa-café |
| N | Mobilidade reducida |
| O | Observacións |
| P | **Estado** (`Pendente` / `Aprobada` / `Rexeitada`) |

### Folla `Catalogo`
| Columna | Campo |
|---|---|
| A | Tipo (`AFI` ou `Formación PFPP`) |
| B | Código |
| C | Título |
| D | Activo (`Si` / `Non`) |

### Folla `Recursos`
| Columna | Campo |
|---|---|
| A | Nome do recurso |
| B | Activo (`Si` / `Non`) |

### Folla `Asesoras`
| Columna | Campo |
|---|---|
| A | Código (3 letras) |
| B | Nome completo |

*Nota de privacidade*: esta folla en si non se publica. Para a validación de iniciais (§1), só se publica a columna A (código) — nunha folla auxiliar se non se quere tocar a orixinal. A columna B (nome completo) non debe publicarse nunca en CSV.

### Folla `Config`
| Columna | Campo |
|---|---|
| A | `PIN` |
| B | Valor do PIN |

### Folla `Erros`
Créase automaticamente pola primeira vez que un envío falla. Non hai que crear nada previamente.

| Columna | Campo |
|---|---|
| A | Timestamp |
| B | Mensaxe de erro |
| C | Datos recibidos (bruto, en formato JSON) |

---

## 7. Constantes CSV no código

| Constante | Folla |
|---|---|
| `CSV_RESERVAS_URL` | Reservas (calendario) |
| `CSV_RECURSOS_URL` | Recursos (formulario) |
| `CSV_CATALOGO_URL` | Catálogo de actividades |
| `CSV_ASESORAS_URL` | Códigos de asesoras (só columna A, para validar o login) |

---

## 8. Cabeceira

A cabeceira da aplicación ten o mesmo estilo que a cartelería TV e o carrusel: logo CFR á esquerda, título centrado con as iniciais da persoa conectada, e logo Xacobeo 2027 á dereita. Os logos están embutidos en base64 no código.

---

## 9. Cambiar o PIN

1. Descarga o `index.html` de GitHub
2. Busca: `const PIN_CORRECTO = '...'`
3. Substitúe o valor
4. Sube de novo a GitHub

---

## 10. Engadir novos recursos

1. Abre a folla `Recursos` do Sheet
2. Engade fila co nome e `Si` en Activo
3. O formulario actualízase automaticamente

---

## 11. Engadir actividades ao catálogo

1. Abre a folla `Catalogo` do Sheet
2. Engade fila con Tipo, Código, Título e `Si` en Activo
3. O despregable actualízase automaticamente

---

## 12. Engadir novos espazos

1. Descarga o `index.html` de GitHub
2. Busca: `const ESPAZOS_TODOS = [`
3. Engade: `{ nome: 'Nome', andar: 'Planta baixa', accesible: true },`
4. Sube de novo a GitHub

---

## 13. Actualización do sistema

Para cambios no **contido** (recursos, catálogo, estados): editar o Google Sheet.

Para cambios no **código ou deseño** (`index.html`):
1. Editar o ficheiro `index.html`
2. Subir a GitHub en `caixadocorreo/reservas-cfr`
3. GitHub Pages actualízase en poucos minutos

Para cambios no **backend** (Apps Script — o código que recibe as reservas e as escribe no Sheet):
1. No Google Sheet: **Extensións → Apps Script**
2. Editar o código e gardar
3. **Implementar → Xestionar implementacións → (icona lapis) → Nova versión → Implementar**. É imprescindible crear unha nova versión; gardar o código só non abonda para que os cambios cheguen á URL que xa usa a aplicación.
