---
title: Análise migratória do Excel
titleSuffix: ML Studio (classic) - Azure
description: Uma comparação de modelos de regressão linear em Excel e no Azure Machine Learning Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 5831f83d8cd38cc07c64fdc398405c3a70935485
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169095"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Migrar análises do Excel para o Azure Machine Learning Studio (clássico)

> *Kate Baroni* e *Ben Boatman* são arquitetos de soluções empresariais no Centro de Excelência de Insights de Dados da Microsoft. Neste artigo, descrevem a sua experiência migrando um pacote de análise de regressão existente para uma solução baseada na nuvem usando o Azure Machine Learning Studio (clássico).

## <a name="goal"></a>Objetivo

O nosso projeto começou com dois objetivos em mente: 

1. Utilize análises preditivas para melhorar a precisão das projeções mensais de receitas da nossa organização 
2. Utilize o Azure Machine Learning Studio (clássico) para confirmar, otimizar, aumentar a velocidade e a escala dos nossos resultados. 

Como muitas empresas, a nossa organização passa por um processo mensal de previsão de receitas. A nossa pequena equipa de analistas empresariais foi encarregada de usar o Azure Machine Learning Studio (clássico) para apoiar o processo e melhorar a precisão da previsão. A equipa passou vários meses a recolher dados de várias fontes e a executar os atributos de dados através de análise estatística identificando atributos-chave relevantes para a previsão de vendas de serviços. O próximo passo foi começar a protótipode modelos de regressão estatística nos dados do Excel. Em poucas semanas, tivemos um modelo de regressão do Excel que estava a superar os atuais processos de previsão de campo e finanças. Este tornou-se o resultado da previsão de base. 

Demos então o passo seguinte para mudar a nossa análise preditiva para studio (clássico) para descobrir como o Studio (clássico) poderia melhorar no desempenho preditivo.

## <a name="achieving-predictive-performance-parity"></a>Alcançar a paridade de desempenho preditiva
A nossa primeira prioridade era alcançar a paridade entre os modelos de regressão studio (clássico) e Excel. Tendo em conta os mesmos dados, e a mesma divisão para os dados de treino e teste, quisemos alcançar a paridade de desempenho preditiva entre o Excel e o Studio (clássico). Inicialmente falhamos. O modelo Excel superou o modelo Studio (clássico). A falha deveu-se à falta de compreensão da definição de ferramentas base no Studio (clássico). Depois de uma sincronização com a equipa de produtos Studio (clássico), obtivemos uma melhor compreensão da definição base necessária para os nossos conjuntos de dados, e alcançamos a paridade entre os dois modelos. 

### <a name="create-regression-model-in-excel"></a>Criar modelo de regressão em Excel
O nosso Excel Regression usou o modelo padrão de regressão linear encontrado no Excel Analysis ToolPak. 

Calculámos *o Erro De % Absoluto médio* e usámos-o como medida de desempenho para o modelo. Demorou 3 meses a chegar a um modelo de trabalho usando o Excel. Trouxemos grande parte da aprendizagem para a experiência Studio (clássica) que acabou por ser benéfica na compreensão dos requisitos.

### <a name="create-comparable-experiment-in-studio-classic"></a>Criar experiência comparável em Estúdio (clássico)
Seguimos estes passos para criar a nossa experiência em Studio (clássico): 

1. Carregou o conjunto de dados como um ficheiro csv para Studio (clássico) (ficheiro muito pequeno)
2. Criei uma nova experiência e utilizou as [Colunas Select no][select-columns] módulo Dataset para selecionar as mesmas funcionalidades de dados utilizadas no Excel 
3. Usou o módulo [De dados divididos][split] (com modo *de expressão relativa)* para dividir os dados nos mesmos conjuntos de dados de treino que tinha sido feito no Excel 
4. Experimentado com o módulo de [regressão linear][linear-regression] (apenas opções padrão), documentado, e comparou os resultados com o nosso modelo de regressão Excel

### <a name="review-initial-results"></a>Rever os resultados iniciais
No início, o modelo Excel superou claramente o modelo Studio (clássico): 

