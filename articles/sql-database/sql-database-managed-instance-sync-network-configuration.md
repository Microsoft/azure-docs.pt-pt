---
title: Serviço de aplicações do Azure - configuração de rede de sincronização | Documentos da Microsoft
description: Este artigo descreve como sincronizar a configuração da rede para o plano de alojamento de App Service do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: 0d7920080fd61389741fbe785f5141003bef5251
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358801"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Sincronizar a configuração de rede para o plano de alojamento de App Service do Azure

Isso pode acontecer que embora [integrado a sua aplicação com uma rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md), não é possível estabelecer ligação à instância gerida. É uma coisa que pode tentar atualizar a configuração de rede para o seu plano de serviço.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Configuração de rede de sincronização para o plano de alojamento de serviço de aplicações

Para tal, siga estes passos:  

1. Aceda às suas aplicações web plano do serviço de aplicações.

   ![plano do Serviço de Aplicações](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Clique em **Networking** e, em seguida, clique em **clique aqui para gerir**.

   ![Gerir o plano do serviço](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Selecione seu **VNet** e clique em **sincronizar rede**.

   ![sincronizar rede](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Aguarde até que a sincronização é feita.
  
   ![Sincronização concluída](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Agora está pronto para tentar restabelecer a ligação à sua instância gerida.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre como configurar a sua VNet para a instância gerida, veja [arquitetura de VNet de instância gerida](sql-database-managed-instance-connectivity-architecture.md) e [como configurar a VNet existente](sql-database-managed-instance-configure-vnet-subnet.md).
