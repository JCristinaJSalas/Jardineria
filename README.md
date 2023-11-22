# 1.4.5 Consultas multitabla (Composición interna)

Resuelva todas las consultas utilizando la sintaxis de `SQL1` y `SQL2`. Las consultas con sintaxis de `SQL2` se deben resolver con `INNER JOIN` y `NATURAL JOIN`.

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.

   ```SQL
   select c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as rep_ventas from cliente c
   join empleado e on c.codigo_empleado_rep_ventas = e.codigo_empleado;
   ```

2. Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.

   ```SQL
   select c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as rep_ventas, p.id_transaccion from cliente c
   join empleado e on c.codigo_empleado_rep_ventas = e.codigo_empleado
   join pago p on c.codigo_cliente = p.codigo_cliente
   where id_transaccion is not null
   GROUP by c.nombre_cliente;
   ```

3. Muestra el nombre de los clientes que no hayan realizado pagos junto con el nombre de sus representantes de ventas.

   ```SQL
    select c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as rep_ventas, p.id_transaccion from cliente c
    join empleado e on c.codigo_empleado_rep_ventas = e.codigo_empleado
    join pago p on c.codigo_cliente = p.codigo_cliente
    where id_transaccion is null
    group by c.nombre_cliente;
   ```

4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```SQL
   select c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as rep_ventas, o.ciudad from cliente c
   join empleado e on c.codigo_empleado_rep_ventas = e.codigo_empleado
   join pago p on c.codigo_cliente = p.codigo_cliente
   join oficina o on e.codigo_oficina = o.codigo_oficina
   where id_transaccion is not null
   group by c.nombre_cliente;
   ```

5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```SQL
   select c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as rep_ventas, o.ciudad from cliente c
   join empleado e on c.codigo_empleado_rep_ventas = e.codigo_empleado
   join pago p on c.codigo_cliente = p.codigo_cliente
   join oficina o on e.codigo_oficina = o.codigo_oficina
   where id_transaccion is null
   group by c.codigo_cliente;
   ```

6. Lista la dirección de las oficinas que tengan clientes en `Fuenlabrada`.

   ```SQL
   SELECT DISTINCT IF(oficina.linea_direccion2 = '', oficina.linea_direccion1,
   CONCAT(oficina.linea_direccion1,' - ',oficina.linea_direccion2)) AS direccion_oficina
   FROM cliente
   JOIN empleado ON cliente.codigo_empleado_rep_ventas = empleado.codigo_empleado
   JOIN oficina ON empleado.codigo_oficina = oficina.codigo_oficina
   WHERE cliente.ciudad = 'Fuenlabrada';
   ```

7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```SQL
   select c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as rep_ventas, o.ciudad from cliente c
   join empleado e on c.codigo_empleado_rep_ventas = e.codigo_empleado
   join pago p on c.codigo_cliente = p.codigo_cliente
   join oficina o on e.codigo_oficina = o.codigo_oficina
   group by c.codigo_cliente;
   ```

8. Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.

   ```SQL
   Select empleado.nombre, ejefe.nombre as jefe from empleado join empleado ejefe on empleado.codigo_jefe = ejefe.codigo_empleado;
   ```

9. Devuelve un listado que muestre el nombre de cada empleado, el nombre de su jefe y el nombre del jefe de sus jefe.

   ```SQL
    Select empleado.nombre, ejefe.nombre as jefe, sjefe.nombre as super_jefe from empleado
    join empleado ejefe on empleado.codigo_jefe = ejefe.codigo_empleado
    join empleado sjefe on ejefe.codigo_jefe = sjefe.codigo_empleado;
   ```

10. Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.

    ```SQL
    SELECT nombre_cliente, pedido.estado  FROM cliente
    JOIN pedido ON cliente.codigo_cliente = pedido.codigo_cliente
    WHERE fecha_entrega > fecha_esperada OR fecha_entrega IS NULL;
    ```

11. Devuelve un listado de las diferentes gamas de producto que ha comprado cada cliente.

    ```SQL
    SELECT cliente.codigo_cliente, cliente.nombre_cliente, GROUP_CONCAT(DISTINCT pro.gama SEPARATOR ', ') AS gamas_producto
    FROM cliente
    JOIN pedido pe ON pe.codigo_cliente = cliente.codigo_cliente
    JOIN detalle_pedido dp ON dp.codigo_pedido = pe.codigo_pedido
    JOIN pago pag ON pag.codigo_cliente = cliente.codigo_cliente
    JOIN producto pro ON dp.codigo_producto = pro.codigo_producto
    GROUP BY cliente.codigo_cliente
    ORDER BY cliente.codigo_cliente;
    ```

