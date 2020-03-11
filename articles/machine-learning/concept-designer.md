---
title: Construa modelos ML com designer
titleSuffix: Azure Machine Learning
description: Conheça os termos, conceitos e fluxo de trabalho que compõe o designer de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 78a6e7fa8d030185f537136a3a2124d8bc59d808
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037630"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>O que é o designer de machine learning azure (pré-visualização)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

O designer de Machine Learning Azure permite ligar visualmente [conjuntos](#datasets) de dados e [módulos](#module) numa tela interativa para criar modelos de aprendizagem automática. Para aprender a começar com o designer, consulte [Tutorial: Prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md)

![Exemplo de designer de aprendizagem automática azure](./media/concept-designer/designer-drag-and-drop.gif)

O designer usa o seu espaço de [trabalho](concept-workspace.md) Azure Machine Learning para organizar recursos partilhados como:

+ [Pipelines](#pipeline)
+ [Conjuntos de dados](#datasets)
+ [Recursos computacionais](#compute)
+ [Modelos registados](concept-azure-machine-learning-architecture.md#models)
+ [Oleodutos publicados](#publish)
+ [Pontos finais em tempo real](#deploy)

## <a name="model-training-and-deployment"></a>Formação e implantação de modelos

O designer dá-lhe uma tela visual para construir, testar e implementar modelos de aprendizagem automática. Com o designer pode:

+ Conjuntos de [dados](#datasets) [de](#module) arrasto e queda na tela.
+ Ligue os módulos em conjunto para criar um projeto de [gasoduto](#pipeline-draft).
+ Envie um [pipeline utilizando](#pipeline-run) os recursos de computação no seu espaço de trabalho Azure Machine Learning.
+ Converta os seus **gasodutos** de treino em oleodutos de **inferência.**
+ [Publique](#publish) os seus oleodutos num **ponto final** do gasoduto REST para submeter novas corridas de gasodutos com diferentes parâmetros e conjuntos de dados.
    + Publique um gasoduto de **formação** para reutilizar um único oleoduto para treinar vários modelos enquanto muda de parâmetros e conjuntos de dados.
    + Publique um pipeline de **inferência** de lote para fazer previsões sobre novos dados utilizando um modelo previamente treinado.
+ [Implementar](#deploy) um **oleoduto de inferência** em tempo real para um ponto final em tempo real para fazer previsões sobre novos dados em tempo real.

![Diagrama de fluxo de trabalho para treino, inferência do lote e inferência em tempo real no designer](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Um [gasoduto](concept-azure-machine-learning-architecture.md#ml-pipelines) é composto por conjuntos de dados e módulos analíticos, que se ligam entre si. Os gasodutos têm muitos usos: você pode fazer um pipeline que treina um único modelo, ou um que treina vários modelos. Pode criar um oleoduto que faça previsões em tempo real ou em lote, ou fazer um pipeline que apenas limpe dados. Os oleodutos permitem-lhe reutilizar o seu trabalho e organizar os seus projetos.

### <a name="pipeline-draft"></a>Projeto de gasoduto

Ao editar um oleoduto no designer, o seu progresso é salvo como um projeto de **oleoduto.** Pode editar um projeto de pipeline em qualquer ponto, adicionando ou removendo módulos, configurando alvos de cálculo, criando parâmetros, e assim por diante.

Um gasoduto válido tem estas características:

* Os conjuntos de dados só podem ligar-se a módulos.
* Os módulos só podem ligar-se a conjuntos de dados ou a outros módulos.
* Todas as portas de entrada para módulos devem ter alguma ligação com o fluxo de dados.
* Todos os parâmetros necessários para cada módulo devem ser definidos.

Quando estiver pronto para executar o seu projeto de oleoduto, submeta uma corrida de gasodutos.

### <a name="pipeline-run"></a>Gasoduto

Cada vez que executa um gasoduto, a configuração do gasoduto e os seus resultados são armazenados no seu espaço de trabalho como uma **execução**de gasoduto . Pode voltar a qualquer pipeline para inspecioná-lo para fins de resolução de problemas ou auditoria. **Clone** um pipeline para criar um novo projeto de oleoduto para você editar.

As corridas de gasodutos são agruparadas em [experiências](concept-azure-machine-learning-architecture.md#experiments) para organizar a história da corrida. Podes preparar a experiência para cada corrida de gasodutos. 

## <a name="datasets"></a>Conjuntos de dados

Um conjunto de dados de aprendizagem automática facilita o acesso e o trabalho com os seus dados. Vários conjuntos de dados de amostras estão incluídos no designer para que possa experimentar. Pode [registar](how-to-create-register-datasets.md) mais conjuntos de dados à medida que precisar.

## <a name="module"></a>Módulo

Um módulo é um algoritmo que pode utilizar nos seus dados. O designer tem uma série de módulos que vão desde funções de ingresso de dados a processos de formação, pontuação e validação.

Um módulo pode ter um conjunto de parâmetros que pode utilizar para configurar algoritmos internos do módulo. Quando seleciona um módulo na tela, os parâmetros do módulo são apresentados no painel Propriedades à direita da tela. Pode modificar os parâmetros nesse painel para otimizar o seu modelo. Pode definir os recursos computacionais para módulos individuais no designer. 

![Propriedades do módulo](./media/concept-designer/properties.png)

Para alguma ajuda a navegar através da biblioteca de algoritmos de aprendizagem automática disponíveis, consulte [algoritmos de referência de algoritmos e módulos](algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Recursos computacionais

Utilize recursos computacionais do seu espaço de trabalho para executar o seu pipeline e hospedar os seus modelos implantados como pontos finais em tempo real ou pontos finais de pipeline (para inferência de lote). Os destinos de computação suportados são:

| Destino de computação | Formação | Implementação |
| ---- |:----:|:----:|
| Computação do Machine Learning do Azure | ✓ | |
| Azure Kubernetes Service | | ✓ |

Os alvos computacionais estão ligados ao seu espaço de [trabalho de Aprendizagem automática Azure](concept-workspace.md). Gere os seus alvos de computação no seu espaço de trabalho no [Azure Machine Learning Studio (clássico)](https://ml.azure.com).

## <a name="deploy"></a>Implementação

Para realizar inferênciaem em tempo real, deve implantar um oleoduto como um **ponto final em tempo real**. O ponto final em tempo real cria uma interface entre uma aplicação externa e o seu modelo de pontuação. Uma chamada para um ponto final em tempo real devolve os resultados da previsão à aplicação em tempo real. Para fazer uma chamada para um ponto final em tempo real, você passa a chave API que foi criada quando você implantou o ponto final. O ponto final é baseado no REST, uma escolha de arquitetura popular para projetos de programação web.

Os pontos finais em tempo real devem ser implantados num cluster do Serviço Azure Kubernetes.

Para aprender a implementar o seu modelo, consulte [Tutorial: Implemente um modelo](tutorial-designer-automobile-price-deploy.md)de aprendizagem automática com o designer.

## <a name="publish"></a>Publicar

Também pode publicar um oleoduto num ponto final do **gasoduto.** Semelhante a um ponto final em tempo real, um ponto final de gasoduto permite-lhe submeter novos gasodutos a partir de aplicações externas utilizando chamadas REST. No entanto, não é possível enviar ou receber dados em tempo real utilizando um ponto final de pipeline.

Os oleodutos publicados são flexíveis, podem ser usados para treinar ou treinar modelos, [realizar inferência](how-to-run-batch-predictions-designer.md)de lote, processar novos dados e muito mais. Pode publicar vários oleodutos num único ponto final do gasoduto e especificar qual a versão do gasoduto a executar.

Um pipeline publicado funciona nos recursos computacionais que define no projeto do gasoduto para cada módulo.

O designer cria o mesmo objeto [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) que o SDK.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Passando da interface visual para o designer

A interface visual (pré-visualização) foi atualizada e é agora designer de Machine Learning Azure (pré-visualização). O designer foi rearchitectado para usar um backend baseado em pipeline que se integra totalmente com as outras características do Azure Machine Learning. 

Como resultado destas atualizações, alguns conceitos e termos para a interface visual foram alterados ou renomeados. Consulte a tabela abaixo para obter as mudanças conceptuais mais importantes. 

| Conceito no designer | Anteriormente na interface visual |
| ---- |:----:|
| Projeto de gasoduto | Experimentação |
| Ponto final em tempo real | Serviço Web |

### <a name="migrating-to-the-designer"></a>Migrando para o designer

Você pode converter experiências de interface visual existentes e serviços web em pipelines e pontos finais em tempo real no designer. Utilize os seguintes passos para migrar os seus ativos de interface visual:

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Passos seguintes

* Conheça o básico da análise preditiva e da aprendizagem automática com [tutorial: Preveja](tutorial-designer-automobile-price-train-score.md) o preço do automóvel com o designer
* Aprenda a modificar as amostras de [designer existentes](samples-designer.md) para adaptá-las às suas necessidades.

