---
title: Extensão subnet em Azure Microsoft Docs
description: Saiba mais sobre a extensão da sub-rede em Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587514"
---
# <a name="subnet-extension"></a>Extensão de sub-rede
A migração da carga de trabalho para a nuvem pública requer um planeamento e coordenação cuidadosos. Uma das principais considerações pode ser a capacidade de reter os seus endereços IP. O que pode ser importante especialmente se as suas aplicações tiverem dependência de endereçoIP ou tiver requisitos de conformidade para usar endereços IP específicos. A Rede Virtual Azure resolve este problema para si, permitindo-lhe criar VNet e Subnets utilizando uma gama de endereços IP à sua escolha.

As migrações podem ser um pouco desafiantes quando o requisito acima referido é associado a um requisito adicional para manter algumas aplicações no local. Em situações como uma situação, terá de dividir as aplicações entre o Azure e o local, sem recontar os endereços IP de ambos os lados. Além disso, terá de permitir que as aplicações se comuniquem como se estivessem na mesma rede.

Uma solução para o problema acima é a extensão da sub-rede. O alargamento de uma rede permite que as aplicações falem sobre o mesmo domínio de transmissão quando existem em diferentes locais físicos, removendo a necessidade de rearchitectar a sua topologia de rede. 

Embora o alargamento da sua rede não seja uma boa prática em geral, os casos abaixo do uso podem torná-lo necessário.

- **Migração faseada**: O cenário mais comum é que quer fasear a sua migração. Você quer trazer algumas aplicações primeiro e com o tempo migrar o resto das aplicações para Azure.
- **Latência**: Os baixos requisitos de latência podem ser outra razão para manter algumas aplicações no local para garantir que estão o mais próximas possível do seu datacenter.
- **Conformidade**: Outro caso de utilização é que pode ter requisitos de conformidade para manter algumas das suas aplicações no local.
 
> [!NOTE] 
> Não deve estender as suas subredes a menos que seja necessário. Nos casos em que se prolongam as suas subredes, devem tentar torná-lo um passo intermédio. Com o tempo, deve tentar re-numero aplicações na sua rede no local e emigrar para Azure.

Na próxima secção, discutiremos como pode estender as suas subredes ao Azure.


## <a name="extend-your-subnet-to-azure"></a>Estenda a sua subneta a Azure
 Pode estender as suas subredes no local ao Azure utilizando uma solução baseada em rede de sobreposição de camada 3. A maioria das soluções utiliza uma tecnologia de sobreposição como a VXLAN para estender a rede camada 2 utilizando uma rede de sobreposição camada 3. O diagrama abaixo mostra uma solução generalizada. Nesta solução, existe a mesma sub-rede em ambos os lados, azure e no local. 

![Exemplo de extensão de sub-rede](./media/subnet-extension/subnet-extension.png)

Os endereços IP da subnet são atribuídos a VMs em Azure e no local. Tanto o Azure como o local têm uma NVA inserida nas suas redes. Quando um VM em Azure tenta falar com um VM na rede no local, o Azure NVA captura o pacote, encapsula-o e envia-o através da VPN/Express Route para a rede no local. A NVA no local recebe o pacote, decapsula-o e encaminha-o para o destinatário pretendido na sua rede. O tráfego de retorno usa um caminho e lógica semelhantes.

No exemplo acima, o Azure NVA e o NVA no local comunicam e aprendem sobre endereços IP atrás uns dos outros. Redes mais complexas também podem ter um serviço de mapeamento, que mantém o mapeamento entre os NVAs e os endereços IP por trás deles. Quando uma NVA recebe um pacote, consulta o serviço de mapeamento para descobrir o endereço da NVA que tem o endereço IP de destino por trás.

Na próxima secção, encontrará detalhes sobre as soluções de extensão da sub-rede que testámos no Azure.

## <a name="next-steps"></a>Passos seguintes 
[Estenda a sua subneta ao Azure utilizando soluções de fornecedor.](https://github.com/microsoft/Azure-LISP)