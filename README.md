import locale
import hashlib

locale.setlocale(locale.LC_ALL, '')

class Usuario:
    def __init__(self, nombre, contrasena):
        # Constructor de la clase Usuario
        self.nombre = nombre
        # Encriptamos la contraseña antes de almacenarla
        self.contrasena_hash = self._encriptar_contrasena(contrasena)
        # Inicializamos el registro financiero y el superávit
        self.registro_financiero = {}
        self.superavit = 0

    def _encriptar_contrasena(self, contrasena):
        # Método para encriptar la contraseña utilizando hash SHA-256
        return hashlib.sha256(contrasena.encode()).hexdigest()

    def agregar_registro_financiero(self, categoria, gasto):
        # Método para agregar un gasto al registro financiero del usuario
        if gasto <= 0:
            print("El monto del gasto debe ser un número positivo.")
            return
        if categoria in self.registro_financiero:
            self.registro_financiero[categoria] += gasto
        else:
            self.registro_financiero[categoria] = gasto

    def calcular_balance(self, ingresos):
        # Método para calcular el balance entre ingresos y gastos
        total_gastos = sum(self.registro_financiero.values())
        balance = ingresos - total_gastos
        return balance

    def calcular_porcentaje_gasto(self, ingresos):
        # Método para calcular el porcentaje de gastos respecto a los ingresos
        if ingresos == 0:
            return 0
        total_gastos = sum(self.registro_financiero.values())
        porcentaje_gasto = (total_gastos / ingresos) * 100
        return porcentaje_gasto

def ingresar_ingresos():
    # Función para ingresar los ingresos mensuales del usuario
    while True:
        ingresos_str = input("Ingrese sus ingresos mensuales: ")
        try:
            ingresos = locale.atof(ingresos_str)
            return ingresos
        except ValueError:
            print("Por favor, ingrese un número válido.")

def ingresar_registro_financiero(usuario):
    # Función para ingresar los gastos del usuario
    ingresos = ingresar_ingresos()
    print("\nPor favor, proporcione los gastos:")
    while True:
        categoria = input("Introduzca el nombre de la categoría de gastos (escriba 'fin' para terminar): ")
        if categoria.lower() == 'fin':
            break
        while True:
            try:
                gasto = locale.atof(input(f"Ingrese el monto de gasto en '{categoria}': "))
                if gasto <= 0:
                    print("El monto del gasto debe ser un número positivo.")
                    continue
                break
            except ValueError:
                print("Por favor, ingrese un número válido.")
        usuario.agregar_registro_financiero(categoria, gasto)

    balance = usuario.calcular_balance(ingresos)
    porcentaje_gasto = usuario.calcular_porcentaje_gasto(ingresos)
    print("\nResumen financiero:")
    print(f"Ingresos: {locale.currency(ingresos, grouping=True)}")
    for categoria, gasto in usuario.registro_financiero.items():
        print(f"{categoria.capitalize()}: {locale.currency(gasto, grouping=True)}")
    print(f"\nBalance mensual: {locale.currency(balance, grouping=True)}")

    if balance < 0:
        print("¡Has gastado más de lo que has ganado este mes!")
    elif balance > 0:
        print("¡Has gastado menos de lo que has ganado este mes!")
        usuario.superavit += balance
        print(f"Felicidades, te ha quedado un superávit de {locale.currency(balance, grouping=True)} este mes.")
    else:
        print("Has gastado exactamente lo que has ganado este mes.")

    print(f"Porcentaje de gasto sobre ingresos: {porcentaje_gasto:.2f}%")

def main():
    print("¡Bienvenido a FinanciApp!")
    usuarios = {}
    while True:
        opcion = input("Por favor, seleccione una opción:\n1. Iniciar sesión\n2. Registrarse\n3. Salir\nOpción: ")
        if opcion == '1':
            nombre = input("Nombre completo: ")
            contrasena = input("Contraseña: ")
            contrasena_hash = hashlib.sha256(contrasena.encode()).hexdigest()
            if nombre in usuarios and usuarios[nombre].contrasena_hash == contrasena_hash:
                usuario = usuarios[nombre]
                print("\n¡Inicio de sesión exitoso! Bienvenido de nuevo a FinanciApp.")
                ingresar_registro_financiero(usuario)
            else:
                print("\nInicio de sesión fallido. Verifique sus datos e intente nuevamente o registre una nueva cuenta.\n")
        elif opcion == '2':
            nombre = input("Nombre completo: ")
            contrasena = input("Ingrese una contraseña para FinanciApp: ")
            usuarios[nombre] = Usuario(nombre, contrasena)
            print("\n¡Registro exitoso! Ahora puedes iniciar sesión.")
        elif opcion == '3':
            print("¡Gracias por utilizar FinanciApp! Hasta luego.")
            break
        else:
            print("Opción no válida. Por favor, seleccione una opción válida.")

if __name__ == "__main__":
    main()


