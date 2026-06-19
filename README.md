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

