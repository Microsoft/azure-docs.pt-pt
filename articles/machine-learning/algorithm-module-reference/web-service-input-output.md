---
title: 'Entrada/saída do Serviço Web: Referência do módulo'
description: Saiba como utilizar os módulos de serviço web no Azure Machine Learning designer para gerir entradas e saídas.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 475ad3de8e0a7636a14949d4fcd8a5ec2812ad5e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421129"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Módulos de entrada e saída de serviço web de serviço web

Este artigo descreve os módulos de entrada de serviço web e de saída de serviço web no designer de aprendizagem automática Azure.

O módulo de entrada de serviço web só pode ligar-se a uma porta de entrada com o tipo **DataFrameDirectory**. O módulo de saída do Serviço Web só pode ser ligado a partir de uma porta de saída com o tipo **DataFrameDirectory**. Pode encontrar os dois módulos na árvore do módulo, na categoria **Serviço Web.** 

O módulo de entrada de serviço web indica onde os dados do utilizador entram no pipeline. O módulo de saída do Serviço Web indica onde os dados do utilizador são devolvidos num pipeline de inferência em tempo real.

## <a name="how-to-use-web-service-input-and-output"></a>Como utilizar a entrada e saída do Serviço Web

Quando [criar um pipeline de inferência em tempo real](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) a partir do seu pipeline de treino, os módulos de entrada de serviço web e de saída de serviço web serão automaticamente adicionados para mostrar onde os dados do utilizador entram no pipeline e onde os dados são devolvidos. 

> [!NOTE]
> Gerar automaticamente um gasoduto de inferência em tempo real é um processo baseado em regras e melhor esforço. Não há garantia de correção. 

Pode adicionar ou remover manualmente os módulos de entrada e saída de serviço web para satisfazer os seus requisitos. Certifique-se de que o seu pipeline de inferência em tempo real tem pelo menos um módulo de entrada de serviço web e um módulo de saída de serviço web. Se tiver vários módulos de entrada de serviço web ou de saída de serviço web, certifique-se de que têm nomes únicos. Pode introduzir o nome no painel direito do módulo.

Também pode criar manualmente um pipeline de inferência em tempo real adicionando módulos de entrada de serviço web e de saída de serviço web ao seu pipeline não submetido.

> [!NOTE]
> O tipo de gasoduto será determinado na primeira vez que o submeter. Certifique-se de adicionar módulos de entrada de serviço web e de saída de serviço web antes de submeter pela primeira vez.

O exemplo a seguir mostra como criar manualmente o gasoduto de inferência em tempo real a partir do módulo executo python script. 

![Exemplo](media/module/web-service-input-output-example.png)
   
Depois de submeter o gasoduto e terminar com sucesso, pode implantar o ponto final em tempo real.
   
> [!NOTE]
>  No exemplo anterior, **introduza manualmente** os dados fornece o esquema de dados para a entrada do serviço web e é necessário para a implementação do ponto final em tempo real. Geralmente, deve sempre ligar um módulo ou conjunto de dados à porta onde a **Entrada do Serviço Web** está ligada para fornecer o esquema de dados.
   
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a implantação do ponto final](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint)em tempo real .

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.