---
title: Descobrir Instância Gerenciada do Banco de Dados SQL do Azure firewall interno
description: Saiba como verificar a proteção de firewall interna no Instância Gerenciada do Banco de Dados SQL do Azure.
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
ms.openlocfilehash: 912eeb95895597b9172d50463f380dd511fe196c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688018"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Verificando o Instância Gerenciada firewall interno

O Instância Gerenciada [regras de segurança de entrada obrigatórias](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) exigem que as portas de gerenciamento 9000, 9003, 1438, 1440, 1452 sejam abertas de **qualquer fonte** no NSG (grupo de segurança de rede) que protege o instância gerenciada. Embora essas portas estejam abertas no nível de NSG, elas são protegidas no nível de rede pelo firewall interno.

## <a name="verify-firewall"></a>Verificar firewall

Para verificar essas portas, use qualquer ferramenta de verificador de segurança para testar essas portas. A captura de tela a seguir mostra como usar uma dessas ferramentas.

![Verificando o firewall interno](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre instâncias gerenciadas e conectividade, consulte [instância gerenciada do banco de dados SQL do Azure arquitetura de conectividade](sql-database-managed-instance-connectivity-architecture.md).