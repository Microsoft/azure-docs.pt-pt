---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929655"
---
Um pipeline é executado em um destino de computação, que é um recurso de computação anexado ao seu espaço de trabalho. Depois de criar um destino de computação, você poderá reutilizá-lo para execuções futuras.

1. Selecione **executar** na parte superior da tela para executar o pipeline.

1. Quando o painel **configurações** for exibido, selecione **Selecionar destino de computação**.

    Se você já tiver um destino de computação disponível, poderá selecioná-lo para executar esse pipeline.

    > [!NOTE]
    > O designer pode executar experimentos somente em destinos de computação Azure Machine Learning. Outros destinos de computação não serão mostrados.

1. Insira um nome para o recurso de computação.

1. Selecione **Guardar**.

    ![Configurar destino de computação](./media/aml-ui-create-training-compute/set-compute.png)

1. Selecione **Executar**.

1. Na caixa de diálogo **configurar execução de pipeline** , selecione **+ novo experimento** para o **experimento**.

    > [!NOTE]
    > O pipeline semelhante do grupo de experimentos é executado juntos. Se você executar um pipeline várias vezes, poderá selecionar o mesmo experimento para execuções sucessivas.

    1. Insira um nome descritivo para o **nome do experimento**.

    1. Selecione **Executar**.
    
    Você pode exibir o status de execução e os detalhes no canto superior direito da tela.

    > [!NOTE]
    > Leva aproximadamente cinco minutos para criar um recurso de computação. Depois que o recurso for criado, você poderá reutilizá-lo e ignorar esse tempo de espera para execuções futuras.
    >
    > O recurso de computação é dimensionado em escala para zero nós quando está ocioso para economizar o custo. Ao usá-lo novamente após um atraso, você pode experimentar aproximadamente cinco minutos de tempo de espera enquanto ele é dimensionado novamente.