### 1.4.6 Consultas multitabla (Composición externa)

Resuelva todas las consultas utilizando las cláusulas LEFT JOIN, RIGHT JOIN, NATURAL LEFT JOIN y NATURAL RIGHT D0IN

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

   ```SQL
   SELECT DISTINCT cliente.nombre_cliente FROM cliente
   LEFT JOIN pago ON cliente.codigo_cliente = pago.codigo_cliente
   WHERE pago.codigo_cliente IS NULL;
   ```

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pedido.

```SQL
   select distinct cliente.codigo_cliente as id, cliente.nombre_cliente as Cliente from pedido
   right join cliente on pedido.codigo_cliente = cliente.codigo_cliente
   WHERE pedido.codigo_cliente IS NULL;
```

3. Devuelve un listado que muestre los clientes que no han realizado ningún pago y los que no han realizado ningún pedido.

```SQL
select distinct cliente.codigo_cliente as id, cliente.nombre_cliente as Cliente from pedido
right join cliente on pedido.codigo_cliente = cliente.codigo_cliente
LEFT JOIN pago ON cliente.codigo_cliente = pago.codigo_cliente
WHERE pago.codigo_cliente IS NULL AND pedido.codigo_cliente IS NULL;
```

4. Devuelve un listado que muestre solamente los empleados que no tienen una oficina asociada.

```SQL
select * from empleado where codigo_oficina is null;
```

5. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado.

```SQL

SELECT empleado.nombre FROM cliente
RIGHT JOIN empleado
ON cliente.codigo_empleado_rep_ventas = empleado.codigo_empleado
WHERE cliente.nombre_cliente IS NULL;

```

6. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado junto con los datos de la oficina donde trabajan.

```SQL
    SELECT empleado.nombre, oficina.* FROM cliente
    RIGHT JOIN empleado
    ON cliente.codigo_empleado_rep_ventas = empleado.codigo_empleado
    inner JOIN oficina
    ON empleado.codigo_oficina = oficina.codigo_oficina
    WHERE cliente.nombre_cliente IS NULL;
```

7. Devuelve un listado que muestre los empleados que no tienen una oficina asociada y los que no tienen un cliente asociado

```SQL
SELECT cliente.nombre_cliente as Cliente, empleado.nombre, oficina.* FROM cliente
RIGHT JOIN empleado
ON cliente.codigo_empleado_rep_ventas = empleado.codigo_empleado
inner JOIN oficina
ON empleado.codigo_oficina = oficina.codigo_oficina
WHERE empleado.codigo_oficina IS NULL;

```

8. Devuelve un listado de los productos que nunca han aparecido en un pedido.

```SQL
SELECT p.*, dp.codigo_producto FROM producto p
LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
WHERE dp.codigo_producto IS NULL;

```

9.  Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar el nombre, la descripción y la imagen del producto

```SQL
SELECT p.nombre, p.descripcion, gp.imagen FROM producto p
LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
JOIN gama_producto gp ON p.gama = gp.gama
WHERE dp.codigo_producto IS NULL;
```

10. Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.

```SQL
SELECT DISTINCT o.*
FROM oficina o
WHERE o.codigo_oficina NOT IN (
    SELECT DISTINCT e.codigo_oficina
    FROM empleado e
    JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
    JOIN detalle_pedido dp ON pe.codigo_pedido = dp.codigo_pedido
    JOIN producto p ON dp.codigo_producto = p.codigo_producto
    WHERE p.gama = 'Frutales'
);


```

11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

```SQL

SELECT DISTINCT c.nombre_cliente, p.codigo_cliente, pg.codigo_cliente FROM cliente c
INNER JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
LEFT JOIN  pago pg ON c.codigo_cliente = pg.codigo_cliente
WHERE p.codigo_cliente IS NOT NULL AND pg.codigo_cliente IS NULL;

```

12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.

```SQL

SELECT e.nombre as empleado, c.nombre_cliente, em.nombre as jefe from empleado e
left JOIN cliente c on e.codigo_empleado = c.codigo_empleado_rep_ventas
JOIN empleado em ON e.codigo_jefe = em.codigo_empleado
WHERE nombre_cliente IS NULL;


```

