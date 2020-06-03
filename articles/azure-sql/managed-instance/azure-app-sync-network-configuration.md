---
title: Configuração da rede sync para O Serviço de Aplicações Azure
titleSuffix: Azure SQL Managed Instance
description: Este artigo discute como sincronizar a sua configuração de rede para o plano de hospedagem do Azure App Service com o seu Azure SQL Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: 8aca5a4f76504f1e9c33f55101f697ea6b3b6856
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309905"
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
