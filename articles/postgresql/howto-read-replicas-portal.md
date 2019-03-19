---
title: Gerir réplicas de leitura da base de dados do Azure para PostgreSQL no portal do Azure
description: Saiba como gerir a base de dados do Azure para PostgreSQL ler réplicas a partir do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 24a37775298d6c6b40ec49f34158fcb77f26a379
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113219"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Criar e gerir réplicas de leitura a partir do portal do Azure

Neste artigo, saiba como criar e gerir réplicas de leitura na base de dados do Azure para PostgreSQL a partir do portal do Azure. Para saber mais acerca das réplicas de leitura, veja a [descrição geral](concepts-read-replicas.md).

> [!IMPORTANT]
> A funcionalidade de réplica de leitura está em pré-visualização pública.

## <a name="prerequisites"></a>Pré-requisitos
Uma [base de dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md) deve ser o servidor principal.

## <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Estes passos devem ser utilizados para preparar um servidor mestre nos escalões fins gerais ou com otimização de memória.

O `azure.replication_support` parâmetro deve ser definido como **RÉPLICA** no servidor principal. Quando este parâmetro for alterado, um reinício do servidor é necessário para que a alteração tenha efeito.

1. No portal do Azure, selecione Azure base de dados existente para o servidor PostgreSQL para utilizar como um modelo.

2. No menu da esquerda, selecione **parâmetros do servidor**.

3. Procure o `azure.replication_support` parâmetro.

   ![Pesquisa para o parâmetro azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Definir o `azure.replication_support` valor do parâmetro **RÉPLICA**. Selecione **guardar** para manter as suas alterações.

   ![Defina o parâmetro como RÉPLICA e guarde as alterações](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Depois de guardar as alterações, irá receber uma notificação:

   ![Guardar a notificação](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Reinicie o servidor para aplicar as alterações. Para saber como reiniciar um servidor, veja [reinicie uma base de dados do Azure para o servidor PostgreSQL](howto-restart-server-portal.md).


## <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Para criar uma réplica de leitura, siga estes passos:

1. Selecione Azure base de dados existente para o servidor PostgreSQL para utilizar como o servidor mestre. 

2. No menu do servidor, em **configurações**, selecione **replicação**.

   Se ainda não definir o `azure.replication_support` parâmetro **RÉPLICA** numa finalidade geral ou com otimização de memória principal do servidor e reinicie o servidor, irá receber uma notificação. Conclua esses passos antes de criar a réplica.

3. Selecione **Adicionar réplica**.

   ![Adicionar uma réplica](./media/howto-read-replicas-portal/add-replica.png)

4. Introduza um nome para a réplica de leitura. Selecione **OK** para confirmar a criação da réplica.

   ![Nome da réplica](./media/howto-read-replicas-portal/name-replica.png) 

Ao utilizar a mesma configuração de servidor como o mestre, é criada uma réplica. Depois de criar uma réplica, várias configurações podem ser alteradas independentemente do servidor mestre: computação geração, vCores, armazenamento e período de retenção de cópia de segurança. O escalão de preço também pode ser alterado de forma independente, exceto de ou para o escalão básico.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre é atualizada para novos valores, atualize a configuração de réplica para valores iguais ou superior. Esta ação garante que a réplica pode acompanhar todas as alterações efetuadas a mestre.

Depois de criar a réplica de leitura, ele pode ser visualizado a partir da **replicação** janela:

![Veja a nova réplica na janela de replicação](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Parar replicação
Pode parar a replicação entre um servidor principal e uma réplica de leitura.

> [!IMPORTANT]
> Depois de parar a replicação para um servidor principal e uma réplica de leitura, não pode ser anulada. A réplica de leitura torna-se um servidor autônomo que oferece suporte a leituras e gravações. O servidor autónomo não pode se transformar numa réplica novamente.

Para parar a replicação entre um servidor principal e uma réplica de leitura do portal do Azure, siga estes passos:

1. No portal do Azure, selecione a sua base de dados principal do Azure para o servidor PostgreSQL.

2. No menu do servidor, em **configurações**, selecione **replicação**.

3. Selecione o servidor de réplica para o qual pretende parar a replicação.

   ![Selecione a réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecione **paragem da replicação**.

   ![Selecione a paragem da replicação](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Selecione **OK** para parar a replicação.

   ![Confirme que pretende para parar a replicação](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Eliminar um servidor principal
Para eliminar um servidor principal, utilize os mesmos passos como para eliminar a base de dados autónomo para o servidor PostgreSQL. 

> [!IMPORTANT]
> Quando elimina um servidor principal, a replicação para todas as réplicas de leitura é parada. As réplicas de leitura tornam-se a servidores autónomos que agora oferecem suporte a leituras e gravações.

Para eliminar um servidor do portal do Azure, siga estes passos:

1. No portal do Azure, selecione a sua base de dados principal do Azure para o servidor PostgreSQL.

2. Abra o **descrição geral** página do servidor. Selecione **Eliminar**.

   ![Na página de descrição geral do servidor, selecione para eliminar o servidor mestre](./media/howto-read-replicas-portal/delete-server.png)
 
3. Introduza o nome do servidor principal para eliminar. Selecione **eliminar** para confirmar a eliminação do servidor mestre.

   ![Confirme que pretende para eliminar o servidor mestre](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Eliminar uma réplica
Pode eliminar uma réplica de leitura semelhante a como eliminar um servidor principal.

- No portal do Azure, abra a **descrição geral** página para a réplica de leitura. Selecione **Eliminar**.

   ![Na página de descrição geral da réplica, selecione para eliminar a réplica](./media/howto-read-replicas-portal/delete-replica.png)
 
Também pode eliminar a réplica de leitura do **replicação** janela seguindo estes passos:

1. No portal do Azure, selecione a sua base de dados principal do Azure para o servidor PostgreSQL.

2. No menu do servidor, em **configurações**, selecione **replicação**.

3. Selecione a réplica de leitura para eliminar.

   ![Selecione a réplica para eliminar](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecione **Eliminar réplica**.

   ![Selecione Eliminar réplica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Introduza o nome da réplica para eliminar. Selecione **eliminar** para confirmar a eliminação da réplica.

   ![Confirme que pretende para eliminar a réplica de te](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorizar uma réplica
Duas métricas estão disponíveis para monitorizar as réplicas de leitura.

### <a name="max-lag-across-replicas-metric"></a>Métrica de máximo de atraso em réplicas
O **máximo de atraso em réplicas** métrica mostra o desfasamento de bytes entre o servidor mestre e maioria lagging réplica. 

1.  No portal do Azure, selecione a base de dados principal do Azure para o servidor PostgreSQL.

2.  Selecione **Métricas**. Na **métricas** janela, selecione **máximo de atraso em réplicas**.

    ![Monitorizar o atraso máximo em réplicas](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Para sua **agregação**, selecione **Max**.


### <a name="replica-lag-metric"></a>Métrica de desfasamento de réplica
O **desfasamento de réplica** métrica mostra o tempo, uma vez que o último reproduzidos transação numa réplica. Se não houver nenhuma transação que ocorrem no seu controlador, a métrica reflete este intervalo de tempo.

1. No portal do Azure, selecione a base de dados do Azure para PostgreSQL a réplica de leitura.

2. Selecione **Métricas**. Na **métricas** janela, selecione **desfasamento de réplica**.

   ![Monitorizar o desfasamento de réplica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Para sua **agregação**, selecione **Max**. 
 
## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [ler réplicas na base de dados do Azure para PostgreSQL](concepts-read-replicas.md).