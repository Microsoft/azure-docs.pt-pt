---
title: Executar tarefas de ciência de dados - Team Data Science Process
description: Como um cientista de dados pode executar um projeto de ciência de dados de uma forma rastreável, controlada e colaborativa.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e69a03cd142fdbcc5864ee38a4843e1c2e44a124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477158"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Executar tarefas de ciência de dados: exploração, modelação e implantação

As tarefas típicas da ciência dos dados incluem a exploração de dados, modelação e implementação. Este artigo mostra como utilizar os serviços interativos de **exploração, análise e reporte de dados (IDEAR)** e **modelação e reporte automatizado (AMAR)** para completar várias tarefas comuns de ciência de dados, tais como exploração interativa de dados, análise de dados, relatórios e criação de modelos. As opções de implantação de um modelo num ambiente de produção podem incluir:

- [Azure Machine Learning](../index.yml)
- [SQL-Server com serviços ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Exploração 

Um cientista de dados pode realizar a exploração e reportagem de várias maneiras: utilizando bibliotecas e pacotes disponíveis para Python (matplotlib, por exemplo) ou com R (ggplot ou lattice, por exemplo). Os cientistas de dados podem personalizar esse código para se adaptarem às necessidades de exploração de dados para cenários específicos. As necessidades de lidar com dados estruturados são diferentes das para dados não estruturados, como texto ou imagens. 

Produtos como o Azure Machine Learning também fornecem [uma preparação avançada de dados](../how-to-create-register-datasets.md) para a discussão e exploração de dados, incluindo a criação de recursos. O utilizador deve decidir sobre as ferramentas, bibliotecas e pacotes que melhor se abarquem com as suas necessidades. 

O resultado no final desta fase é um relatório de exploração de dados. O relatório deve fornecer uma visão bastante abrangente dos dados a utilizar para a modelação e uma avaliação da adaptação dos dados para a etapa de modelação. Os utilitários do Team Data Science Process (TDSP) discutidos nas seguintes secções para exploração, modelação e reporte semi-automatizados também fornecem relatórios padronizados de exploração e modelação de dados. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Exploração, análise e reportagem interativa de dados utilizando o utilitário IDEAR

Este utilitário baseado em cadernos baseado em marcação R ou Python fornece uma ferramenta flexível e interativa para avaliar e explorar conjuntos de dados. Os utilizadores podem rapidamente gerar relatórios a partir do conjunto de dados com o mínimo de codificação. Os utilizadores podem clicar em botões para exportar os resultados da exploração na ferramenta interativa para um relatório final, que pode ser entregue aos clientes ou usado para tomar decisões sobre quais as variáveis a incluir na etapa de modelação subsequente.

Neste momento, a ferramenta só funciona em data-frames na memória. É necessário um ficheiro YAML para especificar os parâmetros do conjunto de dados a explorar. Para mais informações, consulte [idear em TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modelação

Existem inúmeros kits de ferramentas e pacotes para modelos de formação em várias línguas. Os cientistas de dados devem sentir-se livres para usar todos os que estão confortáveis, desde que as considerações de desempenho em relação à precisão e à latência estejam satisfeitas para os casos de utilização do negócio relevantes e cenários de produção.

A secção seguinte mostra como usar um utilitário TDSP baseado em R para modelação semi-automatizada. Este utilitário AMAR pode ser usado para gerar modelos de linha base rapidamente, bem como os parâmetros que precisam de ser sintonizados para fornecer um modelo de melhor desempenho.
A secção de gestão de modelos que se segue mostra como ter um sistema de registo e gestão de vários modelos.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Formação de modelos: modelação e reporte utilizando o utilitário AMAR

O Utilitário automatizado de [Modelação e Reportagem (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) fornece uma ferramenta personalizável e semi-automatizada para realizar a criação de modelos com varredura de hiperparâmetro e comparar a precisão desses modelos. 

O utilitário de criação de modelos é um ficheiro R Markdown que pode ser executado para produzir saída HTML autossuficiente com uma tabela de conteúdos para uma navegação fácil através das suas diferentes secções. Três algoritmos são executados quando o ficheiro Markdown é executado (malha): regressão regularizada usando o pacote de glmnet, floresta aleatória usando o pacote randomForest, e impulsionando árvores usando o pacote xgboost). Cada um destes algoritmos produz um modelo treinado. A precisão destes modelos é então comparada e os enredos relativos da importância da característica são relatados. Atualmente, existem dois utilitários: um é para uma tarefa de classificação binária e um é para uma tarefa de regressão. As principais diferenças entre eles são a forma como os parâmetros de controlo e as métricas de precisão são especificadas para estas tarefas de aprendizagem. 

Um ficheiro YAML é utilizado para especificar:

- a entrada de dados (uma fonte SQL ou um ficheiro R-Data) 
- que parte dos dados é usado para o treino e que parte para testes
- que algoritmos para executar 
- a escolha dos parâmetros de controlo para a otimização do modelo:
    - validação cruzada 
    - bootstrapping
    - dobras de validação cruzada
- os conjuntos de hiperparâmetros para cada algoritmo. 

O número de algoritmos, o número de dobras para otimização, os hiperparâmetros e o número de conjuntos de hiperparâmetros para varrer também podem ser modificados no ficheiro Yaml para executar os modelos rapidamente. Por exemplo, podem ser executados com um número mais baixo de dobras de CV, um número menor de conjuntos de parâmetros. Se for justificado, também podem ser executados de forma mais abrangente com um maior número de dobras de CV ou um maior número de conjuntos de parâmetros.

Para mais informações, consulte Modelação Automatizada e Utilidade de Reporte em Utilitários de Ciência de [Dados Da TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Gestão de modelos
Depois de vários modelos terem sido construídos, você geralmente precisa ter um sistema de registo e gestão dos modelos. Normalmente, precisa de uma combinação de scripts ou APIs e de uma base de dados ou sistema de versão backend. Algumas opções que pode considerar para estas tarefas de gestão são:

1. [Azure Machine Learning - serviço de gestão de modelos](../index.yml)
2. [ModelDB do MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-server como um sistema de gestão de modelos](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> Implantação

A implantação da produção permite que um modelo desempem um papel ativo num negócio. As previsões de um modelo implantado podem ser usadas para decisões empresariais.

### <a name="production-platforms"></a>Plataformas de produção
Existem várias abordagens e plataformas para colocar modelos em produção. Aqui estão algumas opções:


- [Implantação de modelos em Aprendizagem automática Azure](../how-to-deploy-and-where.md)
- [Implementação de um modelo no servidor SQL](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Antes da implantação, é preciso garantir que a latência da pontuação do modelo é baixa o suficiente para ser usada na produção.
>
>

Outros exemplos estão disponíveis em passadiços que demonstram todos os passos no processo para **cenários específicos.** Estão listados e ligados com descrições de miniaturas no artigo [de walkthroughs exemplo.](walkthroughs.md) Ilustram como combinar cloud, ferramentas no local e serviços num fluxo de trabalho ou oleoduto para criar uma aplicação inteligente.

> [!NOTE]
> Para ser implementado utilizando o Azure Machine Learning Studio, consulte [A implantação de um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>Ensaio a/B
Quando vários modelos estão em produção, pode ser útil realizar [testes de A/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar o desempenho dos modelos. 

 
## <a name="next-steps"></a>Passos seguintes

Acompanhar o [progresso dos projetos de ciência de dados](track-progress.md) mostra como um cientista de dados pode acompanhar o progresso de um projeto de ciência de dados.

A operação do modelo e o [CI/CD](ci-cd-flask.md) mostram como o CI/CD pode ser realizado com modelos desenvolvidos.


