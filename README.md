## Como Reproduzir os Experimentos

### Pré-requisitos e Instalação

1. Clone este repositório:
   ```bash
   git clone [https://github.com/costhaisme/tcc-tesouro-ipca-deep-learning.git](https://github.com/costhaisme/tcc-tesouro-ipca-deep-learning.git)
   cd tcc-tesouro-ipca-deep-learning

2. Instale as dependências (recomendado Python 3.10+ ou execução direta no Google Colab):
   pip install numpy pandas scikit-learn matplotlib tensorflow==2.15.0 xlrd openpyxl

3. Certifique-se de que as planilhas históricas oficiais do Tesouro Direto (NTN-B_Principal_2020.xls a NTN-B_Principal_2026.xls, extraídas do portal do Tesouro Transparente / Tesouro Direto) estejam no diretório de execução e execute o notebook principal:
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
