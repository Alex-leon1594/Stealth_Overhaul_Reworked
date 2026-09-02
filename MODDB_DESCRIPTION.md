# Stealth Overhaul — Reworked (ModDB description)

---

## English

# Stealth Overhaul — Reworked

**A complete stealth package for STALKER Anomaly (1.5.1+, GAMMA-ready), built on the classic Stealth 2.0.1 by xcvb and expanded into a full stealth overhaul.**

If you know the original **Stealth 2.0.1**, this is everything it did — reworked NPC vision (luminosity, distance, movement, weight, outfit camo, crouch), the eye detection meter, debug HUD, MCM integration — **plus an entire package of new mechanics**:

### What's new (vs the original Stealth)

**Sound & noise**
- Footstep noise system: crouch 3 m, walk 10 m, run 20 m, sprint 30 m — scaled by weight, outfit `noise_k`, breathing (wounded/low stamina/bleeding) and reduced by rain. Artifacts alter your noise signature (gravi ×1.2, compass ×0.75…).
- NPCs investigate noise (threat_danger logic); loud sounds alert, subtle ones make them curious. Dropped items make noise too.
- In-world noise radius visualization via Modded Exes `debug_render` (MCM flag).

**Detection & AI**
- Squad alarm propagation: when an NPC spots you, squadmates within a configurable radius (default 200 m) get a detection boost.
- Monsters that see you alarm nearby stalkers (configurable radius, default 150 m; both radii and durations adjustable in MCM, 0 = disabled).
- No magic squad knowledge: when AlifeTactics is installed, an attack you land unseen no longer force-reveals your position to the whole squad — only members who can actually see you or are already fighting you learn where you are (MCM `at_disclosure_gate`).
- NPC muzzle flashes: shooters become visible ×7 for 0.2 s — spot enemies in the dark.
- Terrain-dependent camouflage: ghillie suits only work on natural terrain (earth, dirt, grass, bush).
- Dynamic campfire lighting: NPCs near a burning fire are easier to spot (+0.4 luminosity) — no more static constant.
- Night adaptation, flashlight backlight glare (blind NPCs ×0.55 and mark them suspicious), guards/snipers less perceptive (×0.5), searching NPCs more (×1.5).
- Scent system: mutants smell your blood — the more you bleed, the farther they track you (up to ~60 m).
- Community perception: each faction sees you differently (Military 1.25, Ecologists 0.85…).
- Allies never detect you: friendly squads are fully excluded from the eye, the detection formula and noise/investigation. Companions traveling in your squad (escorts) are also excluded from the detection meter regardless of their goodwill.

**NPC NVG system**
- Rank-gated NVG ownership (20%–100% by rank); at night they show a visible red eye-dot, turn off their headlamp and see better.

**HUD**
- Dual icon: classic light gem *or* NPC-vision progress bar (white/yellow/red), fully movable in MCM.
- Detection eye: white → amber → red, with a **smooth exponential calm-down curve** — detection fades fast when you break line of sight.
- New noise bar under the icon shows your current noise radius in real time (hideable independently via MCM `noise_bar`, or toggled in-game with the `noise_bar_key` hotkey, default F7).

**Stealth actions**
- Silent knife takedowns from behind (crouch + knife + undetected), fully integrated with quest/loot/stat callbacks (Stealth Kill Detection Fix compatible).
- Hide corpses: crouch + stare 1.5 s — gone without alarm. Hidden corpses survive save/load.

