// Sistema-de-inventario-
#include <iostream>
#include <iomanip>
#include <vector>
#include <string>

using namespace std;

class Usuario {
public:
    string nombreUsuario;
    string contrasena;

    Usuario(string usuario, string pass) : nombreUsuario(usuario), contrasena(pass) {}
};

class Producto {
public:
    string nombre;
    int cantidad;
    double precio;

    Producto(string n, int c, double p) : nombre(n), cantidad(c), precio(p) {}
};

class Transaccion {
public:
    string tipo;
    Producto producto;
    int cantidad;

    Transaccion(string t, Producto p, int q) : tipo(t), producto(p), cantidad(q) {}
};

class SistemaComprasVentas {
private:
    vector<Producto> inventario;
    vector<Transaccion> historialTransacciones;
    Usuario usuarioActual;

public:
    SistemaComprasVentas() : usuarioActual("", "") {}

    bool iniciarSesion(string usuario, string contrasena) {
        // Verificar si el usuario y la contraseña coinciden (implementación simplificada)
        return (usuario == "admin" && contrasena == "123");
    }

    void agregarProducto(string nombre, int cantidad, double precio) {
        Producto nuevoProducto(nombre, cantidad, precio);
        inventario.push_back(nuevoProducto);
        cout << "Producto '" << nombre << "' agregado con éxito." << endl;
    }

    void realizarTransaccion(string tipo, string nombreProducto, int cantidad) {
        for (auto &producto : inventario) {
            if (producto.nombre == nombreProducto) {
                if (tipo == "compra") {
                    producto.cantidad += cantidad;
                    cout << cantidad << " unidades de '" << nombreProducto << "' compradas con éxito." << endl;
                } else if (tipo == "venta") {
                    if (producto.cantidad >= cantidad) {
                        producto.cantidad -= cantidad;
                        cout << cantidad << " unidades de '" << nombreProducto << "' vendidas con éxito." << endl;
                    } else {
                        cout << "No hay suficiente cantidad de '" << nombreProducto << "' en inventario." << endl;
                        return;
                    }
                } else {
                    cout << "Tipo de transacción no válido." << endl;
                    return;
                }

                Transaccion nuevaTransaccion(tipo, producto, cantidad);
                historialTransacciones.push_back(nuevaTransaccion);
                return;
            }
        }

        cout << "Producto '" << nombreProducto << "' no encontrado en inventario." << endl;
    }

    void mostrarInventario() {
        if (inventario.empty()) {
            cout << "El inventario está vacío." << endl;
            return;
        }

        cout << "\n------ Inventario ------" << endl;
        cout << setw(20) << "Nombre" << setw(10) << "Cantidad" << setw(10) << "Precio" << endl;
        for (const auto &producto : inventario) {
            cout << setw(20) << producto.nombre << setw(10) << producto.cantidad << setw(10) << producto.precio << endl;
        }
    }

    void mostrarHistorialTransacciones() {
        if (historialTransacciones.empty()) {
            cout << "No hay transacciones registradas." << endl;
            return;
        }

        cout << "\n------ Historial de Transacciones ------" << endl;
        cout << setw(10) << "Tipo" << setw(20) << "Producto" << setw(10) << "Cantidad" << endl;
        for (const auto &transaccion : historialTransacciones) {
            cout << setw(10) << transaccion.tipo << setw(20) << transaccion.producto.nombre << setw(10) << transaccion.cantidad << endl;
        }
    }

    void mostrarSeparador() {
        cout << "------------------------" << endl;
    }

    void mostrarMenu() {
        cout << "\n------ Menú ------" << endl;
        cout << "1. Agregar Producto" << endl;
        cout << "2. Realizar Compra" << endl;
        cout << "3. Realizar Venta" << endl;
        cout << "4. Mostrar Inventario" << endl;
        cout << "5. Mostrar Historial de Transacciones" << endl;
        cout << "6. Salir" << endl;
    }
};

int main() {
    SistemaComprasVentas sistema;

    // Inicio de sesión
    cout << "------ Inicio de Sesión ------" << endl;

    int intentos = 3;  // Número máximo de intentos

    while (intentos > 0) {
        string usuario, contrasena;
        cout << "Nombre de Usuario: ";
        cin >> usuario;
        cout << "Contraseña: ";
        cin >> contrasena;

        if (sistema.iniciarSesion(usuario, contrasena)) {
            sistema.mostrarSeparador();
            cout << "Inicio de sesión exitoso. ¡Bienvenido, " << usuario << "!" << endl;

            while (true) {
                sistema.mostrarSeparador();
                sistema.mostrarMenu();

                int opcion;
                cout << "Ingrese la opción deseada: ";
                cin >> opcion;

                switch (opcion) {
                    case 1: {
                        string nombre;
                        int cantidad;
                        double precio;

                        cout << "\nIngrese los detalles del nuevo producto:" << endl;
                        cout << "Nombre: ";
                        cin >> nombre;
                        cout << "Cantidad inicial: ";
                        cin >> cantidad;
                        cout << "Precio por unidad: ";
                        cin >> precio;

                        sistema.agregarProducto(nombre, cantidad, precio);
                        break;
                    }
                    case 2: {
                        string nombreProducto;
                        int cantidad;

                        cout << "\nIngrese los detalles de la compra:" << endl;
                        cout << "Nombre del producto: ";
                        cin >> nombreProducto;
                        cout << "Cantidad a comprar: ";
                        cin >> cantidad;

                        sistema.realizarTransaccion("compra", nombreProducto, cantidad);
                        break;
                    }
                    case 3: {
                        string nombreProducto;
                        int cantidad;

                        cout << "\nIngrese los detalles de la venta:" << endl;
                        cout << "Nombre del producto: ";
                        cin >> nombreProducto;
                        cout << "Cantidad a vender: ";
                        cin >> cantidad;

                        sistema.realizarTransaccion("venta", nombreProducto, cantidad);
                        break;
                    }
                    case 4:
                        sistema.mostrarInventario();
                        break;
                    case 5:
                        sistema.mostrarHistorialTransacciones();
                        break;
                    case 6:
                        cout << "Cerrando sesión. ¡Hasta luego, " << usuario << "!" << endl;
                        return 0;
                    default:
                        cout << "Opción no válida. Inténtelo de nuevo." << endl;
                }
            }
        } else {
            cout << "Inicio de sesión fallido. Usuario o contraseña incorrectos." << endl;
            intentos--;
            cout << "Intentos restantes: " << intentos << endl;
        }
    }

    cout << "Número máximo de intentos alcanzado. Saliendo del programa." << endl;
    return 1;  // Código de error
}
