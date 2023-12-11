
import tkinter as tk
from tkinter import ttk
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import matplotlib.pyplot as plt
import numpy as np


def pendulo():
    # Obtiene las condiciones iniciales que definirán posteriormente el movimiento del péndulo
    # Input de la
    masa = float(masa_entry.get())
    longitud_pendulo = float(longitud_pendulo_entry.get())
    angulo_inicial = np.radians(float(angulo_inicial_entry.get()))
    # Nueva entrada para la velocidad inicial
    velocidad_inicial = float(velocidad_inicial_entry.get())
    gravedad = float(gravedad_entry.get())

    # Calcula el periodo y la frecuencia
    w = np.sqrt(gravedad / longitud_pendulo)
    frecuencia = 1 / (2 * np.pi) * w
    periodo = 1 / frecuencia

    # Muestra los resultados en la interfaz
    resultados_label.config(
        text=f"Resultados:\n\nFrecuencia: {frecuencia:.4} Hz\nPeriodo: {periodo:.4} s\nFrecuencia Angular (omega): {w:.4} rad/s")

    # Crea un rango de tiempo
    tiempo = np.linspace(0, 4 * periodo, 600)  # Se grafica 4 veces el periodo

    # Calcula la posición, velocidad y aceleración
    if velocidad_inicial == 0 and angulo_inicial == 0:
        # Caso especial cuando la velocidad inicial es cero
        # posicion = longitud_muelle * \
        #     np.cos(angulo_inicial) * np.ones_like(tiempo)
        velocidad = np.zeros_like(tiempo)
        aceleracion = np.zeros_like(tiempo)
        posicion = np.zeros_like(tiempo)

    else:
        # Caso general
        posicion = longitud_pendulo * np.cos(w * tiempo + angulo_inicial)
        velocidad = -longitud_pendulo * w * \
            np.sin(w * tiempo + angulo_inicial) + velocidad_inicial
        aceleracion = -longitud_pendulo * w**2 * \
            np.cos(w * tiempo + angulo_inicial)

    # Limpia los ejes antes de actualizar el gráfico
    ax_posicion.clear()
    ax_velocidad.clear()
    ax_aceleracion.clear()

    # Plotea la posición en función del tiempo
    ax_posicion.plot(tiempo, posicion, label='Posición (m)')
    # ax_posicion.legend()  # Crear la leyenda
    ax_posicion.set_xlabel('Tiempo (s)')
    ax_posicion.set_ylabel('Posición (m)')
    ax_posicion.set_title('Posición respecto al Tiempo')

    # Plotea la velocidad en función del tiempo
    ax_velocidad.plot(tiempo, velocidad, "g", label='Velocidad (m/s)')
    # ax_velocidad.legend()  # Crear la leyenda
    ax_velocidad.set_xlabel('Tiempo (s)')
    ax_velocidad.set_ylabel('Velocidad (m/s)')
    ax_velocidad.set_title('Velocidad respecto al Tiempo')

    # Plotea la aceleración en función del tiempo
    ax_aceleracion.plot(tiempo, aceleracion, "r", label='Aceleración (m/s^2)')
    # ax_aceleracion.legend()  # Crear la leyenda
    ax_aceleracion.set_xlabel('Tiempo (s)')
    ax_aceleracion.set_ylabel('Aceleración (m/s^2)')
    ax_aceleracion.set_title('Aceleración respecto al Tiempo')

    # Ajusta el espacio entre los subgráficos
    plt.subplots_adjust()

    # Actualiza los gráficos
    canvas.draw()


# Configuración de la interfaz gráfica
root = tk.Tk()
root.title(' Oscilación de un péndulo')

# Etiquetas y campos de entrada
masa_label = ttk.Label(root, text='Masa (kg):')
masa_entry = ttk.Entry(root)
masa_label.pack()
masa_entry.pack()


longitud_pendulo_label = ttk.Label(root, text='Longitud del péndulo (m):')
longitud_pendulo_entry = ttk.Entry(root)
longitud_pendulo_label.pack()
longitud_pendulo_entry.pack()


angulo_inicial_label = ttk.Label(root, text='Ángulo inicial (grados):')
angulo_inicial_entry = ttk.Entry(root)
angulo_inicial_label.pack()
angulo_inicial_entry.pack()


gravedad_label = ttk.Label(root, text='Gravedad (m/s^2):')
gravedad_entry = ttk.Entry(root)
gravedad_label.pack()
gravedad_entry.pack()


velocidad_inicial_label = ttk.Label(root, text='Velocidad inicial (m/s):')
velocidad_inicial_entry = ttk.Entry(root)
velocidad_inicial_label.pack()
velocidad_inicial_entry.pack()


# Botón para actualizar el gráfico
update_button = ttk.Button(
    root, text='Actualizar Gráfico', command=pendulo)

# Etiqueta para mostrar los resultados
resultados_label = ttk.Label(root, text="Resultados:")

# Configuración de los gráficos
fig, (ax_posicion, ax_velocidad, ax_aceleracion) = plt.subplots(
    1, 3, figsize=(20, 6))

canvas = FigureCanvasTkAgg(fig, master=root)
canvas_widget = canvas.get_tk_widget()

# Empaquetar elementos


