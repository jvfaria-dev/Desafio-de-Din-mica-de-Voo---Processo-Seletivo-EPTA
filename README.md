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
Python
- ```df = pd.read_csv('nashira_telemetry.csv', decimal=',')```

### 1. Tratamento de Dados Reais
Os dados brutos do sensor (`nashira_telemetry.csv`) apresentavam ruído e formatação não padrão (uso de vírgula decimal).
* **Limpeza:** Conversão de tipos e ajuste de escalas (ms para segundos).
* **Suavização:** Aplicação de filtro de média móvel (*rolling mean*) para eliminar ruídos do barômetro e identificar o apogeu com precisão.

### 2. Configuração da Simulação
Devido à indisponibilidade dos dados construtivos exatos do foguete *Nashira* (curva de empuxo e aerodinâmica), utilizou-se o foguete de referência **Calisto** (classe M) para a simulação, conforme permitido no edital.
* **Ambiente:** Coordenadas de **Iacanga/SP** (local real da competição LASC) e *Standard Atmosphere* (Atmosfera Padrão).
* **Veículo:** Parâmetros de massa, inércia e arrasto do foguete Calisto.

### 3. Análise Comparativa (Resultados)
Como os dados de engenharia do Nashira não estavam disponíveis, utilizou-se o foguete de referência **Calisto** (RocketPy Standard) para a simulação inicial.

| Parâmetro | Telemetria Real (CSV) | Simulação Padrão (Calisto) | Discrepância |
| :--- | :--- | :--- | :--- |
| **Apogeu (AGL)** | **283 m** | **3.624 m** | +3.341 m |
| **Tempo de Voo** | ~3 min | ~5 min | +2 min |

* **Local do Voo:** Iacanga/SP (LASC)
* **Altitude do Solo:** 463 m (Barométrico)
* **Apogeu Atingido:** 746 m (Barométrico)
* **Delta-H (Altura Real do Voo):** **283 metros**
---

## 💡 Conclusão Técnica

A discrepância massiva indica que o foguete simulado (Calisto, Motor M1670) possui energia cinética aproximadamente **12x superior** à demandada pelo perfil de voo registrado no CSV.

**Veredito:** O código foi executado com sucesso, cumprindo o requisito de simular um voo teórico e compará-lo com dados reais, evidenciando a importância da modelagem correta dos parâmetros de entrada.

---

## 📂 Estrutura do Repositório

* `Desafio_EPTA.ipynb`: O código fonte completo e documentado (Jupyter Notebook).
* `nashira_telemetry.csv`: Arquivo de dados brutos da missão real.
* `comparacao_voo.png`: Gráfico gerado da análise comparativa.
* `README.md`: Documentação do projeto.

## 🚀 Como Executar o Projeto

Para reproduzir os resultados em sua máquina local:

1.  **Clone o repositório:**
    ```bash
    git clone [https://github.com/SEU_USUARIO/NOME_DO_REPO.git](https://github.com/SEU_USUARIO/NOME_DO_REPO.git)
    cd NOME_DO_REPO
    ```

2.  **Instale as dependências necessárias:**
    ```bash
    pip install rocketpy pandas matplotlib scipy netCDF4
    ```

3.  **Execute o Notebook:**
    Abra o arquivo `Desafio_EPTA.ipynb` no Jupyter Notebook, VS Code ou Google Colab e execute as células sequencialmente.

---
*Desenvolvido por João Vitor Faria Silva para o Processo Seletivo EPTA 2025.*
