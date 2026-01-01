import streamlit as st
import json
import os
from PIL import Image
from io import BytesIO
import base64

# ---------------------------
# Funciones auxiliares
# ---------------------------

def cargar_productos():
    if os.path.exists("productos.json"):
        with open("productos.json", "r", encoding="utf-8") as f:
            return json.load(f)
    return []

def guardar_productos(productos):
    with open("productos.json", "w", encoding="utf-8") as f:
        json.dump(productos, f, ensure_ascii=False, indent=4)

def convertir_imagen_a_base64(imagen):
    buffer = BytesIO()
    imagen.save(buffer, format="PNG")
    return base64.b64encode(buffer.getvalue()).decode()

# ---------------------------
# Configuración básica
# ---------------------------

st.set_page_config(page_title="Catálogo Jessi", layout="wide")

st.title("Catálogo de Accesorios para el Pelo")

# ---------------------------
# Estilos personalizados (CSS)
# ---------------------------

st.markdown("""
<style>

body {
    background-color: #FFF7FB;
    font-family: 'Nunito', sans-serif;
}

/* Títulos */
h1, h2, h3 {
    color: #FF8FA3;
    font-family: 'Poppins', sans-serif;
    font-weight: 700;
}

/* Tarjetas de producto */
.product-card {
    background-color: #FFD6A5;
    padding: 20px;
    border-radius: 18px;
    box-shadow: 0px 4px 12px rgba(0,0,0,0.1);
    margin-bottom: 25px;
    text-align: center;
}

/* Botón de WhatsApp */
.whatsapp-btn {
    background-color: #FF8FA3;
    color: white;
    padding: 10px 18px;
    border-radius: 12px;
    text-decoration: none;
    font-weight: bold;
    display: inline-block;
    margin-top: 10px;
}

.whatsapp-btn:hover {
    background-color: #FF6F91;
}

/* Imagen del producto */
.product-img {
    border-radius: 12px;
    margin-bottom: 10px;
    width: 100%;
    max-width: 220px;
}

/* Sidebar */
section[data-testid="stSidebar"] {
    background-color: #FDE2FF;
}

</style>
""", unsafe_allow_html=True)

# ---------------------------
# Autenticación simple
# ---------------------------

st.sidebar.header("Acceso administrador")
clave = st.sidebar.text_input("Clave de administrador", type="password")

# Cambiá esta clave si querés otra
es_admin = (clave == "jessi2024")

if es_admin:
    menu = st.sidebar.selectbox("Menú", ["Ver catálogo", "Agregar producto", "Eliminar producto"])
else:
    menu = "Ver catálogo"

productos = cargar_productos()

# ---------------------------
# Página: Agregar producto
# ---------------------------

if menu == "Agregar producto" and es_admin:
    st.header("Agregar un nuevo producto")

    nombre = st.text_input("Nombre del producto")
    precio = st.number_input("Precio", min_value=0)
    descripcion = st.text_area("Descripción")
    imagen_archivo = st.file_uploader("Subir foto", type=["jpg", "png", "jpeg"])

    if st.button("Guardar producto"):
        if nombre and precio and imagen_archivo:
            imagen = Image.open(imagen_archivo)
            imagen_base64 = convertir_imagen_a_base64(imagen)

            nuevo_producto = {
                "nombre": nombre,
                "precio": precio,
                "descripcion": descripcion,
                "imagen": imagen_base64
            }

            productos.append(nuevo_producto)
            guardar_productos(productos)

            st.success("Producto guardado correctamente")
        else:
            st.error("Falta completar algún dato obligatorio")

# ---------------------------
# Página: Eliminar producto
# ---------------------------

elif menu == "Eliminar producto" and es_admin:
    st.header("Eliminar productos")

    if len(productos) == 0:
        st.info("No hay productos para eliminar")
    else:
        nombres = [p["nombre"] for p in productos]
        seleccion = st.selectbox("Seleccionar producto a eliminar", nombres)

        if st.button("Eliminar"):
            productos = [p for p in productos if p["nombre"] != seleccion]
            guardar_productos(productos)
            st.success(f"Producto '{seleccion}' eliminado")

# ---------------------------
# Página: Ver catálogo (público)
# ---------------------------

else:
    st.header("Catálogo disponible")

    if len(productos) == 0:
        st.info("Todavía no hay productos cargados")
    else:
        cols = st.columns(3)

        for i, p in enumerate(productos):
            with cols[i % 3]:
                card_html = f"""
                <div class="product-card">
                    <img class="product-img" src="data:image/png;base64,{p['imagen']}"/>
                    <h3>{p['nombre']}</h3>
                    <p><strong>Precio:</strong> ${p['precio']}</p>
                    <p>{p['descripcion']}</p>
                    <a class="whatsapp-btn" href="https://wa.me/5490000000000?text=Hola!%20Quiero%20el%20producto:%20{p['nombre']}" target="_blank">
                        Comprar por WhatsApp
                    </a>
                </div>
                """
                st.markdown(card_html, unsafe_allow_html=True)
