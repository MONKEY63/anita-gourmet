<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Ventas Anita Gourmet</title>

  <!-- Ajustes para iPhone como app -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="default">
  <meta name="apple-mobile-web-app-title" content="Anita Gourmet">

  <!-- Conexión con manifest e íconos -->
  <link rel="manifest" href="manifest.json">
  <link rel="apple-touch-icon" href="apple-touch-icon.png">
  <link rel="icon" href="icon-192.png" type="image/png">

  <!-- Tailwind -->
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen p-6">
  <div class="max-w-6xl mx-auto bg-white shadow-lg rounded-2xl p-6">
    <h1 class="text-2xl font-bold text-center mb-6">📒 Anita Gourmet - Ventas</h1>

    <!-- FORMULARIO DE VENTA -->
    <form id="ventaForm" class="grid grid-cols-1 md:grid-cols-5 gap-4 mb-6">
      <input id="cliente" type="text" placeholder="Cliente" class="border rounded p-2" required>
      <input id="producto" type="text" placeholder="Producto" class="border rounded p-2" required>
      <input id="cantidad" type="number" placeholder="Cantidad" min="1" class="border rounded p-2" required>
      <input id="precio" type="number" placeholder="Precio" min="0" step="0.01" class="border rounded p-2" required>
      <button type="submit" class="bg-blue-600 text-white rounded p-2 hover:bg-blue-700">➕ Añadir Venta</button>
    </form>

    <!-- TABLA HISTORIAL -->
    <div class="overflow-x-auto">
      <table class="w-full border-collapse">
        <thead>
          <tr class="bg-gray-200 text-left">
            <th class="p-2 border">#</th>
            <th class="p-2 border">Cliente</th>
            <th class="p-2 border">Producto</th>
            <th class="p-2 border">Cantidad</th>
            <th class="p-2 border">Precio</th>
            <th class="p-2 border">Total</th>
            <th class="p-2 border">Acciones</th>
          </tr>
        </thead>
        <tbody id="tablaVentas"></tbody>
      </table>
    </div>

    <!-- BOTONES -->
    <div class="flex gap-4 mt-6">
      <button id="btnImprimir" class="bg-green-600 text-white rounded p-2 hover:bg-green-700">🖨 Imprimir Reporte</button>
      <button id="btnLimpiar" class="bg-red-600 text-white rounded p-2 hover:bg-red-700">🗑 Limpiar Ventas</button>
    </div>
  </div>

  <script>
    let ventas = JSON.parse(localStorage.getItem("ventas")) || [];
    let editIndex = null;

    const form = document.getElementById("ventaForm");
    const tabla = document.getElementById("tablaVentas");
    const cliente = document.getElementById("cliente");
    const producto = document.getElementById("producto");
    const cantidad = document.getElementById("cantidad");
    const precio = document.getElementById("precio");

    // Guardar en localStorage
    function guardarDatos() {
      localStorage.setItem("ventas", JSON.stringify(ventas));
    }

    // Renderizar tabla
    function renderTabla() {
      tabla.innerHTML = "";
      ventas.forEach((v, index) => {
        tabla.innerHTML += `
          <tr>
            <td class="p-2 border">${index + 1}</td>
            <td class="p-2 border">${v.cliente}</td>
            <td class="p-2 border">${v.producto}</td>
            <td class="p-2 border">${v.cantidad}</td>
            <td class="p-2 border">$${parseFloat(v.precio).toFixed(2)}</td>
            <td class="p-2 border">$${(v.cantidad * v.precio).toFixed(2)}</td>
            <td class="p-2 border flex gap-2">
              <button onclick="editar(${index})" class="bg-yellow-500 text-white px-2 py-1 rounded hover:bg-yellow-600">✏ Editar</button>
              <button onclick="eliminar(${index})" class="bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600">🗑</button>
            </td>
          </tr>
        `;
      });
    }

    // Añadir/Editar venta
    form.addEventListener("submit", e => {
      e.preventDefault();
      const nueva = {
        cliente: cliente.value,
        producto: producto.value,
        cantidad: parseInt(cantidad.value),
        precio: parseFloat(precio.value)
      };

      if (editIndex !== null) {
        ventas[editIndex] = nueva;
        editIndex = null;
      } else {
        ventas.push(nueva);
      }

      guardarDatos();
      renderTabla();
      form.reset();
    });

    // Editar
    function editar(index) {
      const v = ventas[index];
      cliente.value = v.cliente;
      producto.value = v.producto;
      cantidad.value = v.cantidad;
      precio.value = v.precio;
      editIndex = index;
    }

    // Eliminar
    function eliminar(index) {
      if (confirm("¿Seguro que deseas eliminar esta venta?")) {
        ventas.splice(index, 1);
        guardarDatos();
        renderTabla();
      }
    }

    // Imprimir
    document.getElementById("btnImprimir").addEventListener("click", () => {
      let contenido = `
        <h2>Reporte de Ventas</h2>
        <table border="1" cellspacing="0" cellpadding="5">
          <tr><th>#</th><th>Cliente</th><th>Producto</th><th>Cantidad</th><th>Precio</th><th>Total</th></tr>
          ${ventas.map((v, i) => `
            <tr>
              <td>${i + 1}</td>
              <td>${v.cliente}</td>
              <td>${v.producto}</td>
              <td>${v.cantidad}</td>
              <td>$${v.precio.toFixed(2)}</td>
              <td>$${(v.cantidad * v.precio).toFixed(2)}</td>
            </tr>
          `).join("")}
        </table>
      `;
      let win = window.open("", "_blank");
      win.document.write(contenido);
      win.print();
      win.close();
    });

    // Limpiar
    document.getElementById("btnLimpiar").addEventListener("click", () => {
      if (confirm("¿Seguro que deseas limpiar todas las ventas?")) {
        ventas = [];
        guardarDatos();
        renderTabla();
      }
    });

    // Inicializar
    renderTabla();
  </script>
</body>
</html>
