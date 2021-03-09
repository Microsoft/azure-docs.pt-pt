---
title: Construa modelos ML com o designer
titleSuffix: Azure Machine Learning
description: Conheça os termos, conceitos e fluxo de trabalho que compõe o designer para Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: e6738bf944c5a80d0cb54432ade7555ebdcfbd51
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503545"
---
# <a name="what-is-azure-machine-learning-designer"></a>O que é o estruturador do Azure Machine Learning? 


O designer de Machine Learning Azure permite-lhe conectar visualmente conjuntos de [dados](#datasets) e [módulos](#module) numa tela interativa para criar modelos de aprendizagem automática. Para aprender a começar com o designer, consulte [Tutorial: Predict automobile price with the designer](tutorial-designer-automobile-price-train-score.md)

![Exemplo de designer de aprendizagem de máquinas Azure](./media/concept-designer/designer-drag-and-drop.gif)

O designer usa o seu [espaço de trabalho](concept-workspace.md) Azure Machine Learning para organizar recursos partilhados como:

+ [Pipelines](#pipeline)
+ [Conjuntos de dados](#datasets)
+ [Recursos de cálculo](#compute)
+ [Modelos registados](concept-azure-machine-learning-architecture.md#models)
+ [Oleodutos publicados](#publish)
+ [Pontos finais em tempo real](#deploy)

## <a name="model-training-and-deployment"></a>Formação e implantação de modelos

O designer dá-lhe uma tela visual para construir, testar e implementar modelos de machine learning. Com o designer pode:

+ Conjuntos de [dados](#datasets) e [módulos](#module) de arrasto e entrega sobre a tela.
+ Ligue os módulos para criar um [projeto de gasoduto.](#pipeline-draft)
+ Envie uma [corrida de pipeline](#pipeline-run) utilizando os recursos computacional no seu espaço de trabalho Azure Machine Learning.
+ Converta os seus **gasodutos de treino** **em condutas de inferência.**
+ [Publique](#publish) os seus oleodutos num **ponto final do gasoduto** REST para submeter um novo oleoduto que funciona com diferentes parâmetros e conjuntos de dados.
    + Publique um **pipeline de treino** para reutilizar um único oleoduto para treinar vários modelos enquanto altera parâmetros e conjuntos de dados.
    + Publique um **pipeline de inferência de lote** para fazer previsões sobre novos dados utilizando um modelo previamente treinado.
+ [Implementar](#deploy) um **gasoduto de inferência em tempo real** para um ponto final em tempo real para fazer previsões sobre novos dados em tempo real.

![Diagrama de fluxo de trabalho para treino, inferência de lote e inferência em tempo real no designer](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Um [oleoduto](concept-azure-machine-learning-architecture.md#ml-pipelines) consiste em conjuntos de dados e módulos analíticos, que se conectam. Os oleodutos têm muitos usos: você pode fazer um oleoduto que treina um único modelo, ou um que treina vários modelos. Pode criar um oleoduto que faça previsões em tempo real ou em lote, ou fazer um pipeline que apenas limpe dados. Os oleodutos permitem-lhe reutilizar o seu trabalho e organizar os seus projetos.

### <a name="pipeline-draft"></a>Projeto de gasoduto

Ao editar um oleoduto no designer, o seu progresso é guardado como um **projeto de oleoduto.** Pode editar um rascunho de pipeline em qualquer ponto adicionando ou removendo módulos, configurando metas de computação, criando parâmetros, e assim por diante.

Um gasoduto válido tem estas características:

* Os conjuntos de dados só podem ligar-se aos módulos.
* Os módulos só podem ligar-se a conjuntos de dados ou a outros módulos.
* Todas as portas de entrada para módulos devem ter alguma ligação com o fluxo de dados.
* Todos os parâmetros necessários para cada módulo devem ser definidos.

Quando estiver pronto para executar o seu projeto de oleoduto, envie uma corrida de oleoduto.

### <a name="pipeline-run"></a>Curso de gasoduto

Cada vez que corre um oleoduto, a configuração do gasoduto e os seus resultados são armazenados no seu espaço de trabalho como um **gasoduto .** Pode voltar a qualquer corrida de gasoduto para inspecioná-lo para efeitos de resolução de problemas ou auditoria. **Clone** uma corrida de gasoduto para criar um novo projeto de pipeline para que possa editar.

As corridas de gasodutos são agrupadas em [experiências](concept-azure-machine-learning-architecture.md#experiments) para organizar a história da execução. Pode definir a experiência para cada corrida de gasodutos. 

## <a name="datasets"></a>Conjuntos de dados

Um conjunto de dados de machine learning facilita o acesso e o trabalho com os seus dados. Vários conjuntos de dados de amostra estão incluídos no designer para você experimentar. Pode [registar](how-to-create-register-datasets.md) mais conjuntos de dados à medida que necessitar.

## <a name="module"></a>Módulo

Um módulo é um algoritmo que pode utilizar nos seus dados. O designer tem vários módulos que vão desde funções de entrada de dados até processos de treino, pontuação e validação.

Um módulo pode ter um conjunto de parâmetros que pode utilizar para configurar algoritmos internos do módulo. Quando seleciona um módulo na tela, os parâmetros do módulo são apresentados no painel Propriedades à direita da tela. Pode modificar os parâmetros nesse painel para otimizar o seu modelo. Pode definir os recursos de computação para módulos individuais no designer. 

:::image type="content" source="./media/concept-designer/properties.png" alt-text="Propriedades do módulo":::


Para alguma ajuda na navegação através da biblioteca de algoritmos de machine learning disponíveis, consulte [algoritmo & visão geral de referência do módulo](algorithm-module-reference/module-reference.md). Para ajudar a escolher um algoritmo, consulte a Folha de Batota do [Algoritmo de Aprendizagem de Máquinas Azure.](algorithm-cheat-sheet.md)

## <a name="compute-resources"></a><a name="compute"></a> Recursos de cálculo

Utilize recursos compute do seu espaço de trabalho para executar o seu pipeline e hospedar os seus modelos implantados como pontos finais em tempo real ou pontos finais do gasoduto (para inferência do lote). Os objetivos de computação suportados são:

| Destino de computação | Formação | Implementação |
| ---- |:----:|:----:|
| Azure Machine Learning compute | ✓ | |
| Instância de computação do Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Os alvos computacional estão ligados ao seu [espaço de trabalho Azure Machine Learning](concept-workspace.md). Gere os seus alvos de computação no seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com).

## <a name="deploy"></a>Implementar

Para realizar inferenculação em tempo real, tem de implantar um gasoduto como ponto final em **tempo real**. O ponto final em tempo real cria uma interface entre uma aplicação externa e o seu modelo de pontuação. Uma chamada para um ponto final em tempo real devolve os resultados da previsão à aplicação em tempo real. Para fazer uma chamada para um ponto final em tempo real, você passa a chave API que foi criada quando implementou o ponto final. O ponto final é baseado no REST, uma escolha popular de arquitetura para projetos de programação web.

Os pontos finais em tempo real devem ser implantados num cluster de serviço Azure Kubernetes.

Para aprender a implementar o seu modelo, consulte [Tutorial: Implemente um modelo de machine learning com o designer](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publicar

Também pode publicar um oleoduto num **ponto final de gasoduto**. Semelhante a um ponto final em tempo real, um ponto final do gasoduto permite-lhe submeter novos gasodutos a partir de aplicações externas utilizando chamadas REST. No entanto, não é possível enviar ou receber dados em tempo real utilizando um ponto final do pipeline.

Os oleodutos publicados são flexíveis, podem ser usados para treinar ou retreinar modelos, [realizar inferenculação de lotes,](how-to-run-batch-predictions-designer.md)processar novos dados, e muito mais. Pode publicar vários oleodutos num único ponto final do gasoduto e especificar qual a versão do pipeline a executar.

Um oleoduto publicado funciona nos recursos computacional que define no projeto do pipeline para cada módulo.

O designer cria o mesmo objeto [PublishedPipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline) que o SDK.

## <a name="next-steps"></a>Passos seguintes

* Aprenda os fundamentos da análise preditiva e machine learning com [Tutorial: Predict automobile price with the designer](tutorial-designer-automobile-price-train-score.md)
* Aprenda a modificar as amostras de [designers existentes](samples-designer.md) para as adaptar às suas necessidades.