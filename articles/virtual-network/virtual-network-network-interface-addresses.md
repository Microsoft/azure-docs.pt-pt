---
title: Configure endereços IP para uma interface de rede Azure [ Microsoft Docs
description: Saiba como adicionar, alterar e remover endereços IP privados e públicos para uma interface de rede.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: b5655a58c3538ac47e8649619b079dc46ee01242
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473230"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Add, change, or remove IP addresses for an Azure network interface (Adicionar, alterar ou remover endereços IP para uma interface de rede do Azure)

Saiba como adicionar, alterar e remover endereços IP públicos e privados para uma interface de rede. Os endereços IP privados atribuídos a uma interface de rede permitem a uma máquina virtual comunicar com outros recursos numa rede virtual Azure e redes conectadas. Um endereço IP privado também permite a comunicação de saída para a Internet utilizando um endereço IP imprevisível. Um [endereço IP público](virtual-network-public-ip-address.md) atribuído a uma interface de rede permite a comunicação de entrada a uma máquina virtual a partir da Internet. O endereço também permite a comunicação de saída da máquina virtual para a Internet usando um endereço IP previsível. Para mais detalhes, consulte [a Understanding outbound connections in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Se precisar de criar, alterar ou eliminar uma interface de rede, leia o artigo de interface de [rede Gerir.](virtual-network-network-interface.md) Se precisar de adicionar interfaces de rede ou remover interfaces de rede a partir de uma máquina virtual, leia o artigo [De si](virtual-network-network-interface-vm.md) ou remova o artigo de interfaces de rede.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar os passos em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se numa [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o https://portal.azure.comportal, abra e faça login na sua conta Azure.
- Se utilizar os comandos PowerShell para completar tarefas neste artigo, execute os comandos na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0.0 ou posterior do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comando Azure (CLI) para completar tarefas neste artigo, execute os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI `az login` localmente, também precisa de correr para criar uma ligação com o Azure.

A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações apropriadas listadas nas [permissões](virtual-network-network-interface.md#permissions)da interface da Rede.

## <a name="add-ip-addresses"></a>Adicionar endereços IP

Pode adicionar o maior número de endereços [IPv4](#ipv4) [privados](#private) e [públicos](#public) necessários a uma interface de rede, dentro dos limites listados no artigo dos [limites do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) Pode adicionar um endereço IPv6 privado a uma [configuração ip secundária](#secondary) (desde que não existam configurações IP secundárias existentes) para uma interface de rede existente. Cada interface de rede pode ter, no máximo, um endereço privado IPv6. Pode adicionar opcionalmente um endereço IPv6 público a uma configuração de interface de rede IPv6. Consulte [o IPv6](#ipv6) para obter detalhes sobre a utilização de endereços IPv6.

1. Na caixa que contém os recursos de *pesquisa* de texto no topo do portal Azure, tipo *interfaces*de rede . Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede que pretende adicionar um endereço IPv4 para a lista.
3. Em **DEFINIÇÕES,** selecione **configurações IP**.
4. Em **configurações IP,** selecione **+ Adicione**.
5. Especifique o seguinte e, em seguida, selecione **OK:**

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Nome|Sim|Deve ser único para a interface de rede|
   |Tipo|Sim|Uma vez que está a adicionar uma configuração IP a uma interface de rede existente, e cada interface de rede deve ter uma configuração [IP primária,](#primary) a sua única opção é **Secundária**.|
   |Método privado de atribuição de endereços IP|Sim|[**Dinâmico**](#dynamic): O Azure atribui o próximo endereço disponível para a gama de endereços de sub-rede em que a interface de rede está implantada. [**Estática**](#static): Atribua um endereço não utilizado para a gama de endereços da sub-rede em que a interface de rede está implantada.|
   |Endereço IP público|Não|**Deficientes:** Nenhum recurso de endereço IP público está atualmente associado à configuração IP. **Habilitado:** Selecione um endereço IP público iPv4 existente ou crie um novo. Para saber como criar um endereço IP público, leia o artigo [Endereços IP públicos.](virtual-network-public-ip-address.md#create-a-public-ip-address)|
6. Adicione manualmente endereços IP privados secundários ao sistema operativo virtual da máquina, preenchendo as instruções nos vários endereços IP da Atribuição ao artigo dos [sistemas operativos de máquinas virtuais.](virtual-network-multiple-ip-addresses-portal.md#os-config) Consulte endereços IP [privados](#private) para considerações especiais antes de adicionar manualmente endereços IP a um sistema operativo virtual de máquinas. Não adicione nenhum endereço IP público ao sistema operativo virtual da máquina.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Alterar as definições de endereço IP

Pode ser necessário alterar o método de atribuição de um endereço IPv4, alterar o endereço IPv4 estático ou alterar o endereço IP público atribuído a uma interface de rede. Se estiver a alterar o endereço IPv4 privado de uma configuração ip secundária associada a uma interface de rede secundária numa máquina virtual (saiba mais sobre interfaces de [rede primária e secundária),](virtual-network-network-interface-vm.md)coloque a máquina virtual no estado de paragem (deallocated) antes de completar os seguintes passos:

1. Na caixa que contém os recursos de *pesquisa* de texto no topo do portal Azure, tipo *interfaces*de rede . Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede que pretende visualizar ou alterar as definições de endereço IP da lista.
3. Em **DEFINIÇÕES,** selecione **configurações IP**.
4. Selecione a configuração IP que pretende modificar da lista.
5. Altere as definições, conforme desejado, utilizando a informação sobre as definições no passo 5 de [Adicionar uma configuração IP](#add-ip-addresses).
6. Selecione **Guardar**.

>[!NOTE]
>Se a interface de rede primária tiver múltiplas configurações IP e alterar o endereço IP privado da configuração IP primária, terá de reatribuir manualmente os endereços IP primários e secundários à interface de rede dentro do Windows (não necessário para o Linux). Para atribuir manualmente endereços IP a uma interface de rede dentro de um sistema operativo, consulte [atribuir vários endereços IP a máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config). Para considerações especiais antes de adicionar manualmente endereços IP a um sistema operativo de máquina virtual, consulte endereços IP [privados.](#private) Não adicione nenhum endereço IP público ao sistema operativo virtual da máquina.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az rede nic ip-config atualização](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Remover endereços IP

Pode remover endereços IP [privados](#private) e [públicos](#public) de uma interface de rede, mas uma interface de rede deve ter sempre pelo menos um endereço IPv4 privado atribuído a ele.

1. Na caixa que contém os recursos de *pesquisa* de texto no topo do portal Azure, tipo *interfaces*de rede . Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede que pretende remover endereços IP da lista.
3. Em **DEFINIÇÕES,** selecione **configurações IP**.
4. Selecione uma configuração [IP secundária](#secondary) (não pode excluir a configuração [primária)](#primary) que pretende eliminar, selecione **Eliminar,** e depois selecione **Sim,** para confirmar a eliminação. Se a configuração tivesse um recurso de endereço IP público associado a ele, o recurso é dissociado da configuração IP, mas o recurso não é eliminado.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az rede nic ip-config excluir](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remover-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Configurações IP

Os endereços IP [públicos](#public) [privados](#private) e (opcionalmente) são atribuídos a uma ou mais configurações IP atribuídas a uma interface de rede. Existem dois tipos de configurações IP:

### <a name="primary"></a>Primária

Cada interface de rede é atribuída uma configuração ip primária. Uma configuração ip primária:

- Tem um endereço [IPv4](#ipv4) [privado](#private) atribuído a ele. Não é possível atribuir um endereço [IPv6](#ipv6) privado a uma configuração ip primária.
- Pode também ter um endereço [IPv4 público](#public) atribuído a ele. Não é possível atribuir um endereço IPv6 público a uma configuração IP primária (IPv4). 

### <a name="secondary"></a>Secundária

Além de uma configuração ip primária, uma interface de rede pode ter configurações IP zero ou mais secundárias atribuídas a ela. Uma configuração ip secundária:

- Deve ter um endereço IPv4 ou IPv6 privado atribuído a ele. Se o endereço for IPv6, a interface de rede só pode ter uma configuração IP secundária. Se o endereço for IPv4, a interface de rede pode ter várias configurações ip secundárias atribuídas ao mesmo. Para saber mais sobre quantos endereços IPv4 privados e públicos podem ser atribuídos a uma interface de rede, consulte o artigo dos [limites do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)
- Pode também ter um endereço IPv4 ou IPv6 público atribuído a ele. Atribuir vários endereços IPv4 a uma interface de rede é útil em cenários como:
  - Hospedar vários websites ou serviços com diferentes endereços IP e certificados TLS/SSL num único servidor.
  - Uma máquina virtual que serve de aparelho virtual de rede, como firewall ou equilibrador de carga.
  - A capacidade de adicionar qualquer um dos endereços IPv4 privados para qualquer uma das interfaces de rede a um pool de back-end Azure Load Balancer. No passado, apenas o endereço iPv4 primário para a interface de rede primária poderia ser adicionado a um pool de back-end. Para saber mais sobre como carregar o equilíbrio de várias configurações iPv4, consulte o artigo de configurações IP [de equilíbrio de carga.](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
  - A capacidade de carregar um endereço IPv6 atribuído a uma interface de rede. Para saber mais sobre como carregar o equilíbrio para um endereço IPv6 privado, consulte o artigo de [endereços IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do saldo de carga.

## <a name="address-types"></a>Tipos de endereços

Pode atribuir os seguintes tipos de endereços IP a uma [configuração IP:](#ip-configurations)

### <a name="private"></a>Privado

Os endereços [IPv4](#ipv4) ou IPv6 privados permitem que uma máquina virtual comunique com outros recursos numa rede virtual ou noutras redes conectadas. 

Por predefinição, os servidores Azure DHCP atribuem o endereço IPv4 privado para a [configuração ip primária](#primary) da interface de rede Azure para a interface de rede dentro do sistema operativo virtual da máquina. A menos que seja necessário, nunca deve definir manualmente o endereço IP de uma interface de rede dentro do sistema operativo da máquina virtual.

> [!WARNING]
> Se o endereço IPv4 definido como o endereço IP primário de uma interface de rede dentro do sistema operativo de uma máquina virtual for sempre diferente do endereço IPv4 privado atribuído à configuração ip primária da interface de rede primária anexada a uma máquina virtual dentro do Azure, perde conectividade com a máquina virtual.

Existem cenários em que é necessário definir manualmente o endereço IP de uma interface de rede dentro do sistema operativo da máquina virtual. Por exemplo, deve definir manualmente os endereços IP primários e secundários de um sistema operativo Windows ao adicionar vários endereços IP a uma máquina virtual Azure. Para uma máquina virtual Linux, só poderá ser necessário definir manualmente os endereços IP secundários. Consulte adicionar [endereços IP a um sistema operativo VM](virtual-network-multiple-ip-addresses-portal.md#os-config) para obter mais detalhes. Se alguma vez precisar de alterar o endereço atribuído a uma configuração IP, recomenda-se que:

1. Certifique-se de que a máquina virtual está a receber um endereço dos servidores Azure DHCP. Assim que o tiver, altere a atribuição do endereço IP para DHCP dentro do sistema operativo e reinicie a máquina virtual.
2. Parar (desalocar) a máquina virtual.
3. Altere o endereço IP para a configuração IP dentro do Azure.
4. Inicie a máquina virtual.
5. [Configure manualmente](virtual-network-multiple-ip-addresses-portal.md#os-config) os endereços IP secundários dentro do sistema operativo (e também o endereço IP primário dentro do Windows) para corresponder ao que definiu dentro do Azure.

Seguindo os passos anteriores, o endereço IP privado atribuído à interface de rede dentro do Azure, e dentro do sistema operativo de uma máquina virtual, permanece o mesmo. Para acompanhar quais as máquinas virtuais dentro da sua subscrição para as quais definiu manualmente os endereços IP dentro de um sistema operativo, considere adicionar uma [etiqueta](../azure-resource-manager/management/tag-resources.md) Azure às máquinas virtuais. Pode utilizar "atribuição de endereçoIP: Estática", por exemplo. Desta forma, pode facilmente encontrar as máquinas virtuais dentro da sua subscrição que definiu manualmente o endereço IP para dentro do sistema operativo.

Além de permitir que uma máquina virtual comunique com outros recursos dentro das mesmas redes virtuais, ou conectadas redes virtuais, um endereço IP privado também permite que uma máquina virtual comunique a saída para a Internet. As ligações de saída são endereço de rede de origem traduzido pelo Azure para um endereço IP público imprevisível. Para saber mais sobre a conectividade de saída da Internet azure, leia o artigo de conectividade de saída da [Internet azure.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Não é possível comunicar a entrada no endereço IP privado de uma máquina virtual a partir da Internet. Se as suas ligações de saída necessitarem de um endereço IP público previsível, associe um recurso de endereço IP público a uma interface de rede.

### <a name="public"></a>Público

Os endereços IP públicos atribuídos através de um recurso de endereço IP público permitem a conectividade de entrada a uma máquina virtual a partir da Internet. As ligações de saída à Internet utilizam um endereço IP previsível. Consulte as ligações de [saída em Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter mais detalhes. Pode atribuir um endereço IP público a uma configuração IP, mas não é necessário. Se não atribuir um endereço IP público a uma máquina virtual associando um recurso público de endereço IP, a máquina virtual ainda pode comunicar com saída para a Internet. Neste caso, o endereço IP privado é endereço de rede de origem traduzido pelo Azure para um endereço IP público imprevisível. Para saber mais sobre os recursos de endereços IP públicos, consulte o recurso de endereço IP [público.](virtual-network-public-ip-address.md)

Existem limites para o número de endereços IP privados e públicos que pode atribuir a uma interface de rede. Para mais detalhes, leia o artigo [dos limites do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

> [!NOTE]
> O Azure traduz o endereço IP privado de uma máquina virtual para um endereço IP público. Como resultado, o sistema operativo de uma máquina virtual desconhece qualquer endereço IP público que lhe seja atribuído, pelo que não é necessário atribuir manualmente um endereço IP público dentro do sistema operativo.

## <a name="assignment-methods"></a>Métodos de atribuição

Os endereços IP públicos e privados são atribuídos utilizando um dos seguintes métodos de atribuição:

### <a name="dynamic"></a>Dinâmica

Os endereços dinâmicos iPv4 e IPv6 (opcionalmente) são atribuídos por padrão.

- **Apenas público:** Azure atribui o endereço de uma gama única a cada região de Azure. Para saber quais as gamas atribuídas a cada região, consulte [o Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). O endereço pode mudar quando uma máquina virtual é parada (negociação), e depois reiniciada. Não é possível atribuir um endereço IPv6 público a uma configuração IP utilizando qualquer um dos métodos de atribuição.
- **Apenas em privado:** O Azure reserva os primeiros quatro endereços em cada gama de endereços da sub-rede e não atribui os endereços. O Azure atribui o endereço disponível seguinte a um recurso do intervalo de endereços da sub-rede. Por exemplo, se o intervalo de endereços da sub-rede for 10.0.0.0/16 e os endereços 10.0.0.0.4-10.0.0.14 já estiverem atribuídos (.0-.3 está reservado), o Azure atribui o 10.0.0.15 ao recurso. Dinâmico é o método de alocação predefinido. Após a atribuição, os endereços IP dinâmicos só são lançados se uma interface de rede for eliminada, atribuída a uma sub-rede diferente dentro da mesma rede virtual, ou se o método de alocação for alterado para estático e for especificado um endereço IP diferente. Por predefinição, o Azure atribui o endereço atribuído de forma dinâmica anterior como endereço estático quando o utilizador altera o método de alocação de dinâmico para estático. 

### <a name="static"></a>Estático

Pode (opcionalmente) atribuir um endereço iPv4 ou IPv6 estático público ou privado a uma configuração IP. Para saber mais sobre como o Azure atribui endereços iPv4 públicos estáticos, consulte [endereços IP públicos](virtual-network-public-ip-address.md).

- **Apenas público:** Azure atribui o endereço de uma gama única a cada região de Azure. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure. O endereço não muda até que o recurso de endereço IP público a que é atribuído seja eliminado, ou o método de atribuição seja alterado para dinâmico. Se o recurso de endereço IP público estiver associado a uma configuração IP, deve ser dissociado da configuração IP antes de alterar o seu método de atribuição.
- **Apenas em privado:** Selecione e atribua um endereço do intervalo de endereços da subnet. O endereço que atribuir pode ser qualquer endereço dentro do intervalo de endereços da sub-rede que não seja um dos primeiros quatro endereços no intervalo de endereços da sub-rede e que não esteja atualmente atribuído a qualquer outro recurso na sub-rede. Os endereços estáticos só são lançados se uma interface de rede for eliminada. Se alterar o método de atribuição para estática, o Azure atribui dinamicamente o endereço IP dinâmico anteriormente atribuído como endereço estático, mesmo que o endereço não seja o próximo endereço disponível na gama de endereços da subnet. O endereço também muda se a interface de rede for atribuída a uma sub-rede diferente dentro da mesma rede virtual, mas, para atribuir a interface de rede a uma sub-rede diferente, tem, primeiro, de alterar o método de alocação de estático para dinâmico. Assim que tiver atribuído a interface de rede a uma sub-rede diferente, pode alterar o método de alocação novamente para estático e atribuir um endereço IP do intervalo de endereços da nova sub-rede.

## <a name="ip-address-versions"></a>Versões de endereço IP

Pode especificar as seguintes versões ao atribuir endereços:

### <a name="ipv4"></a>IPv4

Cada interface de rede deve ter uma configuração [IP primária](#primary) com um endereço [IPv4](#ipv4) [privado](#private) atribuído. Pode adicionar uma ou mais configurações [ip secundárias](#secondary) que cada um tem um IPv4 privado e (opcionalmente) um endereço IP [público](#public) IPv4.

### <a name="ipv6"></a>IPv6

Pode atribuir zero ou um endereço [IPv6](#ipv6) privado a uma configuração IP secundária de uma interface de rede. A interface de rede não pode ter configurações ip secundárias existentes. Cada interface de rede pode ter, no máximo, um endereço privado IPv6. Pode adicionar opcionalmente um endereço IPv6 público a uma configuração de interface de rede IPv6.

> [!NOTE]
> Embora possa criar uma interface de rede com um endereço IPv6 utilizando o portal, não é possível adicionar uma interface de rede existente a uma nova máquina virtual, ou existente, utilizando o portal. Utilize o PowerShell ou o Azure CLI para criar uma interface de rede com um endereço IPv6 privado e, em seguida, fixe a interface de rede quando criar uma máquina virtual. Não é possível anexar uma interface de rede com um endereço IPv6 privado atribuído a uma máquina virtual existente. Não é possível adicionar um endereço IPv6 privado a uma configuração IP para qualquer interface de rede ligada a uma máquina virtual utilizando quaisquer ferramentas (portal, CLI ou PowerShell).

Não é possível atribuir um endereço IPv6 público a uma configuração IP primária ou secundária.

## <a name="skus"></a>SKU

Um endereço IP público é criado com o SKU básico ou padrão. Para mais informações sobre as diferenças de SKU, consulte [Gerir endereços IP públicos](virtual-network-public-ip-address.md).

> [!NOTE]
> Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.

## <a name="next-steps"></a>Passos seguintes
Para criar uma máquina virtual com diferentes configurações ip, leia os seguintes artigos:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com várias interfaces de rede|[CLI,](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Crie um único NIC VM com vários endereços IPv4|[CLI,](virtual-network-multiple-ip-addresses-cli.md) [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Crie um único NIC VM com um endereço IPv6 privado (atrás de um Equilíbrio de Carga Azure)|[CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell,](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Modelo de Gestor de [Recursos Azure](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