|  | Excel | Studio (clássico) |
| --- |:---:|:---:|
| Desempenho | | |
| <ul style="list-style-type: none;"><li>Quadrado R ajustado</li></ul> |0.96 |N/D |
| <ul style="list-style-type: none;"><li>Coeficiente de <br />Determinação</li></ul> |N/D |0.78<br />(baixa precisão) |
| Erro absoluto médio |$9.5M |$ 19.4M |
| Erro Absoluto Médio (%) |6.03% |12.2% |

Quando executámos o nosso processo e os resultados pelos desenvolvedores e cientistas de dados na equipa de Machine Learning, rapidamente forneceram algumas dicas úteis. 

* Quando se utiliza o módulo [de regressão linear][linear-regression] em Estúdio (clássico), são fornecidos dois métodos:
  * Descida do Gradiente Online: Pode ser mais adequado para problemas de maior escala
  * Quadrados Mínimos Comuns: Este é o método em que a maioria das pessoas pensa quando ouvem regressão linear. Para pequenos conjuntos de dados, os Quadrados Mínimos Comuns podem ser uma escolha mais ideal.
* Considere ajustar o parâmetro de peso de regularização L2 para melhorar o desempenho. Está definido para 0.001 por padrão, mas para o nosso pequeno conjunto de dados definimo-lo para 0,005 para melhorar o desempenho. 

### <a name="mystery-solved"></a>Mistério resolvido!
Quando aplicámos as recomendações, conseguimos o mesmo desempenho de base no Studio (clássico) como com o Excel: 

|  | Excel | Estúdio (clássico) (Inicial) | Estúdio (clássico) c/ Praças Menos |
| --- |:---:|:---:|:---:|
| Valor rotulado |Reais (numérico) |mesmo |mesmo |
| Aprendiz |Excel -> Análise de Dados -> Regressão |Regressão linear. |Regressão Linear |
| Opções de aprendizagem |N/D |Incumprimentos |quadrados mínimos comuns<br />L2 = 0,005 |
| Conjunto de Dados |26 linhas, 3 características, 1 etiqueta. Tudo numérico. |mesmo |mesmo |
| Split: Comboio |Excel treinou nas primeiras 18 linhas, testadas nas últimas 8 linhas. |mesmo |mesmo |
| Split: Teste |Fórmula de regressão excel aplicada às últimas 8 linhas |mesmo |mesmo |
| **Performance** (Desempenho) | | | |
| Quadrado R ajustado |0.96 |N/D | |
| Coeficiente de Determinação |N/D |0.78 |0.952049 |
| Erro absoluto médio |$9.5M |$ 19.4M |$9.5M |
| Erro Absoluto Médio (%) |<span style="background-color: 00FF00;">6.03%</span> |12.2% |<span style="background-color: 00FF00;">6.03%</span> |

Além disso, os coeficientes Excel compararam-se bem com os pesos de características no modelo treinado pelo Azure:

|  | Coeficientes Excel | Pesos de características azure |
| --- |:---:|:---:|
| Interceção/Enviesamento |19470209.88 |19328500 |
| Recurso A |0.832653063 |0.834156 |
| Recurso B |11071967.08 |11007300 |
| Recurso C |25383318.09 |25140800 |

## <a name="next-steps"></a>Passos Seguintes
Queríamos consumir o serviço web machine learning dentro do Excel. Os nossos analistas de negócios confiam no Excel e precisávamos de uma forma de ligar para o serviço web machine learning com uma linha de dados do Excel e fazê-lo devolver o valor previsto ao Excel. 

Também quisemos otimizar o nosso modelo, utilizando as opções e algoritmos disponíveis no Studio (clássico).

### <a name="integration-with-excel"></a>Integração com Excel
A nossa solução foi operacionalizar o nosso modelo de regressão machine learning através da criação de um serviço web a partir do modelo treinado. Em poucos minutos, o serviço web foi criado e poderíamos chamá-lo diretamente do Excel para devolver um valor de receita previsto. 

A secção *Do Painel de Serviços Web* inclui um livro de excel descarregado. O livro vem pré-formatado com o serviço web API e informações de esquema incorporadas. Quando clicar em *Download Excel Bookbook,* o livro de trabalhos abre e pode guardá-lo para o seu computador local. 

