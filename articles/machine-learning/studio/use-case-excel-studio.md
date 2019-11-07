---
title: Migrando a análise do Excel
titleSuffix: ML Studio (classic) Azure
description: Uma comparação de modelos de regressão linear no Excel e no Azure Machine Learning Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ced0e08dc4cbc38f7fe41d01232bc158680dd0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693439"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Migrar a análise do Excel para Azure Machine Learning Studio (clássico)

> *Kate Baroni* e *Ben barco* são arquitetos de soluções empresariais no data insights Center of Excellence da Microsoft. Neste artigo, eles descrevem sua experiência de migração de um pacote de análise de regressão existente para uma solução baseada em nuvem usando Azure Machine Learning Studio (clássico).

## <a name="goal"></a>Objetivo

Nosso projeto começou com dois objetivos em mente: 

1. Use a análise preditiva para melhorar a precisão das projeções de receita mensal de nossa organização 
2. Use a versão clássica do Azure Machine Learning Studio para confirmar, otimizar, aumentar a velocidade e dimensionar nossos resultados. 

Como muitas empresas, nossa organização passa por um processo de previsão de receita mensal. Nossa pequena equipe de analistas de negócios foi tarefa com o uso da versão clássica do Azure Machine Learning Studio para dar suporte ao processo e melhorar a precisão da previsão. A equipe passou vários meses coletando dados de várias fontes e executando os atributos de dados por meio de análise estatística identificando os principais atributos relevantes para a previsão de vendas de serviços. A próxima etapa era começar a protótipos de modelos de regressão estatística nos dados no Excel. Em algumas semanas, tínhamos um modelo de regressão do Excel que estava executando o campo atual e os processos de previsão de finanças. Isso se tornou o resultado da previsão de linha de base. 

Seguimos a próxima etapa para mover nossa análise preditiva para a versão clássica do Studio para descobrir como a versão clássica do estúdio poderia melhorar o desempenho de previsão.

## <a name="achieving-predictive-performance-parity"></a>Alcançando a paridade de desempenho preditiva
Nossa primeira prioridade era obter a paridade entre a versão clássica do Studio e os modelos de regressão do Excel. Considerando os mesmos dados e a mesma divisão para dados de treinamento e teste, queríamos obter a paridade de desempenho preditiva entre o Excel e a versão clássica do Studio. Inicialmente, falhamos. O modelo do Excel supera o modelo de estúdio (clássico). A falha ocorreu devido à falta de compreensão da configuração da ferramenta base na versão clássica do Studio. Após uma sincronização com a versão clássica da equipe do produto Studio, obtivemos uma melhor compreensão da configuração básica necessária para nossos conjuntos de dados e alcançamos a paridade entre os dois modelos. 

### <a name="create-regression-model-in-excel"></a>Criar modelo de regressão no Excel
Nossa regressão do Excel usou o modelo de regressão linear padrão encontrado nas ferramentas de análise do Excel. 

Calculamos o *erro de média absoluta%* e o usamos como medida de desempenho para o modelo. Levou 3 meses para chegar a um modelo funcional usando o Excel. Trouxemos muito do aprendizado para a versão clássica do experimento do estúdio, que, por fim, era benéfico na compreensão dos requisitos.

### <a name="create-comparable-experiment-in-studio-classic"></a>Criar experimento comparável no estúdio (clássico)
Seguimos estas etapas para criar nosso experimento na versão clássica do Studio: 

1. Carregou o conjunto de um arquivo CSV para a versão clássica do Studio (arquivo muito pequeno)
2. Criou um novo experimento e utilizou o módulo [selecionar colunas no conjunto][select-columns] de dados para selecionar os mesmos recursos usados no Excel 
3. Usou o módulo [dividir dados][split] (com o modo de *expressão relativa* ) para dividir os dados nos mesmos conjuntos de dado de treinamento que foram feitos no Excel 
4. Experimentamos o módulo de [regressão linear][linear-regression] (somente opções padrão), documentados e comparamos os resultados ao nosso modelo de regressão do Excel

### <a name="review-initial-results"></a>Examinar resultados iniciais
A princípio, o modelo do Excel desempenhou claramente o modelo de estúdio (clássico): 

