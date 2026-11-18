# Driver de Potencia de 4 Canales DC (Dual L293DNE)

> **Universidad Anáhuac** | Ingeniería Mecatrónica  
> **Autor:** José Daniel Bobadilla Vega  
> **Revisión:** Primera Revisión (18/11/2026)  
> **Versión de Hardware:** v1.0 | **Costo Total:** ~$200.00 MXN

---

## 1. Resumen General (Overview)

Etapa de potencia autónoma (sin microcontrolador) diseñada para controlar hasta **4 motores de corriente continua (DC)** de forma independiente o simultánea con inversión de giro bidireccional (`CW` / `CCW`) mediante dos circuitos integrados **L293DNE** (doble puente H completo por integrado).

- **Alimentación de Potencia (`VCC2`):** +12V DC nominales vía bornera `J4` protegida por fusible `XF1`.
- **Regulación Lógica Integrada (`VCC1`):** +5V DC mediante regulador lineal `U1` (TO-220) y capacitores de desacoplo `C1` y `C2` (0.1 µF).
- **Inmunidad al Ruido:** Red de 8 resistencias en configuración **Pull-Down** (`R1, R3–R9`) en la bornera de control `J1`.
- **Diagnóstico Visual:** 9 indicadores LED verdes de 3mm (`DS5` encendido general + `DS1–DS4` y `DS6–DS9` en salidas de motor).

---

## 2. Arquitectura del Circuito por Etapas

### 2.1 Etapa de Alimentación y Protección (`J4`, `XF1`, `J6`, `DS5`, `R13`)
- **Bornera `J4` (2 pines):** Entrada principal `+12V_IN` y `GND`.
- **Portafusibles `XF1`:** Conectado en serie con `+12V_IN` para protección contra sobrecorriente y cortocircuito.
- **Bornera `J6` (2 pines):** Puente de habilitación general hacia el bus `+12V`.
- **Testigo `DS5` + `R13` (1 kΩ):** Indicador LED verde de presencia de tensión en el bus de `+12V`.

### 2.2 Etapa de Regulación Lógica +5V (`U1`, `C1`, `C2`, `J3`)
- **Regulador `U1` (TO-220):** Convierte `+12V` a `+5V` para alimentar `VCC1` (pin 16) y los pines de habilitación `1/2EN` (pin 1) y `3/4EN` (pin 9) de ambos L293DNE.
- **Capacitores `C1` y `C2` (0.1 µF, 50V, X7R):** Filtrado de alta frecuencia en entrada y salida del regulador.
- **Bornera `J3` (8 pines):** Distribuye el bus de `+5V` hacia los interruptores externos.

### 2.3 Etapa de Control y Red Pull-Down (`J1`, `R1`, `R3–R9`)
- **Bornera `J1` (8 pines):** Recibe las señales `U1_IN1..U1_IN4` (hacia `U2`) y `U2_IN1..U2_IN4` (hacia `U3`).
- **Resistencias Pull-Down (`R1, R3, R4, R5, R6, R7, R8, R9`):** Fijan `0V` (LOW seguro) cuando los interruptores están abiertos, evitando activaciones falsas por ruido.

### 2.4 Etapa de Potencia y Diagnóstico Visual (`U2`, `U3`, `J2`, `J5`, `J7`, `J8`)
- **Driver `U2` (L293DNE #1):** Controla **Motor 1 (`J2`)** con LEDs `DS1/DS2` (`R2, R10` = 1 kΩ) y **Motor 2 (`J5`)** con LEDs `DS3/DS4` (`R11, R12` = 1 kΩ).
- **Driver `U3` (L293DNE #2):** Controla **Motor 3 (`J7`)** con LEDs `DS6/DS7` (`R14, R15` = 1 kΩ) y **Motor 4 (`J8`)** con LEDs `DS8/DS9` (`R16, R17` = 1 kΩ).

---

## 3. Tabla de Verdad por Puente H (`EN = +5V`)

| Enable (1/2EN, 3/4EN) | IN_A | IN_B | Salida Y_A / Y_B | LEDs (DS_A / DS_B) | Estado del Motor DC |
| :--- | :--- | :--- | :--- | :--- | :--- |
| HIGH (+5V) | LOW (0V) | LOW (0V) | 0V / 0V | OFF / OFF | Motor Detenido (Freno Bajo) |
| HIGH (+5V) | HIGH (+5V) | LOW (0V) | +12V / 0V | ON / OFF | Giro Horario (CW) |
| HIGH (+5V) | LOW (0V) | HIGH (+5V) | 0V / +12V | OFF / ON | Giro Antihorario (CCW) |
| HIGH (+5V) | HIGH (+5V) | HIGH (+5V) | +12V / +12V | ON / ON | Freno Rápido Activo Alto |

---

## 4. Adaptaciones de Manufactura en Placa Física (DFM) y Carcasa

1. **Bases / Zócalos DIP-16 en `U2` y `U3`:** Se instalaron bases portapines de 16 terminales para evitar daño térmico a los L293DNE durante la soldadura y permitir su reemplazo inmediato.
2. **Portafusibles `XF1` Adaptado:** Adecuación física del portafusibles comercial manteniendo la protección en serie en `+12V_IN`.
3. **Carcasa Protectora (Casing):** Fabricada a medida sin CAD para aislar eléctricamente la cara inferior de soldadura, proteger las pistas y brindar acceso directo a las 8 borneras perimetrales y visibilidad de los 9 LEDs testigos.

---

## 5. Lista de Materiales (BOM) y Presupuesto (~$200.00 MXN)

| Designadores | Descripción | Encapsulado | Cant. | Subtotal (MXN) |
| :--- | :--- | :--- | :---: | :---: |
| `U2, U3` | Driver Doble Puente H L293DNE | DIP-16 | 2 | $56.00 |
| `Zócalos DIP` | Bases portapines de 16 pines | DIP-16 | 2 | $8.00 |
| `U1` | Regulador Lineal +5V (LM7805) | TO-220 | 1 | $10.00 |
| `C1, C2` | Capacitor Cerámico 0.1 µF 50V X7R | Radial | 2 | $5.00 |
| `J1, J3` | Bornera de 8 pines | THT | 2 | $28.00 |
| `J2, J4, J5, J6, J7, J8` | Borneras de 2 pines | THT | 6 | $27.00 |
| `DS1 – DS9` | LED Verde 3mm Diffused (`LTL-1CHG`) | 3mm T-1 | 9 | $9.00 |
| `R1, R3–R9` | Resistencias Pull-Down (10kΩ / 100kΩ) | Axial 0.25W | 8 | $4.00 |
| `R2, R10–R17` | Resistencias para LED 1 kΩ | Axial 0.25W | 9 | $4.50 |
| `XF1` | Portafusibles + Fusible | THT | 1 | $12.50 |
| `PCB + Carcasa` | Placa fenólica/FR4, soldadura y carcasa | Custom | 1 | $36.00 |
| **TOTAL** | **43 Componentes** | — | **43** | **~$200.00 MXN** |
