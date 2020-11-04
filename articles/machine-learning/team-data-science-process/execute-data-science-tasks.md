---
title: Executar tarefas de ciência de dados - Processo de Ciência de Dados de Equipa
description: Como um cientista de dados pode executar um projeto de ciência de dados de uma forma controlável, controlada pela versão e colaborativa.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: cbe822b75368a1ab72bcd7f73419770b291d2508
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321148"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Executar tarefas de ciência de dados: exploração, modelação e implantação

As tarefas típicas da ciência dos dados incluem a exploração de dados, modelação e implantação. Este artigo mostra como utilizar os utilitários **Interativos de Exploração, Análise e Reportagem (IDEAR)** e **Modelação e Reportagem Automatizada (AMAR)** para completar várias tarefas comuns de ciência de dados, tais como exploração interativa de dados, análise de dados, reporte e criação de modelos. As opções para a implantação de um modelo num ambiente de produção podem incluir:

- [Azure Machine Learning](../index.yml)
- [SQL-Server com serviços ML](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Exploração 

Um cientista de dados pode realizar a exploração e reportagem de várias maneiras: utilizando bibliotecas e pacotes disponíveis para Python (matplotlib, por exemplo) ou com R (ggplot ou reticulado, por exemplo). Os cientistas de dados podem personalizar tal código para se adaptarem às necessidades de exploração de dados para cenários específicos. As necessidades de lidar com dados estruturados são diferentes das dos dados não estruturados, como texto ou imagens. 

Produtos como a Azure Machine Learning também fornecem [uma preparação avançada](../how-to-create-register-datasets.md) de dados para a luta e exploração de dados, incluindo a criação de recursos. O utilizador deve decidir sobre as ferramentas, bibliotecas e pacotes que melhor se assemessem às suas necessidades. 

O resultado no final desta fase é um relatório de exploração de dados. O relatório deve fornecer uma visão bastante abrangente dos dados a utilizar para modelação e uma avaliação de se os dados são adequados para avançar para o passo de modelação. Os utilitários do Processo de Ciência de Dados de Equipa (TDSP) discutidos nas seguintes secções para exploração, modelação e reporte semi-automatizados também fornecem relatórios padronizados de exploração e modelação de dados. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Exploração, análise e reporte de dados interativos utilizando o utilitário IDEAR

Este utilitário baseado em R markdown ou python baseado em cadernos fornece uma ferramenta flexível e interativa para avaliar e explorar conjuntos de dados. Os utilizadores podem gerar rapidamente relatórios a partir do conjunto de dados com codificação mínima. Os utilizadores podem clicar em botões para exportar os resultados da exploração na ferramenta interativa para um relatório final, que pode ser entregue aos clientes ou usado para tomar decisões sobre quais as variáveis a incluir na etapa de modelação subsequente.

Neste momento, a ferramenta funciona apenas em quadros de dados na memória. É necessário um ficheiro YAML para especificar os parâmetros do conjunto de dados a explorar. Para obter mais informações, consulte [IDEAR em Utilitários de Ciência de Dados TDSP.](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils)


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modelação

Existem inúmeros kits de ferramentas e pacotes para modelos de treino em várias línguas. Os cientistas de dados devem sentir-se à vontade para usar quais os que estão confortáveis, desde que sejam satisfeitas considerações de desempenho em matéria de precisão e latência para os casos de uso das empresas relevantes e cenários de produção.

A secção seguinte mostra como usar um utilitário TDSP baseado em R para modelação semi-automatizada. Este utilitário AMAR pode ser usado para gerar modelos de linha base rapidamente, bem como os parâmetros que precisam de ser sintonizados para fornecer um modelo de melhor desempenho.
A seguinte secção de gestão de modelos mostra como ter um sistema de registo e gestão de vários modelos.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Formação de modelos: modelação e reporte utilizando o utilitário AMAR

O [Utilitário automatizado de Modelação e Reportagem (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) fornece uma ferramenta personalizável e semi-automatizada para executar a criação de modelos com varredura de hiper-parâmetros e para comparar a precisão desses modelos. 

O utilitário de criação de modelos é um ficheiro R Markdown que pode ser executado para produzir uma saída HTML autossuficiente com uma tabela de conteúdos para fácil navegação através das suas diferentes secções. Três algoritmos são executados quando o ficheiro Markdown é executado (malha): regressão regularizada usando o pacote glmnet, floresta aleatória usando o pacote RandomForest, e impulsionando árvores usando o pacote xgboost). Cada um destes algoritmos produz um modelo treinado. A precisão destes modelos é então comparada e os enredos de importância relativa são relatados. Atualmente, existem duas utilidades: uma é para uma tarefa de classificação binária e outra para uma tarefa de regressão. As principais diferenças entre eles são a forma como os parâmetros de controlo e as métricas de precisão são especificados para estas tarefas de aprendizagem. 

Um ficheiro YAML é utilizado para especificar:

- a entrada de dados (uma fonte SQL ou um ficheiro R-Data) 
- que parte dos dados é usada para o treino e que porção para testes
- que algoritmos para executar 
- a escolha dos parâmetros de controlo para a otimização do modelo:
    - validação cruzada 
    - bootstrapping
    - dobras de validação cruzada
- os conjuntos de hiper-parâmetros para cada algoritmo. 

O número de algoritmos, o número de dobras para otimização, os hiper-parâmetros e o número de conjuntos de hiper-parâmetros para varrer também podem ser modificados no ficheiro Yaml para executar os modelos rapidamente. Por exemplo, podem ser executados com um número mais baixo de dobras CV, um número menor de conjuntos de parâmetros. Se for justificado, também podem ser executados de forma mais abrangente com um maior número de dobras CV ou um maior número de conjuntos de parâmetros.

Para obter mais informações, consulte [a Utilidade automatizada de Modelação e Reportagem em Utilitários de Ciência de Dados TDSP.](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)

### <a name="model-management"></a>Gestão de modelos
Depois de vários modelos terem sido construídos, normalmente é necessário ter um sistema de registo e gestão dos modelos. Normalmente, você precisa de uma combinação de scripts ou APIs e uma base de dados de backend ou sistema de versão. Algumas opções que pode considerar para estas tarefas de gestão são:

1. [Azure Machine Learning - serviço de gestão de modelos](../index.yml)
2. [ModelDB do MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-servidor como um sistema de gestão de modelos](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> Implantação

A implantação da produção permite que um modelo desem este um papel ativo num negócio. As previsões de um modelo implantado podem ser usadas para decisões empresariais.

### <a name="production-platforms"></a>Plataformas de produção
Existem várias abordagens e plataformas para colocar modelos em produção. Aqui estão algumas opções:


- [Implantação de modelos em Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implantação de um modelo no sql-servidor](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Antes da implantação, é preciso garantir que a latência da pontuação do modelo é baixa o suficiente para ser usada na produção.
>
>

Outros exemplos estão disponíveis em passos que demonstram todos os passos no processo para **cenários específicos.** Estão listados e ligados com descrições de miniaturas no artigo [exemplo.](walkthroughs.md) Ilustram como combinar ferramentas e serviços de nuvem, no local, num fluxo de trabalho ou oleoduto para criar uma aplicação inteligente.

> [!NOTE]
> Para implementar usando o Azure Machine Learning Studio, consulte [implementar um serviço web Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>Testes A/B
Quando vários modelos estão em produção, pode ser útil realizar [testes A/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar o desempenho dos modelos. 

 
## <a name="next-steps"></a>Passos seguintes

[Acompanhar o progresso dos projetos de ciência de dados](track-progress.md) mostra como um cientista de dados pode acompanhar o progresso de um projeto de ciência de dados.

[O funcionamento do modelo e o CI/CD](ci-cd-flask.md) mostram como o CI/CD pode ser realizado com modelos desenvolvidos.