#  MOD 15/05/2022
- Multiplos en pagina de productos y carrito de compras [Prestashop 1.7]

- Link de video PARTE 1 : [https://www.youtube.com/watch?v=SspCzjBAvTw](https://www.youtube.com/watch?v=SspCzjBAvTw)
[![Alt text](https://img.youtube.com/vi/SspCzjBAvTw/0.jpg)](https://www.youtube.com/watch?v=SspCzjBAvTw)

- Link de video PARTE 2 : [https://www.youtube.com/watch?v=UdyZ9zkTivs](https://www.youtube.com/watch?v=UdyZ9zkTivs)
[![Alt text](https://img.youtube.com/vi/UdyZ9zkTivs/0.jpg)](https://www.youtube.com/watch?v=UdyZ9zkTivs)

Siga los siguientes pasos de acontinuación:

## Proceso
### 1: Paso
- Ingresar a tu proyecto y buscar el archivo **product-add-to-cart.tpl** en la ruta
  **/themes/classic/template/catalog/_partials/** y agrega la class **quantity_wanted**. en el input con id= **"quantity_wanted"**
  
```
  <input
            type="number"
            name="qty"
            id="quantity_wanted"
            value="{$product.quantity_wanted}"
            class="input-group hidden quantity_wanted"
            readonly="readonly"
            min="{$product.minimal_quantity}"
            aria-label="{l s='Quantity' d='Shop.Theme.Actions'}"
          >
  
   ``` 
- Y despues de editar agrega el siguiente código(artificio).

 ```
   <input type="hidden" id="check_step_min">
 ```
 
### 2: Paso
- Ingresar a tu proyecto y buscar el archivo **custom.js** en la ruta
  **/themes/classic/assets/js/** y agregar el siguiente código
  
  ```
    $(function(){
       multiplyProductLoad();
       multiplyCart();

       prestashop.on('updatedProduct', function(){
           multiplyProduct();
       });

        prestashop.on('updateCart', function(){
            multiplyCart();
            let min_update = $('#quantity_wanted');
            let attr_update = min_update.attr('min');
            min_update.val(attr_update);
        });

     });

     function multiplyProduct(){
         let min_step_now = $("input.quantity_wanted").attr('min');
         let min_step_old = $("input#check_step_min").val();

         if(min_step_now != min_step_old){
             $("input.quantity_wanted").val(min_step_now);
             $("input.quantity_wanted").trigger("touchspin.updatesettings", {step: min_step_now});
             $("input#check_step_min").val(min_step_now);
         }
     }

     function multiplyProductLoad(){
         let min_step = $("input.quantity_wanted").attr('min');
         $("input#check_step_min").val(min_step);
         $("input.quantity_wanted").trigger("touchspin.updatesettings", {step: min_step});

     }

     function multiplyCart(){
         let inputs = $('input[name="product-quantity-spin"]');
         inputs.each(function(){
             let min_step_cart = $(this).attr('min');
             $(this).trigger("touchspin.updatesettings", {step: min_step_cart});
         });
     }
  ```
- Con todo lo anterior (paso 1 y paso 2) completamos la configuración para la ficha de producto y dejamos listo el js para el carrito de compras

### 3: Paso
- Ingresar a tu proyecto y buscar el archivo **CartController.php** en la ruta
  **/controllers/front/** y busca la funcion llamada **function processChangeProductInCart()** y copia todo esa funcion completa.

- Luego crea un override; si ya lo tienes creado agrega la funcion copiada dentro de la clase principal, como se muestra en el video y edita:

- Busca dentro de funcion la parte :

 ```
        $qty_to_check = $this->qty;
        $cart_products = $this->context->cart->getProducts();
 ```

- Justo antes de esta linea agrega lo siguiente:

  ```
      if($mode != 'add'){
            $this->qty = $this->updateQtyMultiply($this->id_product, $this->id_product_attribute);
        }
  ```
- En el mismo override agregaras la siguiente funcion:

 ```
      protected function updateQtyMultiply($id_product, $id_product_attribute){

        $cart_products = $this->context->cart->getProducts();
        $result;
        if (is_array($cart_products)) {
            foreach ($cart_products as $cart_product) {
                if ($this->productInCartMatchesCriteria($cart_product)) {

                    $min        = $cart_product['minimal_quantity'] == 0 ? 1 : $cart_product['minimal_quantity'];
                    $multiply   = $cart_product['cart_quantity'] % $min;
                    $refact_quantity = $multiply != 0 ? $min - $multiply : $min;

                    if($cart_product['id_product_attribute'] == $id_product_attribute && $cart_product['id_product'] == $id_product){
                        $result = $refact_quantity;
                    }else{
                        $result = 1;
                    }
                    
                    break;
                }
            }
        }

        return $result;
       
    }
 ```
  
### 4: Paso
- Ahora en este paso final agrega un attributo a un input en el archivo **cart-detailed-product-line.tpl** en la ruta
  **/themes/classic/template/checkout/_partials/**, busca la class= **"js-cart-line-product-quantity"** y agrega lo siguiente:
  
  ```
     min="{$product.minimal_quantity}"
  ```
  
- Y con eso terminariamos el carrito.

## Contribución
1. Luis Huaymana
2. [Prestademia](https://www.youtube.com/c/prestademia) : [https://www.youtube.com/c/prestademia](https://www.youtube.com/c/prestademia)
