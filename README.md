# 🎨 Macro M600 para Cambio de Filamento en Klipper

## 📌 Descripción

Esta macro permite realizar cambios de filamento en impresoras 3D con Klipper sin generar sobreextrusión al reanudar la impresión. Está optimizada para evitar residuos en la pieza y garantizar una transición de color limpia.

### 🔹 ¿Qué hace esta macro?
✅ Pausa la impresión automáticamente al llegar al cambio de color/filamento.  
✅ Retrae el filamento y carga el nuevo sin extrusión excesiva.  
✅ Activa el modo de espera en la pantalla de la impresora.  
✅ Si está habilitado el **buzzer (M300)**, emite un sonido de aviso.  
✅ Maneja la carga y descarga de filamento de forma automática (80 mm por defecto).  
✅ No deja restos de filamento en la pieza al retomar la impresión.  

---

## ⚙️ Instalación y Configuración

### 📁 Agregar las macros en `printer.cfg`
Las siguientes macros deben añadirse en **cualquier parte** del archivo `printer.cfg`.

### 📌 Macro M600 (Cambio de filamento)
```ini
[gcode_macro M600]
gcode:
    {% set B = (params.B|default(2)|int,0)|max %}
    {% set E = (params.E|default(1)|float,0.0)|max %}
    {% set U = (params.U|default(80)|float,0.0)|max %}
    {% set Z = (params.Z|default(30)|float,0.0)|max %}
    SAVE_GCODE_STATE NAME=M600_state
    PAUSE
    G91
    G1 E-50 F2700
    G1 Z{Z}
    G90
    G1 X10 Y250 F3000
    G91
    G1 E-{U} F900
    {% for n in range(B) %}
        M300 P1000 S1000
        G4 P1000
    {% endfor %}
    G91
    G1 E{U} F600
    G1 E5 F300
    G90
    G1 X10 Y250 F3000
    G1 E10 F300
    G1 E-3 F300
    RESTORE_GCODE_STATE NAME=M600_state MOVE=0
```

### 📌 Macro M300 (Sonido de aviso)
```ini
[gcode_macro M300]
#Aviso sonoro para Artillery SW x4 Pro
#Descripcion: Emite un aviso sonoro
	Usage: M300 [P<duration>] [S<frequency>]
gcode:
	# Replace params with default parameters if missing (and scale up to minimum)
	{% set P = (params.P|default(100)|int,0)|max %}
	{% set S = (params.S|default(1000)|int,1)|max %}
	
	# Start beep, wait <duration> and end beep
	SET_PIN PIN=beeper VALUE=0.5 CYCLE_TIME={ 1.0 / S }
	G4 P{P}
	SET_PIN PIN=beeper VALUE=0
```

### 📌 Configuración del Beeper (Opcional)
Si tu impresora cuenta con un **buzzer**, puedes asignar el pin correspondiente:
```ini
[output_pin beeper]
#pin de aviso sonoro para Artillery SW x4 Pro/Plus
pin: PA2
pwm: True
value: 0
shutdown_value: 0
```

---

## 🛠️ Configuración en el Laminador
Para que el cambio de filamento se realice correctamente, debes configurar tu **laminador**.

### 🔹 PrusaSlicer / SuperSlicer
1. Ir a **Configuración de la impresora**.
2. En **G-Code personalizado**, buscar **G-Code de cambio de color**.
3. Agregar:
   ```
   M600
   ```
4. En la barra lateral, seleccionar **Cambio de color** y añadir los cambios deseados.

### 🔹 OrcaSlicer
1. Ir a **Ajustes de la impresora** > **G-Code de la máquina**.
2. Buscar **G-Code para cambio de filamento**.
3. Agregar:
   ```
   M600
   ```
4. En la barra lateral, seleccionar **Cambio de filamento** y añadir los cambios.

---

## ✅ Beneficios
🔹 Evita extrusión excesiva en la pieza al retomar la impresión.  
🔹 Filamento cargado automáticamente sin necesidad de extrusión manual.  
🔹 Compatible con múltiples laminadores y configuraciones de Klipper.  
🔹 Fácil de integrar y personalizar.  

---

## 🔗 Recursos y Créditos
- 📄 [Documentación oficial de Klipper](https://www.klipper3d.org/)
- 💬 Soporte en [Klipperianos Whatsapp](https://chat.whatsapp.com/IHaUnmBsNPnJ1kDIenCrmT)

📢 ## Conéctate conmigo
🔗 **TikTok:** [Fuzion3D](https://www.tiktok.com/@fuzion3d)
📸 **Instagram:** [Fuzion3D Crea](https://www.instagram.com/fuzion3dcrea)
🎥 **YouTube:** [Fuzion3D Crea](https://youtube.com/@fuzion3dcrea)
💬 **WhatsApp:** [Klipperianos Whatsapp](https://chat.whatsapp.com/IHaUnmBsNPnJ1kDIenCrmT)

Si encuentras útil esta macro, ⭐ ¡dale una estrella a este repositorio en GitHub! 🚀
