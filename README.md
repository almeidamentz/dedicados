<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Seleção de Transportador e Placas</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            text-align: center;
        }
        .input-container {
            margin-bottom: 20px;
        }
        .result {
            display: none;
            margin-top: 20px;
            text-align: left;
        }
        .placa {
            margin: 10px 0;
        }
        .placa label {
            margin-right: 10px;
        }
        .placa select {
            padding: 5px;
        }
        .button-container {
            margin-top: 20px;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Seleção de Transportador e Placas</h1>
    
    <div class="input-container">
        <label for="codigo">Digite o código do transportador:</label>
        <input type="text" id="codigo" placeholder="Digite o código">
        <button onclick="mostrarPlacas()">Buscar</button>
    </div>

    <div class="result" id="result">
        <h3 id="nomeTransportador"></h3>
        <div id="placas"></div>
    </div>

    <div class="button-container">
        <button id="btnEnviar" style="display:none" onclick="baixarCSV()">Enviar</button>
    </div>
</div>

<script>
    const transportadores = {
        "88523": {
            nome: "M & A TRANSPORTES DE CARGAS LTDA",
            placas: [
                "RPL9A00", "RPH3C68", "RAO3B19", "QTY1D24", "SKO5D07"
            ]
        },
        "99521": {
            nome: "PABLO TRANSPORTES LTDA",
            placas: [
                "NRZ1B09", "NRZ1B16", "NRZ0F51", "QZU0H07", "RRS9H97", "RRP0A52", "RRX1A22"
            ]
        },
        "10875": {
            nome: "PETROCEM COMERCIO E TRANSPORTES DE COMBUSTIVEIS EIRELI",
            placas: [
                "RWZ3D30", "RWZ3B50", "RXJ2G59", "RWZ3E10", "RWS3F69", "QVR3A94", "RWU0G69", "RXI3D80", "RWZ3C40"
            ]
        }
    };

    function mostrarPlacas() {
        const codigo = document.getElementById('codigo').value;
        const resultado = document.getElementById('result');
        const nomeTransportador = document.getElementById('nomeTransportador');
        const placasDiv = document.getElementById('placas');
        const btnEnviar = document.getElementById('btnEnviar');

        if (transportadores[codigo]) {
            nomeTransportador.textContent = transportadores[codigo].nome;
            placasDiv.innerHTML = '';

            let allPlacasFilled = true;
            transportadores[codigo].placas.forEach(placa => {
                const placaDiv = document.createElement('div');
                placaDiv.classList.add('placa');
                
                placaDiv.innerHTML = `
                    <label for="${placa}">${placa}:</label>
                    <select id="${placa}">
                        <option value="disponivel">Disponível</option>
                        <option value="em_rota">Em Rota</option>
                        <option value="base">Base</option>
                        <option value="manutencao">Manutenção</option>
                    </select>
                `;
                
                placasDiv.appendChild(placaDiv);

                // Verifica se todas as placas possuem status selecionado
                const select = document.getElementById(placa);
                select.addEventListener('change', () => {
                    allPlacasFilled = transportadores[codigo].placas.every(placa => {
                        return document.getElementById(placa).value !== "";
                    });

                    if (allPlacasFilled) {
                        btnEnviar.style.display = 'inline-block';  // Exibe o botão de envio
                    } else {
                        btnEnviar.style.display = 'none';  // Esconde o botão de envio
                    }
                });
            });

            resultado.style.display = 'block';
            btnEnviar.style.display = 'none';  // Esconde o botão inicialmente
        } else {
            resultado.style.display = 'none';
            alert('Código inválido. Tente novamente.');
        }
    }

    function baixarCSV() {
        const codigo = document.getElementById('codigo').value;
        const transportador = transportadores[codigo];
        if (!transportador) {
            alert('Código inválido.');
            return;
        }

        const data = new Date();
        const dataFormatada = `${data.getDate()}/${data.getMonth() + 1}/${data.getFullYear()} ${data.getHours()}:${data.getMinutes()}:${data.getSeconds()}`;
        
        let csvContent = "Transportador,Placa,Status,Data\n";
        
        transportador.placas.forEach(placa => {
            const status = document.getElementById(placa).value;
            csvContent += `${transportador.nome},${placa},${status},${dataFormatada}\n`;
        });

        const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
        const link = document.createElement("a");
        const url = URL.createObjectURL(blob);
        link.setAttribute("href", url);
        link.setAttribute("download", `dados_transportador_${codigo}.csv`);
        link.style.visibility = 'hidden';
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }
</script>

</body>
</html>
