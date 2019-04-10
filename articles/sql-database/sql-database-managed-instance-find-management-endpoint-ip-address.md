---
title: Detetar ponto final de gestão de instância gerida da base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como obter a instância gerida da base de dados SQL do Azure ponto final público endereço IP de gestão e verificar a sua proteção de firewall interno
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b7eb9ecd6b94aad263346ad6b5c45b694e0bd46f
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359763"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Determinar o endereço IP do ponto final de gestão

O cluster virtual de instância gerida da base de dados SQL do Azure contém um ponto final de gestão que a Microsoft utiliza para operações de gestão. O ponto final de gestão está protegido com um firewall interno a verificação de certificado mútua e nível de rede no nível do aplicativo. Pode determinar o endereço IP do ponto final de gestão, mas não pode aceder a este ponto final.

## <a name="determine-ip-address"></a>Determinar o endereço IP

Vamos supor que o anfitrião da instância gerida é `mi-demo.xxxxxx.database.windows.net`. Executar `nslookup` usando o nome de anfitrião.

![Resolução de nomes de anfitrião interno](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Agora, fazer outra `nslookup` para remover o nome realçado a `.vnet.` segmento. Obterá o endereço IP público ao executar este comando.

![Resolução de endereço IP público](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as instâncias geridas e a conectividade, consulte [arquitetura de conectividade de instância de geridas da base de dados do SQL Azure](sql-database-managed-instance-connectivity-architecture.md).
