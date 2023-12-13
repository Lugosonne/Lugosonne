import sympy as sp
import numpy as np
import tkinter as tk
from tkinter import messagebox
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import matplotlib.pyplot as plt

# Creación de la variable del gráfico y de las funciones
x = sp.symbols('x')

# Función que calcula la aproximación de Taylor


def calcular_aproximacion_taylor(f_x, a_value, n_terms_value):
    # Verificar si la derivada de primer orden es nula
    if f_x.diff(x, 1) == 0:
        return None

    taylor_approximation = 0
    for n in range(n_terms_value + 1):
        derivada = f_x.diff(x, n)
        taylor_term = (derivada.subs(x, a_value) /
                       sp.factorial(n)) * (x - a_value)**n
        taylor_approximation += taylor_term

    return taylor_approximation

# Función que grafica ambas funciones


def plot_func_and_approximation(f_x, taylor_approximation, a_value, n_terms_value):
    x_values = np.linspace(a_value - 4, a_value + 4, 600)
    y_original = sp.lambdify(x, f_x)(x_values)
    y_taylor = sp.lambdify(x, taylor_approximation)(x_values)

    integral_original = sp.integrate(f_x, (x, a_value - 4, a_value + 4))
    integral_taylor = sp.integrate(
        taylor_approximation, (x, a_value - 4, a_value + 4))

    if integral_original == 0:
        porcentaje = "El valor de la integral de f(x) es cero, por lo tanto, no se puede calcular el porcentaje."
    else:
        porcentaje = abs(
            (integral_taylor - integral_original) / integral_original) * 100

    ax.clear()
    title_part1 = f'Función Original vs. Aproximación de Taylor (a={a_value})\n' \
                  f'Integral Original: {integral_original.evalf()}\n' \
                  f'Integral Taylor: {integral_taylor.evalf()}\n' \
                  f'Porcentaje: {porcentaje}\n'

    title_part2 = f'Polinomio de Taylor: {sp.N(sp.simplify(taylor_approximation), 5)}'

    ax.set_title(title_part1, fontsize=12)
    ax.text(-0.1, 01.01, title_part2, transform=ax.transAxes, fontsize=10)

    ax.plot(x_values, y_original, label='f(x)')
    ax.plot(x_values, y_taylor, 'r',
            label=f'Aproximación de Taylor ({n_terms_value} grado)')

    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.legend()
    ax.grid(True)
    canvas.draw()

# Base de la interfaz


def calcular_y_graficar():
    # Obtener los términos ingresados en la interfaz
    a_value = float(entry_a.get())
    n_terms_value = int(entry_n_terms.get())
    f_x = sp.sympify(entry_func.get())

    # Calcular ambas funciones con los valores obtenidos
    taylor_approximation = calcular_aproximacion_taylor(
        f_x, a_value, n_terms_value)

    if taylor_approximation is not None:
        plot_func_and_approximation(
            f_x, taylor_approximation, a_value, n_terms_value)
    else:
        messagebox.showerror(
            "Error", "La derivada de primer orden es cero. No se puede calcular la aproximación de Taylor.")


# Crear la interfaz con tkinter
root = tk.Tk()
root.title('Aproximación de Taylor')

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
entry_n_terms.insert(0, "1")
entry_n_terms.pack()

btn_calcular = tk.Button(
    root, text="Calcular Aproximación de Taylor", command=calcular_y_graficar)
btn_calcular.pack()

# Configuración de la gráfica con matplotlib
fig, ax = plt.subplots()
canvas = FigureCanvasTkAgg(fig, master=root)
widget_canvas = canvas.get_tk_widget()
widget_canvas.pack(fill=tk.BOTH, expand="True")

# Iniciar la interfaz
root.mainloop()


fig, ax = plt.subplots()
canvas = FigureCanvasTkAgg(fig, master=root)
widget_canvas = canvas.get_tk_widget()
widget_canvas.pack(fill=tk.BOTH, expand="True")

root.mainloop()

