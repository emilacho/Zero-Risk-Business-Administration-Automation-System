# 📋 REQUERIMIENTOS FUNCIONALES - SISTEMA ZERO RISK

## Sistema de Gestión Administrativa Automatizada

**Versión:** 1.0  
**Fecha:** Febrero 2026  
**Autor:** Emilio Pérez  
**Estado:** Documento de Especificación

---

## 📊 VISIÓN GENERAL DEL SISTEMA

### **Objetivo:**
Automatizar el ciclo administrativo completo de Zero Risk, desde la creación de proformas hasta estados financieros consolidados, eliminando procesos manuales y reduciendo tiempo administrativo en 87%.

### **Alcance:**
Sistema modular que integra:
1. Gestión Comercial (Proformas y Facturas automatizadas)
2. Registro Contable Automático (Libro Diario, Mayor, Estados Financieros)
3. Control de Gastos Operativos
4. Dashboard Ejecutivo en Tiempo Real

---

## 🎯 MÓDULO 1: PROFORMA AUTOMATIZADA

### **RF-001: Formulario Inteligente de Proforma**

**Descripción:**  
La hoja de proforma debe funcionar como un formulario inteligente donde TODOS los campos editables tienen listas desplegables o validación de datos.

**Campos con Listas Desplegables:**

| Campo | Ubicación | Fuente de Datos | Comportamiento |
|-------|-----------|-----------------|----------------|
| **Fecha** | C11 | Selector de calendario | Al hacer clic, abre calendario |
| **Cliente** | C16 | Base Datos → Clientes | Lista de razones sociales |
| **RUC** | C17 | Auto-llenado | Se llena automáticamente al seleccionar cliente |
| **Teléfono** | F17 | Auto-llenado | Se llena automáticamente al seleccionar cliente |
| **Email** | C18 | Auto-llenado | Se llena automáticamente al seleccionar cliente |
| **Dirección** | F18 | Auto-llenado | Se llena automáticamente al seleccionar cliente |
| **Producto (C20-C29)** | C20:C29 | Base Datos → Productos | Lista de productos/servicios |
| **Precio (E20-E29)** | E20:E29 | Auto-llenado | Busca precio del producto seleccionado |
| **Cantidad (B20-B29)** | B20:B29 | Input numérico | Usuario escribe, mínimo 1 |

**Cálculos Automáticos:**

```
Celda F20 (Total Línea 1):
=IF(AND(B20<>"", E20<>""), B20*E20, "")

Celda F34 (Subtotal):
=SUM(F20:F29)

Celda F35 (IVA 15%):
=F34*0.15

Celda F36 (Total):
=F34+F35
```

**Validaciones:**
- ✅ No permitir guardar si falta cliente
- ✅ No permitir guardar si no hay al menos 1 producto
- ✅ Cantidad debe ser > 0
- ✅ Alertar si email de cliente no tiene formato válido

---

### **RF-002: Numeración Automática de Proformas**

**Descripción:**  
Cada proforma debe tener un número único correlativo que se asigna automáticamente.

**Comportamiento:**
```javascript
// Apps Script
function obtenerNumeroProforma() {
  var baseDatos = SpreadsheetApp.openById('ID_BASE_DATOS');
  var config = baseDatos.getSheetByName('Config');
  
  // Leer último número
  var ultimoNumero = config.getRange('B7').getValue(); // Ej: 45
  
  // Incrementar
  var nuevoNumero = ultimoNumero + 1; // 46
  
  // Actualizar en base de datos
  config.getRange('B7').setValue(nuevoNumero);
  
  // Formatear: PRO-2025-046
  var numeroFormateado = 'PRO-2025-' + 
                         String(nuevoNumero).padStart(3, '0');
  
  return numeroFormateado;
}
```

**Ubicación:**  
Celda F10 en la proforma

**Ejemplo:**
```
Proforma #1 → PRO-2025-001
Proforma #2 → PRO-2025-002
Proforma #45 → PRO-2025-045
```

---

### **RF-003: Auto-llenado de Datos de Cliente**

