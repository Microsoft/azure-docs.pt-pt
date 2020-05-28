---
title: Solicitar um aumento de quota
description: Esta página descreve como criar um pedido de apoio para aumentar as quotas para a Base de Dados Azure SQL e para a Instância Gerida Azure SQL.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: 53160fa5a2d24f747b0653673a6f817ae14a7975
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118853"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Aumento das quotas de pedido para a Base de Dados Azure SQL e para a Instância Gerida SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo explica como solicitar um aumento de quota para a Base de Dados Azure SQL e para a Instância Gerida Azure SQL. Também explica como permitir o acesso por subscrição a uma região.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Criar um novo pedido de apoio

Utilize os seguintes passos para criar um novo pedido de suporte do portal Azure para a Base de Dados SQL.

1. No menu [do portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![O link de ajuda + suporte](./media/quota-increase-request/help-plus-support.png)

1. Em **Ajuda + suporte,** selecione **Novo pedido de suporte**.

    ![Criar um novo pedido de apoio](./media/quota-increase-request/new-support-request.png)

1. Para **o tipo de emissão,** selecione limites de serviço e **subscrição (quotas)**.

   ![Selecione um tipo de problema](./media/quota-increase-request/select-quota-issue-type.png)

1. Para **Subscrição,** selecione a subscrição cuja quota pretende aumentar.

   ![Selecione uma subscrição para uma quota aumentada](./media/quota-increase-request/select-subscription-support-request.png)

1. Para **o tipo quota,** selecione um dos seguintes tipos de quotas:

   - **Base de Dados SQL** para base de dados única e quotas elásticas de piscina.
   - **SQL Database Managed Instance** para casos geridos.

   Em seguida, selecione **Seguinte: Soluções >>**.

   ![Selecione um tipo de quota](./media/quota-increase-request/select-quota-type.png)

1. Na janela **Detalhes,** selecione **Fornecer detalhes** para introduzir informações adicionais.

   ![O link "Fornecer detalhes"](./media/quota-increase-request/provide-details-link.png)

Clique **em Fornecer detalhes** mostra a janela de **detalhes da Quota** que lhe permite adicionar informações adicionais. As seguintes secções descrevem as diferentes opções para os tipos de quotas de instância geridas pela Base de **Dados SQL** e pela Base de **Dados SQL.**

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>Tipos de quotas de base de dados SQL

As seguintes secções descrevem três opções de aumento de quota para os tipos de quotas da Base de **Dados SQL:**

- Unidades de transação de base de dados (DTUs) por servidor
- Servidores por subscrição
- Permitir o acesso por subscrição a uma região

### <a name="database-transaction-units-dtus-per-server"></a>Unidades de transação de base de dados (DTUs) por servidor

Utilize os seguintes passos para solicitar um aumento das DTUs por servidor.

1. Selecione as unidades de **transação da Base de Dados (DTUs) por** tipo de quota de servidor.

1. Na lista **de Recursos,** selecione o recurso para o alvo.

1. No novo campo de **quotas,** introduza o novo limite de DTU que está a solicitar.

   ![Detalhes da quota dTU](./media/quota-increase-request/quota-details-dtus.png)

Para obter mais informações, consulte [os limites de recursos para bases de dados únicas utilizando o modelo](resource-limits-dtu-single-databases.md) de compra dTU e os [limites de recursos para piscinas elásticas utilizando o modelo](resource-limits-dtu-elastic-pools.md)de compra DTU .

### <a name="servers-per-subscription"></a>Servidores por subscrição

Utilize os seguintes passos para solicitar um aumento do número de servidores por subscrição.

1. Selecione os Servidores por tipo de quota **de subscrição.**

1. Na lista **de localização,** selecione a região azure para utilizar. A quota é por subscrição em cada região.

1. No **campo de cotações New,** insira o seu pedido para o número máximo de servidores naquela região.

   ![Detalhes da quota dos servidores](./media/quota-increase-request/quota-details-servers.png)

Para mais informações, consulte os limites de recursos da Base de [Dados SQL e a governação dos recursos.](resource-limits-logical-server.md)

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>Permitir o acesso por subscrição a uma região

Alguns tipos de ofertas não estão disponíveis em todas as regiões. Pode ver um erro como o seguinte:

`This location is not available for subscription`

Se a sua subscrição necessitar de acesso numa determinada região, utilize a opção de pedido de **outra quota** para solicitar acesso. No seu pedido, especifique a oferta e os detalhes sKU que pretende ativar para a região. Para explorar a oferta e as opções SKU, consulte os preços da Base de [Dados Azure SQL.](https://azure.microsoft.com/pricing/details/sql-database/single/)

![Outros detalhes da quota](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="sql-managed-instance-quota-type"></a><a id="sqlmiquota"></a>Tipo de quota de instância gerida SQL

Para o tipo de quota **SQL Managed Instance,** utilize os seguintes passos:

1. Na lista **da Região,** selecione a região de Azure para atingir o alvo.

1. Introduza os novos limites que está a solicitar para **subnet** e **vCore**.

   ![Detalhes da quota de instância gerida sQL](./media/quota-increase-request/quota-details-managed-instance.png)

Para mais informações, consulte os limites de recursos de Instância Gerida de [Overview Azure SQL](../managed-instance/resource-limits.md).

## <a name="submit-your-request"></a>Submeter o pedido

O passo final é preencher os detalhes restantes do seu pedido de quota de Base de Dados SQL. Em seguida, selecione **Seguinte: Rever + criar>>** e, depois de rever os detalhes do pedido, clique em **Criar** para submeter o pedido.

## <a name="next-steps"></a>Passos seguintes

Depois de submeter o seu pedido, este será revisto. Será contactado com uma resposta com base nas informações fornecidas no formulário.

Para obter mais informações sobre outros limites Do Azure, consulte [os limites de subscrição e serviço do Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
