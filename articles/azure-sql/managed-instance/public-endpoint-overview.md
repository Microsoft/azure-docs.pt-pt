---
title: Secure Azure SQL Managed Instance pontos finais públicos
description: Utilize de forma segura os pontos finais públicos em Azure SQL Managed Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 05/08/2019
ms.openlocfilehash: d9c1828732b9a4e0e85c3af2263f097edd54437d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332853"
---
# <a name="use-azure-sql-managed-instance-securely-with-public-endpoints"></a>Use Azure SQL Gestd Instance de forma segura com pontos finais públicos
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance pode fornecer conectividade do utilizador sobre [os pontos finais públicos](../../virtual-network/virtual-network-service-endpoints-overview.md). Este artigo explica como tornar esta configuração mais segura.

## <a name="scenarios"></a>Cenários

A Azure SQL Managed Instance fornece um ponto final privado para permitir a conectividade a partir de dentro da sua rede virtual. A opção padrão é proporcionar o isolamento máximo. No entanto, existem cenários em que é necessário fornecer uma ligação pública ao ponto final:

- O caso gerido deve integrar-se com ofertas de plataforma-como-serviço (PaaS) de vários inquilinos.
- Precisa de uma maior produção de troca de dados do que é possível quando se está a usar uma VPN.
- As políticas da empresa proíbem o PaaS dentro das redes corporativas.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Implementar um exemplo gerido para o acesso ao ponto final público

Embora não seja obrigatório, o modelo comum de implementação para uma instância gerida com acesso ao ponto final público é criar o caso numa rede virtual isolada dedicada. Nesta configuração, a rede virtual é usada apenas para isolamento de cluster virtual. Não importa se o espaço de endereço IP da instância gerida se sobrepõe ao espaço de endereço IP de uma rede corporativa.

## <a name="secure-data-in-motion"></a>Dados seguros em movimento

O tráfego de dados de casos geridos SQL é sempre encriptado se o controlador do cliente suportar a encriptação. Os dados enviados entre a instância gerida e outras máquinas virtuais Azure ou serviços Azure nunca saem da espinha dorsal do Azure. Se houver uma ligação entre a instância gerida e uma rede no local, recomendamos que utilize o Azure ExpressRoute. O ExpressRoute ajuda-o a evitar a movimentação de dados pela internet pública. Para a conectividade privada gerida, apenas o espreitamento privado pode ser usado.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bloqueie a conectividade de entrada e saída

O diagrama a seguir mostra as configurações de segurança recomendadas:

![Configurações de segurança para bloquear a conectividade de entrada e saída](./media/public-endpoint-overview/managed-instance-vnet.png)

Um caso gerido tem um [endereço de ponto final público dedicado](management-endpoint-find-ip-address.md). Na firewall de saída do lado do cliente e nas regras do grupo de segurança da rede, desaponte este endereço IP de ponto final público para limitar a conectividade de saída.

Para garantir que o tráfego para a instância gerida provém de fontes fidedignas, recomendamos a ligação de fontes com endereços IP bem conhecidos. Utilize um grupo de segurança de rede para limitar o acesso ao ponto final público gerido na porta 3342.

Quando os clientes precisarem de iniciar uma ligação a partir de uma rede no local, certifique-se de que o endereço de origem é traduzido para um conhecido conjunto de endereços IP. Se não conseguir fazê-lo (por exemplo, uma [mão de obra](point-to-site-p2s-configure.md)móvel é um cenário típico), recomendamos que utilize ligações VPN ponto a local e um ponto final privado .

Se as ligações forem iniciadas a partir do Azure, recomendamos que o tráfego provém de um [conhecido endereço IP virtual](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) atribuído (por exemplo, uma máquina virtual). Para facilitar a gestão dos endereços IP virtuais (VIP), poderá querer utilizar [prefixos de endereço IP públicos](../../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar o ponto final público para gerir casos: [Configurar o ponto final público](public-endpoint-configure.md)
