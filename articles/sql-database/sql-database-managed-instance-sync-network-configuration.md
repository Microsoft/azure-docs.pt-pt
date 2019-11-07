---
title: Serviço de Azure App-configuração de rede de sincronização
description: Este artigo discute como sincronizar sua configuração de rede para o plano de hospedagem do serviço Azure App.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: e7263d6a7716caf9f53e8496c6fb02b6d17b5509
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687892"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Sincronizar a configuração de rede para o plano de hospedagem do serviço Azure App

Pode acontecer que, embora você tenha [integrado seu aplicativo a uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md), não é possível estabelecer conexão com instância gerenciada. Uma coisa que você pode tentar é atualizar a configuração de rede para seu plano de serviço.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Sincronizar a configuração de rede para o plano de hospedagem do serviço de aplicativo

Para tal, siga estes passos:  

1. Vá para o plano do serviço de aplicativo de aplicativos Web.

   ![plano do serviço de aplicativo](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Clique em **rede** e, em seguida, clique **em clique aqui para gerenciar**.

   ![gerenciar plano de serviço](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Selecione sua **VNet** e clique em **sincronizar rede**.

   ![sincronizar rede](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Aguarde até que a sincronização seja concluída.
  
   ![Sincronização concluída](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Agora você está pronto para tentar restabelecer sua conexão com o Instância Gerenciada.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre como configurar sua VNet para Instância Gerenciada, consulte [instância gerenciada arquitetura de vnet](sql-database-managed-instance-connectivity-architecture.md) e [como configurar a vnet existente](sql-database-managed-instance-configure-vnet-subnet.md).
