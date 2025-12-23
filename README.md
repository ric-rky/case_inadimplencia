# Case Técnico: Previsão de Inadimplência

Solução desenvolvida para o case técnico de Cientista de Dados Júnior, com foco em estimar a probabilidade de inadimplência por cobrança, integrando dados cadastrais, mensais e históricos de pagamentos.

---

## Objetivo

Construir um modelo de classificação binária para prever o risco de atraso igual ou superior a 5 dias.

* **Variável Alvo (Target)**:
    * 1 (Inadimplente): se DIAS_ATRASO >= 5
    * 0 (Adimplente): caso contrário
* **Entregável**: Arquivo .csv contendo as probabilidades previstas para o conjunto de teste.

---

## Estrutura do Projeto

* `case_inadimplencia`: Análise exploratória (EDA), pré-processamento e modelagem.
* `submissao_case.csv`: Arquivo final com as predições geradas.
* `requirements.txt`: Dependências para reprodução do ambiente.
* `README.md`: Documentação técnica.

---

## Metodologia

### Consolidação dos Dados
* Integração das bases de dados via operação de LEFT JOIN.
* A tabela de pagamentos foi definida como a tabela fato, preservando a granularidade correta por transação de cobrança.

### Tratamento e Preparação (Data Prep)
* **Valores Ausentes**:
    * Variáveis numéricas: Imputação pela mediana para mitigar o impacto de outliers.
    * Variáveis categóricas: Preenchimento com o rótulo "NA".
* **Seleção de Atributos**: Exclusão da variável FLAG_PF devido à baixa variância.
* **Feature Engineering**: Criação de novas variáveis para aumentar o poder preditivo:
    * `PRAZO_EMISSAO_VENC`: Intervalo em dias entre a data de emissão e o vencimento.
    * `SAFRA_ANO` / `SAFRA_MES`: Decomposição temporal para captura de sazonalidade.
    * `TAXA_RELATIVA`: Razão entre taxas aplicadas e valores contratuais.

### Pipeline de Modelagem
Implementação via `ColumnTransformer` para automação dos processos:
1. `StandardScaler`: Normalização de variáveis numéricas.
2. `OneHotEncoder`: Codificação de variáveis categóricas com tratamento de categorias desconhecidas (`handle_unknown="ignore"`).

---

## Performance e Resultados

Comparação de desempenho obtida durante a fase de validação:

| Modelo | AUC-ROC | Log Loss |
| :--- | :---: | :---: |
| Regressão Logística | 0.81 | 0.53 |
| XGBoost | 0.94 | 0.26 |
| **Random Forest (Otimizado)** | **0.96** | **0.12** |

### Modelo Final
O algoritmo Random Forest foi selecionado como o modelo final devido à superioridade nas métricas de AUC-ROC e Log Loss. O modelo demonstrou alta capacidade de generalização e estabilidade através de validação cruzada, sendo refinado via `RandomizedSearchCV`.

---

## Instruções de Execução

1. Clonar o repositório.
2. Alocar os arquivos brutos no diretório `data/`.
3. Instalar as dependências do projeto:
    ```bash
    pip install -r requirements.txt
    ```
4. Executar o notebook `case_datarisk_notebook.ipynb` para processamento completo e geração do arquivo de submissão.

---

## Formato da Submissão
O arquivo `submissao_case.csv` apresenta as colunas conforme especificação técnica:
* ID_CLIENTE
* SAFRA_REF
* PROBABILIDADE_INADIMPLENCIA