|  | Excel | Estúdio (clássico) |
| --- |:---:|:---:|
| Desempenho | | |
| <ul style="list-style-type: none;"><li>Quadrado R ajustado</li></ul> |0,96 |N/D |
| <ul style="list-style-type: none;"><li>Coeficiente de <br />Decisão</li></ul> |N/D |0,78<br />(baixa precisão) |
| Erro de média absoluta |US $9,5 milhões |US $19.4 m |
| Erro de média absoluta (%) |6, 3% |12,2% |

Quando executamos nosso processo e os resultados pelos desenvolvedores e cientistas de dados na equipe de Machine Learning, eles forneceram rapidamente algumas dicas úteis. 

* Quando você usa o módulo [regressão linear][linear-regression] na versão clássica do Studio, dois métodos são fornecidos:
  * Descendente de gradiente online: pode ser mais adequado para problemas de maior escala
  * Quadrados mínimos comuns: esse é o método que a maioria das pessoas imagina quando ouve regressão linear. Para conjuntos de de pequenos, os quadrados mínimos comuns podem ser uma opção ideal.
* Considere ajustar o parâmetro de peso de regularização L2 para melhorar o desempenho. Ele é definido como 0, 1 por padrão, mas para nosso pequeno conjunto de dados, definimos-o como 0, 5 para melhorar o desempenho. 

### <a name="mystery-solved"></a>Mistério resolvido!
Quando aplicamos as recomendações, atingimos o mesmo desempenho de linha de base na versão clássica do Studio como com o Excel: 

|  | Excel | Studio (clássico) (inicial) | Estúdio (clássico) w/mínimo de quadrados |
| --- |:---:|:---:|:---:|
| Valor rotulado |Dados reais (numéricos) |idêntica |idêntica |
| Aprendiz |Análise de dados do Excel->-> regressão |Regressão linear. |Regressão Linear |
| Opções do aprendiz |N/D |Padrões |quadrados mínimos comuns<br />L2 = 0, 5 |
| Conjunto de dados |26 linhas, 3 recursos, 1 rótulo. Todos os números. |idêntica |idêntica |
| Divisão: treinar |Excel treinado nas primeiras 18 linhas, testado nas últimas 8 linhas. |idêntica |idêntica |
| Divisão: teste |Fórmula de regressão do Excel aplicada às últimas 8 linhas |idêntica |idêntica |
| **Performance** (Desempenho) | | | |
| Quadrado R ajustado |0,96 |N/D | |
| Coeficiente de determinação |N/D |0,78 |0,952049 |
| Erro de média absoluta |US $9,5 milhões |US $19.4 m |US $9,5 milhões |
| Erro de média absoluta (%) |<span style="background-color: 00FF00;">6, 3%</span> |12,2% |<span style="background-color: 00FF00;">6, 3%</span> |

Além disso, os coeficientes do Excel se comparam bem aos pesos do recurso no modelo treinado do Azure:

|  | Coeficientes do Excel | Pesos de recursos do Azure |
| --- |:---:|:---:|
| Interceptação/tendência |19470209,88 |19328500 |
| Recurso A |0,832653063 |0,834156 |
| Recurso B |11071967, 8 |11007300 |
| Recurso C |25383318, 9 |25140800 |

## <a name="next-steps"></a>Próximos Passos
Queríamos consumir o serviço Web Machine Learning no Excel. Nossos analistas de negócios contam com o Excel e precisávamos de uma maneira de chamar o serviço Web Machine Learning com uma linha de dados do Excel e fazer com que ele retornasse o valor previsto para o Excel. 

Também queríamos otimizar nosso modelo, usando as opções e os algoritmos disponíveis na versão clássica do Studio.

### <a name="integration-with-excel"></a>Integração com o Excel
Nossa solução era colocar em operação nosso modelo de regressão de Machine Learning criando um serviço Web do modelo treinado. Em alguns minutos, o serviço Web foi criado e poderíamos chamá-lo diretamente do Excel para retornar um valor de receita previsto. 

A seção *painel de serviços Web* inclui uma pasta de trabalho do Excel para download. A pasta de trabalho vem formatada previamente com a API do serviço Web e as informações de esquema inseridas. Quando você clica em *baixar pasta de trabalho do Excel*, a pasta de trabalho é aberta e você pode salvá-la em seu computador local. 

