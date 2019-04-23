---
title: Proteger pontos de extremidade públicos de instância gerida - base de dados do Azure SQL instância gerida | Documentos da Microsoft
description: Utilizar com segurança os pontos finais públicos no Azure com a instância gerida
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014536"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>Utilizar a base de dados do Azure SQL gerida a instância de forma segura com o ponto final público

Base de dados do SQL do Azure foi possível ativar a instância gerida para fornecer conectividade de usuário pela [ponto final público](../virtual-network/virtual-network-service-endpoints-overview.md). Este artigo fornece orientações sobre como tornar esta configuração mais segura.

## <a name="scenarios"></a>Cenários

Instância gerida fornece o ponto final privado para ativar a conectividade a partir de dentro de sua rede virtual. A opção padrão é fornecer isolamento máximo. No entanto, existem cenários onde é necessária uma ligação de ponto final público:

- Integração com as ofertas de PaaS apenas multi-inquilinos.
- Débito mais elevado de troca de dados que o uso de VPN.
- As políticas da empresa proíbem PaaS dentro de redes corporativas.

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>Implementar a instância gerida para acesso de ponto final público

Embora não seja obrigatório, o modelo de implementação comuns para uma instância gerida com acesso de ponto final público é criar a instância numa rede virtual isolada dedicada. Nesta configuração, a rede virtual é utilizada apenas para isolamento de virtual cluster. Não é relevante se o espaço de endereços IP de instância gerida sobrepõe-se com um espaço de endereços IP de rede empresarial.

## <a name="securing-data-in-motion"></a>Proteção dos dados em movimento

Tráfego de dados de instância gerida é sempre encriptado se o driver cliente suporta a encriptação. Dados entre a instância gerida e outros serviços do Azure ou a máquinas virtuais do Azure nunca sai backbone do Azure. Se houver uma instância gerida para uma ligação de rede no local, é recomendado utilizar o Expressroute com peering da Microsoft. Express Route ajudará a evitar de mover dados pela Internet pública (para conectividade privada de instância gerida, apenas peering privado pode ser utilizado).

## <a name="locking-down-inbound-and-outbound-connectivity"></a>Bloquear a conectividade de entrada e saída

O diagrama seguinte mostra as configurações de segurança recomendadas.

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

A instância gerida tem um [dedicado endereço de ponto final público](sql-database-managed-instance-find-management-endpoint-ip-address.md). Este endereço IP deve ser definido na firewall de saída de lado do cliente e regras do grupo de segurança de rede para limitar a conectividade de saída.

Para garantir que o tráfego para a instância gerida é proveniente de origens confiáveis, é recomendado para ligar a partir de origens com endereços IP conhecidos. Limitar o acesso para o ponto final público de instância gerida na porta 3342 utilizar um grupo de segurança de rede.

Quando os clientes têm de iniciar uma ligação a partir de uma rede no local, certifique-se de que o endereço de origem for convertido num conjunto conhecido de IPs. Se o que não pode ser obtido (por exemplo, a força de trabalho móvel que está a ser um cenário típico), é recomendado que utilize [Point-to-site VPN ligações e um ponto final privado](sql-database-managed-instance-configure-p2s.md).

Se as ligações são iniciadas a partir do Azure, recomenda-se que o tráfego proveniente de bem conhecidos atribuído [VIP](../virtual-network/virtual-networks-reserved-public-ip.md) (por exemplo, as máquinas virtuais). Para facilitar a gestão de endereços VIP, os clientes deve considerar o uso [prefixo do endereço IP público](../virtual-network/public-ip-address-prefix.md).