# Sistema de reserva de espazos · CFR de Vigo — Guía de uso

Aplicación web para que o persoal asesor solicite reservas de aulas e recursos do centro, con calendario de ocupación en tempo real e exportación para a cartelería TV. Publicada en GitHub Pages e conectada a un Google Sheet como base de datos.

URL en produción: `https://caixadocorreo.github.io/reservas-cfr/`

---

## 1. Acceso

A aplicación está protexida por PIN. Na pantalla de inicio:

- **Código PIN**: o PIN do centro (cámbiase no código, ver sección 7)
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
- **Semana** — luns a sábado da semana seleccionada, con navegación anterior/seguinte. Os sábados aparecen en gris claro (actividade esporádica). Os domingos non se mostran.
- **Mes** — mes completo con navegación anterior/seguinte. Os domingos en gris.
- **Set–Dec** — vista cuadrimestral (setembro a decembro)
- **Xan–Mar** — vista trimestral (xaneiro a marzo)
- **Abr–Xuñ** — vista trimestral (abril a xuño)

### Filtros
Na parte superior do calendario pódese filtrar por **espazo** e por **estado**.

### Detalle dunha reserva
Premendo en calquera evento do calendario ábrese un panel con todos os datos da reserva.

### Actualización automática
O calendario recarga os datos do Sheet cada **60 segundos**, polo que os cambios de estado feitos pola persoa responsable reflíctense sen necesidade de recargar a páxina.

---

## 3. Nova reserva

Formulario de solicitude con 5 pasos:

### Paso 1 — Data e horario
- **Data** (mínimo: hoxe)
- **Turno**: Mañá ou Tarde
- **Hora de inicio e hora de fin**
- **Pausa-café**: Si ou Non

### Paso 2 — Espazos
- Indicar se hai **persoas con mobilidade reducida** — se Si, só se mostran espazos de planta baixa (Aula 0, Salón de actos, Polo A, Polo B)
- Engadir os espazos necesarios un a un co botón "+ Engadir". Pódense engadir varios para a mesma reserva

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

### Paso 3 — Tipo de actividade
- **Formación** (con código): activa campos de Código, Título e Relator/a(s)
- Reunión interna
- Videoconferencia
- Docencia compartida PFPP
- Docencia compartida AFC

Para o campo **Relator/a(s)**: se hai máis dunha persoa, separar por `;` (ex: `Ana García; Xosé Fraga`).

### Paso 4 — Recursos
Selección múltiple de recursos dispoñibles. A lista cárgase desde a folla `Recursos` do Sheet — pódese ampliar sen tocar o código.

**Recursos actuais:**
- Carro 1 (21 portátiles)
- Carro 2 (21 portátiles)
- Carro 3 (30 portátiles)
- Kit audiovisual (Radio)
- Kit audiovisual (Vídeo)

### Paso 5 — Observacións
Campo de texto libre para calquera información adicional.

### Previsualización e envío
Antes de enviar móstrase un **resumo** con todos os datos. Ao confirmar, a reserva envíase ao Sheet co estado `Pendente`.

---

## 4. Xestión de reservas (persoa responsable)

A persoa responsable traballa directamente no **Google Sheet**:

1. Abre a folla `Reservas`
2. Na columna **P (Estado)**, cambia o valor mediante o despregable: `Pendente` → `Aprobada` ou `Rexeitada`
3. O calendario web reflicte o cambio no seguinte ciclo de recarga (máximo 60 segundos)

O Sheet ten formato condicional que colorea automaticamente as celas de Estado segundo o valor.

---

## 5. Exportar semana para a folla de Sesións (cartelería TV)

Na vista **Semana** do calendario aparece o botón **"📋 Exportar semana para Sesións"**.

Este botón xera automaticamente os datos das reservas **Aprobadas** de tipo **Formación** da semana visible, no formato exacto da folla de Sesións da cartelería TV:

```
Data    Aula    Código    Título    Relator    HoraInicio    HoraFin
```

- Xérase unha fila por cada espazo de cada reserva
- Ordénanse por data e hora
- O botón **Copiar** copia todo ao portapapeis para pegar directamente na folla de Sesións

Se non hai reservas aprobadas de formación nesa semana, móstrase un aviso.

---

## 6. Fonte de datos — Google Sheet

O Sheet ten catro follas:

### Folla `Reservas`
Recibe automaticamente as solicitudes enviadas desde o formulario web. Columnas:

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
| L | Recursos (separados por `;`) |
| M | Pausa-café |
| N | Mobilidade reducida |
| O | Observacións |
| P | **Estado** (`Pendente` / `Aprobada` / `Rexeitada`) |

### Folla `Asesoras`
Lista de códigos e nomes reais. Só uso interno — non se publica como CSV.

| Columna | Campo |
|---|---|
| A | Código (3 letras) |
| B | Nome completo |

### Folla `Recursos`
Lista de recursos reservables. Publicada como CSV — pódese modificar sen tocar o código.

| Columna | Campo |
|---|---|
| A | Nome do recurso |
| B | Activo (`Si` / `Non`) |

### Folla `Config`
PIN de acceso. Só uso interno — non se publica como CSV.

| Columna | Campo |
|---|---|
| A | `PIN` |
| B | Valor do PIN |

---

## 7. Cambiar o PIN

O PIN está no código do ficheiro `index.html`. Para cambialo:

1. Descarga o `index.html` de GitHub (ou edítao directamente en GitHub)
2. Busca a liña: `const PIN_CORRECTO = '...'`
3. Substitúe o valor polo novo PIN
4. Sube de novo a GitHub

---

## 8. Engadir novos recursos

1. Abre a folla `Recursos` do Sheet
2. Engade unha nova fila co nome do recurso e `Si` na columna Activo
3. O formulario web mostrará o novo recurso automaticamente na próxima carga

---

## 9. Engadir novos espazos

Os espazos están no código do ficheiro `index.html`. Para engadir un novo:

1. Descarga o `index.html` de GitHub
2. Busca o array `const ESPAZOS_TODOS = [`
3. Engade o novo espazo seguindo o formato:
   ```javascript
   { nome: 'Nome do espazo', andar: 'Planta baixa', accesible: true },
   ```
   (`accesible: true` só para espazos de planta baixa)
4. Sube de novo a GitHub

---

## 10. Actualización do sistema

Para cambios no **contido** (recursos, estado de reservas): editar o Google Sheet directamente.

Para cambios no **código ou deseño**:
1. Editar o ficheiro `index.html`
2. Subir a GitHub en `caixadocorreo/reservas-cfr` (Add file → Upload files)
3. GitHub Pages actualízase automaticamente en poucos minutos
