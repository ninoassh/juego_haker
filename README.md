import streamlit as st
import time

st.set_page_config(page_title="Hacker Math - Combinación 2", page_icon="💻", layout="centered")

st.markdown("""
    <style>
    .stApp { background-color: #0f0f1a; color: #00ff66; font-family: 'Courier New', Courier, monospace; }
    h1, h2, h3 { color: #00ff66 !important; }
    .stButton>button { background-color: #252545; color: #00ff66; border: 1px solid #00ff66; font-family: 'Courier New', Courier, monospace; font-weight: bold; width: 100%; }
    .stButton>button:hover { background-color: #00ff66; color: #0f0f1a; }
    .box-mision { background-color: #16162c; padding: 15px; border-radius: 5px; border: 1px solid #00ccff; color: #ffffff; }
    </style>
""", unsafe_allow_html=True)

if "etapa" not in st.session_state:
    st.session_state.etapa = "lobby"
if "jugadores" not in st.session_state:
    st.session_state.jugadores = []
if "puntajes" not in st.session_state:
    st.session_state.puntajes = {}
if "niveles" not in st.session_state:
    st.session_state.niveles = {}
if "jugador_actual_idx" not in st.session_state:
    st.session_state.jugador_actual_idx = 0

preguntas = [
    {
        "titulo": "MISIÓN 01: PUERTA LÉSER (Comb. r=2)",
        "texto": "Para descifrar el primer nivel, debes elegir un par (2 elementos) de un total de 5 cables de seguridad. Aplicando C(5,2), ¿cuántas parejas posibles existen?",
        "opciones": ["10 combinaciones", "20 combinaciones", "5 combinaciones", "25 combinaciones"],
        "correcta": 0
    },
    {
        "titulo": "MISIÓN 02: DOBLE LLAVE MAESTRA (Comb. r=2)",
        "texto": "Un panel de control requiere seleccionar 2 llaves de cifrado de un grupo de 6 llaves disponibles. Usando la combinación C(6,2), ¿cuántas parejas se pueden formar?",
        "opciones": ["12 combinaciones", "15 combinaciones", "30 combinaciones", "6 combinaciones"],
        "correcta": 1
    },
    {
        "titulo": "MISIÓN 03: NODOS DE RED DUOS (Comb. r=2)",
        "texto": "El servidor principal tiene 7 nodos y debes activar exactamente 2 nodos simultáneamente. ¿Cuántas combinaciones C(7,2) forman un par operativo?",
        "opciones": ["14 combinaciones", "42 combinaciones", "21 combinaciones", "28 combinaciones"],
        "correcta": 2
    },
    {
        "titulo": "MISIÓN 04: PROTOCOLO BINARIO (Comb. r=2)",
        "texto": "De un conjunto de 8 protocolos de seguridad, necesitas sincronizar un grupo de 2 protocolos en paralelo. Aplicando C(8,2), ¿cuántas combinaciones de a 2 existen?",
        "opciones": ["56 combinaciones", "28 combinaciones", "16 combinaciones", "64 combinaciones"],
        "correcta": 1
    },
    {
        "titulo": "MISIÓN 05: FIREWALL DE PAREJAS (Comb. r=2)",
        "texto": "Se examinan 10 puertos del sistema para elegir un par (2 puertos) que soporten el puente de datos. ¿Cuántas combinaciones C(10,2) se pueden seleccionar?",
        "opciones": ["90 combinaciones", "20 combinaciones", "100 combinaciones", "45 combinaciones"],
        "correcta": 3
    }
]

