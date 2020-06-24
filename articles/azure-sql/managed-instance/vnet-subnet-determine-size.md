---
title: Determinar o tamanho da sub-rede necessária & gama
titleSuffix: Azure SQL Managed Instance
description: Este tópico descreve como calcular o tamanho da sub-rede onde a Azure SQL Managed Instance será implantada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 8d1073dbcced9532390776a23dd17c1f572cce40
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686686"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Determinar o tamanho da sub-rede necessária & gama para Azure SQL Gestded Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance deve ser implantado dentro de uma rede virtual Azure [(VNet)](../../virtual-network/virtual-networks-overview.md).

O número de casos geridos que podem ser implantados na sub-rede de um VNet depende do tamanho da sub-rede (gama de sub-redes).

Ao criar uma instância gerida, o Azure atribui uma série de máquinas virtuais dependendo do nível selecionado durante o provisionamento. Como estas máquinas virtuais estão associadas à sua sub-rede, necessitam de endereços IP. Para garantir uma elevada disponibilidade durante as operações regulares e manutenção do serviço, o Azure pode alocar máquinas virtuais adicionais. Como resultado, o número de endereços IP necessários numa sub-rede é maior do que o número de casos geridos nessa sub-rede.

Por design, uma instância gerida precisa de um mínimo de 16 endereços IP numa sub-rede e pode usar até 256 endereços IP. Como resultado, pode utilizar uma máscara de sub-rede entre /28 e /24 ao definir as gamas IP da sub-rede. Uma parte da máscara de rede de /28 (14 anfitriões por rede) é um bom tamanho para uma única utilização geral ou crítica de negócio. Um bit de máscara de /27 (30 anfitriões por rede) é ideal para múltiplas implementações de SQL Managed Instance dentro do mesmo VNet. As configurações do bit de máscara de /26 (62 anfitriões) e /24 (254 anfitriões) permitem uma maior escala para fora do VNet para suportar casos geridos adicionais.

> [!IMPORTANT]
> Um tamanho de sub-rede com 16 endereços IP é o mínimo com um potencial limitado onde uma operação de escala como a alteração de tamanho vCore não é suportada. A escolha da sub-rede com o prefixo /27 ou o prefixo mais longo é altamente recomendada.

## <a name="determine-subnet-size"></a>Determinar o tamanho da sub-rede

Se planeia implementar várias instâncias geridas dentro da sub-rede e precisar de otimizar o tamanho da sub-rede, utilize estes parâmetros para formar um cálculo:

- A Azure utiliza cinco endereços IP na sub-rede para as suas próprias necessidades
- Cada instância de propósito geral precisa de dois endereços
- Cada instância crítica de negócios precisa de quatro endereços

**Exemplo:** Pretende ter três instâncias geridas de propósito geral e duas críticas de negócio. Isto significa que precisa de 5 + 3 * 2 + 2 * 4 = 19 endereços IP. Como os intervalos IP são definidos em potências de 2, você precisa do intervalo IP de 32 (2^5) endereços IP. Por isso, é necessário reservar a sub-rede com a máscara de sub-rede de /27.

> [!IMPORTANT]
> O cálculo acima apresentado tornar-se-á obsoleto com novas melhorias.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
- Saiba mais sobre [arquitetura de conectividade para SQL Managed Instance](connectivity-architecture-overview.md).
- Veja como [criar um VNet onde irá implementar o SQL Managed Instance](virtual-network-subnet-create-arm-template.md).
- Para questões dns, consulte [Configurar um DNS personalizado](custom-dns-configure.md).
