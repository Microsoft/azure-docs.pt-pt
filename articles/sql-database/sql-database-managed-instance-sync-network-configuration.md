---
title: Serviço de Aplicações Azure - configuração da rede de sincronização
description: Este artigo discute como sincronizar a configuração da sua rede para o plano de hospedagem do Serviço de Aplicações Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: 0065b5c7ddf8e19897f76aeb49300ec7a6809b30
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772374"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Configuração de rede sync para o plano de hospedagem do Serviço de Aplicações Azure

Pode acontecer que, apesar de ter integrado a [sua aplicação com uma Rede Virtual Azure,](../app-service/web-sites-integrate-with-vnet.md)não é possível estabelecer a ligação à Managed Instance. Uma coisa que pode tentar é refrescar a configuração de rede para o seu plano de serviço.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Configuração de rede sync para plano de hospedagem do Serviço de Aplicações

Para tal, siga estes passos:  

1. Vá ao plano de App Service de aplicações web.

   ![plano de serviço de aplicações](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Clique em **Networking** e, em seguida, clique **em Clicar aqui para gerir**.

   ![gerir plano de serviço](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Selecione o seu **VNet** e clique em **Sync Network**.

   ![rede de sincronização](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Espere até que a sincronização esteja feita.
  
   ![sincronização feito](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Está agora pronto para tentar restabelecer a sua ligação com a sua Instância Gerida.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a configuração do seu VNet para Instância Gerida, consulte a [arquitetura VNet de instância gerida](sql-database-managed-instance-connectivity-architecture.md) e como [configurar o VNet existente](sql-database-managed-instance-configure-vnet-subnet.md).