**Descripción:**  
Al seleccionar un cliente de la lista desplegable, todos sus datos deben llenarse automáticamente.

**Fórmulas:**

```excel
// Celda C17 (RUC)
=IFERROR(INDEX('[BASE_DATOS]Clientes'!$C:$C, 
         MATCH(C16, '[BASE_DATOS]Clientes'!$B:$B, 0)), "")

// Celda F17 (Teléfono)
=IFERROR(INDEX('[BASE_DATOS]Clientes'!$D:$D, 
         MATCH(C16, '[BASE_DATOS]Clientes'!$B:$B, 0)), "")

// Celda C18 (Email)
=IFERROR(INDEX('[BASE_DATOS]Clientes'!$E:$E, 
         MATCH(C16, '[BASE_DATOS]Clientes'!$B:$B, 0)), "")

// Celda F18 (Dirección)
=IFERROR(INDEX('[BASE_DATOS]Clientes'!$F:$F, 
         MATCH(C16, '[BASE_DATOS]Clientes'!$B:$B, 0)), "")
```

**Flujo:**
```
1. Usuario hace clic en celda C16
2. Aparece lista desplegable con clientes
3. Usuario selecciona "Exportquilsa"
4. AUTOMÁTICAMENTE se llenan:
   - C17: 0992345678001
   - F17: 0986434504
   - C18: asistente.compras@exportquilsa.com
   - F18: Vía a la Costa Km 15
```

---

### **RF-004: Auto-llenado de Precios de Productos**

**Descripción:**  
Al seleccionar un producto/servicio, su precio debe aparecer automáticamente.

**Fórmulas:**

```excel
// Celda E20 (Precio Línea 1)
=IFERROR(INDEX('[BASE_DATOS]Productos'!$D:$D, 
         MATCH(C20, '[BASE_DATOS]Productos'!$C:$C, 0)), "")

// Copiar fórmula a E21:E29
```

**Flujo:**
```
1. Usuario selecciona producto: "Recarga extintor 20 Lbs PQS"
2. AUTOMÁTICAMENTE aparece precio: $30.00
3. Usuario escribe cantidad: 15
4. AUTOMÁTICAMENTE calcula total línea: $450.00
```

---

### **RF-005: Opción "Agregar Nuevo Cliente/Producto"**

**Descripción:**  
En las listas desplegables, la última opción debe ser "-- AGREGAR NUEVO --"

**Comportamiento:**
```javascript
// Apps Script
function alSeleccionarOpcionAgregar() {
  var hoja = SpreadsheetApp.getActiveSheet();
  var celda = hoja.getActiveCell();
  var valor = celda.getValue();
  
  if (valor === "-- AGREGAR NUEVO CLIENTE --") {
    mostrarFormularioNuevoCliente();
  } else if (valor === "-- AGREGAR NUEVO PRODUCTO --") {
    mostrarFormularioNuevoProducto();
  }
}

function mostrarFormularioNuevoCliente() {
  var ui = SpreadsheetApp.getUi();
  var resultado = ui.prompt(
    'Nuevo Cliente',
    'Razón Social:',
    ui.ButtonSet.OK_CANCEL
  );
  
  if (resultado.getSelectedButton() == ui.Button.OK) {
    var razonSocial = resultado.getResponseText();
    // Pedir más datos: RUC, teléfono, email, dirección
    // Agregar a base de datos
    agregarClienteABaseDatos(razonSocial, ruc, tel, email, dir);
  }
}
```

---

## 🧾 MÓDULO 2: FACTURA AUTOMATIZADA

### **RF-006: Creación de Factura desde Proforma**

**Descripción:**  
Desde una proforma aprobada, debe poder crearse una factura con 1 clic.

**Ubicación del Botón/Menú:**
```
Menú: "⚡ Zero Risk" → "Crear Factura desde Proforma"
```

**Flujo Completo:**

