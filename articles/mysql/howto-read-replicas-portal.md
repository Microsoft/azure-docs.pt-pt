---
title: Gerir réplicas de leitura - Portal Azure - Base de Dados Azure para MySQL
description: Aprenda a configurar e gerir réplicas de leitura na Base de Dados Azure para mySQL utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: b7226e5ae8c468339e02dbe87e279266e4609da8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063481"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para mySQL usando o portal Azure

Neste artigo, você aprenderá a criar e gerir réplicas de leitura na Base de Dados Azure para o serviço MySQL usando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [Base de Dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será usada como servidor principal.

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para base de dados Azure para servidores MySQL nos níveis de preços otimizados de Propósito Geral ou Memória. Certifique-se de que o servidor principal está num destes níveis de preços.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando os seguintes passos:

1. Assine no [portal Azure.](https://portal.azure.com/)

2. Selecione a base de dados Azure existente para o servidor MySQL que pretende utilizar como mestre. Esta ação abre a página **de visão geral.**

3. **Selecione Replicação** a partir do menu, em **DEFINIÇÕES**.

4. **Selecione Adicionar Réplica**.

   ![Base de Dados Azure para MySQL - Replicação](./media/howto-read-replica-portal/add-replica.png)

5. Introduza um nome para o servidor de réplicas.

    ![Base de Dados Azure para MySQL - Nome de réplica](./media/howto-read-replica-portal/replica-name.png)

6. Selecione a localização do servidor de réplicas. A localização padrão é a mesma do servidor principal.

    ![Base de Dados Azure para MySQL - Localização de réplica](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Para saber mais sobre quais as regiões em que pode criar uma réplica, visite o artigo da [réplica de leitura.](concepts-read-replicas.md) 

7. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração do servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de ter sido criada. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores ao do mestre para garantir que a réplica é capaz de acompanhar o mestre.

Uma vez criado o servidor de réplica, pode ser visto a partir da lâmina de **replicação.**

   ![Base de Dados Azure para MySQL - Réplicas de lista](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Pare a replicação a um servidor de réplicas

> [!IMPORTANT]
> Parar a replicação num servidor é irreversível. Uma vez que a replicação tenha parado entre um mestre e uma réplica, não pode ser desfeita. O servidor de réplica torna-se então um servidor autónomo e agora suporta tanto a leitura como os escritos. Este servidor não pode ser transformado numa réplica novamente.

Para parar a replicação entre um mestre e um servidor de réplica do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a sua base de dados master Azure para o servidor MySQL. 

2. **Selecione Replicação** a partir do menu, em **DEFINIÇÕES**.

3. Selecione o servidor de réplica para o qual pretende parar a replicação.

   ![Base de Dados Azure para MySQL - Parar o servidor selecionado de replicação](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selecione **Parar a replicação**.

   ![Base de Dados Azure para MySQL - Parar a replicação](./media/howto-read-replica-portal/stop-replication.png)

5. Confirme que pretende parar a replicação clicando **em OK**.

   ![Base de Dados Azure para MySQL - Parar a replicação](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Eliminar um servidor de réplicas

Para eliminar um servidor de réplica de leitura do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a sua base de dados master Azure para o servidor MySQL.

2. **Selecione Replicação** a partir do menu, em **DEFINIÇÕES**.

3. Selecione o servidor de réplica que pretende eliminar.

   ![Base de Dados Azure para MySQL - Eliminar o servidor de seleção de réplicas](./media/howto-read-replica-portal/delete-replica-select.png)

4. **Selecione excluir réplica**

   ![Base de Dados Azure para MySQL - Eliminar réplica](./media/howto-read-replica-portal/delete-replica.png)

5. Digite o nome da réplica e clique **em Apagar** para confirmar a eliminação da réplica.  

   ![Base de Dados Azure para MySQL - Excluir réplica confirmação](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Eliminar um servidor principal

> [!IMPORTANT]
> Eliminar um servidor mestre interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor mestre. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor principal do portal Azure, utilize os seguintes passos:

1. No portal Azure, selecione a sua base de dados master Azure para o servidor MySQL.

2. A partir da **visão geral,** selecione **Delete**.

   ![Base de Dados Azure para MySQL - Delete master](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digite o nome do servidor principal e clique **em Eliminar** para confirmar a eliminação do servidor principal.  

   ![Base de Dados Azure para MySQL - Delete master](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorizar a replicação

1. No [portal Azure,](https://portal.azure.com/)selecione a réplica Da Base de Dados Azure para o servidor MySQL que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Métricas:**

3. Selecione o lag de **replicação em segundos** a partir da lista de métricas disponíveis.

   ![Selecione o lag de replicação](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecione o intervalo de tempo que pretende ver. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   ![Selecione intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Veja o intervalo de replicação para o intervalo de tempo selecionado. A imagem abaixo mostra os últimos 30 minutos.

   ![Selecione intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [réplicas de leitura](concepts-read-replicas.md)