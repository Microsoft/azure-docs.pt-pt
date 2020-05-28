---
title: Verifique a segurança do porto em firewall incorporado
description: Saiba como verificar a proteção contra firewall incorporada em 1º Lugar, em Caso Gerido por Azure SQL.
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
ms.openlocfilehash: 04a630e51a68fab8f6c60262fbd14fad2e4aef1c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045075"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Verifique a firewall integrada do Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

As regras de [segurança obrigatórias](connectivity-architecture-overview.md#mandatory-inbound-security-rules) da Instância Azure SQL exigem que as portas de gestão 9000, 9003, 1438, 1440, 1452 sejam abertas a partir de **qualquer fonte** do Grupo de Segurança da Rede (NSG) que proteja a Instância Gerida sQL. Embora estas portas estejam abertas ao nível do NSG, estão protegidas ao nível da rede pela firewall incorporada.

## <a name="verify-firewall"></a>Verifique firewall

Para verificar estas portas, utilize qualquer ferramenta de scanner de segurança para testar estas portas. A imagem que se segue mostra como usar uma destas ferramentas.

![Verificação de firewall incorporado](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre instâncias geridas pela SQL e conectividade, consulte a Arquitetura de Conectividade gerida pelo [Azure SQL](connectivity-architecture-overview.md).