```javascript
function crearFacturaDesdeProforma() {
  var ui = SpreadsheetApp.getUi();
  
  // 1. Confirmar acción
  var respuesta = ui.alert(
    'Crear Factura',
    '¿Crear factura desde esta proforma?',
    ui.ButtonSet.YES_NO
  );
  
  if (respuesta !== ui.Button.YES) return;
  
  // 2. Leer datos de la proforma actual
  var proforma = SpreadsheetApp.getActiveSpreadsheet();
  var hojaProforma = proforma.getSheetByName('Proforma');
  
  var cliente = hojaProforma.getRange('C16').getValue();
  var productos = [];
  for (var i = 20; i <= 29; i++) {
    var producto = hojaProforma.getRange('C' + i).getValue();
    var cantidad = hojaProforma.getRange('B' + i).getValue();
    var precio = hojaProforma.getRange('E' + i).getValue();
    if (producto && cantidad && precio) {
      productos.push({
        nombre: producto,
        cantidad: cantidad,
        precio: precio
      });
    }
  }
  var subtotal = hojaProforma.getRange('F34').getValue();
  var iva = hojaProforma.getRange('F35').getValue();
  var total = hojaProforma.getRange('F36').getValue();
  
  // 3. Obtener número de factura
  var numeroFactura = obtenerNumeroFactura(); // Ej: 159
  
  // 4. Copiar template de factura
  var templateFactura = DriveApp.getFileById('ID_TEMPLATE_FACTURA');
  var carpetaDestino = DriveApp.getFolderById('ID_CARPETA_FACTURAS');
  var nombreArchivo = 'Factura_' + numeroFactura + '_' + cliente;
  var nuevaFactura = templateFactura.makeCopy(nombreArchivo, carpetaDestino);
  
  // 5. Abrir nueva factura y llenar datos
  var ssFactura = SpreadsheetApp.open(nuevaFactura);
  var hojaFactura = ssFactura.getSheetByName('Factura');
  
  hojaFactura.getRange('F10').setValue(numeroFactura); // Nº Factura
  hojaFactura.getRange('C11').setValue(new Date()); // Fecha
  hojaFactura.getRange('E8').setValue(cliente); // Cliente
  
  // Llenar productos
  var filaInicio = 20;
  for (var j = 0; j < productos.length; j++) {
    hojaFactura.getRange('C' + (filaInicio + j)).setValue(productos[j].nombre);
    hojaFactura.getRange('B' + (filaInicio + j)).setValue(productos[j].cantidad);
    hojaFactura.getRange('E' + (filaInicio + j)).setValue(productos[j].precio);
  }
  
  // 6. Mostrar mensaje de éxito
  ui.alert(
    '✅ Factura Creada',
    'Factura #' + numeroFactura + ' creada exitosamente.\n\n' +
    'Archivo: ' + nombreArchivo + '\n\n' +
    '¿Desea registrar en contabilidad ahora?',
    ui.ButtonSet.YES_NO
  );
  
  // 7. Si usuario dice SÍ, registrar en contabilidad
  if (respuesta === ui.Button.YES) {
    registrarFacturaEnContabilidad(numeroFactura, cliente, total, subtotal, iva);
  }
}
```

**Resultado:**
```
Entrada: Proforma_Exportquilsa_20Feb2026.xlsx
         (aprobada por cliente)

Salida:  Factura_159_Exportquilsa.xlsx
         (nueva, con todos los datos copiados)
         (número de factura: 159)
         (fecha: hoy)
```

---

### **RF-007: Contador Automático de Facturas**

**Descripción:**  
El sistema debe llevar un contador que se incrementa automáticamente cada vez que se crea una factura.

**Implementación:**

```javascript
function obtenerNumeroFactura() {
  var baseDatos = SpreadsheetApp.openById('ID_BASE_DATOS');
  var config = baseDatos.getSheetByName('Config');
  
  // Leer último número de factura
  var ultimoNumero = config.getRange('B8').getValue(); // Ej: 158
  
  // Incrementar
  var nuevoNumero = ultimoNumero + 1; // 159
  
  // Actualizar en base de datos
  config.getRange('B8').setValue(nuevoNumero);
  
  return nuevoNumero;
}
```

