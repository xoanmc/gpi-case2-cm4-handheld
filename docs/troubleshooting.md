## 1) LCD en negro con Batocera (v41)

**Síntoma**
- Pantalla LCD de la GPi Case 2 iluminada pero **sin imagen**.
- En TV por HDMI, **negro**.
- El CM4 calentaba (algún arranque había), pero no mostraba UI.

**Hipótesis**
- El sistema está sacando vídeo por **HDMI** y la LCD (DPI) no está configurada.
- La **v41** de Batocera cambió la pila gráfica; el **parche oficial** de pantalla puede no ser suficiente.

**Acciones**
1. **Forcé HDMI** para confirmar vida del SO (en `/boot/config.txt`):
   ```ini
   arm_64bit=1
   disable_splash=1
   hdmi_force_hotplug=1
   hdmi_group=1
   hdmi_mode=4
   dtoverlay=vc4-kms-v3d-pi4,noaudio
   max_framebuffers=2
   ```

Verifiqué que el parche LCD que probé contenía errores:

Bloque [DPI] (no existe en config de Raspberry Pi).

`dpi_output_format=0x00016` (faltaba un 0 → debe ser `0x000016`).

Probé scripts comunitarios para GPi Case 2 (Batocera 40/41):

```bash
# v40/v41:
ssh root@batocera.local  # pass: linux
wget -O - "https://raw.githubusercontent.com/henryjfry/batocera_gpi2/main/batocera40_install_gpi2.sh" | bash
reboot

# Alternativa 41+:
wget -O - "https://raw.githubusercontent.com/MrDuckHunt79/gpic2bato/main/install.sh" | bash
reboot
```

**Resultado**
- HDMI sí llegó a mostrar boot/instalación en alguna prueba → hardware vivo.
- La LCD seguía sin señal estable → compatibilidad parcial con v41 en mi unidad.

**Conclusión**
- El problema era de software/compatibilidad (no de hardware).
- Decidí cambiar a **Recalbox (rpi4_64)**, que RetroFlag recomienda para GPi Case 2.

---

## 2) Validación de hardware con Raspberry Pi OS (64-bit)

**Objetivo**
Separar hardware de configuración.

**Acciones**
- Grabé Raspberry Pi OS 64-bit con Raspberry Pi Imager.
- Arranqué en HDMI.

**Resultado**
- Pantalla de bienvenida de Raspberry Pi Desktop.
→ ✅ CM4 OK, ✅ microSD OK, ✅ vídeo HDMI OK.

**Conclusión**
Confirmado: el problema anterior no era hardware.

---

## 3) Bootloader: “SD: card not detected” (intermitente)

**Síntoma**
- En algunos arranques aparecía pantalla de bootloader:
  `SD: card not detected`
- Intentaba arrancar por NVMe/USB/Red.
- Intermitente: otras veces sí leía la SD y arrancaba.

**Diagnóstico**
- Probé otra microSD → mismo mensaje.
- Reinserté la SD varias veces → a veces funcionaba.
- Revisé el CM4: Lite (CM4102000) → correcto para arrancar por SD.

**Conclusión**
Contacto físico irregular (slot microSD del cartucho / CM4 no totalmente plano / presión insuficiente).

**Acciones**
- Reasenté el CM4 presionando uniforme hasta que quedara totalmente plano.
- Limpié contactos dorados de la SD con isopropanol; inserté hasta clic.
- Probé arrancar presionando suavemente la SD → a veces funcionaba → evidencia de slot flojo o tolerancias mecánicas.
- Si reaparece: RMA del cartucho.

**Conclusión**
- No era software: el mensaje de bootloader aparece antes de leer el SO.
- El origen fue mecánico (contacto intermitente SD/slot).

---

## 4) Recalbox 64-bit + botones que no responden en HDMI

**Síntoma**
- Recalbox rpi4_64 arrancó por HDMI (“Welcome to Recalbox”), pero:
  - Los botones de la GPi Case 2 no respondían.
  - Un mando PS4 por USB tampoco reaccionaba (cable quizá solo de carga).

