# UML completo

`````mermaid
classDiagram
direction LR

%% 1. Clases del modelo
namespace Modelo {
    class Persona:::modelo {
        <<abstract>>
        -int id
        -String nombre
        -String dni
        -String telefono
        +getId() int
        +getNombre() String
        +mostrarRol() String*
    }

    class Cliente:::modelo {
        -String correo
        -String direccion
        +mostrarRol() String
        +realizarPedido() Pedido
    }

    class Empleado:::modelo {
        -String cargo
        -String turno
        -double sueldo
        +mostrarRol() String
        +registrarPedido(Pedido pedido) void
    }

    class Administrador:::modelo {
        -String usuario
        -String clave
        +mostrarRol() String
        +generarReporte(String tipo) Reporte
        +gestionarInventario() void
    }

    class Producto:::modelo {
        <<abstract>>
        -String codigo
        -String nombre
        -double precio
        -int tiempoPreparacion
        +getCodigo() String
        +getPrecio() double
        +calcularPrecio() double*
        +getDescripcion() String*
    }

    class Pastel:::modelo {
        -String sabor
        -String tamano
        -int porciones
        +calcularPrecio() double
        +getDescripcion() String
    }

    class Bebida:::modelo {
        -int volumenMl
        -boolean esFria
        +calcularPrecio() double
        +getDescripcion() String
    }

    class Insumo:::modelo {
        -String codigo
        -String nombre
        -int stock
        -int stockMinimo
        -String unidadMedida
        -double costoUnitario
        +getCodigo() String
        +descontar(int cantidad) boolean
        +reponer(int cantidad) void
        +hayStock(int cantidad) boolean
        +esCritico() boolean
    }

    class Inventario:::modelo {
        -ArbolInventario arbol
        +registrarInsumo(Insumo insumo) void
        +buscarInsumo(String codigo) Insumo
        +descontarStock(String codigo, int cantidad) boolean
        +listarCriticos() ListaEnlazada~Insumo~
    }

    class Prioridad:::modelo {
        <<enumeration>>
        ALTA
        MEDIA
        BAJA
        +getNivel() int
    }

    class Pedido:::modelo {
        -int id
        -LocalDateTime fecha
        -String estado
        -double total
        -Prioridad prioridad
        -ListaEnlazada~DetallePedido~ detalles
        +getPrioridad() Prioridad
        +generarComanda() Comanda
        +getComanda() Comanda
        +agregarDetalle(DetallePedido detalle) void
        +tieneDetalles() boolean
        +calcularTotal() double
        +cambiarEstado(String estado) void
    }

    class DetallePedido:::modelo {
        -int cantidad
        -double subtotal
        +calcularSubtotal() double
    }

    class Comanda:::modelo {
        -int id
        -LocalDateTime horaIngreso
        -String estado
        +iniciarPreparacion() void
        +marcarLista() void
    }

    class Reporte:::modelo {
        -String tipo
        -LocalDate fecha
        +generar() String
        +productosMasVendidos() ListaEnlazada~Producto~
        +consumoInsumos() ListaEnlazada~Insumo~
    }

    class HistorialAccion:::modelo {
        -String descripcion
        -String tipoAccion
        -LocalDateTime fecha
        -Pedido pedidoAfectado
        +deshacer() void
    }
}

%% 2. Estructuras de datos y sus nodos
namespace EstructurasDatos {
    class ListaEnlazada~T~ {
        -NodoLista~T~ cabeza
        -int tamano
        +insertarInicio(T dato) void
        +insertarFinal(T dato) void
        +eliminar(T dato) boolean
        +buscar(T dato) T
        +estaVacia() boolean
        +size() int
    }

    class NodoLista~T~ {
        -T dato
        -NodoLista~T~ siguiente
        +NodoLista(T dato)
        +getDato() T
        +getSiguiente() NodoLista~T~
        +setSiguiente(NodoLista~T~ nodo) void
    }

    class ColaPedidos:::estructuras {
        -NodoCola frente
        -NodoCola fin
        -int tamano
        +encolar(Pedido pedido) void
        +desencolar() Pedido
        +verFrente() Pedido
        +estaVacia() boolean
    }

    class NodoCola:::estructuras {
        -Pedido dato
        -NodoCola siguiente
        +NodoCola(Pedido dato)
        +getDato() Pedido
        +getSiguiente() NodoCola
        +setSiguiente(NodoCola nodo) void
    }

    class PilaHistorial:::estructuras {
        -NodoPila tope
        -int tamano
        +push(HistorialAccion accion) void
        +pop() HistorialAccion
        +peek() HistorialAccion
        +estaVacia() boolean
    }

    class NodoPila:::estructuras {
        -HistorialAccion dato
        -NodoPila siguiente
        +NodoPila(HistorialAccion dato)
        +getDato() HistorialAccion
        +getSiguiente() NodoPila
        +setSiguiente(NodoPila nodo) void
    }

    class ArbolProductos:::estructuras {
        -NodoArbol~Producto~ raiz
        +insertar(Producto producto) void
        +buscar(String codigo) Producto
        +eliminar(String codigo) void
        +recorridoInorden() ListaEnlazada~Producto~
    }

    class ArbolInventario:::estructuras {
        -NodoArbol~Insumo~ raiz
        +insertar(Insumo insumo) void
        +buscar(String codigo) Insumo
        +eliminar(String codigo) boolean
        +recorridoInorden() ListaEnlazada~Insumo~
    }

    class NodoArbol~T~ {
        -T dato
        -NodoArbol~T~ izq
        -NodoArbol~T~ der
    }
}

%% 3. Controladores MVC
namespace ControladoresMVC {
    class PedidoController:::controladores {
        -ColaPedidos colaPedidos
        -PilaHistorial historial
        +crearPedido(Cliente cliente, Prioridad prioridad) Pedido
        +registrarPedido(Pedido pedido) boolean
        +verSiguientePedido() Pedido
        +iniciarPreparacion(Comanda comanda) void
        +marcarListo(Comanda comanda) void
        +deshacerUltimaAccion() boolean
    }

    class InventarioController:::controladores {
        -Inventario inventario
        +registrarInsumo(Insumo insumo) void
        +buscarInsumo(String codigo) Insumo
        +descontarStock(String codigo, int cantidad) boolean
        +listarCriticos() ListaEnlazada~Insumo~
    }

    class ProductoController:::controladores {
        -ArbolProductos catalogo
        +registrarProducto(Producto producto) void
        +buscarProducto(String codigo) Producto
        +eliminarProducto(String codigo) void
        +listarProductos() ListaEnlazada~Producto~
    }

    class ReporteController:::controladores {
        -GeneradorReportes generador
        +generarReporte(String tipo) String
    }

    class LoginController:::controladores {
        -Administrador administrador
        -boolean sesionActiva
        +iniciarSesion(String usuario, char[] clave) boolean
        +cerrarSesion() void
    }

    class ClienteController:::controladores {
        -ListaEnlazada~Cliente~ clientes
        +registrarCliente(Cliente cliente) void
        +listarClientes() ListaEnlazada~Cliente~
    }
}

%% 4. Vistas Swing
namespace VistasSwing {
    class LoginView:::vistas {
        -JTextField txtUsuario
        -JPasswordField txtClave
        -JButton btnIngresar
        +mostrar() void
    }

    class MenuPrincipal:::vistas {
        -JMenuBar barraMenu
        +mostrar() void
        +abrirVista(String opcion) void
    }

    class PedidoView:::vistas {
        -JTable tablaDetalles
        -JButton btnRegistrar
        +mostrar() void
        +mostrarTotal(double total) void
    }

    class InventarioView:::vistas {
        -JTable tablaInsumos
        -JButton btnBuscar
        +mostrar() void
        +mostrarCriticos(ListaEnlazada~Insumo~ insumos) void
    }

    class ProductoView:::vistas {
        -JTable tablaProductos
        -JButton btnGuardar
        +mostrar() void
        +mostrarProductos(ListaEnlazada~Producto~ productos) void
    }

    class ReporteView:::vistas {
        -JComboBox~String~ cmbTipo
        -JTextArea txtReporte
        +mostrar() void
        +mostrarReporte(String contenido) void
    }

    class CocinaView:::vistas {
        -JLabel lblSiguientePedido
        -JButton btnMarcarListo
        +mostrar() void
        +mostrarPedido(Pedido pedido) void
    }

    class ClienteView:::vistas {
        -JTextField txtNombre
        -JTextField txtCorreo
        -JTable tablaClientes
        +mostrar() void
        +mostrarClientes(ListaEnlazada~Cliente~ clientes) void
    }
}

%% 5. Utilidades y punto de entrada
class Utilidades:::utilidades {
    +esTextoValido(String texto) boolean$
    +esCantidadValida(int cantidad) boolean$
    +formatearMoneda(double monto) String$
}

class GeneradorReportes:::utilidades {
    -Administrador administrador
    +generar(String tipo) String
}

class Main:::utilidades {
    +main(String[] args) void$
}
`````

