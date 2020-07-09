---
title: Verifique a segurança do porto na firewall incorporada
description: Saiba como verificar a proteção contra firewall incorporada em Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 1d2fffabba3615394bdf96ed487177bf21f3ecec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708710"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Verifique a firewall incorporada da Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

As [regras de segurança obrigatórias](connectivity-architecture-overview.md#mandatory-inbound-security-rules) de entrada Azure SQL exigem que as portas de gestão 9000, 9003, 1438, 1440 e 1452 sejam abertas a partir de **qualquer fonte** do Grupo de Segurança de Rede (NSG) que proteja a SQL Managed Instance. Embora estas portas estejam abertas ao nível do NSG, estão protegidas ao nível da rede pela firewall incorporada.

## <a name="verify-firewall"></a>Verificar firewall

Para verificar estas portas, utilize qualquer ferramenta de scanner de segurança para testar estas portas. A imagem que se segue mostra como utilizar uma destas ferramentas.

![Verificação da firewall incorporada](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a SQL Managed Instance e conectividade, consulte [a arquitetura de conectividade Azure SQL Managed Instance](connectivity-architecture-overview.md).
