---
title: Gerir réplicas de leitura - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como gerir as réplicas de leitura Azure Database for PostgreSQL - Single Server a partir do portal Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 9fdef187e9bdf77b29c548f767a4b4edfeb62f44
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422183"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL - Servidor Único a partir do portal Azure

Neste artigo, aprende-se a criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL a partir do portal Azure. Para saber mais sobre as réplicas lidas, consulte a [visão geral.](concepts-read-replicas.md)


## <a name="prerequisites"></a>Pré-requisitos
Uma [base de dados Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md) ser o servidor primário.

## <a name="azure-replication-support"></a>Suporte de replicação do Azure

[Leia réplicas](concepts-read-replicas.md) e [descodões lógicas](concepts-logical.md) ambos dependem do postgres escrever antecipadamente log (WAL) para obter informações. Estas duas funcionalidades precisam de diferentes níveis de registo de postgres. A descodão lógica precisa de um nível mais elevado de registo do que réplicas lidas.

Para configurar o nível certo de registo, utilize o parâmetro de suporte de replicação Azure. O suporte de replicação Azure tem três opções de definição:

* **Off** - Coloca a menor informação no WAL. Esta definição não está disponível na maioria dos servidores Azure Database para servidores PostgreSQL.  
* **Réplica** - Mais verboso do que **desligado**. Este é o nível mínimo de exploração madeireira necessário para [que as réplicas de leitura](concepts-read-replicas.md) funcionem. Esta definição é o padrão na maioria dos servidores.
* **Lógico** - Mais verboso do que **réplica.** Este é o nível mínimo de exploração madeireira para a descodão lógica para funcionar. As réplicas de leitura também funcionam neste cenário.


> [!NOTE]
> Ao implementar réplicas de leitura para cargas primárias intensivas persistentes, o lag de replicação pode continuar a crescer e pode nunca ser capaz de recuperar com as primárias. Isto também pode aumentar o uso de armazenamento na primária, uma vez que os ficheiros WAL não são eliminados até que sejam recebidos na réplica.

## <a name="prepare-the-primary-server"></a>Preparar o servidor primário

1. No portal Azure, selecione uma Base de Dados Azure existente para o servidor PostgreSQL para utilizar como mestre.

2. A partir do menu do servidor, selecione **Replication**. Se o suporte de replicação do Azure estiver definido para pelo menos **replica,** pode criar réplicas de leitura. 

3. Se o suporte de replicação do Azure não estiver definido para, pelo menos, **replicar,** desate-o. Selecione **Save** (Guardar).

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Base de Dados Azure para PostgreSQL - Replicação - Definir réplica e salvar":::

4. Reinicie o servidor para aplicar a alteração selecionando **Sim**.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Base de Dados Azure para PostgreSQL - Replicação - Confirme o reinício":::

5. Receberá duas notificações do portal Azure assim que a operação estiver concluída. Há uma notificação para atualizar o parâmetro do servidor. Há outra notificação para o reinício do servidor que se segue imediatamente.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Notificações de sucesso":::

6. Atualize a página do portal Azure para atualizar a barra de ferramentas de replicação. Agora pode criar réplicas de leitura para este servidor.
   

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Para criar uma réplica de leitura, siga estes passos:

1. Selecione uma base de dados de Azure existente para o servidor PostgreSQL para utilizar como servidor primário. 

2. Na barra lateral do servidor, em **DEFINIÇÕES,** selecione **Replicação**.

3. Selecione **Adicionar Réplica**.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Adicione uma réplica":::

4. Insira um nome para a réplica de leitura. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="Diga a réplica":::

5. Selecione um local para a réplica. A localização predefinida é a mesma do servidor primário.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Selecione uma localização":::

   > [!NOTE]
   > Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md) 

6. Selecione **OK** para confirmar a criação da réplica.

Após a criação da réplica de leitura, pode ser visualizada a partir da janela de **replicação:**

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Ver a nova réplica na janela de replicação":::
 

