---
title: Descubra o ponto final de gestão de instâncias gerido
description: Saiba como obter o endereço IP de gestão de ponto final de gestão de instância sacana do Azure SQL e verifique a sua proteção de firewall incorporada
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: ac4675be25cb5f2f65cc444e65bd25d65d3c3856
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770045"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Determinar o endereço IP do ponto final da gestão

O cluster virtual Azure SQL Database Managed Instance contém um ponto final de gestão que a Microsoft utiliza para operações de gestão. O ponto final de gestão está protegido com uma firewall incorporada ao nível da rede e verificação de certificado mútuo no nível de aplicação. Pode determinar o endereço IP do ponto final da gestão, mas não pode aceder a este ponto final.

Para determinar o endereço IP de gestão, faça uma procura dNS na sua instância gerida FQDN: `mi-name.zone_id.database.windows.net` . Isto devolverá uma entrada DNS que é `trx.region-a.worker.vnet.database.windows.net` como. Em seguida, pode fazer uma procura dNS neste FQDN com ".vnet" removido. Isto devolverá o endereço IP de gestão. 

Esta PowerShell fará tudo por si se substituir \< o MI FQDN \> pela entrada DNS da sua instância gerida: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Para obter mais informações sobre instâncias geridas e conectividade, consulte [a Azure SQL Database Managed Instance Connectivity Architecture](sql-database-managed-instance-connectivity-architecture.md).
