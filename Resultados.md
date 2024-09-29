# :white_check_mark: *Resultados:* 

### 🛒  **Carrinho-kit:**
No protótipo do carrinho-kit desenvolvido pela NextBrain, utilizamos o ESP32 DEVKIT V4 para capturar sinais Wi-Fi de roteadores e calcular a posição do carrinho com base nos valores de RSSI obtidos. Um dos métodos aplicados foi a trilateração desses sinais, permitindo estimar a distância até os roteadores e, assim, determinar a localização geográfica do carrinho. Essas coordenadas foram então remapeadas para as dimensões do mapa da fábrica. Antes de enviar as informações ao site, utilizamos um servidor local com XAMPP para criar e gerenciar um banco de dados, onde armazenamos os dados de geolocalização do carrinho-kit.
