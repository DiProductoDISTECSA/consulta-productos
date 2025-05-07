<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Consulta de Productos</title>
  <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.2/papaparse.min.js"></script>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(to bottom, #e3f2fd, #ffffff);
      padding: 20px;
      margin: 0;
    }

    .container {
      max-width: 600px;
      margin: auto;
      background: white;
      padding: 25px;
      border-radius: 12px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
      text-align: center;
    }

    .container img {
      max-width: 100%;
      margin-bottom: 20px;
      border-radius: 8px;
    }

    h1 {
      color: #f7a600; /* Título en color HEX #f7a600 */
      margin-bottom: 20px;
    }

    input[type="text"] {
      width: 100%;
      padding: 12px;
      font-size: 16px;
      border: 2px solid #ccc;
      border-radius: 8px;
      margin-bottom: 15px;
      transition: border-color 0.3s ease;
    }

    input[type="text"]:focus {
      border-color: #f7a600; /* Color del borde cuando se enfoca */
      outline: none;
    }

    .info {
      text-align: left;
      margin-top: 20px;
    }

    .info label {
      font-weight: bold;
      color: #f7a600; /* Títulos en color HEX #f7a600 */
      display: block;
      margin-top: 10px;
    }

    .info span {
      display: block;
      font-size: 17px;
      color: #222;
    }

    @media (max-width: 480px) {
      .container {
        padding: 15px;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- Imagen decorativa o logo -->
    <img src="https://via.placeholder.com/600x150?text=Logo+Empresa" alt="Imagen" />

    <h1>Consulta de Producto</h1>

    <input type="text" id="codigo" placeholder="Ingresa el código (Ej. #CENIT-3-1013230BK)" autocomplete="off">

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
          const dataLimpia = results.data.map(p => ({
            CODIGO: p.CODIGO?.trim(),
            DESCRIPCION: p.DESCRIPCION?.trim(),
            PVP: parseFloat(p.PVP?.replace(/,/g, '').trim()) || 0,
            PVD: parseFloat(p.PVD?.replace(/,/g, '').trim()) || 0
          }));

          productos.push(...dataLimpia);
        },
        error: function(err) {
          alert("Error al cargar los datos del CSV.");
          console.error(err);
        }
      });
    }

    function mostrarDetalles(codigo) {
      const producto = productos.find(p => p.CODIGO === codigo);
      if (producto) {
        $('#descripcion').text(producto.DESCRIPCION || '—');
        $('#pvp').text(producto.PVP.toLocaleString('es-CO', { minimumFractionDigits: 2 }));
        $('#pvd').text(producto.PVD.toLocaleString('es-CO', { minimumFractionDigits: 2 }));
      } else {
        $('#descripcion').text('No encontrado');
        $('#pvp').text('—');
        $('#pvd').text('—');
      }
    }

    $('#codigo').on('input', function () {
      const codigo = $(this).val().trim();
      if (codigo) mostrarDetalles(codigo);
      else {
        $('#descripcion').text('—');
        $('#pvp').text('—');
        $('#pvd').text('—');
      }
    });

    $(document).ready(cargarProductos);
  </script>
</body>
</html>

