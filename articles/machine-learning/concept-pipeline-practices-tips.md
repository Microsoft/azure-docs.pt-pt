---
title: Iterar e evoluir pipelines de machine learning
titleSuffix: Azure Machine Learning
description: Padrões, práticas e dicas para um desenvolvimento rápido
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82858190"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>Iterar e evoluir pipelines de machine learning

Os oleodutos Azure Machine Learning fornecem uma forma eficiente de modular o seu código, reutilizar resultados e otimizar os seus recursos de computação. Aqui ficam algumas dicas práticas e práticas para trabalhar com oleodutos.

## <a name="how-do-you-get-started-with-pipelines"></a>Como se começa com os oleodutos?

Existem várias opções para começar se o seu é novo em oleodutos:

* Se aprender melhor lendo e recriando o processo de construção, o artigo [Criar e executar pipelines de aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md) é um bom encaixe 
* Se gosta de aprender interativamente num caderno Jupyter, o pipeline desenvolvido nos [Pipelines de Aprendizagem de Máquinas Azure: Começar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb) pode ser adequado para si
* Se preferir uma situação de código em primeiro código, a clonagem do [Azure Machine Learning Pipelines Demo](https://github.com/microsoft/aml-pipelines-demo) repo proporciona um bom ponto de partida

## <a name="how-do-you-modularize-pipeline-code"></a>Como modular o código do gasoduto? 

Os módulos e a `ModuleStep` classe dão-lhe uma grande oportunidade de modular o seu código ML. No entanto, há que ter em mente que mover-se entre os degraus do gasoduto é muito mais caro do que uma chamada de função. A pergunta que deve fazer não é tanto "Estas funções e dados são conceptualmente diferentes das desta outra secção?" mas "Quero que estas funções e dados evoluam separadamente?" ou "Esta computação é cara e posso reutilizar a sua produção?" Para obter mais informações, consulte este livro [como criar módulo, ModVersion e usá-los num oleoduto com o ModuleStep.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb)

Tal como discutido anteriormente, separar a preparação de dados da formação é muitas vezes uma dessas oportunidades. Por vezes, a preparação de dados é complexa e morosa o suficiente para que possa quebrar o processo em etapas de gasoduto separadas. Outras oportunidades incluem testes e análises pós-treino. 

## <a name="how-do-you-speed-pipeline-iteration"></a>Como se acelera a iteração do gasoduto? 

As técnicas comuns para os gasodutos de iteração rápida incluem: 

- Clonagem do gasoduto (fazer uma cópia) e reexame rapidamente o gasoduto
- Mantendo a instância de computação em funcionamento, de modo a evitar o tempo de arranque
- Configurar dados e passos para permitir a reutilização permitirá que o gasoduto salte a recalculação de dados imutáveis

Quando quiser iteração rápida, pode clonar o seu oleoduto, fazer um oleoduto e voltar a executar o gasoduto. Outra técnica útil é que se mantiver o seu Compute quente, não incorrerá no custo de girar o novo cálculo. Se configurar o Passo para permitir a reutilização do resultado de uma execução, então a execução repetida reutilizará os resultados sempre que possível (quando não houver alteração nos Passos).

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>Como colabora utilizar gasodutos ML? 

Gasodutos separados são linhas naturais para dividir o esforço. Vários desenvolvedores ou mesmo várias equipas podem trabalhar em diferentes etapas, desde que os dados e argumentos que fluem entre passos sejam acordados. 

Durante o desenvolvimento ativo, você pode obter `PipelineRun` e `StepRun` executar resultados do espaço de trabalho, usar estes objetos para baixar a saída final e intermédia, e usar esses artefactos para o seu próprio trabalho modularizado.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>Utilize gasodutos para testar técnicas isoladamente

As soluções ML do mundo real geralmente envolvem uma personalização considerável de cada passo. Os dados brutos precisam frequentemente de ser preparados de alguma forma: filtrados, transformados e aumentados. Os processos de formação podem ter várias arquiteturas potenciais e, para uma aprendizagem profunda, muitas possíveis variações para tamanhos de camadas e funções de ativação. Mesmo com uma arquitetura consistente, a pesquisa de hiperparímetros pode produzir vitórias significativas.

Além de ferramentas como [o AutoML](concept-automated-ml.md) e [a pesquisa automatizada de hiperparímetros,](how-to-tune-hyperparameters.md)os gasodutos podem ser uma ferramenta importante para soluções de teste A/B. Se tiver várias variantes dos seus passos de oleoduto, é fácil gerar corridas separadas experimentando as suas variações: 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

