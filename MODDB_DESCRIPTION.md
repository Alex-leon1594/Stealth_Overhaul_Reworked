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
- Performance: the HUD's line-of-sight checks run at half the rate (still smooth) for a cheaper per-frame cost.
- **Script optimization**: redundant `camp_lum.script` removed (campfire lighting is handled by `visual_memory_manager.script`); the log folder path is cached after the first write; `light_gem`/`stealth_ui` cache all MCM settings (read once at start, refreshed on MCM change — not every frame); the hot `get_visible_value` detection path uses the cached values.
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
- **Optimización de scripts**: se eliminó el redundante `camp_lum.script` (la iluminación de hogueras la gestiona `visual_memory_manager.script`); la ruta de la carpeta de log se cachea tras la primera escritura; `light_gem`/`stealth_ui` cachean todos los ajustes MCM (se leen una vez al inicio y se refrescan al cambiar en MCM — no cada frame); la ruta crítica `get_visible_value` de detección usa valores cacheados.
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
- Производительность: проверки прямой видимости HUD выполняются вдвое реже (по-прежнему плавно) — меньше затрат на кадр.
- **Качество кода и DLTX (v5.13)**: 100% модульная архитектура DLTX (`mod_*.ltx`) без конфликтов файлов в MO2; оптимизированный поиск трупов через `level.iterate_nearest`; периодическая очистка памяти (15 с) в `visual_memory_manager` и удаление освобождённых трупов (30 с) в `stealth_takedown` (предотвращает разрастание сейвов и памяти); полный контроль жизненного цикла частиц в `stealth_nvg` при смене локации; полная трехъязычная локализация MCM (34 опции).
- **Оптимизация скриптов (v5.10-v5.12)**: исправлен дубликат `compute_radius()` в `stealth_noise`; удалён избыточный `camp_lum.script`; кэширование путей и настроек MCM; оптимизация производительности.
- Полный пересмотр русской локализации + очистка испанских строк, документированные советы по балансу.

**Каждая механика включается/выключается в MCM — одна секция, 34 опции.**
