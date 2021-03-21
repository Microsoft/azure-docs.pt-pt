---
title: Gerir réplicas de leitura - Portal Azure - Base de Dados Azure para MySQL - Servidor Flexível
description: Saiba como configurar e gerir réplicas de leitura na Base de Dados Azure para servidor flexível MySQL utilizando o portal Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: d33734dc7404e49aed94dffae8644b2bc4386925
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492833"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para o MySQL Flexible Server utilizando o portal Azure

> [!IMPORTANT]
> Leia réplicas na Base de Dados Azure para MySQL - Servidor Flexível está em pré-visualização.

Neste artigo, você aprenderá a criar e gerir réplicas de leitura na Base de Dados Azure para o servidor flexível MySQL usando o portal Azure.

> [!Note]
> A réplica não é suportada em servidor ativado por alta disponibilidade. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma [base de dados Azure para servidor flexível do servidor MySQL](quickstart-create-server-portal.md) que será usada como servidor de origem.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando se cria uma réplica para uma fonte que não tem réplicas existentes, a fonte reinicia primeiro para se preparar para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico.

Um servidor de réplica de leitura pode ser criado usando os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione a base de dados Azure existente para o servidor flexível MySQL que pretende utilizar como fonte. Esta ação abre a página **de visão geral.**

3. Selecione **a replicação** do menu, em **DEFINIÇÕES**.

4. Selecione **Adicionar Réplica**.

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Base de Dados Azure para MySQL - Replicação":::

5. Introduza um nome para o servidor de réplica.

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Base de Dados Azure para MySQL - Nome de réplica":::

6. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração do servidor que a fonte. A configuração do servidor de réplica pode ser alterada depois de ter sido criada. O servidor de réplica é sempre criado no mesmo grupo de recursos, na mesma localização e na mesma subscrição que o servidor de origem. Se pretender criar um servidor de réplica para um grupo de recursos diferente ou uma subscrição diferente, pode [mover o servidor de réplica](../../azure-resource-manager/management/move-resource-group-and-subscription.md) após a criação. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores à fonte para garantir que a réplica seja capaz de acompanhar a origem.

Uma vez criado o servidor de réplica, pode ser visto a partir da lâmina de **replicação.**

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Base de Dados Azure para MySQL - Réplicas da lista":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação num servidor é irreversível. Uma vez que a replicação tenha parado entre uma fonte e uma réplica, não pode ser desfeita. O servidor de réplica torna-se então um servidor autónomo e agora suporta tanto a leitura como a escrita. Este servidor não pode ser transformado numa réplica novamente.

Para parar a replicação entre uma fonte e um servidor de réplica do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a base de dados Azure de origem para servidor flexível MySQL. 

2. Selecione **a replicação** do menu, em **DEFINIÇÕES**.

3. Selecione o servidor de réplica para o quais deseja parar a replicação.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL - Parar o servidor de seleção de replicação":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. **Selecione a replicação stop**.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Base de Dados Azure para MySQL - Parar a replicação":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. Confirme que pretende parar a replicação clicando **OK**.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL - Parar a replicação confirmar":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

Para eliminar um servidor de réplica de leitura do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a base de dados Azure de origem para servidor flexível MySQL.

2. Selecione **a replicação** do menu, em **DEFINIÇÕES**.

3. Selecione o servidor de réplica que deseja eliminar.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL - Eliminar servidor selecionado de réplicas":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. Selecione **Eliminar réplica**

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL - Eliminar réplica":::

5. Digite o nome da réplica e clique em **Eliminar** para confirmar a eliminação da réplica.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL - Eliminar réplica confirmar":::

## <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> Eliminar um servidor de origem interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor de origem. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor de origem do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a base de dados Azure de origem para servidor flexível MySQL.

2. A partir do **Resumo,** selecione **Delete**.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Base de Dados Azure para MySQL - Eliminar fonte":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. Digite o nome do servidor de origem e clique em **Eliminar** para confirmar a eliminação do servidor de origem.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL - Eliminar fonte confirmar":::

## <a name="monitor-replication"></a>Monitorizar a replicação

1. No [portal Azure,](https://portal.azure.com/)selecione a réplica Azure Database para o servidor flexível MySQL que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Métricas:**

3. Selecione **o lag de replicação em segundos** a partir da lista de métricas disponíveis.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="Selecione lag de replicação":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. Selecione o intervalo de tempo que deseja visualizar. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Selecione intervalo de tempo":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. Ver o lag de replicação para o intervalo de tempo selecionado. A imagem abaixo mostra os últimos 30 minutos.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Selecione intervalo de tempo 30 minutos":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)