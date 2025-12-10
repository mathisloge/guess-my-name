<script setup lang="ts">
import { ElMessage } from 'element-plus'
import { ref } from 'vue'
const START_CODE = 'XMAS2025'
const MATH_SOLUTION = 28.094558
const FINAL_CODE = 'WOBBEL'
const stage = ref(0)
const inputStartCode = ref('')
const showHintMath = ref(false)
const inputMath = ref('')
const inputLocation = ref('')
const inputFinalCode = ref('')
const showHintRaetsel = ref(false)
const showHintCode = ref(false)

function checkStartCode() {
  if (inputStartCode.value.toUpperCase() === START_CODE) {
    stage.value = 1
  } else {
    ElMessage.error('Falscher Code! Versuch es nochmal.')
  }
}

function checkMath() {
  const input = parseFloat(inputMath.value.trim())
  if (input === MATH_SOLUTION) {
    stage.value = 2
    window.scrollTo({ top: 0, behavior: 'smooth' })
  } else {
    ElMessage.error('Das stimmt leider nicht. Überprüfe deine Berechnungen!')
  }
}

function checkLocation() {
  const input = inputLocation.value.toLowerCase().trim()
  if (input.includes('treppe')) {
    stage.value = 3
    window.scrollTo({ top: 0, behavior: 'smooth' })
  } else {
    ElMessage.error('Das ist nicht der richtige Ort! Schaue dir das Meta-Rätsel nochmal an.')
  }
}

function checkFinalCode() {
  if (inputFinalCode.value.toUpperCase() === FINAL_CODE) {
    stage.value = 4
    window.scrollTo({ top: 0, behavior: 'smooth' })
  } else {
    ElMessage.error('Falscher Code! Bist du wirklich am richtigen Ort?')
  }
}

function openGoogleMaps() {
  window.open('https://www.google.de/maps/place/28.0944558,-97.8280543', '_blank')
}
</script>

