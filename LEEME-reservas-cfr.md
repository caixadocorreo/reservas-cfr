# Sistema de reserva de espazos · CFR de Vigo — Guía de uso

Aplicación web para que o persoal asesor solicite reservas de aulas e recursos do centro, con calendario de ocupación en tempo real, exportación para a cartelería TV e descarga en PDF. Publicada en GitHub Pages e conectada a un Google Sheet como base de datos.

URL en produción: `https://caixadocorreo.github.io/reservas-cfr/`

---

## 1. Acceso

A aplicación está protexida por PIN. Na pantalla de inicio:

- **Código PIN**: o PIN do centro (cámbiase no código, ver sección 8)
- **Iniciais**: as 3 letras que identifican á persoa asesora (primeira letra do nome + primeira letra de cada apelido, en maiúsculas). Exemplo: Ana Martínez García → `AMG`

A relación de iniciais e nomes reais está na folla `Asesoras` do Google Sheet, só accesible internamente.

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
Na parte superior do calendario pódese filtrar por **espazo** e por **estado**.

### Detalle dunha reserva
Premendo en calquera evento do calendario ábrese un panel con todos os datos.

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

## 3. Nova reserva

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

---

## 4. Xestión de reservas (persoa responsable)

A persoa responsable traballa directamente no **Google Sheet**:

1. Abre a folla `Reservas`
2. Na columna **P (Estado)**, cambia o valor mediante o despregable: `Pendente` → `Aprobada` ou `Rexeitada`
3. Pódese modificar calquera campo antes de aprobar (espazo, horario, etc.)
4. O calendario web reflicte o cambio no seguinte ciclo de recarga (máximo 60 segundos)

**Consello**: usa **Datos → Crear un filtro** para ordenar e filtrar por estado, data ou espazo sen alterar a orde real dos datos nin o CSV publicado.

---

## 5. Fonte de datos — Google Sheet

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

### Folla `Config`
| Columna | Campo |
|---|---|
| A | `PIN` |
| B | Valor do PIN |

---

## 6. Constantes CSV no código

| Constante | Folla |
|---|---|
| `CSV_RESERVAS_URL` | Reservas (calendario) |
| `CSV_RECURSOS_URL` | Recursos (formulario) |
| `CSV_CATALOGO_URL` | Catálogo de actividades |

---

## 7. Cabeceira

A cabeceira da aplicación ten o mesmo estilo que a cartelería TV e o carrusel: logo CFR á esquerda, título centrado con as iniciais da persoa conectada, e logo Xacobeo 2027 á dereita. Os logos están embutidos en base64 no código.

---

## 8. Cambiar o PIN

1. Descarga o `index.html` de GitHub
2. Busca: `const PIN_CORRECTO = '...'`
3. Substitúe o valor
4. Sube de novo a GitHub

---

## 9. Engadir novos recursos

1. Abre a folla `Recursos` do Sheet
2. Engade fila co nome e `Si` en Activo
3. O formulario actualízase automaticamente

---

## 10. Engadir actividades ao catálogo

1. Abre a folla `Catalogo` do Sheet
2. Engade fila con Tipo, Código, Título e `Si` en Activo
3. O despregable actualízase automaticamente

---

## 11. Engadir novos espazos

1. Descarga o `index.html` de GitHub
2. Busca: `const ESPAZOS_TODOS = [`
3. Engade: `{ nome: 'Nome', andar: 'Planta baixa', accesible: true },`
4. Sube de novo a GitHub

---

## 12. Actualización do sistema

Para cambios no **contido** (recursos, catálogo, estados): editar o Google Sheet.

Para cambios no **código ou deseño**:
1. Editar o ficheiro `index.html`
2. Subir a GitHub en `caixadocorreo/reservas-cfr`
3. GitHub Pages actualízase en poucos minutos
