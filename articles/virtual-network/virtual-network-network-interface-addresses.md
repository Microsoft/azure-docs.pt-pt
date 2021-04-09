---
title: Configurar endereços IP para uma interface de rede Azure | Microsoft Docs
description: Saiba como adicionar, alterar e remover endereços IP privados e públicos para uma interface de rede.
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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: allensu
ms.openlocfilehash: 1df132e558421d2ec6e26c3883c89457716dfc42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419019"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Add, change, or remove IP addresses for an Azure network interface (Adicionar, alterar ou remover endereços IP para uma interface de rede do Azure)

Saiba como adicionar, alterar e remover endereços IP públicos e privados para uma interface de rede. Os endereços IP privados atribuídos a uma interface de rede permitem que uma máquina virtual comunique com outros recursos numa rede virtual Azure e redes conectadas. Um endereço IP privado também permite a comunicação de saída para a Internet usando um endereço IP imprevisível. Um [endereço IP público](virtual-network-public-ip-address.md) atribuído a uma interface de rede permite a comunicação de entrada a uma máquina virtual a partir da Internet. O endereço também permite a comunicação de saída da máquina virtual para a Internet usando um endereço IP previsível. Para mais informações, consulte [a Compreensão das ligações de saída em Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Se precisar de criar, alterar ou eliminar uma interface de rede, leia o artigo de interface de [rede.](virtual-network-network-interface.md) Se precisar de adicionar interfaces de rede ou remover interfaces de rede de uma máquina virtual, leia o artigo [de Interfaces de Rede Add ou remova.](virtual-network-network-interface-vm.md)

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar etapas em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se para uma [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.com e faça login com a sua conta Azure.
- Se utilizar comandos PowerShell para completar tarefas neste artigo, ou executa os comandos na [Azure Cloud Shell](https://shell.azure.com/powershell), ou executando o PowerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar os comandos da interface da linha de comando do Azure (CLI) para completar as tarefas neste artigo, ou executar os comandos na [Azure Cloud Shell](https://shell.azure.com/bash), ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.

A conta em que inicia sessão ou liga-se ao Azure, deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações [apropriadas listadas](virtual-network-network-interface.md#permissions)nas permissões de interface da Rede .

## <a name="add-ip-addresses"></a>Adicionar endereços IP

Pode adicionar o maior número de endereços [IPv4](#ipv4) [privados](#private) e [públicos](#public) necessários a uma interface de rede, dentro dos limites enumerados no artigo [limites Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) Pode adicionar um endereço IPv6 privado a uma [configuração IP secundária](#secondary) (desde que não existam configurações ip secundárias existentes) para uma interface de rede existente. Cada interface de rede pode ter no máximo um endereço privado IPv6. Pode opcionalmente adicionar um endereço IPv6 público a uma configuração de interface de rede IPv6. Consulte [o IPv6](#ipv6) para obter mais informações sobre a utilização de endereços IPv6.

1. Na caixa que contém o texto *Procurar recursos* no topo do portal Azure, *digitar interfaces de rede*. Quando **as interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede para a seguinte forma que pretenda adicionar um endereço IPv4 da lista.
3. Em **DEFINIÇÕES**, selecione **configurações IP**.
4. Nas **configurações IP**, selecione **+ Adicionar**.
5. Especifique o seguinte e, em seguida, selecione **OK**:

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Name|Yes|Deve ser único para a interface de rede|
   |Tipo|Yes|Uma vez que está a adicionar uma configuração IP a uma interface de rede existente, e cada interface de rede deve ter uma configuração IP [primária,](#primary) a sua única opção é **Secundária**.|
   |Método de atribuição de endereços IP privados|Yes|[**Dinâmica**](#dynamic): O Azure atribui o próximo endereço disponível para o intervalo de endereços da sub-rede em que a interface de rede está implantada. [**Estática**](#static): Atribua um endereço não utilizado para o intervalo de endereços da sub-rede em que a interface de rede está implantada.|
   |Endereço IP público|No|**Deficientes:** Nenhum recurso de endereço IP público está atualmente associado à configuração IP. **Habilitado:** Selecione um endereço IP público IPv4 existente ou crie um novo. Para saber como criar um endereço IP público, leia o artigo [IP público.](virtual-network-public-ip-address.md#create-a-public-ip-address)|
6. Adicione manualmente endereços IP privados secundários ao sistema operativo da máquina virtual, preenchendo as instruções no artigo [de atribuir vários endereços IP a um artigo de sistemas operativos de máquina virtual.](virtual-network-multiple-ip-addresses-portal.md#os-config) Consulte endereços IP [privados](#private) para obter considerações especiais antes de adicionar manualmente endereços IP a um sistema operativo de máquina virtual. Não adicione quaisquer endereços IP públicos ao sistema operativo da máquina virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Alterar definições de endereço IP

Pode ter de alterar o método de atribuição de um endereço IPv4, alterar o endereço estático IPv4 ou alterar o endereço IP público atribuído a uma interface de rede. Se estiver a alterar o endereço IPv4 privado de uma configuração IP secundária associada a uma interface de rede secundária numa máquina virtual (saiba mais sobre [interfaces de rede primária e secundária),](virtual-network-network-interface-vm.md)coloque a máquina virtual no estado parado (deallocado) antes de completar os seguintes passos:

1. Na caixa que contém o texto *Procurar recursos* no topo do portal Azure, *digitar interfaces de rede*. Quando **as interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede que pretende visualizar ou alterar as definições de endereço IP da lista.
3. Em **DEFINIÇÕES**, selecione **configurações IP**.
4. Selecione a configuração IP que pretende modificar a partir da lista.
5. Altere as definições, conforme desejado, utilizando as informações sobre as definições no passo 5 de [Adicionar uma configuração IP](#add-ip-addresses).
6. Selecione **Guardar**.

>[!NOTE]
>Se a interface de rede primária tiver várias configurações IP e alterar o endereço IP privado da configuração IP primária, deve transferir manualmente os endereços IP primários e secundários para a interface de rede dentro do Windows (não é necessário para o Linux). Para atribuir manualmente endereços IP a uma interface de rede dentro de um sistema operativo, consulte [atribuir vários endereços IP a máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config). Para obter considerações especiais antes de adicionar manualmente endereços IP a um sistema operativo de máquina virtual, consulte endereços IP [privados.](#private) Não adicione quaisquer endereços IP públicos ao sistema operativo da máquina virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config atualização](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Remover endereços IP

Pode remover endereços IP [privados](#private) e [públicos](#public) de uma interface de rede, mas uma interface de rede deve ter sempre pelo menos um endereço IPv4 privado atribuído à mesma.

1. Na caixa que contém o texto *Procurar recursos* no topo do portal Azure, *digitar interfaces de rede*. Quando **as interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede que pretende remover endereços IP da lista.
3. Em **DEFINIÇÕES**, selecione **configurações IP**.
4. Selecione à direita uma configuração IP [secundária](#secondary) (não é possível eliminar a configuração [primária)](#primary) que pretende eliminar, selecione **Delete**, em seguida, selecione **Sim**, para confirmar a eliminação. Se a configuração tiver um recurso de endereço IP público associado a ele, o recurso é dissociado da configuração IP, mas o recurso não é eliminado.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az rede nic ip-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remover-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Configurações IP

Os endereços IP [públicos](#public) [e privados](#private) e (opcionalmente) são atribuídos a uma ou mais configurações IP atribuídas a uma interface de rede. Existem dois tipos de configurações IP:

### <a name="primary"></a>Primário

Cada interface de rede é atribuída a uma configuração IP primária. Uma configuração IP primária:

- Tem um endereço [IPv4](#ipv4) [privado](#private) atribuído. Não é possível atribuir um endereço [IPv6](#ipv6) privado a uma configuração IP primária.
- Pode também ter um endereço IPv4 [público](#public) atribuído a ele. Não é possível atribuir um endereço IPv6 público a uma configuração IP primária (IPv4). 

### <a name="secondary"></a>Secundária

Além de uma configuração IP primária, uma interface de rede pode ter configurações IP zero ou mais secundárias atribuídas à mesma. Uma configuração IP secundária:

- Deve ter um endereço IPv4 ou IPv6 privado atribuído. Se o endereço for IPv6, a interface de rede só pode ter uma configuração IP secundária. Se o endereço for IPv4, a interface de rede pode ter várias configurações de IP secundárias atribuídas ao mesmo. Para saber mais sobre quantos endereços IPv4 privados e públicos podem ser atribuídos a uma interface de rede, consulte o artigo [limites Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)
- Pode também ter um endereço público IPv4 ou IPv6 atribuído ao mesmo. Atribuir vários endereços IPv4 a uma interface de rede é útil em cenários como:
  - Hospedar vários websites ou serviços com diferentes endereços IP e certificados TLS/SSL num único servidor.
  - Uma máquina virtual que serve de aparelho virtual de rede, como uma firewall ou um equilibrador de carga.
  - A capacidade de adicionar qualquer um dos endereços IPv4 privados para qualquer uma das interfaces de rede a um pool traseiro do Azure Load Balancer. No passado, apenas o endereço IPv4 primário para a interface de rede primária poderia ser adicionado a uma piscina traseira. Para saber mais sobre como carregar o equilíbrio de várias configurações do IPv4, consulte o artigo [de configurações IP de equilíbrio de carga.](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
  - A capacidade de carregar um endereço IPv6 atribuído a uma interface de rede. Para saber mais sobre como carregar o equilíbrio para um endereço IPv6 privado, consulte o artigo [de endereço iPv6 de equilíbrio de carga.](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="address-types"></a>Tipos de endereços

Pode atribuir os seguintes tipos de endereços IP a uma [configuração IP:](#ip-configurations)

### <a name="private"></a>Privado

Os endereços [IPv4](#ipv4) ou IPv6 privados permitem que uma máquina virtual comunique com outros recursos numa rede virtual ou noutras redes conectadas. 

Por predefinição, os servidores Azure DHCP atribuem o endereço IPv4 privado para a [configuração IP primária](#primary) da interface de rede Azure à interface de rede dentro do sistema operativo da máquina virtual. A menos que seja necessário, nunca deve definir manualmente o endereço IP de uma interface de rede dentro do sistema operativo da máquina virtual.

> [!WARNING]
> Se o endereço IPv4 definido como o endereço IP primário de uma interface de rede dentro do sistema operativo de uma máquina virtual for sempre diferente do endereço IPv4 privado atribuído à configuração IP primária da interface de rede primária ligada a uma máquina virtual dentro do Azure, perde-se a conectividade com a máquina virtual.

Existem cenários em que é necessário definir manualmente o endereço IP de uma interface de rede dentro do sistema operativo da máquina virtual. Por exemplo, deve definir manualmente os endereços IP primários e secundários de um sistema operativo Windows ao adicionar vários endereços IP a uma máquina virtual Azure. Para uma máquina virtual Linux, só poderá ser necessário definir manualmente os endereços IP secundários. Consulte [os endereços IP adicionando a um sistema operativo VM](virtual-network-multiple-ip-addresses-portal.md#os-config) para obter mais informações. Se alguma vez precisar de alterar o endereço atribuído a uma configuração IP, recomenda-se que:

1. Certifique-se de que a máquina virtual está a receber um endereço dos servidores Azure DHCP. Uma vez que tenha, altere a atribuição do endereço IP de volta para DHCP dentro do sistema operativo e reinicie a máquina virtual.
2. Pare (deallocate) a máquina virtual.
3. Altere o endereço IP para a configuração IP dentro do Azure.
4. Inicie a máquina virtual.
5. [Configurar manualmente](virtual-network-multiple-ip-addresses-portal.md#os-config) os endereços IP secundários dentro do sistema operativo (e também o endereço IP primário dentro do Windows) para corresponder ao que definiu dentro do Azure.

Seguindo os passos anteriores, o endereço IP privado atribuído à interface de rede dentro do Azure, e dentro do sistema operativo de uma máquina virtual, permanece o mesmo. Para acompanhar quais as máquinas virtuais dentro da sua subscrição para as quais definiu manualmente endereços IP dentro de um sistema operativo, considere adicionar uma [etiqueta](../azure-resource-manager/management/tag-resources.md) Azure às máquinas virtuais. Pode utilizar a "atribuição de endereços IP: Estática", por exemplo. Desta forma, pode facilmente encontrar as máquinas virtuais dentro da sua subscrição que definiu manualmente o endereço IP para dentro do sistema operativo.

Além de permitir que uma máquina virtual comunique com outros recursos dentro das mesmas redes virtuais, ou conectadas, um endereço IP privado também permite que uma máquina virtual comunique a saída para a Internet. As ligações de saída são o endereço de rede de origem traduzido pelo Azure para um endereço IP público imprevisível. Para saber mais sobre a conectividade de saída da Internet do Azure, leia o artigo [de conectividade de saída da Internet Azure.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Não é possível comunicar a caminho do endereço IP privado de uma máquina virtual a partir da Internet. Se as suas ligações de saída necessitarem de um endereço IP público previsível, associe um recurso de endereço IP público a uma interface de rede.

### <a name="public"></a>Público

Os endereços IP públicos atribuídos através de um recurso de endereço IP público permitem a conectividade de entrada a uma máquina virtual a partir da Internet. As ligações de saída para a Internet utilizam um endereço IP previsível. Consulte [as ligações de saída da Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter mais detalhes. Pode atribuir um endereço IP público a uma configuração IP, mas não é necessário. Se não atribuir um endereço IP público a uma máquina virtual associando um recurso de endereço IP público, a máquina virtual ainda pode comunicar saída para a Internet. Neste caso, o endereço IP privado é o endereço de rede de origem traduzido pelo Azure para um endereço IP público imprevisível. Para saber mais sobre os recursos de endereço IP público, consulte [o recurso de endereço IP público.](virtual-network-public-ip-address.md)

Existem limites para o número de endereços IP privados e públicos que pode atribuir a uma interface de rede. Para mais detalhes, leia o artigo [limites Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

> [!NOTE]
> O Azure traduz o endereço IP privado de uma máquina virtual para um endereço IP público. Como resultado, o sistema operativo de uma máquina virtual desconhece qualquer endereço IP público que lhe seja atribuído, pelo que não é necessário atribuir manualmente um endereço IP público dentro do sistema operativo.

## <a name="assignment-methods"></a>Métodos de atribuição

Os endereços IP públicos e privados são atribuídos utilizando um dos seguintes métodos de atribuição:

### <a name="dynamic"></a>Dinâmica

Os endereços dinâmicos iPv4 e IPv6 (opcionalmente) são atribuídos por padrão.

- **Público apenas**: Azure atribui o endereço de uma gama única a cada região de Azure. Para saber quais as gamas atribuídas a cada região, consulte [os intervalos IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço pode mudar quando uma máquina virtual é interrompida (deallocated) e depois reiniciada. Não é possível atribuir um endereço IPv6 público a uma configuração IP utilizando qualquer método de atribuição.
- **Apenas privado**: O Azure reserva os primeiros quatro endereços em cada gama de endereços da sub-rede e não atribui os endereços. O Azure atribui o endereço disponível seguinte a um recurso do intervalo de endereços da sub-rede. Por exemplo, se o intervalo de endereços da sub-rede for de 10.0.0.0/16 e os endereços 10.0.0.4-10.0.0.14 já estiverem atribuídos (.0-3 estão reservados), a Azure atribui 10.0.0.15 ao recurso. Dinâmico é o método de alocação predefinido. Após a atribuição, os endereços IP dinâmicos só são lançados se uma interface de rede for eliminada, atribuída a uma sub-rede diferente dentro da mesma rede virtual, ou se o método de alocação for alterado para estático e for especificado um endereço IP diferente. Por predefinição, o Azure atribui o endereço atribuído de forma dinâmica anterior como endereço estático quando o utilizador altera o método de alocação de dinâmico para estático. 

### <a name="static"></a>Estático

Pode (opcionalmente) atribuir um endereço IPv4 ou IPv6 a uma configuração IP. Para saber mais sobre como o Azure atribui endereços IPv4 públicos estáticos, consulte [endereços IP públicos](virtual-network-public-ip-address.md).

- **Público apenas**: Azure atribui o endereço de uma gama única a cada região de Azure. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure. O endereço não muda até que o recurso de endereço IP público a que é atribuído seja eliminado, ou o método de atribuição seja alterado para dinâmico. Se o recurso de endereço IP público estiver associado a uma configuração IP, deve ser dissociado da configuração IP antes de alterar o seu método de atribuição.
- **Apenas a privados**: Selecione e atribua um endereço a partir do intervalo de endereços da sub-rede. O endereço que atribuir pode ser qualquer endereço dentro do intervalo de endereços da sub-rede que não seja um dos primeiros quatro endereços no intervalo de endereços da sub-rede e que não esteja atualmente atribuído a qualquer outro recurso na sub-rede. Os endereços estáticos só são lançados se uma interface de rede for eliminada. Se alterar o método de atribuição para estático, o Azure atribui dinamicamente o endereço IP dinâmico anteriormente atribuído como endereço estático, mesmo que o endereço não seja o próximo endereço disponível na gama de endereços da sub-rede. O endereço também muda se a interface de rede for atribuída a uma sub-rede diferente dentro da mesma rede virtual, mas, para atribuir a interface de rede a uma sub-rede diferente, tem, primeiro, de alterar o método de alocação de estático para dinâmico. Assim que tiver atribuído a interface de rede a uma sub-rede diferente, pode alterar o método de alocação novamente para estático e atribuir um endereço IP do intervalo de endereços da nova sub-rede.

## <a name="ip-address-versions"></a>Versões de endereço IP

Pode especificar as seguintes versões ao atribuir endereços:

### <a name="ipv4"></a>IPv4

Cada interface de rede deve ter uma configuração IP [primária](#primary) com um endereço [IPv4](#ipv4) [privado](#private) atribuído. Pode adicionar uma ou mais configurações de IP [secundárias](#secondary) que cada um tem um IPv4 privado e (opcionalmente) um endereço IP [público](#public) IPv4.

### <a name="ipv6"></a>IPv6

Pode atribuir um endereço IPv6 nulo ou um endereço [IPv6](#ipv6) a uma configuração IP secundária de uma interface de rede. A interface de rede não pode ter configurações IP secundárias existentes. Cada interface de rede pode ter no máximo um endereço privado IPv6. Pode opcionalmente adicionar um endereço IPv6 público a uma configuração de interface de rede IPv6.

> [!NOTE]
> Embora possa criar uma interface de rede com um endereço IPv6 utilizando o portal, não é possível adicionar uma interface de rede existente a uma nova máquina virtual, ou existente, utilizando o portal. Utilize o PowerShell ou o Azure CLI para criar uma interface de rede com um endereço IPv6 privado e, em seguida, prenda a interface de rede ao criar uma máquina virtual. Não é possível anexar uma interface de rede com um endereço IPv6 privado que lhe seja atribuído a uma máquina virtual existente. Não é possível adicionar um endereço IPv6 privado a uma configuração IP para qualquer interface de rede ligada a uma máquina virtual utilizando quaisquer ferramentas (portal, CLI ou PowerShell).

## <a name="skus"></a>SKUs

Um endereço IP público é criado com o SKU básico ou padrão. Para obter mais informações sobre as diferenças de SKU, consulte [Gerir os endereços IP públicos.](virtual-network-public-ip-address.md)

> [!NOTE]
> Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](./network-security-groups-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.

## <a name="next-steps"></a>Passos seguintes
Para criar uma máquina virtual com diferentes configurações IP, leia os seguintes artigos:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com várias interfaces de rede|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar um único NIC VM com vários endereços IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Crie um único NIC VM com um endereço IPv6 privado (atrás de um Balançador de Carga Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell,](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [modelo de gestor de recursos azure](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|