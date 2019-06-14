---
title: Executar tarefas de ciência de dados - Team Data Science Process
description: Como um cientista de dados pode executar um projeto de ciência de dados num passível de controlo, versão controlada além de forma colaborativa.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d8ae3a95262b1554e7e97fac8375a44743bf4df
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60344670"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Executar tarefas de ciência de dados: exploração, modelação e implementação

Tarefas de ciência de dados típicos incluem a exploração de dados, modelação e implementação. Este artigo mostra como utilizar o **interativa de exploração de dados, análise e relatórios (IDEAR)** e **automatizada de modelagem e relatórios (AMAR)** utilitários para concluir várias tarefas de ciência de dados comuns Por exemplo, a exploração interativa de dados, análise de dados, relatórios e criação de um modelo. Ele também descreve as opções para implementar um modelo para um ambiente de produção utilizando uma variedade de plataformas de dados e kits de ferramentas, como as seguintes:

- [Azure Machine Learning](../service/index.yml)
- [SQL Server com os serviços de ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Exploração 

Um cientista de dados pode realizar a exploração e relatórios de diversas formas: através da utilização de bibliotecas e os pacotes disponíveis para o Python (matplotlib por exemplo) ou com R (ggplot ou reticulado, por exemplo). Os cientistas de dados podem personalizar esse código para atender às necessidades de exploração de dados para cenários específicos. As necessidades para lidar com dados estruturados são diferentes que para dados não estruturados, como texto ou imagens. 

Produtos como o serviço Azure Machine Learning também fornecem [preparação de dados avançada](../service/how-to-transform-data.md) para preparação de dados e a exploração, incluindo a criação de funcionalidade. O usuário deve decidir sobre as ferramentas, bibliotecas e pacotes que melhor suite suas necessidades. 

O resultado final no final desta fase é um relatório de exploração de dados. O relatório deve fornecer uma visão razoavelmente abrangente dos dados a serem usados para modelagem e uma avaliação se é adequados para avançar para o passo de modelagem de dados. Os utilitários de processo de ciência de dados de equipa (TDSP) abordados nas seções a seguir para exploração semiautomática, modelagem e relatórios também fornecem relatórios de modelagem e exploração de dados padronizada. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Exploração de dados interativos, análise e relatórios usando o utilitário IDEAR

Este R com base em markdown ou um utilitário baseado no bloco de notas do Python fornece uma ferramenta de flexível e interativa para avaliar e explorar os conjuntos de dados. Os utilizadores podem rapidamente gerar relatórios do conjunto de dados com codificação mínima. Os utilizadores podem clicar de botões para exportar os resultados de exploração na ferramenta de interativo para um relatório final, o que pode ser distribuído aos clientes ou utilizado para tomar decisões sobre quais variáveis para incluir o passo de modelagem subsequentes.

Neste momento, a ferramenta só funciona em quadros de dados na memória. Um ficheiro YAML é necessário especificar os parâmetros de conjunto de dados a ser explorada. Para obter mais informações, consulte [IDEAR em utilitários de ciência de dados do TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modelagem

Existem vários kits de ferramentas e os pacotes de modelos de treinamento numa variedade de linguagens. Os cientistas de dados devem se sentir livres para usar a cenários de produção e de casos de utilização que ever aqueles que estão à vontade, enquanto as considerações de desempenho em relação à precisão e latência são cumpridas para as empresas e relevantes.

A secção seguinte mostra como utilizar um utilitário baseado em R TDSP para modelagem semiautomática. Esse utilitário AMAR pode ser utilizado para gerar modelos de linha de base rapidamente, bem como os parâmetros que têm de ser ajustada para fornecer um melhor desempenho de modelos.
A secção de gestão de modelo seguinte mostra como ter um sistema para registar e gerir vários modelos.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Treinamento de modelo: modelagem e relatórios usando o utilitário AMAR

O [automatizada de modelagem e utilitário de geração de relatórios (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) fornece uma ferramenta semiautomática personalizável para executar a criação de um modelo com o hyper-parâmetro varrimento e comparar a precisão desses modelos. 

O utilitário de criação de modelo é um ficheiro de Markdown de R que pode ser executado para produzir independente de HTML de saída, com uma tabela de conteúdos para uma navegação fácil por meio de suas seções diferentes. Três algoritmos são executados quando o ficheiro de Markdown é executado (knit): regressão regularized usando o glmnet empacotar, aleatória floresta utilizando o pacote de randomForest e priorização baseada em árvores utilizando o pacote de xgboost). Cada um desses algoritmos produz um modelo preparado. A precisão desses modelos é então comparada e os gráficos de importância relativa de funcionalidade são comunicados. Atualmente, existem dois utilitários: um é para uma tarefa de classificação binária e um para uma tarefa de regressão. As principais diferenças entre eles é os parâmetros de controle da forma e métricas de precisão são especificadas para essas tarefas de aprendizagem. 

Um ficheiro YAML é utilizado para especificar:

- a entrada de dados (uma origem SQL ou um arquivo de dados de R) 
- que parte dos dados é utilizado para formação e que parte para fins de teste
- quais algoritmos devem ser executados 
- a escolha dos parâmetros de controle para a otimização de modelo:
    - a validação cruzada 
    - arranque do sistema
    - subconjuntos de validação cruzada
- os conjuntos de hyper-parâmetro de cada algoritmo. 

O número de algoritmos, o número de subconjuntos de otimização, os hiper parâmetros e o número de conjuntos de hyper-parâmetro varrê sobre também podem ser modificados no ficheiro Yaml para executar rapidamente os modelos. Por exemplo, podem ser executadas com um número inferior de subconjuntos de CV, um número inferior de conjuntos de parâmetros. Se ele é garantido de tarefas também podem ser executadas mais abrangente com um número superior de subconjuntos de CV ou um número maior de conjuntos de parâmetros.

Para obter mais informações, consulte [automatizada de modelagem e relatórios utilitário em utilitários de ciência de dados do TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Gestão de modelos
Depois de tem sido construídos vários modelos, normalmente, tem de ter um sistema para registar e gerir os modelos. Normalmente, precisa de uma combinação de scripts ou APIs e um sistema back-end de base de dados ou controle de versão. Algumas opções que pode considerar para essas tarefas de gestão são:

1. [O Azure Machine Learning - serviço de gestão de modelos](../service/index.yml)
2. [ModelDB do MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL-server como um sistema de gestão de modelo](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> Implementação

Implantação de produção permite que um modelo para desempenhar um papel ativo numa empresa. Predições a partir de um modelo implementado podem ser utilizadas para a tomada de decisões comerciais.

### <a name="production-platforms"></a>Plataformas de produção
Existem várias abordagens e plataformas para colocar os modelos em produção. Aqui estão algumas opções:


- [Modelo de implementação no serviço Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Implementação de um modelo no SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Antes da implantação, é necessário assegurar que a latência de modelo de classificação é baixa o suficiente para utilizar na produção.
>
>

Mais exemplos estão disponíveis instruções passo a passo que demonstram todas as etapas do processo para **cenários específicos**. Se estão listadas e estão associados ao descrições em miniatura da [instruções passo a passo do exemplo](walkthroughs.md) artigo. Eles ilustram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente.

> [!NOTE]
> Para ver a implementação com o Azure Machine Learning Studio, consulte [implementar um serviço web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A testes a / B
Quando vários modelos na produção, pode ser útil realizar [A testes a / B](https://en.wikipedia.org/wiki/A/B_testing) para comparar o desempenho dos modelos. 

 
## <a name="next-steps"></a>Passos Seguintes

[Controlar o progresso dos projetos de ciência de dados](track-progress.md) mostra como um cientista de dados poderá acompanhar o progresso de um projeto de ciência de dados.

[Modelo de operação e CI/CD](ci-cd-flask.md) mostra como o CI/CD podem ser executada com modelos de desenvolvidos.


