---
title: Gerir réplicas de leitura - Portal Azure - Base de Dados Azure para MySQL
description: Saiba como configurar e gerir réplicas de leitura na Base de Dados Azure para o MySQL utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: 17771cfa9ffa73d22d2e136836dcf0f19e68f8cb
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707213"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para o MySQL utilizando o portal Azure

Neste artigo, você vai aprender a criar e gerir réplicas de leitura no serviço Azure Database for MySQL utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [Base de Dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será usada como servidor principal.

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores MySQL nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor principal está num destes níveis de preços.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando se cria uma réplica para um mestre que não tem réplicas existentes, o mestre recomeçará a preparar-se para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico.

Um servidor de réplica de leitura pode ser criado usando os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione a base de dados Azure existente para o servidor MySQL que pretende utilizar como mestre. Esta ação abre a página **de visão geral.**

3. Selecione **a replicação** do menu, em **DEFINIÇÕES**.

4. Selecione **Adicionar Réplica**.

   ![Base de Dados Azure para MySQL - Replicação](./media/howto-read-replica-portal/add-replica.png)

5. Introduza um nome para o servidor de réplica.

    ![Base de Dados Azure para MySQL - Nome de réplica](./media/howto-read-replica-portal/replica-name.png)

6. Selecione a localização para o servidor de réplica. A localização padrão é a mesma do servidor principal.

    ![Base de Dados Azure para MySQL - Localização de réplica](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md) 

7. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração do servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de ter sido criada. O servidor de réplica é sempre criado no mesmo grupo de recursos e na mesma subscrição que o servidor principal. Se pretender criar um servidor de réplica para um grupo de recursos diferente ou uma subscrição diferente, pode [mover o servidor de réplica](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) após a criação. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores aos do mestre para garantir que a réplica seja capaz de acompanhar o mestre.

Uma vez criado o servidor de réplica, pode ser visto a partir da lâmina de **replicação.**

   ![Base de Dados Azure para MySQL - Réplicas da lista](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação num servidor é irreversível. Uma vez que a replicação tenha parado entre um mestre e uma réplica, não pode ser desfeita. O servidor de réplica torna-se então um servidor autónomo e agora suporta tanto a leitura como a escrita. Este servidor não pode ser transformado numa réplica novamente.

Para parar a replicação entre um mestre e um servidor de réplica do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a sua base de dados Azure master para o servidor MySQL. 

2. Selecione **a replicação** do menu, em **DEFINIÇÕES**.

3. Selecione o servidor de réplica para o quais deseja parar a replicação.

   ![Azure Database for MySQL - Parar o servidor de seleção de replicação](./media/howto-read-replica-portal/stop-replication-select.png)

4. **Selecione a replicação stop**.

   ![Base de Dados Azure para MySQL - Parar a replicação](./media/howto-read-replica-portal/stop-replication.png)

5. Confirme que pretende parar a replicação clicando **OK**.

   ![Azure Database for MySQL - Parar a replicação confirmar](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

Para eliminar um servidor de réplica de leitura do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a sua base de dados Azure master para o servidor MySQL.

2. Selecione **a replicação** do menu, em **DEFINIÇÕES**.

3. Selecione o servidor de réplica que deseja eliminar.

   ![Azure Database for MySQL - Eliminar servidor selecionado de réplicas](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selecione **Eliminar réplica**

   ![Azure Database for MySQL - Eliminar réplica](./media/howto-read-replica-portal/delete-replica.png)

5. Digite o nome da réplica e clique em **Eliminar** para confirmar a eliminação da réplica.  

   ![Azure Database for MySQL - Eliminar réplica confirmar](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Excluir um servidor principal

> [!IMPORTANT]
> Eliminar um servidor mestre interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor mestre. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor principal do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a sua base de dados Azure master para o servidor MySQL.

2. A partir do **Resumo,** selecione **Delete**.

   ![Azure Database for MySQL - Delete master](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digite o nome do servidor principal e clique em **Eliminar** para confirmar a eliminação do servidor principal.  

   ![Azure Database for MySQL - Delete master](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorizar a replicação

1. No [portal Azure,](https://portal.azure.com/)selecione a réplica Azure Database para o servidor MySQL que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Métricas:**

3. Selecione **o lag de replicação em segundos** a partir da lista de métricas disponíveis.

   ![Selecione lag de replicação](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecione o intervalo de tempo que deseja visualizar. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   ![Selecione intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Ver o lag de replicação para o intervalo de tempo selecionado. A imagem abaixo mostra os últimos 30 minutos.

   ![Selecione intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)
