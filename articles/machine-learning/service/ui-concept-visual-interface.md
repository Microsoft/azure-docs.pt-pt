---
title: Interface visual
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre os termos, os conceitos e o fluxo de trabalho que compõem a interface visual (versão prévia) para Azure Machine Learning serviço.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 70141e3ad438ff7ff6d4486e6c27ff4163416e95
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860411"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>O que é a interface visual para o serviço Azure Machine Learning? 

A interface visual (visualização) para Azure Machine Learning serviço permite preparar dados, treinar, testar, implantar, gerenciar e acompanhar modelos de aprendizado de máquina sem escrever código.

Não há nenhuma programação necessária, você conecta visualmente [conjuntos](#dataset) de os e [módulos](#module) para construir seu modelo.

A interface visual usa seu Azure Machine Learning [espaço de trabalho](concept-workspace.md) de serviço para:

+ Os artefatos de gravação do [experimento](#experiment) são executados no espaço de trabalho.
+ Acessar [conjuntos](#dataset)de os.
+ Use os [recursos de computação](#compute) no espaço de trabalho para executar o experimento. 
+ Registrar [modelos](concept-azure-machine-learning-architecture.md#models).
+ [Implante](#deployment) modelos como serviços Web em recursos de computação no espaço de trabalho.

![Visão geral da interface visual](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Fluxo de trabalho

A interface visual oferece uma tela Visual interativa para criar, testar e iterar rapidamente em um modelo. 

+ Você arrasta e solta [módulos](#module) na tela.
+ Conecte os módulos em conjunto para formar um [experimento](#experiment).
+ Execute o experimento usando o recurso de computação do espaço de trabalho do Machine Learning Service.
+ Itere em seu design de modelo editando o experimento e executando-o novamente.
+ Quando estiver pronto, converta seu **teste de treinamento** em um **experimento de previsão**.
+ [Implante](#deployment) o teste de previsão como um serviço Web para que seu modelo possa ser acessado por outras pessoas.

## <a name="experiment"></a>Experimentação

Crie um experimento do zero ou use um experimento de exemplo existente como modelo.  Cada vez que você executa um experimento, os artefatos são armazenados em seu espaço de trabalho.

Um experimento consiste em conjuntos de valores e módulos analíticos, que você conecta juntos para construir um modelo. Especificamente, uma experimentação válida tem estas características:

* Os conjuntos de linhas só podem estar conectados a módulos.
* Os módulos podem estar conectados a conjuntos de os ou outros módulos.
* Todas as portas de entrada para módulos devem ter alguma conexão com o fluxo de dados.
* Todos os parâmetros necessários para cada módulo devem ser definidos.


Para saber como começar a usar a interface visual, consulte [tutorial: Prever o preço do automóvel com a](ui-tutorial-automobile-price-train-score.md)interface visual.

## <a name="dataset"></a>Conjunto de dados

Um conjunto de dados é dado que foi carregado na interface visual para uso no processo de modelagem. Vários conjuntos de exemplos de conjunto de informações são incluídos para você experimentar, e você pode carregar mais conjuntos de informações conforme necessário.

## <a name="module"></a>Módulo

Um módulo é um algoritmo que pode utilizar nos seus dados. A interface visual tem vários módulos que variam de funções de entrada de dados para treinamento, pontuação e processos de validação.

Um módulo pode ter um conjunto de parâmetros que pode utilizar para configurar algoritmos internos do módulo. Quando você seleciona um módulo na tela, os parâmetros do módulo são exibidos no painel Propriedades à direita da tela. Pode modificar os parâmetros nesse painel para otimizar o seu modelo.

![Propriedades do módulo](media/ui-concept-visual-interface/properties.png)

Para obter ajuda para navegar pela biblioteca de algoritmos de aprendizado de máquina disponíveis, consulte [visão geral de referência do módulo & do algoritmo](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Recursos de computação

Use os recursos de computação do seu espaço de trabalho para executar seu experimento ou hospedar seus modelos implantados como serviços Web. Os destinos de computação suportados são:


| Destino de computação | Formação | Implementação |
| ---- |:----:|:----:|
| Computação do Machine Learning do Azure | ✓ | |
| Serviço Kubernetes do Azure | | ✓ |

Os destinos de computação são anexados ao seu [espaço de trabalho](concept-workspace.md)do Machine Learning. Você gerencia seus destinos de computação em seu espaço de trabalho na [portal do Azure](https://portal.azure.com) ou na [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com).

## <a name="deployment"></a>Implementação

Quando seu modelo de análise preditiva estiver pronto, implante-o como um serviço Web diretamente da interface visual.

Os serviços Web fornecem uma interface entre um aplicativo e seu modelo de pontuação. Um aplicativo externo pode se comunicar com o modelo de pontuação em tempo real. Uma chamada para um serviço Web retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada para um serviço Web, tem de transmitir uma chave de API que foi criada quando implementou esse serviço. O serviço Web é baseado em REST, uma opção de arquitetura popular para projetos de programação da Web.

Para saber como implantar seu modelo, consulte [tutorial: Implante um modelo de aprendizado de máquina com a](ui-tutorial-automobile-price-deploy.md)interface visual.

## <a name="next-steps"></a>Passos Seguintes

* Aprenda as noções básicas de análise preditiva e aprendizado de máquina [com o tutorial: Prever o preço do automóvel com a interface visual](ui-tutorial-automobile-price-train-score.md)
* Use um dos exemplos e modifique para a suíte de suas necessidades:
    * [Amostra 1-regressão: Preço de previsão](ui-sample-regression-predict-automobile-price-basic.md)
    * [Exemplo 2-regressão: Prever o preço e comparar algoritmos](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Exemplo 3-classificação: Prever risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
    * [Amostra 4-classificação: Prever o risco de crédito (sensível ao custo)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Exemplo 5-classificação: Prever a rotatividade, a desejo e a venda vertical](ui-sample-classification-predict-churn.md)
