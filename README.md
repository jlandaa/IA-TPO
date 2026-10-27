# Trabajo Práctico Obligatorio - Integración de Aplicaciones (2C2016)

## 📌 Descripción General
Este repositorio contiene el desarrollo del **Trabajo Práctico Obligatorio (TPO)** para la materia Integración de Aplicaciones (UADE). 

El objetivo del proyecto consiste en implementar un sistema distribuido que simule el **flujo completo de compra y entrega de pedidos** de un Portal Web de compras de artículos. El sistema está dividido en diferentes submódulos autónomos que deben integrarse para cumplir con los objetivos de negocio, garantizando la trazabilidad, el monitoreo y el manejo de auditorías.

## 🏗️ Arquitectura y Módulos del Sistema

El sistema está compuesto por 6 módulos principales, los cuales se comunican de forma asincrónica y sincrónica (mediante REST + JSON y/o colas de mensajería):

### 1. 🛍️ Portal Web de Compras
Interfaz principal para los clientes del sistema.
* **Funciones:** Búsqueda y selección de artículos (carrito de compras), checkout (efectivización de la venta), consulta del estado de entrega y visualización del ranking de *Best Sellers*.
* **Integración:** Envía ventas a Logística de forma sincrónica, recibe estados de entrega de Despacho y recibe el ranking de productos más vendidos.

### 2. 📊 Logística y Monitoreo
Encargado de orquestar los despachos, recolectar información del sistema y procesar las auditorías.
* **Funciones:** Recepción de ventas, asignación automática de órdenes de despacho (basada en proximidad geográfica al cliente), reportes de ventas y cálculo/envío de *Best Sellers*.
* **Auditoría:** Recibe eventos generados por todos los módulos del sistema (de manera sincrónica/asincrónica configurable) y expone un visualizador online de los mismos.

### 3. 🚚 Despacho
Módulo utilizado por los operadores logísticos para manejar la entrega final.
* **Funciones:** Procesa órdenes de despacho pendientes, solicita stock a los Depósitos y notifica a los portales/logística una vez que los productos son entregados al cliente final.
* **Backoffice:** Permite administrar usuarios, listar órdenes y trackear solicitudes.

### 4. 📦 Depósito
Módulo de gestión de inventario para los operadores del depósito.
* **Funciones:** Administración de artículos (wizard de creación) y stock. Envía solicitudes de compra a Fábrica cuando el stock es insuficiente, recibe la mercadería y entrega los artículos a los centros de Despacho.

### 5. 🏭 Fábrica
Simula la manufactura y aprovisionamiento de stock.
* **Funciones:** Recibe solicitudes de compra desde los Depósitos mediante REST + JSON, procesa las entregas enviando las compras de forma asincrónica y permite exportar estados a `.xls`.

### 6. 📧 Email
Módulo receptor de ítems de auditoría.
* **Funciones:** Recibe notificaciones y alertas desde el módulo de monitoreo de forma asincrónica y las lista por interfaz web (el envío SMTP de correos real queda fuera de alcance).

## 🔄 Flujo de Trabajo (Resumen)

1. El **Depósito** crea los artículos e informa al **Portal** y **Despacho**.
2. Un cliente compra en el **Portal Web**; se informa a **Logística**.
3. **Logística** asigna y envía la *Orden de Despacho* al **Despacho**.
4. **Despacho** solicita los artículos necesarios al **Depósito**.
5. Si no hay stock, el **Depósito** crea una *Solicitud de Compra* a la **Fábrica**.
6. La **Fábrica** entrega la compra al **Depósito**.
7. El **Depósito** envía los artículos solicitados al **Despacho**.
8. El **Despacho** consolida el pedido y notifica la *Entrega* al **Portal** y a **Logística**.

*(Todos los pasos informan paralelamente eventos de auditoría y logueo al módulo de Logística/Monitoreo).*

## ⚙️ Condiciones Generales y Requisitos Técnicos

* **Arquitectura Tecnológica:** La aplicación debe funcionar sobre una base de datos **MS SQL Server** y estar desplegada en un servidor de aplicaciones **JBoss**.
* **Integración:** Las comunicaciones sincrónicas utilizan tecnología **REST + JSON**. Se requiere una pantalla de configuración para setear IPs y métodos de integración.
* **Frontend:** Todas las interfaces gráficas deben ser amigables, intuitivas y **Responsive**.
* **Manejo de Errores:** Se debe implementar un logeo robusto en archivos de texto locales (guardando stacktraces, fecha, hora, etc.).
* **Documentación:** Se incluyen diagramas de secuencia, diagramas de clase (UML estándar), capturas de pantalla y manual de instalación (deploy).

---
*Desarrollado para el 2° Cuatrimestre de 2016.*
