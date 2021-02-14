---
title: Executar tarefas de ciência de dados - Processo de Ciência de Dados de Equipa
description: Como um cientista de dados pode executar um projeto de ciência de dados de uma forma controlável, controlada pela versão e colaborativa.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e180ecbf5c68dbd9c179244083a641ac6ed42de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371962"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Executar tarefas de ciência de dados: exploração, modelação e implantação

As tarefas típicas da ciência dos dados incluem a exploração de dados, modelação e implantação. Este artigo descreve as tarefas para completar várias tarefas comuns de ciência de dados, tais como exploração interativa de dados, análise de dados, relatórios e criação de modelos. As opções para a implantação de um modelo num ambiente de produção podem incluir:

- [Azure Machine Learning](../index.yml)
- [SQL-Server com serviços ML](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Exploração 

Um cientista de dados pode realizar a exploração e reportagem de várias maneiras: utilizando bibliotecas e pacotes disponíveis para Python (matplotlib, por exemplo) ou com R (ggplot ou reticulado, por exemplo). Os cientistas de dados podem personalizar tal código para se adaptarem às necessidades de exploração de dados para cenários específicos. As necessidades de lidar com dados estruturados são diferentes das dos dados não estruturados, como texto ou imagens. 

Produtos como a Azure Machine Learning também fornecem [uma preparação avançada](../how-to-create-register-datasets.md) de dados para a luta e exploração de dados, incluindo a criação de recursos. O utilizador deve decidir sobre as ferramentas, bibliotecas e pacotes que melhor se assemessem às suas necessidades. 

O resultado no final desta fase é um relatório de exploração de dados. O relatório deve fornecer uma visão bastante abrangente dos dados a utilizar para modelação e uma avaliação de se os dados são adequados para avançar para o passo de modelação. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modelação

Existem inúmeros kits de ferramentas e pacotes para modelos de treino em várias línguas. Os cientistas de dados devem sentir-se à vontade para usar quais os que estão confortáveis, desde que sejam satisfeitas considerações de desempenho em matéria de precisão e latência para os casos de uso das empresas relevantes e cenários de produção.

### <a name="model-management"></a>Gestão de modelos
Depois de vários modelos terem sido construídos, normalmente é necessário ter um sistema de registo e gestão dos modelos. Normalmente, você precisa de uma combinação de scripts ou APIs e uma base de dados de backend ou sistema de versão. Algumas opções que pode considerar para estas tarefas de gestão são:

1. [Azure Machine Learning - serviço de gestão de modelos](../index.yml)
2. [ModelDB do MIT](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
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