**Base de Datos → Hoja Config:**
```
A7: "Último Nº Proforma:"   B7: 45
A8: "Último Nº Factura:"    B8: 159
A9: "Último Nº Certificado:" B9: 12
```

**Numeración:**
```
Factura #158
Factura #159
Factura #160
...
```

---

### **RF-008: Creación de Factura desde Cero**

**Descripción:**  
Debe ser posible crear una factura directamente (sin proforma previa), usando el mismo sistema de listas desplegables.

**Flujo:**
```
1. Usuario duplica "FACTURA_TEMPLATE.xlsx"
2. Renombra: "Factura_160_NuevoCliente.xlsx"
3. Abre archivo
4. Sistema funciona igual que proforma:
   ✅ Listas desplegables en todos los campos
   ✅ Auto-llenado de datos de cliente
   ✅ Auto-llenado de precios
   ✅ Cálculos automáticos
5. Número de factura se asigna automáticamente
```

---

### **RF-009: Permitir Edición de Factura Antes de Finalizar**

**Descripción:**  
Al crear factura desde proforma, usuario debe poder hacer cambios antes de registrar en contabilidad.

**Flujo:**
```
1. Se crea factura desde proforma (datos copiados)
2. Usuario revisa factura
3. Usuario puede:
   ✅ Cambiar cantidades
   ✅ Agregar/quitar productos
   ✅ Modificar datos de cliente
   ✅ Cambiar fecha
4. Cuando todo esté correcto:
   Menú: "⚡ Zero Risk" → "Registrar en Contabilidad"
5. Sistema confirma: "¿Registrar factura #159 en contabilidad?"
6. Usuario: [Sí]
7. AHORA SÍ se registra (irreversible)
```

---

## 📊 MÓDULO 3: REGISTRO CONTABLE AUTOMÁTICO

### **RF-010: Registro Automático en Libro Diario**

**Descripción:**  
Al registrar una factura, debe crear 3 asientos contables automáticamente en el Libro Diario.

**Asientos Contables:**

```
EJEMPLO: Factura #159 - Exportquilsa
Subtotal: $540.00
IVA 15%: $81.00
Total: $621.00

LIBRO DIARIO:
-------------
Fecha: 22/02/2026
Asiento: #127
Doc: Factura
Nº Doc: 159
Cliente: Exportquilsa

Línea 1:
Cuenta: Cuentas por Cobrar
Debe: $621.00
Haber: $0.00

Línea 2:
Cuenta: Ventas
Debe: $0.00
Haber: $540.00

Línea 3:
Cuenta: IVA por Pagar
Debe: $0.00
Haber: $81.00
```

**Código:**

