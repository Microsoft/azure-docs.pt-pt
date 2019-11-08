---
title: Pontos de extremidade públicos de instância gerenciada segura
description: Usar pontos de extremidade públicos com segurança no Azure com uma instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: 6dfeab3530445f8f9a102f47039d15b04fdf134a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821744"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Usar uma instância gerenciada do banco de dados SQL do Azure com segurança com pontos de extremidade públicos

As instâncias gerenciadas do banco de dados SQL do Azure podem fornecer conectividade de usuário em [pontos de extremidade públicos](../virtual-network/virtual-network-service-endpoints-overview.md). Este artigo explica como tornar essa configuração mais segura.

## <a name="scenarios"></a>Cenários

Uma instância gerenciada do banco de dados SQL fornece um ponto de extremidade privado para permitir a conectividade de dentro de sua rede virtual. A opção padrão é fornecer isolamento máximo. No entanto, há situações em que você precisa fornecer uma conexão de ponto de extremidade pública:

- A instância gerenciada deve ser integrada com ofertas de PaaS (plataforma como serviço) multilocatário.
- Você precisa de maior taxa de transferência de troca de dados do que é possível quando você está usando uma VPN.
- As políticas da empresa proíbem PaaS dentro de redes corporativas.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Implantar uma instância gerenciada para acesso de ponto de extremidade público

Embora não seja obrigatório, o modelo de implantação comum para uma instância gerenciada com acesso de ponto de extremidade público é criar a instância em uma rede virtual isolada dedicada. Nessa configuração, a rede virtual é usada somente para isolamento de cluster virtual. Não importa se o espaço de endereço IP da instância gerenciada se sobrepõe ao espaço de endereço IP de uma rede corporativa.

## <a name="secure-data-in-motion"></a>Proteger dados em movimento

O tráfego de dados de instância gerenciada será sempre criptografado se o driver de cliente oferecer suporte à criptografia. Os dados enviados entre a instância gerenciada e outras máquinas virtuais do Azure ou serviços do Azure nunca deixam o backbone do Azure. Se houver uma conexão entre a instância gerenciada e uma rede local, recomendamos que você use o Azure ExpressRoute com o emparelhamento da Microsoft. O ExpressRoute ajuda você a evitar a movimentação de dados pela Internet pública. Para conectividade privada de instância gerenciada, somente o emparelhamento privado pode ser usado.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bloquear a conectividade de entrada e saída

O diagrama a seguir mostra as configurações de segurança recomendadas:

![Configurações de segurança para bloquear a conectividade de entrada e saída](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Uma instância gerenciada tem um [endereço de ponto de extremidade público dedicado](sql-database-managed-instance-find-management-endpoint-ip-address.md). No firewall de saída do lado do cliente e nas regras do grupo de segurança de rede, defina esse endereço IP do ponto de extremidade público para limitar a conectividade de saída.

Para garantir que o tráfego para a instância gerenciada seja proveniente de fontes confiáveis, é recomendável conectar-se de fontes com endereços IP conhecidos. Use um grupo de segurança de rede para limitar o acesso ao ponto de extremidade público da instância gerenciada na porta 3342.

Quando os clientes precisarem iniciar uma conexão de uma rede local, verifique se o endereço de origem está traduzido para um conjunto conhecido de endereços IP. Se você não puder fazer isso (por exemplo, uma força de equipe móvel sendo um cenário típico), recomendamos que você use [conexões VPN ponto a site e um ponto de extremidade privado](sql-database-managed-instance-configure-p2s.md).

Se as conexões forem iniciadas no Azure, recomendamos que o tráfego venha de um [endereço IP virtual](../virtual-network/virtual-networks-reserved-public-ip.md) atribuído conhecido (por exemplo, uma máquina virtual). Para facilitar o gerenciamento de endereços VIP (IP virtual), talvez você queira usar [prefixos de endereço IP público](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar o ponto de extremidade público para gerenciar instâncias: [Configurar ponto de extremidade público](sql-database-managed-instance-public-endpoint-configure.md)
