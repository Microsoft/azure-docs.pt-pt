---
title: Gerir réplicas de leitura - Portal Azure - Base de Dados Azure para MySQL
description: Saiba como configurar e gerir réplicas de leitura na Base de Dados Azure para o MySQL utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 63ce078cc80b856fe09f3d2bbad1c6e96615eae8
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546811"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para o MySQL utilizando o portal Azure

Neste artigo, você vai aprender a criar e gerir réplicas de leitura no serviço Azure Database for MySQL utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [Base de Dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será utilizada como servidor de origem.

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores MySQL nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de origem está num destes níveis de preços.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando se cria uma réplica para uma fonte que não tem réplicas existentes, a fonte reinicia primeiro para se preparar para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico.

Um servidor de réplica de leitura pode ser criado usando os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione a base de dados Azure existente para o servidor MySQL que pretende utilizar como mestre. Esta ação abre a página **de visão geral.**

3. Selecione **a replicação** do menu, em **DEFINIÇÕES** .

4. Selecione **Adicionar Réplica** .

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

5. Introduza um nome para o servidor de réplica.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

6. Selecione a localização para o servidor de réplica. A localização padrão é a mesma que a do servidor de origem.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

   > [!NOTE]
   > Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md) 

7. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração do servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de ter sido criada. O servidor de réplica é sempre criado no mesmo grupo de recursos e na mesma subscrição que o servidor de origem. Se pretender criar um servidor de réplica para um grupo de recursos diferente ou uma subscrição diferente, pode [mover o servidor de réplica](../azure-resource-manager/management/move-resource-group-and-subscription.md) após a criação. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores à fonte para garantir que a réplica seja capaz de acompanhar o mestre.

Uma vez criado o servidor de réplica, pode ser visto a partir da lâmina de **replicação.**

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação num servidor é irreversível. Uma vez que a replicação tenha parado entre uma fonte e uma réplica, não pode ser desfeita. O servidor de réplica torna-se então um servidor autónomo e agora suporta tanto a leitura como a escrita. Este servidor não pode ser transformado numa réplica novamente.

Para parar a replicação entre uma fonte e um servidor de réplica do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a base de dados Azure de origem para o servidor MySQL. 

2. Selecione **a replicação** do menu, em **DEFINIÇÕES** .

3. Selecione o servidor de réplica para o quais deseja parar a replicação.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

4. **Selecione a replicação stop** .

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

5. Confirme que pretende parar a replicação clicando **OK** .

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

## <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

Para eliminar um servidor de réplica de leitura do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a base de dados Azure de origem para o servidor MySQL.

2. Selecione **a replicação** do menu, em **DEFINIÇÕES** .

3. Selecione o servidor de réplica que deseja eliminar.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

4. Selecione **Eliminar réplica**

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

5. Digite o nome da réplica e clique em **Eliminar** para confirmar a eliminação da réplica.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

## <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> Eliminar um servidor de origem interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor de origem. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor de origem do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a base de dados Azure de origem para o servidor MySQL.

2. A partir do **Resumo,** selecione **Delete** .

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

3. Digite o nome do servidor de origem e clique em **Eliminar** para confirmar a eliminação do servidor de origem.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

## <a name="monitor-replication"></a>Monitorizar a replicação

1. No [portal Azure,](https://portal.azure.com/)selecione a réplica Azure Database para o servidor MySQL que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Métricas:**

3. Selecione **o lag de replicação em segundos** a partir da lista de métricas disponíveis.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

4. Selecione o intervalo de tempo que deseja visualizar. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

5. Ver o lag de replicação para o intervalo de tempo selecionado. A imagem abaixo mostra os últimos 30 minutos.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)