1. Devuelve un listado con el código de oficina y la ciudad donde hay oficinas.

   ```SQL
   SELECT codigo_oficina, ciudad FROM oficina where ciudad is not null;
   ```

2. Devuelve un listado con la ciudad y el teléfono de las oficinas de España.

   ```SQL
   SELECT ciudad, telefono FROM oficina WHERE pais = 'España';
   ```

3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un código de jefe igual a 7.

   ```SQL
   Select nombre, CONCAT(apellido1, " ", apellido2) as apellido, email FROM empleado
   WHERE codigo_jefe = 7;
   ```

4. Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la empresa.

   ```SQL
   SELECT nombre, CONCAT(apellido1, " ", apellido2) as apellidos, email, puesto FROM empleado WHERE codigo_jefe IS NULL;
   ```

5. Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas.

   ```SQL
   SELECT nombre, CONCAT(apellido1, " ", apellido2) as apellidos, email, puesto FROM empleado
   WHERE puesto != 'Representante Ventas';
   ```

6. Devuelve un listado con el nombre de los todos los clientes españoles.

   ```SQL
   SELECT DISTINCT nombre_cliente FROM cliente WHERE pais = 'Spain';
   ```

7. Devuelve un listado con los distintos estados por los que puede pasar un pedido.

   ```SQL
   SELECT DISTINCT estado FROM pedido;
   ```

8. Devuelve un listado con el código de cliente de aquellos clientes que realizaron algún pago en 2008. Tenga en cuenta que deberá eliminar aquellos códigos de cliente que aparezcan repetidos. Resuelva la consulta:

   - Utilizando la función `Year` de MySQL.

   ```SQL
   SELECT DISTINCT codigo_cliente FROM pago WHERE YEAR(fecha_pago) = 2008;
   ```

   - Utilizando la función `DATE FORMAT` de MySQL.

   ```SQL
   SELECT DISTINCT codigo_cliente FROM pago WHERE DATE_FORMAT(fecha_pago, '%Y') = 2008;
   ```

   - Sin utilizar ninguna de las funciones anteriores.

   ```SQL
   SELECT DISTINCT codigo_cliente FROM pago
   WHERE fecha_pago BETWEEN '2008-01-01' AND '2008-12-31';
   ```

9. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a tiempo.

   ```SQL
   SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
   WHERE fecha_entrega > fecha_esperada OR fecha_entrega IS NULL
   ORDER BY fecha_entrega IS NULL;
   ```

10. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al menos dos días antes de la fecha esperada.

    - Utilizando la función `ADDDATE` de MySQL.

    ```SQL
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
    WHERE fecha_esperada >= ADDDATE(fecha_entrega, INTERVAL 2 DAY);
    ```

    - Utilizando la función `DATEDIFF` de MySQL.

    ```SQL
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
    WHERE DATEDIFF(fecha_esperada, fecha_entrega) >= 2;
    ```

    - ¿Sería posible resolver esta consulta utilizando el operador de suma `+` o resta `-`?

    ```SQL
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
    WHERE fecha_entrega <= (fecha_esperada - INTERVAL 2 DAY);
    ```

11. Devuelve un listado de todos los pedidos que fueron **rechazados** en `2009`.

    ```SQL
    SELECT * FROM pedido WHERE YEAR(fecha_pedido) = 2009 AND estado = 'Rechazado';
    ```

12. Devuelve un listado de todos los pedidos que han sido **entregados** en el mes de enero de cualquier año.

    ```SQL
    SELECT * FROM pedido WHERE MONTH(fecha_entrega) = 01 AND estado = 'Entregado';
    ```

13. Devuelve un listado con todos los pagos que se realizaron en el año `2008` mediante `Paypal`. Ordene el resultado de mayor a menor.

    ```SQL
    SELECT * FROM pago WHERE forma_pago = 'PayPal' AND YEAR(fecha_pago) = 2008
    ORDER BY total DESC;
    ```

14. Devuelve un listado con todas las formas de pago que aparecen en la tabla `pago` . Tenga en cuenta que no deben aparecer formas de pago repetidas.

    ```SQL
    SELECT DISTINCT forma_pago FROM pago;
    ```

