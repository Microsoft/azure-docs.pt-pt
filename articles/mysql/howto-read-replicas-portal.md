---
title: Criar e gerir réplicas de leitura na base de dados do Azure para MySQL
description: Este artigo descreve como configurar e gerir réplicas de leitura na base de dados do Azure para MySQL com o portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 52f192a179c02e63c394401cce82b51fbe96e92d
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887272"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Como criar e gerir ler réplicas na base de dados do Azure para MySQL com o portal do Azure

Neste artigo, aprenderá como criar e gerir réplicas de leitura na mesma região do Azure como o modelo na base de dados do Azure para o serviço de MySQL com o portal do Azure. A funcionalidade está atualmente em pré-visualização pública.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [base de dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será utilizado como o servidor mestre.

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a base de dados do Azure para MySQL servidores nos escalões de preços para fins gerais ou com otimização de memória. Certifique-se de que o servidor mestre está em um destes escalões de preço.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado através dos seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione a base de dados do Azure existente para o servidor MySQL que pretende utilizar como um modelo. Esta ação abre o **descrição geral** página.

3. Selecione **replicação** no menu, sob **definições**.

4. Selecione **Adicionar réplica**.

   ![Base de dados do Azure para MySQL - replicação](./media/howto-read-replica-portal/add-replica.png)

5. Introduza um nome para o servidor de réplica e clique em **OK** para confirmar a criação da réplica.

   ![Base de dados do Azure para MySQL - criar réplica](./media/howto-read-replica-portal/create-replica.png)

> [!NOTE]
> Réplicas de leitura são criadas com a mesma configuração de servidor como o modelo. A configuração do servidor de réplica pode ser alterada depois de este ter sido criado. Recomenda-se que a configuração do servidor de réplica deve ser mantida em valores iguais ou maiores do que o principal para garantir que a réplica é capaz de acompanhar o mestre.

Quando o servidor de réplica tiver sido criado, ele pode ser visualizado os **replicação** painel.

   ![Base de dados do Azure para MySQL - réplicas de lista](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> A parar a replicação para um servidor é irreversível. Assim que a replicação foi parado entre um mestre e de réplica, não pode ser anulada. O servidor de réplica, em seguida, torna-se um servidor autónomo e agora oferece suporte a leitura e escrita. Este servidor não pode se transformar numa réplica novamente.

Para parar a replicação entre um mestre e um servidor de réplica no portal do Azure, utilize os seguintes passos:

1. No portal do Azure, selecione a sua base de dados principal do Azure para o servidor MySQL. 

2. Selecione **replicação** no menu, sob **definições**.

3. Selecione o servidor de réplica que pretende parar a replicação de.

   ![Base de dados do Azure para MySQL - seleção de servidor de replicação Stop](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selecione **paragem da replicação**.

   ![Base de dados do Azure para MySQL - paragem da replicação](./media/howto-read-replica-portal/stop-replication.png)

5. Confirme que pretende parar a replicação clicando **OK**.

   ![Confirmar paragem da replicação de base de dados do Azure para MySQL-](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Eliminar um servidor de réplica

Para eliminar um servidor de réplica de leitura a partir do portal do Azure, utilize os seguintes passos:

1. No portal do Azure, selecione a sua base de dados principal do Azure para o servidor MySQL.

2. Selecione **replicação** no menu, sob **definições**.

3. Selecione o servidor de réplica que pretende eliminar.

   ![Base de dados do Azure para MySQL - seleção de servidor de réplica Delete](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selecione **Eliminar réplica**

   ![Base de dados do Azure para MySQL - Eliminar réplica](./media/howto-read-replica-portal/delete-replica.png)

5. Escreva o nome da réplica e clique em **eliminar** para confirmar a eliminação da réplica.  

   ![Confirmar a base de dados do Azure para MySQL - Eliminar réplica](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Eliminar um servidor principal

> [!IMPORTANT]
> A eliminar um servidor principal para a replicação para todos os servidores de réplica e elimina o próprio servidor mestre. Servidores de réplica se tornar a servidores autónomos que agora oferecem suporte a leitura e escrita.

Para eliminar um servidor principal do portal do Azure, utilize os seguintes passos:

1. No portal do Azure, selecione a sua base de dados principal do Azure para o servidor MySQL.

2. Partir do **descrição geral**, selecione **eliminar**.

   ![Base de dados do Azure para MySQL - eliminar mestre](./media/howto-read-replica-portal/delete-master-overview.png)

3. Escreva o nome do servidor mestre e clique em **eliminar** para confirmar a eliminação do servidor mestre.  

   ![Base de dados do Azure para MySQL - eliminar mestre](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitor de replicação

1. Na [portal do Azure](https://portal.azure.com/), selecione a réplica de base de dados do Azure para o servidor MySQL que pretende monitorizar.

2. Sob o **monitorização** secção a barra lateral, selecione **métricas**:

3. Selecione **desfasamento em segundos** na lista pendente de métricas disponíveis. 

   ![Selecione o atraso de replicação](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecione o intervalo de tempo que quer ver. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   ![Selecionar intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Ver o desfasamento de replicação para o intervalo de tempo selecionado. A imagem abaixo mostra os últimos 30 minutos.

   ![Selecionar intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)