> [!IMPORTANT]
> Reveja a [secção de considerações da visão geral da Réplica de Leitura.](concepts-read-replicas.md#considerations)
>
> Antes de uma definição de servidor primário ser atualizada para um novo valor, atualize a definição de réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

## <a name="stop-replication"></a>Parar replicação
Pode parar a replicação entre um servidor primário e uma réplica de leitura.

> [!IMPORTANT]
> Depois de parar a replicação num servidor primário e uma réplica de leitura, não pode ser desfeita. A réplica de leitura torna-se um servidor autónomo que suporta tanto as leituras como as escritas. O servidor autónomo não pode ser transformado numa réplica novamente.

Para parar a replicação entre um servidor primário e uma réplica de leitura do portal Azure, siga estes passos:

1. No portal Azure, selecione a sua base de dados de Azure primária para o servidor PostgreSQL.

2. No menu do servidor, em **DEFINIÇÕES,** selecione **Replication**.

3. Selecione o servidor de réplica para o qual parar a replicação.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Selecione a réplica":::
 
4. **Selecione a replicação stop**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Selecione a replicação de stop":::
 
5. Selecione **OK** para parar a replicação.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="Confirme parar a replicação":::
 

## <a name="delete-a-primary-server"></a>Eliminar um servidor primário
Para eliminar um servidor primário, utiliza os mesmos passos que eliminar uma Base de Dados Azure autónoma para servidor PostgreSQL. 

> [!IMPORTANT]
> Quando elimina um servidor primário, a replicação de todas as réplicas lidas é interrompida. As réplicas de leitura tornam-se servidores autónomos que agora suportam tanto as leituras como as escritas.

Para eliminar um servidor do portal Azure, siga estes passos:

1. No portal Azure, selecione a sua base de dados de Azure primária para o servidor PostgreSQL.

2. Abra a página **'Visão Geral'** para o servidor. Selecione **Eliminar**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="Na página 'Visão geral' do servidor, selecione para eliminar o servidor primário":::
 
3. Insira o nome do servidor primário para eliminar. **Selecione Eliminar** para confirmar a eliminação do servidor primário.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="Confirme eliminar o servidor primário":::
 

## <a name="delete-a-replica"></a>Eliminar réplicas
Pode eliminar uma réplica de leitura semelhante à forma como elimina um servidor primário.

- No portal Azure, abra a página **geral** para a réplica de leitura. Selecione **Eliminar**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="Na página 'Visão geral' da réplica, selecione para eliminar a réplica":::
 
Também pode eliminar a réplica de leitura da janela **replicação** seguindo estes passos:

1. No portal Azure, selecione a sua base de dados de Azure primária para o servidor PostgreSQL.

2. No menu do servidor, em **DEFINIÇÕES,** selecione **Replication**.

3. Selecione a réplica de leitura para apagar.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Selecione a réplica para eliminar":::
 
4. Selecione **Eliminar réplica**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Selecione eliminar réplica":::
 
5. Introduza o nome da réplica para apagar. **Selecione Eliminar** para confirmar a eliminação da réplica.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="Confirme para eliminar a réplica te":::
 

## <a name="monitor-a-replica"></a>Monitorize uma réplica
Estão disponíveis duas métricas para monitorizar réplicas de leitura.

### <a name="max-lag-across-replicas-metric"></a>Max Lag através da métrica de réplicas
A métrica **Max Lag Across Replicas** mostra o lag em bytes entre o servidor primário e a réplica mais atrasada. 

1.  No portal Azure, selecione a base de dados Azure primária para o servidor PostgreSQL.

2.  Selecione **Métricas**. Na janela **Métricas,** selecione **Max Lag Across Replicas**.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="Monitorize o desfasamento máximo através de réplicas":::
 
3.  Para a sua **agregação,** selecione **Max**.


### <a name="replica-lag-metric"></a>Métrica de Lag de réplica
A métrica **Replica Lag** mostra o tempo desde a última transação reproduzida numa réplica. Se não houver transações no seu mestre, a métrica reflete este desfasamento temporal.

1. No portal Azure, selecione a Base de Dados Azure para a réplica de leitura PostgreSQL.

2. Selecione **Métricas**. Na janela **Métricas,** selecione **Replica Lag**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="Monitorize o lag da réplica":::
 
3. Para a sua **agregação,** selecione **Max**. 
 
## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [as réplicas lidas na Base de Dados Azure para PostgreSQL](concepts-read-replicas.md).
* Saiba como [criar e gerir réplicas de leitura na ALI Azure e NA API REST.](howto-read-replicas-cli.md)