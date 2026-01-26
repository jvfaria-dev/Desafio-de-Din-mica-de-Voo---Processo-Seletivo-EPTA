# 🚀 Desafio de Dinâmica de Voo: Projeto Nashira

![Status](https://img.shields.io/badge/Status-Concluído-success)
![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Library](https://img.shields.io/badge/Lib-RocketPy-orange)

Este repositório contém a solução técnica para o **Desafio 2: Dinâmica de Voo do Nashira**, desenvolvido como parte do Processo Seletivo da **EPTA (Equipe de Propulsão e Tecnologia Aeroespacial)** da Universidade Federal de Uberlândia (UFU).

## 📄 Resumo dos Dados
A análise baseou-se estritamente no arquivo de log `nashira_telemetry.csv`.

## 📄 Sobre o Projeto

O objetivo principal deste desafio foi realizar uma **Análise Comparativa de Voo**, confrontando dois cenários distintos:
1.  **Cenário Real:** Dados de telemetria obtidos durante o voo do foguete *Nashira* na competição LASC (Latin America Space Challenge).
2.  **Cenário Teórico:** Simulação computacional de trajetória realizada com a biblioteca *RocketPy*.

## 🛠️ Tecnologias e Ferramentas

O projeto foi desenvolvido inteiramente em **Python** utilizando Jupyter Notebooks. As principais bibliotecas empregadas foram:

* **[RocketPy](https://github.com/RocketPy-Team/RocketPy):** Para simulação de voo 6-DOF, modelagem atmosférica e balística.
* **Pandas:** Para ingestão, limpeza e manipulação dos dados brutos de telemetria (.csv).
* **Matplotlib:** Para visualização de dados e plotagem dos perfis de voo (Altitude x Tempo).
* **NumPy:** Para cálculos vetoriais e tratamento numérico.

## 📊 Metodologia

A solução foi estruturada em três etapas lógicas:

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
