---
title: Proteger pontos de extremidade públicos de instância gerida - base de dados do Azure SQL instância gerida | Documentos da Microsoft
description: Utilizar com segurança os pontos finais públicos no Azure com uma instância gerida
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65470282"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Utilizar uma instância gerida da base de dados do Azure SQL em segurança com pontos finais públicos

Base de dados do SQL do Azure instâncias geridas podem fornecer conectividade de usuário pela [pontos finais públicos](../virtual-network/virtual-network-service-endpoints-overview.md). Este artigo explica como tornar esta configuração mais segura.

## <a name="scenarios"></a>Cenários

Uma instância gerida da base de dados SQL fornece um ponto final privado para permitir a ligação da sua rede virtual. A opção padrão é fornecer isolamento máximo. No entanto, existem situações em que precisa para fornecer uma ligação de ponto final público:

- A instância gerida tem de integrar com várias-tenant plataforma-como-serviço (PaaS) as ofertas de apenas.
- É necessário um débito mais elevado de troca de dados que é possível quando estiver usando uma VPN.
- As políticas da empresa proíbem PaaS dentro de redes corporativas.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Implementar uma instância gerida para o acesso de ponto final público

Embora não seja obrigatório, o modelo de implementação comuns para uma instância gerida com acesso de ponto final público é criar a instância numa rede virtual isolada dedicada. Nesta configuração, a rede virtual é utilizada apenas para isolamento de virtual cluster. Não importa se o espaço de endereços IP a instância gerida sobrepõe-se ao espaço de endereços IP de uma rede empresarial.

## <a name="secure-data-in-motion"></a>Proteger os dados em movimento

Tráfego de dados de instância gerida é sempre encriptado se o driver cliente suporta a encriptação. Dados enviados entre a instância gerida e outros serviços do Azure ou a máquinas virtuais do Azure nunca sai backbone do Azure. Se existir uma ligação entre a instância gerida e uma rede no local, recomendamos que utilize o Azure ExpressRoute com peering da Microsoft. ExpressRoute ajuda-o a evitar de mover dados através da internet pública. Para a conectividade privada de instância gerida, pode ser utilizado apenas peering privado.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bloquear a conectividade de entrada e saída

O diagrama seguinte mostra as configurações de segurança recomendadas:

![Configurações de segurança para bloquear a conectividade de entrada e saída](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Uma instância gerida tem um [dedicado endereço de ponto final público](sql-database-managed-instance-find-management-endpoint-ip-address.md). Na firewall de saída do lado do cliente e nas regras de grupo de segurança de rede, defina este endereço IP do ponto final público para limitar a conectividade de saída.

Para garantir que o tráfego para a instância gerida é proveniente de origens confiáveis, recomendamos a ligar a partir de origens com endereços IP conhecidos. Utilize um grupo de segurança de rede para limitar o acesso para o ponto de final público na porta 3342 instância gerida.

Quando os clientes têm de iniciar uma ligação a partir de uma rede no local, certifique-se de que o endereço de origem for convertido num conjunto conhecido de endereços IP. Se não é possível fazê-lo (por exemplo, uma força de trabalho móvel que está a ser um cenário típico), recomendamos que utilize [ligações de VPN ponto a site e um ponto final privado](sql-database-managed-instance-configure-p2s.md).

Se as ligações são iniciadas a partir do Azure, recomendamos que o tráfego proveniente de um conhecido atribuído [endereço IP virtual](../virtual-network/virtual-networks-reserved-public-ip.md) (por exemplo, uma máquina virtual). Para facilitar o gerenciamento de endereços IP virtuais (VIP) mais fácil, deve usar [prefixos de endereços IP públicos](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Passos Seguintes

- Saiba como configurar o ponto final público para gerir instâncias: [Configurar o ponto final público](sql-database-managed-instance-public-endpoint-configure.md)
