# 🚀 Desafio de Dinâmica de Voo: Projeto Nashira

![Status](https://img.shields.io/badge/Status-Concluído-success)
![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Library](https://img.shields.io/badge/Lib-RocketPy-orange)

Este repositório contém a solução técnica para o **Desafio 2: Dinâmica de Voo do Nashira**, desenvolvido como parte do Processo Seletivo da **EPTA (Equipe de Propulsão e Tecnologia Aeroespacial)** da Universidade Federal de Uberlândia (UFU).

### 🚀 Verificação de Ambiente e Dependênciass
Este script inicial tem como objetivo preparar o ambiente de execução Python para simulações aeroespaciais. Ele verifica se todas as bibliotecas necessárias estão instaladas e, caso falte alguma, realiza a instalação automática.
```
# Tenta importar as bibliotecas
try:
    import pandas as pd
    import numpy as np
    import matplotlib.pyplot as plt
    import rocketpy
    print("✅ Sucesso! Todas as bibliotecas estão instaladas.")
except ImportError as e:
    # Se der erro, ele avisa qual falta e explica como instalar
    print(f"❌ Falta uma biblioteca: {e.name}")
    print("Tentando instalar automaticamente...")
    
    # Comando mágico para instalar direto pelo notebook
    # O "!" diz para o notebook rodar um comando de terminal
    !pip install pandas numpy matplotlib rocketpy scipy
    
    print("Instalação concluída. Por favor, reinicie o Kernel (Kernel -> Restart) e tente novamente.")
```
### 📋 Bibliotecas Utilizadas
Biblioteca
- Pandas:	        Manipulação e análise de dados tabulares.
- NumPy:	        Processamento de matrizes e cálculos matemáticos de alta performance.
- Matplotlib:	    Geração de gráficos e visualizações de dados.
- RocketPy:	    Biblioteca principal para simulação de trajetória de foguetes de sondagem.
- SciPy:	        Ferramentas para integração numérica e otimização (instalada como dependência).

### 🛠️ Como o código funciona
- O script utiliza um bloco try-except para gerenciar a configuração de forma inteligente:
- Tentativa de Importação: O Python tenta carregar as bibliotecas na memória. Se todas estiverem presentes, uma mensagem de sucesso é exibida.
- Tratamento de Erros (ImportError): Caso alguma biblioteca não seja encontrada (como a RocketPy, que não costuma vir pré-instalada em ambientes como o Google Colab ou Anaconda), o script captura o erro.
- Instalação Automática: O comando !pip install é acionado diretamente do terminal do notebook para baixar as dependências faltantes.
- Reinicialização do Kernel: Após a instalação, o usuário é instruído a reiniciar o Kernel para que as novas bibliotecas sejam reconhecidas corretamente pelo interpretador.

### ⚠️ Observação Importante
Sempre que o comando !pip install for executado, é fundamental reiniciar o Kernel do seu Jupyter Notebook ou Google Colab antes de prosseguir com os cálculos físicos para evitar conflitos de versão.

# 📊 Carregamento e Inspeção de Telemetria
Esta etapa do projeto foca em importar os dados brutos gerados pelos sensores do foguete (ou simulador) e validar a integridade desses dados para as análises subsequentes.
# Carrega o arquivo avisando ao Python que o decimal é vírgula
```
df = pd.read_csv('nashira_telemetry.csv', decimal=',')

# Mostra as 5 primeiras linhas para conferir se deu certo
display(df.head())

# Mostra informações sobre as colunas (para garantir que são números 'float' e não texto 'object')
df.info()
```

### 🛠️ Descrição do Processo
O script realiza três operações críticas:

1. Leitura de Dados com Formatação Regional
- ```df = pd.read_csv('nashira_telemetry.csv', decimal=',')```
- Como em português utilizamos a vírgula para separar casas decimais (ex: 10,5), informamos ao Pandas para converter automaticamente esses valores para o padrão computacional de ponto (ex: 10.5). Isso evita que os números sejam lidos como texto.

2. Visualização Rápida
- ```display(df.head())```
- Utiliza o comando para renderizar uma tabela com as 5 primeiras linhas do arquivo. Isso permite uma verificação visual imediata de colunas como tempo, altitude, aceleração e pressão.

3. Validação de Tipos de Dados
- ```df.info()```
- Ele exibe: o nome de cada coluna, quantidade de valores nulos e o tipo de dado.

#📥 Download de Ativos e Perfis Técnicos
O script utiliza o utilitário ```curl``` para buscar os seguintes arquivos nos repositórios oficiais da RocketPy:

- NACA0012-radians.txt : Tabela de coordenadas do perfil de asa (aerofólio) NACA 0012, usado para calcular a sustentação das aletas.
- Cesaroni_M1670.eng : Arquivo de motor (padrão RockSim) contendo a curva de empuxo, massa inicial e grãos do motor motor Cesaroni M1670.
- powerOffDragCurve.csv : Coeficiente de arrasto do foguete quando o motor não está queimando (fase de voo inercial).
- powerOnDragCurve.csv : Coeficiente de arrasto do foguete durante a queima do motor (considerando efeitos de base).

### 🛠️ Detalhamento dos Comandos
- ```!pip install rocketpy```: Garante que a biblioteca principal esteja instalada no ambiente de execução.
- ```!curl -o [nome_local] [url]```: O comando curl faz o download do conteúdo da URL e o salva com o nome especificado após o parâmetro -o (output). Isso permite que o código Python acesse esses arquivos localmente como se estivessem na mesma pasta do script.

### 🎯 Por que isso é necessário?
Uma simulação de foguete não depende apenas de fórmulas, mas de dados empíricos. O arquivo .eng, por exemplo, define por quantos segundos o foguete terá aceleração ativa, enquanto os arquivos de dragCurve (curva de arrasto) definem o quão "aerodinâmico" o foguete é em diferentes velocidades (Mach).

# 🛠️ Tratamento e Normalização de Dados
1. Sincronização e Escala Temporal
```
tempo_inicial = df['timestamp_ms'].iloc[0]
df['tempo_s'] = (df['timestamp_ms'] - tempo_inicial) / 1000
```
- Referência Zero: Sensores de telemetria costumam usar o tempo de "uptime" do processador. Subtraímos o tempo_inicial para que o gráfico comece exatamente em 0 segundos.
- Conversão de Unidade: Transformamos milissegundos (ms) em segundos (s) dividindo por 1000, facilitando a leitura de eventos de voo (ex: tempo de queima).

2. Inversão do Eixo Vertical (Referência NED)
```df['vel_vertical'] = -df['vel_d']```
- Entendendo o Sensor: Muitos sistemas de navegação utilizam o padrão NED (North-East-Down). Nesse sistema, a direção "Para Baixo" (Down) é positiva.
- Ajuste Intuitivo: Para que o gráfico de velocidade suba quando o foguete sobe, invertemos o sinal da coluna vel_d. Assim, valores positivos agora representam velocidade de ascensão.

### 📈 Impacto na Análise
Sem esses ajustes, os gráficos seriam de difícil leitura: o tempo estaria em valores na casa dos milhões e o foguete pareceria estar "subindo para baixo" nos gráficos de velocidade.

# 📊 Estrutura dos Gráficos

### 🔵 Gráfico 1: Altitude Barométrica (m) vs. Tempo (s)
- Mostra a trajetória vertical do foguete.
- Ponto Crítico: O ponto mais alto da curva representa o Apogeu. A inclinação da curva indica se a subida foi constante ou se houve variações de arrasto/empuxo.

### 🟠 Gráfico 2: Velocidade Vertical (m/s) vs. Tempo (s)
- Representa a taxa de variação da altitude.
- Ponto Crítico (Burnout): Onde a velocidade atinge seu valor máximo positivo (fim da queima do motor).
- Ponto Crítico (Apogeu): O instante exato em que a velocidade cruza o eixo zero (o foguete para de subir e começa a descer).

### 🔍 Detalhes Técnicos do Código
- ```figsize=(10, 12)```: Define um tamanho amplo para facilitar a leitura dos eixos.
- ```grid(True)```: Adiciona grades de referência, fundamentais para estimar valores exatos visualmente.
- ```legend()```: Identifica as séries de dados, essencial caso mais de um sensor fosse plotado simultaneamente.

# 🧠 Processamento Avançado e Detecção de Eventos

1. Suavização de Sinais (Filtro de Média Móvel)
```df['alt_suave'] = df['alt_bar'].rolling(window=20, center=True).mean()```
- O Problema: Sensores barométricos costumam apresentar "ruído" (oscilações rápidas causadas por vibração ou turbulência), o que gera uma linha serrilhada no gráfico.
- A Solução: Utilizamos a função ```.rolling(window=20)```. Ela calcula a média de cada ponto considerando os 20 pontos vizinhos. Isso "limpa" o gráfico, revelando a tendência real da trajetória sem as interferências do sensor.

2. Identificação Automática do Apogeu
```idx_apogeu = df['alt_suave'].idxmax()```
- O script busca o índice do maior valor na coluna de altitude. Com isso, extraímos com precisão o tempo exato e a altitude máxima alcançada, eliminando a necessidade de "olhar e chutar" o valor no gráfico.

3. Visualização Analítica com Anotações
- Dados Brutos vs. Tratados: O uso de ```alpha=0.5``` nos dados brutos (cinza) permite ver a incerteza do sensor ao fundo, enquanto a linha azul sólida destaca a trajetória filtrada.
- Anotação Dinâmica ```(plt.annotate)```: O código desenha automaticamente uma seta vermelha apontando para o ponto máximo, exibindo o valor da altitude e o tempo. Isso é fundamental para relatórios técnicos rápidos.

# 🧩 Módulos Principais do RocketPy

- ```Environment``` (Ambiente): Define as condições externas, como gravidade, densidade do ar, temperatura e ventos no local do lançamento.
- ```SolidMotor``` (Motor Sólido): Define a propulsão, usando os dados de empuxo (como o arquivo .eng baixado anteriormente) e a queima da massa do propelente.
- ```Rocket``` (Foguete): Define a estrutura física, como massa, dimensões, posição das aletas e centro de gravidade.
- ```Flight``` (Voo): É o módulo que "mistura" tudo. Ele integra o foguete, o motor e o ambiente para calcular a trajetória completa.

### 🖥️ Configuração de Alta Resolução
```
%config InlineBackend.figure_formats = ['svg']
%matplotlib inline
```
- Gráficos em Vetor (svg): Esta configuração garante que todos os gráficos gerados pelo RocketPy tenham qualidade profissional. Ao contrário do formato padrão (pixelado), o formato SVG permite que você dê zoom nos gráficos sem perder a nitidez, o que é essencial para analisar detalhes de estabilidade.
- Modo Inline: Garante que todas as visualizações e plots apareçam diretamente dentro do seu notebook, logo abaixo das células de código.

# 🌍 Modelagem do Ambiente (Standard Environment)
O ambiente foi configurado com base nas coordenadas de lançamento (Iacanga/SP), local onde ocorrem competições como o LASC (Latin American Space Challenge):
- Latitude/Longitude: Define a posição no globo para cálculos de gravidade e rotação da Terra.
- Elevação (423m): Define o nível do solo em relação ao nível do mar. Isso é vital, pois a pressão atmosférica na base da rampa de lançamento afeta o desempenho inicial do motor.
from rocketpy import Environment

# 1.Configurando o Ambiente
```
env = Environment(
    latitude=-21.8914, 
    longitude=-49.0225,
    elevation=423
)

# 2. Definindo a Data do Desafio (Passado)
# Data exata do PDF: 7 de Dezembro de 2025
env.set_date((2025, 12, 7, 12))

# 3. Definindo Atmosfera Padrão
# IMPORTANTE: Como a data é 2025, usamos StandardAtmosphere.
# O modo "Forecast/GFS" só funcionaria se a data fosse HOJE ou AMANHÃ.
env.set_atmospheric_model(type="standard_atmosphere")

# 4. Verificação Final
print("Ambiente configurado com sucesso para Iacanga/SP (LASC)!")
env.info()
```

### 🗓️ Referência Temporal e Modelo Atmosférico
```
env.set_date((2025, 12, 7, 12))
env.set_atmospheric_model(type="standard_atmosphere")
```
- Temporalidade: O simulador está configurado para o dia 07 de dezembro de 2025, às 12:00 UTC.
- Standard Atmosphere (ISA): Como estamos trabalhando com uma data específica (que pode ser passada ou futura), utilizamos o Modelo Atmosférico Padrão. Ele estima a pressão, temperatura e densidade do ar baseando-se em médias globais para cada altitude.
- Nota Técnica: Modelos de previsão real (como GFS/Ensemble) são usados apenas para lançamentos em tempo real (janelas de 24h a 48h).

### 📊 O que o comando env.info() revela?
Ao executar este comando, o RocketPy gera um relatório detalhado contendo:
- Gradiente Térmico: Como a temperatura cai conforme o foguete sobe.
- Densidade do Ar (ρ): Crucial para o cálculo da força de arrasto.
- Velocidade do Som: Necessária para calcular o Número de Mach, identificando se o foguete atingirá velocidades supersônicas.

# ☁️ Integração com Modelos Preditivos (Global Forecast System)
Diferente do modelo de atmosfera padrão, o RocketPy permite a conexão com o GFS (Global Forecast System) da NOAA. Este trecho de código ilustra como automatizar a coleta de dados meteorológicos para um lançamento programado para o futuro próximo.

### 🌐 Como funciona o GFS no RocketPy
O modelo GFS é uma base de dados dinâmica. Ele fornece previsões de vento, pressão e temperatura com base em coordenadas globais. No código:
```import datetime
tomorrow = datetime.date.today() + datetime.timedelta(days=1)
env.set_date(
    (tomorrow.year, tomorrow.month, tomorrow.day, 12)
)  # Hour given in UTC time
env.set_atmospheric_model(type="Forecast", file="GFS")
env.info()
```
- Automação de Data: O uso da biblioteca datetime permite que o script calcule automaticamente a data de "amanhã", garantindo que a consulta ao servidor meteorológico esteja dentro da janela de previsão disponível.
- Modelo "Forecast": Ao definir type="Forecast", o RocketPy realiza uma requisição via API para baixar os arquivos meteorológicos mais recentes para a latitude e longitude especificadas no Environment.

### ⚠️ Limitações e Casos de Uso
**Nota de Rodapé: No projeto atual (Nashira), este método não é utilizado porque o GFS não mantém registros históricos de longo prazo acessíveis via API de previsão.**

# 🚀 Definição do Sistema de Propulsão (SolidMotor)
O objeto SolidMotor utiliza os seguintes parâmetros técnicos:
```thrust_source```:	O arquivo .eng que contém a curva de empuxo em função do tempo.
```grain_number```: 	Quantidade de blocos de combustível (neste caso, 5 grãos).
```grain_density```:	Densidade do propelente sólido (1815kg/m3).
```nozzle_radius```: 	Raio da saída do bocal (33mm).
```throat_radius```:	Raio da garganta do bocal (11mm). Crucial para cálculos de pressão interna.
```dry_mass```:	Massa do motor após a queima total do combustível (1.815kg)

### ⚖️ Dinâmica de Massa e Inércia
Um dos diferenciais deste código é o tratamento da Variação do Centro de Massa (CM):
```grains_center_of_mass_position```: Define onde o combustível está localizado inicialmente.
```dry_inertia```: Define o momento de inércia do motor vazio.
```coordinate_system_orientation```: Configurado como ```"nozzle_to_combustion_chamber"```, definindo que a origem do sistema de coordenadas do motor está no bocal.

### 🔥Curva de Queima
Com o burn_time=3.9, o simulador sabe que terá quase 4 segundos de aceleração ativa. Durante esse tempo, o RocketPy recalculará a inércia do foguete a cada milissegundo, simulando a saída dos gases pelo bocal.

# 📊 Análise de Desempenho do Motor (Motor Info)
Ao executar ```Pro75M1670.info()```, o simulador processa a termodinâmica e a geometria dos grãos para validar o motor. Este relatório é essencial para conferir se o motor se comporta como esperado antes de montá-lo no foguete.

📈 Principais Gráficos Gerados
O relatório fornece quatro visualizações fundamentais:
- Curva de Empuxo (Thrust): Mostra a força (em Newtons) gerada ao longo dos 3.9 segundos de queima. É aqui que vemos o "pico" de aceleração inicial.
- Variação de Massa: Um gráfico decrescente que mostra quanta massa o foguete perde por segundo à medida que o propelente é expelido.
- Centro de Massa (CM): Mostra como o ponto de equilíbrio do motor se desloca durante a queima, o que afetará a estabilidade do voo.
- Inércia Transversal e Longitudinal: Cálculos de como a resistência à rotação muda enquanto o combustível desaparece.

### 🗝️ Variáveis de Saída Cruciais
O resumo textual exibirá valores que definem a categoria do foguete:
- Impulso Total: A "energia" total do motor (Área sob a curva de empuxo). No caso de um motor classe M, este valor é altíssimo, indicando um voo de alta performance.
- Impulso Específico​: Indica a eficiência do propelente. É o equivalente aos "km/l" de um carro; quanto maior, mais eficiente é a queima.
- Massa do Propelente: A diferença entre a massa total inicial e a ```dry_mass```.

# 🚀 Definição da Estrutura do Foguete (Rocket)

### 📐 Especificações Físicas
O objeto Rocket é construído com os seguintes parâmetros:
- Raio (radius): Definido como 0,0635 m (127/2000), o que corresponde a um diâmetro de 5 polegadas. Este valor é crucial para o cálculo da área frontal e, consequentemente, do arrasto aerodinâmico.
- Massa (mass): 14,426 kg. Esta é a massa do foguete sem o motor.
- Momento de Inércia (inertia): Define a resistência do foguete a mudanças em sua rotação nos eixos x,y e z. Essencial para calcular a estabilidade e a resposta a ventos laterais.
- Orientação do Sistema de Coordenadas: Configurado como ```"tail_to_nose"```, o que significa que a origem (0) está no centro de massa (ou ponto de referência escolhido) e os valores aumentam em direção à ponta (nariz) do foguete.

🌬️ Modelagem de Arrasto (Drag)
Diferente de simulações simplificadas, este modelo utiliza curvas reais de arrasto:
- ```power_off_drag```: Coeficiente de arrasto quando o motor já apagou.
- ```power_on_drag```: Coeficiente de arrasto durante a queima (geralmente menor devido ao efeito de base causado pela saída dos gases).
- O uso desses arquivos ```.csv``` permite que a simulação seja extremamente fiel ao comportamento real do foguete em diferentes velocidades (Mach).

🛤️ Botões de Trilho (Rail Buttons)
```
rail_buttons = calisto.set_rail_buttons(
    upper_button_position=0.0818,
    lower_button_position=-0.618,
    angular_position=45,
)
```
- Posicionamento: Define a distância entre os botões superior e inferior.
- Importância: O simulador usa esses dados para calcular o momento em que o foguete perde o contato com a rampa (saída de trilho). Se a velocidade na saída do trilho for muito baixa, o foguete pode não ter estabilidade inicial suficiente.

# ⚙️ Instalação do Motor
```calisto.add_motor(Pro75M1670, position=-1.255)```
- Posicionamento Relativo: O parâmetro position=-1.255 define onde o motor está em relação à origem do sistema de coordenadas do foguete.
- Equilíbrio de Massas: Ao adicionar o motor, o RocketPy recalcula automaticamente a massa total do conjunto e, mais importante, a nova posição do Centro de Massa (CG)

### 🛤️ Guia do Trilho (Rail Buttons)
```calisto.set_rail_buttons(0.2, -0.5)```
- Estabilidade Inicial: O posicionamento desses botões (neste caso em 0,2 e −0,5) é vital para o cálculo da Velocidade de Saída do Trilho.
- Regra de Ouro: Se o foguete sair do trilho muito devagar, as aletas não terão vento suficiente para gerar força restauradora, e o foguete pode "tombar" logo após a saída.

### 🛠️ Componentes Adicionados
1. Nariz (Nose Cone) - Perfil Von Kármán
- Tipo: von karman. Este perfil é matematicamente otimizado para minimizar o arrasto em velocidades transônicas e supersônicas.
- Dimensões: Comprimento de aproximadamente 55,8 cm, posicionado no topo da fuselagem (1,278 m).

2. Aletas Trapezoidais (Fin Set)
- Configuração: 4 aletas posicionadas na base do foguete.
- Perfil de Asa (Airfoil): Utiliza o arquivo NACA0012-radians.txt. Isso permite que o simulador calcule com precisão a sustentação e o arrasto das aletas conforme o ângulo de ataque muda.
- Cant Angle (0,5): As aletas possuem uma leve inclinação para induzir uma rotação (roll) no foguete durante o voo, o que ajuda a anular imperfeições de construção e manter a trajetória reta.

3. Cauda (Tail / Boattail)
- Função: Atua como uma transição cônica entre o corpo do foguete e o bocal do motor.
- Efeito Aerodinâmico: Reduz o "arrasto de base" (vácuo parcial que se forma atrás de superfícies retas), tornando o foguete muito mais eficiente em altas velocidades.

### ⚖️ Verificação de Estabilidade
Com a adição desses itens, o RocketPy agora pode calcular a Margem Estática.
- Regra de Ouro: Para um voo seguro, o Centro de Pressão (CP) deve estar sempre atrás do Centro de Massa (CG). A distância ideal costuma ser entre 1,5 e 2 vezes o diâmetro do foguete (calibres).

# 🪂 Sistema de Recuperação (Parachutes)
### 🛠️ Configuração de Estágio Duplo
1. Paraquedas Piloto (Drogue)
Gatilho ```(trigger="apogee")```: É acionado exatamente no ponto mais alto do voo (Apogeu).

2. Paraquedas Principal (Main)
Gatilho ```(trigger=800)```: Só é acionado quando o foguete desce até 800 metros de altitude.

# ⚖️ Análise de Estabilidade (Static Margin)
O comando ```calisto.plots.static_margin()``` gera um gráfico que monitora essa variação durante todo o tempo de voo.
O que o gráfico mostra:
- Margem Estática em Calibres: A unidade "calibre" representa o diâmetro do foguete (127 mm). Uma margem de 2.0 significa que o CP está 2 diâmetros atrás do CG.
- Estabilidade Positiva: O gráfico deve permanecer em valores positivos. Se a margem cair para zero ou valores negativos, o foguete se torna instável e pode começar a tombar ou girar fora de controle.
- Efeito da Velocidade (Mach): O gráfico revela como o CP se desloca quando o foguete se aproxima da velocidade do som.

### 🎯 Por que analisar a Margem Estática?
- Margem muito baixa (< 1.0): O foguete é "instável" e pode ser desviado por qualquer brisa.
- Margem muito alta (> 4.0): O foguete é "super-estável". Ele tende a se inclinar violentamente contra o vento (weathercocking), o que diminui o apogeu e pode ser perigoso.
- Ideal: Geralmente busca-se uma margem entre 1.5 e 2.5 calibres no momento da saída do trilho.

# 🚀 Execução da Simulação de Voo (Flight)
### ⚙️ Parâmetros de Lançamento
O comando define as condições de contorno da rampa:
- ```rail_length=5.2```: Comprimento do trilho da rampa (5,2 metros). É fundamental para determinar se o foguete sairá do trilho com velocidade suficiente para ser estável.
- ```inclination=85```: O foguete é lançado a 85° em relação ao horizonte (quase vertical). Essa inclinação de 5° evita que o foguete caia sobre a rampa em caso de falha.
- ```heading=0```: Aponta o foguete para o Norte geográfico.

### 📊 Análise de Resultados (all_info)
O comando simulacao.all_info() é o relatório mais completo da RocketPy, gerando dezenas de gráficos divididos em categorias:

1. Trajetória e Perfil de Altitude
- Exibe o Apogeu Teórico, permitindo comparar com o apogeu da telemetria real (o seu alt_apogeu calculado anteriormente).
- Traça a curva de subida e descida, mostrando os pontos exatos de abertura do drogue e do main.

2. Velocidade e Aceleração
- Monitora o Número de Mach. Se o foguete ultrapassar Mach 1, ele entra em regime supersônico, onde o arrasto aumenta drasticamente.
- Verifica a Aceleração Máxima, importante para saber se os componentes internos (eletrônica) suportarão a força G.

3. Estabilidade e Atitude
- Plota o ângulo de ataque e o amortecimento de oscilações.
- Calcula a Velocidade de Saída do Trilho: Essencial para a segurança. Valores abaixo de 15-20 m/s costumam ser considerados perigosos para foguetes desse porte.

# ⚖️ Análise de Sensibilidade: Apogeu vs. Massa
```apogee_by_mass(flight=simulacao, min_mass=5, max_mass=20, points=10)```
O gráfico gerado mostra como o apogeu mudaria se o foguete fosse mais leve ou mais pesado:
Utilzado para termos de base de comparação o voo do foguete Nashira prático e do Calisto teórico

# 🚀 Resumo Geral do Projeto
Este repositório documenta o ciclo completo de engenharia de um foguete de sondagem de alta potência:
- Ingestão de Dados: Leitura e tratamento de telemetria bruta via Pandas.
- Processamento de Sinais: Suavização de ruído barométrico (Média Móvel).
- Modelagem Física: Configuração completa de Ambiente, Motor, Estrutura e Recuperação no RocketPy.
- Estudos de Engenharia: Análises de estabilidade estática, sensibilidade de massa e margem estática dinâmica.
- Exportação e Logística: Geração de arquivos KML para recuperação em campo e CSV para relatórios externos.
- Validação Final: Comparação direta "Teoria vs. Realidade" com cálculo de erro.

---
*Desenvolvido por João Vitor Faria Silva para o Processo Seletivo EPTA (Payload) 2025.*
