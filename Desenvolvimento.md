# :wrench: *Desenvolvimento:*

<h4 align="center"> 
  
![trator (1)](https://github.com/user-attachments/assets/aaf7d891-2172-423f-b579-44e5ececbaf1)
<img src="http://img.shields.io/static/v1?label=STATUS&message=EM%20DESENVOLVIMENTO&color=GREEN&style=for-the-badge"/>
![trator (1)](https://github.com/user-attachments/assets/067765e2-3521-4ee5-b247-ead1bfcd612d)
</h4>

### :diamond_shape_with_a_dot_inside: **Arquitetura dos carrinhos-kits:**

Essa arquitetura ilustra o processo de obtenção das distâncias dos roteadores por meio do valor de RSSI captado pelo ESP32 DEVKIT V4. O dispositivo, programado para essa finalidade, coleta o RSSI dos roteadores e realiza o cálculo de trilateração para determinar a posição atual do carrinho-kit. Em seguida, ele mapeia essas coordenadas obtidas para o sistema de coordenadas do mapa que desenvolvemos para o site.

Nesta etapa, utilizamos três bibliotecas principais: WiFi.h, responsável por ativar o módulo Wi-Fi do ESP32; HTTPClient.h, que gerencia a comunicação entre o ESP e o banco de dados; e math.h, utilizada para os cálculos matemáticos necessários à determinação das distâncias. Com isso, conseguimos obter as distâncias com precisão razoável e transmitir os dados coletados para o nosso banco de dados, garantindo a integração eficiente entre o hardware e o sistema de localização.



<h4 align="center"> 
  
![image](https://github.com/user-attachments/assets/fcfee749-184c-4155-a4cc-4dc308e3eac1)
</h4>
<br>

### :diamond_shape_with_a_dot_inside: **Arquitetura dos rebocadores:**
Essa arquitetura explica o processo de cálculo das distâncias entre o rebocador e os roteadores a partir do valor de RSSI, utilizando o ESP32 DEVKIT V4. O dispositivo é programado para captar o RSSI dos roteadores, aplicar o algoritmo de trilateração e, assim, determinar a posição do rebocador. Após essa etapa, as coordenadas calculadas são convertidas para o formato do mapa que desenvolvemos no site.

Três bibliotecas foram usadas nesse processo: WiFi.h, para habilitar a conectividade Wi-Fi no ESP32; HTTPClient.h, para gerenciar a troca de informações com o banco de dados; e math.h, responsável pelos cálculos matemáticos envolvidos na determinação das distâncias. Dessa forma, foi possível obter as distâncias com boa precisão e garantir a comunicação eficiente dos dados do ESP para o banco de dados, integrando o sistema de localização de maneira eficiente.
<h4 align="center"> 
  
![image](https://github.com/user-attachments/assets/9a0f0384-ddbb-430e-af32-1c7ec8c7b5e0)
</h4>
<br>

### :diamond_shape_with_a_dot_inside: **Arquitetura dos sensores do carrinho-kit:**
Esta arquitetura descreve o funcionamento dos sensores integrados nos carrinhos-kit, que monitoram diversas condições operacionais. O sistema é composto por um sensor de velocidade, que mede a velocidade de deslocamento do carrinho; células de carga, que monitoram o peso da carga no interior do carrinho; sensor de colisão, que identifica impactos e acidentes, aumentando a segurança; e um botão, que permite a liberação do carrinho, mesmo com carga, caso o operador faça a liberação manual.

Após a coleta dos dados gerados por esses sensores, o ESP32 processa e transmite essas informações ao banco de dados, garantindo que elas sejam atualizadas. Esses dados são então disponibilizados no site, permitindo o monitoramento completo das condições do carrinho, incluindo a velocidade, o peso da carga, ocorrências de colisão e ações manuais. Esse fluxo de comunicação assegura uma gestão eficiente e segura de todo o sistema.
<h4 align="center">  
  
![image](https://github.com/user-attachments/assets/3e0019ac-48b6-457e-966e-f460f757f7de)
</h4>
<br>

#  ![linguagem-de-codificacao](https://github.com/user-attachments/assets/90dd44d0-57e5-4c4c-90d2-e64999075ca1) **Linguagens utilizadas:**

<h4 align="center">  
  
![c-](https://github.com/user-attachments/assets/a03d8310-7d33-4425-88c7-1a57c1f0f4e4) ![php](https://github.com/user-attachments/assets/02829bab-2842-4350-83d8-8cea777d76ec) ![html-5](https://github.com/user-attachments/assets/98d83337-2d34-477c-acf7-1fc85ce8fa2b) ![script-java](https://github.com/user-attachments/assets/95e2f365-0694-40d1-b39d-5de8f7d3136d)

</h4>
