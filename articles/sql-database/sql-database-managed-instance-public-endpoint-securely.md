---
title: Pontos finais públicos seguros de instância gerida
description: Use com segurança pontos finais públicos em Azure com um exemplo gerido
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: f11c19ba33ee2fbae0fef265371bedad2fe29cb7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684944"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Utilize uma base de dados Azure SQL gerida de forma segura com pontos finais públicos

As instâncias geridas pela Base de Dados Azure SQL podem fornecer conectividade ao utilizador em relação aos [pontos finais públicos](../virtual-network/virtual-network-service-endpoints-overview.md). Este artigo explica como tornar esta configuração mais segura.

## <a name="scenarios"></a>Cenários

Uma instância gerida pela Base de Dados SQL fornece um ponto final privado para permitir a conectividade a partir do interior da sua rede virtual. A opção padrão é proporcionar o máximo isolamento. No entanto, existem cenários em que é necessário fornecer uma ligação de ponto final público:

- A instância gerida deve integrar-se com ofertas de plataforma-como-serviço multi-inquilinos (PaaS).
- Precisa de uma maior perecção de troca de dados do que é possível quando se está a utilizar uma VPN.
- As políticas da empresa proíbem o PaaS dentro das redes corporativas.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Implementar uma instância gerida para acesso ao ponto final público

Embora não seja obrigatório, o modelo comum de implantação para uma instância gerida com acesso ao ponto final público é criar a instância numa rede virtual isolada dedicada. Nesta configuração, a rede virtual é usada apenas para isolamento de cluster virtual. Não importa se o espaço de endereço IP da instância gerida se sobrepõe ao espaço de endereçoIP de uma rede corporativa.

## <a name="secure-data-in-motion"></a>Proteger dados em movimento

O tráfego de dados de instância gerido é sempre encriptado se o condutor do cliente suportar a encriptação. Os dados enviados entre a instância gerida e outras máquinas virtuais Azure ou serviços Azure nunca saem da espinha dorsal do Azure. Se houver uma ligação entre a instância gerida e uma rede no local, recomendamos que utilize o Azure ExpressRoute. O ExpressRoute ajuda-o a evitar a movimentação de dados através da internet pública. Para a conectividade privada gerida, apenas o epeering privado pode ser usado.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bloquear a conectividade de entrada e saída

O diagrama seguinte mostra as configurações de segurança recomendadas:

![Configurações de segurança para bloquear a conectividade de entrada e saída](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Um caso gerido tem um endereço final dedicado ao [público.](sql-database-managed-instance-find-management-endpoint-ip-address.md) Na firewall de saída do lado do cliente e nas regras do grupo de segurança da rede, detetou este endereço IP do ponto final público para limitar a conectividade de saída.

Para garantir que o tráfego para a instância gerida provém de fontes fidedignas, recomendamos a ligação de fontes com endereços IP bem conhecidos. Utilize um grupo de segurança de rede para limitar o acesso ao ponto final da instância gerida no porto 3342.

Quando os clientes precisarem de iniciar uma ligação a partir de uma rede no local, certifique-se de que o endereço originário é traduzido para um conjunto bem conhecido de endereços IP. Se não o puder fazer (por exemplo, uma mão de obra móvel é um cenário típico), recomendamos que utilize [ligações VPN ponto-a-site e um ponto final privado](sql-database-managed-instance-configure-p2s.md).

Se as ligações forem iniciadas a partir de Azure, recomendamos que o tráfego venha de um [conhecido endereço IP virtual](../virtual-network/virtual-networks-reserved-public-ip.md) atribuído (por exemplo, uma máquina virtual). Para facilitar a gestão dos endereços IP virtuais (VIP), é melhor utilizar [prefixos de endereçoip públicos](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar o ponto final público para gerir casos: [Configurar o ponto final do público](sql-database-managed-instance-public-endpoint-configure.md)
