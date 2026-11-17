# Deep Learning na Marcação a Mercado: Uma Abordagem de Validação em Janela Deslizante para Títulos do Tesouro Direto

[![Python 3.10](https://img.shields.io/badge/Python-3.10-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![TensorFlow 2.15](https://img.shields.io/badge/TensorFlow-2.15-FF6F00?logo=tensorflow&logoColor=white)](https://www.tensorflow.org/)
[![Keras](https://img.shields.io/badge/Keras-2.15-D00000?logo=keras&logoColor=white)](https://keras.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

Repositório oficial contendo os algoritmos, o *pipeline* de engenharia de dados e os scripts de simulação experimental desenvolvidos para o Trabalho de Conclusão de Curso (TCC) apresentado à **Faculdade de Sistemas de Informação** da **Universidade Federal do Sul e Sudeste do Pará (UNIFESSPA)**.

* **Autor:** Youri Matheus Costhaisme
* **Orientador:** Prof. Dr. Adam Dreyton Ferreira dos Santos
* **Local e Ano:** Marabá - PA, 2026

---

## Visão Geral da Pesquisa

Esta pesquisa investiga, desenvolve e valida um arcabouço computacional de Aprendizado Profundo (*Deep Learning*) integrado a filtros estatísticos descritivos para automatizar estratégias de **marcação a mercado** no título público **Tesouro IPCA+ 2035 (NTN-B Principal)** entre os anos de **2020 e 2026** (625 pregões).

Para superar as falhas recorrentes em estudos que utilizam particionamento estático (*Hold-out*), este projeto implementa:
1. **Isolamento Estrito contra Vazamento de Dados (*Data Leakage*):** Padronização estatística via *Z-Score* ajustada exclusivamente dentro da janela ativa de treinamento.
2. **Validação em Janela Deslizante (*Walk-Forward Validation*):** Treinamento móvel em blocos de 500 pregões (~2 anos úteis), *lookback* de 20 pregões e passo de teste fora da amostra (*out-of-sample*) de 20 pregões, com expurgo explícito da sessão computacional (`clear_session()`) a cada iteração para mitigar a deriva de conceito (*concept drift*).
3. **Benchmarking de Paradigmas Neurais:** Confronto direto entre redes recorrentes sequenciais (**LSTM** e **GRU**) e modelos baseados em mecanismos de autoatenção (**Transformers**).
4. **Regra do Sinal Combinado ($Q_1$ e $Q_3$):** Filtro qualitativo sobre subjanela móvel de 60 pregões que exige dupla confirmação (projeção neural + posição estatística interquartil) para inibir o excesso de negociações (*overtrading*).
5. **Auditoria Fiduciária (*Backtesting*):** Simulação financeira operando sobre o Preço Unitário (PU) real do título com capital inicial de R$ 10.000,00, contrastando a gestão ativa guiada por IA frente à estratégia passiva (*Buy and Hold*).

---

## Arquitetura do Pipeline Experimental

O ecossistema computacional foi modularizado em seis etapas sequenciais determinísticas (`seed = 42`):

1. **Ingestão e Tratamento de Dados (ETL):** Unificação automatizada das planilhas históricas legadas (`.xls`) da Secretaria do Tesouro Nacional (2020–2026), filtragem da aba `NTN-B Princ 150535` e tratamento de feriados bancários via propagação do último valor válido (*Forward Fill*).
2. **Pré-processamento e Escalonamento Dinâmico:** Cálculo do *Z-Score* restrito ao conjunto de treinamento e estruturação em tensores tridimensionais `(amostras, 20, 1)`.
3. **Modelagem de Aprendizado Profundo:**
   * **LSTM:** 50 unidades recorrentes, regularização $L_2$ ($\lambda = 0,01$), *Dropout* (30%), camada densa ReLU (25 neurônios) e saída linear.
   * **GRU:** 50 unidades recorrentes, regularização $L_2$ ($\lambda = 0,01$), *Dropout* (30%), camada densa ReLU (25 neurônios) e saída linear.
   * **Transformer:** 2 blocos codificadores com *Multi-Head Self-Attention* (4 cabeças, dimensão 64), conexões residuais, *Layer Normalization*, *GlobalAveragePooling1D* e camadas densas.
4. **Motor Walk-Forward:** Execução cronológica iterativa com re-treinamento contínuo (20 épocas, *batch size* = 32, otimizador *Adam* com taxa de aprendizado de $0,001$ e função de perda MSE).
5. **Avaliador Estatístico e Sinal Combinado:** Cálculo de RMSE, MAE e MAPE na escala original revertida e geração de sinais operacionais (`COMPRA EXCELENTE`, `VENDA` e `NEUTRO / AGUARDAR`).
6. **Simulador Financeiro (*Backtesting*):** Conversão dos alertas táticos em operações fiduciárias pelo Preço Unitário de compra da NTN-B Principal 2035.

---

## Principais Resultados Empíricos

### 1. Benchmarking Arquitetural (Validação em Janela Deslizante)

| Arquitetura Avaliada | Paradigma Computacional | Estrutura Principal | MAPE Observado (%) | Classificação |
| :--- | :--- | :--- | :---: | :---: |
| **LSTM** | Recorrente Sequencial (3 portões + Célula $C_t$) | 50 unid. + $L_2(0,01)$ + *Dropout* (30%) | **2,03%** | 1º Lugar (Superior) |
| **GRU** | Recorrente Simplificado (2 portões lógicos) | 50 unid. + $L_2(0,01)$ + *Dropout* (30%) | **2,06%** | 2º Lugar (Competitivo) |
| **Transformers** | Não Sequencial (*Multi-Head Self-Attention*) | 2 blocos, 4 cabeças (dim. 64) + *GlobalAvgPool1D* | **23,36%** | 3º Lugar (Degradação) |

### 2. Simulação Financeira Fiduciária (*Backtesting* — Aporte Inicial: R$ 10.000,00)

| Estratégia Operacional Simulada | Capital Inicial (R$) | Capital Final (R$) | Resultado Líquido (R$) | ROI (%) |
| :--- | :---: | :---: | :---: | :---: |
| **Alocação Passiva (*Buy and Hold*)** | R$ 10.000,00 | R$ 10.344,00 | +R$ 344,00 | **+3,44%** |
| **Gestão Ativa Guiada por IA (Sinal Combinado)** | R$ 10.000,00 | R$ 9.559,00 | -R$ 441,00 | **-4,41%** |

> **Conclusão Científica:** Embora as redes recorrentes (LSTM e GRU) alcancem elevada precisão matemática na projeção das taxas de juros (MAPE ~2%), a exposição à elevada duração (*duration*) de títulos longos durante ciclos prolongados de abertura da curva DI exige o acoplamento matemático do Fator de Convexidade, *Position Sizing* fracionado e travas dinâmicas de *Stop-Loss* para proteger o Preço Unitário em carteira.

---

## Como Reproduzir os Experimentos

### Pré-requisitos e Instalação

1. Clone este repositório:
   ```bash

2. Instale as dependências (recomendado Python 3.10+ ou execução direta no Google Colab):
   pip install numpy pandas scikit-learn matplotlib tensorflow==2.15.0 xlrd openpyxl

4. Certifique-se de que as planilhas históricas oficiais do Tesouro Direto (NTN-B_Principal_2020.xls a NTN-B_Principal_2026.xls, extraídas do portal do Tesouro Transparente / Tesouro Direto) estejam no diretório de execução e execute o notebook principal:
   jupyter notebook Notebook_Marcacao_a_Mercado.ipynb

## Citação Acadêmica

Caso utilize este código ou metodologia em sua pesquisa, cite como:
@thesis{costhaisme2026deeplearning,
  author       = {Costhaisme, Youri Matheus},
  title        = {Deep Learning na Marcação a Mercado: Uma Abordagem de Validação em Janela Deslizante para Títulos do Tesouro Direto},
  school       = {Universidade Federal do Sul e Sudeste do Pará (UNIFESSPA)},
  year         = {2026},
  address      = {Marabá, PA},
  type         = {Trabalho de Conclusão de Curso (Bacharelado em Sistemas de Informação)},
  note         = {Orientador: Prof. Dr. Adam Dreyton Ferreira dos Santos}
}
   git clone [https://github.com/costhaisme/tcc-tesouro-ipca-deep-learning.git](https://github.com/costhaisme/tcc-tesouro-ipca-deep-learning.git)
   cd tcc-tesouro-ipca-deep-learning
