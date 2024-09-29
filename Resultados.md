# :white_check_mark: *Resultados:* 

## 🛒  **Carrinho-kit:**
No protótipo do carrinho-kit desenvolvido pela NextBrain, utilizamos o ESP32 DEVKIT V4 para capturar sinais Wi-Fi de roteadores e calcular a posição do carrinho com base nos valores de RSSI obtidos. Um dos métodos aplicados foi a trilateração desses sinais, permitindo estimar a distância até os roteadores e, assim, determinar a localização geográfica do carrinho. Essas coordenadas foram então remapeadas para as dimensões do mapa da fábrica. Antes de enviar as informações ao site, utilizamos um servidor local com XAMPP para criar e gerenciar um banco de dados, onde armazenamos os dados de geolocalização do carrinho-kit.

### 🌎 **Informações obtidas pelo ESP32 DEVKIT V4**
<h4 align="center"> 
  
![8a8fd89a-e15b-4456-b47b-e81a5906d6e1](https://github.com/user-attachments/assets/eaa6ac19-0818-4357-801a-21d5222199c6)
</h4>
<br>

## 🛒  **Sensores presentes no Carrinho-kit:**
No protótipo do carrinho-kit, embarcamos uma série de sensores com o objetivo de coletar dados essenciais para o monitoramento e a operação eficiente do equipamento. Para medir a velocidade do carrinho, utilizamos um reed switch, que fornece dados sobre a velocidade de deslocamento. Células de carga foram integradas ao sistema para monitorar o peso da carga transportada no interior do carrinho, garantindo maior controle sobre os carrinhos vazios. Além disso, instalamos um sensor de colisão na base do carrinho, responsável por identificar eventuais choques ou impactos durante o percurso.

Atendendo a uma solicitação específica da equipe da John Deere, incluímos um botão que permite liberar o carrinho mesmo quando há peso em seu interior, oferecendo maior flexibilidade operacional no chão de fábrica. Esses dados, capturados pelos sensores, são processados e enviados via ESP32 DEVKIT V1, que se comunica diretamente com o servidor local XAMPP. No servidor, as informações são armazenadas em um banco de dados e posteriormente disponibilizadas em um sistema web, proporcionando aos administradores maior controle e visibilidade sobre as operações e condições dos carrinhos na fábrica. Esse sistema de coleta e monitoramento contínuo de dados garante uma gestão mais eficiente e uma melhor tomada de decisões.

### 🤖 **Informações obtidas pelos sensores:**
<h4 align="center"> 
  
![65a6b210-7076-433a-9f90-7adb1932f47c](https://github.com/user-attachments/assets/92dfa2b7-845e-47f8-8f2a-4910d07ae0f0)

</h4>
<br>

## 🚜  **Rebocador:**
No protótipo do rebocador desenvolvido pela NextBrain, utilizamos o ESP32 DEVKIT V4 para capturar sinais Wi-Fi emitidos por roteadores e calcular a posição do veículo com base nos valores de RSSI (Received Signal Strength Indicator) obtidos. Um dos métodos aplicados foi a trilateração, que nos permite estimar a distância até os roteadores e, com isso, determinar a localização precisa do rebocador dentro da fábrica. As coordenadas calculadas são, então, ajustadas às dimensões reais do mapa da planta, garantindo maior precisão no monitoramento.

Para gerenciar e armazenar essas informações de geolocalização, utilizamos um servidor local rodando XAMPP, onde criamos um banco de dados dedicado. Esse banco armazena todas as informações de posicionamento do rebocador antes de serem enviadas e exibidas no sistema web, proporcionando uma visualização clara e em tempo real das operações no ambiente fabril.

### 🗺️ **Informações obtidas pelo ESP32 DEVKIT V4**
<h4 align="center"> 
  
![8a8fd89a-e15b-4456-b47b-e81a5906d6e1](https://github.com/user-attachments/assets/0797bdfa-2582-4969-bf12-a98a0df89456)
</h4>
<br>

## 💻  **Site:**
Todas as informações coletadas são disponibilizadas em nosso site, onde a geolocalização é atualizada e apresentada por meio de ícones. O rebocador é representado por um "caminhãozinho" azul, enquanto o carrinho-kit aparece como um carrinho de supermercado. O site também conta com uma tela dedicada às informações dos sensores e outra para o recebimento de pedidos, indicados por quadrados verde e amarelo no canto inferior esquerdo da página. Além disso, há uma interface de login e cadastro, bem como uma seção específica para a realização de pedidos de peças, garantindo uma gestão completa e integrada das operações. Além disso, temos um sistema de gamificação da realização das entregas, quando o colaborador termina uma entrega eles ganha pontos que futuramente pode ser trocado por algo. 

### :scroll: **Tela de pedidos**
<h4 align="center"> 
  
![6d59fd29-b347-48d3-9fb8-31c0f9ff797b](https://github.com/user-attachments/assets/aff6cb8a-4e9d-4ace-a6d5-df6f8fd5f1bf)
</h4>
<br>

### 🖥️ **Mapa do site**
<h4 align="center"> 
  
![6d59fd29-b347-48d3-9fb8-31c0f9ff797b](https://github.com/user-attachments/assets/aff6cb8a-4e9d-4ace-a6d5-df6f8fd5f1bf)
</h4>
<br>
