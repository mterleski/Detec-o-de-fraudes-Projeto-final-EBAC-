# Detecção de Fraudes em Transações com Cartão de Crédito usando XGBoost

## 📌 Visão Geral

Fraudes em cartões de crédito geram prejuízos bilionários todos os anos para bancos, empresas e consumidores. Identificar transações suspeitas rapidamente é um desafio complexo, principalmente porque os casos de fraude representam apenas uma pequena fração de todas as operações realizadas.

Neste projeto, desenvolvi um modelo de **Machine Learning** capaz de identificar transações potencialmente fraudulentas em um cenário de **extremo desbalanceamento de dados**, onde apenas **0,17% das transações são fraudes**.

A solução foi construída utilizando **XGBoost**, um dos algoritmos mais utilizados em problemas de classificação tabular, com foco especial em detectar o maior número possível de fraudes sem gerar um volume excessivo de alertas incorretos.

---

## 💼 Contexto de Negócio

Imagine uma instituição financeira processando milhões de transações diariamente.

Se um sistema simplesmente classificasse todas as operações como legítimas, ele alcançaria mais de 99% de acurácia, mas deixaria passar praticamente todas as fraudes.

Por isso, em problemas como este, o objetivo não é apenas acertar a maioria dos casos, mas sim identificar eventos raros e de alto impacto financeiro.

O desafio consiste em encontrar um equilíbrio entre:

* Detectar o maior número possível de fraudes.
* Evitar bloquear ou sinalizar transações legítimas de clientes.

---

## 🎯 Objetivo do Projeto

O projeto teve como objetivos:

* Desenvolver um modelo de classificação binária para identificar fraudes.
* Tratar adequadamente um conjunto de dados altamente desbalanceado.
* Aplicar técnicas de engenharia de atributos para aumentar o poder preditivo do modelo.
* Avaliar o desempenho utilizando métricas adequadas para problemas de fraude.
* Demonstrar como técnicas de Machine Learning podem apoiar processos de prevenção a perdas financeiras.

---

## 📊 Conjunto de Dados

Os dados foram disponibilizados por uma instituição financeira e representam transações realizadas por portadores de cartões europeus.

### Visão Geral

* **Total de registros:** 284.807 transações
* **Total de variáveis:** 31
* **Variável alvo:** `Class`

  * `0` = transação legítima
  * `1` = fraude

### Estrutura das Variáveis

Para preservar a privacidade dos clientes, a maior parte das informações originais foi anonimizada utilizando **PCA (Principal Component Analysis)**.

* `V1` a `V28`: componentes numéricos resultantes do PCA.
* `Time`: tempo em segundos desde a primeira transação registrada.
* `Amount`: valor monetário da transação.

### Desbalanceamento das Classes

| Classe   | Quantidade | Percentual |
| -------- | ---------- | ---------- |
| Legítima | 284.315    | 99,83%     |
| Fraude   | 492        | 0,17%      |

Esse cenário reproduz um problema comum do mundo real: as fraudes são raras, mas extremamente importantes de detectar.

---

## 🧹 Preparação dos Dados

### Limpeza e Padronização

A primeira etapa consistiu na análise da qualidade dos dados.

* Verificação de valores ausentes.
* Verificação de inconsistências.
* Padronização das variáveis `Time` e `Amount`, que não haviam passado pelo processo de PCA.

Nenhum valor nulo foi identificado no conjunto analisado.

### Engenharia de Atributos

Buscando extrair mais informação dos dados disponíveis, foram criadas novas representações para algumas variáveis.

#### Tempo Cíclico

O horário de uma transação pode influenciar a probabilidade de fraude.

Por exemplo, uma operação realizada às 23h59 está muito mais próxima de uma realizada às 00h01 do que de uma realizada às 12h.

Para capturar esse comportamento periódico, foram aplicadas transformações seno e cosseno sobre a variável temporal.

#### Transformação Logarítmica

A variável de valor monetário apresentava forte assimetria devido à presença de transações muito altas.

Foi aplicada uma transformação logarítmica para reduzir a influência de outliers e facilitar o aprendizado do modelo.

### Divisão dos Dados

O conjunto foi dividido em três etapas:

* **Treino:** aprendizado do modelo.
* **Validação:** ajuste de hiperparâmetros.
* **Teste:** avaliação final em dados nunca vistos.

### Tratamento do Desbalanceamento

Em vez de criar observações artificiais, foi utilizada uma abordagem nativa do XGBoost por meio do parâmetro `scale_pos_weight`.