**Under the hood**
- Complete stability rewrite: ID-based NPC registry, event-driven updates (engine spawn/destroy callbacks), safe `pcall` guards everywhere — no crashes from destroyed objects.
- Danger system refinement: player-caused dangers persist and are never distance-ignored; hidden corpses don't trigger danger.
- Fixed stock bugs (NPCs alerting when you stand still, takedown crash, `xr_combat_ignore` condition, item-drop crash).
- **Anti-Lag for GAMMA Integration & Combat Distance Optimization (v5.19)**: Integrated early-exit distance constraints at the top of `is_enemy` in `xr_combat_ignore.script` to discard distant targets before executing expensive callbacks, bribe checks, and safe-zone polygon math, saving CPU cycles in crowded scenes; fixed an upstream Anti-Lag typo in Cordon faction comparisons while retaining full peaceful NPC protection (`is_peaceful_npc`) and non-assault safe zones. Fixed noise-bar hotkey toggle (F8) resetting on map transitions and save loads by persisting state to MCM (`ui_mcm.set`) and save files (`save_state`/`load_state`).
- **Luabind Member Access Fix & Entity Hardening (v5.18)**: Fixed engine error spam (`CSciptEntity [physic_object/lights_hanging_lamp]: cannot access class member Alive!`) when crouching near hanging lamps/physics props by prioritizing `IsStalker` (which safely queries `clsid()`) before checking `alive()` in `process_corpse`, and adding defensive `IsStalker or IsMonster` type guards to all local `alive()` helpers.
- **Hostility-Gated Detection & Peaceful NPC Protection (v5.17)**: Strictly gated stealth vision accumulation, footstep/movement sound investigation, and HUD stealth eye calculation to hostile enemies (`npc:relation(db.actor) == game_object.enemy`); added `is_peaceful_npc` helper protecting traders, mechanics, and bar staff from false combat panics.
- **Engine Hardening, Nil-Guards & Performance (v5.16)**: Added nil-pointer safety checks across `xr_combat_ignore` and `xr_danger` callbacks (`npc_on_hit_callback` / `npc_on_death_callback`) to prevent CTDs from environmental/anomaly/script damage; optimized `lights_lum()` with per-tick memoization reducing weather vector queries during crowded NPC scenes; added bone fallback safety (`"eyelid_1"` → `"bip01_head"`) and level-transit particle cleanup in `stealth_nvg`; added rear-arc angle checks in `stealth_takedown` to ensure takedowns strictly require rear/flank stealth positioning; clamped RGB values in `light_gem`.
- **Vision Architecture & DLTX Hardening (v5.15)**: Relocated creature vision patches to root `mod_system_stalker_*.ltx` for guaranteed DLTX global injection into `system.ltx`; decoupled `danger_mult` in `visual_memory_manager` from engine `time_quant` variations (`danger_mult = 1.0`), ensuring robust, responsive visual target acquisition across vanilla and all modded engines.
- **AlifeTactics 1.2.0 & Vision Responsiveness (v5.14)**: Clean delegation of movement-noise alerting to AlifeTactics (`noise_handoff`) without AI state conflicts; calibrated `time_quant = 0.05` and `visibility_threshold = 25.0 / 20.0` with point-blank proximity awareness (`prox_boost`) for instantaneous close-range target confirmation; clamped visual accumulation formulas in `visual_memory_manager` against negative numbers; fixed corpse-hiding 30 s registry pruning bug; robust logging module export.
- **Code quality & DLTX (v5.13)**: 100% modular DLTX architecture (`mod_*.ltx`) with zero MO2 file conflicts; `level.iterate_nearest` optimized corpse search; periodic 15 s memory purge in `visual_memory_manager` and 30 s released corpse cleanup in `stealth_takedown` (prevents save/RAM bloat); particle lifecycle handlers in `stealth_nvg` (no orphaned eye particles on level change); complete trilingual MCM localization (34 options).
- **Code quality (v5.12)**: fixed a `compute_radius()` duplicate in `stealth_noise` (the cached version with outfit/artifact caching was dead code — now fixed); `purge_stale()` throttled to 1 Hz; corpse-cleanup timer made deterministic; three dead `--[[...--]]` blocks removed from `xr_danger`.
- Full Russian retranslation + cleaned Spanish strings, documented balance tips.

**Every mechanic is toggleable via MCM — one section, 34 options.**

---

## Español

# Stealth Overhaul — Reworked

**Un paquete completo de sigilo para STALKER Anomaly (1.5.1+, compatible con GAMMA), construido sobre el clásico Stealth 2.0.1 de xcvb y ampliado hasta convertirse en un overhaul completo del sigilo.**

Si conoces el **Stealth 2.0.1** original, esto es todo lo que hacía — visión NPC rehecha (luminosidad, distancia, movimiento, peso, camuflaje del traje, agachado), el indicador de detección (ojo), HUD de debug, integración MCM — **más un paquete entero de mecánicas nuevas**:

### Qué hay de nuevo (frente al Stealth original)

