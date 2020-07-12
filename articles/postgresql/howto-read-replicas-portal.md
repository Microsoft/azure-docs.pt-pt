---
title: Gerir réplicas de leitura - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como gerir as réplicas de leitura Azure Database for PostgreSQL - Single Server a partir do portal Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 8ca4d3d2d52e79dbcaaa15eba5794a4d2d28366a
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86274548"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL - Servidor Único a partir do portal Azure

Neste artigo, aprende-se a criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL a partir do portal Azure. Para saber mais sobre as réplicas lidas, consulte a [visão geral.](concepts-read-replicas.md)


## <a name="prerequisites"></a>Pré-requisitos
Uma [base de dados Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md) ser o servidor principal.

## <a name="azure-replication-support"></a>Suporte de replicação do Azure

[Leia réplicas](concepts-read-replicas.md) e [descodões lógicas](concepts-logical.md) ambos dependem do postgres escrever antecipadamente log (WAL) para obter informações. Estas duas funcionalidades precisam de diferentes níveis de registo de postgres. A descodão lógica precisa de um nível mais elevado de registo do que réplicas lidas.

Para configurar o nível certo de registo, utilize o parâmetro de suporte de replicação Azure. O suporte de replicação Azure tem três opções de definição:

* **Off** - Coloca a menor informação no WAL. Esta definição não está disponível na maioria dos servidores Azure Database para servidores PostgreSQL.  
* **Réplica** - Mais verboso do que **desligado**. Este é o nível mínimo de exploração madeireira necessário para [que as réplicas de leitura](concepts-read-replicas.md) funcionem. Esta definição é o padrão na maioria dos servidores.
* **Lógico** - Mais verboso do que **réplica.** Este é o nível mínimo de exploração madeireira para a descodão lógica para funcionar. As réplicas de leitura também funcionam neste cenário.

O servidor precisa de ser reiniciado após uma alteração deste parâmetro. Internamente, este parâmetro define os parâmetros postgres, `wal_level` `max_replication_slots` e `max_wal_senders` .

## <a name="prepare-the-master-server"></a>Prepare o servidor principal

1. No portal Azure, selecione uma Base de Dados Azure existente para o servidor PostgreSQL para utilizar como mestre.

2. A partir do menu do servidor, selecione **Replication**. Se o suporte de replicação do Azure estiver definido para pelo menos **replica,** pode criar réplicas de leitura. 

3. Se o suporte de replicação do Azure não estiver definido para, pelo menos, **replicar,** desate-o. Selecione **Guardar**.

   ![Base de Dados Azure para PostgreSQL - Replicação - Definir réplica e salvar](./media/howto-read-replicas-portal/set-replica-save.png)

4. Reinicie o servidor para aplicar a alteração selecionando **Sim**.

   ![Base de Dados Azure para PostgreSQL - Replicação - Confirme o reinício](./media/howto-read-replicas-portal/confirm-restart.png)

5. Receberá duas notificações do portal Azure assim que a operação estiver concluída. Há uma notificação para atualizar o parâmetro do servidor. Há outra notificação para o reinício do servidor que se segue imediatamente.

   ![Notificações de sucesso](./media/howto-read-replicas-portal/success-notifications.png)

6. Atualize a página do portal Azure para atualizar a barra de ferramentas de replicação. Agora pode criar réplicas de leitura para este servidor.
   

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Para criar uma réplica de leitura, siga estes passos:

1. Selecione uma base de dados de Azure existente para o servidor PostgreSQL para utilizar como servidor principal. 

2. Na barra lateral do servidor, em **DEFINIÇÕES,** selecione **Replicação**.

3. Selecione **Adicionar Réplica**.

   ![Adicione uma réplica](./media/howto-read-replicas-portal/add-replica.png)

4. Insira um nome para a réplica de leitura. 

    ![Diga a réplica](./media/howto-read-replicas-portal/name-replica.png)

5. Selecione um local para a réplica. A localização padrão é a mesma do servidor principal.

    ![Selecionar uma localização](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md) 

6. Selecione **OK** para confirmar a criação da réplica.

Após a criação da réplica de leitura, pode ser visualizada a partir da janela de **replicação:**

![Ver a nova réplica na janela de replicação](./media/howto-read-replicas-portal/list-replica.png)
 