<template>
  <el-card class="main-card" shadow="always">
    <div class="header-section">
      <div class="header-title">
        <span class="emoji-large">🎄</span>
        <span>Wichtel-Geocache</span>
        <span class="emoji-large">🎁</span>
      </div>
      <p class="header-subtitle">Löse die Rätsel und finde heraus, wer dein Wichtel ist!</p>
    </div>

    <div class="progress-section">
      <el-steps :active="stage" align-center finish-status="success">
        <el-step title="Start"></el-step>
        <el-step title="Rätsel 1"></el-step>
        <el-step title="Rätsel 2"></el-step>
        <el-step title="Code"></el-step>
        <el-step title="Ziel"></el-step>
      </el-steps>
    </div>

    <div class="content-section">
      <!-- Stage 0: Start Code -->
      <transition name="fade" mode="out-in">
        <div v-if="stage === 0" key="stage0">
          <el-alert title="🔐 Zugang" type="info" :closable="false" style="margin-bottom: 20px">
            <template #default>
              Willkommen beim Wichtel-Geocache! Um zu starten, gib den Code von deinem Geschenk ein.
            </template>
          </el-alert>

          <el-input
            v-model="inputStartCode"
            placeholder="Code eingeben..."
            type="password"
            size="large"
            @keyup.enter="checkStartCode"
            style="margin-bottom: 15px"
          >
            <template #prefix>
              <el-icon><Lock /></el-icon>
            </template>
          </el-input>

          <el-button type="primary" size="large" @click="checkStartCode" style="width: 100%">
            Weiter
            <el-icon class="el-icon--right"><Right /></el-icon>
          </el-button>
        </div>
      </transition>

      <!-- Stage 1: Math Riddle -->
      <transition name="fade" mode="out-in">
        <div v-if="stage === 1" key="stage1">
          <el-card class="riddle-card" shadow="hover">
            <div class="riddle-title">🧮 Rätsel 1: Die mathematische Koordinate</div>
            <p style="margin-bottom: 10px"><strong>Ein mathematisches Rätsel:</strong></p>
            <p>Berechne folgende Zahlen und setze sie zusammen:</p>
            <ul class="math-list">
              <li>A = Die Anzahl der Buchstaben im Wort "WICHTELN" / 4</li>
              <li>B = Quadratwurzel von 64</li>
              <li>C = 5² - 25</li>
              <li>
                D = Die Anzahl der Rentiere die den Schlitten des Weihnachtsmanns ziehen, wenn du
                Rudolf dazuzählst?
              </li>
              <li>E = Anzahl der Räder von zwei Fahrädern</li>
              <li>
                F = Am Weihnachtsmorgen stehen vor dem Haus zwei festlich geschmückte Fahrräder.
                Jedes hat einen roten Weihnachtsstern am Lenker und einen grünen Tannenzweig am
                Gepäckträger. Zusätzlich hängt am Zaun noch eine goldene Glocke. Wie viele
                weihnachtliche Dekorationen sind insgesamt zu sehen?
              </li>
              <li>
                G = Wie viele goldene Sterne hängen am Adventskranz, wenn an jedem der vier Kerzen
                ein Stern befestigt ist und in der Mitte noch einer leuchtet?
              </li>
              <li>
                H = Wie viele Geschenke liegen unter dem Weihnachtsbaum, wenn jedes der vier Kinder
                zwei Päckchen bekommt?
              </li>
              <li></li>
            </ul>
            <p style="margin-top: 15px"><strong>Die Koordinate ist: A B . C D E F G H</strong></p>
            <p style="font-size: 14px; color: #666; margin-top: 5px">
              (Format: XX.XXXXXX ohne Leerzeichen)
            </p>
          </el-card>

          <el-button type="warning" plain class="hint-button" @click="showHintMath = !showHintMath">
            <el-icon><Lightbulb /></el-icon>
            {{ showHintMath ? 'Tipp verbergen' : 'Tipp anzeigen' }}
            <el-icon :class="{ 'rotate-180': showHintMath }" style="transition: transform 0.3s">
              <ArrowDown />
            </el-icon>
          </el-button>

          <transition name="fade">
            <el-alert
              v-if="showHintMath"
              type="warning"
              :closable="false"
              style="margin-bottom: 15px"
            >
              💡 Tipp: Rechne Schritt für Schritt und setze die Zahlen als Koordinate direkt
              hintereinander!
            </el-alert>
          </transition>

          <el-input
            v-model="inputMath"
            placeholder="Koordinate eingeben"
            size="large"
            @keyup.enter="checkMath"
            style="margin-bottom: 15px"
          >
          </el-input>

          <el-button type="primary" size="large" @click="checkMath" style="width: 100%">
            Überprüfen
          </el-button>
        </div>
      </transition>

      <!-- Stage 2: Location Riddles -->
      <transition name="fade" mode="out-in">
        <div v-if="stage === 2" key="stage2">
          <div class="coordinates-display" style="margin-bottom: 30px">
            <el-icon :size="40" color="#28a745" style="margin-bottom: 10px">
              <SuccessFilled />
            </el-icon>
            <h3 style="color: #155724; margin-bottom: 10px">Koordinate gefunden!</h3>
            <div class="coord-value">{{ MATH_SOLUTION }} N</div>
          </div>

          <el-alert
            title="🔍 Rätsel 2: Finde den wahren Ort"
            type="info"
            :closable="false"
            style="margin-bottom: 20px"
          >
            <template #default>
              Drei Orte verbergen Geheimnisse. Lies die Gedichte aufmerksam, finde die Orte und löse
              dann das Meta-Rätsel (Öffne noch nicht die Umschläge!):
            </template>
          </el-alert>

          <el-card class="location-card" shadow="hover">
            <h4 style="color: #667eea; font-size: 18px; margin-bottom: 10px">📮 Rätsel 1</h4>
            <div class="poem-box">
              <p class="poem-line">Ein stummer Wächter, kalt und still,</p>
              <p class="poem-line">bewahrt die Botschaft nach eigenem Will'.</p>
              <p class="poem-line">Ein roter Glanz im Winterlicht,</p>
              <p class="poem-line">trägt Grüße heim, die kein Auge bricht.</p>
              <p class="poem-line">Wenn Schnee die Straßen leise deckt,</p>
              <p class="poem-line">ist's hier, wo Freude heimwärts weckt.</p>
            </div>
          </el-card>

          <el-card class="location-card" shadow="hover">
            <h4 style="color: #667eea; font-size: 18px; margin-bottom: 10px">🌀 Rätsel 2</h4>
            <div class="poem-box">
              <p class="poem-line">Tief unten, wo der Frost nicht wohnt,</p>
              <p class="poem-line">ein wirbelndes Lied den Raum belohnt.</p>
              <p class="poem-line">Die Kleider tanzen, nass und sacht,</p>
              <p class="poem-line">wie Schneeflocken in der Winternacht.</p>
              <p class="poem-line">Ein warmer Dampf, ein leises Brummen,</p>
              <p class="poem-line">hier lässt der Winter seine Wäsche summen.</p>
            </div>
          </el-card>

          <el-card class="location-card" shadow="hover">
            <h4 style="color: #667eea; font-size: 18px; margin-bottom: 10px">🪜 Rätsel 3</h4>
            <div class="poem-box">
              <p class="poem-line">Stufen steigen, alt und still,</p>
              <p class="poem-line">durchs Haus, das schläft im Winterwill'.</p>
              <p class="poem-line">Kerzenschein vom Fenster flieht,</p>
              <p class="poem-line">ein Echo leise Schritte sieht.</p>
              <p class="poem-line">Wer leise steigt im Dämmerglanz,</p>
              <p class="poem-line">dem offenbart sich Weihnachtstanz.</p>
            </div>
          </el-card>

          <div class="meta-riddle">
            <div class="meta-riddle-title">🎯 Das Meta-Rätsel</div>
            <div class="poem-box" style="border-color: #667eea">
              <p class="poem-line">Drei Türen stehen offen in der Winternacht,</p>
              <p class="poem-line">drei Geheimnisse, nur eines lacht.</p>
              <p class="poem-line">Ihr Name birgt ein Zauberzeichen,</p>
              <p class="poem-line">ein Buchstabe, der alles kann erreichen.</p>
              <p class="poem-line" style="margin-top: 10px">
                Der häufigste Buchstabe in unserm Wortgebrauch,
              </p>
              <p class="poem-line">zeigt dir den wahren Ort im Haus und auch im Bauch.</p>
              <p class="poem-line">Vergleiche in jedem Namen, der dir begegnet sacht,</p>
              <p class="poem-line">den dritten Buchstaben, der im Licht erwacht.</p>
              <p class="poem-line" style="margin-top: 10px">Wer den wahren Ort gefunden hat,</p>
              <p class="poem-line">entdeckt den Schatz, verborgen hinter Zeichen,</p>
              <p class="poem-line">geheim wie der Winter und wie Rätsel aus fernen Gassen.</p>
              <p class="poem-line">Entschlüssel ihn, wie es Schatzsucher tun –</p>
              <p class="poem-line">nur so öffnet sich das Tor zum Glück und frohem Ruh'n.</p>
            </div>
          </div>

          <el-button
            type="warning"
            plain
            class="hint-button"
            @click="showHintRaetsel = !showHintRaetsel"
          >
            <el-icon><Lightbulb /></el-icon>
            {{ showHintRaetsel ? 'Tipp verbergen' : 'Tipp anzeigen' }}
            <el-icon :class="{ 'rotate-180': showHintRaetsel }" style="transition: transform 0.3s">
              <ArrowDown />
            </el-icon>
          </el-button>

          <transition name="fade">
            <el-alert
              v-if="showHintRaetsel"
              type="warning"
              :closable="false"
              style="margin-bottom: 15px"
            >
              💡 Tipp: Schaue dir den <strong>dritten Buchstaben</strong> in jedem Ortsnamen an. Der
              häufigste Buchstabe im Deutschen ist "E"!
            </el-alert>
          </transition>

          <el-input
            v-model="inputLocation"
            placeholder="Name des Ortes"
            size="large"
            @keyup.enter="checkLocation"
            style="margin-bottom: 15px"
          >
          </el-input>

          <el-button type="primary" size="large" @click="checkLocation" style="width: 100%">
            Ort überprüfen
          </el-button>
        </div>
      </transition>

      <!-- Stage 3: Code Entry -->
      <transition name="fade" mode="out-in">
        <div v-if="stage === 3" key="stage3">
          <el-alert
            title="✅ Richtig erkannt!"
            type="success"
            :closable="false"
            style="margin-bottom: 20px"
          >
            <template #default>
              <p style="margin-bottom: 10px">
                <strong>Das Treppenhaus</strong> ist der richtige Ort!
              </p>
            </template>
          </el-alert>

          <el-alert type="info" :closable="false" style="margin-bottom: 20px">
            <strong>Öffne nun den Umschlag!</strong><br />
            Dort findest du einen versteckten Code. Löse ihn und gib ihn unten ein, um fortzufahren.
          </el-alert>

          <el-card shadow="hover" style="margin-bottom: 20px">
            <div class="riddle-title">🔐 Code-Eingabe</div>
            <p>Hast du den Code im Treppenhaus gelöst? Gib ihn hier ein:</p>
          </el-card>

          <el-input
            v-model="inputFinalCode"
            placeholder="Geheimen Code eingeben..."
            type="password"
            size="large"
            @keyup.enter="checkFinalCode"
            style="margin-bottom: 15px"
          >
            <template #prefix>
              <el-icon><Key /></el-icon>
            </template>
          </el-input>

          <el-button type="primary" size="large" @click="checkFinalCode" style="width: 100%">
            Code überprüfen
          </el-button>

          <el-button type="warning" plain class="hint-button" @click="showHintCode = !showHintCode">
            <el-icon><Lightbulb /></el-icon>
            {{ showHintCode ? 'Tipp verbergen' : 'Tipp anzeigen' }}
            <el-icon :class="{ 'rotate-180': showHintCode }" style="transition: transform 0.3s">
              <ArrowDown />
            </el-icon>
          </el-button>

          <transition name="fade">
            <el-alert
              v-if="showHintCode"
              type="warning"
              :closable="false"
              style="margin-bottom: 15px"
            >
              💡 Zwei Zahlen flüstern mir den Pfad:<br />
              die Vier und die Sieben, eng verwoben.<br />
              Sie bilden keine Jahreszeit,<br />
              doch weisen sie auf einen alten Code der Sucher.<br />
              <br />
              ,Schau dorthin, wo alle Zeichen wohnen',<br />
              sprach einst ein Wanderer im Schnee.<br />
              ,Greif in die Mitte ihrer Reihen<br />
              und verschiebe dein Blick um siebenundvierzig Schritte -<br />
              dann erwacht das Verborgene im rotem Licht.<br />
            </el-alert>
          </transition>
        </div>
      </transition>

      <!-- Stage 4: Final Coordinates -->
      <transition name="fade" mode="out-in">
        <div v-if="stage === 4" key="stage4" style="text-align: center">
          <div class="final-emoji">🎉</div>
          <h2 style="font-size: 36px; color: #667eea; margin-bottom: 20px">Perfekt!</h2>
          <p style="font-size: 18px; color: #666; margin-bottom: 30px">
            Du hast alle Rätsel gelöst!
          </p>

          <div class="coordinates-display" style="margin: 30px 0">
            <div class="coord-value">{{ MATH_SOLUTION }} N</div>
            <div class="coord-value">97.8280543 W</div>
          </div>

          <el-card class="location-card" shadow="hover">
            <h4 style="color: #667eea; font-size: 18px; margin-bottom: 10px">
              🗺️ So geht's weiter:
            </h4>
            <div class="poem-box">
              <p class="poem-line">Im warmen Schein der Weihnachtszeit,</p>
              <p class="poem-line">da steht ein Plan, ganz still bereit.</p>
              <p class="poem-line">Öffne Google Maps, so klar und fein,</p>
              <p class="poem-line">dort soll dein erster Schritt nun sein.</p>
              <p class="poem-line" style="margin-top: 10px">Dann tippe leis' und ohne Hast,</p>
              <p class="poem-line">die Zahlen ein, die führen fast:</p>
              <p class="poem-line">{{ MATH_SOLUTION }}, -97.8280543, ganz genau,</p>
              <p class="poem-line">und schau, wohin die Karte zeigt dir lau.</p>
              <p class="poem-line" style="margin-top: 10px">Betrachte still den Ortsnamen sacht,</p>
              <p class="poem-line">dann weißt du, wer dein Wichtel lacht.</p>
            </div>
          </el-card>

          <el-button type="success" size="large" @click="openGoogleMaps" style="width: 100%">
            <el-icon><Location /></el-icon>
            Führe mich zum Ort des Wichtels
          </el-button>

          <p style="margin-top: 30px; color: #999; font-size: 14px">Frohe Weihnachten! 🎄✨</p>
        </div>
      </transition>
    </div>
  </el-card>