```javascript
function registrarFacturaEnContabilidad(numeroFactura, cliente, total, subtotal, iva) {
  var baseDatos = SpreadsheetApp.openById('ID_BASE_DATOS');
  var libroDiario = baseDatos.getSheetByName('Libro Diario');
  
  // Obtener última fila
  var ultimaFila = libroDiario.getLastRow() + 1;
  
  // Calcular número de asiento
  var numeroAsiento = ultimaFila - 2; // Porque fila 1=título, fila 2=headers
  
  // Fecha actual
  var fecha = new Date();
  
  // ASIENTO 1: Debe Cuentas por Cobrar
  libroDiario.getRange(ultimaFila, 1).setValue(fecha); // Fecha
  libroDiario.getRange(ultimaFila, 2).setValue(numeroAsiento); // Asiento
  libroDiario.getRange(ultimaFila, 3).setValue('Factura'); // Doc
  libroDiario.getRange(ultimaFila, 4).setValue(numeroFactura); // Nº Doc
  libroDiario.getRange(ultimaFila, 5).setValue(cliente); // Cliente
  libroDiario.getRange(ultimaFila, 6).setValue('Cuentas por Cobrar'); // Cuenta
  libroDiario.getRange(ultimaFila, 7).setValue('Venta según factura #' + numeroFactura); // Descripción
  libroDiario.getRange(ultimaFila, 8).setValue(total); // Debe
  libroDiario.getRange(ultimaFila, 9).setValue(0); // Haber
  
  // ASIENTO 2: Haber Ventas
  libroDiario.getRange(ultimaFila + 1, 1).setValue(fecha);
  libroDiario.getRange(ultimaFila + 1, 2).setValue(numeroAsiento);
  libroDiario.getRange(ultimaFila + 1, 3).setValue('Factura');
  libroDiario.getRange(ultimaFila + 1, 4).setValue(numeroFactura);
  libroDiario.getRange(ultimaFila + 1, 5).setValue(cliente);
  libroDiario.getRange(ultimaFila + 1, 6).setValue('Ventas');
  libroDiario.getRange(ultimaFila + 1, 7).setValue('Venta según factura #' + numeroFactura);
  libroDiario.getRange(ultimaFila + 1, 8).setValue(0); // Debe
  libroDiario.getRange(ultimaFila + 1, 9).setValue(subtotal); // Haber
  
  // ASIENTO 3: Haber IVA por Pagar
  libroDiario.getRange(ultimaFila + 2, 1).setValue(fecha);
  libroDiario.getRange(ultimaFila + 2, 2).setValue(numeroAsiento);
  libroDiario.getRange(ultimaFila + 2, 3).setValue('Factura');
  libroDiario.getRange(ultimaFila + 2, 4).setValue(numeroFactura);
  libroDiario.getRange(ultimaFila + 2, 5).setValue(cliente);
  libroDiario.getRange(ultimaFila + 2, 6).setValue('IVA por Pagar');
  libroDiario.getRange(ultimaFila + 2, 7).setValue('IVA factura #' + numeroFactura);
  libroDiario.getRange(ultimaFila + 2, 8).setValue(0); // Debe
  libroDiario.getRange(ultimaFila + 2, 9).setValue(iva); // Haber
  
  Logger.log('✅ Factura #' + numeroFactura + ' registrada en Libro Diario');
}
```

---

### **RF-011: Actualización Automática de Mayor General**

**Descripción:**  
El Mayor General debe calcular automáticamente los saldos de cada cuenta sumando del Libro Diario.

**Fórmulas:**

```excel
// Hoja: Mayor General
// Celda D3 (Debe de cuenta "Cuentas por Cobrar")
=SUMIFS('[Libro Diario]'!$H:$H, '[Libro Diario]'!$F:$F, B3)

// Celda E3 (Haber de cuenta "Cuentas por Cobrar")
=SUMIFS('[Libro Diario]'!$I:$I, '[Libro Diario]'!$F:$F, B3)

// Celda F3 (Saldo)
=D3-E3  // Para cuentas de Activo
// o
=E3-D3  // Para cuentas de Pasivo/Patrimonio/Ingresos
```

**Ejemplo Mayor General:**

```
Código    | Cuenta              | Tipo     | Debe      | Haber     | Saldo
----------|---------------------|----------|-----------|-----------|----------
1.2.01    | Cuentas por Cobrar  | Activo   | $12,450   | $7,100    | $5,350
2.2.01    | IVA por Pagar       | Pasivo   | $0        | $1,867.50 | $1,867.50
4.1.01    | Ventas              | Ingreso  | $0        | $12,450   | $12,450
```

---

### **RF-012: Registro en Cuentas por Cobrar**

**Descripción:**  
Al registrar factura, debe crear entrada en Cuentas por Cobrar con cálculo automático de vencimiento.

**Código:**

```javascript
function registrarEnCuentasPorCobrar(numeroFactura, cliente, total) {
  var baseDatos = SpreadsheetApp.openById('ID_BASE_DATOS');
  var cxc = baseDatos.getSheetByName('Cuentas x Cobrar');
  
  var ultimaFila = cxc.getLastRow() + 1;
  var fecha = new Date();
  
  // Calcular vencimiento (30 días después)
  var vencimiento = new Date(fecha);
  vencimiento.setDate(vencimiento.getDate() + 30);
  
  cxc.getRange(ultimaFila, 1).setValue(cliente); // Cliente
  cxc.getRange(ultimaFila, 2).setValue(numeroFactura); // Nº Factura
  cxc.getRange(ultimaFila, 3).setValue(fecha); // Fecha Emisión
  cxc.getRange(ultimaFila, 4).setValue(vencimiento); // Vencimiento
  cxc.getRange(ultimaFila, 6).setValue(total); // Total
  cxc.getRange(ultimaFila, 7).setValue(0); // Pagado (inicial = 0)
  
  // Columna 5 (Días): fórmula automática =TODAY()-C{fila}
  // Columna 8 (Saldo): fórmula automática =F{fila}-G{fila}
  // Columna 9 (Estado): fórmula automática basada en días
}
```

