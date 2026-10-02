# ESP32Watch-template

Plantilla para crear firmwares nuevos para la Waveshare **ESP32-S3-Touch-AMOLED-2.06** con `ESP-IDF 5.5.4` + `LVGL 9` + BSP oficial de Waveshare.

Incluye una app minima con el patron comun (arranque, pantalla con `watch_display`, botones, apagado de pantalla por inactividad y light sleep con `watch_power`), la configuracion de la placa (`sdkconfig.defaults`, `partitions.csv` con la tabla comun del launcher) y la dependencia a [ESP32Watch-core](https://github.com/Sethyrus/ESP32Watch-core) (`watch_board`). Ya viene preparada para el modo launcher: llama a `watch_launcher_boot_once()` y, arrancada desde el launcher, `PWR` vuelve a el (sola, `PWR` apaga la pantalla).

## Crear una app nueva desde esta plantilla

Este es el checklist unico; el README del launcher solo cubre su parte (paso 8).

1. En GitHub, **Use this template** > **Create a new repository** (por ejemplo `ESP32Watch-MiApp`), y clonarlo junto al resto.
2. Cambiar el nombre del proyecto en `CMakeLists.txt` (`project(ESP32WatchApp)` -> `project(ESP32WatchMiApp)`; el launcher muestra el nombre sin el prefijo `ESP32Watch`) y en `.devcontainer/devcontainer.json`.
3. Reescribir este README y `AGENTS.md` para la app, y anadir `docs/<APP>_DESIGN.md` cuando haya diseno que contar.
4. Renombrar `components/demo_app` a `components/<app>_app` (y anadir `components/<app>_engine` si hay logica pura sin hardware); `main/main.c` se queda como arranque. Estructura en core [ARCHITECTURE](https://github.com/Sethyrus/ESP32Watch-core/blob/main/docs/ARCHITECTURE.md#estructura-de-una-app).
5. Servicios de placa: `REQUIRES watch_board` en el `CMakeLists.txt` del componente y los headers de core (`watch_display.h`, `watch_power.h`, `watch_buttons.h`, `watch_battery.h`, `watch_rtc.h`, `watch_nvs.h`, `watch_launcher.h`, `imu_service.h`). La pantalla siempre con `watch_display_start()`, nunca `bsp_display_start()`; el sleep siempre con `watch_power_sleep()`/`watch_power_screen_off()`, que tambien apagan el tactil (sin eso el reloj puede reiniciarse al despertar). Nunca escribir registros del AXP2101: ver core [PMU_SAFETY](https://github.com/Sethyrus/ESP32Watch-core/blob/main/docs/PMU_SAFETY.md).
6. Modo launcher:
   - mantener `watch_launcher_boot_once()` lo primero en `app_main`;
   - ofrecer "Salir" (`watch_launcher_exit()`) solo si `watch_launcher_is_available()`, y guardar antes lo que deba persistir;
   - en la raiz de la app, `PWR` vuelve al launcher (ver "Convencion De Botones" en core).
7. Persistencia: NVS con `watch_nvs_init()` y un namespace propio, anadido a la tabla de "Persistencia" de core `ARCHITECTURE.md`. Nunca borrar la NVS entera: la comparten todas las apps.
8. Registrarla en [ESP32Watch-Launcher](https://github.com/Sethyrus/ESP32Watch-Launcher#anadir-una-app-nueva): un slot OTA y una linea en `apps.conf`.
9. No tocar offsets en `partitions.csv`: es la tabla comun y se cambia en el launcher. Otros ajustes de Kconfig van en `sdkconfig.defaults` (nunca solo en `sdkconfig`).

El repo nuevo es una copia independiente: los cambios posteriores en esta plantilla no le llegan. Lo que se deba compartir entre apps va a ESP32Watch-core, y cada arreglo de core se propaga a todas las apps (ver "Alineacion De Repos" en core `AGENTS.md`).

## Compilar y flashear

```sh
source "$HOME/.espressif/tools/activate_idf_v5.5.4.sh"   # EIM; otras instalaciones: core docs/SETUP.md
idf.py set-target esp32s3
idf.py build
idf.py -p <PORT> flash monitor   # p. ej. /dev/tty.usbmodem1101; sin -p lo autodetecta
```

Debe aparecer una tarjeta "LVGL + watch_board" con un contador que sube con `BOOT`; la pantalla se apaga tras el timeout del launcher (15 s por defecto) y `BOOT` o `PWR` la encienden. Asi, sola, ocupa `factory` (sin launcher); para probarla dentro del launcher, usar `flash_all.sh` de ESP32Watch-Launcher.

## Documentacion

La documentacion de hardware y entorno esta en [ESP32Watch-core/docs](https://github.com/Sethyrus/ESP32Watch-core/tree/main/docs): hardware y pines, setup de ESP-IDF, gotchas, arquitectura y bring-up.

## Apps hechas con esta base

- [ESP32Watch-Maze](https://github.com/Sethyrus/ESP32Watch-Maze): laberinto con IMU.
- [ESP32Watch-Doom](https://github.com/Sethyrus/ESP32Watch-Doom): port de Doom.
- [ESP32Watch-Fluid](https://github.com/Sethyrus/ESP32Watch-Fluid): simulacion de fluido con la IMU.
- [ESP32Watch-Recorder](https://github.com/Sethyrus/ESP32Watch-Recorder): grabadora de voz con microfono y microSD.
- [ESP32Watch-Launcher](https://github.com/Sethyrus/ESP32Watch-Launcher): launcher para tenerlas todas grabadas a la vez.

## Licencia

MIT. Ver [LICENSE](LICENSE). Una app creada desde la plantilla puede usar la licencia que necesite.
