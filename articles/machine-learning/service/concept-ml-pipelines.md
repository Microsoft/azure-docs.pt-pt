---
title: 'Pipelines: otimizar os fluxos de trabalho do machine learning'
titleSuffix: Azure Machine Learning service
description: Neste artigo, saiba mais sobre o machine learning pipelines, que pode criar com o Azure Machine Learning SDK para Python e as vantagens da utilização de pipelines. Os pipelines de machine learning (ML) são utilizados por cientistas de dados para compilar, otimizar e gerir os respetivos fluxos de trabalho de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 12/4/2018
ms.custom: seodec18
ms.openlocfilehash: d4bef53a21e6ab7b55c16e27083b818929fbd47c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879259"
---
# <a name="build-machine-learning-pipelines-with-the-azure-machine-learning-service"></a>Criar machine learning pipelines com o serviço Azure Machine Learning

Neste artigo, saiba mais sobre o machine learning pipelines, que pode criar com o Azure Machine Learning SDK para Python e as vantagens da utilização de pipelines.

## <a name="what-are-machine-learning-pipelines"></a>Quais são os pipelines de aprendizagem automática?

Utilizar pipelines (ML) de aprendizado de máquina, cientistas de dados, engenheiros de dados e os profissionais de TI pode colaborar nas etapas envolvidas na:
+ Preparação de dados, como normalizations e transformações
+ Preparação de modelos
+ Avaliação do modelo
+ Implementação 

O diagrama seguinte mostra um pipeline de exemplo:

![Aprendizagem automática pipelines no serviço Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png)

### <a name="which-azure-pipeline-technology-should-i-use"></a>Tecnologia de pipeline do Azure que devo utilizar?

A cloud do Azure fornece várias outras pipelines, cada um com uma finalidade diferente. A tabela seguinte lista os pipelines diferentes e o que são utilizados para:

| Pipeline | O que faz | Canonical pipe |
| ---- | ---- | ---- |
| O Azure Machine Learning pipelines | Define reutilizável de machine learning fluxos de trabalho que podem ser utilizados como um modelo para o cenários da aprendizagem automática. | Dados -> modelo |
| [Pipelines do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Atividades de controlo, transformação e movimento de dados de grupos necessários para executar uma tarefa.  | Data -> data |
| [Pipelines do Azure](https://azure.microsoft.com/services/devops/pipelines/) | Integração contínua e entrega da sua aplicação para qualquer plataforma/qualquer cloud  | Código -> aplicação/serviço |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Porquê criar pipelines com o Azure Machine Learning?

Pode utilizar o [do Azure Machine Learning SDK para Python](#the-python-sdk-for-pipelines) para criar pipelines de ML, bem como para submeter e monitorizar as execuções de pipeline individuais.

Com os pipelines, pode otimizar o seu fluxo de trabalho com simplicidade, velocidade, portabilidade e reutilização. Ao criar pipelines com o Azure Machine Learning, que se possa concentrar seus conhecimentos técnicos, aprendizagem automática, em vez de infraestrutura.

Usar as etapas distintas torna possível voltar a executar apenas os passos que necessários, à medida que ajustar e testar o fluxo de trabalho. Um passo é uma unidade computacional no pipeline. Conforme mostrado no diagrama anterior, a tarefa de preparação de dados pode envolver muitos passos. Estes passos incluem, mas não estão limitados a, normalização, transformação, validação e featurization. Origens de dados e dados intermediários são reutilizados em pipeline, que poupa tempo e recursos de computação. 

Depois do pipeline foi concebido, há muitas vezes mais ajustar o loop de treinamento do pipeline. Quando voltar a executar um pipeline, a saltar execução para as etapas que precisam ser executados novamente, por exemplo, um script de treinamento atualizado e ignora o que não mudou. O paradigma do mesmo se aplica a inalterado scripts utilizados para a execução do passo. 

Com o Azure Machine Learning, pode usar vários kits de ferramentas e estruturas, como o Microsoft Cognitive Toolkit ou o TensorFlow, para cada etapa no seu pipeline. As coordenadas do Azure entre os diversos [destinos de computação](concept-azure-machine-learning-architecture.md) utilizar, para que os seus dados intermediários podem ser facilmente partilhados com os destinos de computação de downstream. 

Pode [controlar as métricas para suas experimentações do pipeline](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) diretamente no portal do Azure. 

## <a name="key-advantages"></a>Principais vantagens

As principais vantagens para a criação de pipelines para de aprendizagem para fluxos de trabalho são:

|Principais vantagens|Descrição|
|:-------:|-----------|
|**Autónoma&nbsp;é executado**|Agende alguns passos para executar em paralelo ou em sequência de uma forma automática e fiável. Uma vez que a preparação de dados e a Modelagem podem últimos dias ou semanas, pode agora se concentrar em outras tarefas enquanto seu pipeline está em execução. |
|**Mista e diversificada de computação**|Utilize vários pipelines que são fiável coordenados entre computações heterogêneas e dimensionáveis e de armazenamento. Pode executar os passos de pipeline individuais nos destinos de computação diferentes, como o HDInsight, VMs de ciência de dados de GPU e Databricks. Isso faz uso eficiente de opções de computação disponíveis.|
|**Capacidade de reutilização**|Pode templatize pipelines para cenários específicos, como reparametrização e classificação de lote. Dispará-los de sistemas externos por meio de chamadas REST simples.|
|**Controlo de versões e controlo**|Em vez de controlar manualmente os dados e o resultado caminhos como iterar, utilizam os SDK de pipelines explicitamente o nome e versão seus dados de origens, entradas e saídas. Também pode gerir scripts e dados em separado para uma maior produtividade.|

## <a name="the-python-sdk-for-pipelines"></a>O SDK de Python para pipelines

Utilizar o Python para criar os seus pipelines de ML. O SDK do Azure Machine Learning oferece construções imperativas para sequenciar e paralelização os passos em seus pipelines quando nenhuma dependência de dados está presente. Pode interagir com ele em blocos de notas do Jupyter ou em outro ambiente de desenvolvimento integrado preferencial. 

Através de dependências de dados declarativa, pode otimizar as suas tarefas. O SDK inclui uma estrutura de módulos criados previamente para tarefas comuns, como a transferência de dados e a publicação de modelo. Pode estender o framework para modelar suas próprias convenções, implementando passos personalizados que são reutilizáveis em pipelines. Também pode gerir os destinos de computação e de recursos de armazenamento diretamente a partir do SDK.

Pode guardar pipelines como modelos e implementá-las para um ponto final REST, portanto, pode agendar as tarefas de classificação de lote ou reparametrização.

Para ver como criar o seu, consulte a [documentos de referência do Python SDK para pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o bloco de notas na próxima seção.

## <a name="example-notebooks"></a>Blocos de notas de exemplo
 
Os seguintes blocos de notas demonstram pipelines com o Azure Machine Learning: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos Seguintes

Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md).
