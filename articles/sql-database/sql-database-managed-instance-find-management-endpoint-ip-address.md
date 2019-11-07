---
title: Descobrir ponto de extremidade de gerenciamento de Instância Gerenciada do Banco de Dados SQL do Azure
description: Saiba como obter Instância Gerenciada do Banco de Dados SQL do Azure endereço IP público do ponto de extremidade de gerenciamento e verificar sua proteção de firewall interna
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 0cc3ca3a2b8130e52d8c7fc670c0ee7997e4aa47
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688042"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Determinar o endereço IP do ponto de extremidade de gerenciamento

O cluster virtual Instância Gerenciada do Banco de Dados SQL do Azure contém um ponto de extremidade de gerenciamento que a Microsoft usa para operações de gerenciamento. O ponto de extremidade de gerenciamento é protegido com um firewall interno no nível da rede e a verificação de certificado mútuo no nível do aplicativo. Você pode determinar o endereço IP do ponto de extremidade de gerenciamento, mas não pode acessar esse ponto de extremidade.

Para determinar o endereço IP de gerenciamento, faça uma pesquisa de DNS em seu FQDN de instância gerenciada: `mi-name.zone_id.database.windows.net`. Isso retornará uma entrada DNS como `trx.region-a.worker.vnet.database.windows.net`. Em seguida, você pode fazer uma pesquisa de DNS nesse FQDN com a ". vnet" removida. Isso retornará o endereço IP de gerenciamento. 

Esse PowerShell fará tudo por você se você substituir \<\> de FQDN de MI pela entrada DNS da sua instância gerenciada: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Para obter mais informações sobre instâncias gerenciadas e conectividade, consulte [instância gerenciada do banco de dados SQL do Azure arquitetura de conectividade](sql-database-managed-instance-connectivity-architecture.md).
