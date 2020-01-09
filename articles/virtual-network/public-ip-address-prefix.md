---
title: Prefixo de endereço IP público do Azure | Microsoft Docs
description: Saiba mais sobre o que é um prefixo de endereço IP público do Azure e como ele pode ajudá-lo a atribuir endereços IP públicos previsíveis para seus recursos.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 12fb7e03062600745cd8511d37b439ce44f2ef78
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640725"
---
# <a name="public-ip-address-prefix"></a>Prefixo de endereço IP público

Um prefixo de endereço IP público é um intervalo reservado de endereços IP para seus pontos de extremidade públicos no Azure. O Azure aloca um intervalo contíguo de endereços para sua assinatura com base na quantidade especificada. Se você não estiver familiarizado com os endereços públicos, consulte [endereços IP públicos.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Os endereços IP públicos são atribuídos de um pool de endereços em cada região do Azure. Você pode [baixar](https://www.microsoft.com/download/details.aspx?id=56519) a lista de intervalos que o Azure usa para cada região. Por exemplo, 40.121.0.0/16 é um dos mais de 100 intervalos que o Azure usa na região leste dos EUA. O intervalo inclui os endereços utilizáveis de 40.121.0.1-40.121.255.254.

Você cria um prefixo de endereço IP público em uma região e uma assinatura do Azure especificando um nome e quantos endereços você deseja incluir no prefixo. Por exemplo, se você criar um prefixo de endereço IP público de/28, o Azure alocará 16 endereços de um de seus intervalos para você. Você não sabe qual intervalo o Azure atribuirá até que você crie o intervalo, mas os endereços são contíguos. Os prefixos de endereço IP público têm uma taxa. Para obter detalhes, consulte [preços de endereço IP público](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Por que criar um prefixo de endereço IP público?

Quando você cria recursos de endereço IP público, o Azure atribui um endereço IP público disponível de qualquer um dos intervalos usados na região. Depois que o Azure atribui o endereço, você sabe qual é o endereço, mas até que o Azure atribua o endereço, você não sabe qual endereço pode ser atribuído. Isso pode ser problemático quando, por exemplo, você ou seus parceiros de negócios, configuram regras de firewall que permitem endereços IP específicos. Cada vez que você atribui um novo endereço IP público a um recurso, o endereço deve ser adicionado à regra de firewall. Quando você atribui endereços a seus recursos de um prefixo de endereço IP público, as regras de firewall não precisam ser atualizadas toda vez que você atribuir um dos endereços, pois todo o intervalo pode ser adicionado a uma regra.

## <a name="benefits"></a>Vantagens

- Você pode criar recursos de endereço IP público a partir de um intervalo conhecido.
- Você, ou seus parceiros de negócios, podem criar regras de firewall com intervalos que incluem endereços IP públicos que você atribuiu atualmente, bem como endereços que ainda não foram atribuídos. Isso elimina a necessidade de alterar as regras de firewall à medida que você atribui endereços IP a novos recursos.
- O tamanho padrão de um intervalo que você pode criar é/28 ou 16 endereços IP.
- No entanto, não há limites quanto a quantos intervalos você pode criar. no entanto, há limites no número máximo de endereços IP públicos estáticos que você pode ter em uma assinatura do Azure. Como resultado, o número de intervalos que você cria não pode abranger endereços IP públicos mais estáticos do que você pode ter em sua assinatura. Para obter mais informações, consulte [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Os endereços que você cria usando endereços do prefixo podem ser atribuídos a qualquer recurso do Azure ao qual você possa atribuir um endereço IP público.
- Você pode ver facilmente quais endereços IP alocados e ainda não foram alocados dentro do intervalo.

## <a name="scenarios"></a>Cenários
Você pode associar os seguintes recursos a um endereço IP público estático de um prefixo:

|Recurso|Cenário|Passos|
|---|---|---|
|Virtual Machines| A associação de IPs públicos de um prefixo às suas máquinas virtuais no Azure reduz a sobrecarga de gerenciamento quando se trata de colocar os IPs na lista de permissões em um firewall. Você pode simplesmente colocar um prefixo inteiro na lista de permissões com uma única regra de firewall. Ao dimensionar com máquinas virtuais no Azure, você pode associar IPs do mesmo prefixo economizando custo, tempo e sobrecarga de gerenciamento.| Para associar IPs de um prefixo à sua máquina virtual: 1. [Crie um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. [Associe o IP à interface de rede da sua máquina virtual.](virtual-network-network-interface-addresses.md#add-ip-addresses) Você também pode [associar os IPs a um conjunto de dimensionamento de máquinas virtuais](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Balanceadores de carga padrão | Associar IPs públicos de um prefixo à sua configuração de IP de front-end ou regra de saída de um Load Balancer garante a simplificação do seu espaço de endereço IP público do Azure. Você pode simplificar seu cenário ao fazer com que as conexões de saída de grooming sejam originadas de um intervalo de endereços IP contíguos definidos pelo prefixo IP público. | Para associar IPs de um prefixo ao balanceador de carga: 1. [Crie um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. Ao criar o Load Balancer, selecione ou atualize o IP criado na etapa 2 acima como o IP de front-end do seu Load Balancer. |
| Azure Firewall | Você pode usar um IP público de um prefixo para SNAT de saída. Isso significa que todo o tráfego de rede virtual de saída é convertido para o IP público do [Firewall do Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Como esse IP vem de um prefixo predeterminado, é muito fácil saber antecipadamente qual será a aparência do seu IP público no Azure. | 1. [crie um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. Ao [implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), certifique-se de selecionar o IP que você alocou anteriormente do prefixo.|
| Gateway de aplicativo v2 | Você pode usar um IP público de um prefixo para o seu dimensionamento automático e o gateway de aplicativo com redundância de zona v2. Como esse IP vem de um prefixo predeterminado, é muito fácil saber antecipadamente qual será a aparência do seu IP público no Azure. | 1. [crie um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. Ao [implantar o gateway de aplicativo](../application-gateway/quick-create-portal.md#create-an-application-gateway), certifique-se de selecionar o IP que você alocou anteriormente do prefixo.|

## <a name="constraints"></a>Restrições

- Você não pode especificar os endereços IP para o prefixo. O Azure aloca os endereços IP para o prefixo, com base no tamanho que você especificar.
- Você pode criar um prefixo de até 16 endereços IP ou um/28. Para obter mais informações, consulte [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Você não pode alterar o intervalo, depois de criar o prefixo.
- Somente endereços IP públicos estáticos criados com o SKU Standard podem ser atribuídos a partir do intervalo do prefixo. Para saber mais sobre as SKUs de endereço IP público, consulte [endereço IP público](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Os endereços do intervalo só podem ser atribuídos a Azure Resource Manager recursos. Os endereços não podem ser atribuídos a recursos no modelo de implantação clássico.
- Todos os endereços IP públicos criados a partir do prefixo devem existir na mesma região e assinatura do Azure que o prefixo e devem ser atribuídos aos recursos na mesma região e assinatura.
- Você não poderá excluir um prefixo se algum endereço dentro dele for atribuído a recursos de endereço IP público associados a um recurso. Dissociar todos os recursos de endereço IP público que recebem endereços IP do prefixo primeiro.


## <a name="next-steps"></a>Passos seguintes

- [Criar](manage-public-ip-address-prefix.md) um prefixo de endereço IP público
