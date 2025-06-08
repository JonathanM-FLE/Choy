# TMPL_FLE_EXERCISE_v1 - Documentation Claude

## CORE_SPECS
```
Purpose: FLE exercises template (Français Langue Étrangère)
Tech: HTML+CSS+JS / Claude_palette / responsive
Features: CECR_levels, bilingual_instructions, examples, markdown_export, auto_catalog
Deploy: github_pages_single_repo / netlify / notion_embed
Catalog: auto-generated, auto-maintained, URL_copying
```

## PROJECT_STRUCTURE
```
📂 fle-catalog/
├── 📄 index.html (auto-catalog with search/filters)
├── 📁 exercises/
│   ├── a1-salutations-qcm.html
│   ├── a1-prononciation-bonjour.html
│   ├── a2-verbes-reguliers-drag.html
│   ├── b1-subjonctif-vrai-faux.html
│   └── ... (auto-detected by catalog)
└── 📁 assets/ (shared CSS, audio, images)

Base URL: https://username.github.io/fle-catalog/
Exercise URLs: {base_url}/exercises/{filename}
```

## FILENAME_CONVENTION
```
Pattern: {level}-{topic}-{type}.html
Examples:
- a1-salutations-qcm.html
- a2-verbes-reguliers-drag.html  
- b1-prononciation-bonjour.html
- b2-subjonctif-texte-trous.html
- c1-argumentation-reponse-libre.html

Slugification rules:
- Lowercase only
- Replace spaces with hyphens
- Remove accents: é→e, à→a, ç→c
- Remove special chars except hyphens
```

## EXERCISE_TYPES
```
Implemented: [qcm, vrai_faux, reponse_libre, flashcards, drag_drop, image_interactive, pronunciation]
Pending: [tri_categories, timeline, relier_colonnes, legendes, texte_trous, 
         mots_croises, reorganiser_phrases, surlignage, quiz_progressif, parcours_adaptatif]
```

## CONFIG_STRUCTURE
```javascript
const exerciseConfig = {
    type: "qcm", // voir EXERCISE_TYPES
    title: "Question FLE",
    subtitle: "Grammaire française",
    cecr_level: "A2", // A1, A2, B1, B2, C1, C2
    filename: "a2-grammaire-qcm.html", // auto-generated from metadata
    content: {
        question: "Quelle est la forme correcte ?",
        question_en: "What is the correct form?", // tooltip_hover
        options: ["Je mange", "Je manges", "Je manger"],
        correct: 0,
        feedback: {
            correct: "Parfait ! 'Je mange' est la forme correcte.",
            incorrect: "Non, avec 'je' on utilise la 1ère personne du singulier."
        },
        example: "Exemple : Je mange une pomme. (I eat an apple)"
    }
};
```

## AUTO_CATALOG_INTEGRATION
```javascript
// Metadata extracted for catalog
exercise_metadata = {
    filename: generateFilename(level, topic, type),
    title: config.title,
    level: config.cecr_level,
    type: config.type,
    description: auto_generated_description,
    url: base_url + "/exercises/" + filename
}

// Filename generation
function generateFilename(level, topic, type) {
    const slug = topic.toLowerCase()
        .replace(/[éèêë]/g, 'e')
        .replace(/[àâä]/g, 'a') 
        .replace(/[ç]/g, 'c')
        .replace(/[îï]/g, 'i')
        .replace(/[ôö]/g, 'o')
        .replace(/[ùûü]/g, 'u')
        .replace(/\s+/g, '-')
        .replace(/[^a-z0-9-]/g, '');
    
    return `${level.toLowerCase()}-${slug}-${type}.html`;
}
```

## CECR_INTEGRATION
```css
.cecr-badge {
    background: colors_per_level;
    A1: #E8F5E8, A2: #FFF8DC, B1: #E6F3FF, 
    B2: #F0E6FF, C1: #FFE6E6, C2: #F5F5DC
}
```

