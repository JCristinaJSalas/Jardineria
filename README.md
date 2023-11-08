# 1.4.5 Consultas multitabla (Composición interna)

Resuelva todas las consultas utilizando la sintaxis de `SQL1` y `SQL2`. Las consultas con sintaxis de `SQL2` se deben resolver con `INNER JOIN` y `NATURAL JOIN`.

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.

    ```SQL
    SELECT cliente.nombre_cliente as Nombre_Cliente,
    CONCAT(empleado.nombre,' ',empleado.apellido1,' ',empleado.apellido2)
    AS Nombre_Representante_Ventas
    FROM cliente
    JOIN empleado ON c.codigo_empleado_rep_ventas = empleado.codigo_empleado;
    ```

2. Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.

    ```SQL
    SELECT DISTINCT cliente.nombre_cliente, empleado.nombre AS nombre_representante FROM cliente
    JOIN pago ON cliente.codigo_cliente = pago.codigo_cliente
    JOIN empleado ON cliente.codigo_empleado_rep_ventas = empleado.codigo_empleado;
    ```

3. Muestra el nombre de los clientes que no hayan realizado pagos junto con el nombre de sus representantes de ventas.

    ```SQL
    SELECT DISTINCT cliente.nombre_cliente, empleado.nombre AS nombre_representante FROM cliente
    LEFT JOIN pago ON cliente.codigo_cliente = pago.codigo_cliente
    JOIN empleado ON cliente.codigo_empleado_rep_ventas = empleado.codigo_empleado
    WHERE pago.codigo_cliente IS NULL;
    ```

4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

    ```SQL
    SELECT DISTINCT cliente.nombre_cliente, empleado.nombre AS nombre_representante, oficina.ciudad FROM cliente 
    JOIN pago ON cliente.codigo_cliente = pago.codigo_cliente
    JOIN empleado ON cliente.codigo_empleado_rep_ventas = empleado.codigo_empleado
    JOIN oficina ON empleado.codigo_oficina = oficina.codigo_oficina;
    ```

5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

    ```SQL
    SELECT DISTINCT cliente.nombre_cliente, empleado.nombre AS nombre_representante, oficina.ciudad FROM cliente 
    LEFT JOIN pago ON cliente.codigo_cliente = pago.codigo_cliente
    JOIN empleado ON cliente.codigo_empleado_rep_ventas = empleado.codigo_empleado
    JOIN oficina ON empleado.codigo_oficina = oficina.codigo_oficina
    WHERE pago.codigo_cliente IS NULL;
    ```

6. Lista la dirección de las oficinas que tengan clientes en `Fuenlabrada`.

    ```SQL
    SELECT DISTINCT IF(oficina.linea_direccion2 = '', oficina.linea_direccion1,
    CONCAT(oficina.linea_direccion1,' - ',oficina.linea_direccion2)) AS direccion_oficina
    FROM cliente
    JOIN empleado ON cliente.codigo_empleado_rep_ventas = empleado.codigo_empleado
    JOIN oficina ON empleado.codigo_oficina = oficina.codigo_oficina
    WHERE c.ciudad = 'Fuenlabrada';
    ```

7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

    ```SQL
    SELECT DISTINCT cliente.nombre_cliente, empleado.nombre AS nombre_representante, oficina.ciudad FROM cliente 
    JOIN empleado ON cliente.codigo_empleado_rep_ventas = empleado.codigo_empleado
    JOIN oficina ON empleado.codigo_oficina = oficina.codigo_oficina;
    ```

8. Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.

    ```SQL
    SELECT empleado.nombre AS nombre_empleado, empleado2.nombre AS nombre_jefe FROM empleado
    LEFT JOIN empleado e2 ON empleado.codigo_jefe = empleado2.codigo_empleado;
    ```

9. Devuelve un listado que muestre el nombre de cada empleado, el nombre de su jefe y el nombre del jefe de sus jefe.

    ```SQL
    SELECT empleado.nombre AS nombre_empleado, empleado2.nombre AS nombre_jefe, empleado3.nombre AS nombre_jefe_mayor
    FROM empleado 
    LEFT JOIN empleado  ON empleado.codigo_jefe = empleado2.codigo_empleado
    LEFT JOIN empleado  ON empleado2.codigo_jefe = empleado3.codigo_empleado;
    ```

10. Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.

    ```SQL
    SELECT DISTINCT nombre_cliente FROM cliente
    JOIN pedido ON cliente.codigo_cliente = pago.codigo_cliente
    WHERE fecha_entrega > fecha_esperada;
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