---
title: Gerir réplicas de leitura - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e gerir réplicas de leitura na Base de Dados Azure para MariaDB usando o portal
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 3ca6ef3c368a5f578cc90fae3923caa89f3b076a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665009"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para MariaDB usando o portal Azure

Neste artigo, você vai aprender a criar e gerir réplicas de leitura na Base de Dados Azure para o serviço MariaDB usando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [Base de Dados Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) que será utilizado como servidor de origem.

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores MariaDB nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de origem está num destes níveis de preços.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando se cria uma réplica para uma fonte que não tem réplicas existentes, a fonte reinicia primeiro para se preparar para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico.

Um servidor de réplica de leitura pode ser criado usando os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione a base de dados Azure existente para o servidor MariaDB que pretende utilizar como mestre. Esta ação abre a página **de visão geral.**

3. Selecione **a replicação** do menu, em **DEFINIÇÕES**.

4. Selecione **Adicionar Réplica**.

   ![Base de Dados Azure para MariaDB - Replicação](./media/howto-read-replica-portal/add-replica.png)

5. Introduza um nome para o servidor de réplica.

    ![Base de Dados Azure para MariaDB - Nome de réplica](./media/howto-read-replica-portal/replica-name.png)

6. Selecione a localização para o servidor de réplica. A localização padrão é a mesma que a do servidor de origem.

    ![Base de Dados Azure para MariaDB - Localização de réplica](./media/howto-read-replica-portal/replica-location.png)

7. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração do servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de ter sido criada. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores à fonte para garantir que a réplica seja capaz de acompanhar o mestre.

Uma vez criado o servidor de réplica, pode ser visto a partir da lâmina de **replicação.**

   ![Base de Dados Azure para MariaDB - Lista de réplicas](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação num servidor é irreversível. Uma vez que a replicação tenha parado entre uma fonte e uma réplica, não pode ser desfeita. O servidor de réplica torna-se então um servidor autónomo e agora suporta tanto a leitura como a escrita. Este servidor não pode ser transformado numa réplica novamente.

Para parar a replicação entre uma fonte e um servidor de réplica do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a base de dados Azure de origem para o servidor MariaDB. 

2. Selecione **a replicação** do menu, em **DEFINIÇÕES**.

3. Selecione o servidor de réplica para o quais deseja parar a replicação.

   ![Base de Dados Azure para MariaDB - Parar o servidor de seleção de replicação](./media/howto-read-replica-portal/stop-replication-select.png)

4. **Selecione a replicação stop**.

   ![Base de Dados Azure para MariaDB - Parar a replicação](./media/howto-read-replica-portal/stop-replication.png)

5. Confirme que pretende parar a replicação clicando **OK**.

   ![Azure Database for MariaDB - Parar a replicação confirmar](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

Para eliminar um servidor de réplica de leitura do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a base de dados Azure de origem para o servidor MariaDB.

2. Selecione **a replicação** do menu, em **DEFINIÇÕES**.

3. Selecione o servidor de réplica que deseja eliminar.

   ![Base de Dados Azure para MariaDB - Eliminar servidor selecionado de réplicas](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selecione **Eliminar réplica**

   ![Base de Dados Azure para MariaDB - Eliminar réplica](./media/howto-read-replica-portal/delete-replica.png)

5. Digite o nome da réplica e clique em **Eliminar** para confirmar a eliminação da réplica.  

   ![Azure Database for MariaDB - Eliminar réplica confirmar](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> Eliminar um servidor de origem interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor de origem. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor de origem do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a base de dados Azure de origem para o servidor MariaDB.

2. A partir do **Resumo,** selecione **Delete**.

   ![Azure Database for MariaDB - Delete master](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digite o nome do servidor de origem e clique em **Eliminar** para confirmar a eliminação do servidor de origem.  

   ![Azure Database for MariaDB - Eliminar master confirmar](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorizar a replicação

1. No [portal Azure,](https://portal.azure.com/)selecione a réplica Azure Database para o servidor MariaDB que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Métricas:**

3. Selecione **o lag de replicação em segundos** a partir da lista de métricas disponíveis.

   ![Selecione lag de replicação](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecione o intervalo de tempo que deseja visualizar. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   ![Selecione intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Ver o lag de replicação para o intervalo de tempo selecionado. A imagem abaixo mostra os últimos 30 minutos para uma grande carga de trabalho.

   ![Selecione intervalo de tempo 30 minutos](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)
