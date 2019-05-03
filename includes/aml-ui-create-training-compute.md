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
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028519"
---
Uma experimentação é executada num destino de computação, um recurso de computação que está ligado à área de trabalho.  Depois de criar um destino de computação, pode ser reutilizado em execuções futuras.

1. Selecione **executar** na parte inferior para executar a experimentação.

     ![Execute experimentação](./media/aml-ui-create-training-compute/run-experiment.png)

1. Quando o **destinos de computação do programa de configuração** caixa de diálogo for apresentada, se a sua área de trabalho já tiver um recurso de computação, pode selecioná-lo agora.  Caso contrário, selecione **criar novo**.

    > [!NOTE]
    > A interface visual só pode executar experimentações em destinos de computação do Machine Learning. Outros destinos de computação não serão apresentados.

1. Forneça um nome para o recurso de computação.

1. Selecione **Executar**.

    ![Destino de computação do programa de configuração](./media/aml-ui-create-training-compute/set-compute.png)

    O recurso de computação agora será criado. Ver o estado no canto superior direito da experimentação. 

    > [!NOTE]
    > Demora cerca de 5 minutos para criar um recurso de computação. Depois do recurso é criado, pode reutilizá-lo e ignorar este tempo de espera para execuções futuras.
    >
    > O recurso de computação será automaticamente dimensionado para 0 nós, quando está ociosa para poupar no custo.  Quando usá-lo novamente após um atraso, pode ocorrer novamente cerca de 5 minutos de tempo de espera enquanto dimensionamento back.
