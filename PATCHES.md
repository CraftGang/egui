# Craft suite patches on egui 0.37.0 (`craft/0.37`)

Base: upstream tag `0.37.0` (`841a328b`). Each patch is a `craft/<topic>` branch off `0.37.0` (or off the
topic named in its row), merged here in table order. The workspace patches `winit` to aiongg/winit
`craft/suite`. Apps patch every egui-workspace crate they use to this checkout, so only one egui resolves.
Drop a patch when an egui release in use contains it.

| # | Commit | Crate | Change | Upstream | Source in the suite |
|---|---|---|---|---|---|
| E1 | `8f3abf06` | eframe | `NativeOptions::viewport_hooks` (`ViewportHooks`): each viewport's native window on create/remove, and every viewport's raw input before egui (immediate viewports included); honoured by `run_native` and `create_native` | not proposed upstream yet | craft-tablet (native Wayland pen input) |
| E2+E3 | `9dd799ab` | egui-wgpu | fit a surface within `max_texture_dimension_2d`; first configure and renderer creation inside wgpu error scopes, returned as `WgpuError::ConfigureSurface` / `CreateRenderer` | not proposed; the surface fit would close emilk/egui#8361 | pdfcraft#609 (#577), #617 (#519), #1042; gridcraft#761 |
| E5 | `0e27d87e`, `777977b7`, `f7e89e23`, `5b2b8e19` | epaint, egui | bidi text: right-to-left runs shaped, rows in visual order, marks, end caret and selection; word-motion underflow fix; tests build without `default_fonts` | emilk/egui#8577 (open) ported, with #8698 and review fixes | language support (craft-libs#198) |
| E6 | `0abfabcf` | egui | `request_focus` on the focused widget no longer interrupts IME composition | not proposed | IME input |
| E7 | `0670c58a` | egui-winit | Windows: letter keys from the virtual-key code on layouts without A–Z (Russian, Arabic, Hebrew) | not proposed | shortcuts on non-Latin layouts |
| E8 | `1b03c10f` | egui | `TextEdit` no longer clips glyphs above or below the row (Thai, Vietnamese, Arabic marks) | not proposed | language support |
| E9 | `b2f5cafc`, `e1341a3d`, `5105330d`, `75a5b3d3` | egui | `Style::right_to_left` mirrors menus, scroll bars, headers, title bars, combo boxes, checkboxes, popups, resize grips | not proposed | RTL UI (craft-libs#198) |
| E10 | `dc90c13e`, `c8e573f7`, `34b2c0d4` | eframe (web) | named keys not typed as text; letter keys from `KeyboardEvent.code` on non-Latin layouts; `lang` on the hidden input and speech | not proposed | web builds |
| E11 | `92e61afd` | epaint | glyphs rasterized on a single-threaded vello_cpu context, so its `multithreading` feature no longer panics | emilk/egui#8641 (open draft, flush only) | servo and VectorCraft enable `multithreading` |
| E12 | `e955bb18`, `485c0b89`, `0185887c`, `7015ac23`, `4067b2c4` | epaint, egui | `LayoutJob::direction`; RTL paragraphs right-aligned; `…` at the visual end; UAX #9 rule L1; linear-time reordering (on E5) | not proposed; follow-up to #8577 | language support |
| E13 | `5e4da7f4` | epaint, egui | caret, hit-test, Delete and Backspace by grapheme cluster (on E5) | not proposed | language support |
| E14 | `cbd77549`, `b3722ac0` | epaint, egui | `FontTweak::fallback_align`: fallback fonts on the primary baseline, rows grow to fit; default unchanged | not proposed | CJK and Indic fallback fonts |
| E15 | `f1cae609` | epaint, egui | `LineBreaker` trait (`SimpleLineBreaker` keeps the old rules), grapheme-safe emergency breaks, `LayoutJob::language` | not proposed | craft-text line breaking |
| E16 | `b3ef9771`, `bed6dfe3` | epaint, egui | visual cursor movement with affinity, Home/End in reading order, password mask per cluster, neutral paragraphs inherit direction | not proposed | language support |
| E17 | `520435df` | egui | `Context::set_language`, `UiBuilder::language`, `Response::accessible_language`; AccessKit root language; one text run per bidi level | not proposed | screen readers (craft-libs#198) |
| E18 | `89de8f3c` | eframe | `Frame::set_ui_language` sets `Context::set_language`; web falls back to `Context::language` | fork-only | craft-i18n UI language |
| E19 | `d64047bf`, `642b74f7`, `baeb4515` | egui-winit, egui | one letter-key rule for Windows, macOS and Linux (first Latin group); `RawInput::keyboard_layout_is_latin`; `RawInput::latin_layout_chars` (each key's Latin layout character, so a key without a Latin letter is no letter shortcut) | fork-only until winit has the APIs | shortcuts on non-Latin layouts; needs winit `craft/suite` |
| E20 | `23955535` | egui | widget text laid out in the language of its `Ui` | fork-only (goes with E15 and E17) | language support |
| E21 | `a70470b0` | egui-winit, egui | `ImeEvent::DeleteSurroundingBytes`: `TextEdit` deletes the exact bytes an input method replaces, in the commit's undo step | not proposed | macOS press-and-hold and dictation, Wayland input methods; needs winit `craft/suite` |

E4 (egui-winit uses winit's Wayland clipboard) is not on this branch: it needs
`winit::platform::wayland::Clipboard` (`for_display`, `load_text`, `store_text`) from storytold/winit
`craft/0.30.13` W15, which aiongg/winit `craft/suite` doesn't have.

E1 changes from `e7e37ad`: the hooks are installed in `init_native`, so `create_native` uses them too, and a
unit test covers installing and replacing them. E2+E3 is the same change as `b02321d` with a `surface_fit`
test.