Essa estratégia aumenta a penalização dos erros cometidos na classe de fraude, incentivando o modelo a prestar mais atenção nos casos raros.

---

## 🔎 Análise Exploratória

Devido ao processo de anonimização por PCA, a interpretação individual das variáveis é limitada.

Por esse motivo, a análise exploratória concentrou-se em:

* Distribuição das classes.
* Comportamento das variáveis `Time` e `Amount`.
* Verificação de qualidade dos dados.

A exploração inicial confirmou que o principal desafio do projeto seria o forte desbalanceamento entre fraudes e transações legítimas.

---

## 🤖 Modelagem

### Algoritmo Utilizado

Foi utilizado o algoritmo **XGBoost (Extreme Gradient Boosting)**, amplamente reconhecido por seu desempenho em competições de ciência de dados e aplicações empresariais.

Principais vantagens:

* Excelente desempenho em dados tabulares.
* Robustez diante de relações complexas entre variáveis.
* Capacidade de lidar com classes desbalanceadas.
* Boa capacidade de generalização.

### Otimização do Modelo

Os hiperparâmetros foram ajustados utilizando **GridSearchCV**, permitindo testar diferentes combinações de configurações.

Entre os parâmetros avaliados estavam:

* Profundidade máxima das árvores.
* Taxa de aprendizado.
* Quantidade de estimadores.
* Configurações de regularização.

---

## 📈 Métricas de Avaliação

Em problemas de fraude, a acurácia isoladamente pode ser enganosa.

Por isso, foram utilizadas métricas mais adequadas ao contexto:

### Recall (Métrica Principal)

Indica quantas fraudes reais foram identificadas pelo modelo.

Quanto maior o Recall, menor a quantidade de fraudes que passam despercebidas.

### Precision

Indica quantos alertas de fraude emitidos pelo modelo realmente eram fraudes.

### F1-Score

Combina Recall e Precision em uma única métrica.

### ROC AUC

Mede a capacidade geral de separação entre transações legítimas e fraudulentas.

### Curva Precision-Recall

Particularmente importante em conjuntos altamente desbalanceados.

### Matriz de Confusão

Permite visualizar detalhadamente os acertos e erros do modelo.

---

## ✅ Resultados

### Desempenho no Conjunto de Teste

| Métrica  | Resultado |
| -------- | --------- |
| ROC AUC  | 0,9785    |
| Recall   | 0,83      |
| Precisão | 0,90      |
| F1-Score | 0,86      |

### Matriz de Confusão

Em um conjunto de **56.962 transações**:

* Verdadeiros Negativos: 56.855
* Falsos Positivos: 9
* Falsos Negativos: 17
* Verdadeiros Positivos: 81

### Interpretação dos Resultados

Em termos práticos:

* O modelo identificou aproximadamente **83% das fraudes existentes**.
* Apenas **9 transações legítimas** foram classificadas incorretamente como fraude.
* A maioria das operações legítimas passou pelo sistema sem qualquer impacto ao cliente.

Esses resultados demonstram uma solução equilibrada entre segurança e experiência do usuário.

---

## 🏁 Conclusão

Este projeto demonstra a aplicação de técnicas de Machine Learning em um problema real de alto impacto financeiro.

Mesmo diante de um cenário extremamente desbalanceado, a combinação de **engenharia de atributos**, **tratamento adequado das classes** e **XGBoost** permitiu construir um modelo com excelente capacidade de detecção.

Além dos resultados quantitativos, o projeto reforça a importância de selecionar métricas alinhadas ao contexto de negócio, especialmente em aplicações onde o custo dos erros não é uniforme.

---

## 🛠️ Tecnologias Utilizadas

* Python
* Pandas
* NumPy
* Scikit-learn
* XGBoost
* Matplotlib
* Seaborn

---

## 🚀 Principais Aprendizados

Durante o desenvolvimento deste projeto, foram explorados conceitos importantes de Ciência de Dados e Machine Learning, incluindo:

* Tratamento de bases altamente desbalanceadas.
* Engenharia de atributos para variáveis temporais.
* Ajuste de hiperparâmetros com validação cruzada.
* Avaliação de modelos utilizando métricas orientadas ao negócio.
* Construção de soluções voltadas para problemas reais de detecção de anomalias e prevenção a fraudes.

---

## 📌 Observações Finais

Este projeto tem caráter acadêmico e utiliza dados anonimizados. As variáveis transformadas via PCA não possuem interpretação direta, respeitando a privacidade dos clientes da instituição financeira.