**Sonido y ruido**
- Sistema de ruido de pasos: agachado 3 m, caminando 10 m, corriendo 20 m, esprintando 30 m — afectado por el peso, el `noise_k` del traje, la respiración (herido / sin energía / sangrando) y reducido por la lluvia. Los artefactos alteran tu firma de ruido (gravi ×1.2, compass ×0.75…).
- Los NPC investigan el ruido (lógica threat_danger); los sonidos fuertes alertan, los sutiles despiertan curiosidad. Soltar objetos también hace ruido.
- Visualización del radio de ruido en el mundo (debug_render de Modded Exes, activable en MCM).

**Detección e IA**
- Propagación de alarma en escuadras: si un NPC te ve, sus compañeros en un radio configurable (por defecto 200 m) reciben un impulso de detección.
- Los monstruos que te ven alertan a los stalkers cercanos (radio configurable, por defecto 150 m; radios y duraciones ajustables en MCM, 0 = desactivado).
- Sin conocimiento mágico del escuadrón: con AlifeTactics instalado, un ataque que aterrizas sin ser visto ya no revela tu posición a todo el escuadrón — solo los miembros que pueden verte o ya están combatiendo contigo saben dónde estás (MCM `at_disclosure_gate`).
- Destellos de boca de cañón de NPC: los tiradores son visibles ×7 durante 0,2 s — localiza tiradores en la oscuridad.
- Camuflaje dependiente del terreno: los trajes ghillie solo funcionan en terreno natural (tierra, barro, hierba, arbustos).
- Iluminación dinámica de hogueras: los NPC cerca de un fuego encendido son más fáciles de ver (+0,4 de luminosidad).
- Adaptación nocturna, deslumbramiento de linterna (ciega a los NPC ×0,55 y los marca como sospechosos), guardias/tiradores menos perceptivos (×0,5), NPC en búsqueda más perceptivos (×1,5).
- Sistema de olor: los mutantes huelen tu sangre — cuanto más sangras, más lejos te rastrean (hasta ~60 m).
- Percepción por facción: cada facción te ve distinto (Militares 1.25, Ecologistas 0.85…).
- Los aliados nunca te detectan: las escuadras amigas quedan excluidas del ojo, de la fórmula de detección y del ruido/investigación. Los acompañantes que viajan en tu escuadra también quedan fuera del indicador de detección, sin importar su goodwill.

**Sistema NVG para NPC**
- Los stalkers de mayor rango pueden tener NVGs (20%–100% según rango); de noche muestran un punto rojo visible en el ojo, apagan su linterna y ven mejor.

**HUD**
- Icono dual: gema de luz clásica *o* barra de visión NPC (blanco/amarillo/rojo), totalmente movible en MCM.
- Ojo de detección: blanco → ámbar → rojo, con **curva de calmado exponencial suave** — la detección se desvanece rápido al perder la línea de visión.
- Nueva barra de ruido bajo el icono que muestra tu radio de ruido en tiempo real (ocultable de forma independiente con el MCM `noise_bar`, o con la tecla `noise_bar_key`, F7 por defecto).

**Acciones sigilosas**
- Asesinatos silenciosos con cuchillo por la espalda (agachado + cuchillo + sin ser detectado), integrados con los callbacks de misiones/saqueo/estadísticas (compatible con Stealth Kill Detection Fix).
- Esconder cadáveres: agachado + mirar 1,5 s — desaparece sin alarma. Los cadáveres escondidos sobreviven a los guardados.