---

### **RF-013: Actualización de Estado de Resultados**

**Descripción:**  
El Estado de Resultados debe mostrar ingresos y gastos actualizados en tiempo real.

**Fórmulas:**

```excel
// Hoja: Estado de Resultados

// Celda D7 (Ventas de Servicios)
=SUMIFS('[Libro Diario]'!$I:$I, '[Libro Diario]'!$F:$F, "Ventas")

// Celda D13 (Gastos de Operaciones)
=SUMIFS('[Libro Diario]'!$H:$H, '[Libro Diario]'!$F:$F, "Gastos Operaciones")

// Celda D9 (Total Ingresos)
=D7+D8

// Celda D19 (Total Gastos)
=SUM(D13:D18)

// Celda D21 (Utilidad/Pérdida)
=D9-D19
```

---

## 📈 MÓDULO 4: DASHBOARD EJECUTIVO

### **RF-014: KPIs en Tiempo Real**

**Descripción:**  
Dashboard debe mostrar métricas clave actualizadas automáticamente.

**KPIs Principales:**

```excel
// KPI 1: Clientes Activos
Celda: D5
Fórmula: =COUNTIF('[BASE_DATOS]Clientes'!H:H, "Activo")

// KPI 2: Total Facturado (Mes Actual)
Celda: D8
Fórmula: =SUMIFS('[Libro Diario]'!H:H, 
                 '[Libro Diario]'!C:C, "Factura",
                 '[Libro Diario]'!A:A, ">="&DATE(YEAR(TODAY()), MONTH(TODAY()), 1))

// KPI 3: Total Por Cobrar
Celda: D11
Fórmula: =SUM('[Cuentas x Cobrar]'!H:H)

// KPI 4: Facturas Vencidas
Celda: D14
Fórmula: =COUNTIF('[Cuentas x Cobrar]'!I:I, "Vencido")

// KPI 5: Utilidad del Mes
Celda: D17
Fórmula: ='[Estado Resultados]'!D21
```

---

### **RF-015: Alertas Automáticas**

**Descripción:**  
Dashboard debe mostrar alertas visuales para situaciones críticas.

**Formato Condicional:**

```
SI: Facturas Vencidas > 0
ENTONCES: Celda D14 en ROJO con ícono ⚠️

SI: Por Cobrar > $5,000
ENTONCES: Celda D11 en AMARILLO con ícono 💰

SI: Utilidad < 0
ENTONCES: Celda D17 en ROJO con ícono ⚠️
```

---

## 🔄 FLUJO COMPLETO - CASO DE USO DOCUMENTADO

### **Escenario Real: Venta a Cliente Exportquilsa**

**PASO 1: Crear Proforma**
```
Usuario duplica: PROFORMA_TEMPLATE.xlsx
Renombra: Proforma_Exportquilsa_22Feb2026.xlsx
Abre archivo

Celda C16: Clic → Lista desplegable → Selecciona "Exportquilsa"
Auto-llena: RUC, Teléfono, Email, Dirección

Celda C20: Clic → Lista desplegable → "Recarga extintor 20 Lbs PQS"
Auto-llena: Precio $30.00
Celda B20: Escribe 15
Auto-calcula: Total $450.00

Celda C21: Clic → "Extintor CO2 10 lb"
Auto-llena: Precio $30.00
Celda B21: Escribe 3
Auto-calcula: Total $90.00

Subtotal: $540.00 (automático)
IVA 15%: $81.00 (automático)
TOTAL: $621.00 (automático)

Guardar → Enviar PDF a cliente
```

