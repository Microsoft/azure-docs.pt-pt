---
title: VPN Gateway Classic para Gestor de Recursos Migração [ Microsoft Docs
description: Esta página fornece uma visão geral da VPN Gateway Classic para a migração do Gestor de Recursos.
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
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c1a75630c6419816b048495ee87d24c81979af16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77064772"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN Gateway clássico para migração de Gestor de Recursos
Os Gateways VPN podem agora ser migrados do modelo clássico para o modelo de implantação do Gestor de Recursos. Pode ler mais sobre [funcionalidades e benefícios](../azure-resource-manager/management/overview.md)do Gestor de Recursos do Azure. Neste artigo, detalhamos como migrar de implementações clássicas para o novo modelo baseado em Recursos. 

Os Gateways VPN são migrados como parte da migração vNet do clássico para o Gestor de Recursos. Esta migração é feita um VNet de cada vez. Não existe requisitoadicional em termos de ferramentas ou pré-requisitos para a migração. Os passos de migração são idênticos à migração vnet existente e estão documentados na página de migração de [recursos iaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Não existe tempo de paragem da trajetória de dados durante a migração e, portanto, as cargas de trabalho existentes continuariam a funcionar sem perder a conectividade no local durante a migração. O endereço IP público associado ao gateway VPN não muda durante o processo de migração. Isto implica que não terá de reconfigurar o seu router no local assim que a migração estiver concluída.  

O modelo em Resource Manager é diferente do modelo clássico e é composto por gateways de rede virtuais, gateways de rede locais e recursos de conexão. Estes representam o próprio gateway VPN, o local que representa nas instalações o espaço de endereço e a conectividade entre os dois, respectivamente. Uma vez concluída a migração, os seus gateways não estariam disponíveis em modelos clássicos e todas as operações de gestão em gateways de rede virtuais, gateways de rede locais e objetos de ligação devem ser realizados usando o modelo De Gestor de Recursos.

## <a name="supported-scenarios"></a>Cenários suportados
Os cenários de conectividade VPN mais comuns são cobertos pelo clássico para a migração do Gestor de Recursos. Os cenários apoiados incluem -

* Aponte para a conectividade do local
* Site para site conectividade com VPN Gateway ligado à localização do local
* Conectividade VNet para VNet entre dois VNets usando gateways VPN
* Múltiplos VNets ligados ao mesmo na localização do local
* Conectividade multi-local
* Túneis forçados habilitados vNets

Os cenários que não são apoiados incluem -  

* A VNet com o ExpressRoute Gateway e o VPN Gateway não está atualmente suportada.
* Cenários de trânsito em que as extensões vm estão ligadas a servidores no local. As limitações de conectividade VPN de trânsito são detalhadas abaixo.

> [!NOTE]
> A validação do CIDR no modelo De Gestor de Recursos é mais rigorosa do que a do modelo clássico. Antes de migrar, certifique-se de que as gamas de endereços clássicos dadas estão em conformidade com o formato CIDR válido antes de iniciar a migração. O CIDR pode ser validado utilizando quaisquer validadores CIDR comuns. VNet ou sites locais com gamas CIDR inválidas quando migrados resultariam em estado falhado.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migração de conectividade VNet para VNet
A conectividade VNet para VNet em clássico foi conseguida através da criação de uma representação local do site local do VNet conectado. Os clientes foram obrigados a criar dois sites locais que representavam os dois VNets que precisavam de ser ligados entre si. Estes foram então ligados aos VNets correspondentes utilizando o túnel IPsec para estabelecer a conectividade entre os dois VNets. Este modelo tem desafios de gestão, uma vez que quaisquer alterações de alcance de endereço num VNet também devem ser mantidas na representação do local correspondente. No modelo do Gestor de Recursos, esta suver já não é necessária. A ligação entre os dois VNets pode ser alcançada diretamente utilizando o tipo de ligação 'Vnet2Vnet' no recurso de ligação. 

![Screenshot de VNet para migração VNet.](./media/vpn-gateway-migration/migration1.png)

Durante a migração vNet, detetamos que a entidade conectada ao gateway VPN da VNet atual é outro VNet e asseguramos que uma vez concluída a migração de ambos os VNets, deixará de ver dois sites locais que representam os outros VNet. O modelo clássico de dois gateways VPN, dois locais e duas ligações entre eles é transformado para o modelo De Gestor de Recursos com dois gateways VPN e duas ligações de tipo Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Conectividade VPN de trânsito
Pode configurar gateways VPN numa topologia de tal forma que a conectividade no local para um VNet é alcançada ligando-se a outro VNet que está diretamente ligado às instalações. Esta é a conectividade VPN de trânsito onde os casos no primeiro VNet estão ligados aos recursos no local através do trânsito para o gateway VPN em VNet conectado que está diretamente ligado às instalações. Para alcançar esta configuração no modelo de implementação clássico, você precisaria criar um site local que tenha prefixos agregados representando tanto o vnet conectado como o espaço de endereço no local. Este local local de representação está então ligado ao VNet para alcançar a conectividade de trânsito. Este modelo clássico também tem desafios de gestão semelhantes, uma vez que qualquer alteração na gama de endereços no local também deve ser mantida no local representando o agregado de VNet e no local. A introdução do suporte bGP em gateways suportados pelo Gestor de Recursos simplifica a capacidade de gestão, uma vez que os gateways conectados podem aprender rotas a partir de instalações sem modificação manual para prefixos.

![Screenshot do cenário de encaminhamento de trânsito.](./media/vpn-gateway-migration/migration2.png)

Uma vez que transformamos a conectividade VNet em VNet sem exigir locais locais, o cenário de trânsito perde a conectividade no local para a VNet que está indiretamente ligada às instalações. A perda de conectividade pode ser atenuada nas duas seguintes formas, após a migração estar concluída - 

* Ative o BGP nos portões VPN que estão ligados entre si e no local. Permitir o BGP restaura a conectividade sem qualquer outra alteração de configuração, uma vez que as rotas são aprendidas e publicitadas entre gateways VNet. Note que a opção BGP só está disponível em SKUs Standard e mais elevado.
* Estabeleça uma ligação explícita da VNet afetada ao portal de rede local que representa a localização no local. Isto também exigiria a alteração da configuração no router no local para criar e configurar o túnel IPsec.

## <a name="next-steps"></a>Passos seguintes
Depois de aprender sobre o apoio à migração de gateways VPN, vá para a [migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos](../virtual-machines/windows/migration-classic-resource-manager-ps.md) para começar.

