
#include <iostream>

using namespace std;

struct Producto {
    int id;
    string nombre;
    int cantidad;
    double precio_compra_unitario;
    double precio_venta_unitario;
    double ganancia_unidad;
};

class PROJEC_J {
private:
    vector<Producto> productos;
    int next_id;
    string archivo_datos;

public:
    PROJEC_J() : next_id(1), archivo_datos("inventario_projec_j.dat") {
        cargarDatos();
    }

    ~PROJEC_J() {
        guardarDatos();
    }

    void mostrarLogo() {
        cout << "=============================================\n";
        cout << "           PROJEC J - Sistema de Gestion     \n";
        cout << "        Inventario y Finanzas Inteligente    \n";
        cout << "=============================================\n";
        cout << endl;
    }

    void mostrarStockBot() {
        cout << "=============================================\n";
        cout << "        StockBot - Asistente PROJEC J       \n";
        cout << "=============================================\n";
        cout << "Funciones:\n";
        cout << "- Gestionar inventario inteligente\n";
        cout << "- Calcular ganancias automaticamente\n";
        cout << "- Convertir monedas al instante\n";
        cout << "- Hacer crecer tu negocio\n";
        cout << "=============================================\n";
        cout << endl;
    }

    void guardarDatos() {
        ofstream archivo(archivo_datos);
        if (!archivo.is_open()) {
            cout << "Error al guardar los datos.\n";
            return;
        }

        archivo << next_id << "\n";
        for (const auto& p : productos) {
            archivo << p.id << "|" << p.nombre << "|" << p.cantidad << "|"
                    << p.precio_compra_unitario << "|" << p.precio_venta_unitario
                    << "|" << p.ganancia_unidad << "\n";
        }
        archivo.close();
    }

    void cargarDatos() {
        ifstream archivo(archivo_datos);
        if (!archivo.is_open()) {
            cout << "Inicializando PROJEC J con datos de ejemplo...\n";
            agregarProducto("Pilfrut", 50, 1.20, 2.00);
            agregarProducto("Galletas", 30, 3.50, 5.00);
            agregarProducto("Aceite", 20, 15.00, 22.00);
            agregarProducto("Arroz", 25, 8.00, 12.00);
            agregarProducto("Leche", 35, 4.50, 7.00);
            return;
        }

        string linea;
        getline(archivo, linea);
        next_id = stoi(linea);

        productos.clear();
        while (getline(archivo, linea)) {
            stringstream ss(linea);
            string campo;
            vector<string> campos;

            while (getline(ss, campo, '|'))
                campos.push_back(campo);

            if (campos.size() == 6) {
                Producto p;
                p.id = stoi(campos[0]);
                p.nombre = campos[1];
                p.cantidad = stoi(campos[2]);
                p.precio_compra_unitario = stod(campos[3]);
                p.precio_venta_unitario = stod(campos[4]);
                p.ganancia_unidad = stod(campos[5]);
                productos.push_back(p);
            }
        }
        archivo.close();
        cout << "PROJEC J cargado: " << productos.size() << " productos listos.\n";
    }

    void agregarProducto(string nombre, int cantidad, double compra, double venta) {
        double ganancia = venta - compra;
        Producto nuevo = { next_id++, nombre, cantidad, compra, venta, ganancia };
        productos.push_back(nuevo);
        guardarDatos();
        cout << "Producto '" << nombre << "' agregado (ID: " << (next_id-1) << ")\n";
    }

    void quitarProducto() {
        if (productos.empty()) {
            cout << "No hay productos en el inventario.\n";
            return;
        }

        int id;
        cout << "ID del producto a quitar: ";
        cin >> id;

        auto it = find_if(productos.begin(), productos.end(),
            [id](const Producto& p) { return p.id == id; });

        if (it == productos.end()) {
            cout << "Producto no encontrado.\n";
            return;
        }

        cout << "Producto encontrado: " << it->nombre << " (Stock: " << it->cantidad << ")\n";
        cout << "¿Está seguro de que desea eliminar este producto? (s/n): ";
        
        char confirmacion;
        cin >> confirmacion;
        
        if (confirmacion == 's' || confirmacion == 'S') {
            string nombre_eliminado = it->nombre;
            productos.erase(it);
            guardarDatos();
            cout << "Producto '" << nombre_eliminado << "' eliminado correctamente.\n";
        } else {
            cout << "Eliminacion cancelada.\n";
        }
    }

    void mostrarInventario() {
        if (productos.empty()) {
            cout << "El inventario está vacío.\n";
            return;
        }

        cout << "\n";
        cout << "===========================================================\n";
        cout << "                   INVENTARIO - PROJEC J                  \n";
        cout << "-----------------------------------------------------------\n";
        cout << "ID   PRODUCTO               STOCK  COMPRA/u  VENTA/u  GANANCIA/u\n";
        cout << "-----------------------------------------------------------\n";

        double valor_total = 0;
        double ganancia_total = 0;

        for (const auto& p : productos) {
            cout << left << setw(4) << p.id << " "
                 << setw(20) << p.nombre.substr(0, 19) << " "
                 << setw(5) << p.cantidad << " "
                 << setw(8) << fixed << setprecision(2) << p.precio_compra_unitario << " "
                 << setw(8) << p.precio_venta_unitario << " "
                 << setw(11) << p.ganancia_unidad << endl;

            valor_total += p.cantidad * p.precio_compra_unitario;
            ganancia_total += p.cantidad * p.ganancia_unidad;
        }

        cout << "-----------------------------------------------------------\n";
        cout << "VALOR TOTAL DEL INVENTARIO: Bs " << valor_total << "\n";
        cout << "GANANCIA POTENCIAL TOTAL:   Bs " << ganancia_total << "\n";
        cout << "===========================================================\n";
    }

