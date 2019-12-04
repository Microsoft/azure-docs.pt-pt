---
title: Gerenciar réplicas de leitura-portal do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o portal
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 7e290e6d773485b84ef42c7a79abf084e3b0da9f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765941"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o portal do Azure

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para o serviço MariaDB usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um [banco de dados do Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) que será usado como o servidor mestre.

> [!IMPORTANT]
> O recurso ler réplica só está disponível para o banco de dados do Azure para servidores MariaDB nos tipos de preço Uso Geral ou com otimização de memória. Verifique se o servidor mestre está em um desses tipos de preço.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando as seguintes etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione o banco de dados do Azure existente para o servidor MariaDB que você deseja usar como mestre. Essa ação abre a página **visão geral** .

3. Selecione **replicação** no menu, em **configurações**.

4. Selecione **Adicionar réplica**.

   ![Banco de dados do Azure para MariaDB-replicação](./media/howto-read-replica-portal/add-replica.png)

5. Insira um nome para o servidor de réplica.

    ![Banco de dados do Azure para MariaDB-nome da réplica](./media/howto-read-replica-portal/replica-name.png)

6. Selecione o local para o servidor de réplica. O local padrão é o mesmo que o do servidor mestre.

    ![Banco de dados do Azure para MariaDB-local da réplica](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > A replicação entre regiões está em versão prévia. Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo conceitos de leitura de réplica](concepts-read-replicas.md). 

7. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração de servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de ser criada. É recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores que o mestre para garantir que a réplica seja capaz de acompanhar o mestre.

Depois que o servidor de réplica tiver sido criado, ele poderá ser exibido na folha **replicação** .

   ![Banco de dados do Azure para MariaDB-listar réplicas](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação em um servidor de réplica

> [!IMPORTANT]
> Parar a replicação em um servidor é irreversível. Depois que a replicação for interrompida entre um mestre e uma réplica, ela não poderá ser desfeita. O servidor de réplica se torna um servidor autônomo e agora dá suporte à leitura e às gravações. Este servidor não pode ser tornado novamente em uma réplica.

Para interromper a replicação entre um servidor mestre e uma réplica do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione o servidor mestre do banco de dados do Azure para MariaDB. 

2. Selecione **replicação** no menu, em **configurações**.

3. Selecione o servidor de réplica para o qual você deseja interromper a replicação.

   ![Banco de dados do Azure para MariaDB-parar replicação selecionar servidor](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selecione **parar replicação**.

   ![Banco de dados do Azure para MariaDB-interromper replicação](./media/howto-read-replica-portal/stop-replication.png)

5. Confirme se deseja interromper a replicação clicando em **OK**.

   ![Banco de dados do Azure para MariaDB-parar confirmação de replicação](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

Para excluir um servidor de réplica de leitura do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione o servidor mestre do banco de dados do Azure para MariaDB.

2. Selecione **replicação** no menu, em **configurações**.

3. Selecione o servidor de réplica que você deseja excluir.

   ![Banco de dados do Azure para MariaDB-excluir réplica selecionar servidor](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selecionar **excluir réplica**

   ![Banco de dados do Azure para MariaDB-excluir réplica](./media/howto-read-replica-portal/delete-replica.png)

5. Digite o nome da réplica e clique em **excluir** para confirmar a exclusão da réplica.  

   ![Banco de dados do Azure para MariaDB-excluir confirmação de réplica](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Excluir um servidor mestre

> [!IMPORTANT]
> Eliminar um servidor mestre interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor mestre. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para excluir um servidor mestre do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione o servidor mestre do banco de dados do Azure para MariaDB.

2. Na **visão geral**, selecione **excluir**.

   ![Banco de dados do Azure para MariaDB-excluir mestre](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digite o nome do servidor mestre e clique em **excluir** para confirmar a exclusão do servidor mestre.  

   ![Banco de dados do Azure para MariaDB-excluir mestre](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorar a replicação

1. Na [portal do Azure](https://portal.azure.com/), selecione a réplica do banco de dados do Azure para o servidor MariaDB que você deseja monitorar.

2. Na seção **monitoramento** da barra lateral, selecione **métricas**:

3. Selecione **atraso de replicação em segundos** na lista suspensa de métricas disponíveis.

   ![Selecionar atraso de replicação](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecione o intervalo de tempo que você deseja exibir. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   ![Selecionar intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Exibir o atraso de replicação para o intervalo de tempo selecionado. A imagem abaixo exibe os últimos 30 minutos para uma carga de trabalho grande.

   ![Selecionar intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [réplicas de leitura](concepts-read-replicas.md)