**Parte 2: relaciones, colores y cierre**

`````text
%% Herencia del modelo
Persona <|-- Cliente
Persona <|-- Empleado
Empleado <|-- Administrador
Producto <|-- Pastel
Producto <|-- Bebida

%% Relaciones del negocio
Cliente "1" --> "0..*" Pedido : realiza
Empleado "0..1" --> "0..*" Pedido : registra
Pedido "1" *-- "0..*" DetallePedido : contiene
DetallePedido "0..*" --> "1" Producto : referencia
Pedido --> "1" Prioridad : tiene
Pedido "1" *-- "0..1" Comanda : genera al registrarse
Producto --> "0..*" Insumo : usa en receta
Inventario o-- "0..*" Insumo : administra
HistorialAccion "0..*" --> "0..1" Pedido : afecta
Administrador ..> Reporte : genera
Reporte ..> Pedido : consulta
Reporte ..> Inventario : consulta

%% Estructuras y almacenamiento
Pedido *-- ListaEnlazada : guarda detalles
Inventario *-- ArbolInventario : guarda insumos por código
ListaEnlazada *-- NodoLista : contiene nodos
ColaPedidos *-- NodoCola : contiene nodos
ColaPedidos ..> Pedido : ordena por prioridad
PilaHistorial *-- NodoPila : contiene nodos
PilaHistorial ..> HistorialAccion : apila acciones
ArbolProductos *-- NodoArbol : contiene nodos
ArbolProductos ..> Producto : indexa por código
ArbolInventario *-- NodoArbol : contiene nodos
ArbolInventario ..> Insumo : ordena por código

%% Inicio y navegación
Main ..> LoginView : inicia Swing
LoginView ..> MenuPrincipal : abre al ingresar
MenuPrincipal ..> PedidoView : abre
MenuPrincipal ..> InventarioView : abre
MenuPrincipal ..> ProductoView : abre
MenuPrincipal ..> ReporteView : abre
MenuPrincipal ..> CocinaView : abre
MenuPrincipal ..> ClienteView : abre

%% Vistas hacia controladores
LoginView --> LoginController : autentica
PedidoView --> PedidoController : registra pedidos
CocinaView --> PedidoController : actualiza preparación
InventarioView --> InventarioController : consulta stock
ProductoView --> ProductoController : gestiona catálogo
ReporteView --> ReporteController : solicita reporte
ClienteView --> ClienteController : registra clientes

%% Controladores hacia el modelo y las estructuras
PedidoController --> ColaPedidos : organiza pedidos
PedidoController --> PilaHistorial : consulta historial
PedidoController ..> Comanda : actualiza estado
InventarioController --> Inventario : gestiona stock
ProductoController --> ArbolProductos : gestiona catálogo
ReporteController --> GeneradorReportes : delega
LoginController --> Administrador : verifica credenciales
ClienteController --> ListaEnlazada : guarda clientes

%% Servicios auxiliares
GeneradorReportes --> Administrador : solicita reporte
GeneradorReportes ..> Reporte : obtiene contenido
PedidoView ..> Utilidades : formatea total
InventarioView ..> Utilidades : valida cantidad

%% Colores por sección
classDef modelo fill:#EAF2FF,stroke:#426699,color:#16304D
classDef estructuras fill:#EDF7EB,stroke:#4D754A,color:#263D25
classDef controladores fill:#F4ECFB,stroke:#775492,color:#362748
classDef vistas fill:#FFF1E1,stroke:#976338,color:#492F1A
classDef utilidades fill:#EDF2F6,stroke:#63788C,color:#243543
style ListaEnlazada fill:#EDF7EB,stroke:#4D754A,color:#263D25
style NodoLista fill:#EDF7EB,stroke:#4D754A,color:#263D25
style NodoArbol fill:#EDF7EB,stroke:#4D754A,color:#263D25
````
````

**Qué cambié respecto al de tu compañero**
- Quité `TablaHashInventario`, la línea `indicePorCodigo` de `Inventario`, las dos relaciones con la tabla y el comentario que la mencionaba.
- Ajusté el texto de la relación `Inventario *-- ArbolInventario` para que diga "guarda insumos por código".
- Todo lo demás quedó igual.

**Al terminar**
1. Revisa que el archivo tenga unas **390 líneas**. Si tiene menos, el pegado se cortó y hay que repetirlo.
2. Abre la pestaña **Preview**.
3. Si sale un error de sintaxis, quita las líneas `namespace ... {` con sus llaves de cierre `}` y los `:::modelo`, `:::vistas`, etc. Se pierden los colores y los recuadros por sección, pero el diagrama funciona igual. Si te pasa, mándame la captura del error y te devuelvo la versión simplificada lista para pegar.
