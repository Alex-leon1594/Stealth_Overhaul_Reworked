# Reglas y Patrones de Modding para S.T.A.L.K.E.R. Anomaly / X-Ray Engine

## 1. Manipulación de Slots y Cinturón del Actor (API Lua)
- **Límites de `db.actor:item_in_slot(slot)`**:
  - Los slots del actor válidos en el motor van exclusivamente del `1` al `13` (cuchillo, pistolas, rifles, granadas, binoculares, perno, traje, casco, PDA, detector, linterna, contenedor/mochila).
  - **NUNCA** llamar a `item_in_slot(i)` con índices $\ge 14$ (como bucles `for i=8,19 do`), ya que genera una excepción C++ fuera de rango capturada por Modded Exes / `BusyHandsDebug` que fuerza un guardado de emergencia (`crash_save`).
- **Iteración de Artefactos en el Cinturón**:
  - Para examinar artefactos equipados, utilizar siempre:
    ```lua
    db.actor:iterate_belt(function(owner, item)
        local sec = item:section()
        -- lógica con el artefacto
    end)
    ```

## 2. Callbacks de `axr_main` y Scripts
- **Verificación de funciones**: Antes de registrar un callback con `RegisterScriptCallback`, asegurarse de que la función exista (no sea `nil`).
- **Callbacks válidos**: Evitar llamadas a eventos no estándar (ej. `on_game_save` no existe en `axr_main`, utilizar `save_state` o `actor_on_save`).

## 3. Animaciones HUD de `player_hud`
- En llamadas a `stop_hud_motion` o control de partes de animación, `script_anim_part` debe ser un valor válido menor a 3 (`0, 1, 2`). No enviar valores arbitrarios como `255`.

## 4. Diagnóstico de Registros (`.log` de X-Ray)
- **Filtrado de ruido**: Diferenciar advertencias benignas (comentarios OGG faltantes, texturas/sonidos menores) de excepciones críticas de Lua (`! [LUA] [BusyHandsDebug] Runtime Error`, `! [LUA]`, `@ crash_save`).
- **Trazas de ejecución**: Analizar la pila de llamadas (`STACK TRACEBACK`) de abajo hacia arriba para identificar el archivo script y la línea exacta del disparador original.
