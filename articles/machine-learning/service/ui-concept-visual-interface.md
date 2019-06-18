---
title: Interface visual
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre o fluxo de trabalho que compõem a interface visual (pré-visualização) para o serviço Azure Machine Learning, conceitos e termos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: be07e0f3438ea93312d4eb440e7e63b8f98e11b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077368"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>O que é a interface visual para o serviço Azure Machine Learning? 

A interface visual (pré-visualização) para o serviço Azure Machine Learning permite-lhe de preparação de dados, formar, testar, implementar, gerir e controlar os modelos de aprendizagem automática sem ter de escrever código.

Não existe nenhuma programação necessária, vai ligar visualmente [conjuntos de dados](#dataset) e [módulos](#module) para construir o seu modelo.

A interface visual utiliza o serviço Azure Machine Learning [área de trabalho](concept-workspace.md) para:

+ Escrever artefatos das [experimentar](#experiment) é executado para a área de trabalho.
+ Acesso [conjuntos de dados](#dataset).
+ Utilize o [recursos de computação](#compute) na área de trabalho para executar a experimentação. 
+ Registe-se [modelos](concept-azure-machine-learning-architecture.md#models).
+ [Implementar](#deployment) modelos como serviços da web sobre os recursos na área de trabalho de computação.

![Descrição geral da interface do visual](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Fluxo de trabalho

A interface visual dá-lhe uma tela interativa e visual para rapidamente criar, testar e iterar um modelo. 

+ Pode arrastar e largar [módulos](#module) para a tela.
+ Ligar os módulos em conjunto para formar uma [experimentar](#experiment).
+ Execute a experimentação com o recurso de computação da área de trabalho do serviço Machine Learning.
+ Itere o design de modelo editando a experimentação e executá-lo novamente.
+ Quando estiver pronto, converter seus **experimentação de preparação** para um **experimentação preditiva**.
+ [Implementar](#deployment) a experimentação preditiva como um web service para que o modelo pode ser acedido por outras pessoas.

## <a name="experiment"></a>Experimentação

Criar uma experimentação de raiz ou utilizar uma experimentação de exemplo existente como modelo.  Sempre que executar uma experimentação, artefactos são armazenados na sua área de trabalho.

Uma experimentação é constituída por conjuntos de dados e módulos analíticos, que ligar em conjunto para construir um modelo. Especificamente, uma experimentação válida tem estas características:

* Conjuntos de dados podem estar conectados a apenas aos módulos.
* Módulos podem estar conectados a conjuntos de dados ou outros módulos.
* Todas as portas de entrada para módulos tem de ter alguma ligação ao fluxo de dados.
* Todos os necessários parâmetros para cada módulo tem de ser definidos.

Para obter um exemplo de uma experimentação simples, consulte [início rápido: Preparar e visualize os dados sem escrever código no Azure Machine Learning](ui-quickstart-run-experiment.md).

Para obter instruções mais completas de uma solução de Análise Preditiva, consulte [Tutorial: Prever o preço de automóvel com a interface visual](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Conjunto de dados

Um conjunto de dados é os dados que foi carregados para a interface visual a utilizar no processo de modelagem. Um número de conjuntos de dados de exemplo está incluído para que possa experimentar e pode carregar mais conjuntos de dados sempre que precisar.

## <a name="module"></a>Módulo

Um módulo é um algoritmo que pode utilizar nos seus dados. A interface visual tem um número de módulos que vão desde a funções de entrada de dados, formação, classificação e processos de validação.

Um módulo pode ter um conjunto de parâmetros que pode utilizar para configurar algoritmos internos do módulo. Quando seleciona um módulo na tela, os parâmetros do módulo são apresentados no painel de propriedades para a direita da tela. Pode modificar os parâmetros nesse painel para otimizar o seu modelo.

![Propriedades do módulo](media/ui-concept-visual-interface/properties.png)

Para obter ajuda na navegação por meio da biblioteca de algoritmos de machine learning disponíveis, consulte [descrição geral de referência de algoritmos e módulos](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> Recursos de computação

Recursos da sua área de trabalho para executar a experimentação ou anfitrião seus modelos implementados como serviços da web de computação de utilização. Os destinos de computação suportados são:


| Destino de computação | Formação | Implementação |
| ---- |:----:|:----:|
| Computação do Machine Learning do Azure | ✓ | |
| Serviço Kubernetes do Azure | | ✓ |

Computação destinos estão ligados ao seu Aprendizado de máquina [área de trabalho](concept-workspace.md). Gerir o seus destinos de computação na sua área de trabalho do [portal do Azure](https://portal.azure.com).

## <a name="deployment"></a>Implementação

Assim que o modelo de Análise Preditiva estiver pronto, implementá-la como um serviço web diretamente a partir da interface visual.

Os serviços web fornecem uma interface entre uma aplicação e seu modelo de classificação. Uma aplicação externa comunica com o modelo de classificação em tempo real. Uma chamada para um serviço web devolve resultados de predição para uma aplicação externa. Para fazer uma chamada para um serviço Web, tem de transmitir uma chave de API que foi criada quando implementou esse serviço. O serviço web baseia-se em REST, uma opção de arquitetura popular para projetos de programação web.

Para saber como implementar o seu modelo, consulte o artigo [Tutorial: Implementar um modelo de aprendizagem automática com a interface visual](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Passos Seguintes

* Aprenda as noções básicas de Análise Preditiva e machine learning com [início rápido: Preparar e visualize os dados sem escrever código no Azure Machine Learning](ui-quickstart-run-experiment.md).
* Utilize um dos exemplos e modifique ao suite suas necessidades:
    * [Exemplo 1 - regressão: Prever o preço](ui-sample-regression-predict-automobile-price-basic.md)
    * [Exemplo 2 - regressão: Prever o preço e comparar algoritmos](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Exemplo 3 - classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
    * [Exemplo 4 - classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Exemplo 5 - classificação: Prever o volume de alterações, apetência e de segurança de vendas](ui-sample-classification-predict-churn.md)
