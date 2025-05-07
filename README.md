# Consulta-Productos

<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Consulta de Productos</title>
  <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.2/papaparse.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f4f4f4;
      padding: 20px;
    }
    .container {
      max-width: 600px;
      margin: 0 auto;
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    input {
      padding: 10px;
      margin: 10px 0;
      width: 100%;
      font-size: 16px;
    }
    .suggestions {
      border: 1px solid #ccc;
      max-height: 150px;
      overflow-y: auto;
      display: none;
      background: white;
      position: absolute;
      width: calc(100% - 42px);
      z-index: 1000;
    }
    .suggestions div {
      padding: 10px;
      cursor: pointer;
    }
    .suggestions div:hover {
      background: #f0f0f0;
    }
    .info {
      margin-top: 20px;
    }
    .info label {
      font-weight: bold;
      display: block;
      margin-top: 10px;
    }
    .info span {
      display: block;
      font-size: 16px;
      color: #333;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Consulta de Producto</h1>
    <label for="codigo">Escribe o selecciona un Código</label>
    <div style="position: relative;">
      <input id="codigo" name="codigo" autocomplete="off" placeholder="Ej. #CENIT-3-1013230BK">
      <div class="suggestions" id="sugerencias"></div>
    </div>

    <div class="info">
      <label>Descripción:</label>
      <span id="descripcion">—</span>

      <label>PVP:</label>
      <span id="pvp">—</span>

      <label>PVD:</label>
      <span id="pvd">—</span>
    </div>
  </div>

  <script>
    const productos = [];

    const csvUrl = 'https://raw.githubusercontent.com/DiProductoDISTECSA/consulta-productos/refs/heads/main/Copy%20of%20Lista%20de%20precios%20.xlsx%20-%20Lista%20de%20precios%20.csv';

    function cargarProductos() {
      Papa.parse(csvUrl, {
        download: true,
        header: true,
        skipEmptyLines: true,
        complete: function(results) {
          const dataLimpia = results.data.map(p => {
            return {
              CODIGO: p.CODIGO?.trim(),
              DESCRIPCION: p.DESCRIPCION?.trim(),
              PVP: parseFloat(p.PVP?.replace(/"/g, '').replace(/,/g, '').trim()) || 0,
              PVD: parseFloat(p.PVD?.replace(/"/g, '').replace(/,/g, '').trim()) || 0
            };
          });

          productos.push(...dataLimpia);
        },
        error: function(error) {
          console.error("Error al cargar el CSV:", error);
          alert("Error al cargar los datos del archivo CSV.");
        }
      });
    }

    function mostrarDetalles(codigo) {
      const producto = productos.find(p => String(p.CODIGO).trim() === String(codigo).trim());

      if (producto) {
        $('#descripcion').text(producto.DESCRIPCION || '—');
        $('#pvp').text(producto.PVP.toLocaleString('es-CO', {minimumFractionDigits: 2}));
        $('#pvd').text(producto.PVD.toLocaleString('es-CO', {minimumFractionDigits: 2}));
      } else {
        $('#descripcion').text('No encontrado');
        $('#pvp').text('—');
        $('#pvd').text('—');
      }
    }

    $(document).ready(function() {
      cargarProductos();

      $('#codigo').on('input', function () {
        const input = $(this).val().toLowerCase();
        const sugerencias = productos
          .filter(p => p.CODIGO && p.CODIGO.toLowerCase().includes(input))
          .slice(0, 10);

        const sugerenciasHTML = sugerencias.map(p => `<div data-codigo="${p.CODIGO}">${p.CODIGO}</div>`).join('');
        if (sugerencias.length > 0 && input.length > 0) {
          $('#sugerencias').html(sugerenciasHTML).show();
        } else {
          $('#sugerencias').hide();
        }
      });

      $('#sugerencias').on('click', 'div', function () {
        const codigo = $(this).data('codigo');
        $('#codigo').val(codigo);
        $('#sugerencias').hide();
        mostrarDetalles(codigo);
      });

      $(document).on('click', function (e) {
        if (!$(e.target).closest('#codigo, #sugerencias').length) {
          $('#sugerencias').hide();
        }
      });
    });
  </script>
</body>
</html>

