# Detecção de Fraudes em Transações com Cartão de Crédito usando XGBoost

## 📌 Visão Geral

Este projeto aborda o problema crítico da **detecção de fraudes em transações de cartão de crédito** em um cenário de **extremo desbalanceamento de classes**, onde apenas **0,17%** das transações são fraudulentas. O objetivo central é construir um modelo robusto de *Machine Learning* capaz de identificar atividades suspeitas com alta confiabilidade, protegendo tanto clientes quanto instituições financeiras.

A solução utiliza o algoritmo **XGBoost**, com foco na **maximização do Recall**, garantindo que o maior número possível de fraudes seja detectado, sem gerar excesso de falsos alarmes que prejudiquem a experiência do usuário.

---

## 🎯 Objetivo do Projeto

* Desenvolver um modelo de classificação binária para detecção de fraudes.
* Lidar adequadamente com dados altamente desbalanceados.
* Aplicar **engenharia de atributos avançada** para melhorar a capacidade preditiva.
* Avaliar o modelo com métricas apropriadas ao contexto de fraude.

---

## 📊 Conjunto de Dados

Os dados foram fornecidos por uma instituição financeira e correspondem a transações realizadas por portadores de cartões europeus.

* **Total de registros:** 284.807 transações
* **Total de variáveis:** 31
* **Variável alvo:** `Class` (0 = legítima, 1 = fraude)

### Estrutura das Variáveis

* `V1` a `V28`: Componentes numéricos resultantes de **PCA**, aplicados para preservar a confidencialidade dos dados.
* `Time`: Tempo em segundos desde a primeira transação registrada.
* `Amount`: Valor monetário da transação.

### Desbalanceamento

* Transações legítimas: 284.315 (99,83%)
* Transações fraudulentas: 492 (0,17%)

Esse desequilíbrio extremo exige estratégias específicas de modelagem e avaliação.

---

## 🧹 Tratamento e Preparação dos Dados

### Limpeza e Padronização

* Verificação de valores ausentes (nenhum valor nulo encontrado).
* Padronização das variáveis `Time` e `Amount`, que não passaram pelo PCA original.

### Engenharia de Atributos

* **Transformação Cíclica do Tempo:**

  * Aplicação de seno e cosseno sobre a variável `Time` para capturar padrões periódicos diários.
* **Transformação Logarítmica do Valor:**

  * Aplicação de log na variável `Amount` para reduzir assimetria e impacto de outliers.

### Divisão dos Dados

* **Treino:** aprendizado do modelo
* **Validação:** ajuste de hiperparâmetros via `GridSearchCV`
* **Teste:** avaliação final e imparcial

### Balanceamento

* Utilização do parâmetro `scale_pos_weight` do XGBoost para penalizar a classe minoritária.
* Não foi utilizado *oversampling* (ex: SMOTE), preservando a distribuição original dos dados.

---

## 🔎 Análise Exploratória

Devido ao uso prévio de PCA, a análise exploratória foi limitada à:

* Distribuição da variável alvo (`Class`).
* Avaliação das variáveis `Time` e `Amount`.

A ausência de valores ausentes e a natureza numérica das variáveis permitiram direcionar o foco para engenharia de atributos e modelagem.

---

## 🤖 Modelagem

### Algoritmo Utilizado

* **XGBoost (Extreme Gradient Boosting)**

  * Alta performance em dados tabulares.
  * Suporte nativo a dados desbalanceados.
  * Forte capacidade de generalização.

### Otimização

* Ajuste de hiperparâmetros via **GridSearchCV**.
* Busca por parâmetros como:

  * Profundidade das árvores
  * Taxa de aprendizado
  * Número de estimadores

---

## 📈 Métricas de Avaliação

Em detecção de fraudes, a acurácia isolada não é informativa. As métricas priorizadas foram:

* **Recall (Sensibilidade)** – métrica principal
* **Precision (Precisão)**
* **F1-Score**
* **ROC AUC**
* **Curva Precision-Recall** (mais adequada para classes raras)
* **Matriz de Confusão**

---

## ✅ Resultados

### Desempenho no Conjunto de Teste

* **ROC AUC:** 0,9785
* **Recall (fraudes):** 0,83
* **Precisão (fraudes):** 0,90
* **F1-Score:** 0,86

### Matriz de Confusão (56.962 transações)

* Verdadeiros Negativos: 56.855
* Falsos Positivos: 9
* Falsos Negativos: 17
* Verdadeiros Positivos: 81

Os resultados demonstram excelente capacidade de detecção com impacto mínimo sobre clientes legítimos.

---

## 🏁 Conclusão

O projeto atingiu plenamente seu objetivo ao entregar um modelo altamente confiável para detecção de fraudes. A combinação de **engenharia de atributos bem fundamentada** com a robustez do **XGBoost** permitiu maximizar o Recall sem comprometer a Precisão, resultando em uma solução eficaz e aplicável em cenários reais de instituições financeiras.

---

## 🛠️ Tecnologias Utilizadas

* Python
* Pandas / NumPy
* Scikit-learn
* XGBoost
* Matplotlib / Seaborn

---

## 📌 Observações Finais

Este projeto tem caráter acadêmico e utiliza dados anonimizados. As variáveis transformadas via PCA não possuem interpretação direta, respeitando a privacidade dos clientes da instituição financeira.