15. Devuelve un listado con todos los productos que pertenecen a la gama `Ornamentales` y que tienen más de `100` unidades en stock. El listado deberá estar ordenado por su precio de venta, mostrando en primer lugar los de mayor precio.

    ```SQL
    SELECT nombre, gama, cantidad_en_stock, precio_venta FROM producto
    WHERE gama = "Ornamentales" AND cantidad_en_stock > 100
    ORDER BY precio_venta desc
    ```

16. Devuelve un listado con todos los clientes que sean de la ciudad de `Madrid` y cuyo representante de ventas tenga el código de empleado `11` o `30`.

    ```SQL
    SELECT * FROM cliente WHERE ciudad = 'Madrid'
    AND (codigo_empleado_rep_ventas = 11 OR codigo_empleado_rep_ventas = 30);
    ```

### Query Nov 09

### 1.4.8 Subconsultas

#### 1.4.8.1 Con operadores básicos de comparación

1. Devuelve el nombre del cliente con mayor límite de crédito.

   ```SQL
   select nombre_cliente, limite_credito from cliente where limite_credito = (select max(limite_credito) from cliente);
   ```

2. Devuelve el nombre del producto que tenga el precio de venta más caro.

   ```SQL
   select nombre, precio_venta from producto where precio_venta = (select max(precio_venta) from producto);

   ```

3. Devuelve el nombre del producto del que se han vendido más unidades. (Tenga en cuenta que tendrá que calcular cuál es el número total de unidades que se han vendido de cada producto a partir de los datos de la tabla `detalle_pedido`)

   ```SQL
   SELECT nombre FROM producto WHERE codigo_producto = (
   SELECT codigo_producto
   FROM detalle_pedido
   GROUP BY codigo_producto ORDER BY SUM(cantidad) DESC LIMIT 1
   );
   ```

4. Los clientes cuyo límite de crédito sea mayor que los pagos que haya realizado. (Sin utilizar `INNER JOIN`).

   ```SQL
   select nombre_cliente, limite_credito from cliente
   where limite_credito > (Select MAX(total) from pago);
   ```

5. Devuelve el producto que más unidades tiene en stock.

   ```SQL
   select nombre, cantidad_en_stock from producto
   where cantidad_en_stock = (select max(cantidad_en_stock) from producto);
   ```

6. Devuelve el producto que menos unidades tiene en stock.

   ```SQL
   select nombre, cantidad_en_stock from producto
   where cantidad_en_stock = (select min(cantidad_en_stock) from producto);
   ```

7. Devuelve el nombre, los apellidos y el email de los empleados que están a cargo de **Alberto Soria**.

   ```SQL

   SELECT nombre, apellido1, apellido2, email, codigo_jefe
   FROM empleado WHERE codigo_jefe = (SELECT e.codigo_empleado FROM empleado e
   WHERE CONCAT(e.nombre, " " , e.apellido1) = "Alberto Soria");
   ```

#### 1.4.8.2 Subconsultas con ALL y ANY

1. Devuelve el nombre del cliente con mayor límite de crédito.
   ```SQL
   select nombre_cliente from cliente where limite_credito >= ALL(select limite_credito from cliente);
   ```

2. Devuelve el nombre del producto que tenga el precio de venta más caro.
   ```SQL
   select nombre from producto 
   where precio_venta >= ALL (select precio_venta from producto);
   ```

3. Devuelve el producto que menos unidades tiene en stock.

```SQL
   select nombre from producto 
   where cantidad_en_stock <= ALL (select cantidad_en_stock from producto);
```

#### 1.4.8.3 Subconsultas con IN y NOT IN

1. Devuelve el nombre, apellido1 y cargo de los empleados que no representen a ningún cliente.

```SQL
   SELECT e.nombre, e.apellido1, e.puesto from empleado e
   where e.codigo_empleado not in (select codigo_empleado_rep_ventas from cliente);
```

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

```SQL
   SELECT c.nombre_cliente from cliente c
   where c.codigo_cliente not in (select p.codigo_cliente from pago p);
```

3. Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago.

```SQL   
   SELECT c.nombre_cliente from cliente c
   where c.codigo_cliente in (select p.codigo_cliente from pago p);
 
```

4. Devuelve un listado de los productos que nunca han aparecido en un pedido.

```SQL
   SELECT * from producto p
   where p.codigo_producto not in (select dp.codigo_producto from detalle_pedido dp); 
```

5. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente.

```SQL
   select nombre, CONCAT(apellido1,' ',apellido2) as apellido, puesto, o.telefono
   from empleado
   JOIN oficina o ON empleado.codigo_oficina = o.codigo_oficina
   where empleado.codigo_empleado not in (select codigo_empleado_rep_ventas from cliente);
```

