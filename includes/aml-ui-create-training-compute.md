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
ms.openlocfilehash: 28478f38df6ba6ea356626cc36c23ad498fc1f47
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692258"
---
Um pipeline é executado em um destino de computação, um recurso de computação anexado ao seu espaço de trabalho.  Depois de criar um destino de computação, você poderá reutilizá-lo para execuções futuras.

1. Selecione **executar** na parte superior da tela para executar o pipeline.

1. Quando o painel **configurações** for exibido, selecione **Selecionar destino de computação**.

    Se você já tiver um destino de computação disponível, poderá selecioná-lo para executar esse pipeline.

    > [!NOTE]
    > A interface visual só pode executar experimentos em destinos de Computação do Machine Learning. Outros destinos de computação não serão mostrados.

1. Forneça um nome para o recurso de computação.

1. Selecione **Guardar**.

    ![Configurar destino de computação](./media/aml-ui-create-training-compute/set-compute.png)

1. Selecione **Executar**.

1. Na caixa de diálogo **configurar execução de pipeline** , selecione **+ novo experimento** para o **experimento**

    * Insira um **nome de experimento** descritivo

    * Selecione **executar**
    
    Você pode exibir o status de execução e os detalhes no canto superior direito da tela.

    > [!NOTE]
    > Leva aproximadamente 5 minutos para criar um recurso de computação. Depois que o recurso for criado, você poderá reutilizá-lo e ignorar esse tempo de espera para execuções futuras.
    >
    > O recurso de computação fará o dimensionamento automático para 0 nós quando estiver ocioso para economizar custo.  Ao usá-lo novamente após um atraso, você pode experimentar novamente aproximadamente 5 minutos de tempo de espera enquanto ele é dimensionado novamente.
