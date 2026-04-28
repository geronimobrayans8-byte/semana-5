# semana-5
import sympy as sp
import math

def biseccion(f, a, b, tol):
    if f(a) * f(b) > 0:
        print(f"\n[Error Bisección] f({a})={f(a):.5f} y f({b})={f(b):.5f} tienen el mismo signo. El método no puede iniciar.")
        return None, None, None

    iteraciones = 0
    c = a
    while True:
        c = (a + b) / 2
        error = abs(f(c))
        iteraciones += 1
        
        if error < tol:
            break
            
        if f(a) * f(c) < 0:
            b = c
        else:
            a = c
            
    return c, iteraciones, error

def newton_raphson(f, df, x0, tol, max_iter=100):
    iteraciones = 0
    x = x0
    while iteraciones < max_iter:
        fx = f(x)
        error = abs(fx)
        
        if error < tol:
            return x, iteraciones, error
            
        dfx = df(x)
        if dfx == 0:
            print("\n[Error Newton] La derivada se hizo cero. El método falla.")
            return None, None, None
            
        x = x - fx / dfx
        iteraciones += 1
        
    print("\n[Aviso] Newton-Raphson alcanzó el máximo de iteraciones.")
    return x, iteraciones, abs(f(x))

def secante(f, x0, x1, tol, max_iter=100):
    iteraciones = 0
    while iteraciones < max_iter:
        fx0 = f(x0)
        fx1 = f(x1)
        error = abs(fx1)
        
        if error < tol:
            return x1, iteraciones, error
            
        if fx1 - fx0 == 0:
            print("\n[Error Secante] División por cero detectada.")
            return None, None, None
            
        x_next = x1 - fx1 * ((x1 - x0) / (fx1 - fx0))
        x0 = x1
        x1 = x_next
        iteraciones += 1
        
    print("\n[Aviso] El método de la Secante alcanzó el máximo de iteraciones.")
    return x1, iteraciones, abs(f(x1))

def main():
    print("=== RESOLUCIÓN DE ECUACIONES NO LINEALES ===")
    print("1. Bisección")
    print("2. Newton-Raphson")
    print("3. Secante")
    print("4. Comparativo (Ejecutar los tres)")
    
    opcion = input("Seleccione una opción (1-4): ")
    
    x_sym = sp.Symbol('x')
    func_str = input("\nIngrese la función f(x) [Ejemplo: x*cos(x) - 1]: ")
    
    try:
        f_expr = sp.sympify(func_str)
        f = sp.lambdify(x_sym, f_expr, 'math')
        df_expr = sp.diff(f_expr, x_sym)
        df = sp.lambdify(x_sym, df_expr, 'math')
    except Exception as e:
        print("\nError al interpretar la función. Asegúrese de escribirla en formato Python válido.")
        return

    tol_str = input("Ingrese el error / tolerancia (Ejemplo: 1e-6): ")
    tol = float(tol_str)

    if opcion in ['1', '4']:
        print("\n--- Método de Bisección ---")
        a = float(input("Ingrese el límite inferior (a): "))
        b = float(input("Ingrese el límite superior (b): "))
        raiz, iters, err = biseccion(f, a, b, tol)
        if raiz is not None:
            print(f"Raíz obtenida: {raiz:.8f}")
            print(f"Iteraciones necesarias: {iters}")
            print(f"Error final |f(c)|: {err:.2e}")

    if opcion in ['2', '4']:
        print("\n--- Método de Newton-Raphson ---")
        x0 = float(input("Ingrese el punto semilla inicial (x0): "))
        raiz, iters, err = newton_raphson(f, df, x0, tol)
        if raiz is not None:
            print(f"Raíz obtenida: {raiz:.8f}")
            print(f"Iteraciones necesarias: {iters}")
            print(f"Error final |f(x)|: {err:.2e}")

    if opcion in ['3', '4']:
        print("\n--- Método de la Secante ---")
        x0 = float(input("Ingrese el primer punto semilla (x0): "))
        x1 = float(input("Ingrese el segundo punto semilla (x1): "))
        raiz, iters, err = secante(f, x0, x1, tol)
        if raiz is not None:
            print(f"Raíz obtenida: {raiz:.8f}")
            print(f"Iteraciones necesarias: {iters}")
            print(f"Error final |f(x)|: {err:.2e}")

if __name__ == "__main__":
    main()