**Bajo el capó**
- Reescritura completa de estabilidad: registro de NPC basado en IDs, actualizaciones por eventos (callbacks de spawn/destroy del motor), protecciones `pcall` — sin cuelgues por objetos destruidos.
- Refinamiento del sistema de peligro: los peligros causados por el jugador persisten más y nunca se ignoran por distancia; los cadáveres escondidos no generan eventos de peligro.
- Bugs del stock corregidos (NPC en alerta al estar quieto, crash de takedown, condición de `xr_combat_ignore`, crash al soltar objetos).
- Rendimiento: las comprobaciones de línea de visión del HUD se ejecutan a mitad de frecuencia (sigue siendo fluido) por un coste por frame menor.
- **Integración con Anti-Lag for GAMMA y optimización de distancia de combate (v5.19)**: Integración de restricciones de distancia tempranas al inicio de `is_enemy` en `xr_combat_ignore.script` para descartar objetivos lejanos antes de costosas llamadas a callbacks, sobornos y cálculos de zonas seguras, ahorrando ciclos de CPU; se corrigió una errata de Anti-Lag en la comparación de facciones en Cordon preservando al 100% la protección de NPC pacíficos (`is_peaceful_npc`) y las zonas seguras. Corregido el restablecimiento de la barra de ruido (tecla F8) al cambiar de mapa o cargar partida, persistiendo su estado en MCM (`ui_mcm.set`) y en partidas guardadas (`save_state`/`load_state`).
- **Corrección de acceso Luabind y blindaje de entidades (v5.18)**: Se solucionó el spam de errores en el log (`CSciptEntity [physic_object/lights_hanging_lamp]: cannot access class member Alive!`) al agacharse cerca de lámparas y accesorios físicos mediante la priorización de `IsStalker` antes de `alive()` en `process_corpse` y añadiendo protecciones `IsStalker or IsMonster` en las funciones `alive()`.
- **Detección restringida a enemigos y protección de NPC pacíficos (v5.17)**: La acumulación de visión de sigilo, la investigación de pasos/sonidos y el cálculo del ojo en el HUD se limitaron estrictamente a enemigos hostiles (`npc:relation(db.actor) == game_object.enemy`); se añadió la protección `is_peaceful_npc` para comerciantes, mecánicos y médicos.
- **Blindaje del motor, guardas Nil y rendimiento (v5.16)**: Comprobaciones de seguridad ante punteros nulos en callbacks de `xr_combat_ignore` y `xr_danger` (`npc_on_hit_callback` / `npc_on_death_callback`) para evitar CTDs por daños ambientales/anomalías/scripts; optimización de `lights_lum()` con memoización por tick reduciendo consultas de vectores de clima en zonas densas de NPC; fallback de seguridad de huesos (`"eyelid_1"` → `"bip01_head"`) y limpieza de partículas en transiciones de nivel en `stealth_nvg`; comprobación estricta de ángulo por la espalda/flanco en `stealth_takedown`; clamping de valores RGB en `light_gem`.
- **Arquitectura de Visión y DLTX Global (v5.15)**: Parches DLTX de criaturas reubicados en la raíz `mod_system_stalker_*.ltx` para inyección global garantizada en `system.ltx`; desacoplado `danger_mult` en `visual_memory_manager` de las variaciones de `time_quant` del motor (`danger_mult = 1.0`), asegurando una detección visual sólida y responsiva en cualquier versión o modpack.
- **AlifeTactics 1.2.0 y responsividad de visión (v5.14)**: Delegación limpia del ruido de pasos a AlifeTactics (`noise_handoff`) evitando conflictos de IA; calibración de `time_quant = 0.05` y `visibility_threshold = 25.0 / 20.0` junto con multiplicador de proximidad a corta distancia (`prox_boost`) para confirmación visual inmediata; protección de la acumulación visual contra números negativos; corrección del bug de purga a los 30 s de cadáveres ocultos; exportación robusta del módulo de logging.
- **Calidad de código y DLTX (v5.13)**: arquitectura DLTX 100% modular (`mod_*.ltx`) con cero conflictos en MO2; búsqueda de cadáveres optimizada con `level.iterate_nearest`; purga periódica de memoria (15 s) en `visual_memory_manager` y limpieza de cadáveres (30 s) en `stealth_takedown` (evita inflar guardados y RAM); control del ciclo de vida de partículas en `stealth_nvg` al cambiar de nivel; localización MCM completa en 3 idiomas (34 opciones).
- **Optimización de scripts (v5.10-v5.12)**: se eliminó el redundante `camp_lum.script` (la iluminación de hogueras la gestiona `visual_memory_manager.script`); la ruta de la carpeta de log se cachea tras la primera escritura; `light_gem`/`stealth_ui` cachean todos los ajustes MCM (se leen una vez al inicio y se refrescan al cambiar en MCM — no cada frame); la ruta crítica `get_visible_value` de detección usa valores cacheados.
- Retraducción completa al ruso + cadenas en español limpiadas, consejos de balance documentados.

