---
title: Pausa e retomar a computação na piscina SYnapse SQL através do portal Azure
description: Utilize o portal Azure para parar a computação para a piscina SQL para economizar custos. Retome a computação quando estiver pronto para utilizar o armazém de dados.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3a131c1ebbf2a69f1c738cbc3421635406500d3c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130357"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Quickstart: Pausa e retoma a computação na piscina Synapse SQL através do portal Azure

Pode utilizar o portal Azure para parar e retomar os recursos de computação do synapse SQL (data warehouse). Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Use [Create and Connect - portal](create-data-warehouse-portal.md) para criar um pool SQL chamado **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Computação de pausa

Para reduzir custos, pode parar e retomar os recursos de computação a pedido. Por exemplo, se não utilizar a base de dados durante a noite e aos fins de semana, pode fazer uma pausa durante esses tempos e retomá-la durante o dia. 
>[!NOTE]
>Não será cobrado pelos recursos da computação enquanto a base de dados estiver em pausa. No entanto, continuará a ser cobrado para armazenamento. 

Siga estes passos para fazer uma pausa numa piscina SQL:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página de navegação esquerda do portal Azure.
2. Selecione **mySampleDataWarehouse** da página **Azure Synapse Analytics (anteriormente SQL DW)** para abrir a piscina SQL. 
3. Na página **mySampleDataWarehouse,** o **estado do** aviso está **online**.

    ![Compute online](media/pause-and-resume-compute-portal/compute-online.png)

4. Para parar a piscina SQL, clique no botão **Pausa.** 
5. Uma pergunta de confirmação aparece perguntando se quer continuar. Clique em **Sim**.
6. Espere alguns momentos e depois note que o **Estado** está **a fazer uma pausa.**

    ![Pausa](media/pause-and-resume-compute-portal/pausing.png)

7. Quando o funcionamento da pausa estiver concluído, o estado é **interrompido** e o botão de opção é **Retomado**.
8. Os recursos computacionais para o pool SQL estão agora offline. Não será cobrado pela computação até que retome o serviço.

    ![Compute offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Retomar a computação

Siga estes passos para retomar a piscina SQL.

1. Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página esquerda do portal Azure.
2. Selecione **mySampleDataWarehouse** da página **Azure Synapse Analytics (anteriormente SQL DW)** para abrir a página de piscina SQL. 
3. Na página **mySampleDataWarehouse,** o **estado do** aviso é **interrompido**.

    ![Compute offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Para retomar a piscina SQL, clique em **Resume**. 
5. Uma pergunta de confirmação aparece perguntando se quer começar. Clique em **Sim**.
6. Note que o **Estado** está **a retomar**.

    ![A retomar](media/pause-and-resume-compute-portal/resuming.png)

7. Quando a piscina SQL está novamente on-line, o estado é **Online** e o botão de opção é **Pausa**.
8. Os recursos computacionais para piscina SQL estão agora on-line e você pode usar o serviço. As acusações de computação foram retomadas.

    ![Compute online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Limpar recursos

Está a ser cobrado pelas unidades de armazém de dados e pelos dados armazenados na sua piscina SQL. Estes recursos de computação e armazenamento são faturados em separado. 

- Se quiser manter os dados armazenados, faça uma pausa no cálculo.
- Se quiser remover futuras tarifas, pode eliminar a piscina SQL. 

Siga estes passos para limpar os recursos conforme quiser.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e clique na sua piscina SQL.

    ![Limpar recursos](media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Para interromper a computação, clique no botão **Pausar**. 

2. Para remover a piscina SQL para que não seja cobrado para computação ou armazenamento, clique **em Eliminar**.

3. Para remover o servidor SQL que criou, clique **em sqlpoolservername.database.windows.net**, e, em seguida, clique em **Eliminar**.  

   > [!CAUTION]
   > Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, clique em **myResourceGroup** e, em seguida, clique em **Eliminar grupo de recursos**.


## <a name="next-steps"></a>Passos seguintes

Fez agora uma pausa e retomou a computação para a sua piscina SQL. Continue para o próximo artigo para saber mais sobre como [carregar dados em pool SQL](load-data-from-azure-blob-storage-using-polybase.md). Para obter informações adicionais sobre a gestão das capacidades computacionais, consulte o artigo de visão geral da [computação Manage.](sql-data-warehouse-manage-compute-overview.md) 