![Baixar livro excel do Painel de Serviços Web](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Com o livro aberto, copie os seus parâmetros predefinidos na secção parâmetro azul, como mostrado abaixo. Uma vez introduzidos os parâmetros, o Excel chama para o serviço web machine learning e as etiquetas pontuadas previstas serão exibidas na secção valores previstos verdes. O livro continuará a criar previsões para parâmetros baseados no seu modelo treinado para todos os itens de linha introduzidos em Parâmetros. Para obter mais informações sobre como utilizar esta funcionalidade, consulte [Consumir um Serviço Web de Aprendizagem automática Azure do Excel](consuming-from-excel.md). 

![Livro de trabalho do Modelo Excel que se conecta ao serviço web implantado](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Otimização e mais experiências
Agora que tínhamos uma linha de base com o nosso modelo Excel, adiantei-nos para otimizar o nosso Modelo linear de regressão linear de aprendizagem automática. Utilitámos o módulo [Filter-Based Feature Selection][filter-based-feature-selection] para melhorar a nossa seleção de elementos de dados iniciais e isso ajudou-nos a alcançar uma melhoria de desempenho de 4,6% de Erro Absoluto Médio. Para projetos futuros, utilizaremos esta funcionalidade que poderá poupar-nos semanas em iteração através de atributos de dados para encontrar o conjunto certo de funcionalidades a utilizar para modelação. 

Em seguida, planeamos incluir algoritmos adicionais como [Bayesian][bayesian-linear-regression] ou [Boosted Decision Trees][boosted-decision-tree-regression] na nossa experiência para comparar o desempenho. 

Se quiser experimentar com regressão, um bom conjunto de dados para experimentar é o conjunto de dados da amostra de regressão de eficiência energética, que tem muitos atributos numéricos. O conjunto de dados é fornecido como parte dos conjuntos de dados da amostra em Studio (clássico). Pode utilizar uma variedade de módulos de aprendizagem para prever a carga de aquecimento ou a carga de arrefecimento. O gráfico abaixo é uma comparação de desempenho de diferentes regressões aprende com o conjunto de dados de eficiência energética que prevê para a variável alvo Carga de arrefecimento: 

| Modelo | Erro absoluto médio | Erro quadrado da raiz média | Erro absoluto relativo | Erro quadrado relativo | Coeficiente de Determinação |
| --- | --- | --- | --- | --- | --- |
| Árvore de decisão reforçada |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Regressão Linear (Descida gradiente) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regressão da Rede Neural |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Regressão Linear (Quadrados Mínimos Ordinários) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Principais takeaways
Aprendemos muito com a regredção do Excel e experiências de Estúdio (clássico) em paralelo. Criar o modelo de base no Excel e compará-lo com modelos usando a [Regressão Linear][linear-regression] de Machine Learning ajudou-nos a aprender Studio (clássico), e descobrimos oportunidades para melhorar a seleção de dados e o desempenho do modelo. 

Também descobrimos que é aconselhável usar a [Seleção de Funcionalidades Baseada][filter-based-feature-selection] em Filtros para acelerar futuros projetos de previsão. Ao aplicar a seleção de funcionalidades aos seus dados, pode criar um modelo melhorado em Studio (clássico) com melhor desempenho geral. 

A capacidade de transferir a previsão analítica preditiva do Studio (clássico) para o Excel permite um aumento significativo na capacidade de fornecer resultados com sucesso a um amplo público de utilizadores de negócios. 

## <a name="resources"></a>Recursos
Aqui estão alguns recursos para ajudá-lo a trabalhar com regressão: 

* Regressão em Excel. Se nunca experimentou a regressão no Excel, este tutorial facilita: [https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regressão vs previsão. Tyler Chessman escreveu um artigo de blogue explicando como fazer previsão de séries de tempo no Excel, que contém uma boa descrição de regressão linear. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Regressão linear dos Quadrados Comuns: Falhas, Problemas e Armadilhas. Para uma introdução e discussão de Regressão: [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

