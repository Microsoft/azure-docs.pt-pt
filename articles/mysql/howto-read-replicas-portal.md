---
title: Criar e gerir réplicas de leitura na base de dados do Azure para MySQL
description: Este artigo descreve como configurar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL usando o Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 275c7bc8127855712c191141ef681526da7c377b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309490"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL usando o portal do Azure

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no serviço de banco de dados do Azure para MySQL usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [base de dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será utilizado como o servidor mestre.

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a base de dados do Azure para MySQL servidores nos escalões de preços para fins gerais ou com otimização de memória. Certifique-se de que o servidor mestre está em um destes escalões de preço.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando as seguintes etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione o banco de dados do Azure para servidor MySQL existente que você deseja usar como mestre. Esta ação abre o **descrição geral** página.

3. Selecione **replicação** no menu, em **configurações**.

4. Selecione **Adicionar réplica**.

   ![Banco de dados do Azure para MySQL-replicação](./media/howto-read-replica-portal/add-replica.png)

5. Insira um nome para o servidor de réplica.

    ![Banco de dados do Azure para MySQL-nome da réplica](./media/howto-read-replica-portal/replica-name.png)

6. Selecione o local para o servidor de réplica. O local padrão é o mesmo que o do servidor mestre.

    ![Banco de dados do Azure para MySQL-local da réplica](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo conceitos de leitura de réplica](concepts-read-replicas.md). 

7. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> Réplicas de leitura são criadas com a mesma configuração de servidor como o modelo. A configuração do servidor de réplica pode ser alterada depois de este ter sido criado. Recomenda-se que a configuração do servidor de réplica deve ser mantida em valores iguais ou maiores do que o principal para garantir que a réplica é capaz de acompanhar o mestre.

Depois que o servidor de réplica tiver sido criado, ele poderá ser exibido na folha **replicação** .

   ![Banco de dados do Azure para MySQL-listar réplicas](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> A parar a replicação para um servidor é irreversível. Assim que a replicação foi parado entre um mestre e de réplica, não pode ser anulada. O servidor de réplica, em seguida, torna-se um servidor autónomo e agora oferece suporte a leitura e escrita. Este servidor não pode se transformar numa réplica novamente.

Para interromper a replicação entre um servidor mestre e uma réplica do portal do Azure, use as seguintes etapas:

1. No portal do Azure, selecione o servidor mestre do banco de dados do Azure para MySQL. 

2. Selecione **replicação** no menu, em **configurações**.

3. Selecione o servidor de réplica para o qual você deseja interromper a replicação.

   ![Banco de dados do Azure para MySQL-parar replicação selecionar servidor](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selecione **parar replicação**.

   ![Banco de dados do Azure para MySQL – parar a replicação](./media/howto-read-replica-portal/stop-replication.png)

5. Confirme se deseja interromper a replicação clicando em **OK**.

   ![Banco de dados do Azure para MySQL-parar confirmação de replicação](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Eliminar um servidor de réplica

Para excluir um servidor de réplica de leitura do portal do Azure, use as seguintes etapas:

1. No portal do Azure, selecione o servidor mestre do banco de dados do Azure para MySQL.

2. Selecione **replicação** no menu, em **configurações**.

3. Selecione o servidor de réplica que você deseja excluir.

   ![Banco de dados do Azure para MySQL-excluir réplica selecionar servidor](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selecionar **excluir réplica**

   ![Banco de dados do Azure para MySQL – excluir réplica](./media/howto-read-replica-portal/delete-replica.png)

5. Digite o nome da réplica e clique em **excluir** para confirmar a exclusão da réplica.  

   ![Banco de dados do Azure para MySQL-excluir confirmação de réplica](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Eliminar um servidor principal

> [!IMPORTANT]
> A eliminar um servidor principal para a replicação para todos os servidores de réplica e elimina o próprio servidor mestre. Servidores de réplica se tornar a servidores autónomos que agora oferecem suporte a leitura e escrita.

Para excluir um servidor mestre do portal do Azure, use as seguintes etapas:

1. No portal do Azure, selecione o servidor mestre do banco de dados do Azure para MySQL.

2. Na **visão geral**, selecione **excluir**.

   ![Banco de dados do Azure para MySQL – excluir mestre](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digite o nome do servidor mestre e clique em **excluir** para confirmar a exclusão do servidor mestre.  

   ![Banco de dados do Azure para MySQL – excluir mestre](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorar a replicação

1. Na [portal do Azure](https://portal.azure.com/), selecione o servidor de réplica do banco de dados do Azure para MySQL que você deseja monitorar.

2. Na seção **monitoramento** da barra lateral, selecione **métricas**:

3. Selecione **atraso de replicação em segundos** na lista suspensa de métricas disponíveis.

   ![Selecionar atraso de replicação](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecione o intervalo de tempo que você deseja exibir. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   ![Selecionar intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Exibir o atraso de replicação para o intervalo de tempo selecionado. A imagem abaixo exibe os últimos 30 minutos.

   ![Selecionar intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)