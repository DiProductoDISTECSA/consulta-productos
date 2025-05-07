# Consulta-Productos
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Consulta de Productos</title>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.2/papaparse.min.js"></script>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .container { max-width: 600px; margin: 0 auto; }
        input, button { padding: 10px; margin: 10px 0; width: 100%; font-size: 16px; }
        .result { margin-top: 20px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Consulta de Producto</h1>
        <label for="codigo">Escribe o selecciona un Código</label>
        <input list="lista-codigos" id="codigo" name="codigo" placeholder="Ej. #CENIT-3-1013230BK">
        <datalist id="lista-codigos"></datalist>

        <button onclick="getDetails()">Consultar</button>

        <div class="result" id="result"></div>
    </div>

    <script>
        const productos = [];

        // URL del archivo CSV en GitHub (Formato RAW)
        const csvUrl = 'https://raw.githubusercontent.com/DiProductoDISTECSA/consulta-productos/main/Copy%20of%20Lista%20de%20precios%20.xlsx%20-%20Lista%20de%20precios.csv';

        function cargarProductos() {
            Papa.parse(csvUrl, {
                download: true,
                header: true,
                skipEmptyLines: true,
                dynamicTyping: false,
                complete: function(results) {
                    // Mostrar los datos cargados en consola para depuración
                    console.log("Datos cargados:", results.data);

                    // Verifica si los datos son válidos
                    if (results.data && results.data.length > 0) {
                        // Limpiar los precios, quitando las comas
                        results.data.forEach(p => {
                            if (p.PVP) {
                                p.PVP = parseFloat(p.PVP.replace(/,/g, ''));  // Eliminar comas y convertir a número
                            }
                            if (p.PVD) {
                                p.PVD = parseFloat(p.PVD.replace(/,/g, ''));  // Eliminar comas y convertir a número
                            }
                        });

                        productos.push(...results.data);

                        let options = '';
                        productos.forEach(p => {
                            if (p.CODIGO) {
                                options += `<option value="${p.CODIGO}"></option>`;
                            }
                        });
                        $('#lista-codigos').html(options);
                    } else {
                        alert("No se encontraron datos válidos en el archivo CSV.");
                    }
                },
                error: function(error) {
                    console.error("Error al cargar el CSV:", error);
                    alert("Error al cargar los datos del archivo CSV.");
                }
            });
        }

        function getDetails() {
            const codigo = $('#codigo').val().trim();
            if (!codigo) {
                alert("Por favor, ingresa un código.");
                return;
            }

            // Buscar el producto en los datos cargados
            const producto = productos.find(p => String(p.CODIGO) === String(codigo));
            console.log(producto); // Verifica qué producto se encuentra
            if (producto) {
                $('#result').html(`
                    <h2>Detalles del Producto</h2>
                    <p><strong>Descripción:</strong> ${producto.DESCRIPCION}</p>
                    <p><strong>PVP:</strong> ${producto.PVP.toFixed(2)}</p> <!-- Mostrar PVP con dos decimales -->
                    <p><strong>PVD:</strong> ${producto.PVD.toFixed(2)}</p> <!-- Mostrar PVD con dos decimales -->
                `);
            } else {
                $('#result').html('<p>No se encontraron detalles para este código.</p>');
            }
        }

        // Cargar productos cuando la página esté lista
        $(document).ready(cargarProductos);
    </script>
</body>
</html>
