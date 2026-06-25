# Sistema-de-Gestion-Bibliotecario-digital
SGB Integrantes : Giuliana Cristaldo , Lucas Cristaldo, Luis Cristaldo

class RegistroMeta(type):
"""Metaclase que registra automaticamente todas las clases del sistema."""
   _registro_clases = {}

   def __new__(mcs, name , bases, attrs):
       cls = super().__new__(mcs, name, bases, attrs)
       if name != "EntidadBase"
          mcs._registro_clases[name] = cls
       return cls

       @classmethod
       def obtener_clases_registradas(mcs):
           return list(mcs._registro_clases.keys())

2.DECORADOR
def log_operacion(func):
"""Decorador para registrar el inicio y fin de operaciones CRUD."""
@functools.wraps(func)
def wrapper(*args, **kwargs):
    timestamp_inicio = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    print(f"\n[LOG - {timestamp_inicio}] iniciando operacion: ´{func.__name__}´")
    resultado = func(*args, **kwargs)
    timestamp_fin = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    print(f"[LOG - {timestamp_fin}] operacion ´{func.__name_}´ finalizada con exito.")
return wrapper

3.Herencia y composicion.
class EntidadBase(metaclass=RegistroMeta):
    """clase base abstracta (conceptual) para la gestion de IDs unicos."""
    _contador_id = 1

    def __init__(self):
        self.id = EntidadBase._contador_id
        EntidadBase._contador_id +=1

    def mostrar_info(self) - str:
        raise notimplementedError("subclasses deben implementar mostrar_info()")

class historialLibro:
    """clase componente para la relacion de composicion con libro."""
    def __init__(self):
        self.eventos = []

    def registrar_evento(self, evento: str):
        self.eventos.append(f"{datatime.now().strftime(´%Y-%m-%d %H:%M´)}: {evento}")

    def obtener_historial(self):
        return self.eventos if self.eventos else ["sin movimientos."]

class Elementocatalogo(EntidadBase):
    """clase intermedia en la jerarquia para elementos prestables."""
    def __init__(self, titulo: str, autor: str, isbn: str, anio_publicacion: int):
        super().__init__()
        self.titulo = titulo
        self.autor = autor
        self.isbn = isbn
        self.anio_publicacion = anio_publicacion
        self.prestado = false 

    def tipo(self) - str:
        return "Elemento Generico"

class libro(elementocatalogo):
    """clase libro. aplica composicion con historialLibro."""
    def __init__(self, titulo: str, autor: str, isbn: str, anio_publicacion: int, cant_paginas: int):
        super().__init__(titulo, autor, isbn, anio_publicacion)
        self.cant_paginas = cant_paginas
        self._historial = historialLibro()
        self._historial.registrar_evento("libro dado de alta en el sistema.")

    def tipo(self) - str:
        return "libro"

    def registrar_movimiento(self, mensaje: str):
        self._historial.registrar_evento(mensaje)

    def ver_historial(self):
        return self._historial.obtener_historial()

    # POLIMORFISMO
    def mostrar_info(self) - str:
        estado = "prestado" if self.prestado else "Disponible"
        return f"[{self.tipo()} ID {self.id}] ´{self.titulo}´ por {self.autor} ({self.anio_publicacion}) - {self.cant_paginas} pags. status: {estado}"

class Usuario(EntidadBase):
    """clase que representa a los usuarios del sistema."""
    def __init__(self, nombre: str, apellido: str, dni: str, email: str):
    super().__init__()
    self.nombre = nombre
    self.apellido = apellido
    self.dni = dni
    self.email = email

    def mostrar_info(self) - str:
        return f"[usuario ID {self.id}] {self.apellido}, {self.nombre} (DNI: { self.dni}) - Email: {self.email}"

4.Relacion de agregacion
class prestamo(EntidadBase):
    """Clase prestamo. Aplica agregacion (recibe instancias de usuario y Elementocatalogo)."""
    def __init__(self, usuario: usuario, elemento: elementocatalogo):
    super().__init__()
    self.usuario = usuario
    self.elemento = elemento
    self.fecha_prestamo = datetime.now()
    self.fecha_devolucion = None
    self.activo = true

    self.elemento.prestado = true
    if isinstance(self.elemento, Libro):
        self.elemento.registrar_movimiento(f"prestado a {usuario.nombre} {usuario.apellido}")

    def finalizar_devolucion(self):
        self.fecha_devolucion = datetime.now()
        self.activo = false
        self.elemento.prestado = false
        if isnstance(self.elemento, Libro):
            self.elemento.registrar_movimiento("devuelto a la biblioteca.")

    def mostrar_info(self) - str:
        f_prestamo = self.fecha_prestamo.strftime("%Y-%m-%d %H:%M")
        f_dev = self.fecha_devolucion.strftime("%Y-%m-%d %H:%M") if self.fecha_devolucion else "Pendiente"
        return f"[prestamo ID {self.id}] ´{self.elemento.titulo}´ prestado a {self.usuario.nombre} {self.usuario.apellido}. fecha: {f_prestamo} / devolucion: {f_dev}"

 5. patron de diseño singleton
class Biblioteca:
    """clase controladora del sistema bajo el patron singleton."""
     _instancia=none

     def __new__(cls,*args,**krawgs):
         if not cls._instancia:
            cls._instancia = super().__new__(cls, *args, ***kwargs)
            cls._instancia.catalogo = {}
            cls._instancia.usuarios = {}
            cls._instancia.prestamos = {}
          return cls._instancia

    @log_operacion
    def alta_elemento(self, elemento: Elementocatalogo):
        self.catalogo[elemento.id] = elemento
        print(f"Elemento ´{elemento.titulo}´ añadido al catalogo.")

    @log_operacion
    def modificar_elemento(self, id_elemento: int, **kwargs):
        if id_elemento in self.catalogo:
            elem = self.catalogo[id_elemento]
            for clave, valor in kwargs.items():
                if hasattr(elem, clave):
                    setattr(elem, clave, valor)
            if isnstance(elem, Libro):
                elem.registrar_movimiento("Datos modificados en el sistema.")
            print(f"Elemento ID {id_elemento} modificado.")
        else:
            print("Error: Elemento no encontrado.")

    @log_operacion
    def baja_elemento(self, id_elemento: int):
        if id_elemento in self.catalogo:
            elem = self.catalogo.pop(id_elemento)
            print(f"Elemento ´{elem.titulo}´ eliminado del sistema.")
        else:
            print("Error: Elemento no encontrado.")

    def listar_elementos(self):
        print("/n--- catalogo de la biblioteca ---")
        if not self.catalogo:
            print("El catalogo esta vacio.")
        for elem in self.catalogo.values():
            print(elem.mostrar_info())

    @log_operacion
    def alta_usuario(self, usuario: usuario):
        self.usuarios[usuario.id] = usuario
        print(f"usuario ´{usuario.nombre} {usuario.apellido}´ dado de alta.")

    @log_operacion
    def modificar_usuario(self, id_usuario: int, **kwargs):
        if id_usuario in self.usuarios:
            user = self.usuarios[id_usuario]
            for clave, valor in kwargs.items():
                if hasattr(user, clave):
                    setattr(user, clave, valor)
            print(f"usuario ID {id_usuario} modificado.")
        else:
            print(f"Error: usuario no encontrado.")

    @log_operacion
    def baja_usuario(self, id_usuario: int):
        if id_usuario in self.usuarios:
            user = self.usuarios.pop(id_usuario)
            print(f"usuario ´{user.nombre} {user.apellido} ´eliminado.")
        else:
            print("Error: usuario no encontrado.")

    def listar_usuarios(self):
    
    