## AUDIO_SYSTEM
```javascript
// Pronunciation exercise config
{
    type: "pronunciation",
    cecr_level: "A1",
    content: {
        text_to_read: "Bonjour, comment allez-vous ?",
        text_to_read_en: "Hello, how are you?",
        example: "Écoutez d'abord, puis répétez",
        phonetic: "[bon-ZHOOR, ko-mahn tah-lay VOO]",
        audio_model: "audio/bonjour.mp3", // reference audio
        teacher_email: "prof@exemple.com" // for audio sending
    }
}

// Audio recording flow
navigator.mediaDevices.getUserMedia({audio: true})
→ MediaRecorder API
→ Compression (opus/webm)
→ Base64 encoding
→ Email with attachment
```

## AUDIO_FEATURES
```
- getUserMedia() permission request
- Real-time recording indicator
- Playback student recording
- Compare with model audio
- Compression: opus/webm format
- Auto-email with audio attachment
- Waveform visualization (optional)
```
```javascript
// Hover tooltips pour toutes les consignes
<span class="instruction" data-en="English translation">Consigne française</span>

// CSS
.instruction:hover::after {
    content: attr(data-en);
    // positioning + styling
}
```

## MANDATORY_ELEMENTS
```
- cecr_level display (badge top-right)
- example section (before exercise)
- bilingual instructions (hover tooltips)
- markdown export avec CECR level
```

## TEMPLATE_ARCHITECTURE
```
ExerciseManager {
    constructor() {
        this.cecrLevel = config.cecr_level
        this.startTime = new Date()
        this.answers = []
        this.audioRecorder = null
        this.recordedAudio = null
    }
    
    render() {
        renderCecrBadge()
        renderExample()
        renderBilingualInstructions()
        renderExerciseByType()
        if(type === 'pronunciation') initAudioSystem()
    }
    
    initAudioSystem() {
        setupMediaRecorder()
        setupAudioControls()
        setupEmailSending()
    }
    
    generateMarkdownReport() {
        // Include CECR level in header
        // Format: "# 📚 Résultats FLE - [CECR_LEVEL] - [TYPE]"
        // For pronunciation: include audio attachment notice
    }
}
```

## STYLE_GUIDE
```css
/* Claude palette */
primary: #F7F3F0 (bg)
accent: #CC785C (buttons, highlights)
text: #2F2F2F
secondary: #E5E5E5 (borders)
success: #4A8B3A
error: #C53030

/* Typography */
font-family: 'Crimson Text', Georgia, serif
```

## USAGE_INSTRUCTIONS
```
Claude workflow:
1. Receive: raw markdown exercises from user
2. Generate: individual HTML files with proper filenames
3. Output: complete exercises/ folder + updated catalog
4. User: single push to GitHub → all exercises live + auto-catalog

Filename generation:
- Extract level + topic from exercise content
- Apply slugification rules
- Ensure uniqueness with auto-increment if needed
- Format: {level}-{topic-slug}-{type}.html

Catalog auto-update:
- Scans exercises/ directory on page load
- Extracts metadata from each HTML file
- Groups by CECR level automatically
- Provides copy-paste URLs for Notion embedding
```

## DEPLOYMENT_WORKFLOW
```
1. User creates GitHub repo "fle-catalog"
2. Claude generates:
   - index.html (auto-catalog)
   - exercises/*.html (individual exercises)
   - README.md with setup instructions
3. User pushes to GitHub + enables Pages
4. Auto-catalog available at: username.github.io/fle-catalog
5. Individual exercises: username.github.io/fle-catalog/exercises/filename.html
6. Copy URLs from catalog → embed in Notion
```

## EXTENSION_PATTERNS
```javascript
// Add new exercise type
case 'new_type':
    this.renderNewType(content);
    break;

case 'pronunciation':
    this.renderPronunciation(content);
    break;

renderNewType(container) {
    container.innerHTML = `
        <div class="cecr-badge">${this.cecrLevel}</div>
        <div class="example-box">${content.example}</div>
        <div class="instruction" data-en="${content.question_en}">
            ${content.question}
        </div>
        <!-- exercise content -->
    `;
}

renderPronunciation(container) {
    container.innerHTML = `
        <div class="cecr-badge">${this.cecrLevel}</div>
        <div class="example-box">${content.example}</div>
        
        <div class="pronunciation-text">
            <div class="text-to-read">${content.text_to_read}</div>
            <div class="phonetic">${content.phonetic}</div>
        </div>
        
        <div class="audio-controls">
            <audio controls src="${content.audio_model}">Audio modèle</audio>
            <button class="btn record-btn" onclick="exercise.startRecording()">🎤 Enregistrer</button>
            <button class="btn stop-btn" onclick="exercise.stopRecording()" disabled>⏹️ Arrêter</button>
            <audio id="playback" controls style="display:none"></audio>
        </div>
        
        <div class="audio-actions" style="display:none">
            <button class="btn" onclick="exercise.sendAudioEmail()">📧 Envoyer l'enregistrement</button>
        </div>
    `;
}
```

