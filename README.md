
# Diferencias entre `mutableStateOf` y `mutableStateListOf`

## 1. Diferencias básicas

### `mutableStateOf`
- Envuelve un valor inmutable (como una lista) en un estado observable.
- Necesitas reasignar toda la lista para detectar cambios.
- Uso recomendado para trabajar con listas inmutables.

### `mutableStateListOf`
- Es una lista observable que detecta automáticamente los cambios internos.
- Puedes mutar la lista directamente (agregar, eliminar, modificar elementos).
- Uso recomendado para trabajar con listas mutables.

### Comparación

| Aspecto                      | **`mutableStateOf`**                    | **`mutableStateListOf`**              |
|-------------------------------|------------------------------------------|---------------------------------------|
| **Reactividad**               | Necesita reasignar la lista completa.    | Detecta cambios internos automáticamente. |
| **Uso principal**             | Para datos inmutables.                  | Para listas mutables con cambios frecuentes. |

---

# Manejo del estado en ViewModel

## 1. Usando `mutableStateOf`

```kotlin
class ShoppingListViewModel : ViewModel() {
    var products by mutableStateOf(getFakeProducts())
        private set

    fun addProduct(productName: String) {
        if (products.any { it.name.equals(productName, ignoreCase = true) }) return
        products = listOf(Product(productName, false)) + products
    }

    fun removeProduct(index: Int) {
        products = products.toMutableList().apply { removeAt(index) }
    }

    fun toggleProductChecked(index: Int) {
        products = products.toMutableList().apply {
            val product = this[index]
            this[index] = product.copy(isChecked = !product.isChecked)
        }
    }
}
```

## 2. Usando `mutableStateListOf`

```kotlin
class ShoppingListViewModel : ViewModel() {
    val products = mutableStateListOf(*getFakeProducts().toTypedArray())

    fun addProduct(productName: String) {
        if (products.any { it.name.equals(productName, ignoreCase = true) }) return
        products.add(0, Product(productName, false))
    }

    fun removeProduct(index: Int) {
        products.removeAt(index)
    }

    fun toggleProductChecked(index: Int) {
        val product = products[index]
        products[index] = product.copy(isChecked = !product.isChecked)
    }
}
```

---

# Comparación final

| Aspecto                      | **`mutableStateOf`**                    | **`mutableStateListOf`**              |
|-------------------------------|------------------------------------------|---------------------------------------|
| **Gestión del estado**         | Reasignas la lista completa cada vez.   | Mutas directamente la lista.         |
| **Reactividad**                | Detecta cambios solo al reasignar.      | Detecta cambios internos automáticamente. |
| **Eficiencia**                 | Menos eficiente si hay cambios frecuentes. | Más eficiente para cambios frecuentes. |
| **Uso recomendado**            | Cuando prefieres inmutabilidad.         | Cuando necesitas trabajar con listas mutables. |

---
