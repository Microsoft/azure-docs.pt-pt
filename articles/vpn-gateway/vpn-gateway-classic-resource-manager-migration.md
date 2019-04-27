---
title: Gateway VPN clássico para migração do Resource Manager | Documentos da Microsoft
description: Esta página fornece uma visão geral do clássico de Gateway de VPN para migração do Gestor de recursos.
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
ms.openlocfilehash: b65b47389611bcc0e5acb3c7ebff672f72a87581
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761589"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Gateway de VPN clássico para migração do Gestor de recursos
Gateways de VPN agora podem ser migrados do clássico para o modelo de implementação do Resource Manager. Pode ler mais sobre o Azure Resource Manager [recursos e benefícios](../azure-resource-manager/resource-group-overview.md). Neste artigo, vamos detalhar como migrar de implementações clássicas para o modelo do Resource Manager com base mais recente. 

Gateways de VPN são migrados como parte da migração de VNet do clássico para Resource Manager. Esta migração é feita uma VNet ao mesmo tempo. Não existe nenhum requisito adicional em termos de ferramentas ou de pré-requisitos para migração. Passos de migração são idênticos para a migração de VNet existente e estão documentados em [página de migração de recursos de IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Não há nenhum tempo de inatividade do caminho de dados durante a migração e, portanto, cargas de trabalho existentes continuarão a funcionar sem perda de conectividade no local durante a migração. O endereço IP público associado ao gateway VPN não é alterada durante o processo de migração. Isso implica que não terá de reconfigurar o router no local, depois de concluída a migração.  

O modelo no Resource Manager é diferente do modelo clássico e é composto por gateways de rede virtual, gateways de rede local e recursos de ligação. Representam o gateway de VPN em si, o site de local que representa no espaço de endereços no local e a conectividade entre os dois, respetivamente. Depois de concluída a migração, os gateways não estarão disponíveis no modelo clássico e todas as operações de gestão em gateways de rede virtual, os gateways de rede local e objetos de conexão devem ser efetuadas utilizando o modelo do Resource Manager.

## <a name="supported-scenarios"></a>Cenários suportados
Cenários mais comuns de conectividade VPN estão abrangidos por clássico para migração do Resource Manager. Os cenários suportados incluem-

* Aponte para conectividade de site
* Site para conectividade de site com o Gateway de VPN ligado na localização no local
* Conectividade VNet a VNet entre duas VNets com gateways de VPN
* Várias VNets ligadas mesma numa localização no local
* Conectividade de múltiplos site
* O túnel forçado ativado VNets

Os cenários que não são suportados incluem-  

* VNet com o Gateway do ExpressRoute e Gateway de VPN não é atualmente suportada.
* Cenários de trânsito em que as extensões de VM estão ligadas a servidores no local. Limitações de conectividade VPN de trânsito são detalhadas abaixo.

> [!NOTE]
> Validação de CIDR no modelo do Resource Manager é mais rígida que um no modelo clássico. Antes de migrar Certifique-se de que os intervalos de endereços clássico fornecidos está em conformidade com formato CIDR válido antes de iniciar a migração. CIDR pode ser validado com qualquer validadores CIDR comuns. VNet ou sites locais com intervalos CIDR inválidos quando migrados iria resultar num Estado com falhas.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>VNet para VNet a migração de conectividade
Conectividade VNet a VNet clássica foi obtido através da criação de uma representação de local site da VNet ligada. Os clientes eram necessário criar dois sites locais que representada as duas VNets que precisava ser ligadas em conjunto. Estes, em seguida, foram ligadas às VNets correspondentes a utilizar o túnel IPsec para estabelecer a conectividade entre as duas VNets. Esse modelo tem desafios de capacidade de gestão, uma vez que as alterações de intervalo de endereços numa VNet também devem ser mantidas na representação de local site correspondente. No modelo do Resource Manager esta solução não é mais necessária. A ligação entre as duas VNets pode ser obtida de diretamente com o tipo de ligação de "Vnet2Vnet" no recurso de ligação. 

![Captura de ecrã de VNet para VNet migração.](./media/vpn-gateway-migration/migration1.png)

Durante a migração de VNet detetamos que a entidade ligada ao gateway de VPN da VNet atual é outra VNet e certifique-se de que depois de concluída a migração de ambas as VNets, já não verá dois sites locais que representa a outra VNet. O modelo clássico de dois gateways de VPN, os dois sites locais e duas ligações entre eles é transformado para o modelo do Resource Manager com dois gateways de VPN e duas ligações do tipo Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Conectividade VPN de trânsito
Pode configurar gateways de VPN numa topologia de forma que a conectividade no local para uma VNet é obtida ao ligar a outra VNet que estejam ligado diretamente para o local. Isso é trânsito conectividade VPN em que as instâncias na primeira VNet estão ligadas a recursos no local através de trânsito para o gateway VPN de VNet ligada que estejam ligado diretamente para o local. Para obter esta configuração no modelo de implementação clássica, terá de criar um site local que tenha agregados prefixos que representa a VNet ligada e espaço de endereços no local. Este site local representational, em seguida, está ligado à VNet para alcançar a conectividade de trânsito. Este modelo clássico também tem desafios de capacidade de gerenciamento semelhantes, uma vez que qualquer alteração no intervalo de endereços no local também deve ser mantida no site local que representa o agregado da VNet e no local. Introdução do suporte BGP nos gateways de Gestor de recursos suportados simplifica a capacidade de gestão, uma vez que os gateways ligados podem saber as rotas no local, sem modificações manuais nos prefixos.

![Captura de ecrã do cenário de encaminhamento de trânsito.](./media/vpn-gateway-migration/migration2.png)

Uma vez que transformemos VNet para VNet conectividade sem a necessidade de sites locais, o cenário de trânsito perde conectividade no local para VNet que está ligada indiretamente a no local. A perda de conectividade pode ser atenuada das seguintes duas formas, após a migração estar concluída- 

* Ative o BGP em gateways de VPN que estão ligados em conjunto e no local. A ativar o BGP restaura a conectividade sem qualquer alteração de configuração, pois as rotas são adquiridas e anunciadas entre os gateways de VNet. Tenha em atenção que a opção de BGP só está disponível em SKUs Standard e superiores.
* Estabelece uma ligação explícita da VNet afetado para o gateway de rede local que representa a localização no local. Isso também exigiria que a alteração de configuração no router no local para criar e configurar o túnel IPsec.

## <a name="next-steps"></a>Passos Seguintes
Após saber mais sobre o suporte de migração do gateway VPN, aceda a [migração suportada por plataforma de recursos de IaaS da implementação clássica para Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-ps.md) para começar a utilizar.

