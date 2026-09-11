# Analisis de un sistema para monitoreo de tanque


Integrantes:

Axel Iván Breña Torres (Estudiante A)

Axel Iván Breña Torres (x2) (Estudiante B)





# 1. Descripción 

El problema nos habla de una planta con varios tanques para almacenar liquidos, los tanques tiene
una capacidad limite que no puede superarse y un nivel cambia si el tanque se va llenando o vaciando 

El sistema también tiene la posibilidad de poder pararse a mitad de cualquier proceso de llenado o vaciado 

Necesitamos que el sistema pueda saber de cada tanque su identificador, su capacidad, 
cuánto tiene ahora mismo y qué acción está haciendo

Cada tanque también tiene un sensor que comprueba el nivel, el sistema debe de poder consultar toda esa
información para poder empezar a llenarlo o vaciarlo, detenerlo, ver su porcentaje de nivel, y pedirle
al sensor que haga una lectura


---

## 2. Identificación de objetos

Considero que el tanque es el elemento principal ya que cuenta con un identificador, una capacidad, un nivel y un estado que cambia a lo largo
del tiempo, por lo tanto debe de ser un objeto

necesitamos mantener su estado y no tendría caso separar esos datos, el trabajo es no dejar que el nivel se salga de los límites permitidos ,el sensor lo
considero también como un objeto aparte pero está muy relacionado con el tanque

la idea fue que en un sistema automatizado real el sensor debería ser un componente distinto con su propio tag y su propio rango de operación, y puede dar
lecturas fuera de rango, si usamos esa lógica la clase terminaría haciendo cosas que no le corresponden

---

## 3. Estado y comportamiento

| Objeto | Responsabilidad                                                                 | Información que debe guardar                                                            | Comportamientos                                                                                                                                                                               |
|---|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tanque** | Mantener su nivel dentro de los límites             | *Tag<br/>*Capacidad<br/>*Nivel<br/>*Acción actual                                       | *consulta la información<br/>*Permite llenarse sin pasar la capacidad máxima<br/> *Permitir vaciarse sin bajar de cero <br/>*Permitir detenerse<br/>*Permite calcular su porcentaje de liquido |
| **SensorNivel** | Obtener una lectura del tanque al que está asociado | *Su tag <br/>*el tanque asociado<br/>*el rango válido<br/> *la última lectura| Permitir realizar una lectura<br/>* Permitir consultar la última lectura<br/>* Permitir saber si esa lectura esta en rango                                                                    |

---

## 4. Relaciones entre los objetos

El sensor necesita saber a qué tanque se asoció porque tiene que acceder al tanque para saber cual su nivel en ese momento

Separé esta acción ya que el sensor es el unico que consulta al tanque, pero el tanque no necesita saber si tiene un sensor consultandolo
ya que el tanque funciona sin que
que nada lo esté midiendo

Elegí está estructura para no complicar al tanque con algo que no le toca, y para que las dos clases no dependan una de la otra al mismo tiempo






### Clase tanque

| Atributo | Tipo de dato | Visibilidad |
|---|--------------|---|
| id | String       | private |
| capacidadMaxima | double       | private |
| nivelActual | double       | private |
| estado | String       | private |

el constructo reecibe id y capacidadMaxima, el nivel arranca en 0 y el estado en "DETENIDO"
para que ningún tanque empiece con datos raros.

Métodos propuestos:
- + getId() - String
- + getCapacidadMaxima() - double
- + getNivelActual() - double
- + getEstado() - EstadoTanque
- + llenar(double cantidad) - sube el nivel sin pasar el limite max y deja el estado en LLENANDO
- + vaciar(double cantidad) - baja el nivel sin llegar a menos de cero, deja el estado en VACIANDO
- + detener() - deja el estado en DETENIDO, no afecta al nivel
- + calcularPorcentaje() - double(nivelActual / capacidadMaxima) * 100
- + mostrarInformacion() - imprime las Var. del tanque

Responsabilidad - cuidar su propio nivel y sus propios estado.

### Clase `SensorNivel`

| Atributo       | Tipo de dato | Visibilidad |
|----------------|---|---|
| id             | String | private |
| tanqueAsociado | Tanque | private |
| valorMinV      | double | private |
| valorMaxV      | double | private |
| ultimaLectura  | double | private |

Constructor: recibe id del tanque asociado y el rango válido

Métodos propuestos:
- + getId() : String
- + realizarLectura() : consulta tanque y su nivel la guarda como ultimaLectura y la regresa.
- + getUltimaLectura() : double
- + esLecturaValida() : checa si ultimaLectura está entre valorMinimoValido y valorMaximoValido.

Responsabilidad: tomar y validar lecturas del tanque asociado.

###  Auxiliar EstadoTanque (string)


DETENIDO, LLENANDO, VACIANDO



### Privado o público (encapsulacion)

Todos los atributos de las dos clases son private
ninguno se manipula desde main(), los cambios de nivel y estado en Tanque pasan siempre por llenar(), vaciar() y detener(),
que tienen validaciones, lo pasa mismo con ultimaLectura en SensorNivel, que solo cambia con realizarLectura(). 

Los métodos get...()sí son públicos porque otras clases necesitan consultar esos datos.

---

## 6. Diagrama UML 



---

## 7. Justificación del diseño

1. ¿Por qué propusieron esas clases? son las dos partes del problema con estado y comportamiento propios, lo demás está a nivel de software
2. ¿Cuál es la responsabilidad principal de cada clase? Tanque cuida su nivel y su estado y SensorNivel toma y valida lecturas.


3. ¿Por qué determinados atributos fueron definidos como privados? Porque si los dejamos público, cualquiera podría meterle un valor inválido al nivel sin pasar por las validaciones.


4. ¿Qué información decidieron proporcionar mediante los constructores? En Tanque solo id y capacidadMaxima, porque el nivel y el estado siempre deben arrancar igual, en cero y detenido y en SensorNivel, el id, el tanque y el rango válido, porque sin eso no puede hacer sus operaciones correctamente


5. ¿Qué objetos se relacionan entre sí y por qué? SensorNivel con Tanque porque necesita su nivel para poder leer.


6. ¿Qué decisiones tomaron para evitar duplicar responsabilidades? Que el nivel real solo esté dispoiible desde el tanque y que el sensor nada más guarde el resultado de su propia lectura.



