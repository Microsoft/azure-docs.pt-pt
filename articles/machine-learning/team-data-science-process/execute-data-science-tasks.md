---
title: Executar tarefas de ciência de dados – processo de ciência de dados de equipe
description: Como um cientista de dados pode executar um projeto de ciência de dados de maneira rastreável, controlada por versão e colaborativa.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a6b463c4a1d0e640cc6c2a380ee978fcafa2877e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670342"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Executar tarefas de ciência de dados: exploração, modelagem e implantação

As tarefas típicas de ciência de dados incluem exploração, modelagem e implantação de dados. Este artigo mostra como usar os utilitários de **exploração, análise e relatório de dados interativos (ideia)** e **modelos automatizados de modelagem e relatório (amar)** para concluir várias tarefas comuns de ciência de dados, como exploração interativa de dados, dados análise, relatórios e criação de modelo. Ele também descreve as opções para implantar um modelo em um ambiente de produção usando uma variedade de kits de opções e plataformas de dados, como as seguintes:

- [Azure Machine Learning](../index.yml)
- [SQL-Server com serviços ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> exploração 

Um cientista de dados pode executar exploração e relatórios de várias maneiras: usando bibliotecas e pacotes disponíveis para Python (matplotlib, por exemplo) ou com R (ggplot ou malha, por exemplo). Os cientistas de dados podem personalizar esse código de acordo com as necessidades de exploração de dados para cenários específicos. As necessidades de lidar com dados estruturados são diferentes para dados não estruturados, como texto ou imagens. 

Produtos como Azure Machine Learning também fornecem [preparação de dados avançada](../service/how-to-transform-data.md) para Wrangling e exploração de dados, incluindo a criação de recursos. O usuário deve decidir as ferramentas, as bibliotecas e os pacotes que melhor atendem às suas necessidades. 

O resultado final desta fase é um relatório de exploração de dados. O relatório deve fornecer uma exibição bastante abrangente dos dados a serem usados para modelagem e uma avaliação de se os dados são adequados para prosseguir para a etapa de modelagem. Os utilitários TDSP (processo de ciência de dados de equipe) discutidos nas seções a seguir para exploração, modelagem e relatórios semiautomatizados também fornecem relatórios padronizados de exploração e modelagem de dados. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Exploração, análise e relatórios de dados interativos usando o utilitário de ideias

Este utilitário baseado em redução de R ou Python baseado em Notebook fornece uma ferramenta flexível e interativa para avaliar e explorar conjuntos de dados. Os usuários podem gerar relatórios rapidamente a partir do conjunto de dados com a codificação mínima. Os usuários podem clicar em botões para exportar os resultados de exploração na ferramenta interativa para um relatório final, que pode ser entregue aos clientes ou usado para tomar decisões sobre quais variáveis incluir na etapa de modelagem subsequente.

Neste momento, a ferramenta só funciona em quadros de dados na memória. Um arquivo YAML é necessário para especificar os parâmetros do conjunto de dados a ser explorado. Para obter mais informações, consulte [ideias em utilitários de ciência de dados do TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> modelagem

Há vários kits de idiomas e pacotes para modelos de treinamento em uma variedade de linguagens. Os cientistas de dados devem se sentir livres para usar com quem eles estão familiarizados, desde que as considerações de desempenho relacionadas à precisão e à latência sejam satisfeitas para os casos de uso de negócios relevantes e cenários de produção.

A próxima seção mostra como usar um utilitário TDSP baseado em R para modelagem semiautomatizada. Esse utilitário AMAR pode ser usado para gerar modelos de linha de base rapidamente, bem como os parâmetros que precisam ser ajustados para fornecer um modelo de melhor desempenho.
A seção de gerenciamento de modelos a seguir mostra como ter um sistema para registrar e gerenciar vários modelos.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Treinamento de modelo: modelagem e relatórios usando o utilitário AMAR

O [Utilitário de modelagem e relatório automatizado (amar)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) fornece uma ferramenta personalizável e semiautomatizada para executar a criação do modelo com a limpeza de hiperparâmetro e para comparar a precisão desses modelos. 

O utilitário de criação de modelo é um arquivo R Markdown que pode ser executado para produzir uma saída HTML independente com um sumário para facilitar a navegação por meio de suas seções diferentes. Três algoritmos são executados quando o arquivo de redução é executado (spojit): regressão regular usando o pacote glmnet, floresta aleatória usando o pacote randomForest e impulsionando árvores usando o pacote xgboost). Cada um desses algoritmos produz um modelo treinado. Em seguida, a precisão desses modelos é comparada e as plotagens de importância do recurso relativo são relatadas. Atualmente, há dois utilitários: um é para uma tarefa de classificação binária e um é para uma tarefa de regressão. As principais diferenças entre eles é a maneira como os parâmetros de controle e as métricas de precisão são especificados para essas tarefas de aprendizado. 

Um arquivo YAML é usado para especificar:

- a entrada de dados (uma fonte SQL ou um arquivo de dados R) 
- Qual parte dos dados é usada para treinamento e qual parte do teste
- Quais algoritmos executar 
- a opção de parâmetros de controle para otimização de modelo:
    - validação cruzada 
    - inicialização
    - dobras da validação cruzada
- os conjuntos de hiperparâmetros para cada algoritmo. 

O número de algoritmos, o número de dobras para otimização, os hiperparâmetros e o número de conjuntos de hiperparâmetros a serem varridos também podem ser modificados no arquivo YAML para executar os modelos rapidamente. Por exemplo, eles podem ser executados com um número menor de dobras de CV, um número menor de conjuntos de parâmetros. Se for garantido, eles também podem ser executados de forma mais abrangente com um número maior de dobras de CV ou um número maior de conjuntos de parâmetros.

Para obter mais informações, consulte [utilitário automatizado de modelagem e relatório em utilitários de ciência de dados do TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Gestão de modelos
Depois que vários modelos tiverem sido criados, normalmente você precisará ter um sistema para registrar e gerenciar os modelos. Normalmente, você precisa de uma combinação de scripts ou APIs e um banco de dados de back-end ou sistema de controle de versão. Algumas opções que você pode considerar para essas tarefas de gerenciamento são:

1. [Serviço de gerenciamento de modelos de Azure Machine Learning](../index.yml)
2. [ModelDB do MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL-Server como um sistema de gerenciamento de modelos](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> implantação

A implantação de produção permite que um modelo reproduza uma função ativa em uma empresa. As previsões de um modelo implantado podem ser usadas para decisões de negócios.

### <a name="production-platforms"></a>Plataformas de produção
Há várias abordagens e plataformas para colocar os modelos em produção. Aqui estão algumas opções:


- [Implantação de modelo no Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Implantação de um modelo no SQL-Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Antes da implantação, é necessário garantir que a latência da Pontuação do modelo seja baixa o suficiente para uso na produção.
>
>

Outros exemplos estão disponíveis em passo a passos que demonstram todas as etapas no processo para **cenários específicos**. Eles são listados e vinculados a descrições em miniatura no artigo [passo a passos de exemplo](walkthroughs.md) . Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente.

> [!NOTE]
> Para a implantação usando o Azure Machine Learning Studio, consulte [implantar um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>Teste A/B
Quando vários modelos estão em produção, pode ser útil executar testes a [/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar o desempenho dos modelos. 

 
## <a name="next-steps"></a>Passos seguintes

[Acompanhar o progresso de projetos de ciência de dados](track-progress.md) mostra como um cientista de dados pode acompanhar o progresso de um projeto de ciência de dados.

A [operação de modelo e CI/CD](ci-cd-flask.md) mostra como o CI/CD pode ser executado com modelos desenvolvidos.