# velocidad_inicial_label.pack()
# velocidad_inicial_entry.pack()
update_button.pack()
resultados_label.pack()
canvas_widget.pack()

# Inicia la interfaz gráfica
root.mainloop()























































import sympy as sp
import numpy as np
import tkinter as tk
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import matplotlib.pyplot as plt

# Antes de comenzar con el analisis de la parte que considera mas dificil del proyecto creo que la parte mas didactica es las interfaz y el proceso de creacion de ellas
# Creacion de la variable del gráfico y de las funciones
x = sp.symbols('x')

# Creacion de la funcion que describe las series de Taylor
# Esta es la parte menos interesante esta hecha en clase ademas no añade ninguna novedad que se considere importante ya que es una funcion que siempre funciona


def calcular_aproximacion_taylor(f_x, a_value, n_terms_value):
    taylor_approximation = 0
    for n in range(n_terms_value+1):  # Hasta el grado del polinomio
        derivada = f_x.diff(x, n)
        # Formula de Taylor
        taylor_term = (derivada.subs(x, a_value) /
                       sp.factorial(n)) * (x - a_value)**n
        taylor_approximation += taylor_term
    return taylor_approximation

# Grafica de ambas funciones


def plot_func_and_approximation(f_x, taylor_approximation, a_value, n_terms_value):
    # Intervalo que se va representar en el eje x
    x_values = np.linspace(a_value-4, a_value + 4, 600)
    # Transformamos mediante la funcion lambdify de la libreria sympy una expresion matematica en expresion numerica
    # Creamos nuevos nombres para simplificar el codigo
    y_original = sp.lambdify(x, f_x)(x_values)
    y_taylor = sp.lambdify(x, taylor_approximation)(x_values)

    # Calculamos las integrales en el intervalo que hemos definido para representar la funcion
    integral_original = sp.integrate(f_x, (x, a_value-4, a_value+4))
    integral_taylor = sp.integrate(
        taylor_approximation, (x, a_value-4, a_value+4))

    # Verificamos si la integral_original es cero antes de calcular el porcentaje
    if integral_original == 0:
        porcentaje = "El valor de la integral de f(x) es cero, por lo tanto, no se puede calcular el porcentaje."
    else:
        # Calculamos el porcentaje en valor absoluto
        porcentaje = abs(
            (integral_taylor - integral_original) / integral_original) * 100

    ax.clear()  # Como es una interfaz esta parte es fundamental ya que borra lo anterior y no hace  que hayan varias funciones representadas
    # El titulo la parte mas dificil
    title_part1 = f'Función Original vs. Aproximación de Taylor (a={a_value})\n' \
        f'Integral Original: {integral_original.evalf()}\n' \
        f'Integral Taylor: {integral_taylor.evalf()}\n' \
        f'Porcentaje: {porcentaje}\n'  # Modificación aquí

    title_part2 = f'Polinomio de Taylor: {sp.N(sp.simplify(taylor_approximation), 5)}'

    ax.set_title(title_part1, fontsize=12)
    ax.text(-0.1, 01.01, title_part2, transform=ax.transAxes,
            fontsize=10)

    ax.plot(x_values, y_original, label='f(x)')
    ax.plot(x_values, y_taylor, 'r',
            label=f'Aproximación de Taylor ({n_terms_value} términos)')

    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.legend()
    ax.grid(True)
    canvas.draw()

# Base de la interfaz


def calcular_y_graficar():
    # Los terminos que se escogen en cada parte
    a_value = float(entry_a.get())
    n_terms_value = int(entry_n_terms.get())
    f_x = sp.sympify(entry_func.get())
    # Calcula ambas funciones con los valores obtenidos
    taylor_approximation = calcular_aproximacion_taylor(
        f_x, a_value, n_terms_value)
    plot_func_and_approximation(
        f_x, taylor_approximation, a_value, n_terms_value)


# Segunda parte de la interfaz a partir de la libreria tinker
root = tk.Tk()
root.title('Aproximación de Taylor')

# Ajustamos la ventana al tamaño de la pantalla ########Toda la pantalla#
screen_width = root.winfo_screenwidth()
screen_height = root.winfo_screenheight()
root.geometry(f'{screen_width}x{screen_height}')

label_func = tk.Label(root, text="Función:")
label_func.pack()

entry_func = tk.Entry(root)
entry_func.insert(0, "x")
entry_func.pack()

label_a = tk.Label(root, text="Valor de 'a':")
label_a.pack()

entry_a = tk.Entry(root)
entry_a.insert(0, "0.0")
entry_a.pack()

label_n_terms = tk.Label(root, text="Grado del polinomio de Taylor:")
label_n_terms.pack()

entry_n_terms = tk.Entry(root)
# Valor inicial del grado de polinomio###Escogemos la recta tangente
entry_n_terms.insert(0, "1")
entry_n_terms.pack()

btn_calcular = tk.Button(
    root, text="Calcular Aproximación de Taylor", command=calcular_y_graficar)
btn_calcular.pack()

fig, ax = plt.subplots()
canvas = FigureCanvasTkAgg(fig, master=root)
widget_canvas = canvas.get_tk_widget()
widget_canvas.pack(fill=tk.BOTH, expand="True")

root.mainloop()

