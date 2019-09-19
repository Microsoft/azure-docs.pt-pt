---
title: 'Portal do Azure: Replicação geográfica do banco de dados SQL | Microsoft Docs'
description: Configurar a replicação geográfica para um banco de dados individual ou em pool no banco de dados SQL do Azure usando o portal do Azure e iniciar o failover
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 049122b97a26e63188142dd5494927c2ae71d852
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103233"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Configurar a replicação geográfica ativa para o banco de dados SQL do Azure no portal do Azure e iniciar o failover

Este artigo mostra como configurar a [replicação geográfica ativa para bancos de dados individuais e em pool](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) no banco de dados SQL do Azure usando o [portal do Azure](https://portal.azure.com) e para iniciar o failover.

Para obter informações sobre grupos de failover automático com bancos de dados únicos e em pool, consulte [práticas recomendadas de uso de grupos de failover com bancos de dados individuais e em pool](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Para obter informações sobre grupos de failover automático com instâncias gerenciadas, consulte [práticas recomendadas de uso de grupos de failover com instâncias gerenciadas](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a replicação geográfica ativa usando o portal do Azure, você precisará do seguinte recurso:

* Um banco de dados SQL do Azure: O banco de dados primário que você deseja replicar para uma região geográfica diferente.

> [!Note]
> Ao usar portal do Azure, você só pode criar um banco de dados secundário dentro da mesma assinatura que o primário. Se for necessário que o banco de dados secundário esteja em uma assinatura diferente, use [criar API REST de banco de dados](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) ou [API Transact-SQL ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Adicionar um banco de dados secundário

As etapas a seguir criam um novo banco de dados secundário em uma parceria de replicação geográfica.  

Para adicionar um banco de dados secundário, você deve ser o proprietário ou o coproprietário da assinatura.

O banco de dados secundário tem o mesmo nome que o banco de dados primário e tem, por padrão, a mesma camada de serviço e tamanho de computação. O banco de dados secundário pode ser um banco de dados individual ou um banco de dados em pool. Para obter mais informações, consulte [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
Depois que o secundário é criado e propagado, os dados começam a ser replicados do banco de dados primário para o novo banco de dados secundário.

> [!NOTE]
> Se o banco de dados parceiro já existir (por exemplo, como resultado do encerramento de uma relação de replicação geográfica anterior), o comando falhará.

1. Na [portal do Azure](https://portal.azure.com), navegue até o banco de dados que você deseja configurar para a replicação geográfica.
2. Na página banco de dados SQL, selecione **replicação geográfica**e, em seguida, selecione a região para criar o banco de dados secundário. Você pode selecionar qualquer região que não seja a região que hospeda o banco de dados primário, mas recomendamos a [região emparelhada](../best-practices-availability-paired-regions.md).

    ![Configurar georreplicação](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Selecione ou configure o servidor e o tipo de preço para o banco de dados secundário.

    ![Configurar secundário](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Opcionalmente, você pode adicionar um banco de dados secundário a um pool elástico. Para criar o banco de dados secundário em um pool, clique em **pool elástico** e selecione um pool no servidor de destino. Um pool já deve existir no servidor de destino. Este fluxo de trabalho não cria um pool.
5. Clique em **criar** para adicionar o secundário.
6. O banco de dados secundário é criado e o processo de propagação começa.

    ![Configurar secundário](./media/sql-database-geo-replication-portal/seeding0.png)
7. Quando o processo de propagação for concluído, o banco de dados secundário exibirá seu status.

    ![Propagação concluída](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Iniciar um failover

O banco de dados secundário pode ser alternado para se tornar o primário.  

1. Na [portal do Azure](https://portal.azure.com), navegue até o banco de dados primário na parceria de replicação geográfica.
2. Na folha banco de dados SQL, selecione **todas as configurações** > **replicação geográfica**.
3. Na lista **secundários** , selecione o banco de dados que você deseja tornar o novo primário e clique em **failover**.

    ![ativação pós-falha](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Clique em **Sim** para iniciar o failover.

O comando alterna imediatamente o banco de dados secundário para a função primária. Esse processo normalmente deve ser concluído dentro de 30 segundos ou menos.

Há um curto período durante o qual os dois bancos de dados ficam indisponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. Se o banco de dados primário tiver vários bancos de dados secundários, o comando reconfigurará automaticamente os outros secundários para se conectar ao novo primário. A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais.

> [!NOTE]
> Esse comando foi projetado para recuperação rápida do banco de dados em caso de uma interrupção. Ele dispara failover sem sincronização de dados (failover forçado).  Se o primário estiver online e confirmando transações quando o comando for emitido, poderá ocorrer alguma perda de dados.

## <a name="remove-secondary-database"></a>Remover banco de dados secundário

Essa operação encerra permanentemente a replicação para o banco de dados secundário e altera a função do secundário para um banco de dados de leitura/gravação normal. Se a conectividade com o banco de dados secundário for interrompida, o comando terá sucesso, mas o secundário não se tornará de leitura/gravação até que a conectividade seja restaurada.  

1. Na [portal do Azure](https://portal.azure.com), navegue até o banco de dados primário na parceria de replicação geográfica.
2. Na página banco de dados SQL, selecione **replicação geográfica**.
3. Na lista **secundários** , selecione o banco de dados que você deseja remover da parceria de replicação geográfica.
4. Clique em **parar replicação**.

    ![Remover secundário](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Uma janela de confirmação é aberta. Clique em **Sim** para remover o banco de dados da parceria de replicação geográfica. (Defina-o como um banco de dados de leitura/gravação que não faz parte de qualquer replicação.)

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre a replicação geográfica ativa, consulte [replicação geográfica ativa](sql-database-active-geo-replication.md).
* Para saber mais sobre grupos de failover automático, consulte [grupos de failover automático](sql-database-auto-failover-group.md)
* Para obter uma visão geral e cenários de continuidade de negócios, consulte [visão geral da continuidade de negócios](sql-database-business-continuity.md)