    void convertirMoneda(double cantidad_bs = 0) {
        if (cantidad_bs == 0) {
            cout << "\n";
            cout << "=============================================\n";
            cout << "              CONVERSOR DE MONEDAS          \n";
            cout << "=============================================\n";
            cout << "Ingrese cantidad en Bolivianos (Bs): ";
            cin >> cantidad_bs;
        }

        string nombre_moneda;
        double tipo_cambio;

        cout << "Moneda extranjera: ";
        cin.ignore();
        getline(cin, nombre_moneda);

        cout << "Valor de 1 " << nombre_moneda << " en Bs: ";
        cin >> tipo_cambio;

        if (tipo_cambio <= 0) {
            cout << "Tipo de cambio no válido.\n";
            return;
        }

        double equivalente = cantidad_bs / tipo_cambio;

        cout << fixed << setprecision(2);
        cout << "\n";
        cout << "=============================================\n";
        cout << "              CONVERSION EXITOSA            \n";
        cout << "---------------------------------------------\n";
        cout << cantidad_bs << " Bs = " << equivalente << " " << nombre_moneda << "\n";
        cout << "Tipo: 1 " << nombre_moneda << " = " << tipo_cambio << " Bs\n";
        cout << "=============================================\n";
    }

    void venderProducto() {
        if (productos.empty()) {
            cout << "No hay productos en el inventario.\n";
            return;
        }

        vector<pair<int, int>> items_vendidos;
        double total_venta = 0;
        double total_ganancia = 0;

        cout << "\n";
        cout << "=============================================\n";
        cout << "                PUNTO DE VENTA              \n";
        cout << "=============================================\n";
        
        cout << "\nProductos disponibles:\n";
        for (const auto& p : productos) {
            if (p.cantidad > 0) {
                cout << "ID " << p.id << ": " << p.nombre << " - Bs "
                     << p.precio_venta_unitario << " (Stock: " << p.cantidad << ")\n";
            }
        }

        while (true) {
            int id, cantidad;
            cout << "\nIngrese ID del producto (0 para finalizar): ";
            cin >> id;
            if (id == 0) break;

            auto it = find_if(productos.begin(), productos.end(),
                [id](const Producto& p) { return p.id == id; });

            if (it == productos.end()) {
                cout << "ID inválido.\n";
                continue;
            }

            if (it->cantidad <= 0) {
                cout << "Producto agotado.\n";
                continue;
            }

            cout << "Cantidad a vender: ";
            cin >> cantidad;

            if (cantidad <= 0) {
                cout << "Cantidad no válida.\n";
                continue;
            }

            if (cantidad > it->cantidad) {
                cout << "Stock insuficiente. Solo hay " << it->cantidad << " unidades.\n";
                continue;
            }

            double subtotal = cantidad * it->precio_venta_unitario;
            double ganancia = cantidad * it->ganancia_unidad;
            
            it->cantidad -= cantidad;
            total_venta += subtotal;
            total_ganancia += ganancia;
            items_vendidos.push_back({id, cantidad});

            cout << cantidad << " x " << it->nombre
                 << " = Bs " << subtotal << " (Ganancia: Bs " << ganancia << ")\n";
        }

        if (total_venta == 0) {
            cout << "No se realizó ninguna venta.\n";
            return;
        }

        cout << "\n";
        cout << "=============================================\n";
        cout << "              RESUMEN DE VENTA              \n";
        cout << "---------------------------------------------\n";
        
        for (const auto& item : items_vendidos) {
            auto producto = find_if(productos.begin(), productos.end(),
                [&](const Producto& p) { return p.id == item.first; });
            cout << item.second << " x " << producto->nombre << "\n";
        }
        
        cout << "---------------------------------------------\n";
        cout << "TOTAL A PAGAR: Bs " << fixed << setprecision(2) << total_venta << "\n";
        cout << "GANANCIA TOTAL: Bs " << total_ganancia << "\n";
        cout << "=============================================\n";

        double pago;
        cout << "\nIngrese el dinero recibido (Bs): ";
        cin >> pago;

        if (pago < total_venta) {
            cout << "Dinero insuficiente. Faltan Bs " << (total_venta - pago) << "\n";
            for (const auto& item : items_vendidos) {
                auto producto = find_if(productos.begin(), productos.end(),
                    [&](const Producto& p) { return p.id == item.first; });
                producto->cantidad += item.second;
            }
            cout << "Venta cancelada. Stock restaurado.\n";
            return;
        }

        double cambio = pago - total_venta;
        cout << "Cambio a devolver: Bs " << cambio << "\n";

        char opcion_conversion;
        cout << "\n¿Desea convertir el cambio a otra moneda? (s/n): ";
        cin >> opcion_conversion;

        if (opcion_conversion == 's' || opcion_conversion == 'S') {
            convertirMoneda(cambio);
        }

        guardarDatos();
        cout << "¡Venta completada exitosamente!\n";
    }