</template>

<style scoped>
.main-card {
  border-radius: 20px;
  overflow: hidden;
}

.header-section {
  text-align: center;
  padding: 30px 20px 20px;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
}

.header-title {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
  margin-bottom: 10px;
  font-size: 32px;
  font-weight: bold;
  color: #333;
}

.emoji-large {
  font-size: 48px;
}

.header-subtitle {
  color: #666;
  font-size: 16px;
}

.progress-section {
  padding: 20px;
  background: #fff;
}

.content-section {
  padding: 30px;
  min-height: 400px;
}

.riddle-card {
  margin-bottom: 20px;
}

.riddle-title {
  font-size: 20px;
  font-weight: bold;
  color: #667eea;
  margin-bottom: 15px;
  display: flex;
  align-items: center;
  gap: 10px;
}

.poem-box {
  background: #f8f9fa;
  border: 2px solid #667eea;
  border-radius: 8px;
  padding: 20px;
  font-family: Georgia, serif;
  font-style: italic;
  line-height: 1.8;
  color: #333;
}

.poem-line {
  margin: 5px 0;
}

.location-card {
  margin-bottom: 15px;
}

.meta-riddle {
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.1) 0%, rgba(118, 75, 162, 0.1) 100%);
  border: 3px solid #667eea;
  border-radius: 12px;
  padding: 25px;
  margin: 25px 0;
}

.meta-riddle-title {
  text-align: center;
  font-size: 22px;
  font-weight: bold;
  color: #667eea;
  margin-bottom: 20px;
}

.coordinates-display {
  text-align: center;
  padding: 30px;
  background: #d4edda;
  border: 2px solid #28a745;
  border-radius: 12px;
  margin: 20px 0;
}

.coord-value {
  font-size: 32px;
  font-weight: bold;
  color: #155724;
  font-family: 'Courier New', monospace;
  margin: 10px 0;
}

.math-list {
  margin-left: 20px;
  margin-top: 10px;
}

.math-list li {
  margin: 8px 0;
  line-height: 1.6;
}

.hint-button {
  width: 100%;
  margin: 15px 0;
}

.final-emoji {
  font-size: 80px;
  text-align: center;
  margin: 20px 0;
}

.fade-enter-active,
.fade-leave-active {
  transition:
    opacity 0.3s,
    transform 0.3s;
}

.fade-enter-from {
  opacity: 0;
  transform: translateY(20px);
}

.fade-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}

.el-step__title {
  font-size: 13px;
}
</style>
