---
title: Prefixo do endereço IP público Azure
description: Saiba o que é um prefixo de endereço IP público Azure e como pode ajudá-lo a atribuir endereços IP públicos previsíveis aos seus recursos.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: allensu
ms.openlocfilehash: 0f71f845ef3209146ead79cafae2f3aa5c8c6d7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82144512"
---
# <a name="public-ip-address-prefix"></a>Prefixo de endereço IP público

Um prefixo de endereço IP público é uma gama reservada de endereços IP para os seus pontos finais públicos em Azure. O Azure atribui uma gama contígua de endereços à sua subscrição com base no número de especificações. Se não estiver familiarizado com endereços públicos, consulte [os endereços IP públicos.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Os endereços IP públicos são atribuídos a partir de um conjunto de endereços em cada região de Azure. Você pode [baixar](https://www.microsoft.com/download/details.aspx?id=56519) a lista de gamas que Azure usa para cada região. Por exemplo, 40.121.0.0/16 é uma das mais de 100 gamas que o Azure utiliza na região leste dos EUA. A gama inclui os endereços utilizáveis de 40.121.0.1 - 40.121.255.254.

Cria um prefixo de endereço IP público numa região de Azure e subscrição especificando um nome e quantos endereços pretende que o prefixo inclua. Por exemplo, Se criar um prefixo de endereço IP público de /28, o Azure atribui 16 endereços de uma das suas gamas para si. Não sabe qual o alcance que o Azure irá atribuir até criar o alcance, mas os endereços são contíguos. Os prefixos de endereço IP públicos têm uma taxa. Para mais informações, consulte [os preços do endereço IP público.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="why-create-a-public-ip-address-prefix"></a>Porquê criar um prefixo de endereço IP público?

Ao criar recursos de endereço IP públicos, o Azure atribui um endereço IP público disponível a partir de qualquer uma das gamas utilizadas na região. Assim que o Azure atribuir o endereço, sabe qual é o endereço, mas até o Azure atribuir o endereço, não sabe qual o endereço que poderá ser atribuído. Isto pode ser problemático quando, por exemplo, você, ou os seus parceiros de negócio, configurar regras de firewall que permitem endereços IP específicos. Sempre que atribuir um novo endereço IP público a um recurso, o endereço tem de ser adicionado à regra da firewall. Quando atribui endereços aos seus recursos a partir de um prefixo de endereço IP público, as regras de firewall não precisam de ser atualizadas cada vez que atribui um dos endereços, porque toda a gama pode ser adicionada a uma regra.

## <a name="benefits"></a>Benefícios

- Pode criar recursos de endereço IP públicos a partir de um alcance conhecido.
- Você, ou os seus parceiros de negócios podem criar regras de firewall com intervalos que incluem endereços IP públicos que você atribuiu atualmente, bem como endereços que ainda não atribuiu. Isto elimina a necessidade de alterar as regras de firewall à medida que atribui endereços IP a novos recursos.
- O tamanho predefinido de uma gama que pode criar é de endereços IP /28 ou 16.
- Não existem limites para quantas gamas pode criar, no entanto, existem limites para o número máximo de endereços IP públicos estáticos que pode ter numa subscrição do Azure. Como resultado, o número de gamas que cria não pode abranger endereços IP públicos mais estáticos do que pode ter na sua subscrição. Para mais informações, consulte [os limites do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)
- Os endereços que cria utilizando endereços a partir do prefixo podem ser atribuídos a qualquer recurso Azure a que possa atribuir um endereço IP público.
- Pode facilmente ver quais endereços IP que são atribuídos e ainda não atribuídos dentro do intervalo.

## <a name="scenarios"></a>Cenários
Pode associar os seguintes recursos a um endereço IP público estático a partir de um prefixo:

|Recurso|Cenário|Passos|
|---|---|---|
|Máquinas Virtuais| Associar os IPs públicos de um prefixo às suas máquinas virtuais em Azure reduz a sobrecarga de gestão quando se trata de iPs de lista branca numa firewall. Pode simplesmente branquear um prefixo inteiro com uma única regra de firewall. À medida que escala com máquinas virtuais em Azure, pode associar os IPs do mesmo custo de poupança, tempo e sobrecarga de gestão.| Para associar os IPs de um prefixo à sua máquina virtual: 1. [Criar um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. [Associe o IP à interface de rede da sua máquina virtual.](virtual-network-network-interface-addresses.md#add-ip-addresses) Também pode [associar os IPs a um Conjunto de Escala de Máquina Virtual.](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)
| Balanceadores de carga padrão | Associar os IPs públicos de um prefixo à configuração IP frontal ou regra de saída de um Balancer de Carga garante a simplificação do seu espaço de endereço IP público Azure. Pode simplificar o seu cenário através do aliciamento de ligações de saída a originar de uma série de endereços IP contíguos definidos pelo prefixo IP público. | Para associar os IPs de um prefixo ao seu equilibrador de carga: 1. [Criar um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. Ao criar o Balanceador de Carga, selecione ou atualize o IP criado no passo 2 acima como o IP frontal do seu Balanceador de Carga. |
| Azure Firewall | Você pode usar um IP público a partir de um prefixo para SNAT de saída. Isto significa que todo o tráfego de rede virtual de saída é traduzido para o IP público [Azure Firewall.](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Uma vez que este IP provém de um prefixo pré-determinado, é muito fácil saber com antecedência como será a sua pegada ip pública em Azure. | 1. [Criar um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. Quando [colocar a firewall Azure,](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)certifique-se de selecionar o IP que alocado anteriormente a partir do prefixo.|
| Gateway de aplicação v2 | Pode utilizar um IP público a partir de um prefixo para o seu porta de entrada de aplicação de auto-caling e redundante de zona v2. Uma vez que este IP provém de um prefixo pré-determinado, é muito fácil saber com antecedência como será a sua pegada ip pública em Azure. | 1. [Criar um prefixo.](manage-public-ip-address-prefix.md) 2. [Crie um IP a partir do prefixo.](manage-public-ip-address-prefix.md) 3. Quando [implementar o Gateway de Aplicações,](../application-gateway/quick-create-portal.md#create-an-application-gateway)certifique-se de selecionar o IP que já alocado a partir do prefixo.|

## <a name="constraints"></a>Restrições

- Não é possível especificar os endereços IP para o prefixo. O Azure atribui os endereços IP para o prefixo, com base no tamanho especificado.
- Pode criar um prefixo de até 16 endereços IP ou um /28 por predefinição. Os [limites da rede](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) de revisão aumentam os pedidos e os [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para mais informações.
- Não se pode mudar o alcance, depois de criar o prefixo.
- Apenas endereços IP públicos estáticos criados com o SKU Standard podem ser atribuídos a partir do intervalo do prefixo. Para saber mais sobre o endereço IP público SKUs, consulte [o endereço IP público.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- Os endereços da gama só podem ser atribuídos aos recursos do Gestor de Recursos Azure. Os endereços não podem ser atribuídos a recursos no modelo clássico de implantação.
- Todos os endereços IP públicos criados a partir do prefixo devem existir na mesma região de Azure e subscrição como o prefixo, e devem ser atribuídos a recursos na mesma região e subscrição.
- Não é possível eliminar um prefixo se algum endereço nele for atribuído a recursos de endereço IP públicos associados a um recurso. Dissociem todos os recursos de endereço IP públicos que são atribuídos endereços IP a partir do prefixo primeiro.


## <a name="next-steps"></a>Passos seguintes

- [Criar](manage-public-ip-address-prefix.md) um prefixo de endereço IP público
