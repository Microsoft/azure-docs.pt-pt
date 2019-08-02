---
title: Descobrir ponto de extremidade de gerenciamento de Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
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
ms.openlocfilehash: a0d47c61bf84cfb22c767fd09b3feed74f55b7fc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567491"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Determinar o endereço IP do ponto de extremidade de gerenciamento

O cluster virtual Instância Gerenciada do Banco de Dados SQL do Azure contém um ponto de extremidade de gerenciamento que a Microsoft usa para operações de gerenciamento. O ponto de extremidade de gerenciamento é protegido com um firewall interno no nível da rede e a verificação de certificado mútuo no nível do aplicativo. Você pode determinar o endereço IP do ponto de extremidade de gerenciamento, mas não pode acessar esse ponto de extremidade.

Para determinar o endereço IP de gerenciamento, faça uma pesquisa de DNS no FQDN da instância `mi-name.zone_id.database.windows.net`gerenciada:. Isso retornará uma entrada DNS como `trx.region-a.worker.vnet.database.windows.net`essa. Em seguida, você pode fazer uma pesquisa de DNS nesse FQDN com a ". vnet" removida. Isso retornará o endereço IP de gerenciamento. 

Esse PowerShell fará tudo por você se você substituir \<o FQDN\> de mi pela entrada DNS da sua instância gerenciada: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Para obter mais informações sobre instâncias gerenciadas e conectividade, consulte [instância gerenciada do banco de dados SQL do Azure arquitetura de conectividade](sql-database-managed-instance-connectivity-architecture.md).
