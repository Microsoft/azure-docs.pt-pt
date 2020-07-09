---
title: 'Quickstart: Configure o isolamento da carga de trabalho - Portal'
description: Utilize o portal Azure para configurar o isolamento da carga de trabalho.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 30862a0c16995e143df72f2a243419819941f54e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213045"
---
# <a name="quickstart-configure-synapse-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Quickstart: Configurar o isolamento da carga de trabalho da piscina da Synapse SQL utilizando um grupo de carga de trabalho no portal Azure

Neste arranque rápido, irá configurar o [isolamento da carga de trabalho](sql-data-warehouse-workload-isolation.md) criando um grupo de carga de trabalho para reservar recursos.  Para efeitos deste tutorial, criaremos o grupo de carga de trabalho para o carregamento de dados chamado `DataLoads` . O grupo de trabalho reservará 20% dos recursos do sistema.  Com 20% de isolamento para cargas de dados, são recursos garantidos que lhes permitem atingir SLAs.  Depois de criar o grupo de carga de trabalho, [crie um classificador de carga de trabalho](quickstart-create-a-workload-classifier-portal.md) para atribuir consultas a este grupo de carga de trabalho.


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.


## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

> [!NOTE]
> A criação de uma 22 2019 em Azure Synapse Analytics pode resultar num novo serviço de faturação.  Para mais informações, consulte [os preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart pressupõe que já tem uma instância de piscina SQL em Synapse SQL e que tem permissões de BASE DE DADOS DE CONTROLO. Se precisar de criar um, utilize [Criar e Ligar - Portal](create-data-warehouse-portal.md) para criar um armazém de dados chamado **mySampleDataWarehouse**.

>[!IMPORTANT] 
>A sua piscina SQL deve estar online para configurar a gestão da carga de trabalho. 

## <a name="configure-workload-isolation"></a>Configure o isolamento da carga de trabalho
Os recursos de piscina SQL podem ser isolados e reservados para cargas de trabalho específicas, criando grupos de carga de trabalho.  Consulte a documentação do conceito [de isolamento de carga de trabalho](sql-data-warehouse-workload-isolation.md) para obter mais detalhes sobre como os grupos de carga de trabalho o ajudam a gerir a sua carga de trabalho.  O [Startup De Criar e Ligar - portal](create-data-warehouse-portal.md) quickstart criou o **mySampleDataWarehouse** e iniciaisizou-o com 400 DWUs. Os passos seguintes criam um grupo de carga de trabalho no **mySampleDataWarehouse**.

Criar um grupo de carga de trabalho com 20% de isolamento:
1.  Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página esquerda do portal Azure.
2.  Selecione **mySampleDataWarehouse** a partir da página **Azure Synapse Analytics (anteriormente SQL DW).** A piscina SQL abre.
3.  Clique **na gestão da carga de trabalho.**
4.  Clique em **Novo grupo de carga de trabalho**.
5.  Clique **em Personalizado**.

    ![Clique em Personalizado](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  `DataLoads`Insira para o grupo **Workload**.
7.  `20`Insira para **min. recursos %**.
8.  `5`Insira **os recursos min. % por pedido**.
9.  `100`Insira para os recursos da Cap **%**.
10.   Clique em **Guardar**.

   ![Clicar em Guardar](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Uma notificação do portal aparece quando o grupo de carga de trabalho é criado.  Os recursos do grupo de carga de trabalho são apresentados nas tabelas abaixo dos valores configurados.

   ![Clique em final](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar o `DataLoads` grupo de carga de trabalho criado neste tutorial:
1. Clique no **`...`** direito do grupo de carga de `DataLoads` trabalho.
2. Clique no **grupo de carga de trabalho Delete**.
3. Clique em **Sim** quando solicitado para confirmar a eliminação do grupo de carga de trabalho.
4. Clique em **Guardar**.

   ![Clique em Eliminar](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Está a ser cobrado por unidades de armazém de dados e dados armazenados no seu armazém de dados. Estes recursos de computação e armazenamento são faturados em separado.

- Se quiser manter os dados no armazenamento, pode interromper a computação quando não estiver a utilizar o armazém de dados. Ao fazer uma pausa no cálculo, só é cobrado para armazenamento de dados. Quando estiver pronto para trabalhar com os dados, retome o cálculo.
- Se quiser remover futuras cobranças, pode eliminar o armazém de dados.

Siga estes passos para limpar os recursos.

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione no seu armazém de dados.

    ![Limpar recursos](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para parar o cálculo, selecione o **botão Pausa.** Quando o armazém de dados estiver em pausa, verá um botão **Iniciar**.  Para retomar o cálculo, selecione **Start**.

3. Para remover o armazém de dados para que não seja cobrado para calcular ou armazenar, selecione **Delete**.

4. Para remover o servidor SQL que criou, selecione **sqlpoolservername.database.windows.net** na imagem anterior e, em seguida, selecione **Delete**.  Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, selecione **myResourceGroup**e, em seguida, **selecione Delete resource group**.

## <a name="next-steps"></a>Passos seguintes

Para utilizar o `DataLoads` grupo de carga de trabalho, é necessário criar um [classificador](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) de carga de trabalho para encaminhar os pedidos para o grupo de carga de trabalho.  Continue a criar tutorial [de classificação de carga de trabalho](quickstart-create-a-workload-classifier-portal.md) para criar um classificador de carga de trabalho para `DataLoads` .

## <a name="see-also"></a>Ver também
Consulte o artigo Gerir e monitorizar a [Gestão da Carga de Trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) para obter detalhes sobre como monitorizar as cargas de trabalho para a gestão da carga de trabalho.
