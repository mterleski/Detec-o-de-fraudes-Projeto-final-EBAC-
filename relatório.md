Detecção de Fraudes em Transações com Cartão de Crédito utilizando XGBoost

1. Introdução

A segurança e a integridade das operações financeiras dependem da identificação ágil e precisa de atividades suspeitas, especialmente em transações realizadas com cartões de crédito. Nesse contexto, a detecção de fraudes representa um desafio relevante, sobretudo em cenários de extremo desequilíbrio de dados, nos quais as ocorrências fraudulentas correspondem a apenas 0,17% do volume total de transações. A relevância deste problema está diretamente associada à necessidade de proteger usuários e instituições financeiras contra prejuízos econômicos, ao mesmo tempo em que se busca preservar uma experiência satisfatória para clientes legítimos.

O objetivo principal deste projeto é desenvolver um modelo de aprendizado de máquina baseado no algoritmo XGBoost, com foco na maximização da taxa de detecção de fraudes (Recall). A abordagem proposta utiliza técnicas de engenharia de atributos, como transformações cíclicas da variável temporal e transformações logarítmicas nos valores das transações, com o intuito de melhorar a capacidade preditiva do modelo. Busca-se, assim, uma solução eficaz que aumente a segurança das transações financeiras sem gerar um número excessivo de alarmes falsos.

2. Coleta de Dados

Os dados utilizados neste projeto foram fornecidos por uma instituição financeira e correspondem a transações realizadas por portadores de cartões europeus. O conjunto de dados é composto por 284.807 transações, organizadas em 31 variáveis numéricas.

A maior parte das variáveis (V1 a V28) resulta de um processo de Análise de Componentes Principais (PCA), aplicado previamente pela instituição com o objetivo de preservar a confidencialidade das informações sensíveis dos clientes. Essa técnica mantém apenas as variações estatísticas mais relevantes, impossibilitando a interpretação direta do significado original dessas variáveis.

3. Estrutura e Limitações dos Dados

O conjunto de dados apresenta 284.807 registros distribuídos em 31 colunas. Além das variáveis transformadas por PCA (V1 a V28), o dataset inclui a variável Time, que representa o tempo em segundos entre uma transação e a primeira transação registrada, a variável Amount, que indica o valor monetário da transação, e a variável alvo Class, responsável por indicar se a transação é legítima (0) ou fraudulenta (1).

As variáveis são predominantemente do tipo float64, com exceção da variável Class, que é do tipo int64, caracterizando um problema de classificação binária.

A principal limitação do conjunto de dados é o seu extremo desequilíbrio de classes. Apenas 492 transações (0,17%) são classificadas como fraudulentas, enquanto 284.315 são legítimas. Além disso, a utilização de PCA impede a interpretação direta das variáveis V1 a V28, restringindo análises exploratórias mais profundas sobre o comportamento individual de cada atributo. Diante dessas limitações, torna-se necessário um tratamento específico das variáveis Time e Amount, de modo que o modelo consiga capturar padrões temporais e financeiros relevantes.

4. Tratamento e Preparação dos Dados

A etapa de preparação dos dados foi fundamental para mitigar as limitações identificadas e potencializar o desempenho do algoritmo XGBoost. O processo foi conduzido em três frentes principais.

Inicialmente, realizou-se a verificação da existência de valores ausentes e duplicatas. O conjunto de dados apresentou-se íntegro, não sendo necessária a aplicação de técnicas de imputação. As variáveis que não passaram pelo processo de PCA (Time e Amount) foram padronizadas, garantindo que todas as features estivessem em escalas comparáveis para o modelo.

Em seguida, foram aplicadas técnicas de engenharia de atributos. A variável Time foi transformada por meio de funções seno e cosseno, de forma a representar seu comportamento cíclico. Essa abordagem permite que o modelo compreenda corretamente a proximidade temporal entre o final de um dia e o início do seguinte, capturando padrões de periodicidade diária. Já a variável Amount, que apresentava distribuição altamente assimétrica e presença de outliers, foi submetida a uma transformação logarítmica, reduzindo sua variância e facilitando a convergência do algoritmo.

