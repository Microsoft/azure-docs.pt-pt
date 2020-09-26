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
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: da9f3e2b6b8936c74e20a226b606082fb3e0d3ea
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263169"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Verificar a firewall incorporada do Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

As [regras de segurança obrigatórias](connectivity-architecture-overview.md#mandatory-inbound-security-rules) de entrada Azure SQL exigem que as portas de gestão 9000, 9003, 1438, 1440 e 1452 sejam abertas a partir de **qualquer fonte** do Grupo de Segurança de Rede (NSG) que proteja a SQL Managed Instance. Embora estas portas estejam abertas ao nível do NSG, estão protegidas ao nível da rede pela firewall incorporada.

## <a name="verify-firewall"></a>Verificar firewall

Para verificar estas portas, utilize qualquer ferramenta de scanner de segurança para testar estas portas. A imagem que se segue mostra como utilizar uma destas ferramentas.

![Verificação da firewall incorporada](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a SQL Managed Instance e conectividade, consulte [a arquitetura de conectividade Azure SQL Managed Instance](connectivity-architecture-overview.md).
