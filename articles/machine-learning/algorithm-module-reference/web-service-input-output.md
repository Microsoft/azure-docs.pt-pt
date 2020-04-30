---
title: Entrada/Saída do Serviço Web
description: Conheça os módulos de serviço web em Azure Machine Learning designer (pré-visualização)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462090"
---
# <a name="web-service-inputoutput"></a>Entrada/Saída do Serviço Web

Este artigo descreve o módulo de entrada de **serviço web** e o módulo de saída do **serviço web** no designer de machine learning Azure (pré-visualização).

**O** módulo de entrada do Serviço Web só pode ligar-se à porta de entrada com o tipo **DataFrameDirectory**. E o módulo de saída do **serviço Web** só pode ser ligado a partir da porta de saída com o tipo **DataFrameDirectory**. Os dois módulos podem ser encontrados na árvore do módulo, na categoria **De Serviço Web.** 

**O** módulo de entrada do Serviço Web é utilizado para indicar onde os dados do utilizador entram no pipeline e o módulo de saída do **serviço web** é utilizado para indicar onde os dados do utilizador são devolvidos num Pipeline de inferência em tempo real.

## <a name="how-to-use-web-service-inputoutput"></a>Como utilizar a entrada/saída do serviço Web

- Quando criar um pipeline de inferência em tempo real a partir do seu pipeline de treino, o módulo de entrada de **serviço web** e o módulo de saída do **serviço web** serão automaticamente adicionados para mostrar onde os dados dos utilizadores entram no pipeline e onde os dados são devolvidos. 

    Saiba mais sobre [a criação de um oleoduto de inferência](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)em tempo real.

    > [!NOTE]
    > Gerar automaticamente um gasoduto de inferência em tempo real é um processo baseado em regras de melhor esforço, não há garantia para a correção. Pode adicionar ou remover manualmente os módulos de **entrada/saída** do Serviço Web para satisfazer os seus requisitos. Certifique-se de que existe pelo menos um módulo de entrada de **serviço Web** e um módulo de saída de **serviço web** no seu pipeline de inferência em tempo real. Se tiver vários módulos de entrada de **serviço web** ou de saída de **serviço web,** certifique-se de que possuem nomes únicos, que pode inseri-lo no painel certo do módulo.

- Também pode criar manualmente um pipeline de inferência em tempo real adicionando módulos de entrada de **serviço web** e saída de **serviço web** ao seu pipeline não submetido.

    > [!NOTE]
    >  O tipo de gasoduto será determinado na primeira vez que o submeter. Por isso, certifique-se de adicionar o módulo de saída de entrada de **serviço web** e de saída de **serviço web** antes de submeter pela primeira vez se quiser criar um pipeline de inferência em tempo real.

   Abaixo o exemplo mostra como criar manualmente o gasoduto de inferência em tempo real a partir do módulo **Execute Python Script.** 

   ![Exemplo](media/module/web-service-input-output-example.png)
   
   Depois de submeter o oleoduto e a execução terminar com sucesso, poderá implantar o ponto final em tempo real.
   
   > [!NOTE]
   >  No exemplo acima, o **Enter Data Manualmente** fornece o esquema de dados para entrada de serviço web e é necessário para implementar o ponto final em tempo real. Geralmente, deve sempre ligar um módulo ou conjunto de dados à porta que a Entrada do **Serviço Web** está ligada para fornecer o esquema de dados.
   
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a implementação do ponto final em tempo real](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.