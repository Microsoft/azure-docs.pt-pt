---
title: Gerir réplicas de leitura - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como gerir as réplicas de leitura Azure Database para PostgreSQL - Servidor Único a partir do portal Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: dd79618b8d9f016c92166edb9ecdb0bfb113947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768954"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL - Servidor Único a partir do portal Azure

Neste artigo, aprende-se a criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL a partir do portal Azure. Para saber mais sobre as réplicas de leitura, consulte a [visão geral.](concepts-read-replicas.md)


## <a name="prerequisites"></a>Pré-requisitos
Uma [Base de Dados Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md) para ser o servidor principal.

## <a name="prepare-the-master-server"></a>Preparar o servidor principal
Estes passos devem ser utilizados para preparar um servidor principal nos níveis Geral de Propósito ou Memória Otimizados. O servidor principal está preparado para a replicação, definindo o parâmetro azure.replication_support. Quando o parâmetro de replicação é alterado, é necessário reiniciar o servidor para que a alteração produza efeitos. No portal Azure, estes dois passos são encapsulado por um único botão, **Suporte de Replicação Ativa**.

1. No portal Azure, selecione a base de dados Azure existente para o servidor PostgreSQL para usar como mestre.

2. Na barra lateral do servidor, em **DEFINIÇÕES,** **selecione Replicação**.

> [!NOTE] 
> Se vir o suporte de **replicação desativado** acinzentado, as definições de replicação já estão definidas no seu servidor por padrão. Pode saltar os seguintes passos e ir criar uma réplica de leitura. 