6. Devuelve las oficinas donde **no trabajan** ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama `Frutales`.

```SQL
 SELECT DISTINCT o.*
FROM oficina o
WHERE o.codigo_oficina NOT IN (
    SELECT DISTINCT e.codigo_oficina
    FROM empleado e
    WHERE e.codigo_empleado IN (
        SELECT DISTINCT c.codigo_empleado_rep_ventas
        FROM cliente c
        WHERE c.codigo_cliente IN (
            SELECT DISTINCT pe.codigo_cliente
            FROM pedido pe
            JOIN detalle_pedido dp ON pe.codigo_pedido = dp.codigo_pedido
            JOIN producto p ON dp.codigo_producto = p.codigo_producto
            WHERE p.gama = 'Frutales'
        )
    )
);

```

7. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

```SQL
      SELECT *
      FROM cliente
      WHERE cliente.codigo_cliente IN (
         SELECT codigo_pedido
         FROM detalle_pedido
         WHERE codigo_pedido IS NOT NULL
      )
      AND cliente.codigo_cliente NOT IN (
         SELECT codigo_cliente
         FROM pago
         WHERE codigo_cliente IS NOT NULL
      );
```


#### 1.4.8.4 Subconsultas con EXISTS y NOT EXISTS

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

```SQL
      select c.* from cliente c
      where not exists (
         select codigo_cliente from pago p
         where c.codigo_cliente = p.codigo_cliente
);
```
2. Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago.
  
   ```SQL
      select c.* from cliente c
      where exists (
         select codigo_cliente from pago p
         where c.codigo_cliente = p.codigo_cliente);
   ```
3. Devuelve un listado de los productos que nunca han aparecido en un pedido.
   
   ```SQL
   select * from producto p
   where not exists (
      select codigo_producto from detalle_pedido dp
      where p.codigo_producto = dp.codigo_producto);

   ```
4. Devuelve un listado de los productos que han aparecido en un pedido alguna vez.
   
   ```SQL
   select * from producto p
   where exists (
      select codigo_producto from detalle_pedido dp
      where p.codigo_producto = dp.codigo_producto);
   ```

-----

## 5 Tips de GROUP BY

1.  **AGRUPAR POR VARIOS CAMPOS**

   **GROUP BY** tiene la posibilidad de agrupar mas de dos atributos. Para ello solo debes agregar los atributos separados por coma.

   #### Estructura:
      SELECT... FROM ...
      JOIN...
      *GROUP BY* atributo1 , atributo2 ...

   #### Query con Jardineria

   ```SQL  
      SELECT ciudad, pais FROM cliente
      GROUP by pais ASC, ciudad;
   ```


2.  **FUNCIONES DE AGREGADO**

   Este tip nos ayudara a no presentar el error que da mysql al querer usar funciones agregadas con where. Por ello podremos utilizar HAVING en conjunto con WHERE.

   
   #### Estructura:
      SELECT...COUNT(..) FROM ...
      JOIN...
      *GROUP BY* atributo1
      ***HAVING** COUNT(..);

   #### Query con Jardineria

   ```SQL  
      SELECT gama, COUNT(*) as cantidad FROM producto
      GROUP by gama
      HAVING COUNT(*);
   ```


3.  **FUNCIONES ESCALARES**

   las funciones escalares funcionan con elementos nativos(INT, VARCHAR ...). Se puede utilizar estas funciones escalares con gruop by, como LOWER, UPPER, CONCAT O SUBSTRING entre otras.

   #### Estructura:
      SELECT...funcion_escalar(..) FROM ...
      JOIN...
      *GROUP BY* funcion_escalar(..)

   #### Query con Jardineria

   ```SQL  
      SELECT LOWER(pais)  FROM oficina
      GROUP by LOWER(pais);
   ```

4.  **UNION ALL**

   Este comando nos permite unir mas de dos tablas y mostrarlas en una sola query. 
   *Es importante que en la union de las query se utilice la misma cantidad de columnas a mostrar.

   #### Estructura:
      SELECT... FROM ...
      WHERE
      **UNION ALL**
      SELECT... FROM ...
      WHERE ... ;

   #### Query con Jardineria

   ```SQL  
      SELECT nombre FROM producto
      UNION ALL
      SELECT total FROM pago;
   ```