## EXAMPLES_PER_TYPE
```javascript
// QCM A2 Example → a2-conjugaison-qcm.html
{
    type: "qcm",
    cecr_level: "A2",
    title: "Conjugaison au présent",
    content: {
        question: "Complétez : Je ___ français.",
        question_en: "Complete: I ___ French.",
        example: "Exemple : Je parle anglais. (I speak English)",
        options: ["parle", "parles", "parlent"],
        correct: 0
    }
}

// Drag&Drop B1 Example → b1-verbes-complements-drag.html
{
    type: "drag_drop",
    cecr_level: "B1", 
    title: "Verbes et compléments",
    content: {
        question: "Associez les verbes aux compléments",
        question_en: "Match verbs with complements",
        example: "Exemple : manger → une pomme",
        items: [
            {text: "lire", targetId: 0},
            {text: "écouter", targetId: 1}
        ],
        targets: [
            {label: "un livre"},
            {label: "de la musique"}
        ]
    }
}

// Pronunciation A1 Example → a1-salutations-prononciation.html
{
    type: "pronunciation",
    cecr_level: "A1",
    title: "Salutations de base",
    content: {
        text_to_read: "Bonjour, je m'appelle Marie.",
        text_to_read_en: "Hello, my name is Marie.",
        example: "Écoutez l'audio modèle, puis enregistrez-vous",
        phonetic: "[bon-ZHOOR, zhuh mah-PELL mah-REE]",
        audio_model: "audio/marie.mp3",
        teacher_email: "prof@exemple.com"
    }
}
```

## MARKDOWN_OUTPUT_FORMAT
```markdown
# 📚 Résultats FLE - [CECR_LEVEL] - [TYPE]

**Niveau CECR :** [A1/A2/B1/B2/C1/C2]  
**Date :** [timestamp]  
**Exercice :** [title]  

---

## 📊 Performance
- **Score final :** ✅/❌ X/Y (Z%)
- **Temps passé :** ⏱️ XminYs

---

## 📝 Détail des réponses
[answers_breakdown]

---

*Exercice FLE généré automatiquement*
```

## CLAUDE_GENERATION_WORKFLOW
```
Input: User provides raw markdown exercises
Process:
1. Parse exercise content + extract metadata
2. Determine CECR level from content complexity
3. Generate appropriate filename using convention
4. Create HTML file with template + config
5. Add bilingual instructions + examples
6. Include metadata for auto-catalog detection
7. Output complete exercises/ directory structure

Batch generation:
- Multiple exercises in single session
- Consistent filename convention
- Auto-increment for duplicates (topic-qcm-2.html)
- Generate index.html catalog alongside exercises
- Provide deployment-ready file structure
```

## AUTO_CATALOG_FEATURES
```
Tech: Vanilla JS, no dependencies
Features:
- Auto-scan exercises/ directory
- Extract metadata from HTML files
- Group by CECR level (A1→C2)
- Search: title, description, level
- Filter: by exercise type
- Copy URL button → clipboard ready for Notion
- Preview button → new tab
- Responsive design
- Real-time stats (exercise count, levels, types)
```

## FUTURE_DEVELOPMENT
```
Priority extensions:
1. ✅ Audio support (pronunciation exercises with recording)
2. Image selection exercises  
3. Verb conjugation tables
4. Grammar pattern matching
5. Listening comprehension with audio
6. Speech-to-text validation for pronunciation
7. Audio waveform visualization
8. Batch audio processing for classes
```

## TECHNICAL_REQUIREMENTS
```
Browser APIs needed:
- MediaRecorder API (audio recording)
- getUserMedia() (microphone access)
- Blob/File API (audio compression)
- Email services or SMTP integration

Audio formats:
- Input: WebM/Opus (best compression)
- Fallback: MP3/AAC
- Max duration: 30 seconds per recording
- Auto-compression for email attachments
```