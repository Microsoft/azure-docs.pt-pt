---
title: Instância Gerenciada do Banco de Dados SQL do Azure determinar o tamanho da VNet/sub-rede
description: Este tópico descreve como calcular o tamanho da sub-rede em que as instâncias gerenciadas do banco de dados SQL do Azure serão implantadas.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: fb0d79a82553349d5dad547a2d78a460301cbd6d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688128"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Determinar o tamanho da sub-rede VNet para Instância Gerenciada do Banco de Dados SQL do Azure

Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantado em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md)do Azure.

O número de instâncias gerenciadas que podem ser implantadas na sub-rede da VNet depende do tamanho da sub-rede (intervalo de sub-rede).

Quando você cria uma Instância Gerenciada, o Azure aloca um número de máquinas virtuais dependendo da camada selecionada durante o provisionamento. Como essas máquinas virtuais estão associadas à sua sub-rede, elas exigem endereços IP. Para garantir a alta disponibilidade durante as operações regulares e a manutenção do serviço, o Azure pode alocar máquinas virtuais adicionais. Como resultado, o número de endereços IP necessários em uma sub-rede é maior do que o número de instâncias gerenciadas nessa sub-rede.

Por design, uma Instância Gerenciada precisa de um mínimo de 16 endereços IP em uma sub-rede e pode usar até 256 endereços IP. Como resultado, você pode usar uma máscara de sub-rede entre/28 e/24 ao definir seus intervalos de IP de sub-rede. Um bit de máscara de rede de/28 (14 hosts por rede) é um bom tamanho para uma única finalidade geral ou implantação crítica para os negócios. Um bit de máscara de/27 (30 hosts por rede) é ideal para várias implantações de Instância Gerenciada dentro da mesma VNet. As configurações de bits de máscara de/26 (62 hosts) e/24 (hosts 254) permitem a expansão adicional da VNet para dar suporte a instâncias gerenciadas adicionais.

> [!IMPORTANT]
> Um tamanho de sub-rede com 16 endereços IP é o mínimo, com potencial limitado, em que uma operação de dimensionamento como alteração de tamanho vCore não é suportada. É altamente recomendável escolher a sub-rede com o prefixo/27 ou o prefixo mais longo.

## <a name="determine-subnet-size"></a>Determinar o tamanho da sub-rede

Se você planeja implantar várias instâncias gerenciadas dentro da sub-rede e precisar otimizar o tamanho da sub-rede, use esses parâmetros para formar um cálculo:

- O Azure usa cinco endereços IP na sub-rede para suas próprias necessidades
- Cada instância de Uso Geral precisa de dois endereços
- Cada instância de Comercialmente Crítico precisa de quatro endereços

**Exemplo**: você planeja ter três uso geral e duas comercialmente crítico instâncias gerenciadas. Isso significa que você precisa de 5 + 3 * 2 + 2 * 4 = 19 endereços IP. Como os intervalos IP são definidos na potência de 2, você precisa do intervalo IP de 32 (2 ^ 5) endereços IP. Portanto, você precisa reservar a sub-rede com a máscara de sub-rede de/27.

> [!IMPORTANT]
> O cálculo exibido acima se tornará obsoleto com outras melhorias.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral, consulte [o que é um instância gerenciada](sql-database-managed-instance.md).
- Saiba mais sobre [a arquitetura de conectividade para instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Consulte como [criar VNet onde você implantará instâncias gerenciadas](sql-database-managed-instance-create-vnet-subnet.md)
- Para problemas de DNS, consulte [Configurando um DNS personalizado](sql-database-managed-instance-custom-dns.md)
