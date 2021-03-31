---
title: 'Quickstart: Pausa e retomar o cálculo na piscina SQL dedicada através do portal Azure'
description: Utilize o portal Azure para fazer uma pausa no cálculo para uma piscina SQL dedicada para economizar custos. Retomar o cálculo quando estiver pronto para utilizar o armazém de dados.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f9cc5083c4b515454b9d5cbc40ed3b48cba80211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602167"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>Quickstart: Pausa e retomar o cálculo na piscina SQL dedicada através do portal Azure

Você pode usar o portal Azure para parar e retomar os recursos dedicados de cálculo de piscina SQL. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Use [Create and Connect - portal](../quickstart-create-sql-pool-portal.md) para criar uma piscina SQL dedicada chamada **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Cálculo de pausa

Para reduzir custos, pode parar e retomar os recursos de cálculo a pedido. Por exemplo, se não utilizar a base de dados durante a noite e aos fins de semana, pode fazer uma pausa durante esses horários e retomá-la durante o dia.
 
>[!NOTE]
>Não será cobrado por recursos de computação enquanto a base de dados estiver em pausa. No entanto, continuará a ser cobrado para armazenamento. 

Siga estes passos para fazer uma pausa numa piscina DE SQL dedicada:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue até você é a página **de piscina dedicada SQL** para abrir a piscina SQL. 
3. O **Estado do** Aviso está **Online**.

    ![Computação online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Para fazer uma pausa na piscina SQL dedicada, clique no botão **Pausa.** 
5. Uma pergunta de confirmação parece perguntar se quer continuar. Clique em **Sim**.
6. Espere alguns momentos e, em seguida, note que o **Estado** é **pausa.**

    ![A screenshot mostra o portal Azure para um armazém de dados de amostra com um valor de Estado de Pausa.](./media/pause-and-resume-compute-portal/pausing.png)

7. Quando a operação de pausa estiver concluída, o estado é **pausado** e o botão de opção é **Retomar**.
8. Os recursos de computação para a piscina dedicada SQL estão agora offline. Não será cobrado por computação até retomar o serviço.

    ![Compute offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Retomar o cálculo

Siga estes passos para retomar uma piscina SQL dedicada.

1. Navegue até você é a página **de piscina dedicada SQL** para abrir a piscina SQL.
3. Na página **mySampleDataWarehouse,** note **que o Estado** é **pausado**.

    ![Compute offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Para retomar a piscina SQL, clique em **Currículo**. 
1. Uma pergunta de confirmação parece perguntar se quer começar. Clique em **Sim**.
1. Note que o **Estado** é **Retomar**.

    ![A screenshot mostra o portal Azure para um armazém de dados de amostra com o botão Iniciar selecionado e um valor de Estado de Resumo.](./media/pause-and-resume-compute-portal/resuming.png)

1. Quando a piscina SQL está novamente on-line, o estado está **online** e o botão de opção é **Pausa**.
1. Os recursos compute para piscina SQL estão agora on-line e você pode usar o serviço. Os encargos com o cálculo foram retomados.

    ![Computação online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Está a ser cobrado pelas unidades de armazém de dados e pelos dados armazenados na sua piscina de SQL dedicada. Estes recursos de computação e armazenamento são faturados em separado. 

- Se quiser manter os dados armazenados, faça uma pausa no cálculo.
- Se quiser remover as cargas futuras, pode eliminar a piscina SQL dedicada. 

Siga estes passos para limpar os recursos conforme quiser.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e selecione a sua piscina SQL dedicada.

    ![Limpar os recursos](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Para interromper a computação, clique no botão **Pausar**. 

1. Para remover a piscina SQL dedicada para que não esteja carregada para computação ou armazenamento, clique em **Apagar**.



## <a name="next-steps"></a>Passos seguintes

Já fez uma pausa e retomou o cálculo para a sua piscina DE SQL dedicada. Continue para o próximo artigo para saber mais sobre como [carregar dados numa piscina de SQL dedicada.](./load-data-from-azure-blob-storage-using-copy.md) Para obter informações adicionais sobre a gestão das capacidades de computação, consulte o artigo [de visão geral do cálculo do](sql-data-warehouse-manage-compute-overview.md) cálculo.