5.  **CONCATENAR LOS VALORES DEL RESULTADO EN UNA CELDA**

   Para realizar esta funcion usaremos el comando ROLLUP, el cual se encargara de devolver el total de cada agrupamiento hecho. Es decir agrupara por cierto atributo y luego sumara los campos de cada gurpo.


  #### Estructura:
      SELECT ... COUNT(*) AS Total
      FROM ...
      JOIN...
      GROUP BY ... **WITH ROLLUP**; 

   #### Query con Jardineria

   ```SQL  
      SELECT p.nombre, p.gama, COUNT(*) AS Total
      FROM producto p
      JOIN gama_producto g ON p.gama = g.gama
      GROUP BY p.gama, p.nombre WITH ROLLUP; 
   ```


   -----

## 5 Tips de UPDATE


1. **EDITAR APARTIR DE UN ATRIBUTO**

Se puede modificar el contenido de los campos de una columna especifica ya existente. si te da error puedes desactivar el modo de actualización segura para la sesión actual ejecutando la siguiente consulta:

   *SET SQL_SAFE_UPDATES = 0;*

   #### Estructura:
      UPDATE nombre_tabla SET atributo = atributo + 2;


   #### Query con Jardineria

   ```SQL  
      UPDATE producto SET precio_venta = (precio_venta * 0.19) WHERE codigo_producto = 11679;
   ```

2. **VALOR POR DEFECTO**


En mysql podremos utilizar el valor por defecto que se tiene ingresado. Si se olvido de agregar la modalidad de un valor por defecto cuando se creo la tabla usa el siguiente comando para anadirlo.

 ``` ALTER TABLE producto MODIFY COLUMN precio_venta DECIMAL(14,00) DEFAULT 1.00; ```

   #### Estructura:
      UPDATE nombre_tabla SET atributo = atributo + DEFAULT;

   #### Query con Jardineria

   ```SQL  
     UPDATE producto SET precio_venta = DEFAULT 
     WHERE codigo_producto = 21636;
   ```

3. **MODIFICAR LOS CAMPOS CON SUBCONSULTAS**


Otra manera de modificar los campos seleccionados es por medio de subconsultas.

   #### Estructura:
      update tabla SET 
      atributo = ( select CONCAT(atributo1,' ', atributo2) from tabla t
      WHERE t.id = tabla.id)

   #### Query con Jardineria

   ```SQL  
      ALTER TABLE cliente ADD COLUMN nombre_completo_contacto VARCHAR(100);

      update cliente SET 
      nombre_completo_contacto = ( select CONCAT(nombre_contacto,' ', apellido_contacto) from cliente c
      WHERE c.codigo_cliente = cliente.codigo_cliente)

      select nombre_cliente, nombre_contacto, apellido_contacto, nombre_completo_contacto from cliente;

   ```

4. **MODIFICAR LOS CAMPOS CON SUBCONSULTAS DENTRO DE WHERE**


Cuando utilizamos unn where para hacer un update, aqui podremos utilizar una subconsulta dentro del where, asi:

   #### Estructura:
      update tabla SET 
      atributo = ( select CONCAT(atributo1,' ', atributo2) from tabla t
      WHERE t.id = tabla.id)

   #### Query con Jardineria

   ```SQL  
      ALTER TABLE cliente ADD COLUMN nombre_completo_contacto VARCHAR(100);

      update cliente SET 
      nombre_completo_contacto = ( select CONCAT(nombre_contacto,' ', apellido_contacto) from cliente c
      WHERE c.codigo_cliente = cliente.codigo_cliente)

      select nombre_cliente, nombre_contacto, apellido_contacto, nombre_completo_contacto from cliente;

   ```

5. **UPDATE CON INNER JOIN**

Para utilizar el inner join en la modificacion de campos debemos seguir la estructura:

   #### Estructura:


      UPDATE tabla1
      SET atributo_cambiar = 'valor' 
      where tabla1.codigo_oficina in (
      select tabla2.codigo_oficina from tabla2
      where tabla2.ciudad LIKE 'B%'
      );

   #### Query con Jardineria

   ```SQL  
      ALTER TABLE empleado ADD COLUMN ciudad_oficina VARCHAR(100);

      UPDATE empleado
      SET ciudad_oficina = 'España' 
      where empleado.codigo_oficina in (
      select o.codigo_oficina from oficina o
      where o.ciudad LIKE 'B%'
      );

      select nombre, ciudad_oficina from empleado;
   ```

    -----



