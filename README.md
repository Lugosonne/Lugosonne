##############PENDULO TOTAL
import tkinter as tk
from tkinter import ttk
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import matplotlib.pyplot as plt
import numpy as np


def update_plot():
    # Obtiene las condiciones iniciales que definirán posteriormente el movimiento del péndulo
    masa = float(masa_entry.get())
    longitud_muelle = float(longitud_muelle_entry.get())
    angulo_inicial = np.radians(float(angulo_inicial_entry.get()))
    # Nueva entrada para la velocidad inicial
    velocidad_inicial = float(velocidad_inicial_entry.get())
    gravedad = float(gravedad_entry.get())

    # Calcula el periodo y la frecuencia
    w = np.sqrt(gravedad / longitud_muelle)
    frecuencia = 1 / (2 * np.pi) * w
    periodo = 1 / frecuencia

    # Muestra los resultados en la interfaz
    resultados_label.config(
        text=f"Resultados:\n\nFrecuencia: {frecuencia:.2f} Hz\nPeriodo: {periodo:.2f} s\nFrecuencia Angular (omega): {w:.2f} rad/s")

    # Crea un rango de tiempo
    t = np.linspace(0, 4 * periodo, 1000)

    # Calcula la posición, velocidad y aceleración
    if velocidad_inicial == 0:
        # Caso especial cuando la velocidad inicial es cero
        posicion = longitud_muelle * np.cos(angulo_inicial) * np.ones_like(t)
        velocidad = np.zeros_like(t)
        aceleracion = np.zeros_like(t)
    else:
        # Caso general
        posicion = longitud_muelle * np.cos(w * t + angulo_inicial)
        velocidad = -longitud_muelle * w * \
            np.sin(w * t + angulo_inicial) + velocidad_inicial
        aceleracion = -longitud_muelle * w**2 * np.cos(w * t + angulo_inicial)

    # Limpia los ejes antes de actualizar el gráfico
    ax_posicion.clear()
    ax_velocidad.clear()
    ax_aceleracion.clear()

    # Plotea la posición en función del tiempo
    ax_posicion.plot(t, posicion, label='Posición (m)')
    ax_posicion.legend()
    ax_posicion.set_xlabel('Tiempo (s)')
    ax_posicion.set_ylabel('Posición (m)')
    ax_posicion.set_title('Posición respecto al Tiempo')

    # Plotea la velocidad en función del tiempo
    ax_velocidad.plot(t, velocidad, label='Velocidad (m/s)')
    ax_velocidad.legend()
    ax_velocidad.set_xlabel('Tiempo (s)')
    ax_velocidad.set_ylabel('Velocidad (m/s)')
    ax_velocidad.set_title('Velocidad respecto al Tiempo')

    # Plotea la aceleración en función del tiempo
    ax_aceleracion.plot(t, aceleracion, label='Aceleración (m/s^2)')
    ax_aceleracion.legend()
    ax_aceleracion.set_xlabel('Tiempo (s)')
    ax_aceleracion.set_ylabel('Aceleración (m/s^2)')
    ax_aceleracion.set_title('Aceleración respecto al Tiempo')

    # Ajusta el espacio entre los subgráficos
    plt.tight_layout()

    # Actualiza los gráficos
    canvas.draw()


# Configuración de la interfaz gráfica
root = tk.Tk()
root.title('Simulador de Oscilación')

# Etiquetas y campos de entrada
masa_label = ttk.Label(root, text='Masa (kg):')
masa_entry = ttk.Entry(root)
longitud_muelle_label = ttk.Label(root, text='Longitud del muelle (m):')
longitud_muelle_entry = ttk.Entry(root)
angulo_inicial_label = ttk.Label(root, text='Ángulo inicial (grados):')
angulo_inicial_entry = ttk.Entry(root)
gravedad_label = ttk.Label(root, text='Gravedad (m/s^2):')
gravedad_entry = ttk.Entry(root)

# Coloca las etiquetas y campos de entrada en la interfaz
masa_label.grid(row=0, column=0, padx=10, pady=5)
masa_entry.grid(row=0, column=1, padx=10, pady=5)
longitud_muelle_label.grid(row=1, column=0, padx=10, pady=5)
longitud_muelle_entry.grid(row=1, column=1, padx=10, pady=5)
angulo_inicial_label.grid(row=2, column=0, padx=10, pady=5)
angulo_inicial_entry.grid(row=2, column=1, padx=10, pady=5)
gravedad_label.grid(row=3, column=0, padx=10, pady=5)
gravedad_entry.grid(row=3, column=1, padx=10, pady=5)

