---
title: 'Tutorial: Geo-replicação & falha no portal'
description: Configure a geo-replicação para uma única ou piscina de base de dados na Base de Dados Azure SQL utilizando o portal Azure e inicie a falha.
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
ms.openlocfilehash: 601c537a51e29ae1f107127e1b83c07448eee9ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256435"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Configure a geo-replicação ativa para a Base de Dados Azure SQL no portal Azure e inicie a falha

Este artigo mostra-lhe como configurar a [geo-replicação ativa para bases de dados únicas e agrofadas](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) na Base de Dados Azure SQL utilizando o [portal Azure](https://portal.azure.com) e iniciar a falha.

Para obter informações sobre grupos de auto-failover com bases de dados individuais e agrupadas, consulte [as melhores práticas de utilização de grupos failover com bases de dados individuais e agrupadas](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Para obter informações sobre grupos de falha automática com Instâncias Geridas, consulte [as melhores práticas de utilização de grupos de failover com instâncias geridas](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a geo-replicação ativa utilizando o portal Azure, precisa do seguinte recurso:

* Uma base de dados Azure SQL: A base de dados primária que pretende replicar para uma região geográfica diferente.

> [!Note]
> Ao utilizar o portal Azure, só é possível criar uma base de dados secundária dentro da mesma subscrição que a principal. Se for necessária uma base de dados secundária para estar numa subscrição diferente, utilize a [Create Database REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) ou [alter DATABASE Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Adicione uma base de dados secundária

Os passos seguintes criam uma nova base de dados secundária numa parceria de geo-replicação.  

Para adicionar uma base de dados secundária, deve ser o proprietário ou coproprietário da subscrição.

A base de dados secundária tem o mesmo nome que a base de dados primária e tem, por padrão, o mesmo nível de serviço e tamanho de cálculo. A base de dados secundária pode ser uma única base de dados ou uma base de dados agrinada. Para obter mais informações, consulte [o modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o modelo de compra baseado em [vCore](sql-database-service-tiers-vcore.md).
Após a criação e semeado do secundário, os dados começam a replicar-se da base de dados primária para a nova base de dados secundária.

> [!NOTE]
> Se a base de dados do parceiro já existir (por exemplo, como resultado do fim de uma relação de geo-replicação anterior) o comando falha.

1. No [portal Azure,](https://portal.azure.com)navegue até à base de dados que pretende configurar para a geo-replicação.
2. Na página de base de dados SQL, **selecione geo-replicação**e, em seguida, selecione a região para criar a base de dados secundária. Pode selecionar qualquer outra região que não a região que acolhe a base de dados primária, mas recomendamos a [região emparelhada.](../best-practices-availability-paired-regions.md)

    ![Configurar georreplicação](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Selecione ou configure o servidor e o nível de preços para a base de dados secundária.

    ![Configurar secundário](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Opcionalmente, pode adicionar uma base de dados secundária a uma piscina elástica. Para criar a base de dados secundária numa piscina, clique em **piscina elástica** e selecione uma piscina no servidor alvo. Uma piscina já deve existir no servidor alvo. Este fluxo de trabalho não cria uma piscina.
5. Clique em **Criar** para adicionar o secundário.
6. A base de dados secundária é criada e o processo de sementeing começa.

    ![Configurar secundário](./media/sql-database-geo-replication-portal/seeding0.png)
7. Quando o processo de sementeira estiver concluído, a base de dados secundária mostra o seu estado.

    ![Sementeing completo](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Iniciar uma falha

A base de dados secundária pode ser trocada para se tornar a principal.  

1. No [portal Azure,](https://portal.azure.com)navegue até à base de dados primária na parceria de geo-replicação.
2. Na lâmina de base de dados SQL, selecione **todas as definições** > **de geo-replicação**.
3. Na lista **SECONDARIES,** selecione a base de dados que pretende tornar-se a nova primária e clique em **Failover**.

    ![failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Clique **sim** para começar a falha.

O comando muda imediatamente a base de dados secundária para a função principal. Este processo normalmente deve estar concluído dentro de 30 segundos ou menos.

Há um curto período durante o qual ambas as bases de dados não estão disponíveis (na ordem dos 0 a 25 segundos) enquanto as funções são trocadas. Se a base de dados primária tiver várias bases de dados secundárias, o comando reconfigura automaticamente os outros secundários para se ligar às novas primárias. Toda a operação deve demorar menos de um minuto a ser concluída em circunstâncias normais.

> [!NOTE]
> Este comando foi concebido para uma rápida recuperação da base de dados em caso de paragem. Desencadeia a falha sem sincronização de dados (falha forçada).  Se o primário estiver on-line e cometer transações quando o comando for emitido, poderá ocorrer alguma perda de dados.

## <a name="remove-secondary-database"></a>Remover base de dados secundária

Esta operação termina permanentemente a replicação para a base de dados secundária e altera o papel do secundário para uma base de dados regular de leitura. Se a conectividade com a base de dados secundária for quebrada, o comando tem sucesso, mas o secundário só se torna read-write depois de a conectividade ser restaurada.  

1. No [portal Azure,](https://portal.azure.com)navegue até à base de dados primária na parceria de geo-replicação.
2. Na página de base de dados SQL, **selecione geo-replicação**.
3. Na lista **SECONDARIES,** selecione a base de dados que pretende remover da parceria de geo-replicação.
4. Clique na **replicação**stop .

    ![Remover secundário](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Abre-se uma janela de confirmação. Clique em **Sim** para remover a base de dados da parceria de geo-replicação. (Desloque-o para uma base de dados de leitura e escrita não parte de qualquer replicação.)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a geo-replicação ativa, consulte a [geo-replicação ativa.](sql-database-active-geo-replication.md)
* Para aprender sobre grupos de auto-failover, consulte [grupos de falha automática](sql-database-auto-failover-group.md)
* Para uma visão geral da continuidade do negócio e cenários, consulte a [visão geral da continuidade do Negócio.](sql-database-business-continuity.md)
