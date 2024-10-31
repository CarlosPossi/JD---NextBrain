![Design sem nome](https://github.com/user-attachments/assets/d5365aa3-6853-4153-ab99-a2a3dfd11f53)
<br>
# :white_check_mark: *Teste realizados no protótipo:* 

## 📶  **Teste de Estabilidade de sinal:**

### a) Definição da ferramenta de teste:
No protótipo de localização, realizamos testes para validar e otimizar a precisão, utilizando roteadores em posições fixas e ajustando os padrões do mapa para maior consistência. Após ajustes e uma série de testes, alcançamos uma precisão com variação de cerca de um metro, conseguindo quase mitigar essa margem de erro. Esses testes ocorreram no Innovation Lab, um ambiente controlado onde pudemos observar como mudanças estruturais e materiais próximos afetam diretamente a precisão do protótipo.
<br>
### b) Evidências de testes:
*Teste demonstrado na IDE do Arduino:*
<h4 align="center"> 
  
![image](https://github.com/user-attachments/assets/fec5aff0-7b30-4ddd-8ec7-7a50f97e541f)
</h4>
<br>

### c) Discussão dos resultados: 
Com base nesses resultados, acreditamos que é possível aprimorar a precisão da geolocalização dos carrinhos-kits e rebocadores no espaço fabril por meio de métodos matemáticos e melhorias no hardware. Com a atualização do hardware, será possível captar o sinal RSSI dos roteadores com mais eficiência, o que aumentará a precisão da localização. Além disso, a aplicação de algoritmos matemáticos avançados contribuirá ainda mais para a precisão do sistema, otimizando o desempenho da geolocalização em ambiente fabril.
<br>
### d) Soluções futuras: 
Para otimizar o hardware dos protótipos, planejamos implementar um microcontrolador com WiFi mais avançado, projetado para captar sinais com maior sensibilidade e estabilidade. Isso permitirá uma coleta de dados mais precisa, essencial para reduzir inconsistências na localização. Esses dados serão posteriormente processados para filtrar ruídos e outras interferências que possam comprometer a precisão da geolocalização.

Além disso, pretendemos utilizar roteadores de maior potência, com capacidade de alcance e penetração ampliadas, o que melhorará a intensidade e a estabilidade do sinal RSSI recebido. Esses roteadores robustos serão instalados em pontos estratégicos, otimizando a distribuição do sinal pelo espaço fabril e facilitando sua captação pelo microcontrolador. Com essa configuração aprimorada, criaremos um ambiente favorável para maximizar a precisão de localização dos carrinhos-kits e rebocadores, permitindo um monitoramento mais eficiente e confiável no ambiente fabril.
<br>
<br>

## 🕐 **Teste de Tempo de resposta:**

### a) Definição da ferramenta de teste:
Durante o desenvolvimento do protótipo do projeto, realizamos testes para medir o tempo de resposta desde a captação do sinal RSSI dos roteadores até o envio dos dados para o site que desenvolvemos. Acompanharam-se esses tempos de resposta tanto pelo monitor serial na IDE do Arduino quanto pelo armazenamento em nosso banco de dados. Com essas duas formas de validação, verificamos que o tempo de resposta médio do protótipo foi de aproximadamente 3 minutos. É importante destacar que esses testes foram feitos usando nossos roteadores pessoais; com outro tipo de roteador, os resultados podem variar.
### b) Evidências de testes:
*Teste demonstrado na IDE do Arduino:*
<h4 align="center"> 
  
![image](https://github.com/user-attachments/assets/b9aeba21-4bdc-4d9a-9035-63bb4909a9ec)
</h4>
<br>
### c) Discussão dos resultados: 
Com o resultado obtido no tempo de resposta do nosso protótipo, consideramos o desempenho satisfatório para a captação e envio dos dados. Esse tempo atende aos requisitos do projeto em condições normais, garantindo uma comunicação eficiente entre os sensores, o sistema de armazenamento e o site. Contudo, identificamos que, em ambientes com roteadores de menor capacidade ou conexões de internet de baixa qualidade, o tempo de resposta é significativamente impactado, resultando em um atraso acima do esperado. Esse aumento ocorre devido à menor velocidade de transmissão dos dados, o que compromete a eficiência e pode limitar a precisão do sistema em situações de uso intensivo ou em locais com infraestrutura de rede instável.
### d) Soluções futuras: 
Nosso grupo realizou uma série de testes com diferentes modelos de roteadores e uma variedade de condições de rede para identificar os requisitos mínimos de desempenho que um roteador precisa atender, garantindo o funcionamento otimizado do protótipo. A intenção é obter dados que nos permitam estabelecer parâmetros mínimos para a qualidade do sinal e a velocidade de transmissão, assegurando uma resposta rápida e precisa mesmo em cenários de conectividade variada. Paralelamente, estamos considerando o desenvolvimento de uma solução de software que permita captar e amplificar o sinal recebido, reduzindo significativamente o impacto de redes de baixa qualidade. Com essa abordagem, esperamos não apenas melhorar o tempo de resposta em condições adversas, mas também garantir um desempenho consistente e confiável em diferentes ambientes de operação. Essa solução integrada visa proporcionar uma experiência de uso mais eficiente e estável, independentemente da infraestrutura de rede disponível.
