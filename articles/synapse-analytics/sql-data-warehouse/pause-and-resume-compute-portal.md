---
title: Pausa e retomar o cálculo na piscina Synapse SQL através do portal Azure
description: Utilize o portal Azure para fazer uma pausa no cálculo da piscina SQL para poupar custos. Retomar o cálculo quando estiver pronto para utilizar o armazém de dados.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: aa86161bce0b52eb1043d792fb2f4770df0992ee
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369421"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Quickstart: Pausa e retomar o cálculo na piscina Synapse SQL através do portal Azure

Pode utilizar o portal Azure para fazer uma pausa e retomar os recursos de cálculo do Pool Synapse SQL (data warehouse). Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Before you begin

Use [Create and Connect - portal](create-data-warehouse-portal.md) para criar uma piscina SQL chamada **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Cálculo de pausa

Para reduzir custos, pode parar e retomar os recursos de cálculo a pedido. Por exemplo, se não utilizar a base de dados durante a noite e aos fins de semana, pode fazer uma pausa durante esses horários e retomá-la durante o dia. 
>[!NOTE]
>Não será cobrado por recursos de computação enquanto a base de dados estiver em pausa. No entanto, continuará a ser cobrado para armazenamento. 

Siga estes passos para fazer uma pausa numa piscina SQL:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página de navegação esquerda do portal Azure.
2. Selecione **mySampleDataWarehouse** da página **Azure Synapse Analytics (anteriormente SQL DW)** para abrir a piscina SQL. 
3. Na página **mySampleDataWarehouse,** note **status** is **Online**.

    ![Computação online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Para fazer uma pausa na piscina SQL, clique no botão **Pausa.** 
5. Uma pergunta de confirmação parece perguntar se quer continuar. Clique em **Sim**.
6. Espere alguns momentos e, em seguida, note que o **Estado** é **pausa.**

    ![A screenshot mostra o portal Azure para um armazém de dados de amostra com um valor de Estado de Pausa.](./media/pause-and-resume-compute-portal/pausing.png)

7. Quando a operação de pausa estiver concluída, o estado é **pausado** e o botão de opção é **Retomar**.
8. Os recursos de computação para a piscina SQL estão agora offline. Não será cobrado por computação até retomar o serviço.

    ![Compute offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Retomar o cálculo

Siga estes passos para retomar a piscina SQL.

1. Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página esquerda do portal Azure.
2. Selecione **mySampleDataWarehouse** a partir da página **Azure Synapse Analytics (anteriormente SQL DW)** para abrir a página da piscina SQL. 
3. Na página **mySampleDataWarehouse,** note **que o Estado** é **pausado**.

    ![Compute offline](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Para retomar a piscina SQL, clique em **Currículo**. 
5. Uma pergunta de confirmação parece perguntar se quer começar. Clique em **Sim**.
6. Note que o **Estado** é **Retomar**.

    ![A screenshot mostra o portal Azure para um armazém de dados de amostra com o botão Iniciar selecionado e um valor de Estado de Resumo.](./media/pause-and-resume-compute-portal/resuming.png)

7. Quando a piscina SQL está novamente on-line, o estado está **online** e o botão de opção é **Pausa**.
8. Os recursos compute para piscina SQL estão agora on-line e você pode usar o serviço. Os encargos com o cálculo foram retomados.

    ![Computação online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Está a ser cobrado pelas unidades de armazém de dados e pelos dados armazenados na sua piscina SQL. Estes recursos de computação e armazenamento são faturados em separado. 

- Se quiser manter os dados armazenados, faça uma pausa no cálculo.
- Se quiser remover as cargas futuras, pode apagar a piscina SQL. 

Siga estes passos para limpar os recursos conforme quiser.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e clique na sua piscina SQL.

    ![Limpar os recursos](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Para interromper a computação, clique no botão **Pausar**. 

2. Para remover a piscina SQL para que não esteja cobrado para calcular ou armazenar, clique em **Eliminar**.

3. Para remover o servidor SQL que criou, clique **em sqlpoolservername.database.windows.net**e, em seguida, clique em **Apagar**.  

   > [!CAUTION]
   > Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, clique em **myResourceGroup** e, em seguida, clique em **Eliminar grupo de recursos**.


## <a name="next-steps"></a>Passos seguintes

Já fez uma pausa e retomou o cálculo para a sua piscina SQL. Continue até ao próximo artigo para saber mais sobre como [carregar dados na piscina SQL](load-data-from-azure-blob-storage-using-polybase.md). Para obter informações adicionais sobre a gestão das capacidades de computação, consulte o artigo [de visão geral do cálculo do](sql-data-warehouse-manage-compute-overview.md) cálculo. 

