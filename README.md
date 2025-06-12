import streamlit as st
import pandas as pd

st.set_page_config(page_title="Banfield - Control de Pagos", layout="centered")

# Escudo y Título
st.image("https://upload.wikimedia.org/wikipedia/commons/thumb/5/52/Escudo_del_Club_Atl%C3%A9tico_Banfield.svg/1200px-Escudo_del_Club_Atl%C3%A9tico_Banfield.svg.png", width=100)
st.markdown("<h2 style='color:#004d00;'>Control de Pagos - Escuela de Fútbol Banfield</h2>", unsafe_allow_html=True)

dni = st.text_input("Ingresá el DNI del jugador", "")

if dni:
    try:
        url = "https://docs.google.com/spreadsheets/d/1JhP8lEnm245IIzl-awqLFSNdKGmAqGhoTunOm3XV6E0/export?format=csv"
        df = pd.read_csv(url)
        df["DOCUMENTO"] = pd.to_numeric(df["DOCUMENTO"], errors="coerce")
        jugador = df[df["DOCUMENTO"] == int(dni)]

        if not jugador.empty:
            nombre = jugador.iloc[0]["SOCIO"]
            matricula = jugador.iloc[0]["MATRICULA"]
            deuda = []
            meses = ["FEBRERO", "MARZO", "ABRIL", "MAYO", "JUNIO", "JULIO", "AGOSTO", "SEPTIEMBRE", "OCTUBRE", "NOVIEMBRE", "DICIEMBRE"]
            for mes in meses:
                valor = str(jugador.iloc[0].get(mes, "")).strip().upper()
                if valor in ["DEBE", ""]:
                    deuda.append(mes)

            st.subheader(f"🧒 Jugador: {nombre}")
            st.markdown(f"<b>📄 Matrícula:</b> <span style='color:#DAA520;'>{matricula}</span>", unsafe_allow_html=True)
            if deuda:
                st.error(f"❌ El jugador debe los siguientes meses: {', '.join(deuda)}")
            else:
                st.success("✅ El jugador está al día")
        else:
            st.warning("Jugador no encontrado. Verificá el DNI.")
    except Exception as e:
        st.error(f"Error al cargar los datos: {e}")