**PASO 2: Cliente Aprueba (3 días después)**
```
Abrir: Proforma_Exportquilsa_22Feb2026.xlsx
Menú: "⚡ Zero Risk" → "Crear Factura desde Proforma"
Sistema pregunta: "¿Crear factura ahora?"
Usuario: [Sí]

AUTOMÁTICAMENTE:
✅ Crea archivo: Factura_159_Exportquilsa.xlsx
✅ Copia todos los datos
✅ Asigna número: 159
✅ Fecha: 22/02/2026

Sistema pregunta: "¿Registrar en contabilidad?"
Usuario: [Sí]
```

**PASO 3: Registro Contable Automático**
```
AUTOMÁTICAMENTE:

LIBRO DIARIO (3 nuevos asientos):
- Asiento #127
- Debe: Cuentas por Cobrar $621.00
- Haber: Ventas $540.00
- Haber: IVA por Pagar $81.00

MAYOR GENERAL (actualizado):
- Cuentas por Cobrar: +$621.00
- Ventas: +$540.00
- IVA por Pagar: +$81.00

CUENTAS POR COBRAR (nueva entrada):
- Cliente: Exportquilsa
- Factura: 159
- Fecha: 22/02/2026
- Vencimiento: 24/03/2026
- Total: $621.00
- Saldo: $621.00
- Estado: Al Día

ESTADO DE RESULTADOS (actualizado):
- Ventas: +$540.00
- Utilidad: recalculada

DASHBOARD (actualizado):
- Total Facturado (Mes): +$621.00
- Por Cobrar: +$621.00
- Facturas del Mes: +1

Mensaje: "✅ Factura #159 registrada exitosamente"
```

**PASO 4: Cliente Paga (30 días después)**
```
Usuario abre: Cuentas x Cobrar
Busca: Factura #159
Celda G{fila} (Pagado): Escribe $621.00

AUTOMÁTICAMENTE:
- Saldo cambia a: $0.00
- Estado cambia a: "✓ Pagado"
- Dashboard actualiza: Por Cobrar -$621.00
```

---

## ✅ RESUMEN DE REQUERIMIENTOS

| ID | Módulo | Descripción | Prioridad |
|----|--------|-------------|-----------|
| RF-001 | Proforma | Formulario inteligente con listas desplegables | Alta |
| RF-002 | Proforma | Numeración automática | Alta |
| RF-003 | Proforma | Auto-llenado datos cliente | Alta |
| RF-004 | Proforma | Auto-llenado precios | Alta |
| RF-005 | Proforma | Opción "Agregar Nuevo" | Media |
| RF-006 | Factura | Creación desde proforma (1 clic) | Alta |
| RF-007 | Factura | Contador automático | Alta |
| RF-008 | Factura | Creación desde cero | Media |
| RF-009 | Factura | Permitir edición antes de registrar | Alta |
| RF-010 | Contabilidad | Registro automático Libro Diario | Alta |
| RF-011 | Contabilidad | Actualización Mayor General | Alta |
| RF-012 | Contabilidad | Registro Cuentas por Cobrar | Alta |
| RF-013 | Contabilidad | Estado Resultados en tiempo real | Alta |
| RF-014 | Dashboard | KPIs automáticos | Media |
| RF-015 | Dashboard | Alertas visuales | Media |

---

## 📝 NOTAS TÉCNICAS

### **Tecnología:**
- Google Sheets (front-end)
- Google Apps Script (back-end, JavaScript)
- IMPORTRANGE (conexiones entre archivos)

### **Arquitectura:**
- Modular (archivos independientes)
- Base de datos centralizada
- Automatización mediante menús y scripts

### **Seguridad:**
- Permisos de solo lectura en Base de Datos
- Historial de versiones en Google Drive
- Backup automático diario

---

**Documento elaborado por:** Emilio Pérez  
**Fecha:** Febrero 2026  
**Versión:** 1.0 - Especificación Inicial
