---
title: 'Quickstart: Pause & resume compute - Portal Azure '
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
ms.openlocfilehash: 49684e6844c2d9f58e9b750b12991428218e4426
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200648"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>Quickstart: Pausa e retoma a computação na piscina Azure Synapse Analytics SQL no portal Azure

Utilize o portal Azure para parar a computação para a piscina SQL para economizar custos. [Retome](sql-data-warehouse-manage-compute-overview.md) a computação quando estiver pronto para utilizar o armazém de dados.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Use [Create and Connect - portal](create-data-warehouse-portal.md) para criar um pool SQL chamado **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Computação de pausa

Para poupar custos, pode parar e retomar os recursos de computação a pedido. Por exemplo, se não utilizar a base de dados durante a noite e aos fins de semana, pode fazer uma pausa durante esses tempos e retomá-la durante o dia. Não será cobrado pelos recursos da computação enquanto a base de dados estiver em pausa. No entanto, continuará a ser cobrado para armazenamento. 

Siga estes passos para fazer uma pausa numa piscina SQL.

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

Está a ser cobrado pelas unidades de armazém de dados e pelos dados armazenados no seu armazém de dados. Estes recursos de computação e armazenamento são faturados em separado. 

- Se quiser manter os dados armazenados, faça uma pausa no cálculo.
- Se quiser remover futuras tarifas, pode eliminar a piscina SQL. 

Siga estes passos para limpar os recursos conforme quiser.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e clique na sua piscina SQL.

    ![Limpar recursos](media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Para interromper a computação, clique no botão **Pausar**. 

2. Para remover a piscina SQL para que não seja cobrado para computação ou armazenamento, clique **em Eliminar**.

3. Para remover o servidor SQL que criou, clique **em sqlpoolservername.database.windows.net**, e, em seguida, clique em **Eliminar**.  Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

4. Para remover o grupo de recursos, clique em **myResourceGroup** e, em seguida, clique em **Eliminar grupo de recursos**.


## <a name="next-steps"></a>Passos seguintes

Fez agora uma pausa e retomou a computação para a sua piscina SQL. Para os próximos passos continuam no tutorial para carregar dados.

> [!div class="nextstepaction"]
> [Carregue os dados no pool SQL](load-data-from-azure-blob-storage-using-polybase.md)