if st.session_state.etapa == "lobby":
    st.markdown("<h1>SYS_HACK // MÓDULO COMBINACIÓN 2</h1>", unsafe_allow_html=True)
    st.markdown("<h3>[ REGISTRO DE AGENTES ]</h3>", unsafe_allow_html=True)
    
    st.info("Ingresa los nombres de los hackers que competirán resolviendo misiones de combinaciones de orden 2 (grupos de 2).")
    
    nombre_nuevo = st.text_input("Alias del Agente:")
    
    col1, col2 = st.columns(2)
    with col1:
        if st.button("+ Agregar Agente"):
            nombre_limpio = nombre_nuevo.strip()
            if nombre_limpio and nombre_limpio not in st.session_state.jugadores:
                st.session_state.jugadores.append(nombre_limpio)
                st.session_state.puntajes[nombre_limpio] = 0
                st.session_state.niveles[nombre_limpio] = 0
                st.rerun()
            elif not nombre_limpio:
                st.warning("Escribe un nombre válido.")
            else:
                st.warning("Este agente ya está registrado.")
                
    st.write("---")
    st.write("**Agentes listos:**")
    if st.session_state.jugadores:
        for j in st.session_state.jugadores:
            st.write(f"• {j}")
            
        if st.button("INICIAR COMPETENCIA ONLINE"):
            st.session_state.etapa = "juego"
            st.session_state.jugador_actual_idx = 0
            st.rerun()
    else:
        st.write("Ningún agente inscrito todavía.")

elif st.session_state.etapa == "juego":
    jugador_actual = st.session_state.jugadores[st.session_state.jugador_actual_idx]
    nivel = st.session_state.niveles[jugador_actual]
    
    if nivel >= len(preguntas):
        st.session_state.etapa = "podio"
        st.rerun()
        
    st.markdown(f"### Agente en Terminal: `{jugador_actual}`")
    st.write(f"Misión {nivel + 1} de {len(preguntas)}")
    
    p = preguntas[nivel]
    
    st.markdown(f"""
        <div class="box-mision">
            <b>{p['titulo']}</b><br><br>
            {p['texto']}
        </div>
    """, unsafe_allow_html=True)
    
    st.write("")
    seleccion = st.radio("Selecciona tu respuesta:", p["opciones"], key=f"q_{nivel}_{jugador_actual}")
    
    if st.button("EJECUTAR HACKEO"):
        with st.spinner("[ SISTEMA HACKEANDO (r=2)... ] Calculando C(n,2) = nPr / 2!..."):
            time.sleep(1.2)
            
        idx_elegido = p["opciones"].index(seleccion)
        if idx_elegido == p["correcta"]:
            st.session_state.puntajes[jugador_actual] += 20
            st.success("¡Código aceptado! Misión superada con éxito.")
        else:
            st.error("¡Acceso denegado! Alarma activada.")
            
        st.session_state.niveles[jugador_actual] += 1
        st.session_state.jugador_actual_idx = (st.session_state.jugador_actual_idx + 1) % len(st.session_state.jugadores)
        time.sleep(1)
        st.rerun()

elif st.session_state.etapa == "podio":
    st.markdown("<h1>🏆 COMPETENCIA TERMINADA 🏆</h1>", unsafe_allow_html=True)
    st.markdown("<h3>TABLA DE POSICIONES</h3>", unsafe_allow_html=True)
    
    ranking = sorted(st.session_state.jugadores, key=lambda j: st.session_state.puntajes[j], reverse=True)
    ganador = ranking[0]
    
    st.success(f"🥇 AGENTE MVP: {ganador} con {st.session_state.puntajes[ganador]} puntos")
    
    st.write("---")
    st.write("**Reporte Final de la Red:**")
    for idx, j in enumerate(ranking):
        st.write(f"{idx + 1}. **{j}** — {st.session_state.puntajes[j]} pts")
        
    st.write("")
    col_a, col_b = st.columns(2)
    with col_a:
        if st.button("Volver al Menú Principal"):
            st.session_state.etapa = "lobby"
            st.session_state.jugadores = []
            st.session_state.puntajes = {}
            st.session_state.niveles = {}
            st.rerun()
    with col_b:
        if st.button("Reiniciar Competencia"):
            for j in st.session_state.jugadores:
                st.session_state.puntajes[j] = 0
                st.session_state.niveles[j] = 0
            st.session_state.etapa = "juego"
            st.session_state.jugador_actual_idx = 0
            st.rerun()
