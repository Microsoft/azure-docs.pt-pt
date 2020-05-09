---
title: Oleodutos de aprendizagem automática de eterno e evolução
titleSuffix: Azure Machine Learning
description: Padrões, práticas e dicas para o rápido desenvolvimento
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858190"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>Oleodutos de aprendizagem automática de eterno e evolução

Os oleodutos Azure Machine Learning fornecem uma forma eficiente de modular o seu código, reutilizar resultados e otimizar os seus recursos computacionais. Aqui estão algumas dicas e práticas práticas para trabalhar com oleodutos.

## <a name="how-do-you-get-started-with-pipelines"></a>Como se começa com oleodutos?

Existem várias opções para começar se o seu é novo em pipelines:

* Se aprender melhor lendo e recriando o processo de construção, o artigo Criar e executar gasodutos de [aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md) é um bom encaixe 
* Se gosta de aprender interativamente num caderno jupyter, o oleoduto desenvolvido nos Pipelines de [Aprendizagem automática Azure: Começar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb) o caderno pode ser o certo para si
* Se preferir uma situação de código-primeiro, clonagem do [Azure Machine Learning Pipelines Demo](https://github.com/microsoft/aml-pipelines-demo) repo proporciona um bom ponto de partida

## <a name="how-do-you-modularize-pipeline-code"></a>Como modular o código do oleoduto? 

Os módulos `ModuleStep` e a classe dão-lhe uma grande oportunidade de modular o seu código ML. No entanto, há que ter em conta que mover-se entre passos de gasoduto é muito mais caro do que uma chamada de função. A pergunta que deve fazer não é tanto "Estas funções e dados são conceptualmente diferentes das desta outra secção?" mas "Quero que estas funções e dados evoluam separadamente?" ou "Este cálculo é caro e posso reutilizar a sua produção?" Para mais informações, consulte este n'tebook [Como criar Módulo, ModuleVersion, e use-os num pipeline com ModuleStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb).

Como já foi discutido, separar a preparação de dados da formação é muitas vezes uma dessas oportunidades. Por vezes, a preparação de dados é complexa e morosa o suficiente para que possa quebrar o processo em passos de gasoduto separados. Outras oportunidades incluem testes e análises pós-treino. 

## <a name="how-do-you-speed-pipeline-iteration"></a>Como acelera a iteração do gasoduto? 

As técnicas comuns para a eitiação rápida de gasodutos incluem: 

- Clonagem do gasoduto (fazer uma cópia) e reexecução rapidamente do gasoduto
- Manter a instância computacional em funcionamento, de modo a evitar o tempo de arranque
- Configurar dados e passos para permitir a reutilização permitirá que o gasoduto ignore o recálculo de dados imutáveis

Quando quiseritar rapidamente, pode clonar o seu oleoduto, fazer um oleoduto e refazer o gasoduto. Outra técnica útil é que se mantiver o seu Compute quente, não incorrerá no custo de girar o novo computado. Se configurar o Passo para permitir a reutilização do resultado de uma corrida, então a execução repetida reutilizará os resultados sempre que possível (quando não houver alteração nas Etapas).

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>Como colabora com os oleodutos ML? 

Oleodutos separados são linhas naturais ao longo das quais se dividem esforços. Vários desenvolvedores ou mesmo várias equipas podem trabalhar em diferentes passos, desde que os dados e argumentos que fluam entre passos sejam acordados. 

Durante o desenvolvimento ativo, pode recuperar `PipelineRun` e `StepRun` executar resultados do espaço de trabalho, usar estes objetos para descarregar saída final e intermediária e usar esses artefactos para o seu próprio trabalho modularizado.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>Utilize gasodutos para testar técnicas isoladamente

As soluções ML do mundo real geralmente envolvem uma personalização considerável de cada passo. Os dados brutos muitas vezes precisam de ser preparados de alguma forma: filtrados, transformados e aumentados. Os processos de formação podem ter várias arquiteturas potenciais e, para aprendizagem profunda, muitas variações possíveis para tamanhos de camadas e funções de ativação. Mesmo com uma arquitetura consistente, a pesquisa de hiperparâmetros pode produzir vitórias significativas.

Além de ferramentas como [autoML](concept-automated-ml.md) e pesquisa automatizada de [hiperparâmetros,](how-to-tune-hyperparameters.md)os gasodutos podem ser uma ferramenta importante para soluções de teste A/B. Se tiver várias variantes dos seus passos de pipeline, é fácil gerar corridas separadas experimentando as suas variações: 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

