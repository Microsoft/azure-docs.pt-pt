---
title: Descubra firewall de instância gerida incorporada
description: Saiba como verificar a proteção contra firewall incorporada na Instância gerida pela Base de Dados Azure SQL.
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
ms.openlocfilehash: 91b8b37de69a3f08bf11650e0328b616d8633e7a
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83769926"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Verificar a firewall incorporada da Instância Gerida

As regras de [segurança obrigatórias](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) da Instância Gerida exigem que os portos de gestão 9000, 9003, 1438, 1440, 1452 sejam abertos a partir de **qualquer fonte** do Grupo de Segurança da Rede (NSG) que proteja a Instância Gerida. Embora estas portas estejam abertas ao nível do NSG, estão protegidas ao nível da rede pela firewall incorporada.

## <a name="verify-firewall"></a>Verifique firewall

Para verificar estas portas, utilize qualquer ferramenta de scanner de segurança para testar estas portas. A imagem que se segue mostra como usar uma destas ferramentas.

![Verificação de firewall incorporado](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre instâncias geridas e conectividade, consulte [a Azure SQL Database Managed Instance Connectivity Architecture](sql-database-managed-instance-connectivity-architecture.md).