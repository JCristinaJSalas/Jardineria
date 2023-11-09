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
   group by c.nombre_cliente;
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
   group by c.nombre_cliente;
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
   group by c.nombre_cliente;
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
