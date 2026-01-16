# Strudel + Monaco Editor in TouchDesigner

Un sistema per fare live coding musicale in TouchDesigner usando Monaco Editor (il cuore di VS Code) con sintassi Strudel e autocompletamento.

## 🎯 Cosa fa

- **Monaco Editor** in un Web Browser COMP con:
  - Syntax highlighting per Strudel
  - Autocompletamento per tutte le funzioni Strudel
  - Tema dark ottimizzato per live coding
  - Shortcuts Ctrl+Enter (run) e Ctrl+. (stop)

- **Strudel Player** in un secondo Web Browser COMP
  
- **Bridge WebSocket** che connette i due

## 📁 Files inclusi

```
strudel-monaco/
├── monaco-strudel-editor.html   # La pagina Monaco con sintassi Strudel
├── websocket_callbacks.py       # Callbacks per WebSocket Server DAT
├── websocket_callbacks_alt.py   # Versione alternativa (più metodi di injection)
├── http_callbacks.py            # Callbacks per Web Server DAT (hosting locale)
├── SETUP_INSTRUCTIONS.py        # Guida dettagliata + script auto-setup
└── README.md                    # Questo file
```

## 🚀 Quick Start

### Opzione 1: Hosting Esterno (Più Affidabile)

1. **Hosta il file HTML** su GitHub Pages, CodePen, o qualsiasi server:
   - Carica `monaco-strudel-editor.html`
   - Ottieni l'URL pubblico

2. **In TouchDesigner:**
   ```
   Web Browser COMP (monaco) → URL: [il tuo URL pubblico]
   Web Browser COMP (strudel) → URL: https://strudel.cc
   WebSocket Server DAT → Port: 9980, Active: ON
   Text DAT (callbacks) → [incolla websocket_callbacks.py]
   ```

3. **Collega i callbacks** al WebSocket Server DAT

### Opzione 2: Server Locale in TouchDesigner

1. **Crea questi operatori:**
   ```
   Web Browser COMP (monaco_browser)
   Web Browser COMP (strudel_browser) → URL: https://strudel.cc
   WebSocket Server DAT (ws_server) → Port: 9980
   Web Server DAT (web_server) → Port: 8000
   Text DAT (ws_callbacks) → [incolla websocket_callbacks.py]
   Text DAT (http_callbacks) → [incolla http_callbacks.py]
   Text DAT (monaco_html) → [incolla tutto il contenuto di monaco-strudel-editor.html]
   Text DAT (strudel_code) → [vuoto, per storage]
   ```

2. **Collega i callbacks:**
   - ws_server.par.callbacks → ws_callbacks
   - web_server.par.callbacks → http_callbacks

3. **Imposta URL Monaco:**
   ```
   monaco_browser → URL: http://localhost:8000/editor
   ```

## ⚠️ Troubleshooting

### Monaco è bianco/non carica
- **Causa probabile:** Mixed content o CORS
- **Soluzione:** Usa l'opzione di hosting esterno
- **Verifica:** Apri l'URL direttamente nel browser normale per vedere gli errori

### WebSocket non si connette
- **Verifica:** WebSocket Server DAT è Active?
- **Verifica:** Porta 9980 è libera?
- **Prova:** Guarda il textport di TouchDesigner per errori

### Il codice non suona in Strudel
1. **Prima testa manualmente:**
   - Vai su https://strudel.cc nel browser normale
   - Scrivi `sound("bd sd")` e premi play
   - Se non funziona lì, è un problema audio del sistema

2. **Verifica l'injection:**
   - Apri il textport in TouchDesigner
   - Esegui: `op('ws_callbacks').module.test_connection()`

### Strudel carica ma l'audio non parte
- Strudel richiede un click utente per sbloccare l'audio
- Clicca una volta nella finestra Strudel prima di mandare codice

## 📝 Funzioni Strudel incluse nell'autocompletamento

### Sound Sources
`sound`, `s`, `note`, `n`, `freq`, `midinote`

### Pattern Constructors
`cat`, `stack`, `seq`, `fastcat`, `slowcat`, `timecat`, `polymeter`, `polyrhythm`, `rand`, `irand`, `choose`, `wchoose`, `run`, `scan`, `range`, `rangex`

### Time Modifiers
`fast`, `slow`, `hurry`, `stretch`, `compress`, `early`, `late`, `off`, `when`, `while`

### Pattern Modifiers
`rev`, `palindrome`, `every`, `almostEvery`, `almostNever`, `someCycles`, `sometimes`, `rarely`, `often`, `always`, `first`, `last`, `chunk`, `chunkBack`, `ply`, `striate`, `chop`, `slice`, `splice`, `linger`, `trunc`, `degrade`, `degradeBy`, `jux`, `juxBy`, `superimpose`, `layer`, `struct`, `mask`, `euclid`, `euclidRot`, `euclidLegato`

### Effects
`lpf`, `hpf`, `bpf`, `vowel`, `cutoff`, `resonance`, `gain`, `velocity`, `amp`, `pan`, `orbit`, `delay`, `delaytime`, `delayfeedback`, `delayfb`, `room`, `size`, `dry`, `crush`, `coarse`, `shape`, `distort`, `speed`, `begin`, `end`, `cut`, `loop`, `loopAt`, `attack`, `decay`, `sustain`, `release`, `hold`, `legato`, `clip`

### Pitch
`transpose`, `add`, `sub`, `mul`, `div`, `scale`, `scaleTranspose`, `voicing`, `rootNotes`

### Control
`cpm`, `bpm`, `setcps`, `hush`, `silence`, `reset`

## 🎹 Keyboard Shortcuts (in Monaco)

| Shortcut | Action |
|----------|--------|
| Ctrl+Enter | Run code |
| Shift+Enter | Run code (alternative) |
| Ctrl+. | Stop playback (hush) |
| Ctrl+Space | Trigger autocomplete |

## 🔧 Personalizzazione

### Cambiare la porta WebSocket
1. In `monaco-strudel-editor.html`, cambia la riga:
   ```javascript
   const WS_URL = 'ws://localhost:9980';
   ```
2. In TouchDesigner, cambia la porta del WebSocket Server DAT

### Aggiungere più samples all'autocompletamento
In `monaco-strudel-editor.html`, trova l'array `sampleNames` e aggiungi i tuoi:
```javascript
const sampleNames = [
    'bd', 'sd', 'hh', 
    // ... aggiungi qui
];
```

### Cambiare i colori del tema
Trova la sezione `monaco.editor.defineTheme('strudel-dark', ...)` e modifica i colori.

## 📚 Risorse

- [Strudel Documentation](https://strudel.cc/learn/)
- [Monaco Editor API](https://microsoft.github.io/monaco-editor/docs.html)
- [TouchDesigner Web Browser COMP](https://docs.derivative.ca/Web_Browser_COMP)

## 🐛 Problemi noti

1. **Audio unlock:** Strudel richiede un'interazione utente per sbloccare l'audio del browser. Clicca nella finestra Strudel almeno una volta.

2. **JavaScript injection:** Il Web Browser COMP di TouchDesigner ha limitazioni nell'esecuzione di JavaScript. Se i metodi standard non funzionano, prova `websocket_callbacks_alt.py` che prova più approcci.

3. **Monaco da CDN:** Se Monaco non carica, potrebbe essere un problema di connettività o CORS. L'hosting esterno è più affidabile del server locale.
