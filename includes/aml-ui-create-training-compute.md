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
ms.openlocfilehash: f7cdfb8b9edbce5ef2b094cf6a603904d39a7404
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493632"
---
Um pipeline é executado em um destino de computação que é um recurso de computação anexado ao seu espaço de trabalho. Depois de criar um destino de computação, você poderá reutilizá-lo para execuções futuras.

1. Selecione **executar** na parte superior da tela para executar o pipeline.

1. Quando o painel **configurações** for exibido, selecione **Selecionar destino de computação**.

    Se você já tiver um destino de computação disponível, poderá selecioná-lo para executar esse pipeline.

    > [!NOTE]
    > O designer só pode executar experimentos em destinos de Computação do Machine Learning. Outros destinos de computação não serão mostrados.

1. Forneça um nome para o recurso de computação.

1. Selecione **Guardar**.

    ![Configurar destino de computação](./media/aml-ui-create-training-compute/set-compute.png)

1. Selecione **Executar**.

1. Na caixa de diálogo **configurar execução de pipeline** , selecione **+ novo experimento** para o **experimento**

    > [!NOTE]
    > O pipeline semelhante do grupo de experimentos é executado juntos. Se você executar um pipeline várias vezes, poderá selecionar o mesmo experimento para execuções sucessivas.

    * Insira um **nome de experimento** descritivo

    * Selecione **executar**
    
    Você pode exibir o status de execução e os detalhes no canto superior direito da tela.

    > [!NOTE]
    > Leva aproximadamente 5 minutos para criar um recurso de computação. Depois que o recurso for criado, você poderá reutilizá-lo e ignorar esse tempo de espera para execuções futuras.
    >
    > O recurso de computação fará o dimensionamento automático para 0 nós quando estiver ocioso para economizar custo.  Ao usá-lo novamente após um atraso, você pode experimentar novamente aproximadamente 5 minutos de tempo de espera enquanto ele é dimensionado novamente.
