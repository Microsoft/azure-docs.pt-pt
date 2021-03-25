---
title: Prefixo do endereço IP público Azure
description: Saiba o que é um prefixo de endereço IP público Azure e como pode ajudá-lo a atribuir endereços IP públicos previsíveis aos seus recursos.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 8bfc76afc1ef799be0fb29654bd341f53d1b0a8c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048464"
---
# <a name="public-ip-address-prefix"></a>Prefixo de endereço IP público

Um prefixo de endereço IP público é uma gama reservada de endereços IP em Azure. O Azure dá uma gama contígua de endereços à sua subscrição com base em quantos especifica. 

Se não estiver familiarizado com endereços públicos, consulte [os endereços IP públicos.](./public-ip-addresses.md#public-ip-addresses)

Os endereços IP públicos são atribuídos a partir de um conjunto de endereços em cada região de Azure. Você pode [baixar](https://www.microsoft.com/download/details.aspx?id=56519) a lista de gamas que Azure usa para cada região. Por exemplo, 40.121.0.0/16 é uma das mais de 100 gamas que o Azure utiliza na região leste dos EUA. A gama inclui os endereços utilizáveis de 40.121.0.1 - 40.121.255.254.

Cria um prefixo de endereço IP público numa região de Azure e subscrição especificando um nome e quantos endereços pretende que o prefixo inclua. 

Os intervalos de endereços IP públicos são atribuídos com um prefixo à sua escolha. Se criar um prefixo de /28, o Azure dá 16 endereços ip de uma das suas gamas.

Não sabe qual o alcance que o Azure irá atribuir até criar o alcance, mas os endereços são contíguos. 

Os prefixos de endereço IP públicos têm uma taxa, para mais informações, consulte [o preço do endereço IP público.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="why-create-a-public-ip-address-prefix"></a>Porquê criar um prefixo de endereço IP público?

Ao criar recursos de endereço IP públicos, o Azure atribui um endereço IP público disponível a partir de qualquer uma das gamas utilizadas nessa região. 

Até que o Azure atribua o endereço IP, não saberá o IP exato. Este processo pode ser problemático quando cria regras de firewall que permitem endereços IP específicos. Para cada endereço IP adicionado, deve ser adicionada uma regra de firewall correspondente.

Quando atribui endereços aos seus recursos a partir de um prefixo de endereço IP público, não são necessárias atualizações de regras de firewall. Toda a gama é adicionada à regra.

## <a name="benefits"></a>Benefícios

- Criação de recursos de endereço IP públicos a partir de um alcance conhecido.
- Configuração de regras de firewall com intervalos que incluem endereços IP públicos que atribuiu atualmente e endereços que ainda não atribuiu. Esta configuração elimina a necessidade de alterar as regras de firewall à medida que atribui endereços IP a novos recursos.
- O tamanho predefinido de uma gama que pode criar é de endereços IP /28 ou 16.
- Não há limites para quantas gamas podes criar. Existem limites para o número máximo de endereços IP públicos estáticos que pode ter numa subscrição do Azure. O número de gamas que cria não pode abranger endereços IP públicos mais estáticos do que pode ter na sua subscrição. Para mais informações, consulte [os limites do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)
- Os endereços que cria utilizando endereços a partir do prefixo podem ser atribuídos a qualquer recurso Azure a que possa atribuir um endereço IP público.
- Pode facilmente ver quais endereços IP que são dados e não são dados dentro do alcance.

## <a name="scenarios"></a>Cenários
Pode associar os seguintes recursos a um endereço IP público estático a partir de um prefixo:

|Recurso|Scenario|Passos|
|---|---|---|
|Máquinas virtuais| Associar os IPs públicos de um prefixo às suas máquinas virtuais em Azure reduz a sobrecarga de gestão ao adicionar endereços IP a uma lista de autorizações na firewall. Pode adicionar um prefixo inteiro com uma única regra de firewall. À medida que escala com máquinas virtuais em Azure, pode associar os IPs do mesmo custo de poupança, tempo e sobrecarga de gestão.| Para associar os IPs de um prefixo à sua máquina virtual: </br> 1. [Criar um prefixo.](manage-public-ip-address-prefix.md) </br> 2. [Criar um IP a partir do prefixo.](manage-public-ip-address-prefix.md) </br> 3. [Associe o IP à interface de rede da sua máquina virtual.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> Também pode [associar os IPs a um Conjunto de Escala de Máquina Virtual.](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)
| Equilibradores de carga padrão | Associar os IPs públicos de um prefixo à configuração IP frontal ou regra de saída de um equilibrador de carga garante a simplificação do seu espaço de endereço IP público Azure. Simplifique o seu cenário através do aliciamento de ligações de saída a partir de uma gama de endereços IP contíguos. | Para associar os IPs de um prefixo ao seu equilibrador de carga: </br> 1. [Criar um prefixo.](manage-public-ip-address-prefix.md) </br> 2. [Criar um IP a partir do prefixo.](manage-public-ip-address-prefix.md) </br> 3. Ao criar o equilibrador de carga, selecione ou atualize o IP criado no passo 2 acima como o IP frontal do seu equilibrador de carga. |
| Azure Firewall | Você pode usar um IP público a partir de um prefixo para SNAT de saída. Todo o tráfego de rede virtual de saída é traduzido para o IP público [Azure Firewall.](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Para associar um IP de um prefixo à sua firewall: </br> 1. [Criar um prefixo.](manage-public-ip-address-prefix.md) </br> 2. [Criar um IP a partir do prefixo.](manage-public-ip-address-prefix.md) </br> 3. Quando [colocar a firewall Azure,](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)certifique-se de selecionar o IP que anteriormente deu a partir do prefixo.|
| VPN Gateway (AZ SKU) ou Gateway de aplicação v2 | Pode utilizar um IP público a partir de um prefixo para a sua zona redundante VPN ou gateway de aplicação v2. | Para associar um IP de um prefixo ao seu gateway: </br> 1. [Criar um prefixo.](manage-public-ip-address-prefix.md) </br> 2. [Criar um IP a partir do prefixo.](manage-public-ip-address-prefix.md) </br> 3. Quando implementar o [Gateway VPN](../vpn-gateway/tutorial-create-gateway-portal.md) ou [o Gateway de Aplicação,](../application-gateway/quick-create-portal.md#create-an-application-gateway)certifique-se de selecionar o IP que anteriormente deu a partir do prefixo.|

## <a name="constraints"></a>Restrições

- Não é possível especificar os endereços IP para o prefixo. O Azure dá os endereços IP para o prefixo, com base no tamanho especificado.
- Pode criar um prefixo de até 16 endereços IP ou um /28 por predefinição. Os [limites da rede](../azure-portal/supportability/networking-quota-requests.md) de revisão aumentam os pedidos e os [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para mais informações.
- Não se pode mudar o alcance, depois de criar o prefixo.
- Apenas endereços IP públicos estáticos criados com o SKU Standard podem ser atribuídos a partir do intervalo do prefixo. Para saber mais sobre o endereço IP público SKUs, consulte [o endereço IP público.](./public-ip-addresses.md#public-ip-addresses)
- Os endereços da gama só podem ser atribuídos aos recursos do Gestor de Recursos Azure. Os endereços não podem ser atribuídos a recursos no modelo clássico de implantação.
- Todos os endereços IP públicos criados a partir do prefixo devem existir na mesma região de Azure e subscrição como o prefixo. Os endereços devem ser atribuídos a recursos na mesma região e subscrição.
- Não é possível eliminar um prefixo se algum endereço nele for atribuído a recursos de endereço IP públicos associados a um recurso. Dissociem todos os recursos de endereço IP públicos que são atribuídos endereços IP a partir do prefixo primeiro.


## <a name="next-steps"></a>Passos seguintes

- [Criar](manage-public-ip-address-prefix.md) um prefixo de endereço IP público