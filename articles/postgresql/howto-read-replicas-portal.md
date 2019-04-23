---
title: Gerir réplicas de leitura da base de dados do Azure para PostgreSQL no portal do Azure
description: Saiba como gerir a base de dados do Azure para PostgreSQL ler réplicas a partir do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: bf1fb1c1343173949ecb6348284cb537282b277b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787668"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Criar e gerir réplicas de leitura a partir do portal do Azure

Neste artigo, saiba como criar e gerir réplicas de leitura na base de dados do Azure para PostgreSQL a partir do portal do Azure. Para saber mais acerca das réplicas de leitura, veja a [descrição geral](concepts-read-replicas.md).


## <a name="prerequisites"></a>Pré-requisitos
Uma [base de dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md) deve ser o servidor principal.

## <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Estes passos devem ser utilizados para preparar um servidor mestre nos escalões fins gerais ou com otimização de memória. O servidor mestre é preparado para replicação, definindo o parâmetro azure.replication_support. Quando o parâmetro de replicação é alterado, reiniciar o servidor é necessário para que a alteração tenha efeito. No portal do Azure, essas duas etapas são encapsuladas por um único botão, **ativar o suporte de replicação**.

1. No portal do Azure, selecione Azure base de dados existente para o servidor PostgreSQL para utilizar como um modelo.

2. Na barra lateral do servidor, em **configurações**, selecione **replicação**.

3. Selecione **ativar o suporte de replicação**. 

   ![Ativar o suporte de replicação](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Confirme que pretende ativar o suporte de replicação. Esta operação irá reiniciar o servidor mestre. 

   ![Confirmar a ativar o suporte de replicação](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Receberá notificações do portal do Azure dois depois de concluída a operação. Há uma notificação para o parâmetro de servidor de atualização. Há outra notificação para o reinício de servidor que segue imediatamente.

   ![Ativar notificações de sucesso-](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Atualize a página de portal do Azure para atualizar a barra de ferramentas de replicação. Agora, pode criar as réplicas de leitura para este servidor.

   ![Barra de ferramentas atualizada](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Ativar o suporte de replicação é uma operação única por servidor mestre. R **desativar o suporte de replicação** botão é disponibilizado para sua comodidade. Não recomendamos desativar o suporte de replicação, a menos que tenha a certeza de que nunca irá criar uma réplica neste servidor mestre. Não é possível desativar o suporte de replicação enquanto o servidor mestre tem réplicas existentes.


## <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Para criar uma réplica de leitura, siga estes passos:

1. Selecione Azure base de dados existente para o servidor PostgreSQL para utilizar como o servidor mestre. 

2. Na barra lateral do servidor, em **configurações**, selecione **replicação**.

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