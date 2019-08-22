---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: eb84dc1b5bf3f756e484ef27aaa998ab6b94cc51
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891653"
---
Um experimento é executado em um destino de computação, um recurso de computação anexado ao seu espaço de trabalho.  Depois de criar um destino de computação, você poderá reutilizá-lo para execuções futuras.

1. Selecione **executar** na parte inferior para executar o experimento.

1. Quando a caixa de diálogo **Configurar destinos de computação** for exibida, se o seu espaço de trabalho já tiver um recurso de computação, você poderá selecioná-lo agora.  Caso contrário, selecione **criar novo**.

    > [!NOTE]
    > A interface visual só pode executar experimentos em destinos de Computação do Machine Learning. Outros destinos de computação não serão mostrados.

1. Forneça um nome para o recurso de computação.

1. Selecione **Executar**.

    ![Configurar destino de computação](./media/aml-ui-create-training-compute/set-compute.png)

    O recurso de computação será criado agora. Exiba o status no canto superior direito do experimento. 

    > [!NOTE]
    > Leva aproximadamente 5 minutos para criar um recurso de computação. Depois que o recurso for criado, você poderá reutilizá-lo e ignorar esse tempo de espera para execuções futuras.
    >
    > O recurso de computação fará o dimensionamento automático para 0 nós quando estiver ocioso para economizar custo.  Ao usá-lo novamente após um atraso, você pode experimentar novamente aproximadamente 5 minutos de tempo de espera enquanto ele é dimensionado novamente.