## 5 TIPS CON SELECT

1. **Valores Fijos**


   #### Estructura:

      INSERT INTO tabla
      SELECT atributos FROM tabla1, tabla2
      WHERE condiciones;

   #### Query con Jardineria
   
      ```sql
      CREATE TABLE IF NOT EXISTS table_empleado_cliente(
      nombreEmpleado varchar(100),
      nombreCliente varchar(100)
      );
      
      INSERT INTO table_empleado_cliente
      SELECT CONCAT(nombre, " ", apellido1), nombre_cliente FROM empleado em, cliente cl
      WHERE cl.codigo_empleado_rep_ventas = em.codigo_empleado
      AND codigo_cliente < 8;
      
      SELECT * FROM table_empleado_cliente;
      ```

2. **Operaciones con columnas**


   #### Estructura:

      select atributos_columnas as subnombre from tabla;

   #### Query con Jardineria  
      ```sql
      select codigo_oficina, CONCAT(' - ', region) as Ubicacion from oficina;
      ```


3. **Condiciones**

   #### Estructura:

      select atributos...
      CASE WHEN condicion THEN mensaje1 ELSE mensaje2 END as subnombre
      FROM tabla;

   #### Query con Jardineria  

      ```sql
      select nombre_cliente, limite_credito, 
      CASE WHEN limite_credito > 20000 THEN "Credito Alto" ELSE "Credito Bajo" END as NivelCredito
      FROM cliente;
      ```



4. **Subconsultas**

   #### Estructura:

      SELECT atributos (subconsulta) as subnombre FROM tabla;

   #### Query con Jardineria  
   
      ```sql
      SELECT gama, (SELECT count(*) FROM producto WHERE gama_producto.gama = producto.gama) as CantidadProductos FROM gama_producto;
      ```

5. **Consulta sobre Subconsulta**


   #### Estructura:

      SELECT atributos...
      FROM (
          Ssubconsulta
      ) AS subconsulta
      WHERE condiciones;


   #### Query con Jardineria

   
```sql
SELECT codigo_producto, proveedor, cantidad
FROM (
    SELECT codigo_producto, proveedor, cantidad_en_stock as cantidad
    FROM producto
    WHERE cantidad_en_stock < 100
) AS subconsulta
WHERE cantidad > 5 AND cantidad < 50;
```



## 5 Tips de WHERE


1. **NEGACION CON IN (NOT IN)**

Filtrar infomracion con la expresion IN pero utilizando la palabra de negacion NOT.

   #### Estructura:

      select .. from ..
      where ... NOT IN (...);


   #### Query con Jardineria

   ```SQL  
      select codigo_empleado, nombre from empleado
      where codigo_empleado NOT IN (2,31,17,5);

   ```

2. **SUBCONSULTAS**

Otra manera de usar subconsultas es de la mano con where.

   #### Estructura:
      select atributos from table 
      where (select COUNT(*) 
            from table2
            where table1.id = table2.id
      ) > 0;

   #### Query con Jardineria

   ```SQL  
      select nombre, codigo_oficina from empleado 
      where (select COUNT(*) 
            from oficina o
            where o.codigo_oficina = empleado.codigo_oficina
      ) > 0;
   ```

3. **REGEX**

   El regex se usa para verificar el dato apartiir de una secuencia.

   #### Estructura:
   SELECT * FROM table
   WHERE [Name] LIKE '[A-Za-z] [A-Za-z]%'

   #### Query con Jardineria
   ```SQL  
      SELECT nombre FROM empleado       
      WHERE nombre RLIKE '[A-Za-z] [A-Za-z]';
   ```


4. **SUBCONSULTA Y IN**

   Tambien se podra mezclar una subconsulta con IN.

   #### Estructura:


   #### Query con Jardineria
   ```SQL  
      SELECT nombre, codigo_oficina FROM empleado    
      WHERE codigo_oficina in (
            SELECT o.codigo_oficina FROM oficina o       
            WHERE codigo_oficina RLIKE 'K$' );
   ```


5. **FUNCIONES**

   #### Estructura:

SELECT atributo FROM table
WHERE SUBSTRING (atributo, 1, 1) = 'A'

   #### Query con Jardineria

   ```SQL  

SELECT nombre FROM producto
WHERE SUBSTRING(nombre, 1, 1) = 'A';
   ```