# Etiquetas y campos de entrada para la velocidad inicial
velocidad_inicial_label = ttk.Label(root, text='Velocidad inicial (m/s):')
velocidad_inicial_entry = ttk.Entry(root)
velocidad_inicial_label.grid(row=4, column=0, padx=10, pady=5)
velocidad_inicial_entry.grid(row=4, column=1, padx=10, pady=5)

# Botón para actualizar el gráfico
update_button = ttk.Button(
    root, text='Actualizar Gráfico', command=update_plot)
update_button.grid(row=5, column=0, columnspan=2, pady=10)

# Etiqueta para mostrar los resultados
resultados_label = ttk.Label(root, text="Resultados:")
resultados_label.grid(row=6, column=0, columnspan=2, pady=10)

# Configuración de los gráficos
fig, (ax_posicion, ax_velocidad, ax_aceleracion) = plt.subplots(
    1, 3, figsize=(12, 4), sharey=True)
canvas = FigureCanvasTkAgg(fig, master=root)
canvas_widget = canvas.get_tk_widget()
canvas_widget.grid(row=7, column=0, columnspan=2)

# Inicia la interfaz gráfica
root.mainloop()








#############INTEGRALES FINAL
import sympy as sp
import numpy as np
import tkinter as tk
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import matplotlib.pyplot as plt
import sympy as sp
import numpy as np
import tkinter as tk
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import matplotlib.pyplot as plt

# Antes de comenzar con el analisis de la parte que considera mas dificil del proyecto creo que la parte mas didactica es las interfaz y el proceso de creaion de ellas
# Creacion de la variable del gráfico y de las dunciones
x = sp.symbols('x')

# Creacion de la funcion que describe las series de Taylor
# Esta es la parte menos interesante esta hecha en clase ademas no añade ninguna novedad que se considere importante ya que es una funcion que siempre funciona


def calcular_aproximacion_taylor(f_x, a_value, n_terms_value):
    taylor_approximation = 0
    for n in range(n_terms_value+1):
        derivada = f_x.diff(x, n)
        # Formula de Taylor
        taylor_term = (derivada.subs(x, a_value) /
                       sp.factorial(n)) * (x - a_value)**n
        taylor_approximation += taylor_term
    return taylor_approximation
# Grafica de ambas funciones


def plot_func_and_approximation(f_x, taylor_approximation, a_value, n_terms_value):
    # Intervalo que se va representar en el eje x
    x_values = np.linspace(a_value-4, a_value + 4, 400)
    # Transformamos mediante la funcion lambdify de la libreria sympy una expresion matematica en expresion numerica
    # Creamos nuevos nombres para simplificar el codigo
    y_original = sp.lambdify(x, f_x, "numpy")(x_values)
    y_taylor = sp.lambdify(x, taylor_approximation, "numpy")(x_values)
# Calculamos las integrales en el intervalo que hemos definido para representar la funcion
    integral_original = sp.integrate(f_x, (x, a_value-4, a_value+4))
    integral_taylor = sp.integrate(
        taylor_approximation, (x, a_value-4, a_value+4))
# Calculamos el porcentaje en valor absoluto
    porcentaje = abs((integral_taylor - integral_original) /
                     integral_original) * 100
    ax.clear()  # Como es una interfaz esta parte es fundamental ya que borra lo anterior y no hace  que hayan varias funciones representadas
# El titulo la parte mas dificil
    title_part1 = f'Función Original vs. Aproximación de Taylor (a={a_value})\n' \
                  f'Integral Original: {integral_original.evalf()}\n' \
                  f'Integral Taylor: {integral_taylor.evalf()}\n' \
                  f'Porcentaje: {porcentaje.evalf()}%\n'

    title_part2 = f'Polinomio de Taylor: {sp.simplify(taylor_approximation)}'

    ax.set_title(title_part1, fontsize=13)
    ax.text(-0.1, 1.02, title_part2, transform=ax.transAxes,
            fontsize=8.5, verticalalignment='top')

    ax.plot(x_values, y_original, label='f(x)')
    ax.plot(x_values, y_taylor, 'r',
            label=f'Aproximación de Taylor ({n_terms_value} términos)')

    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    # ax.legend()
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

fig, ax = plt.subplots(figsize=(8, 6))
canvas = FigureCanvasTkAgg(fig, master=root)
widget_canvas = canvas.get_tk_widget()
widget_canvas.pack(fill=tk.BOTH, expand=1)

root.mainloop()


