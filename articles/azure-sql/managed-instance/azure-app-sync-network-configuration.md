---
title: Configuração da rede Sync para o Serviço de Aplicações Azure
titleSuffix: Azure SQL Managed Instance
description: Este artigo discute como sincronizar a configuração da sua rede para o plano de hospedagem do Serviço de Aplicações Azure com a sua Instância Gerida Azure SQL.
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
ms.openlocfilehash: 6e4f6789be2faa8d1af0fd75830541f8a9e27a7e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044571"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Configuração de rede sync para o plano de hospedagem do Serviço de Aplicações Azure com instância gerida azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Pode acontecer que, apesar de ter integrado a [sua aplicação com uma Rede Virtual Azure,](../../app-service/web-sites-integrate-with-vnet.md)não é possível estabelecer uma ligação com a SQL Managed Instance. Refrescante, ou sincronizando, a configuração de rede para o seu plano de serviço pode resolver este problema. 

## <a name="sync-network-configuration"></a>Configuração de rede sincronizada 

Para tal, siga estes passos:  

1. Vá ao plano de App Service de aplicações web.

   ![plano de serviço de aplicações](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Selecione **Networking** e, em seguida, selecione **Clique aqui para gerir**.

   ![gerir plano de serviço](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Selecione o seu **VNet** e clique em **Sync Network**.

   ![rede de sincronização](./media/azure-app-sync-network-configuration/sync.png)

4. Espere até que a sincronização esteja feita.
  
   ![sincronização feito](./media/azure-app-sync-network-configuration/sync-done.png)

Está agora pronto para tentar restabelecer a sua ligação com a sua Instância Gerida SQL.

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre a configuração do seu VNet para SQL Managed Instance, consulte a arquitetura VNet gerida pela [SQL](connectivity-architecture-overview.md) e [como configurar a VNet existente](vnet-existing-add-subnet.md).
