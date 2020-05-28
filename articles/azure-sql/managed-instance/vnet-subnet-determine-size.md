---
title: Determinar o tamanho necessário & tamanho da rede
titleSuffix: Azure SQL Managed Instance
description: Este tópico descreve como calcular o tamanho da subnet onde serão implantados os Casos Geridos Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 5eebde1fc95cb50f8ff7c13b6cbc411484ddf943
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050934"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Determine o tamanho exigido & gama de & para a instância gerida pelo Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A Instância Gerida Azure SQL deve ser implantada dentro de uma rede virtual Azure [(VNet)](../../virtual-network/virtual-networks-overview.md).

O número de Instâncias Geridas sQL que podem ser implantadas na sub-rede de VNet depende do tamanho da sub-rede (gama de sub-rede).

Ao criar uma Instância Gerida SQL, o Azure aloca uma série de máquinas virtuais dependendo do nível selecionado durante o provisionamento. Como estas máquinas virtuais estão associadas à sua sub-rede, necessitam de endereços IP. Para garantir uma elevada disponibilidade durante operações regulares e manutenção de serviços, o Azure poderá alocar máquinas virtuais adicionais. Como resultado, o número de endereços IP necessários numa subnet é maior do que o número de Instâncias Geridas SQL nessa sub-rede.

Por conceção, um Caso Gerido SQL necessita de um mínimo de 16 endereços IP numa sub-rede e pode utilizar até 256 endereços IP. Como resultado, pode utilizar uma máscara de sub-rede entre /28 e /24 ao definir as gamas IP da sub-rede. Um bit de máscara de rede de /28 (14 hospedeiros por rede) é um bom tamanho para um único propósito geral ou implantação crítica do negócio. Um bit de máscara de /27 (30 hospedeiros por rede) é ideal para uma múltipla sQL Managed Instance implementações dentro do mesmo VNet. As definições de bits de máscara de /26 (62 anfitriões) e /24 (254 anfitriões) permitem uma maior escala fora da VNet para suportar instâncias geridas por SQL adicionais.

> [!IMPORTANT]
> Um tamanho de sub-rede com 16 endereços IP é o mínimo nu com potencial limitado onde uma operação de escala como a alteração do tamanho vCore não é suportada. A escolha da sub-rede com o prefixo /27 ou prefixo mais longo é altamente recomendada.

## <a name="determine-subnet-size"></a>Determinar o tamanho da subnet

Se planeia implementar várias instâncias geridas pelo SQL dentro da subnet e precisa de otimizar o tamanho da sub-rede, utilize estes parâmetros para formar um cálculo:

- Azure usa cinco endereços IP na subnet para as suas próprias necessidades
- Cada instância de Propósito Geral precisa de dois endereços
- Cada instância Business Critical precisa de quatro endereços

**Exemplo:** Pretende ter três General Purpose e duas Instâncias Geridas sQL de Negócios Críticos. Isto significa que precisa de 5 + 3 * 2 + 2 * 4 = 19 endereços IP. Como as gamas IP são definidas na potência de 2, você precisa da gama IP de 32 (2^5) endereços IP. Por isso, é necessário reservar a sub-rede com máscara de sub-rede de /27.

> [!IMPORTANT]
> O cálculo acima apresentado tornar-se-á obsoleto com novas melhorias.

## <a name="next-steps"></a>Próximos passos

- Para uma visão geral, veja [O que é uma Instância Gerida SQL](sql-managed-instance-paas-overview.md).
- Saiba mais sobre [a arquitetura de conectividade para a Instância Gerida SQL.](connectivity-architecture-overview.md)
- Veja como [criar vNet onde irá implementar Instâncias Geridas SQL](virtual-network-subnet-create-arm-template.md)
- Para problemas dNS, consulte [Configurar um DNS personalizado](custom-dns-configure.md)
