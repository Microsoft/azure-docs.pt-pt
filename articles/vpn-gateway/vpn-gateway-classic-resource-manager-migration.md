---
title: VPN Gateway Classic para a Migração de Gestores de Recursos / Microsoft Docs
description: Esta página fornece uma visão geral da migração VPN Gateway Classic para Resource Manager.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: b9154b124f89a8b672c58d5e7bce28c0833e8a8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84988043"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN Gateway clássico para migração de Gestor de Recursos
Os Gateways VPN podem agora ser migrados do modelo de implementação clássico para o Gestor de Recursos. Pode ler mais sobre [funcionalidades e benefícios](../azure-resource-manager/management/overview.md)do Azure Resource Manager . Neste artigo, detalhamos como migrar de implementações clássicas para um modelo mais recente baseado em Gestor de Recursos. 

Os Gateways VPN são migrados como parte da migração da VNet do clássico para o Gestor de Recursos. Esta migração é feita um VNet de cada vez. Não existe qualquer exigência adicional em termos de ferramentas ou pré-requisitos para a migração. As etapas de migração são idênticas à migração vnet existente e estão documentadas na [página de migração de recursos iaaS.](../virtual-machines/windows/migration-classic-resource-manager-ps.md) Não existe uma trajetória de inatividade durante a migração e, por conseguinte, as cargas de trabalho existentes continuariam a funcionar sem perda de conectividade no local durante a migração. O endereço IP público associado ao gateway VPN não muda durante o processo de migração. Isto implica que não precisará de reconfigurar o seu router no local uma vez que a migração esteja concluída.  

O modelo em Resource Manager é diferente do modelo clássico e é composto por gateways de rede virtuais, gateways de rede locais e recursos de conexão. Estes representam a própria porta de entrada VPN, o local que representa no espaço de endereço das instalações e a conectividade entre os dois, respectivamente. Uma vez concluída a migração, os seus gateways não estariam disponíveis no modelo clássico e todas as operações de gestão em gateways de rede virtuais, gateways de rede locais e objetos de conexão devem ser realizados usando o modelo de Gestor de Recursos.

## <a name="supported-scenarios"></a>Cenários suportados
Os cenários de conectividade VPN mais comuns são cobertos pela migração clássica do Gestor de Recursos. Os cenários apoiados incluem -

* Ponto para a conectividade do site
* Conectividade do site para site com VPN Gateway conectado à localização do local
* Conectividade VNet para VNet entre dois VNets usando gateways VPN
* Vários VNets ligados ao mesmo no local
* Conectividade multi-site
* Túneis forçados habilitados VNets

Os cenários que não são apoiados incluem -  

* O VNet com o ExpressRoute Gateway e o VPN Gateway não está atualmente suportado.
* Cenários de trânsito onde as extensões VM estão ligadas a servidores no local. As limitações de conectividade VPN de trânsito são detalhadas abaixo.

> [!NOTE]
> A validação do CIDR no modelo Resource Manager é mais rigorosa do que a do modelo clássico. Antes de migrar, certifique-se de que as gamas de endereços clássicas dadas estão em conformidade com o formato CIDR válido antes de iniciar a migração. O CIDR pode ser validado utilizando quaisquer validadores CIDR comuns. VNet ou sites locais com gamas CIDR inválidas quando migrados resultariam em estado falhado.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migração da conectividade VNet para VNet
A conectividade VNet para VNet no clássico foi conseguida criando uma representação local do VNet conectado. Os clientes foram obrigados a criar dois locais que representassem os dois VNets que precisavam de ser conectados entre si. Estes foram então ligados aos VNets correspondentes utilizando o túnel IPsec para estabelecer conectividade entre os dois VNets. Este modelo tem desafios de gestão, uma vez que qualquer alteração da gama de endereços num VNet também deve ser mantido na representação local correspondente. No modelo Resource Manager esta solução já não é necessária. A ligação entre os dois VNets pode ser diretamente alcançada utilizando o tipo de ligação 'Vnet2Vnet' no recurso Connection. 

![Screenshot da migração VNet para VNet.](./media/vpn-gateway-migration/migration1.png)

Durante a migração do VNet, detetamos que a entidade ligada ao gateway VPN da VNet atual é outra VNet e asseguramos que uma vez concluída a migração de ambos os VNets, deixará de ver dois locais locais que representam o outro VNet. O modelo clássico de dois gateways VPN, dois locais locais e duas ligações entre eles é transformado em modelo de Gestor de Recursos com dois gateways VPN e duas ligações do tipo Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Conectividade VPN de trânsito
Pode configurar gateways VPN numa topologia de modo a que a conectividade no local para um VNet seja conseguida ligando-se a outro VNet que esteja diretamente ligado ao local. Trata-se de uma conectividade VPN de trânsito em que os casos no primeiro VNet estão ligados a recursos no local através do trânsito para o gateway VPN em VNet conectado que está diretamente ligado ao local. Para conseguir esta configuração no modelo de implementação clássico, você precisaria de criar um site local que tenha prefixos agregados que representam tanto o espaço de endereço de VNet conectado como no local. Este site local de representação é então ligado ao VNet para alcançar a conectividade de trânsito. Este modelo clássico também tem desafios de gestão semelhantes, uma vez que qualquer alteração na gama de endereços no local também deve ser mantida no site local que representa o agregado de VNet e no local. A introdução do suporte BGP no Resource Manager suporta gateways simplifica a gestão, uma vez que os gateways conectados podem aprender rotas a partir das instalações sem modificação manual aos prefixos.

![Screenshot do cenário de encaminhamento de trânsito.](./media/vpn-gateway-migration/migration2.png)

Uma vez que transformamos a conectividade VNet para VNet sem exigir sites locais, o cenário de trânsito perde a conectividade no local para o VNet que está indiretamente ligado às instalações. A perda de conectividade pode ser atenuada das duas maneiras seguintes, após a migração estar concluída - 

* Ativar o BGP em gateways VPN que estejam ligados entre si e no local. Permitir que o BGP restaure a conectividade sem qualquer outra alteração de configuração, uma vez que as rotas são aprendidas e publicitadas entre gateways VNet. Note que a opção BGP só está disponível na Standard e skus mais alta.
* Estabeleça uma ligação explícita do VNet afetado à porta de entrada de rede local que representa a localização no local. Isto exigiria igualmente a alteração da configuração no router no local para criar e configurar o túnel IPsec.

## <a name="next-steps"></a>Passos seguintes
Depois de aprender sobre o suporte de migração de gateway VPN, vá para a [migração apoiada pela plataforma de recursos IaaS de clássico para Gestor de Recursos](../virtual-machines/windows/migration-classic-resource-manager-ps.md) para começar.