**Cada mecánica se activa/desactiva desde MCM — una sección, 34 opciones.**

---

## Русский

# Stealth Overhaul — Reworked

**Полный пакет стелса для STALKER Anomaly (1.5.1+, совместим с GAMMA), созданный на основе классического аддона Stealth 2.0.1 от xcvb и расширенный до полноценного переосмысления стелс-механики.**

Если вы знакомы с оригинальным **Stealth 2.0.1**, здесь есть всё, что он делал — переработанное зрение NPC (освещённость, дистанция, движение, вес, камуфляж костюма, присед), индикатор обнаружения (глаз), отладочный HUD, интеграция с MCM — **плюс целый пакет новых механик**:

### Что нового (по сравнению с оригиналом)

**Звук и шум**
- Система шума шагов: присед 3 м, шаг 10 м, бег 20 м, спринт 30 м — зависит от веса, параметра костюма `noise_k`, дыхания (ранен / без сил / кровотечение) и снижается дождём. Артефакты меняют вашу шумовую сигнатуру (gravi ×1.2, compass ×0.75…).
- NPC исследуют источники шума (логика threat_danger): громкие звуки поднимают тревогу, тихие — любопытство. Брошенные предметы тоже шумят.
- Визуализация радиуса шума в мире через `debug_render` (флаг в MCM).

**Обнаружение и ИИ**
- Распространение тревоги в группе: когда NPC замечает вас, союзники в настраиваемом радиусе (по умолчанию 200 м) получают бонус обнаружения.
- Мутанты, заметившие вас, поднимают тревогу у ближайших сталкеров (настраиваемый радиус, по умолчанию 150 м; радиусы и длительность настраиваются в MCM, 0 = отключено).
- Без «магического» знания группы: при установленном AlifeTactics незамеченная вами атака больше не раскрывает вашу позицию всей группе — о вашем местонахождении узнают только те, кто видит вас или уже сражается с вами (MCM `at_disclosure_gate`).
- Вспышки выстрелов NPC: стреляющие становятся заметны ×7 на 0,2 с — находите стрелков в темноте.
- Камуфляж от местности: костюмы «гилли» работают только на естественном грунте (земля, грязь, трава, кусты).
- Динамический свет костров: NPC у горящего огня заметнее (+0,4 освещённости) — больше не статичная константа.
- Ночная адаптация глаз, ослепление фонарём (×0,55 + метка подозрения), часовые/снайперы менее зоркие (×0,5), ищущие NPC — более зоркие (×1,5).
- Система запаха: мутанты чуют вашу кровь — чем сильнее кровотечение, тем дальше вас чуют (до ~60 м).
- Восприятие по фракциям: каждая фракция видит вас по-своему (Военные 1.25, Экологи 0.85…).
- Союзники никогда вас не замечают: дружественные группы полностью исключены из глаза, формулы обнаружения и шума/исследования. Спутники, идущие в вашей группе (эскорт), также исключены из индикатора обнаружения независимо от отношения к вам.

**Система ПНВ у NPC**
- Обладание ПНВ зависит от ранга (20%–100%); ночью у таких NPC видна красная точка на глазу, они выключают фонарь и видят лучше.

**HUD**
- Двойной индикатор: классический гем света *или* шкала зрения NPC (белый/жёлтый/красный), позиция настраивается в MCM.
- Глаз обнаружения: белый → жёлтый → красный, с **плавной экспоненциальной кривой затухания** — обнаружение быстро спадает после потери визуального контакта.
- Новая шкала шума под индикатором показывает ваш текущий радиус шума в реальном времени (её можно скрыть отдельно через MCM `noise_bar` или горячей клавишей `noise_bar_key`, по умолчанию F7).

**Стелс-действия**
- Бесшумные убийства ножом со спины (присед + нож + без обнаружения), полностью интегрированы с квестами/лутом/статистикой (совместимо со Stealth Kill Detection Fix).
- Прятать трупы: присед + взгляд 1,5 с — исчезает без тревоги. Спрятанные трупы переживают сохранения.

