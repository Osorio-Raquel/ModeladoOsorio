<template>
  <div class="generador-card">
    <h2 class="title">Generador Multiplicativo Óptimo</h2>

    <div class="form">
      <label>
        Período
        <input type="number" v-model.number="periodo" min="1" @input="validarPeriodo"/>
      </label>

      <label>
        Semilla (X0, impar)
        <input type="number" v-model.number="semilla" min="1" @input="validarSemilla"/>
      </label>

      <label>
        k
        <input type="number" v-model.number="k" min="0" @input="validarNumero('k')"/>
      </label>

      <label>
        a
        <select v-model="tipoA">
          <option value="3">3 + 8k</option>
          <option value="5">5 + 8k</option>
        </select>
      </label>

      <button class="btn-generate" @click="generar">Generar</button>
    </div>

    <p v-if="error" class="error">{{ error }}</p>

    <div v-if="resultadosBase.length" class="info-calc">
      <div><strong>g =</strong> {{ gComputed }} <span class="formula">(g = (ln P / ln 2) + 2)</span></div>
      <div><strong>m =</strong> {{ mComputed }} <span class="formula">(m = 2^g)</span></div>
      <div><strong>a =</strong> {{ aComputed }} <span class="formula">(a = {{tipoA}} + 8*k)</span></div>
    </div>

    <div v-if="resultadosBase.length" class="decimales-control">
      <button class="btn-icon" @click="disminuirDecimales">−</button>
      <div class="decimales-label">{{ decimales }} decimales</div>
      <button class="btn-icon" @click="aumentarDecimales">+</button>
    </div>

    <div v-if="resultados.length" class="table-wrap">
      <table>
        <thead>
          <tr>
            <th>N° Op</th>
            <th>Operación</th>
            <th>Xi</th>
            <th>Ri</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row in resultados" :key="row.numero" class="row-anim">
            <td>{{ row.numero }}</td>
            <td class="mono">{{ row.operacion }}</td>
            <td>{{ row.xi }}</td>
            <td>{{ row.ri }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

const periodo = ref(null);
const semilla = ref(null);
const k = ref(null);
const tipoA = ref("3"); // "3" o "5"

const decimales = ref(3);
const resultadosBase = ref([]);
const error = ref("");

// Fórmulas
const gComputed = computed(() => {
  if (!periodo.value || periodo.value <= 0) return null;
  return (Math.log(periodo.value) / Math.log(2)) + 2;
});
const mComputed = computed(() => gComputed.value != null ? 2**gComputed.value : null);
const aComputed = computed(() => k.value != null ? parseInt(tipoA.value) + 8*k.value : null);

// Validaciones
function validarNumero(campo) {
  if (isNaN(eval(campo).value) || eval(campo).value < 0) {
    alert(`El campo ${campo} debe ser un número positivo`);
    eval(campo).value = null;
  }
}

function validarSemilla() {
  if (isNaN(semilla.value) || semilla.value < 1) {
    alert("La semilla X0 debe ser un número positivo");
    semilla.value = null;
    return;
  }
  if (semilla.value % 2 === 0) {
    alert("La semilla X0 debe ser impar");
    semilla.value = null;
  }
}

function validarPeriodo() {
  if (!periodo.value || periodo.value <= 0) {
    alert("El período debe ser un número positivo");
    periodo.value = null;
    return;
  }
  // Verificar potencia de 2
  if ((periodo.value & (periodo.value - 1)) !== 0) {
    alert("El período debe ser potencia de 2");
    periodo.value = null;
  }
}

// Generación de números
function generar() {
  error.value = "";
  if (!periodo.value || !semilla.value || !k.value) {
    error.value = "Complete todos los campos correctamente.";
    return;
  }

  resultadosBase.value = [];
  let xiPrev = semilla.value;
  for (let i = 0; i <= periodo.value; i++) {
    const operacion = `(${aComputed.value} * ${xiPrev}) mod ${mComputed.value}`;
    const xi = (aComputed.value * xiPrev) % mComputed.value;
    resultadosBase.value.push({ numero: i+1, operacion, xi, m: mComputed.value });
    xiPrev = xi;
  }
}

// Resultados con decimales
const resultados = computed(() =>
  resultadosBase.value.map(r => ({
    ...r,
    ri: (r.xi / (r.m - 1)).toFixed(decimales.value)
  }))
);

function aumentarDecimales(){ if(decimales.value<10) decimales.value++; }
function disminuirDecimales(){ if(decimales.value>0) decimales.value--; }
</script>

<style scoped>
.generador-card {
  background: #fff;
  border-radius: 14px;
  padding: 1.6rem;
  max-width: 980px;
  margin: 1rem auto;
  box-shadow: 0 10px 30px rgba(0,0,0,0.06);
  text-align: center;
}

.title { color:#b30000; margin-bottom:1rem; }
.form { display:flex; flex-wrap:wrap; gap:0.8rem; justify-content:center; align-items:end; margin-bottom:1rem; }
.form label { display:flex; flex-direction:column; font-weight:600; color:#333; min-width:120px; }
.form input, .form select { margin-top:0.4rem; padding:0.45rem 0.6rem; border-radius:8px; border:1px solid #e6e6e6; text-align:center; width:140px; }
.btn-generate { background: linear-gradient(180deg,#cc0000,#a50000); color:white; border:none; padding:0.65rem 1rem; border-radius:10px; cursor:pointer; }
.btn-generate:hover { transform:translateY(-3px); box-shadow:0 6px 18px rgba(204,0,0,0.18); }

.error { color:#d00000; margin:0.6rem 0; }
.info-calc { display:flex; gap:1.2rem; justify-content:center; margin-bottom:0.6rem; color:#444; font-weight:600; }
.formula { font-weight:400; color:#666; font-size:0.85rem; margin-left:0.4rem; }

.decimales-control { display:flex; gap:0.6rem; justify-content:center; align-items:center; margin-bottom:1rem; }
.btn-icon { display:flex; align-items:center; justify-content:center; width:40px; height:40px; border-radius:50%; border:none; background:#cc0000; color:white; font-size:20px; cursor:pointer; }
.btn-icon:hover { transform:scale(1.05); box-shadow:0 6px 18px rgba(204,0,0,0.18); }
.decimales-label { font-weight:700; color:#333; }

.table-wrap { overflow-x:auto; }
table { width:100%; border-collapse:collapse; margin-top:0.6rem; }
th, td { padding:0.6rem 0.45rem; border:1px solid #eee; text-align:center; }
th { background:#cc0000; color:#fff; font-weight:700; }
.mono { font-family:ui-monospace, Menlo, Monaco, "Roboto Mono", monospace; font-size:0.92rem; color:#222; }
.row-anim { animation: rowIn .25s ease; }
@keyframes rowIn { from {transform:translateY(6px);opacity:0;} to {transform:translateY(0);opacity:1;} }
</style>
