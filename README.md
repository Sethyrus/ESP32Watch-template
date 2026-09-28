# ESP32Watch-template

Plantilla para crear firmwares nuevos para la Waveshare **ESP32-S3-Touch-AMOLED-2.06** con `ESP-IDF 5.5.4` + `LVGL 9` + BSP oficial de Waveshare.

Incluye una pantalla de prueba (LVGL + BSP), la configuracion de la placa (`sdkconfig.defaults`, `partitions.csv`) y la dependencia a [ESP32Watch-core](https://github.com/Sethyrus/ESP32Watch-core) (`watch_board`: IMU y botones).

## Crear una app nueva desde esta plantilla

1. En GitHub, **Use this template** > **Create a new repository** (por ejemplo `ESP32Watch-MiApp`), y clonarlo.
2. Cambiar el nombre del proyecto en `CMakeLists.txt` (`project(ESP32WatchApp)`) y en `.devcontainer/devcontainer.json`.
3. Reescribir este README y `AGENTS.md` para la app.
4. Poner la logica en componentes bajo `components/` y dejar `main/main.c` como arranque.
5. Para usar IMU o botones: `REQUIRES watch_board` en el `CMakeLists.txt` del componente e incluir `imu_service.h` / `watch_buttons.h`.
6. Si la app necesita otra tabla de particiones o Kconfig, cambiarlo en `partitions.csv` / `sdkconfig.defaults` (nunca solo en `sdkconfig`).

El repo nuevo es una copia independiente: los cambios posteriores en esta plantilla no le llegan. Lo que se deba compartir entre apps va a ESP32Watch-core.

## Compilar y flashear

```sh
source "$HOME/.espressif/v5.5.4/esp-idf/export.sh"
idf.py set-target esp32s3
idf.py build
idf.py -p /dev/tty.usbmodem21301 flash monitor
```

Debe aparecer una tarjeta "ESP32S3Watch / LVGL + Waveshare BSP" en la pantalla.

## Documentacion

La documentacion de hardware y entorno esta en [ESP32Watch-core/docs](https://github.com/Sethyrus/ESP32Watch-core/tree/main/docs): hardware y pines, setup de ESP-IDF, gotchas, arquitectura y bring-up.

## Apps hechas con esta base

- [ESP32Watch-Maze](https://github.com/Sethyrus/ESP32Watch-Maze): laberinto con IMU.
- [ESP32Watch-Doom](https://github.com/Sethyrus/ESP32Watch-Doom): port de Doom.

## Licencia

MIT. Ver [LICENSE](LICENSE). Una app creada desde la plantilla puede usar la licencia que necesite.