**Под капотом**
- Полная переработка стабильности: реестр NPC по ID, событийные обновления (колбэки spawn/destroy движка), защита `pcall` везде — никаких вылетов от уничтоженных объектов.
- Улучшение системы опасностей: опасности, вызванные игроком, живут дольше и никогда не игнорируются по дистанции; спрятанные трупы не создают события опасности.
- Исправлены баги стоковой версии (тревога NPC при простое, вылет такедауна, условие `xr_combat_ignore`, вылет при броске предмета).
- **Интеграция с Anti-Lag for GAMMA и оптимизация боевой дистанции (v5.19)**: Ранняя проверка дистанций в начале `is_enemy` в `xr_combat_ignore.script` для мгновенного отсечения далёких целей до вызова тяжёлых колбэков, проверок подкупа и зон безопасности; исправлена опечатка Anti-Lag в проверке группировок на Кордоне с сохранением защиты мирных NPC (`is_peaceful_npc`) и безопасных зон. Исправлен сброс состояния шкалы шума (клавиша F8) при переходе между локациями и загрузке игры за счёт сохранения состояния в MCM (`ui_mcm.set`) и файлы сохранений (`save_state`/`load_state`).
- **Исправление Luabind и защита сущностей (v5.18)**: Устранён спам ошибок в логе (`CSciptEntity [physic_object/lights_hanging_lamp]: cannot access class member Alive!`) при приседании возле висячих ламп и физических объектов за счёт первоочередной проверки `IsStalker` перед `alive()` в `process_corpse` и добавления проверок `IsStalker or IsMonster` в функции `alive()`.
- **Ограничение обнаружения врагами и защита торговцев (v5.17)**: Накопление видимости, исследование шума шагов и расчёт глаза HUD строго ограничены враждебными целями (`npc:relation(db.actor) == game_object.enemy`); добавлена защита `is_peaceful_npc` для торговцев, техников и медиков.
- **Надёжность движка, nil-guard защита и оптимизация (v5.16)**: Проверки безопасности на nil-указатели в `xr_combat_ignore` и `xr_danger` (`npc_on_hit_callback` / `npc_on_death_callback`) для предотвращения вылетов при уроне от аномалий/окружения/скриптов; оптимизация `lights_lum()` с покадровой мемоизацией, устраняющая избыточные запросы погоды в сценах со множеством NPC; резервный поиск костей (`"eyelid_1"` → `"bip01_head"`) и очистка частиц ПНВ при переходе между локациями в `stealth_nvg`; строгая проверка угла нападения со спины/фланга в `stealth_takedown`; ограничение RGB-значений в `light_gem`.
- **Глобальная DLTX-инъекция и отзывчивость зрения (v5.15)**: Патчи зрения существ перенесены в корень `mod_system_stalker_*.ltx` для гарантированной глобальной интеграции в `system.ltx`; `danger_mult` в `visual_memory_manager` полностью отвязан от движкового `time_quant` (`danger_mult = 1.0`), что гарантирует стабильное и быстрое визуальное обнаружение на любых сборках и движках.
- **AlifeTactics 1.2.0 и отзывчивость зрения (v5.14)**: Чистая передача шума шагов в AlifeTactics (`noise_handoff`) без конфликтов ИИ; калибровка `time_quant = 0.05` и `visibility_threshold = 25.0 / 20.0` с множителем ближней дистанции (`prox_boost`) для мгновенного зрительного контакта в упор; защита математики зрения от отрицательных значений; исправление бага очистки спрятанных трупов через 30 с; надёжный экспорт модуля логов.
- **Качество кода и DLTX (v5.13)**: 100% модульная архитектура DLTX (`mod_*.ltx`) без конфликтов файлов в MO2; оптимизированный поиск трупов через `level.iterate_nearest`; периодическая очистка памяти (15 с) в `visual_memory_manager` и удаление освобождённых трупов (30 с) в `stealth_takedown` (предотвращает разрастание сейвов и памяти); полный контроль жизненного цикла частиц в `stealth_nvg` при смене локации; полная трехъязычная локализация MCM (34 опции).
- **Оптимизация скриптов (v5.10-v5.12)**: исправлен дубликат `compute_radius()` в `stealth_noise`; удалён избыточный `camp_lum.script`; кэширование путей и настроек MCM; оптимизация производительности.
- Полный пересмотр русской локализации + очистка испанских строк, документированные советы по балансу.

**Каждая механика включается/выключается в MCM — одна секция, 34 опции.**
