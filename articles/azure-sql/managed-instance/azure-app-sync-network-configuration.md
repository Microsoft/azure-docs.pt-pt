---
title: Configuração da rede sync para O Serviço de Aplicações Azure
titleSuffix: Azure SQL Managed Instance
description: Este artigo discute como sincronizar a sua configuração de rede para o plano de hospedagem do Azure App Service com o seu Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 12/13/2018
ms.openlocfilehash: e102aaace15d065d02e44fa06655827068020959
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620227"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Configuração de rede sincronizada para o plano de hospedagem do Azure App Service com Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Pode acontecer que, apesar de [ter integrado a sua aplicação com uma Rede Virtual Azure,](../../app-service/web-sites-integrate-with-vnet.md)não é possível estabelecer uma ligação com a SQL Managed Instance. Refrescante, ou sincronizado, a configuração de rede para o seu plano de serviço pode resolver este problema. 

## <a name="sync-network-configuration"></a>Configuração de rede sincronizada 

Para tal, siga estes passos:  

1. Vá ao seu plano de Serviço de Aplicações web.

   ![Screenshot do plano de serviço de aplicações](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Selecione **Networking** e, em seguida, **selecione Clique aqui para Gerir**.

   ![Screenshot do plano de serviço de gestão](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Selecione o seu **VNet** e clique em **Sync Network**.

   ![Screenshot da rede de sincronização](./media/azure-app-sync-network-configuration/sync.png)

4. Espere até que a sincronização esteja feita.
  
   ![Screenshot da sincronização feita](./media/azure-app-sync-network-configuration/sync-done.png)

Está agora pronto para tentar restabelecer a sua ligação à sua SQL Managed Instance.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a configuração do seu VNet para sql Managed Instance, consulte [a arquitetura VNet de Instância Gerida SQL](connectivity-architecture-overview.md) e [Como Configurar o VNet existente](vnet-existing-add-subnet.md).