![Baixar a pasta de trabalho do Excel no painel de serviços Web](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Com a pasta de trabalho aberta, copie os parâmetros predefinidos na seção de parâmetros azuis, conforme mostrado abaixo. Depois que os parâmetros forem inseridos, o Excel chamará para o serviço Web Machine Learning e os rótulos pontuados previstos serão exibidos na seção valores previstos verdes. A pasta de trabalho continuará a criar previsões para parâmetros com base em seu modelo treinado para todos os itens de linha inseridos em parâmetros. Para obter mais informações sobre como usar esse recurso, consulte [consumindo um serviço Web Azure Machine Learning do Excel](consuming-from-excel.md). 

![Pasta de trabalho de modelo do Excel conectando ao serviço Web implantado](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Otimização e experimentos adicionais
Agora que tivemos uma linha de base com nosso modelo do Excel, passamos adiante para otimizar nosso Machine Learning modelo de regressão linear. Usamos a seleção de [recursos baseada em filtro][filter-based-feature-selection] de módulo para melhorar nossa seleção de elementos de dados iniciais e isso nos ajudou a alcançar uma melhoria de desempenho de 4,6% de erro absoluto médio. Para futuros projetos, usaremos esse recurso, que pode economizar semanas na iteração por meio de atributos de dados para encontrar o conjunto certo de recursos a serem usados para modelagem. 

Em seguida, planejamos incluir algoritmos adicionais, como [Bayesiana][bayesian-linear-regression] ou [árvores de decisão aumentadas][boosted-decision-tree-regression] em nosso experimento para comparar o desempenho. 

Se você quiser experimentar a regressão, um bom conjunto de uma para tentar é o conjunto de exemplo de regressão de eficiência de energia, que tem muitos atributos numéricos. O conjunto de valores é fornecido como parte dos conjuntos de exemplos de exemplo na versão clássica do Studio. Você pode usar uma variedade de módulos de aprendizado para prever a carga de aquecimento ou a carga de resfriamento. A tabela a seguir é uma comparação de desempenho de diferentes regressão contra o conjunto de energia de eficiência energética que prevê a carga de resfriamento da variável de destino: 

| Modelo | Erro de média absoluta | Erro ao quadrado da média raiz | Erro absoluto relativo | Erro de quadrado relativo | Coeficiente de determinação |
| --- | --- | --- | --- | --- | --- |
| Árvore de decisão aumentada |0,930113 |1,4239 |0,106647 |0, 21662 |0,978338 |
| Regressão linear (descendente de gradiente) |2, 35693 |2,98006 |0,233414 |0, 94881 |0,905119 |
| Regressão da Rede Neural |1,548195 |2,114617 |0,177517 |0, 47774 |0,952226 |
| Regressão linear (quadrados mínimos comuns) |1,428273 |1,984461 |0,163767 |0, 42074 |0,957926 |

## <a name="key-takeaways"></a>Principais argumentos
Aprendemos muito por meio da execução da regressão do Excel e da versão clássica dos experimentos do estúdio em paralelo. Criar o modelo de linha de base no Excel e compará-lo a modelos usando Machine Learning [regressão linear][linear-regression] nos ajudou a aprender o Studio (clássico) e descobrimos oportunidades para melhorar a seleção de dados e o desempenho do modelo. 

Também descobrimos que é aconselhável usar a seleção de [recursos baseada em filtro][filter-based-feature-selection] para acelerar projetos de previsão futuros. Ao aplicar a seleção de recursos aos seus dados, você pode criar um modelo aprimorado na versão clássica do Studio com melhor desempenho geral. 

A capacidade de transferir a previsão analítica preditiva da versão clássica do Studio para o Excel sistematicamente permite um aumento significativo na capacidade de fornecer resultados com êxito a um grande público de usuários de negócios. 

## <a name="resources"></a>Recursos
Aqui estão alguns recursos para ajudá-lo a trabalhar com a regressão: 

* Regressão no Excel. Se você nunca experimentou a regressão no Excel, este tutorial facilitará: [https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regressão versus previsão. O Tyler Chess escreveu um artigo de blog explicando como fazer a previsão de série temporal no Excel, que contém uma boa descrição do principiante de regressão linear. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Regressão linear de quadrados mínimos simples: falhas, problemas e armadilhas. Para obter uma introdução e uma discussão sobre regressão: [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

