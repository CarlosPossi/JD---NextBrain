# ![programacao (2)](https://github.com/user-attachments/assets/1897c3da-0278-4fda-b978-36c3fd895c75) *Programação:*

### :desktop_computer: *Programação do site:*


```<?php
session_start();
include 'db.php';

if (!isset($_SESSION['pontos'])) {
    $_SESSION['pontos'] = 0;
}

$codigo_pedido = isset($_GET['codigo']) ? $_GET['codigo'] : '';
$all_pedidos_stmt = $pdo->query("SELECT DISTINCT codigo_pedido FROM pedidos");
$all_pedidos = $all_pedidos_stmt->fetchAll(PDO::FETCH_ASSOC);
$alert = "";
$pedidos = []; 

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $codigo_pedido_para_concluir = $_POST['codigo_pedido'];
    
    if ($codigo_pedido_para_concluir) {
        $status_stmt = $pdo->prepare("SELECT status FROM pedidos WHERE codigo_pedido = ?");
        $status_stmt->execute([$codigo_pedido_para_concluir]);
        $status = $status_stmt->fetchColumn();

        if ($status !== 'concluido') {
            // Marcar o pedido como concluído
            $stmt = $pdo->prepare("UPDATE pedidos SET status = 'concluido' WHERE codigo_pedido = ?");
            if ($stmt->execute([$codigo_pedido_para_concluir])) {
                // Adicionar 10 pontos à sessão
                $_SESSION['pontos'] += 10;

                // Atualizar o saldo no banco n ta funcionando corretamente....
                $stmt_saldo = $pdo->prepare("UPDATE saldo SET valor = valor + 10");
                if ($stmt_saldo->execute()) {
                    // Redirecionar para a mesma página com mensagem de sucesso (se der certo)
                    header('Location: mapa.php?codigo=' . $codigo_pedido . '&success=true');
                    exit();
                } else {
                    $alert = "Erro ao atualizar o saldo: " . implode(", ", $stmt_saldo->errorInfo());
                }
            } else {
                $alert = "Erro ao atualizar o pedido: " . implode(", ", $stmt->errorInfo());
            }
        } else {
            $alert = "Este pedido já foi concluído.";
        }
    }
}

if ($codigo_pedido) {
    $stmt = $pdo->prepare("SELECT p.nome, pe.quantidade FROM pedidos pe JOIN produtos p ON pe.produto_id = p.id WHERE pe.codigo_pedido = ?");
    $stmt->execute([$codigo_pedido]);
    $pedidos = $stmt->fetchAll(PDO::FETCH_ASSOC); // A variável $pedidos agora é preenchida corretamente
}

// Mensagem de sucesso se o pedido foi concluído
if (isset($_GET['success'])) {
    $alert = "Pedido concluído com sucesso! Você ganhou 10 pontos.";
}
?>


<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>John Deere - Mapa Interno</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css">
    <link rel="stylesheet" type="text/css" href="../CSS/estilo2.css" />
	
    
  <style>
        
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f7f7f7;
        }

        header {
            color: white;
            text-align: center;
            padding: 20px 0;
            border-radius: 5px;
            background-image: url('../IMG/agri.jpg');
            background-size: -200px; 
            background-position: center; 
            opacity: 100%;background-color: rgba(0, 0, 0, 0.5); 
            filter: grayscale(60%);
        }

        .container {
            display: flex;
            justify-content: center;
            align-items: flex-start;
            padding: 20px;
        }

        #map {
            height: 700px;
            width: 900px;
            z-index: 3;
        }

        .controls {
            display: flex;
            flex-direction: column;
            justify-content: center;
            margin-left: 20px;
            
        }

        .controls button {
            background-color: rgb(18, 143, 116);
            color: white;
            border: none;
            padding: 10px 20px;
            font-size: 14px;
            cursor: pointer;
            margin-top: 10px;
            border-radius: 5px;
            margin-Left: 6%;
            width: 190px;
            transition: all 0.5s ease;
        }

        .controls button:hover {
            background-color: transparent;
            color:rgb(18, 143, 116);
            border: 1px solid rgb(18, 143, 116);
            transition: all 0.5s ease;
        }

        .controls #resetButton {
            background-color: #f44336;
            margin-Left: -1%;
            width:80px;
            transition: all 0.5s ease;
        }

        .controls #resetButton:hover {
            background-color: transparent;
            color:#e53935;
            border: 1px solid #e53935;
            transition: all 0.5s ease;
        }

        .controls #selectedList {
            margin-top: 20px;
            padding: 10px;
            background-color: #fff;
            border-radius: 5px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            max-height: 400px;
            overflow-y: auto;
        }

        .controls #selectedList ul {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }

        .controls #selectedList ul li {
            padding: 5px;
            border-bottom: 1px solid #ddd;
        }

        .controls select {
            margin-top: 10px;
            padding: 10px;
            font-size: 16px;
            border-radius: 5px;
        }

        footer {
            background-color: #333;
            margin-Top:20%;
            color: white;
            text-align: center;
            padding: 10px 0;
            position: relative;
            bottom: 0;
            width: 100%;
        }
        .pontos{
            width: 170px;
            height:30px;
            position: absolute;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            z-index: 4;
            text-align: right;
            background: #fff;
            margin: -20px;
            border-radius:5px
        }
        .pontos{
            width: 170px;
            height:30px;
            position: absolute;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            z-index: 4;
            text-align: right;
            background: #fff;
            margin: -20px;
            border-radius:5px
        }
        .pontos h2{
            left: 55px;
            position: absolute
        }
        .moeda{
            width: 20%;
            position: absolute;
            margin-top:3%;
            margin-left: -25%;
        }



@media only screen and (max-width:750px) {

  .sidebar_perfil,.sidebar_carro{
		height:470px;
		width: 60%;
		box-shadow: 2px 8px 12px rgba(30, 29, 37, 0.199);
		position: absolute;
		margin-top: -200%;
		background: hsl(180, 100%, 100%);

opacity: 1;
		margin-left: 20%;
		border-radius:5px;
		
	}
.btn_concluido
{
	position: absolute;
	margin-top: 80%;
	margin-left:20%;
    width: 150px;
    font-size:12px;
	border-radius:2px;
}
#tag_pedidos,#tag_pedidos_2{
	position: absolute;
	margin-top: -140%;
	margin-left: -25%;
	z-index: 3;
	width: 150px;
	height: 50px;
	background-color: rgb(18, 143, 116);
	color: #fff;
	text-align: center;
	justify-content: center;
	display: flex;
	border-radius: 5px;
	transition: all 0.5s ease;
}

#tag_pedidos:hover,#tag_pedidos_2:hover{
	transition: all 0.5s ease;
	margin-top: -140%;
	margin-left: -6%;
	cursor: pointer;
}
#tag_pedidos_2{
	background-color: #f0ba56;
}
#tag_pedidos a,#tag_pedidos_2 a{
	position: absolute;
	margin-top: 12%;
	margin-left: -10%;
	text-decoration: none;
	color: #fff;
	text-align: center;
	justify-content: center;
	display: flex;}

  .sidebar_perfil h2{
	font-size: 35px;
	margin-top: 15%;
	margin-left: 1%;
    font-weight: 700;
}
.sidebar_perfil p{
	font-size: 16px;
	margin-top: 30%;
	margin-left: 2%;
    text-align: center;
	color: rgba(255, 0, 0, 0.541);
    
}
        }
    </style>
</head>
<body>
    <header>
        <h1>John Deere - Mapa Interno</h1>
    </header>
    <div class="pontos">
    <h2 >
    <img class="moeda"src="../IMG/dollar.png">
    Pontos:<?= htmlspecialchars($_SESSION['pontos']) ?>
    </div>


</h2>

   <div class="container">
        <div id="map"></div>
        <div class="controls">
            <button id="startTowing">Carro Kit mais proximo</button>
            <button id="resetButton">Reset</button>
            <select id="routeSelect" style="display:none;">
                <option value="">Selecione o percurso</option>
            </select>
            <div id="selectedList">
                <h3>Carros Kit Selecionados</h3>
                <ul></ul>
            </div>
        </div>
    </div>
    <br><br><br><br><br>
    <footer>
        <p>&copy; 2024 John Deere. Todos os direitos reservados.</p>
    </footer>

  <script src="https://unpkg.com/leaflet/dist/leaflet.js">



  </script>
  <script>
       const map = L.map('map', {
            crs: L.CRS.Simple,
            minZoom: -1,
            maxZoom: 1, // Ajusta o máximo zoom para garantir que a imagem se ajuste
            dragging: true,
            touchZoom: false,
            scrollWheelZoom: true,
            doubleClickZoom: false
        });


        // Definição das dimensões do mapa com base nas proporções da imagem original
        const imageWidth = 900;  // Largura da imagem original
        const imageHeight = 700; // Altura da imagem original
        const bounds = [[0, 0], [imageHeight, imageWidth]];

        // Centraliza o mapa com o zoom inicial
        map.setView([imageHeight / 2, imageWidth / 2], 0);

        // Adiciona a imagem do mapa como um layer
        const imageUrl = 'plano-interno.png'; // Ajuste o caminho conforme necessário
        L.imageOverlay(imageUrl, bounds).addTo(map);

        // Aqui vai a sua matriz binária real
        const binaryMatrix = [
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1]
        ];

        // Função para desenhar o grid no mapa
        function drawGrid(matrix, cellSize) {
            const gridLayer = L.layerGroup().addTo(map);

            matrix.forEach((row, y) => {
                row.forEach((cell, x) => {
                    if (cell === 0) { // 0 representa um obstáculo
                        const cellBounds = [
                            [y * cellSize, x * cellSize],
                            [(y + 1) * cellSize, (x + 1) * cellSize]
                        ];
                        L.rectangle(cellBounds, {
                            color: "#000", // Cor preta para obstáculos
                            weight: 0,
                            fillOpacity: 0,
                            fillColor: "#000" // Cor preta para obstáculos
                        }).addTo(gridLayer);
                    }
                });
            });
        }

        // Exemplo de chamada para desenhar o grid com tamanho de célula apropriado
        const cellSize = imageHeight / binaryMatrix.length; // Tamanho da célula baseado na altura da imagem
        drawGrid(binaryMatrix, cellSize);

        // Define as posições iniciais dos marcadores
        const markerPositions = [
    { pos: [500, 400], name: "Rebocador" },
    { pos: [400, 700], name: "Carro Kit 1" },
    { pos: [20, 600], name: "Carro Kit 2" },
    { pos: [20, 200], name: "Carro Kit 3" },
    { pos: [218, 300], name: "Carro Kit 4" },
    { pos: [440, 800], name: "Chegada" },
    { pos: [360, 600], name: "Setor A" }, // Setor A
    { pos: [50, 700], name: "Setor B" }, // Setor B
    { pos: [40, 100], name: "Setor C" }, // Setor C
    { pos: [350, 200], name: "Setor D" }, // Setor D
];

        // Ícones personalizados usando Font Awesome
const rebocadorIcon = L.divIcon({
    className: 'custom-icon',
    html: '<i class="fas fa-truck" style="color:blue; font-size: 20px;"></i>',
    iconSize: [30, 30],
    iconAnchor: [15, 30],
    popupAnchor: [1, -34],
});

const carroKitIcon = L.divIcon({
    className: 'custom-icon',
    html: '<i class="fas fa-shopping-cart" style="color:green; font-size: 20px;"></i>',
    iconSize: [30, 30],
    iconAnchor: [15, 30],
    popupAnchor: [1, -34],
});

const carroKitSelectedIcon = L.divIcon({
    className: 'custom-icon',
    html: '<i class="fas fa-shopping-cart" style="color:red; font-size: 20px;"></i>',
    iconSize: [30, 30],
    iconAnchor: [15, 30],
    popupAnchor: [1, -34],
});

const carroKitInTowingIcon = L.divIcon({
    className: 'custom-icon',
    html: '<i class="fas fa-shopping-cart" style="color:orange; font-size: 20px;"></i>',
    iconSize: [30, 30],
    iconAnchor: [15, 30],
    popupAnchor: [1, -34],
});

const rebocadorInTowingIcon = L.divIcon({
    className: 'custom-icon',
    html: '<i class="fas fa-truck" style="color:black; font-size: 20px;"></i>',
    iconSize: [30, 30],
    iconAnchor: [15, 30],
    popupAnchor: [1, -34],
});

const chegadaIcon = L.divIcon({
    className: 'custom-icon',
    html: '<i class="fas fa-flag-checkered" style="color:purple; font-size: 20px;"></i>',
    iconSize: [30, 30],
    iconAnchor: [15, 30],
    popupAnchor: [1, -34],

});

const setorIcon = L.divIcon({
    className: 'custom-icon',
    html: '<i class="fas fa-warehouse" style="color:orange; font-size: 20px;"></i>',
    iconSize: [30, 30],
    iconAnchor: [15, 30],
    popupAnchor: [1, -34],
});


const markers = markerPositions.map((marker, index) => {
    const icon = marker.name === "Rebocador" ? rebocadorIcon :
                marker.name === "Chegada" ? chegadaIcon :
                marker.name.startsWith("Setor") ? setorIcon : // Adicionando a lógica para Setores
                carroKitIcon;
    return L.marker(marker.pos, { icon }).addTo(map)
        .bindPopup(marker.name)
        .on('click', () => handleMarkerClick(marker, index));
});


let selectedMarkers = [];
let currentRoute = null;

function handleMarkerClick(marker, index) {
    const markerName = marker.name;

    // Lógica para selecionar setores
  if (markerName.startsWith("Setor")) {
        const isSelected = selectedMarkers.some(m => m.marker.name === markerName);

  if (isSelected) {
            // Deseleciona o setor
            selectedMarkers = selectedMarkers.filter(m => m.marker.name !== markerName);
        } else {
            // Seleciona o setor
            selectedMarkers.push({ marker, index: -1 }); // -1 para indicar que é um setor
        }

  updateSelectedList();
        return; // Evita a lógica para outros marcadores
    }

    // Restante da lógica para rebocador e carros kit
  if (markerName === "Rebocador") {
        return; // Não faz nada para rebocador
    }

  if (markerName === "Chegada") {
        if (!selectedMarkers.some(m => m.marker.name === "Chegada")) {
            selectedMarkers.push({ marker, index: -1 });
            updateSelectedList();
        }
        return;
    }

  const isSelected = selectedMarkers.some(m => m.index === index);

  if (isSelected) {
        markers[index].setIcon(carroKitIcon);
        selectedMarkers = selectedMarkers.filter(m => m.index !== index);
    } else {
        markers[index].setIcon(carroKitSelectedIcon);
        selectedMarkers.push({ marker, index });
    }

  updateSelectedList();
}



function updateSelectedList() {
    const selectedList = document.querySelector('#selectedList ul');
    selectedList.innerHTML = '';

  selectedMarkers.forEach(m => {
        const listItem = document.createElement('li');
        listItem.textContent = m.marker.name;
        selectedList.appendChild(listItem);
    });

  const routeSelect = document.querySelector('#routeSelect');
    routeSelect.innerHTML = '<option value="">Selecione o percurso</option>';

    // Adicionar opções para cada carro kit selecionado
  ["Carro Kit 1", "Carro Kit 2", "Carro Kit 3", "Carro Kit 4"].forEach((kit, idx) => {
        if (selectedMarkers.some(m => m.marker.name === kit)) {
            const option = document.createElement('option');
            option.value = `carroKit${idx + 1}`;
            option.textContent = kit;
            routeSelect.appendChild(option);
        }
    });

    // Adiciona a opção de chegada se estiver selecionada
  if (selectedMarkers.some(m => m.marker.name === "Chegada")) {
        const chegadaOption = document.createElement('option');
        chegadaOption.value = 'chegada';
        chegadaOption.textContent = 'Chegada';
        routeSelect.appendChild(chegadaOption);
    }

    // Adiciona opções para setores selecionados
  selectedMarkers.forEach(m => {
        if (m.index === -1) { // Se for um setor
            const option = document.createElement('option');
            option.value = m.marker.name; // Usar o nome do setor
            option.textContent = m.marker.name;
            routeSelect.appendChild(option);
        }
    });

  routeSelect.style.display = selectedMarkers.length > 0 ? 'block' : 'none';
}




let closestCarroKitPosition = null; // Variável para armazenar a posição do carro kit mais próximo 

document.querySelector('#startTowing').addEventListener('click', () => {
    const rebocadorMarker = markers.find(marker => marker.getPopup().getContent() === "Rebocador");
    if (!rebocadorMarker) {
        alert('Rebocador não encontrado.');
        return;
    }

  const rebocadorPos = rebocadorMarker.getLatLng();
    let start = [rebocadorPos.lat, rebocadorPos.lng];
    let end = null;

  if (selectedMarkers.length === 0) {
        // Nenhum carro kit selecionado, encontre o carro kit mais próximo
        const nearestCarroKit = findNearestCarroKit(start);
        if (nearestCarroKit) {
        closestCarroKitPosition = nearestCarroKit.pos; // Armazena a posição do carro kit mais próximo
        drawRoute(start, closestCarroKitPosition); // Desenha a rota
        } else {
            alert("Nenhum carro kit encontrado para reboque.");
            return;
        }
    } else {
        // Rota para carros kit selecionados
        const selectedEnd = selectedMarkers.map(m => m.marker.pos);
        if (selectedEnd.length > 0) {
            end = selectedEnd[0]; // Aqui você pode adicionar lógica para lidar com múltiplos carros kits
        }
    }

   if (end) {
        // Atualiza ícones
        markers[markerPositions.findIndex(m => m.name === "Rebocador")].setIcon(rebocadorInTowingIcon);
        if (selectedMarkers.length === 0) {
            markers[markerPositions.findIndex(m => m.name === findNearestCarroKit(start).name)].setIcon(carroKitInTowingIcon);
        } else {
            selectedMarkers.forEach(m => {
                markers[m.index].setIcon(carroKitInTowingIcon);
            });
        }

        // Desenha a rota
  drawRoute(start, end);
    }
});
function findNearestCarroKit(start) {
    let nearestCarroKit = null;
    let minDistance = Infinity;

markerPositions.forEach(marker => {
        if (marker.name.startsWith("Carro Kit")) {
            const distance = calculateDistance(start, marker.pos);
            if (distance < minDistance) {
                minDistance = distance;
                nearestCarroKit = marker;
            }
        }
    });

return nearestCarroKit;
}
function calculateDistance(pos1, pos2) {
    return Math.sqrt(Math.pow(pos1[0] - pos2[0], 2) + Math.pow(pos1[1] - pos2[1], 2));
}


document.querySelector('#resetButton').addEventListener('click', () => {
    if (currentRoute) {
        map.removeLayer(currentRoute);
        currentRoute = null; // Limpa a referência
    }

    // Remove a rota do carro kit mais próximo se houver
closestCarroKitPosition = null;

markers.forEach(marker => {
        if (marker.getPopup().getContent().includes('Carro Kit')) {
            marker.setIcon(carroKitIcon);
        } else if (marker.getPopup().getContent().includes('Rebocador')) {
            marker.setIcon(rebocadorIcon);
        } else if (marker.getPopup().getContent().includes('Chegada')) {
            marker.setIcon(chegadaIcon);
        }
    });

selectedMarkers = [];
    updateSelectedList();
});



document.querySelector('#routeSelect').addEventListener('change', (event) => {
    const selectedValue = event.target.value;

if (selectedValue !== '') {
        // Obtém a posição atual do rebocador
        const rebocadorMarker = markers.find(marker => marker.getPopup().getContent() === "Rebocador");
        const start = rebocadorMarker.getLatLng();

        // Verifica se o setor foi selecionado
  if (selectedMarkers.some(m => m.marker.name === selectedValue)) {
            const end = markerPositions.find(m => m.name === selectedValue).pos;
            drawRoute([start.lat, start.lng], end);
        } else if (selectedValue === 'chegada') {
            const end = markerPositions.find(m => m.name === "Chegada").pos;
            drawRoute([start.lat, start.lng], end);
        } else {
            const selectedMarkerName = {
                'carroKit1': "Carro Kit 1",
                'carroKit2': "Carro Kit 2",
                'carroKit3': "Carro Kit 3",
                'carroKit4': "Carro Kit 4"
            }[selectedValue];

  if (selectedMarkerName) {
                const selectedMarker = selectedMarkers.find(m => m.marker.name === selectedMarkerName);
                if (selectedMarker) {
                    const end = selectedMarker.marker.pos;
                    drawRoute([start.lat, start.lng], end);
                }
            }
        }
    }
});



  function aStar(matrix, start, end) {
            const rows = matrix.length;
            const cols = matrix[0].length;

  const openList = [];
            const closedList = [];
            const cameFrom = {};
            const gScore = Array.from({ length: rows }, () => Array(cols).fill(Infinity));
            const fScore = Array.from({ length: rows }, () => Array(cols).fill(Infinity));
            
  const { row: startRow, col: startCol } = start;
            const { row: endRow, col: endCol } = end;

  gScore[startRow][startCol] = 0;
            fScore[startRow][startCol] = heuristic(start, end);
            openList.push({ row: startRow, col: startCol });

  while (openList.length > 0) {
                const current = openList.reduce((prev, curr) => fScore[prev.row][prev.col] < fScore[curr.row][curr.col] ? prev : curr);
                const { row: currentRow, col: currentCol } = current;

  if (currentRow === endRow && currentCol === endCol) {
                    const path = [];
                    let step = current;
                    while (step) {
                        path.push(step);
                        step = cameFrom[`${step.row}-${step.col}`];
                    }
                    return path.reverse();
                }

  openList.splice(openList.indexOf(current), 1);
                closedList.push(current);

  const neighbors = getNeighbors(current, rows, cols);
                for (const neighbor of neighbors) {
                    const { row: neighborRow, col: neighborCol } = neighbor;
                    if (closedList.some(c => c.row === neighborRow && c.col === neighborCol) || matrix[neighborRow][neighborCol] === 0) {
                        continue;
                    }

  const tentativeGScore = gScore[currentRow][currentCol] + 1;
                    if (tentativeGScore < gScore[neighborRow][neighborCol]) {
                        cameFrom[`${neighborRow}-${neighborCol}`] = current;
                        gScore[neighborRow][neighborCol] = tentativeGScore;
                        fScore[neighborRow][neighborCol] = gScore[neighborRow][neighborCol] + heuristic(neighbor, end);

 if (!openList.some(n => n.row === neighborRow && n.col === neighborCol)) {
                            openList.push(neighbor);
                        }
                    }
                }
            }

            return []; // Retorna um array vazio se não houver caminho
        }

        function heuristic(a, b) {
            return Math.abs(a.row - b.row) + Math.abs(a.col - b.col);
        }

         // Função para desenhar a rota
function drawRoute(start, end) {
    // Limpa a rota anterior se existir
    if (currentRoute) {
        map.removeLayer(currentRoute);
        currentRoute = null; // Limpa a referência
    }

    // Converte coordenadas de mapa para matriz
  const startMatrix = mapToMatrix(start);
    const endMatrix = mapToMatrix(end);

  const path = aStar(binaryMatrix, startMatrix, endMatrix);

  if (path.length === 0) {
        alert("Nenhum caminho encontrado.");
        return;
    }

    // Converte o caminho de volta para coordenadas de mapa
  const pathLatLng = path.map(p => matrixToMap(p));
    
    // Desenha a nova rota
  currentRoute = L.polyline(pathLatLng, { color: 'blue', weight: 4 }).addTo(map);
}



  function getNeighbors(node, rows, cols) {
            const { row, col } = node;
            const neighbors = [];

  if (row > 0) neighbors.push({ row: row - 1, col });
  if (row < rows - 1) neighbors.push({ row: row + 1, col });
  if (col > 0) neighbors.push({ row, col: col - 1 });
  if (col < cols - 1) neighbors.push({ row, col: col + 1 });

   return neighbors;
  }

  let currentRebocadorPosition = null;

       // Atualiza a posição do rebocador e recalcula a rota
       function updateRebocadorPosition(x, y) {
currentRebocadorPosition = [x, y]; // Armazena a posição atualizada

const rebocadorMarker = markers.find(marker => marker.getPopup().getContent() === "Rebocador");
    if (rebocadorMarker) {
        rebocadorMarker.setLatLng([x, y]); // Atualiza a posição do rebocador

        // Obtém a posição atual do rebocador
  const start = [x, y];

        // Verifica se há um carro kit mais próximo armazenado
  if (closestCarroKitPosition) {
            drawRoute(start, closestCarroKitPosition); // Desenha a nova rota para o carro kit mais próximo
        }

        // Verifica se existe uma seleção ativa no menu
  const selectedOption = document.querySelector('#routeSelect').value;
  if (selectedOption) {
            let end;
            if (selectedOption === 'chegada') {
                end = markerPositions.find(m => m.name === "Chegada").pos;
            } else if (selectedMarkers.some(m => m.marker.name === selectedOption)) {
                end = markerPositions.find(m => m.name === selectedOption).pos;
            } else {
                // Se um carro kit estiver selecionado, obtém a posição do carro kit
                const selectedMarkerName = {
                    
  'carroKit2': "Carro Kit 2",
                    'carroKit3': "Carro Kit 3",
                    'carroKit4': "Carro Kit 4"
                    }[selectedOption];

  const selectedMarker = selectedMarkers.find(m => m.marker.name === selectedMarkerName);
                if (selectedMarker) {
                    end = selectedMarker.marker.pos;
                }
            }

            // Desenha a nova rota
  if (end) {
                drawRoute(start, end);
            }
        }
    } else {
        console.error('Marcador do rebocador não encontrado.');
    }
}




// Atualiza a posição do rebocador periodicamente
async function fetchRebocadorPosition() {
    try {
        const response = await fetch('http://192.168.88.94/projeto-mapa/receive_position.php');
        if (!response.ok) {
            throw new Error('Erro na resposta da solicitação.');
        }
        const data = await response.json();
        console.log(data); // Verifique os dados retornados
        const { x, y } = data;
        // Certifique-se de que x e y são válidos
        if (typeof x !== 'number' || typeof y !== 'number') {
            throw new Error('Valores inválidos para x e y.');
        }
        updateRebocadorPosition(x, y);
    } catch (error) {
        console.error('Erro ao buscar posição do rebocador:', error);
    }
}
setInterval(fetchRebocadorPosition, 3000); // Atualiza a cada 3 segundos


// Função para buscar a posição do Carro Kit 1
async function fetchCarroKit1Position() {
    try {
        const response = await fetch('http://192.168.88.94/projeto-mapa/receive_carro.php');
        if (!response.ok) {
            throw new Error('Erro na resposta da solicitação.');
        }
        const data = await response.json();
        console.log(data); // Verifique os dados retornados

  const { x, y } = data;

        // Verifica se as coordenadas são válidas
  if (typeof x === 'number' && typeof y === 'number') {
            updateCarroKit1Position(x, y);
        }
    } catch (error) {
  console.error('Erro ao buscar posição do Carro Kit 1:', error);
    }
}

// Função para atualizar a posição do Carro Kit 1
function updateCarroKit1Position(x, y) {
    const carroKit1Marker = markers.find(marker => marker.getPopup().getContent() === "Carro Kit 1");
    
  if (carroKit1Marker) {
        carroKit1Marker.setLatLng([x, y]); // Atualiza a posição do marcador
        carroKit1Marker.setIcon(carroKitIcon); // Define o ícone padrão

        // Verifica se o "Carro Kit 1" está selecionado
  const selectedOption = document.querySelector('#routeSelect').value;
        if (selectedOption === 'carroKit1') {
            const rebocadorMarker = markers.find(marker => marker.getPopup().getContent() === "Rebocador");
            const start = rebocadorMarker.getLatLng();
            drawRoute([start.lat, start.lng], [x, y]); // Desenha a nova rota
        }
    } else {
        console.error("Marcador Carro Kit 1 não encontrado.");
    }
}


// Atualiza a posição do Carro Kit 1 periodicamente
setInterval(fetchCarroKit1Position, 3000); // Atualiza a cada 3 segundos



// Funções auxiliares
function mapToMatrix(latLng) {
    const cellSize = imageHeight / binaryMatrix.length;
    return {
        row: Math.floor(latLng[0] / cellSize),
        col: Math.floor(latLng[1] / cellSize)
    };
}
function matrixToMap(matrix) {
    const cellSize = imageHeight / binaryMatrix.length;
    return [matrix.row * cellSize + cellSize / 2, matrix.col * cellSize + cellSize / 2];
}    







function toggleSidebar5() {
            const sidebar = document.getElementById("sidebar_perfil");
            if (sidebar.style.width === "60%") {
                sidebar.style.width = "0";
                sidebar.style.opacity ="0";
            } else {
                sidebar.style.width = "60%";
                sidebar.style.opacity ="1";
            
  }
            
        
}


function toggleSidebar6() {
            const sidebar = document.getElementById("sidebar_carro");
            if (sidebar.style.width === "60%") {
                sidebar.style.width = "0";
                sidebar.style.opacity ="0";
            } else {
                sidebar.style.width = "60%";
                sidebar.style.opacity ="1";
            
  }
            
        
  }
  function closereset2() {
            const sidebar = document.getElementById("sidebar_perfil");
            if (sidebar.style.width === "0%") {
                sidebar.style.width = "0";
            } else {
                sidebar.style.width = "0%";
                sidebar.style.opacity ="0";
            
  }
  }
   function closereset3() {
            const sidebar = document.getElementById("sidebar_carro");
            if (sidebar.style.width === "0%") {
                sidebar.style.width = "0";
            } else {
                sidebar.style.width = "0%";
                sidebar.style.opacity ="0";
            
  }
  }







        //DADOS SENSORES//
        
  function atualizarDados() {
            fetch('get_data.php')
                .then(response => response.json())
                .then(data => {
                    document.getElementById('batidaDetectada').innerText = "Batida Detectada: " + data.batidaDetectada;
                    document.getElementById('carrinhoLiberado').innerText = "Carrinho Liberado: " + data.carrinhoLiberado;
                    document.getElementById('velocidade').innerText = "Velocidade (km/h): " + data.velocidade;
                    document.getElementById('peso').innerText = "Peso (kg): " + data.peso;
                })
                .catch(error => console.error('Erro ao buscar dados:', error));
        }

  setInterval(atualizarDados, 2000); // Atualiza a cada 2 segundos
        atualizarDados(); // Chama imediatamente ao carregar a página
    </script>



			
<div class="posi_index">	
<div  id="sidebar_perfil" class="sidebar_perfil">
    <!-- Lista de todos os pedidos -->
        <a href="#" class="form_close" onclick="closereset2()">×</a>
    <h2>Meus Pedidos</h2> 
    <h4>Confira seus últimos pedidos, status e os gerencie.</h4>

  <div class="termos">
    <ul>
        <?php foreach ($all_pedidos as $pedido): ?>
            <li>
                <a href="mapa.php?codigo=<?= htmlspecialchars($pedido['codigo_pedido']) ?>">Pedido <?= htmlspecialchars($pedido['codigo_pedido']) ?></a>
                <?php 
                // Verifica o status do pedido
                $status_stmt = $pdo->prepare("SELECT status FROM pedidos WHERE codigo_pedido = ?");
                $status_stmt->execute([$pedido['codigo_pedido']]);
                $status = $status_stmt->fetchColumn();
                ?>
                (<?= htmlspecialchars($status) ?>)
            </li>
        <?php endforeach; ?>
    </ul>
    </div>
    <?php if ($codigo_pedido): ?>
        <!-- Detalhes do pedido específico -->
        <h3>Detalhes do Pedido <?= htmlspecialchars($codigo_pedido) ?></h3>
        <table border="1">
            <thead>
                <tr>
                    <th>Produto</th>
                    <th>Quantidade</th>
                </tr>
            </thead>
            <tbody>
<?php foreach ($pedidos as $pedido): ?>
                <tr>
                    <td><?= htmlspecialchars($pedido['nome']) ?></td>
                    <td><?= htmlspecialchars($pedido['quantidade']) ?></td>
                </tr>
                <?php endforeach; ?>
            </tbody>
        </table>

        <!-- Formulário para marcar o pedido como concluído -->
  <form action="mapa.php" method="post">
            <input type="hidden" name="codigo_pedido" value="<?= htmlspecialchars($codigo_pedido) ?>">
 <button class="btn_concluido" type="submit">Marcar como Concluído</button>
  </form>
  <?php else: ?>
  <p>Nenhum pedido foi selecionado.</p>
  <?php endif; ?>

  <br>
</a>

</div>


  </div>

			
	
<div  id="sidebar_carro" class="sidebar_carro">
    <!-- Lista de todos os pedidos -->
        <a href="#" class="form_close" onclick="closereset3()">×</a>
        <h1>Dados dos Sensores</h1>
  <div class="dados-sensor">
        <div class="dado" id="batidaDetectada">Batida Detectada: -</div>
        <div class="dado" id="carrinhoLiberado">Carrinho Liberado: -</div>
        <div class="dado" id="velocidade">Velocidade (km/h): -</div>
        <div class="dado" id="peso">Peso (kg): -</div>
    </div>

</div>


	
	
<div id="sidebar4" class="sidebar4">

  <div class="carro_select">
            <img class="img_carro" src="../IMG/carro.png">
            <img class="img_money" src="../IMG/dollar.png">
            <h2>CarroKitX</h2>
            <h3>11:01</h3>
            <h5>20</h5>
        </div>
        <a href="#" class="closebtn" onclick="closereset()">×</a>
          <div class="icon" onclick="aceitarcorrida() "><a href="#" onclick="toggleSidebar4()"><h4>Confirmar</h4></div></a>
        
    </div>


  <div id="tag_pedidos" ><a href="#" class="tag_pedidos" onclick="toggleSidebar5()">
    
  
  <h4>Pedidos</h4>
  <svg class="icon_loja" xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="#FFFFFF"><path d="M200-80q-33 0-56.5-23.5T120-160v-560q0-33 23.5-56.5T200-800h40v-80h80v80h320v-80h80v80h40q33 0 56.5 23.5T840-720v560q0 33-23.5 56.5T760-80H200Zm0-80h560v-400H200v400Zm0-480h560v-80H200v80Zm0 0v-80 80Zm80 240v-80h400v80H280Zm0 160v-80h280v80H280Z"/></svg>    
                   

</div></a>
<br><br><br><br>
<div id="tag_pedidos_2" ><a href="#" class="tag_pedidos" onclick="toggleSidebar6()">
    
  
    <h4>CarroKit</h4>
   <svg class="icon_loja" xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="#FFFFFF"><path d="M240-200v40q0 17-11.5 28.5T200-120h-40q-17 0-28.5-11.5T120-160v-320l84-240q6-18 21.5-29t34.5-11h440q19 0 34.5 11t21.5 29l84 240v320q0 17-11.5 28.5T800-120h-40q-17 0-28.5-11.5T720-160v-40H240Zm-8-360h496l-42-120H274l-42 120Zm-32 80v200-200Zm100 160q25 0 42.5-17.5T360-380q0-25-17.5-42.5T300-440q-25 0-42.5 17.5T240-380q0 25 17.5 42.5T300-320Zm360 0q25 0 42.5-17.5T720-380q0-25-17.5-42.5T660-440q-25 0-42.5 17.5T600-380q0 25 17.5 42.5T660-320Zm-460 40h560v-200H200v200Z"/></svg>
  </div></a>
  


</body>
</html>
