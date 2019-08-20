---
title: 'Início rápido: Pausar e retomar a computação no Azure SQL Data Warehouse-portal do Azure | Microsoft Docs'
description: Use o portal do Azure para pausar a computação no Azure SQL Data Warehouse para economizar custos. Retome a computação quando estiver pronto para usar o data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 46fb1fc6455cd35d8bb1eaf0a1b7ee499f5c1caf
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574881"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Início rápido: Pausar e retomar a computação para uma SQL Data Warehouse do Azure no portal do Azure

Use o portal do Azure para pausar a computação no Azure SQL Data Warehouse para economizar custos. [Retome a computação](sql-data-warehouse-manage-compute-overview.md) quando estiver pronto para usar o data warehouse.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Use [Create e Connect-portal](create-data-warehouse-portal.md) para criar um data warehouse chamado **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausar computação

Para economizar custos, você pode pausar e retomar os recursos de computação sob demanda. Por exemplo, se você não estiver usando o banco de dados durante a noite e nos finais de semana, poderá pausá-lo durante esses horários e retomá-lo durante o dia. Você não será cobrado pelos recursos de computação enquanto o banco de dados estiver em pausa. No entanto, você continuará a ser cobrado pelo armazenamento. 

Siga estas etapas para pausar uma SQL Data Warehouse.

1. Clique em **bases de dados SQL** na página da esquerda do Portal do Azure.
2. Selecione **mySampleDataWarehouse** da página de **bases de dados SQL**. Esta ação abre o armazém de dados. 
3. Na página **mySampleDataWarehouse** , observe que **status** está **online**.

    ![Computação online](media/pause-and-resume-compute-portal/compute-online.png)

4. Para pausar a data warehouse, clique no botão Pausar. 
5. Uma pergunta de confirmação é exibida perguntando se você deseja continuar. Clique em **Sim**.
6. Aguarde alguns instantes e observe que o **status** está sendo **pausando**.

    ![A colocar em pausa](media/pause-and-resume-compute-portal/pausing.png)

7. Quando a operação de pausa for concluída, o status será pausado e o botão de opção será **iniciado**.
8. Os recursos de computação para os data warehouse agora estão offline. Você não será cobrado pela computação até que você retome o serviço.

    ![Computação offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Retomar computação

Siga estas etapas para retomar uma SQL Data Warehouse.

1. Clique em **bases de dados SQL** na página da esquerda do Portal do Azure.
2. Selecione **mySampleDataWarehouse** da página de **bases de dados SQL**. Esta ação abre o armazém de dados. 
3. Na página **mySampleDataWarehouse** , observe que **status** está em **pausa**.

    ![Computação offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Para retomar o data warehouse, clique em **Iniciar**. 
5. Uma pergunta de confirmação é exibida perguntando se você deseja iniciar. Clique em **Sim**.
6. Observe que o **status** é retomado.

    ![A retomar](media/pause-and-resume-compute-portal/resuming.png)

7. Quando o data warehouse está online novamente, o status é **online** e o botão de opçãoé pausar.
8. Os recursos de computação para os data warehouse agora estão online e você pode usar o serviço. Os encargos para computação foram retomados.

    ![Computação online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Limpar recursos

Você está sendo cobrado pelas unidades de data warehouse e pelos dados armazenados em seu data warehouse. Estes recursos de computação e armazenamento são faturados em separado. 

- Se você quiser manter os dados no armazenamento, Pause a computação.
- Se quiser remover futuras cobranças, pode eliminar o armazém de dados. 

Siga estes passos para limpar os recursos conforme quiser.

1. Entre no [portal do Azure](https://portal.azure.com)e clique em seu data warehouse.

    ![Limpar recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Para interromper a computação, clique no botão **Pausar**. Quando o armazém de dados estiver em pausa, verá um botão **Iniciar**.  Para retomar a computação, clique em **Iniciar**.

2. Para remover o armazém de dados para não lhe ser cobrada a computação ou o armazenamento, clique em **Eliminar**.

3. Para remover o SQL Server que você criou, clique em **mynewserver-20171113.Database.Windows.net**e em **excluir**.  Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

4. Para remover o grupo de recursos, clique em **myResourceGroup** e, em seguida, clique em **Eliminar grupo de recursos**.


## <a name="next-steps"></a>Passos seguintes

Agora você está pausado e retomou a computação para seu data warehouse. Para saber mais sobre o Azure SQL Data Warehouse, avance para o tutorial para carregar dados.

> [!div class="nextstepaction"]
> [Carregar dados em um SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
