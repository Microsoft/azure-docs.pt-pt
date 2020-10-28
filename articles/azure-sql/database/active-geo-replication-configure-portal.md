---
title: 'Tutorial: Geo-replicação & falha no portal'
description: Configure a geo-replicação para uma base de dados utilizando o portal Azure e inicie a falha.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673274"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Tutorial: Configurar a geo-replicação ativa e o failover no portal Azure (Base de Dados Azure SQL)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo mostra-lhe como configurar [a geo-replicação ativa para a Base de Dados Azure SQL](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) utilizando o [portal Azure](https://portal.azure.com) e iniciar a falha.

Para obter as melhores práticas utilizando grupos de auto-failover, consulte [as melhores práticas para a Base de Dados Azure SQL](auto-failover-group-overview.md#best-practices-for-sql-database) e [as melhores práticas para Azure SQL Managed Instance](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Pré-requisitos

Para configurar a geo-replicação ativa utilizando o portal Azure, precisa do seguinte recurso:

* Uma base de dados na Base de Dados Azure SQL: A base de dados primária que pretende replicar para uma região geográfica diferente.

> [!Note]
> Ao utilizar o portal Azure, só é possível criar uma base de dados secundária dentro da mesma subscrição que a principal. Se for necessária uma base de dados secundária para uma subscrição diferente, utilize [a API do Rest da Base de Dados](/rest/api/sql/databases/createorupdate) ou [a ALTER DATABASE Transact-SQL API](/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Adicione uma base de dados secundária

Os passos seguintes criam uma nova base de dados secundária numa parceria de geo-replicação.  

Para adicionar uma base de dados secundária, você deve ser o proprietário da subscrição ou coproprietário.

A base de dados secundária tem o mesmo nome que a base de dados primária e tem, por padrão, o mesmo nível de serviço e tamanho de cálculo. A base de dados secundária pode ser uma única base de dados ou uma base de dados agrária. Para obter mais informações, consulte [o modelo de compra baseado em DTU](service-tiers-dtu.md) e o modelo de compra baseado em [vCore.](service-tiers-vcore.md)
Após a criação e semeada do secundário, os dados começam a replicar-se da base de dados primária para a nova base de dados secundária.

> [!NOTE]
> Se a base de dados do parceiro já existir (por exemplo, como resultado do fim de uma relação de geo-replicação anterior) o comando falha.

1. No [portal Azure,](https://portal.azure.com)navegue na base de dados que pretende configurar para a geo-replicação.
2. Na página SQL Database, selecione **a geo-replicação** e, em seguida, selecione a região para criar a base de dados secundária. Pode selecionar qualquer região que não seja a região que acolhe a base de dados primária, mas recomendamos a [região emparelhada.](../../best-practices-availability-paired-regions.md)

    ![Configurar georreplicação](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. Selecione ou configuure o servidor e o nível de preços para a base de dados secundária.

    ![criar forma secundária](./media/active-geo-replication-configure-portal/create-secondary.png)
4. Opcionalmente, pode adicionar uma base de dados secundária a uma piscina elástica. Para criar a base de dados secundária numa piscina, clique em **piscina elástica** e selecione uma piscina no servidor alvo. Uma piscina já deve existir no servidor alvo. Este fluxo de trabalho não cria uma piscina.
5. Clique em **Criar** para adicionar o secundário.
6. A base de dados secundária é criada e o processo de sementeira começa.

    ![mapa secundário](./media/active-geo-replication-configure-portal/seeding0.png)
7. Quando o processo de sementeira estiver concluído, a base de dados secundária apresenta o seu estado.

    ![Sementeira completa](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Iniciar uma ativação pós-falha

A base de dados secundária pode ser mudada para se tornar a principal.  

1. No [portal Azure,](https://portal.azure.com)navegue pela base de dados primária na parceria de geo-replicação.
2. Na lâmina SQL Database, selecione **Todas as definições**  >  **de geo-replicação** .
3. Na lista **DE SECUNDÁRIOS,** selecione a base de dados que pretende tornar-se a nova primária e clique em **"Falha Forçada".**

    ![ativação pós-falha](./media/active-geo-replication-configure-portal/secondaries.png)
4. Clique **em Sim** para iniciar a falha.

O comando muda imediatamente a base de dados secundária para o papel primário. Este processo deve normalmente estar concluído dentro de 30 segundos ou menos.

Há um curto período durante o qual ambas as bases de dados estão indisponíveis (na ordem de 0 a 25 segundos) enquanto as funções são trocadas. Se a base de dados primária tiver várias bases de dados secundárias, o comando reconfigura automaticamente os outros secundários para se ligarem à nova primária. Toda a operação deve demorar menos de um minuto a ser concluída em circunstâncias normais.

> [!NOTE]
> Este comando foi concebido para uma rápida recuperação da base de dados em caso de paragem. Desencadeia falhas sem sincronização de dados (falha forçada).  Se o principal estiver online e efetivo de transações quando o comando é emitido, pode ocorrer alguma perda de dados.

## <a name="remove-secondary-database"></a>Remover base de dados secundária

Esta operação termina permanentemente a replicação para a base de dados secundária e altera o papel do secundário para uma base de dados regular de leitura-escrita. Se a conectividade com a base de dados secundária for quebrada, o comando terá sucesso, mas o secundário só se torna escrito após a restauração da conectividade.  

1. No [portal Azure,](https://portal.azure.com)navegue pela base de dados primária na parceria de geo-replicação.
2. Na página de base de dados SQL, selecione **a geo-replicação.**
3. Na lista **DE SECUNDÁRIOS,** selecione a base de dados que pretende remover da parceria de geo-replicação.
4. Clique **em Stop Replication** .

    ![Remover secundário](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Abre-se uma janela de confirmação. Clique **em Sim** para remover a base de dados da parceria de geo-replicação. (Coloque-o numa base de dados de leitura-escrita que não faça parte de qualquer replicação.)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a geo-replicação ativa, consulte [a geo-replicação ativa.](active-geo-replication-overview.md)
* Para conhecer os grupos de falha automática, consulte [os grupos auto-failover](auto-failover-group-overview.md)
* Para uma visão geral de continuidade do negócio e cenários, consulte [a visão geral da continuidade do Negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)