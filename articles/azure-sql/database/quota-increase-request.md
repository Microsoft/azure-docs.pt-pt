---
title: Solicitar um aumento de quota
description: Esta página descreve como criar um pedido de suporte para aumentar as quotas para Azure SQL Database e Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: 27719663acfbdbcd7293defc4b746153359adb61
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251862"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Aumentos de quota de pedido para Azure SQL Database e SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo explica como solicitar um aumento de quota para a Azure SQL Database e Azure SQL Managed Instance. Também explica como permitir o acesso de subscrição a uma região e como solicitar o acesso a hardware específico numa região.

## <a name="create-a-new-support-request"></a><a id="newquota"></a> Criar um novo pedido de apoio

Utilize as seguintes etapas para criar um novo pedido de suporte a partir do portal Azure para a Base de Dados SQL.

1. No menu [do portal Azure,](https://portal.azure.com) selecione **Help + support**.

   ![O link de suporte Help +](./media/quota-increase-request/help-plus-support.png)

1. No **Suporte Help +**, selecione Novo pedido de **suporte**.

    ![Criar um novo pedido de apoio](./media/quota-increase-request/new-support-request.png)

1. Para **o tipo de emissão**, selecione **limites de serviço e de subscrição (quotas)**.

   ![Selecione um tipo de problema](./media/quota-increase-request/select-quota-issue-type.png)

1. Para **Subscrição**, selecione a subscrição cuja quota pretende aumentar.

   ![Selecione uma subscrição para uma quota aumentada](./media/quota-increase-request/select-subscription-support-request.png)

1. Para **o tipo de quota**, selecione um dos seguintes tipos de quota:

   - **Base de Dados SQL** para base de dados única e quotas elásticas de piscina.
   - **SQL Database Managed Instance** para casos geridos.

   Em seguida, selecione **Seguinte: Soluções >>**.

   ![Selecione um tipo de quota](./media/quota-increase-request/select-quota-type.png)

1. Na janela **Detalhes,** selecione **Introduzir detalhes** para introduzir informações adicionais.

   ![Introduzir link de detalhes](./media/quota-increase-request/provide-details-link.png)

Clicar **em introduzir detalhes** mostra a janela **de detalhes da Quota** que lhe permite adicionar informações adicionais. As seguintes secções descrevem as diferentes opções para os tipos de quota de placas de **base de dados SQL** e **SQL Database Managed Instance.**

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> Tipos de quotas de base de dados SQL

As seguintes secções descrevem as opções de aumento de quota para os tipos de quotas **sql Database:**

- Unidades de transação de base de dados (DTUs) por servidor
- Servidores por subscrição
- Acesso regional a subscrições ou hardware específico

### <a name="database-transaction-units-dtus-per-server"></a>Unidades de transação de base de dados (DTUs) por servidor

Utilize os seguintes passos para solicitar um aumento dos DTUs por servidor.

1. Selecione as **unidades de transação de base de dados (DTUs) por** tipo de quota de servidor.

1. Na lista **de Recursos,** selecione o recurso para o alvo.

1. No campo novas **quotas,** insira o novo limite DTU que está a solicitar.

   ![Detalhes da quota da DTU](./media/quota-increase-request/quota-details-dtus.png)

Para obter mais informações, consulte [os limites de recursos para bases de dados únicas utilizando o modelo de compra DTU](resource-limits-dtu-single-databases.md) e [limites de Recursos para piscinas elásticas utilizando o modelo de compra DTU](resource-limits-dtu-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servidores por subscrição

Utilize as seguintes etapas para solicitar um aumento do número de servidores por subscrição.

1. Selecione os Servidores por tipo de quota **de subscrição.**

1. Na lista **de Localização,** selecione a região Azure para utilizar. A quota é por subscrição em cada região.

1. No campo **novas quotas,** insira o seu pedido para o número máximo de servidores naquela região.

   ![Detalhes da quota dos servidores](./media/quota-increase-request/quota-details-servers.png)

Para obter mais informações, consulte [os limites de recursos da Base de Dados SQL e a governação dos recursos.](resource-limits-logical-server.md)

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> Permitir o acesso por subscrição a uma região

Alguns tipos de oferta não estão disponíveis em todas as regiões. Pode ver um erro como o seguinte:

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

Se a sua subscrição precisar de acesso numa determinada região, selecione a opção **de acesso da Região.** No seu pedido, especifique a oferta e os detalhes da SKU que deseja ativar para a região. Para explorar as opções de oferta e SKU, consulte [os preços da Base de Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/).

1. Selecione o tipo de quota de **acesso da Região.**

1. Na lista **de localização Selecione,** selecione a região Azure para utilizar. A quota é por subscrição em cada região.

1. Insira o **Modelo de Compra** e Detalhes do Consumo **Esperado.**

   ![Solicitar acesso à região](./media/quota-increase-request/quota-request.png)

### <a name="request-enabling-specific-hardware-in-a-region"></a>Pedido que permita hardware específico numa região

Se uma [geração de hardware](service-tiers-vcore.md#hardware-generations) que pretende utilizar não estiver disponível na sua região (ver [disponibilidade de Hardware),](service-tiers-vcore.md#hardware-availability)poderá solicitá-lo utilizando os seguintes passos.

1. Selecione o outro tipo de quota **de pedido de quota.**

1. No campo **Descrição,** indicar o seu pedido, incluindo o nome da geração de hardware e o nome da região em que precisa.

   ![Solicite hardware numa nova região](./media/quota-increase-request/hardware-in-new-region.png)

## <a name="submit-your-request"></a>Submeter o pedido

O passo final é preencher os detalhes restantes do seu pedido de quota de Base de Dados SQL. Em seguida, selecione **Seguinte: Rever + criar>>** e, depois de rever os detalhes do pedido, clique em **Criar** para submeter o pedido.

## <a name="next-steps"></a>Passos seguintes

Depois de submeter o seu pedido, este será revisto. Será contactado com uma resposta com base nas informações fornecidas no formulário.

Para obter mais informações sobre outros limites do Azure, consulte [os limites de subscrição e serviços, quotas e restrições de subscrição da Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