    void reponerStock() {
        int id, cantidad;
        cout << "ID del producto a reponer: ";
        cin >> id;

        auto it = find_if(productos.begin(), productos.end(),
            [id](const Producto& p) { return p.id == id; });

        if (it == productos.end()) {
            cout << "Producto no encontrado.\n";
            return;
        }

        cout << "Producto: " << it->nombre << " - Stock actual: " << it->cantidad << "\n";
        cout << "Cantidad a agregar: ";
        cin >> cantidad;

        if (cantidad <= 0) {
            cout << "Cantidad no válida.\n";
            return;
        }

        it->cantidad += cantidad;
        guardarDatos();
        cout << "Stock actualizado: " << it->nombre << " = " << it->cantidad << " unidades\n";
    }

    void buscarProducto() {
        string nombre;
        cout << "Nombre del producto a buscar: ";
        cin.ignore();
        getline(cin, nombre);

        cout << "\n";
        cout << "=============================================\n";
        cout << "           RESULTADOS DE BUSQUEDA           \n";
        cout << "=============================================\n";
        
        bool encontrado = false;

        for (const auto& p : productos) {
            if (p.nombre.find(nombre) != string::npos) {
                cout << "ID " << p.id << ": " << p.nombre 
                     << " | Stock: " << p.cantidad 
                     << " | Precio: Bs " << p.precio_venta_unitario 
                     << " | Ganancia: Bs " << p.ganancia_unidad << endl;
                encontrado = true;
            }
        }

        if (!encontrado) {
            cout << "No se encontraron productos\n";
        }
        cout << "=============================================\n";
    }
};

void mostrarMenu() {
    cout << "\n";
    cout << "=============================================\n";
    cout << "           MENU PRINCIPAL - PROJEC J        \n";
    cout << "=============================================\n";
    cout << "1. Mostrar inventario completo\n";
    cout << "2. Vender productos\n";
    cout << "3. Agregar nuevo producto\n";
    cout << "4. Reponer stock\n";
    cout << "5. Buscar producto\n";
    cout << "6. Convertir moneda\n";
    cout << "7. Quitar producto\n";
    cout << "8. Salir\n";
    cout << "=============================================\n";
    cout << "Seleccione una opcion: ";
}

int main() {
    PROJEC_J sistema;
    int opcion;

    sistema.mostrarLogo();
    sistema.mostrarStockBot();

    cout << "Presiona Enter para comenzar...";
    cin.ignore();
    cin.get();

    do {
        system("cls || clear");
        sistema.mostrarLogo();
        sistema.mostrarStockBot();
        mostrarMenu();
        cin >> opcion;

        switch (opcion) {
        case 1: 
            system("cls || clear");
            sistema.mostrarLogo();
            sistema.mostrarInventario(); 
            break;
        case 2: 
            system("cls || clear");
            sistema.mostrarLogo();
            sistema.venderProducto(); 
            break;
        case 3: {
            system("cls || clear");
            sistema.mostrarLogo();
            string nombre;
            int cantidad;
            double compra, venta;
            cout << "\n";
            cout << "=============================================\n";
            cout << "           AGREGAR NUEVO PRODUCTO           \n";
            cout << "=============================================\n";
            cout << "Nombre del producto: ";
            cin.ignore();
            getline(cin, nombre);
            cout << "Cantidad inicial: "; 
            cin >> cantidad;
            cout << "Precio de compra (Bs): "; 
            cin >> compra;
            cout << "Precio de venta (Bs): "; 
            cin >> venta;
            sistema.agregarProducto(nombre, cantidad, compra, venta);
            break;
        }
        case 4:
            system("cls || clear");
            sistema.mostrarLogo();
            sistema.reponerStock();
            break;
        case 5:
            system("cls || clear");
            sistema.mostrarLogo();
            sistema.buscarProducto();
            break;
        case 6:
            system("cls || clear");
            sistema.mostrarLogo();
            sistema.convertirMoneda();
            break;
        case 7:
            system("cls || clear");
            sistema.mostrarLogo();
            sistema.quitarProducto();
            break;
        case 8:
            cout << "\n";
            cout << "=============================================\n";
            cout << "              HASTA PRONTO                  \n";
            cout << "=============================================\n";
            cout << "Gracias por usar PROJEC J!\n";
            cout << "Vuelve pronto para seguir gestionando tu negocio!\n";
            cout << "=============================================\n";
            break;
        default: 
            cout << "Opcion no válida. Elija entre 1-8.\n"; 
            break;
        }

        if (opcion != 8) {
            cout << "\nPresiona Enter para continuar...";
            cin.ignore();
            cin.get();
        }

    } while (opcion != 8);

    return 0;
}
