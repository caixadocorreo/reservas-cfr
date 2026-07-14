# Sistema de reserva de espazos · CFR de Vigo — Guía de uso

Aplicación web para que o persoal asesor solicite reservas de aulas e recursos do centro, con calendario de ocupación en tempo real e exportación para a cartelería TV. Publicada en GitHub Pages e conectada a un Google Sheet como base de datos.

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

---

## 3. Nova reserva

### Paso 1 — Data e horario
- **Data** (mínimo: hoxe)
- **Turno**: Mañá ou Tarde
- **Hora de inicio e hora de fin**
- **Pausa-café**: Si ou Non

> Para reservas de tipo **AFI** e **Formación PFPP** o horario e a pausa-café introdúcense por sesión no paso 3b.

### Paso 2 — Espazos
- Indicar se hai **persoas con mobilidade reducida** — se Si, só se mostran espazos de planta baixa
- Engadir os espazos necesarios un a un co botón "+ Engadir"

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

### Paso 3 — Tipo de actividade e código

**Tipos dispoñibles:**
- **AFI** → activa catálogo, campos de código/título e táboa de sesións múltiples
- **Formación PFPP** → activa catálogo, campos de código/título e táboa de sesións múltiples
- **Reunión interna** → sen código
- **Videoconferencia** → sen código
- **Docencia compartida PFPP** → sen código
- **Docencia compartida AFC** → sen código

Para **AFI** e **Formación PFPP** aparece un despregable de **catálogo** que auto-enche o código e o título ao seleccionar a actividade. Se a actividade non está no catálogo, pódese escribir manualmente.

### Paso 3b — Sesións (só AFI e Formación PFPP)

Táboa de sesións onde se engade unha fila por cada día de formación:

| Campo | Contido |
|---|---|
| Data | Data de cada sesión |
| Hora inicio | Hora de comezo |
| Hora fin | Hora de remate |
| Relator/a(s) | Pode variar por sesión. Separar por `;` se hai varias persoas |
| Recursos | Recurso necesario ese día (un por sesión) |
| Café | Si ou Non para esa sesión |

Botón **"+ Engadir sesión"** para ir completando a táboa. Ao enviar, xérase unha fila no Sheet por cada sesión.

### Paso 4 — Recursos (só para tipos non multisesión)
Selección de recursos para reservas de sesión única. A lista cárgase desde a folla `Recursos` do Sheet.

**Recursos actuais:**
- Carro 1 (21 portátiles)
- Carro 2 (21 portátiles)
- Carro 3 (30 portátiles)
- Kit audiovisual (Radio)
- Kit audiovisual (Vídeo)

### Paso 4 — Observacións
Campo de texto libre para información adicional.

### Previsualización e envío
Antes de enviar móstrase un **resumo** con todos os datos. Ao confirmar, a reserva envíase ao Sheet co estado `Pendente`.

---

## 4. Xestión de reservas (persoa responsable)

A persoa responsable traballa directamente no **Google Sheet**:

1. Abre a folla `Reservas`
2. Na columna **P (Estado)**, cambia o valor mediante o despregable: `Pendente` → `Aprobada` ou `Rexeitada`
3. Pódese modificar calquera campo (espazo, horario...) antes de aprobar se é necesario
4. O calendario web reflicte o cambio no seguinte ciclo de recarga (máximo 60 segundos)

---

## 5. Exportar semana para a folla de Sesións (cartelería TV)

Na vista **Semana** do calendario aparece o botón **"📋 Exportar semana para Sesións"**.

Xera os datos das reservas **Aprobadas** de tipo **AFI** ou **Formación PFPP** da semana visible, no formato da folla de Sesións da cartelería TV:

```
Data    Aula    Código    Título    Relator    HoraInicio    HoraFin
```

- Unha fila por cada espazo de cada reserva
- Ordenadas por data e hora
- Botón **Copiar** para pegar directamente na folla de Sesións

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
Catálogo de actividades para o despregable do formulario.
| Columna | Campo |
|---|---|
| A | Tipo (`AFI` ou `Formación PFPP`) |
| B | Código |
| C | Título |
| D | Activo (`Si` / `Non`) |

Para desactivar unha actividade do despregable sen borrala, pon `Non` na columna Activo.

### Folla `Recursos`
Lista de recursos reservables.
| Columna | Campo |
|---|---|
| A | Nome do recurso |
| B | Activo (`Si` / `Non`) |

### Folla `Asesoras`
Lista de códigos e nomes reais. Só uso interno.
| Columna | Campo |
|---|---|
| A | Código (3 letras) |
| B | Nome completo |

### Folla `Config`
PIN de acceso. Só uso interno.
| Columna | Campo |
|---|---|
| A | `PIN` |
| B | Valor do PIN |

---

## 7. Constantes CSV no código

| Constante | Folla |
|---|---|
| `CSV_RESERVAS_URL` | Reservas (calendario) |
| `CSV_RECURSOS_URL` | Recursos (formulario) |
| `CSV_CATALOGO_URL` | Catálogo de actividades |

Se algunha folla cambia de URL de publicación, actualiza a constante no código e sube de novo a GitHub.

---

## 8. Cambiar o PIN

1. Descarga o `index.html` de GitHub
2. Busca: `const PIN_CORRECTO = '...'`
3. Substitúe o valor
4. Sube de novo a GitHub

---

## 9. Engadir novos recursos

1. Abre a folla `Recursos` do Sheet
2. Engade unha nova fila co nome e `Si` en Activo
3. O formulario actualízase automaticamente

---

## 10. Engadir novas actividades ao catálogo

1. Abre a folla `Catalogo` do Sheet
2. Engade unha nova fila con Tipo, Código, Título e `Si` en Activo
3. O despregable actualízase automaticamente

---

## 11. Engadir novos espazos

1. Descarga o `index.html` de GitHub
2. Busca: `const ESPAZOS_TODOS = [`
3. Engade o novo espazo:
   ```javascript
   { nome: 'Nome do espazo', andar: 'Planta baixa', accesible: true },
   ```
4. Sube de novo a GitHub

---

## 12. Actualización do sistema

Para cambios no **contido** (recursos, catálogo, estado de reservas): editar o Google Sheet directamente.

Para cambios no **código ou deseño**:
1. Editar o ficheiro `index.html`
2. Subir a GitHub en `caixadocorreo/reservas-cfr`
3. GitHub Pages actualízase en poucos minutos
