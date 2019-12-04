---
title: Gerenciar réplicas de leitura-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como gerenciar réplicas de leitura banco de dados do Azure para PostgreSQL-servidor único do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 5e031354d4695a1d7eb6f199e23e74b796273230
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770225"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Criar e gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL-servidor único do portal do Azure

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL no portal do Azure. Para saber mais sobre réplicas de leitura, consulte a [visão geral](concepts-read-replicas.md).


## <a name="prerequisites"></a>Pré-requisitos
Um [banco de dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md) ser o servidor mestre.

## <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Essas etapas devem ser usadas para preparar um servidor mestre nas camadas de Uso Geral ou com otimização de memória. O servidor mestre está preparado para replicação definindo o parâmetro Azure. replication_support. Quando o parâmetro de replicação é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor. No portal do Azure, essas duas etapas são encapsuladas por um único botão, **habilitar o suporte de replicação**.

1. Na portal do Azure, selecione o banco de dados do Azure para servidor PostgreSQL existente a ser usado como mestre.

2. Na barra lateral do servidor, em **configurações**, selecione **replicação**.

3. Selecione **habilitar suporte à replicação**. 

   ![Habilitar suporte à replicação](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Confirme que você deseja habilitar o suporte à replicação. Esta operação reiniciará o servidor mestre. 

   ![Confirmar habilitar suporte à replicação](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Você receberá duas notificações de portal do Azure quando a operação for concluída. Há uma notificação para atualizar o parâmetro do servidor. Há outra notificação para a reinicialização do servidor que segue imediatamente.

   ![Notificações de êxito-habilitar](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Atualize a página de portal do Azure para atualizar a barra de ferramentas de replicação. Agora você pode criar réplicas de leitura para este servidor.

   ![Barra de ferramentas atualizada](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Habilitar o suporte de replicação é uma operação única por servidor mestre. Um botão para **desabilitar o suporte à replicação** é fornecido para sua conveniência. Não recomendamos desabilitar o suporte à replicação, a menos que você tenha certeza de que nunca criará uma réplica nesse servidor mestre. Não é possível desabilitar o suporte à replicação enquanto o servidor mestre tiver réplicas existentes.


## <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Para criar uma réplica de leitura, siga estas etapas:

1. Selecione o servidor do banco de dados do Azure para PostgreSQL existente a ser usado como servidor mestre. 

2. Na barra lateral do servidor, em **configurações**, selecione **replicação**.

3. Selecione **Adicionar réplica**.

   ![Adicionar uma réplica](./media/howto-read-replicas-portal/add-replica.png)

4. Insira um nome para a réplica de leitura. 

    ![Nomear a réplica](./media/howto-read-replicas-portal/name-replica.png)

5. Selecione um local para a réplica. O local padrão é o mesmo que o do servidor mestre.

    ![Selecionar um local](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo conceitos de leitura de réplica](concepts-read-replicas.md). 

6. Selecione **OK** para confirmar a criação da réplica.

Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: geração de computação, vCores, armazenamento e período de retenção de backup. O tipo de preço também pode ser alterado de forma independente, exceto para ou da camada básica.

> [!IMPORTANT]
> Antes que uma configuração de servidor mestre seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Essa ação ajuda a réplica a acompanhar as alterações feitas no mestre.

Depois que a réplica de leitura é criada, ela pode ser exibida na janela **replicação** :

![Exibir a nova réplica na janela replicação](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Parar replicação
Você pode interromper a replicação entre um servidor mestre e uma réplica de leitura.

> [!IMPORTANT]
> Depois de parar a replicação em um servidor mestre e em uma réplica de leitura, ela não pode ser desfeita. A réplica de leitura torna-se um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode ser tornado novamente em uma réplica.

Para interromper a replicação entre um servidor mestre e uma réplica de leitura do portal do Azure, siga estas etapas:

1. No portal do Azure, selecione o servidor mestre do banco de dados do Azure para PostgreSQL.

2. No menu servidor, em **configurações**, selecione **replicação**.

3. Selecione o servidor de réplica para o qual interromper a replicação.

   ![Selecionar a réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecione **parar replicação**.

   ![Selecione parar replicação](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Selecione **OK** para parar a replicação.

   ![Confirmar para parar a replicação](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Excluir um servidor mestre
Para excluir um servidor mestre, use as mesmas etapas para excluir um servidor autônomo do banco de dados do Azure para PostgreSQL. 

> [!IMPORTANT]
> Quando você exclui um servidor mestre, a replicação para todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores autônomos que agora dão suporte a leituras e gravações.

Para excluir um servidor do portal do Azure, siga estas etapas:

1. No portal do Azure, selecione o servidor mestre do banco de dados do Azure para PostgreSQL.

2. Abra a página **visão geral** do servidor. Selecione **Eliminar**.

   ![Na página Visão geral do servidor, selecione para excluir o servidor mestre](./media/howto-read-replicas-portal/delete-server.png)
 
3. Insira o nome do servidor mestre a ser excluído. Selecione **excluir** para confirmar a exclusão do servidor mestre.

   ![Confirmar para excluir o servidor mestre](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Excluir uma réplica
Você pode excluir uma réplica de leitura semelhante a como você exclui um servidor mestre.

- Na portal do Azure, abra a página **visão geral** da réplica de leitura. Selecione **Eliminar**.

   ![Na página Visão geral da réplica, selecione para excluir a réplica](./media/howto-read-replicas-portal/delete-replica.png)
 
Você também pode excluir a réplica de leitura da janela **replicação** seguindo estas etapas:

1. No portal do Azure, selecione o servidor mestre do banco de dados do Azure para PostgreSQL.

2. No menu servidor, em **configurações**, selecione **replicação**.

3. Selecione a réplica de leitura a ser excluída.

   ![Selecionar a réplica a ser excluída](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecione **excluir réplica**.

   ![Selecionar Excluir réplica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Insira o nome da réplica a ser excluída. Selecione **excluir** para confirmar a exclusão da réplica.

   ![Confirmar para excluir a réplica de te](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorar uma réplica
Duas métricas estão disponíveis para monitorar réplicas de leitura.

### <a name="max-lag-across-replicas-metric"></a>Atraso máximo entre as métricas de réplicas
A métrica de **atraso máximo entre réplicas** mostra o retardo em bytes entre o servidor mestre e a réplica mais deatrasante. 

1.  No portal do Azure, selecione o servidor mestre do banco de dados do Azure para PostgreSQL.

2.  Selecione **Métricas**. Na janela **métricas** , selecione **atraso máximo entre réplicas**.

    ![Monitorar o atraso máximo entre réplicas](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Para sua **agregação**, selecione **máximo**.


### <a name="replica-lag-metric"></a>Métrica de atraso de réplica
A métrica de **atraso de réplica** mostra o tempo desde a última transação reproduzida em uma réplica. Se não houver nenhuma transação ocorrendo no mestre, a métrica refletirá esse intervalo de tempo.

1. Na portal do Azure, selecione a réplica de leitura do banco de dados do Azure para PostgreSQL.

2. Selecione **Métricas**. Na janela **métricas** , selecione **atraso de réplica**.

   ![Monitorar a latência da réplica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Para sua **agregação**, selecione **máximo**. 
 
## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [réplicas de leitura no banco de dados do Azure para PostgreSQL](concepts-read-replicas.md).
* Saiba como [criar e gerenciar réplicas de leitura no CLI do Azure e na API REST](howto-read-replicas-cli.md).