**Diagnóstico**
- Los botones del GPi Case 2 van por GPIO, no USB.
- Necesito instalar el display patch de RetroFlag: además de redirigir vídeo a la LCD, activa drivers de entrada y safe-shutdown.

**Acciones**
1. Conecté teclado USB al dock para poder moverme:
   - Enter = Start
   - F4 = salir de EmulationStation
2. En Windows, ejecuté el parche oficial (SD en el PC):
   - Copiar carpeta del patch a la raíz de la SD.
   - Abrir la carpeta → `install_patch.bat` → “Successful configuration”.
   - Reinicié en modo portátil.

**Resultado**
✅ LCD funcionando.  
✅ Botones operativos.  
✅ Safe-Shutdown (opcional) y autoswitch.

**Conclusión**
En Recalbox, el patch de RetroFlag soluciona de una vez vídeo + entrada para GPi Case 2.

---

## 5) Config LCD manual (referencia)

No fue necesaria al final con Recalbox + patch, pero dejo la versión correcta como referencia (los errores más comunes eran el bloque [DPI] y el valor de dpi_output_format):

```ini
[rpi4]
dtoverlay=vc4-fkms-v3d
max_framebuffers=1

[all]
display_rotate=0
dtoverlay=dpi24
overscan_left=0
overscan_right=0
overscan_top=0
overscan_bottom=0
framebuffer_width=640
framebuffer_height=480
enable_dpi_lcd=1
display_default_lcd=1
dpi_group=2
dpi_mode=87
dpi_output_format=0x000016
hdmi_timings=640 0 41 40 41 480 0 18 9 18 0 0 0 60 0 24000000 1
```

---

## 6) Estado final

**SO:** Recalbox (rpi4_64)  
**Hardware:** CM4 Lite 2GB + GPi Case 2 + microSD 64GB A1

**Funciona:**
✅ LCD (DPI) + botones nativos (GPIO)  
✅ Guardado/carga de estados (RetroArch)  
✅ Dock HDMI (con alimentación USB-C 5V/3A)  
✅ Emulación fluida hasta GBA  

---

## 7) Lecciones aprendidas (claves)

- Validar hardware con Raspberry Pi OS antes de dedicar horas a parches.
- En GPi Case 2, el HDMI es la salida por defecto; la LCD necesita patch/config.
- Batocera v40/v41 puede requerir scripts de comunidad; Recalbox fue “plug-and-play”.
- “SD: card not detected” → casi siempre contacto (slot/SD/CM4), no software.
- Tener un teclado USB a mano evita bloqueos al principio.

---

## 8) Evidencias (añade tus fotos en docs/images)

| Estado | Imagen | Descripción |
|---------|--------|-------------|
| ✅ Recalbox instalando | docs/images/recalbox_welcome.jpg | Instalador en HDMI (dock alimentado) |
| ⚠️ Bootloader error | docs/images/sd_card_not_detected.jpg | Mensaje real “SD: card not detected” |
| ✅ Sistema final | docs/images/recalbox_ok.jpg | EmulationStation funcionando |

> Las imágenes no se incluyen aquí por tamaño/privacidad. Añádelas en `docs/images` y actualiza los nombres.

---

## 9) Atajos útiles (Recalbox/RetroArch)

- **Select + Start** → salir al menú  
- **Select + Y** → guardar estado  
- **Select + X** → cargar estado  
- **Select + B** → menú rápido  

**Botón TURBO (GPi Case 2)**  
- Turbo: mantener TURBO y pulsar A/B/X/Y/L/R  
- Auto-Turbo: TURBO + doble pulsación  
- Swap: mantener 2 botones + TURBO  
- Slow-motion: Auto-Turbo en Start  

⚠️ No se puede usar Turbo y Swap a la vez en el mismo botón.

---

## 10) Referencias rápidas

- **Recalbox (rpi4/400/CM4 64-bit):** descarga oficial.  
- **RetroFlag GPi Case 2:** display patch y safe-shutdown.  
- **Scripts comunidad GPi Case 2 para Batocera 40/41.**  

Los enlaces exactos están en `docs/decisions.md` y en el `README` principal.

---

## Cierre

Este documento no pretende ser una guía universal, sino una bitácora real con mis hipótesis, pruebas y decisiones.  
