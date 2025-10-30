# GPi Case 2 + Raspberry Pi CM4 (2GB Lite) — Retro Handheld

[![status](https://img.shields.io/badge/status-working-brightgreen)](#)
[![os](https://img.shields.io/badge/OS-Recalbox%2064--bit-blue)](#)
[![hardware](https://img.shields.io/badge/Hardware-CM4%202GB%20Lite%20%2B%20GPi%20Case%202-lightgrey)](#)
[![license](https://img.shields.io/badge/license-MIT-black)](LICENSE)

Consola portátil basada en **RetroFlag GPi Case 2 Deluxe** + **Raspberry Pi CM4 (Lite)**.
Este repo documenta el proyecto **de principio a fin**, incluidas las decisiones y los problemas reales que aparecieron, para que cualquiera pueda replicarlo en menos de una hora.

---

## Demo
- Capturas: en [`docs/images`](docs/images)

---

## Objetivo
Construir una portátil **limpia y reproducible** que emule hasta **GBA** y dejar constancia de:
- decisiones técnicas (SO, hardware),
- problemas y cómo los resolví,
- pasos claros para que otra persona lo copie.

---

## Materiales y coste
> Detalle ampliado en [`docs/bill-of-materials.md`](docs/bill-of-materials.md)

| Componente                             | Comentario                         | Precio aprox. (€) |
|---------------------------------------|------------------------------------|-------------------|
| RetroFlag **GPi Case 2 Deluxe**       | Carcasa + dock + batería 4000 mAh  | 70–130            |
| **Raspberry Pi CM4 Lite (2GB, Wi-Fi)**| Sin eMMC (arranca por microSD)     | 60–100            |
| microSD **64 GB A1** (SanDisk/Samsung)| Sistema + ROMs                     | 9–20              |
| Lector microSD USB-C                  | Para grabar la SD                  | 6–12              |

> ⚠️ No se distribuyen **ROMs/BIOS**.

---

## Guía rápida (15 minutos)
1. Graba **Recalbox (RPi4/400/CM4 – 64-bit)** en la microSD con **Raspberry Pi Imager**.
2. Primer arranque **en el dock** (con **USB-C 5V/3A**); espera la instalación inicial.
3. Si la **LCD** no muestra imagen, instala el **display patch** de RetroFlag (Windows: `install_patch.bat`).
4. Copia ROMs por red:  
   - Windows: `\\RECALBOX\roms\`  
   - macOS/Linux: `smb://recalbox/roms/`  
5. Start → Update Game Lists → ¡jugar!

> Guía paso a paso con fotos: [`docs/build-guide.md`](docs/build-guide.md)

---

## Decisiones clave (y por qué)
- **Recalbox** frente a **Batocera v41**: Recalbox ofrece experiencia **plug-and-play** con GPi Case 2; Batocera necesitaba **scripts/parches** para la LCD (v40/v41).
- **CM4 Lite** (sin eMMC): garantiza arranque por **microSD**, evita tener que flashear eMMC.
- **Validación con Raspberry Pi OS**: sirvió para separar **hardware** de **configuración** (si sale vídeo en HDMI, el módulo y la SD están bien).

## Diagrama rápido de diagnóstico

```mermaid
flowchart TD
  A[Montaje CM4 + microSD] --> B{Arranca}
  B -- HDMI OK --> C[Recalbox rpi4_64]
  C --> D{LCD funciona}
  D -- Si --> E[Configurar y jugar]
  D -- No --> F[Aplicar display patch RetroFlag]
  B -- No --> G[Probar Raspberry Pi OS 64-bit]
  G -- HDMI OK --> H[Revisar configuracion del SO anterior]
  G -- No --> I[Revisar SD o ranura CM4 contacto]