Por fim, os dados foram divididos em conjuntos de treino, validação e teste. A divisão foi realizada de forma estratificada, garantindo a manutenção da proporção entre classes. Para lidar com o desequilíbrio, optou-se pelo ajuste do parâmetro scale_pos_weight do XGBoost, atribuindo maior peso à classe minoritária, em vez de utilizar técnicas de oversampling como o SMOTE, preservando assim a distribuição estatística original dos dados.

5. Análise Exploratória

Devido à natureza dos dados, previamente transformados por Análise de Componentes Principais, a análise exploratória convencional de correlação entre variáveis torna-se limitada. Dessa forma, a análise concentrou-se principalmente na distribuição da variável alvo e nas características das variáveis não transformadas (Time e Amount).

A confirmação de que o dataset é composto exclusivamente por variáveis numéricas e não apresenta valores ausentes permitiu direcionar o foco do projeto para a engenharia de atributos e a otimização do modelo de detecção de fraudes.

6. Modelagem e Estratégia de Avaliação

O algoritmo selecionado para este projeto foi o XGBoost (Extreme Gradient Boosting), um método baseado em árvores de decisão que utiliza o framework de gradient boosting. A escolha se justifica pela alta performance do algoritmo em dados tabulares e pelo suporte nativo ao tratamento de dados desbalanceados, por meio do parâmetro scale_pos_weight, que penaliza de forma mais rigorosa erros cometidos na classe minoritária.

A base de dados foi particionada em três conjuntos distintos: treino, validação e teste. O conjunto de validação foi utilizado para a otimização de hiperparâmetros por meio do GridSearchCV, permitindo uma busca exaustiva pelos melhores valores de parâmetros como profundidade das árvores, taxa de aprendizado e número de estimadores, reduzindo o risco de sobreajuste.

Em relação às métricas de avaliação, a acurácia não foi considerada adequada, uma vez que, em cenários de fraude, um modelo que classifica todas as transações como legítimas ainda apresentaria alto valor de acerto. Assim, priorizou-se o Recall, com o objetivo de minimizar falsos negativos e detectar o maior número possível de fraudes. A Precision foi monitorada para evitar impactos excessivos sobre clientes legítimos. Também foram utilizadas as métricas AUC-ROC, curva Precision-Recall e matriz de confusão, sendo esta última fundamental para a visualização direta dos acertos e erros do modelo.

7. Resultados e Conclusão

O modelo XGBoost apresentou desempenho elevado na identificação de transações fraudulentas. A estratégia de divisão entre treino, validação e teste permitiu um ajuste eficiente dos hiperparâmetros, resultando em métricas robustas mesmo diante do severo desequilíbrio do conjunto de dados.

No conjunto de teste, o modelo alcançou um valor de AUC-ROC de 0,9785, indicando excelente capacidade de discriminação entre as classes. O Recall para a classe fraudulenta foi de 0,83, demonstrando que 83% das fraudes reais foram corretamente identificadas. A Precision atingiu 0,90, indicando que a grande maioria das detecções realizadas correspondia, de fato, a transações fraudulentas. O F1-Score foi de 0,86, refletindo um bom equilíbrio entre sensibilidade e precisão.

A matriz de confusão revelou 56.855 verdadeiros negativos, apenas 9 falsos positivos, 17 falsos negativos e 81 verdadeiros positivos, evidenciando um impacto mínimo sobre clientes legítimos e uma alta taxa de fraudes interrompidas.

Conclui-se que o projeto atingiu seu objetivo ao entregar um modelo confiável e eficiente para detecção de fraudes. A combinação de engenharia de atributos bem fundamentada com a robustez do algoritmo XGBoost permitiu priorizar a identificação de transações fraudulentas sem comprometer a experiência do usuário, tornando a solução aplicável a cenários reais de instituições financeiras.