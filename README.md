<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Entrada e Saída de Veículos</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #000;
            color: #f8f8f8;
            margin: 0;
            padding: 20px;
        }

        h2, h3 {
            color: #fcfbfa;
            text-align: center;
        }

        form, #carrosCadastrados, #vagasStatus, .botao-limpar {
            background-color: #1a1a1a;
            color: #ffffff;
            padding: 20px;
            border-radius: 8px;
            margin: 20px auto;
            max-width: 500px;
            box-shadow: 0 0 10px rgba(255, 215, 0, 0.3);
        }

        label {
            display: block;
            margin: 10px 0 5px;
            font-weight: bold;
        }

        input[type="text"],
        input[type="datetime-local"],
        select,
        button {
            width: 100%;
            box-sizing: border-box;
            padding: 10px;
            margin-top: 10px;
            font-size: 16px;
            border: 1px solid #ffffff;
            border-radius: 4px;
        }

        input[type="text"],
        input[type="datetime-local"],
        select {
            background-color: #000;
            color: #fdfdfd;
        }

        button {
            background-color: #FFD700;
            color: #000;
            border: none;
            font-weight: bold;
            cursor: pointer;
        }

        button:hover {
            background-color: #FFC700;
        }

        .carro {
            background-color: #333;
            padding: 10px;
            border-radius: 5px;
            margin-bottom: 10px;
            color: #FFD700;
        }

        #vagasStatus div {
            margin-top: 10px;
            padding: 10px;
            background-color: #222;
            border-radius: 5px;
            color: #ffffff;
        }

        .livre {
            background-color: #006400 !important;
        }

        .ocupada {
            background-color: #8B0000 !important;
        }
    </style>
</head>
<body>

    <h2>Controle de Entrada e Saída de Veículos</h2>

    <form id="formVeiculo">
        <label for="placa">Placa do Veículo:</label>
        <input type="text" id="placa" name="placa" placeholder="Ex: ABC-1234" required>

        <label for="tipo">Tipo de Veículo:</label>
        <select id="tipo" name="tipo">
            <option value="carro">Carro</option>
            <option value="moto">Moto</option>
            <option value="caminhao">Caminhão</option>
        </select>

        <label for="vaga">Escolha a Vaga:</label>
        <select id="vaga" name="vaga" required>
            <option value="1">Vaga 1</option>
            <option value="2">Vaga 2</option>
            <option value="3">Vaga 3</option>
            <option value="4">Vaga 4</option>
        </select>

        <label for="entrada">Data e Hora de Entrada:</label>
        <input type="datetime-local" id="entrada" name="entrada" required>

        <button type="submit">Registrar</button>
    </form>

    <div id="carrosCadastrados">
        <h3>Carros Cadastrados:</h3>
    </div>

    <div id="vagasStatus">
        <h3>Status das Vagas:</h3>
        <div id="vaga1Status" class="livre">Vaga 1: Livre</div>
        <div id="vaga2Status" class="livre">Vaga 2: Livre</div>
        <div id="vaga3Status" class="livre">Vaga 3: Livre</div>
        <div id="vaga4Status" class="livre">Vaga 4: Livre</div>
    </div>

    <div class="botao-limpar">
        <button onclick="limparCarros()">Limpar Carros Cadastrados</button>
    </div>

    <script>
        function getCarros() {
            return JSON.parse(localStorage.getItem('carros')) || [];
        }

        function salvarCarros(carros) {
            localStorage.setItem('carros', JSON.stringify(carros));
        }

        function atualizarListaCarros() {
            const carros = getCarros();
            const carrosCadastrados = document.getElementById('carrosCadastrados');
            carrosCadastrados.innerHTML = '<h3>Carros Cadastrados:</h3>';

            carros.forEach(carro => {
                const div = document.createElement('div');
                div.classList.add('carro');
                div.innerHTML = `<strong>Placa:</strong> ${carro.placa} <br>
                                 <strong>Tipo:</strong> ${carro.tipo} <br>
                                 <strong>Vaga:</strong> ${carro.vaga} <br>
                                 <strong>Entrada:</strong> ${carro.entrada}`;
                carrosCadastrados.appendChild(div);
            });

            atualizarStatusVagas();
        }

        function atualizarStatusVagas() {
            const carros = getCarros();
            for (let i = 1; i <= 4; i++) {
                const vagaElement = document.getElementById(`vaga${i}Status`);
                vagaElement.className = 'livre';
                vagaElement.textContent = `Vaga ${i}: Livre`;
            }

            carros.forEach(carro => {
                const vagaElement = document.getElementById(`vaga${carro.vaga}Status`);
                if (vagaElement) {
                    vagaElement.className = 'ocupada';
                    vagaElement.textContent = `Vaga ${carro.vaga}: Ocupada`;
                }
            });
        }

        document.getElementById('formVeiculo').addEventListener('submit', function (event) {
            event.preventDefault();

            const placa = document.getElementById('placa').value;
            const tipo = document.getElementById('tipo').value;
            const vaga = parseInt(document.getElementById('vaga').value);
            const entrada = document.getElementById('entrada').value;

            const carros = getCarros();

            if (carros.some(carro => carro.vaga === vaga)) {
                alert('A vaga já está ocupada! Escolha outra vaga.');
                return;
            }

            carros.push({ placa, tipo, vaga, entrada });
            salvarCarros(carros);
            atualizarListaCarros();

            alert(`Veículo com placa ${placa} cadastrado com sucesso na Vaga ${vaga}!`);
            document.getElementById('formVeiculo').reset();
        });

        function limparCarros() {
            localStorage.removeItem('carros');
            atualizarListaCarros();
        }

        atualizarListaCarros();
        
    </script>
</body>
</html>
