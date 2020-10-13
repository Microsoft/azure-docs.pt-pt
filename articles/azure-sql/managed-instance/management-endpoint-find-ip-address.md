---
title: Descubra o endereço IP do ponto final de gestão
titleSuffix: Azure SQL Managed Instance
description: Saiba como obter o endereço IP público de gestão de instância gerida Azure SQL e verificar a sua proteção incorporada de firewall
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: a9a2b904bd7526f00a8f8a5d013be0c1e42e38a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617371"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Determinar o endereço IP do ponto final de gestão - Azure SQL Gestd Instance 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O cluster virtual Azure SQL Managed Instance contém um ponto final de gestão que a Azure utiliza para operações de gestão. O ponto final de gestão está protegido com uma firewall incorporada a nível de rede e verificação de certificado mútuo no nível de aplicação. Pode determinar o endereço IP do ponto final de gestão, mas não pode aceder a este ponto final.

Para determinar o endereço IP de gestão, faça uma [pesquisas DNS](/windows-server/administration/windows-commands/nslookup) no seu SQL Managed Instance FQDN: `mi-name.zone_id.database.windows.net` . Isto devolverá uma entrada de DNS que é `trx.region-a.worker.vnet.database.windows.net` como. Em seguida, pode fazer uma procura de DNS neste FQDN com ".vnet" removido. Isto devolverá o endereço IP de gestão. 

Este código PowerShell fará tudo por si se substituir \<MI FQDN\> pela entrada DNS da SQL Managed Instance: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Para obter mais informações sobre a SQL Managed Instance e conectividade, consulte [a arquitetura de conectividade Azure SQL Managed Instance](connectivity-architecture-overview.md).
