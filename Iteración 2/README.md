Segunda Iteración QueMePongo
===

[Enunciado](https://docs.google.com/document/d/10j6XB9zIhl5xox2xBEDEFsgPmueHMkyvLSHcLxl_27Y/edit)

> *Como usuarie de QuéMePongo, quiero especificar qué trama tiene la tela de una prenda (lisa, rayada, con lunares, a cuadros o un estampado).*

La trama puede ser modelada como enum, ya que no hay ninguna restricción sobre las tramas y su relación con los otros componentes del dominio
```java
public enum Trama {
  LISA,
  RAYADA,
  A_CUADROS,
  ESTAMPADO
}
```

> *Como usuarie de QuéMePongo, quiero guardar un borrador de la última prenda que empecé a cargar para continuar después.*

Para este requerimiento, podemos cargar una nueva clase que sea BorradorPrenda. Su objetivo va a ser ocuparse de la **creación** de una prenda
```java
public class BorradorPrenda {
  // ...
}
```

> *Como usuarie de QuéMePongo, quiero crear una prenda especificando primero de qué tipo es.*

> *Como usuarie de QuéMePongo, quiero poder no indicar ninguna trama para una tela, y que por defecto ésta sea lisa.*

Respecto a los requerimientos pedidos, podemos solucionar 'cargar primero el tipo de prenda' cargándolo directamente en el constructor para que el TipoPrenda sea obligatorio, y sea lo primero que se carga.

Respecto al requerimiento 'tener por defecto la trama lisa', también podemos setear en el constructor la trama como Lisa para que cada vez que se pida la misma desde un getter, el valor que tenga sea Lisa.
```java
public class BorradorPrenda {
  public BorradorPrenda(TipoPrenda tipoPrenda) {
    this.tipoPrenda = Objects.requireNonNull(tipoPrenda, "El tipo de prenda es obligatorio");
    this.trama = Trama.LISA;
  }
```

> *Como usuarie de QuéMePongo, quiero crear una prenda especificando en segundo lugar los aspectos relacionados a su material (colores, material, trama, etc) para evitar elegir materiales inconsistentes con el tipo de prenda.*

Para esto, podemos crear setters en nuestra clase BorradorPrenda, que vaya cargando cada parte de la prenda en el momento que se necesite.

```java
public class BorradorPrenda {
  // ...anterior
  BorradorPrenda setTipoPrenda(TipoPrenda tipoPrenda) {
    this.tipoPrenda = tipoPrenda;
    return this;
  }

  BorradorPrenda setMaterial(Material material) {
    this.material = material;
    return this;
  }

  BorradorPrenda setTrama(Trama trama) {
    this.trama = trama == null ? Trama.LISA : trama;
    return this;
  }

  BorradorPrenda setColorPrimario(Color color) {
    this.colorPrimario = color;
    return this;
  }
}
```

Además podríamos anidar los setters devolviendo *this*.

```java
BorradorPrenda intentoDeZapatilla = new BorradorPrenda(TipoPrenda.Zapato);
intentoDeZapatilla.setMaterial(...).setTrama(...).setColorPrimario(...)
```


> *Como usuarie de QuéMePongo, quiero poder guardar una prenda 
solamente si esta es válida.*

Esto lo podemos solucionar con un método en el BorradorPrenda, que se ocupe de instanciar la prenda cuando sea necesario.
```java
public class BorradorPrenda {
    //...anterior
    public Prenda crearPrenda() {
        this.validarPrenda();
        return new Prenda(
            this.getTipoPrenda(),
            this.getMaterial(),
            this.getColorPrincipal(),
            this.getColorSecundario());
    }
}
```
