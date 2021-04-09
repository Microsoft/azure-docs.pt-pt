---
title: Extensão do sub-rede em Azure | Microsoft Docs
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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: 555c903dc3375080a8b0d006d2aac7216854c939
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539303"
---
# <a name="subnet-extension"></a>Extensão de sub-rede
A migração da carga de trabalho para a nuvem pública requer um planeamento e coordenação cuidadosos. Uma das principais considerações pode ser a capacidade de reter os seus endereços IP. O que pode ser importante especialmente se as suas aplicações tiverem dependência de endereços IP ou se tiver requisitos de conformidade para usar endereços IP específicos. A Rede Virtual Azure resolve este problema por si, permitindo-lhe criar VNet e Subnets utilizando um intervalo de endereços IP à sua escolha.

As migrações podem ser um pouco desafiantes quando o requisito acima referido é associado a um requisito adicional para manter algumas aplicações no local. Em casos como o caso, terá de dividir as aplicações entre o Azure e o local, sem renumerar os endereços IP de ambos os lados. Além disso, terá de permitir que as aplicações se comuniquem como se estivessem na mesma rede.

Uma solução para o problema acima referido é a extensão da sub-rede. O alargamento de uma rede permite que as aplicações falem sobre o mesmo domínio de transmissão quando existem em diferentes locais físicos, removendo a necessidade de rearquitectar a topologia da sua rede. 

Embora o alargamento da sua rede não seja uma boa prática em geral, abaixo os casos de utilização podem torná-la necessária.

- **Migração faseada**: O cenário mais comum é que se queira fasear a sua migração. Você quer trazer algumas aplicações primeiro e com o tempo migrar o resto das aplicações para Azure.
- **Latência**: Os baixos requisitos de latência podem ser outra razão para manter algumas aplicações no local para garantir que estão o mais perto possível do seu datacenter.
- **Conformidade**: Outro caso de utilização é que pode ter requisitos de conformidade para manter algumas das suas aplicações no local.
 
> [!NOTE] 
> Não deve estender as suas sub-redes a menos que seja necessário. Nos casos em que estendia as suas sub-redes, deve tentar torná-lo um passo intermédio. Com o tempo, deve tentar re-numerar aplicações na sua rede de instalações e emigrá-las para Azure.

Na próxima secção, discutiremos como pode estender as suas sub-redes ao Azure.


## <a name="extend-your-subnet-to-azure"></a>Estenda a sua sub-rede a Azure
 Pode estender as suas sub-redes no local ao Azure utilizando uma solução baseada em rede de sobreposição de camadas 3. A maioria das soluções usa uma tecnologia de sobreposição, como a VXLAN, para estender a rede camada-2 utilizando uma rede de sobreposição de camadas 3. O diagrama abaixo mostra uma solução generalizada. Nesta solução, existe a mesma sub-rede em ambos os lados, a Azure e no local. 

![Exemplo de extensão do sub-rede](./media/subnet-extension/subnet-extension.png)

Os endereços IP da sub-rede são atribuídos a VMs em Azure e no local. Tanto o Azure como o local têm uma NVA inserida nas suas redes. Quando um VM em Azure tenta falar com um VM na rede de instalações, o Azure NVA captura o pacote, encapsula-o e envia-o sobre a VPN/Rota Expressa para a rede de instalações. O NVA no local recebe o pacote, descapsula-o e encaminha-o para o destinatário pretendido na sua rede. O tráfego de retorno usa um caminho e lógica semelhantes.

No exemplo acima, o Azure NVA e as NVA no local comunicam e aprendem sobre endereços IP atrás uns dos outros. Redes mais complexas também podem ter um serviço de mapeamento, que mantém o mapeamento entre os NVAs e os endereços IP por trás deles. Quando um NVA recebe um pacote, consulta o serviço de mapeamento para saber o endereço do NVA que tem o endereço IP de destino por trás dele.

Na próxima secção, encontrará detalhes sobre as soluções de extensão da sub-rede que testámos no Azure.

## <a name="next-steps"></a>Passos seguintes 
[Estenda a sua sub-rede ao Azure utilizando soluções de fornecedor.](https://github.com/microsoft/Azure-LISP)