3. Selecione **Ativar suporte de replicação**. 

   ![Ativar suporte de replicação](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Confirme que pretende ativar o suporte à replicação. Esta operação reiniciará o servidor principal. 

   ![Confirmar suporte de replicação de ativação](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Receberá duas notificações do portal Azure assim que a operação estiver concluída. Há uma notificação para atualizar o parâmetro do servidor. Há outra notificação para o reinício do servidor que se segue imediatamente.

   ![Notificações de sucesso - permitir](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Refresque a página do portal Azure para atualizar a barra de ferramentas de replicação. Agora pode criar réplicas de leitura para este servidor.

   ![Barra de ferramentas atualizada](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Ativar o suporte de replicação é uma operação única por servidor principal. Um botão de suporte de **replicação desativação** é fornecido para a sua conveniência. Não recomendamos que desapeça o suporte de replicação, a menos que tenha a certeza de que nunca criará uma réplica neste servidor principal. Não é possível desativar o suporte de replicação enquanto o seu servidor principal tiver réplicas existentes.


## <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Para criar uma réplica de leitura, siga estes passos:

1. Selecione a base de dados Azure existente para o servidor PostgreSQL para usar como servidor principal. 

2. Na barra lateral do servidor, em **DEFINIÇÕES,** **selecione Replicação**.

3. **Selecione Adicionar Réplica**.

   ![Adicione uma réplica](./media/howto-read-replicas-portal/add-replica.png)

4. Insira um nome para a réplica de leitura. 

    ![Diga o nome da réplica](./media/howto-read-replicas-portal/name-replica.png)

5. Selecione um local para a réplica. A localização padrão é a mesma do servidor principal.

    ![Selecionar uma localização](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Para saber mais sobre quais as regiões em que pode criar uma réplica, visite o artigo da [réplica de leitura.](concepts-read-replicas.md) 

6. Selecione **OK** para confirmar a criação da réplica.

Uma réplica é criada usando as mesmas definições de cálculo e armazenamento que o mestre. Após a criação de uma réplica, várias definições podem ser alteradas independentemente do servidor principal: geração de computação, vCores, armazenamento e período de retenção de back-up. O nível de preços também pode ser alterado de forma independente, exceto para ou a partir do nível Básico.

> [!IMPORTANT]
> Antes de uma definição de servidor principal ser atualizada para um novo valor, atualize a definição de réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

Após a criação da réplica de leitura, pode ser vista a partir da janela **de replicação:**

![Veja a nova réplica na janela de replicação](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Parar replicação
Pode parar a replicação entre um servidor principal e uma réplica de leitura.

> [!IMPORTANT]
> Depois de parar a replicação a um servidor principal e de uma réplica de leitura, não pode ser desfeita. A réplica de leitura torna-se um servidor autónomo que suporta tanto as leituras como as escritas. O servidor autónomo não pode voltar a ser transformado numa réplica.

Para parar a replicação entre um servidor principal e uma réplica de leitura do portal Azure, siga estes passos:

1. No portal Azure, selecione a sua base de dados master Azure para o servidor PostgreSQL.

2. No menu do servidor, em **DEFINIÇÕES,** **selecione Replication**.

3. Selecione o servidor de réplica para o qual parar a replicação.

   ![Selecione a réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecione **Parar a replicação**.

   ![Selecione a replicação de parar](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Selecione **OK** para parar a replicação.

   ![Confirme para parar a replicação](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Eliminar um servidor principal
Para eliminar um servidor principal, utiliza os mesmos passos que para eliminar uma Base de Dados Azure autónoma para servidor PostgreSQL. 

> [!IMPORTANT]
> Quando se apaga um servidor principal, a replicação a todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores autónomos que agora suportam tanto as leituras como as escritas.

Para eliminar um servidor do portal Azure, siga estes passos:

1. No portal Azure, selecione a sua base de dados master Azure para o servidor PostgreSQL.

2. Abra a página **de visão geral** para o servidor. Selecione **Eliminar**.

   ![Na página 'Overview' do servidor, selecione para eliminar o servidor principal](./media/howto-read-replicas-portal/delete-server.png)
 
3. Introduza o nome do servidor principal para apagar. **Selecione Excluir** para confirmar a eliminação do servidor principal.

   ![Confirme para eliminar o servidor principal](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Eliminar réplicas
Pode eliminar uma réplica de leitura semelhante à forma como elimina um servidor principal.

- No portal Azure, abra a página **de visão geral** para a réplica de leitura. Selecione **Eliminar**.

   ![Na página de visão geral da réplica, selecione para eliminar a réplica](./media/howto-read-replicas-portal/delete-replica.png)
 
Também pode eliminar a réplica da leitura da janela **de replicação** seguindo estes passos:

1. No portal Azure, selecione a sua base de dados master Azure para o servidor PostgreSQL.

2. No menu do servidor, em **DEFINIÇÕES,** **selecione Replication**.

3. Selecione a réplica de leitura para apagar.

   ![Selecione a réplica para eliminar](./media/howto-read-replicas-portal/select-replica.png)
 
4. **Selecione Eliminar a réplica**.

   ![Selecione eliminar réplica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Introduza o nome da réplica para apagar. **Selecione Excluir** para confirmar a eliminação da réplica.

   ![Confirme para apagar a réplica te](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorize uma réplica
Duas métricas estão disponíveis para monitorizar réplicas de leitura.

### <a name="max-lag-across-replicas-metric"></a>Métrica de Max Lag Através de Réplicas
A métrica **Max Lag Across Replicas** mostra o lag in bytes entre o servidor principal e a réplica mais atrasada. 

1.  No portal Azure, selecione a base de dados master Azure para o servidor PostgreSQL.

2.  Selecione **Métricas**. Na janela **Métricas,** selecione **Max Lag Através de Réplicas**.

    ![Monitorize o desfasamento máximo através de réplicas](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Para a sua **Agregação,** selecione **Max**.


### <a name="replica-lag-metric"></a>Métrica de Lag de Réplica
A métrica **do Ré Réplica** mostra o tempo desde a última transação reproduzida numa réplica. Se não houver transações que ocorram no seu mestrado, a métrica reflete este atraso de tempo.

1. No portal Azure, selecione a base de dados Azure para a réplica de leitura PostgreSQL.

2. Selecione **Métricas**. Na janela **Métricas,** selecione **Réplica Lag**.

   ![Monitorize o lag da réplica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Para a sua **Agregação,** selecione **Max**. 
 
## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [réplicas de leitura na Base de Dados Azure para PostgreSQL](concepts-read-replicas.md).
* Aprenda a [criar e gerir réplicas de leitura no Azure CLI e REST API](howto-read-replicas-cli.md).