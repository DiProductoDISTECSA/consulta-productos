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
    <input list="lista-codigos" id="codigo" name="codigo" placeholder="Ej. #CENIT-3-1013230BK">
    <datalist id="lista-codigos"></datalist>

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

    const csvUrl = 'https://raw.githubusercontent.com/DiProductoDISTECSA/consulta-productos/main/Copy%20of%20Lista%20de%20precios%20.xlsx%20-%20Lista%20de%20precios%20.csv';

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

          let options = '';
          productos.forEach(p => {
            if (p.CODIGO) {
              options += `<option value="${p.CODIGO}"></option>`;
            }
          });
          $('#lista-codigos').html(options);
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

    $('#codigo').on('input', function () {
      const codigo = $(this).val().trim();
      if (codigo) {
        mostrarDetalles(codigo);
      } else {
        $('#descripcion').text('—');
        $('#pvp').text('—');
        $('#pvd').text('—');
      }
    });

    $(document).ready(cargarProductos);
  </script>
</body>
</html>