> [!IMPORTANT]
> Reveja a [secção de considerações da visão geral da Réplica de Leitura.](concepts-read-replicas.md#considerations)
>
> Antes de uma definição de servidor principal ser atualizada para um novo valor, atualize a definição de réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

## <a name="stop-replication"></a>Parar replicação
Pode parar a replicação entre um servidor principal e uma réplica de leitura.

> [!IMPORTANT]
> Depois de parar a replicação num servidor principal e uma réplica de leitura, não pode ser desfeita. A réplica de leitura torna-se um servidor autónomo que suporta tanto as leituras como as escritas. O servidor autónomo não pode ser transformado numa réplica novamente.

Para parar a replicação entre um servidor principal e uma réplica de leitura do portal Azure, siga estes passos:

1. No portal Azure, selecione a sua base de dados Azure master para o servidor PostgreSQL.

2. No menu do servidor, em **DEFINIÇÕES,** selecione **Replication**.

3. Selecione o servidor de réplica para o qual parar a replicação.

   ![Selecione a réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4. **Selecione a replicação stop**.

   ![Selecione a replicação de stop](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Selecione **OK** para parar a replicação.

   ![Confirme parar a replicação](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Excluir um servidor principal
Para eliminar um servidor principal, utiliza os mesmos passos que eliminar uma Base de Dados Azure autónoma para servidor PostgreSQL. 

> [!IMPORTANT]
> Quando elimina um servidor principal, a replicação de todas as réplicas lidas é interrompida. As réplicas de leitura tornam-se servidores autónomos que agora suportam tanto as leituras como as escritas.

Para eliminar um servidor do portal Azure, siga estes passos:

1. No portal Azure, selecione a sua base de dados Azure master para o servidor PostgreSQL.

2. Abra a página **'Visão Geral'** para o servidor. Selecione **Eliminar**.

   ![Na página 'Visão geral' do servidor, selecione para eliminar o servidor principal](./media/howto-read-replicas-portal/delete-server.png)
 
3. Insira o nome do servidor principal para eliminar. **Selecione Eliminar** para confirmar a eliminação do servidor principal.

   ![Confirme para eliminar o servidor principal](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Eliminar réplicas
Pode eliminar uma réplica de leitura semelhante à forma como elimina um servidor principal.

- No portal Azure, abra a página **geral** para a réplica de leitura. Selecione **Eliminar**.

   ![Na página 'Visão geral' da réplica, selecione para eliminar a réplica](./media/howto-read-replicas-portal/delete-replica.png)
 
Também pode eliminar a réplica de leitura da janela **replicação** seguindo estes passos:

1. No portal Azure, selecione a sua base de dados Azure master para o servidor PostgreSQL.

2. No menu do servidor, em **DEFINIÇÕES,** selecione **Replication**.

3. Selecione a réplica de leitura para apagar.

   ![Selecione a réplica para eliminar](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecione **Eliminar réplica**.

   ![Selecione eliminar réplica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Introduza o nome da réplica para apagar. **Selecione Eliminar** para confirmar a eliminação da réplica.

   ![Confirme para eliminar a réplica te](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorize uma réplica
Estão disponíveis duas métricas para monitorizar réplicas de leitura.

### <a name="max-lag-across-replicas-metric"></a>Max Lag através da métrica de réplicas
A métrica **Max Lag Across Replicas** mostra o lag em bytes entre o servidor principal e a réplica mais atrasada. 

1.  No portal Azure, selecione a base de dados Azure master para o servidor PostgreSQL.

2.  Selecione **Métricas**. Na janela **Métricas,** selecione **Max Lag Across Replicas**.

    ![Monitorize o desfasamento máximo através de réplicas](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Para a sua **agregação,** selecione **Max**.


### <a name="replica-lag-metric"></a>Métrica de Lag de réplica
A métrica **Replica Lag** mostra o tempo desde a última transação reproduzida numa réplica. Se não houver transações no seu mestre, a métrica reflete este desfasamento temporal.

1. No portal Azure, selecione a Base de Dados Azure para a réplica de leitura PostgreSQL.

2. Selecione **Métricas**. Na janela **Métricas,** selecione **Replica Lag**.

   ![Monitorize o lag da réplica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Para a sua **agregação,** selecione **Max**. 
 
## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [as réplicas lidas na Base de Dados Azure para PostgreSQL](concepts-read-replicas.md).
* Saiba como [criar e gerir réplicas de leitura na ALI Azure e NA API REST.](howto-read-replicas-cli.md)