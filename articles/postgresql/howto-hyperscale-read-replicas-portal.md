---
title: Gerir réplicas de leitura - Portal Azure - Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
description: Saiba como gerir réplicas de leitura Azure Database for PostgreSQL - Hyperscale (Citus) a partir do portal Azure.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 4d3a88692da6d1fc78e96a6261d42d3ca97b0dd0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024037"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL - Hiperescala (Citus) a partir do portal Azure

> [!IMPORTANT]
> As réplicas de leitura em Hyperscale (Citus) estão atualmente em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
>
> Pode ver uma lista completa de outras novidades em [funcionalidades de pré-visualização para Hyperscale (Citus)](hyperscale-preview-features.md).

Neste artigo, aprende-se a criar e gerir réplicas de leitura em Hyperscale (Citus) a partir do portal Azure. Para saber mais sobre as réplicas lidas, consulte a [visão geral.](concepts-hyperscale-read-replicas.md)


## <a name="prerequisites"></a>Pré-requisitos

Um [grupo de servidores de Hiperescala (Citus)](quickstart-create-hyperscale-portal.md) é o principal.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Para criar uma réplica de leitura, siga estes passos:

1. Selecione uma base de dados de Azure existente para o grupo de servidores PostgreSQL para utilizar como principal. 

2. Na barra lateral do grupo do servidor, sob **a gestão do grupo Server,** selecione **Replication**.

3. Selecione **Adicionar Réplica**.

4. Insira um nome para a réplica de leitura. 

5. Selecione **OK** para confirmar a criação da réplica.

Após a criação da réplica de leitura, pode ser vista a partir da janela **de replicação.**

> [!IMPORTANT]
>
> Reveja a [secção de considerações da visão geral da Réplica de Leitura.](concepts-hyperscale-read-replicas.md#considerations)
>
> Antes de uma definição do grupo de servidor primário ser atualizada para um novo valor, atualize a definição de réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

## <a name="delete-a-primary-server-group"></a>Eliminar um grupo de servidor primário

Para eliminar um grupo de servidor primário, utiliza os mesmos passos que para eliminar um grupo de servidores hyperscale (Citus) autónomos. 

> [!IMPORTANT]
>
> Quando elimina um grupo de servidor primário, a replicação de todas as réplicas lidas é interrompida. As réplicas de leitura tornam-se grupos de servidor autónomos que agora suportam tanto as leituras como as escritas.

Para eliminar um grupo de servidores do portal Azure, siga estes passos:

1. No portal Azure, selecione a sua base de dados primária de Azure para o grupo de servidores PostgreSQL.

2. Abra a página **'Visão Geral'** para o grupo do servidor. Selecione **Eliminar**.
 
3. Introduza o nome do grupo de servidor primário para eliminar. **Selecione Eliminar** para confirmar a eliminação do grupo de servidor primário.
 

## <a name="delete-a-replica"></a>Eliminar réplicas

Pode eliminar uma réplica de leitura da mesma forma que elimina um grupo de servidor primário.

- No portal Azure, abra a página **geral** para a réplica de leitura. Selecione **Eliminar**.
 
Também pode eliminar a réplica de leitura da janela **replicação** seguindo estes passos:

1. No portal Azure, selecione o seu grupo de servidor primário hyperscale (Citus).

2. No menu do grupo do servidor, sob **a gestão do grupo Server,** selecione **Replication**.

3. Selecione a réplica de leitura para apagar.
 
4. Selecione **Eliminar réplica**.
 
5. Introduza o nome da réplica para apagar. **Selecione Eliminar** para confirmar a eliminação da réplica.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [réplicas de leitura na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](concepts-hyperscale-read-replicas.md).
