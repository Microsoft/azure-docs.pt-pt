---
title: Gateway de VPN clássico para migração do Gerenciador de recursos | Microsoft Docs
description: Esta página fornece uma visão geral do gateway de VPN clássico para a migração do Gerenciador de recursos.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: amsriva
ms.openlocfilehash: 966df2a01d0178c19c2ae6e698a6610bf0e321c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425828"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Gateway de VPN clássico para migração do Gerenciador de recursos
Os gateways de VPN agora podem ser migrados do modelo de implantação clássico para o Gerenciador de recursos. Você pode ler mais sobre Azure Resource Manager [recursos e benefícios](../azure-resource-manager/management/overview.md). Neste artigo, detalharemos como migrar de implantações clássicas para um modelo baseado no Resource Manager mais recente. 

Os gateways de VPN são migrados como parte da migração de VNet do clássico para o Gerenciador de recursos. Essa migração é feita uma VNet por vez. Não há nenhum requisito adicional em termos de ferramentas ou pré-requisitos para a migração. As etapas de migração são idênticas à migração de VNet existente e estão documentadas na [página de migração de recursos de IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Não há nenhum tempo de inatividade de caminho de dados durante a migração e, portanto, as cargas de trabalho existentes continuarão a funcionar sem perda de conectividade local durante a migração. O endereço IP público associado ao gateway de VPN não é alterado durante o processo de migração. Isso implica que não será necessário reconfigurar o roteador local quando a migração for concluída.  

O modelo no Gerenciador de recursos é diferente do modelo clássico e é composto por gateways de rede virtual, gateways de rede local e recursos de conexão. Eles representam o próprio gateway de VPN, o site local que representa o espaço de endereço local e a conectividade entre os dois, respectivamente. Depois que a migração for concluída, seus gateways não estarão disponíveis no modelo clássico e todas as operações de gerenciamento em gateways de rede virtual, gateways de rede local e objetos de conexão deverão ser executados usando o modelo do Resource Manager.

## <a name="supported-scenarios"></a>Cenários suportados
Os cenários de conectividade VPN mais comuns são cobertos pela migração clássica para o Resource Manager. Os cenários com suporte incluem-

* Conectividade ponto a site
* Conectividade site a site com o gateway de VPN conectado à localização local
* Conectividade VNet com VNet entre dois VNets usando gateways de VPN
* Vários VNets conectados à mesma localização local
* Conectividade multissite
* VNets habilitado para túnel forçado

Cenários que não têm suporte incluem-  

* Atualmente, não há suporte para VNet com gateway de ExpressRoute e gateway de VPN.
* Cenários de trânsito em que as extensões de VM são conectadas a servidores locais. As limitações de conectividade de VPN de trânsito são detalhadas abaixo.

> [!NOTE]
> A validação de CIDR no modelo do Resource Manager é mais estrita do que a do modelo clássico. Antes de migrar, verifique se os intervalos de endereços clássicos estão em conformidade com o formato CIDR válido antes de iniciar a migração. O CIDR pode ser validado usando qualquer validador CIDR comum. Os sites VNet ou locais com intervalos CIDR inválidos quando migrados resultarão em estado de falha.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migração de conectividade VNet para VNet
A conectividade de VNet para VNet no clássico foi obtida com a criação de uma representação de site local da VNet conectada. Os clientes eram obrigados a criar dois sites locais que representavam os dois VNets que precisavam ser conectados juntos. Em seguida, eles foram conectados ao VNets correspondente usando o túnel IPsec para estabelecer a conectividade entre os dois VNets. Esse modelo tem desafios de gerenciamento, pois qualquer alteração de intervalo de endereços em uma VNet também deve ser mantida na representação do site local correspondente. No modelo do Resource Manager, essa solução alternativa não é mais necessária. A conexão entre os dois VNets pode ser obtida diretamente usando o tipo de conexão ' Vnet2Vnet ' no recurso de conexão. 

![Captura de tela da migração de VNet para VNet.](./media/vpn-gateway-migration/migration1.png)

Durante a migração de VNet, detectamos que a entidade conectada ao gateway de VPN da VNet atual é outra VNet e garante que, depois que a migração de ambos os VNets for concluída, você não verá mais dois sites locais representando a outra VNet. O modelo clássico de dois gateways de VPN, dois sites locais e duas conexões entre eles são transformados no modelo do Resource Manager com dois gateways de VPN e duas conexões do tipo Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Conectividade de VPN de trânsito
Você pode configurar gateways de VPN em uma topologia de modo que a conectividade local para uma VNet seja obtida conectando-se a outra VNet conectada diretamente ao local. Essa é a conectividade de VPN em trânsito em que as instâncias na primeira VNet estão conectadas a recursos locais via trânsito para o gateway de VPN na VNet conectada que está diretamente conectada ao local. Para obter essa configuração no modelo de implantação clássico, você precisaria criar um site local que tenha prefixos agregados que representem tanto a VNet conectada quanto o espaço de endereço local. Esse site local de reapresentação é então conectado à VNet para obter conectividade de trânsito. Esse modelo clássico também tem desafios de gerenciamento semelhantes, já que qualquer alteração no intervalo de endereços local também deve ser mantida no site local que representa a agregação de VNet e local. A introdução do suporte a BGP nos gateways com suporte do Gerenciador de recursos simplifica a capacidade de gerenciamento, já que os gateways conectados podem aprender rotas locais sem modificações manuais em prefixos.

![Captura de tela do cenário de roteamento de trânsito.](./media/vpn-gateway-migration/migration2.png)

Como podemos transformar a conectividade VNet em VNet sem a necessidade de sites locais, o cenário de trânsito perde a conectividade local para VNet que está conectada indiretamente ao local. A perda de conectividade pode ser atenuada das duas maneiras a seguir, após a conclusão da migração- 

* Habilite o BGP em gateways de VPN que estão conectados juntos e localmente. Habilitar o BGP restaura a conectividade sem nenhuma outra alteração de configuração, uma vez que as rotas são aprendidas e anunciadas entre os gateways de VNet. Observe que a opção BGP só está disponível em SKUs Standard e superiores.
* Estabeleça uma conexão explícita da VNet afetada com o gateway de rede local que representa a localização no local. Isso também exigiria a alteração da configuração no roteador local para criar e configurar o túnel IPsec.

## <a name="next-steps"></a>Passos seguintes
Depois de aprender sobre o suporte à migração do gateway de VPN, vá para [migração com suporte da plataforma de recursos de IaaS do clássico para o Gerenciador de recursos](../virtual-machines/windows/migration-classic-resource-manager-ps.md) para começar.

