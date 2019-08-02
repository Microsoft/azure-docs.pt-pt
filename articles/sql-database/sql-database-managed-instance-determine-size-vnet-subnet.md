---
title: Instância Gerenciada do Banco de Dados SQL do Azure determinar o tamanho da VNet/sub-rede | Microsoft Docs
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
ms.openlocfilehash: 4b627b13fb79cd5105a95d9161d9239f28f2e062
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567503"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Determinar o tamanho da sub-rede VNet para Instância Gerenciada do Banco de Dados SQL do Azure

Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantado em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md)do Azure.

O número de instâncias gerenciadas que podem ser implantadas na sub-rede da VNet depende do tamanho da sub-rede (intervalo de sub-rede).

Quando você cria uma Instância Gerenciada, o Azure aloca um número de máquinas virtuais dependendo da camada selecionada durante o provisionamento. Como essas máquinas virtuais estão associadas à sua sub-rede, elas exigem endereços IP. Para garantir a alta disponibilidade durante as operações regulares e a manutenção do serviço, o Azure pode alocar máquinas virtuais adicionais. Como resultado, o número de endereços IP necessários em uma sub-rede é maior do que o número de instâncias gerenciadas nessa sub-rede.

Por design, uma Instância Gerenciada precisa de um mínimo de 16 endereços IP em uma sub-rede e pode usar até 256 endereços IP. Como resultado, você pode usar uma máscara de sub-rede entre/28 e/24 ao definir seus intervalos de IP de sub-rede. Um bit de máscara de rede de/28 (14 hosts por rede) é um bom tamanho para uma única finalidade geral ou implantação crítica para os negócios. Um bit de máscara de/27 (30 hosts por rede) é ideal para várias implantações de Instância Gerenciada dentro da mesma VNet. As configurações de bits de máscara de/26 (62 hosts) e/24 (hosts 254) permitem a expansão adicional da VNet para dar suporte a instâncias gerenciadas adicionais.

> [!IMPORTANT]
> Um tamanho de sub-rede com 16 endereços IP é o mínimo simples com potencial limitado para o maior Instância Gerenciada escalar horizontalmente. A escolha da sub-rede com o prefixo/27 ou abaixo é altamente recomendável.

## <a name="determine-subnet-size"></a>Determinar o tamanho da sub-rede

Se você planeja implantar várias instâncias gerenciadas dentro da sub-rede e precisar otimizar o tamanho da sub-rede, use esses parâmetros para formar um cálculo:

- O Azure usa cinco endereços IP na sub-rede para suas próprias necessidades
- Cada instância de Uso Geral precisa de dois endereços
- Cada instância de Comercialmente Crítico precisa de quatro endereços

**Exemplo**: Você planeja ter três Uso Geral e duas Comercialmente Crítico instâncias gerenciadas. Isso significa que você precisa de 5 + 3 * 2 + 2 * 4 = 19 endereços IP. Como os intervalos IP são definidos na potência de 2, você precisa do intervalo IP de 32 (2 ^ 5) endereços IP. Portanto, você precisa reservar a sub-rede com a máscara de sub-rede de/27.

> [!IMPORTANT]
> O cálculo exibido acima se tornará obsoleto com outras melhorias.

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma visão geral, consulte [o que é um instância gerenciada](sql-database-managed-instance.md).
- Saiba mais sobre [a arquitetura de conectividade para instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Consulte como [criar VNet onde você implantará instâncias gerenciadas](sql-database-managed-instance-create-vnet-subnet.md)
- Para problemas de DNS, consulte Configurando [um DNS personalizado](sql-database-managed-instance-custom-dns.md)
