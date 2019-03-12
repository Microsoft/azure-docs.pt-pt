---
title: 'Início rápido: Colocar em pausa e retomar a computação do Azure SQL Data Warehouse - portal do Azure | Documentos da Microsoft'
description: Utilize o portal do Azure para computação em pausa no Azure SQL Data Warehouse para reduzir os custos. Retomar a computação quando estiver pronto para utilizar o armazém de dados.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9c3ed6dd79d6225b38751c910253cfa1f0720d1c
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731974"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Início rápido: Colocar em pausa e retomar a computação para um Azure SQL Data Warehouse no portal do Azure

Utilize o portal do Azure para computação em pausa no Azure SQL Data Warehouse para reduzir os custos. [Retomar a computação](sql-data-warehouse-manage-compute-overview.md) quando estiver pronto para utilizar o armazém de dados.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Uso [criar e ligar - portal](create-data-warehouse-portal.md) para criar um armazém de dados chamado **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Computação em pausa

Para reduzir os custos, pode colocar em pausa e retomar a computação recursos sob demanda. Por exemplo, se não utilizará a base de dados durante a noite e no fim de semana, pode colocar em pausa durante essas horas e retomá-lo durante o dia. Não será cobrado para recursos de computação enquanto a base de dados está em pausa. No entanto, continuará a ser cobrado o armazenamento. 

Siga estes passos para colocar em pausa um SQL data warehouse.

1. Clique em **bases de dados SQL** na página da esquerda do Portal do Azure.
2. Selecione **mySampleDataWarehouse** da página de **bases de dados SQL**. Esta ação abre o armazém de dados. 
3. Sobre o **mySampleDataWarehouse** página, repare **estado** é **Online**.

    ![Computação online](media/pause-and-resume-compute-portal/compute-online.png)

4. Para colocar em pausa o armazém de dados, clique nas **colocar em pausa** botão. 
5. Uma pergunta de confirmação é exibido, perguntando se quiser continuar. Clique em **Sim**.
6. Aguarde uns momentos e, em seguida, observe a **Status** é **colocar em pausa**.

    ![A colocar em pausa](media/pause-and-resume-compute-portal/pausing.png)

7. Quando a operação de colocar em pausa estiver concluída, o estado é **em pausa** e o botão de opção é **iniciar**.
8. Os recursos de computação para o armazém de dados agora estão offline. Não será cobrado pela computação até que a retomar o serviço.

    ![Computação offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Computação de retomar

Siga estes passos para retomar um SQL data warehouse.

1. Clique em **bases de dados SQL** na página da esquerda do Portal do Azure.
2. Selecione **mySampleDataWarehouse** da página de **bases de dados SQL**. Esta ação abre o armazém de dados. 
3. Sobre o **mySampleDataWarehouse** página, repare **estado** é **em pausa**.

    ![Computação offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Para retomar o armazém de dados, clique em **iniciar**. 
5. Uma pergunta de confirmação é exibido, perguntando se deseja iniciar. Clique em **Sim**.
6. Observe que o **Status** é **retomar**.

    ![A retomar](media/pause-and-resume-compute-portal/resuming.png)

7. Quando o armazém de dados esteja novamente online, o estado é **Online** e o botão de opção é **colocar em pausa**.
8. Os recursos de computação para o armazém de dados agora estão online e pode utilizar o serviço. Custos de computação têm retomado.

    ![Computação online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Limpar recursos

A ser cobrado para unidades de armazém de dados e os dados armazenados no seu armazém de dados. Estes recursos de computação e armazenamento são faturados em separado. 

- Se pretender manter os dados no armazenamento, interromper a computação.
- Se quiser remover futuras cobranças, pode eliminar o armazém de dados. 

Siga estes passos para limpar os recursos conforme quiser.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e clique no seu armazém de dados.

    ![Limpar recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Para interromper a computação, clique no botão **Pausar**. Quando o armazém de dados estiver em pausa, verá um botão **Iniciar**.  Para retomar a computação, clique em **Iniciar**.

2. Para remover o armazém de dados para não lhe ser cobrada a computação ou o armazenamento, clique em **Eliminar**.

3. Para remover o servidor SQL que criou, clique em **mynewserver 20171113.database.windows.net**e, em seguida, clique em **eliminar**.  Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

4. Para remover o grupo de recursos, clique em **myResourceGroup** e, em seguida, clique em **Eliminar grupo de recursos**.


## <a name="next-steps"></a>Passos Seguintes

Agora está em pausa e retomar a computação para o seu armazém de dados. Para saber mais sobre o Azure SQL Data Warehouse, avance para o tutorial para carregar dados.

> [!div class="nextstepaction"]
> [Carregar dados para o SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
