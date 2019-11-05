---
title: Criar modelos de ML com o designer
titleSuffix: Azure Machine Learning
description: Saiba mais sobre os termos, os conceitos e o fluxo de trabalho que compõem o designer para Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517868"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>O que é o designer de Azure Machine Learning (versão prévia)? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

O designer para Azure Machine Learning permite preparar dados, treinar, testar, implantar, gerenciar e acompanhar modelos de aprendizado de máquina sem escrever código.

Não há nenhuma programação necessária, você conecta visualmente [conjuntos](#datasets) de os e [módulos](#module) para construir seu modelo.

O designer usa seu [espaço de trabalho](concept-workspace.md) Azure Machine Learning para:

+ Crie, edite e execute [pipelines](#pipeline) no espaço de trabalho.
+ Acessar [conjuntos](#datasets)de os.
+ Use os [recursos de computação](#compute) no espaço de trabalho para executar o pipeline. 
+ Registrar [modelos](concept-azure-machine-learning-architecture.md#models).
+ [Publicar](#publish) pipelines como pontos de extremidade REST.
+ [Implante](#deployment) modelos como pontos de extremidade de pipeline (para inferência de lote) ou pontos de extremidade em tempo real em recursos de computação no espaço de trabalho.

![Visão geral do designer](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Fluxo de trabalho

O designer oferece uma tela Visual interativa para criar, testar e iterar rapidamente em um modelo. 

+ Você arrasta e solta os [conjuntos](#datasets) de itens e os [módulos](#module) na tela.
+ Conecte os módulos em conjunto para formar um [pipeline](#pipeline).
+ Execute o pipeline usando o recurso de computação do espaço de trabalho do serviço Machine Learning.
+ Itere em seu design de modelo editando o pipeline e executando-o novamente.
+ Quando estiver pronto, converta seu **pipeline de treinamento** em um **pipeline de inferência**.
+ [Publique](#publish) seu pipeline como um ponto de extremidade REST se você quiser reenviá-lo sem o código do Python construí-lo.
+ [Implante](#deployment) o pipeline de inferência como um ponto de extremidade de pipeline ou ponto de extremidade em tempo real para que seu modelo possa ser acessado por outras pessoas.

## <a name="pipeline"></a>Pipeline

Crie um [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) de ml do zero ou use um pipeline de exemplo existente como modelo. Cada vez que você executa um pipeline, os artefatos são armazenados em seu espaço de trabalho. As execuções de pipeline são agrupadas em [experimentos](concept-azure-machine-learning-architecture.md#experiments).

Um pipeline consiste em conjuntos de valores e módulos analíticos, que você conecta juntos para construir um modelo. Especificamente, um pipeline válido tem estas características:

* Os conjuntos de linhas só podem estar conectados a módulos.
* Os módulos podem estar conectados a conjuntos de os ou outros módulos.
* Todas as portas de entrada para módulos devem ter alguma conexão com o fluxo de dados.
* Todos os parâmetros necessários para cada módulo devem ser definidos.


Para saber como começar a usar o designer, consulte [tutorial: prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md).

## <a name="datasets"></a>Conjuntos de dados

Um conjunto de dados de aprendizado de máquina facilita o acesso e o trabalho com eles. Vários exemplos de conjuntos de exemplo estão incluídos no designer para experimentar. Você pode [registrar](./how-to-create-register-datasets.md) mais conjuntos de informações conforme necessário.

## <a name="module"></a>Módulo

Um módulo é um algoritmo que pode utilizar nos seus dados. O designer tem vários módulos que variam de funções de entrada de dados até processos de treinamento, pontuação e validação.

Um módulo pode ter um conjunto de parâmetros que pode utilizar para configurar algoritmos internos do módulo. Quando você seleciona um módulo na tela, os parâmetros do módulo são exibidos no painel Propriedades à direita da tela. Pode modificar os parâmetros nesse painel para otimizar o seu modelo.

![Propriedades do módulo](media/ui-concept-visual-interface/properties.png)

Para obter ajuda para navegar pela biblioteca de algoritmos de aprendizado de máquina disponíveis, consulte [visão geral de referência do módulo & do algoritmo](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Recursos de computação

Use os recursos de computação do seu espaço de trabalho para executar seu pipeline e hospedar seus modelos implantados como pontos de extremidade em tempo real ou pontos de extremidade de pipeline (para inferência de lote). Os destinos de computação com suporte são:

| Destino de computação | Formação | Implementação |
| ---- |:----:|:----:|
| Computação Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Os destinos de computação são anexados ao seu [espaço de trabalho](concept-workspace.md)do Machine Learning. Você gerencia seus destinos de computação em seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com).

## <a name="publish"></a>Publicar

Quando você tiver um pipeline pronto, poderá publicá-lo como um ponto de extremidade REST. Um [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) pode ser enviado sem o código Python que o construiu.

Além disso, um PublishedPipeline pode ser usado para reenviar um pipeline com diferentes valores e entradas de PipelineParameter.

## <a name="deployment"></a>Implementação

Quando seu modelo de previsão estiver pronto, implante-o como um ponto de extremidade de pipeline ou ponto de extremidade em tempo real diretamente do designer.

O ponto de extremidade do pipeline é um PublishedPipeline, que você pode enviar uma execução de pipeline com diferentes valores de PipelineParameter e entradas para inferência de lote.

O ponto de extremidade em tempo real fornece uma interface entre um aplicativo e seu modelo de pontuação. Um aplicativo externo pode se comunicar com o modelo de pontuação em tempo real. Uma chamada para um ponto de extremidade em tempo real retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada para um ponto de extremidade em tempo real, você passa uma chave de API que foi criada quando você implantou o ponto de extremidade. O ponto de extremidade é baseado em REST, uma opção de arquitetura popular para projetos de programação da Web.

Para saber como implantar seu modelo, consulte [tutorial: implantar um modelo de aprendizado de máquina com o designer](tutorial-designer-automobile-price-deploy.md).

## <a name="moving-from-the-visual-interface-to-the-designer"></a>Movendo da interface visual para o designer

A interface visual (versão prévia) foi atualizada e agora é Azure Machine Learning designer (versão prévia). O designer foi reprojetado para usar um back-end baseado em pipeline que se integra totalmente com os outros recursos do Azure Machine Learning. 

Como resultado dessas atualizações, alguns conceitos e termos da interface visual foram alterados ou renomeados. Consulte a tabela abaixo para obter as alterações conceituais mais importantes. 

| Conceito no designer | Anteriormente na interface visual |
| ---- |:----:|
| Rascunho do pipeline | Testes |
| Ponto de extremidade em tempo real | Serviço Web |

### <a name="migrating-to-the-designer"></a>Migrando para o designer

Você pode converter experimentos de interface visual existentes e serviços Web em pipelines e pontos de extremidade em tempo real no designer. Use as etapas a seguir para migrar seus ativos de interface visual:

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Passos seguintes

* Conheça os conceitos básicos da análise preditiva e do aprendizado de máquina com [o tutorial: prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md)
* Use um dos exemplos e modifique para a suíte de suas necessidades:

- [Exemplo 1-regressão: prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemplo 2-regressão: comparar algoritmos para previsão de preço de automóvel](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemplo 3-classificação com seleção de recursos: Previsão de renda](how-to-designer-sample-classification-predict-income.md)
- [Exemplo 4-classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemplo 5-classificação: Previsão de rotatividade](how-to-designer-sample-classification-churn.md)
- [Exemplo 6-classificação: prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
- [Exemplo de classificação de texto 7: conjunto de teste da Wikipédia SP 500](how-to-designer-sample-text-classification.md)

