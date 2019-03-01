---
title: Prefixo de endereço IP público do Azure | Documentos da Microsoft
description: Saiba mais sobre o que um Azure público prefixo de endereço IP é e como ele pode ajudar que atribuir os endereços IP públicos previsíveis aos seus recursos.
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
ms.openlocfilehash: 5496be93a5241621cd4dc5e873e4386f8ed6c992
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195222"
---
# <a name="public-ip-address-prefix"></a>Prefixo do endereço IP público

Um prefixo de endereço IP público é um intervalo reservado de endereços IP para os pontos finais públicos no Azure. Azure aloca um intervalo contíguo de endereços para a sua subscrição com base no número que especificar. Se não estiver familiarizado com endereços públicos, consulte o artigo [endereços IP públicos.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Endereços IP públicos são atribuídos a partir de um conjunto de endereços em cada região do Azure. Pode [transferir](https://www.microsoft.com/download/details.aspx?id=56519) a lista de intervalos que utiliza o Azure para cada região. Por exemplo, 40.121.0.0/16 é um dos mais de 100 intervalos que utiliza o Azure na região E.U.A. Leste. O intervalo inclui os endereços utilizáveis dos 40.121.0.1 - 40.121.255.254.

Crie um prefixo de endereço IP público numa região do Azure e subscrição ao especificar um nome e quantos endereços que pretende que o prefixo para incluir. Por exemplo, se criar um prefixo de endereço IP público de/28, o Azure aloca 16 endereços de um dos seus intervalos para. Não sabe o intervalo Azure serão atribuídas depois de criar o intervalo, mas os endereços sejam contíguos. Prefixos de endereços IP públicos têm uma taxa. Para obter detalhes, consulte [preços de endereços IP públicos](https://azure.microsoft.com/pricing/details/ip-addresses).

> [!IMPORTANT]
> Prefixo de IP público é em pré-visualização pública em regiões limitadas. Pode [saber o que significa no modo de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Prefixo do IP público está atualmente disponível em: EUA Centro-Oeste, E.U.A. oeste, E.U.A. oeste 2, E.U.A. Central, Europa do Norte, Europa Ocidental e Sudeste asiático. Para obter uma lista atualizada de regiões, visite [atualizações do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="why-create-a-public-ip-address-prefix"></a>Porquê criar um prefixo de endereço IP público?

Ao criar recursos de endereço IP público, Azure atribuir um endereço IP público disponível a partir de qualquer um dos intervalos utilizados numa região. Assim que o Azure atribui o endereço, sabe o que é o endereço, mas até que o Azure atribui o endereço, não sabe quais endereço pode ser atribuído. Isso pode ser problemático quando, por exemplo, ou seus parceiros comerciais, configurar regras de firewall que permitem que os endereços IP específicos. Sempre que atribuir um novo endereço IP público a um recurso, o endereço tem de ser adicionado para a regra de firewall. Quando atribui endereços aos seus recursos de um prefixo de endereço IP público, regras de firewall não precisam de ser atualizados sempre que atribuir um dos endereços, porque o intervalo inteiro poderia ser adicionado a uma regra.

## <a name="benefits"></a>Benefícios

- Pode criar recursos de endereço IP público a partir de um intervalo conhecido.
- Ou seus parceiros comerciais podem criar regras de firewall com intervalos que incluem os endereços IP públicos que atribuiu atualmente, bem como os endereços que ainda não atribuiu ainda. Isso elimina a necessidade de alterar as regras de firewall, como atribuir endereços IP para novos recursos.
- O tamanho predefinido de um intervalo que pode criar é/28 ou 16 endereços IP.
- Existem não existem limites sobre quantos intervalos, pode criar, no entanto, existem limites para o número máximo de endereços IP públicos estáticos que pode ter uma subscrição do Azure. Como resultado, o número de intervalos que cria não possa abranger mais endereços IP públicos estáticos que pode ter na sua subscrição. Para obter mais informações, consulte [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Os endereços que crie com endereços do prefixo podem ser atribuídos a qualquer recurso do Azure que pode atribuir um endereço IP público para.
- Pode ver facilmente que os endereços IP que são alocados e ainda não alocados no intervalo.

## <a name="scenarios"></a>Cenários
Pode associar os seguintes recursos para um endereço IP público estático a partir de um prefixo:

|Recurso|Cenário|Passos|
|---|---|---|
|Virtual Machines| Associar IPs públicos de um prefixo para as máquinas virtuais no Azure reduz a sobrecarga de gerenciamento que diz respeito à lista de permissões IPs numa firewall. Pode simplesmente lista branca um prefixo de inteiro com uma única regra de firewall. À medida que aumenta com máquinas virtuais no Azure, pode associar IPs do mesmo prefixo, economizando custo, tempo e custos de gestão.| Para associar IPs de um prefixo para a máquina virtual: 1. [Crie um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. [Associe o IP para a interface de rede da sua máquina virtual.](virtual-network-network-interface-addresses.md#add-ip-addresses)
| Balanceador de Carga | Associar IPs públicos de um prefixo para o seu IP de front-end configuração ou a regra de saída de um balanceador de carga garante simplificação do seu espaço de endereço IP público do Azure. Pode simplificar o seu cenário pelo tratamento de ligações de saída para ser originado de um intervalo de endereços IP contíguos definido pelo prefixo IP público. | Para associar IPs de um prefixo para o Balanceador de carga: 1. [Crie um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. Ao criar o Balanceador de carga, selecione ou atualizar o IP criado no passo 2 acima, como o IP de front-end do seu Balanceador de carga. |
| Azure Firewall | Pode utilizar um IP público de um prefixo de SNAT de saída. Isso significa que todo tráfego de rede virtual saída é traduzido para o [Firewall do Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) IP público. Uma vez que este IP é proveniente de um prefixo predeterminado, é muito fácil de saber antecipadamente aparência sua pegada de IP pública no Azure. | 1. [Crie um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. Quando [implantar o firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), certifique-se de que selecione IP alocados anteriormente o prefixo.|

## <a name="constraints"></a>Restrições

- Não é possível especificar os endereços IP para o prefixo. Azure atribui os endereços IP para o prefixo, com base no tamanho que especificar.
- Não é possível alterar o intervalo de, depois de criar o prefixo.
- O intervalo é de apenas endereços IPv4. O intervalo não contém endereços IPv6.
- Apenas endereços IP públicos estáticos criados com o SKU Standard podem ser atribuídos a partir do intervalo do prefixo. Para saber mais sobre o IP público de endereços SKUs, veja [endereço IP público](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Endereços do intervalo só podem ser atribuídos aos recursos do Azure Resource Manager. Não não possível atribuir endereços a recursos no modelo de implementação clássica.
- Todos os endereços IP públicos criados a partir do prefixo tem de existir na mesma região do Azure e subscrição como o prefixo e devem ser atribuídos a recursos na mesma região e subscrição.
- Não é possível eliminar um prefixo se quaisquer endereços dentro da mesma são atribuídos a recursos de endereço IP público associados a um recurso. Desassociar a todos os recursos de endereço IP público que são atribuídos endereços IP a partir do prefixo pela primeira vez.


## <a name="next-steps"></a>Passos Seguintes

- [Criar](manage-public-ip-address-prefix.md) um prefixo de endereço IP público
