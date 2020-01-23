---
title: Configurar endereços IP para uma interface de rede do Azure | Microsoft Docs
description: Saiba como adicionar, alterar e remover endereços IP públicos e privados para uma interface de rede.
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
ms.openlocfilehash: a2a85d98bf29e78d58bf0c578ce79943bae21fc1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543091"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Adicionar, alterar ou remover endereços IP para uma interface de rede do Azure

Saiba como adicionar, alterar e remover endereços IP públicos e privados para uma interface de rede. Endereços IP privados atribuídos a uma interface de rede permitem que uma máquina virtual se comunique com outros recursos em uma rede virtual do Azure e redes conectadas. Um endereço IP privado também permite a comunicação de saída com a Internet usando um endereço IP imprevisível. Um [endereço IP público](virtual-network-public-ip-address.md) atribuído a uma interface de rede permite a comunicação de entrada para uma máquina virtual da Internet. O endereço também permite a comunicação de saída da máquina virtual para a Internet usando um endereço IP previsível. Para obter detalhes, consulte [noções básicas sobre conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Se você precisar criar, alterar ou excluir uma interface de rede, leia o artigo [gerenciar uma interface de rede](virtual-network-network-interface.md) . Se você precisar adicionar adaptadores de rede ou remover adaptadores de rede de uma máquina virtual, leia o artigo [Adicionar ou remover interfaces de rede](virtual-network-network-interface-vm.md) .

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Se você ainda não tiver uma conta do Azure, Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se estiver usando comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se você estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este tutorial requer o CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com o, deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões de interface de rede](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Adicionar endereços IP

Você pode adicionar quantos endereços [IPv4](#ipv4) [públicos](#public) e [privados](#private) forem necessários para uma interface de rede, dentro dos limites listados no artigo [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . Você pode adicionar um endereço IPv6 privado a uma [configuração de IP secundário](#secondary) (desde que não haja nenhuma configuração de IP secundário existente) para uma interface de rede existente. Cada interface de rede pode ter no máximo um endereço IPv6 privado. Opcionalmente, você pode adicionar um endereço IPv6 público a uma configuração de interface de rede IPv6. Consulte [IPv6](#ipv6) para obter detalhes sobre como usar endereços IPv6.

1. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *interfaces de rede*. Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede à qual você deseja adicionar um endereço IPv4 na lista.
3. Em **configurações**, selecione **configurações de IP**.
4. Em **configurações de IP**, selecione **+ Adicionar**.
5. Especifique o seguinte e, em seguida, selecione **OK**:

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Nome|Sim|Deve ser exclusivo para a interface de rede|
   |Tipo|Sim|Como você está adicionando uma configuração de IP a um adaptador de rede existente e cada interface de rede deve ter uma configuração de IP [primário](#primary) , sua única opção é **secundária**.|
   |Método de atribuição de endereço IP privado|Sim|[**Dinâmico**](#dynamic): o Azure atribui o próximo endereço disponível para o intervalo de endereços de sub-rede no qual o adaptador de rede está implantado. [**Estático**](#static): você atribui um endereço não utilizado ao intervalo de endereços de sub-rede no qual o adaptador de rede está implantado.|
   |Endereço IP público|Não|**Desabilitado:** Nenhum recurso de endereço IP público está associado no momento à configuração de IP. **Habilitado:** Selecione um endereço IP público IPv4 existente ou crie um novo. Para saber como criar um endereço IP público, leia o artigo [endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address) .|
6. Adicione manualmente endereços IP privados secundários ao sistema operacional da máquina virtual, concluindo as instruções no artigo [atribuir vários endereços IP a sistemas operacionais de máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config) . Consulte endereços IP [privados](#private) para considerações especiais antes de adicionar manualmente os endereços IP a um sistema operacional de máquina virtual. Não adicione nenhum endereço IP público ao sistema operacional da máquina virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Alterar configurações de endereço IP

Talvez seja necessário alterar o método de atribuição de um endereço IPv4, alterar o endereço IPv4 estático ou alterar o endereço IP público atribuído a uma interface de rede. Se você estiver alterando o endereço IPv4 privado de uma configuração de IP secundário associada a uma interface de rede secundária em uma máquina virtual (Saiba mais sobre as [interfaces de rede primária e secundária](virtual-network-network-interface-vm.md)), coloque a máquina virtual no estado parado (desalocado) antes de concluir as seguintes etapas:

1. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *interfaces de rede*. Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione o adaptador de rede que você deseja exibir ou altere as configurações de endereço IP da lista.
3. Em **configurações**, selecione **configurações de IP**.
4. Selecione a configuração de IP que você deseja modificar na lista.
5. Altere as configurações, conforme desejado, usando as informações sobre as configurações na etapa 5 de [Adicionar uma configuração de IP](#add-ip-addresses).
6. Selecione **Guardar**.

>[!NOTE]
>Se a interface de rede principal tiver várias configurações de IP e você alterar o endereço IP privado da configuração de IP primário, será necessário reatribuir manualmente os endereços IP primários e secundários para o adaptador de rede no Windows (não é necessário para o Linux) . Para atribuir manualmente endereços IP a um adaptador de rede em um sistema operacional, consulte [atribuir vários endereços IP a máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config). Para considerações especiais antes de adicionar manualmente os endereços IP a um sistema operacional de máquina virtual, consulte endereços IP [privados](#private) . Não adicione nenhum endereço IP público ao sistema operacional da máquina virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Remover endereços IP

Você pode remover endereços IP [públicos](#public) e [privados](#private) de um adaptador de rede, mas uma interface de rede sempre deve ter pelo menos um endereço IPv4 privado atribuído a ele.

1. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *interfaces de rede*. Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione o adaptador de rede para o qual você deseja remover endereços IP da lista.
3. Em **configurações**, selecione **configurações de IP**.
4. Selecione uma configuração de IP [secundária](#secondary) (não é possível excluir a configuração [primária](#primary) ) que você deseja excluir, selecione **excluir**e, em seguida, selecione **Sim**para confirmar a exclusão. Se a configuração tiver um recurso de endereço IP público associado a ela, o recurso será dissociado da configuração de IP, mas o recurso não será excluído.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Configurações de IP

Endereços IP [públicos](#public) [e (](#private) opcionalmente) são atribuídos a uma ou mais configurações de IP atribuídas a uma interface de rede. Há dois tipos de configurações de IP:

### <a name="primary"></a>Primária

Cada interface de rede é atribuída a uma configuração de IP primário. Uma configuração de IP primário:

- Tem um endereço [IPv4](#ipv4) [privado](#private) atribuído a ele. Você não pode atribuir um endereço [IPv6](#ipv6) privado a uma configuração de IP primário.
- Também pode ter um endereço IPv4 [público](#public) atribuído a ele. Você não pode atribuir um endereço IPv6 público a uma configuração de IP primário (IPv4). 

### <a name="secondary"></a>Secundária

Além de uma configuração de IP primário, uma interface de rede pode ter zero ou mais configurações de IP secundários atribuídas a ela. Uma configuração de IP secundário:

- Deve ter um endereço IPv4 ou IPv6 privado atribuído a ele. Se o endereço for IPv6, a interface de rede poderá ter apenas uma configuração de IP secundário. Se o endereço for IPv4, a interface de rede poderá ter várias configurações de IP secundários atribuídas a ele. Para saber mais sobre quantos endereços IPv4 públicos e privados podem ser atribuídos a uma interface de rede, consulte o artigo [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .
- Também pode ter um endereço IPv4 ou IPv6 público atribuído a ele. A atribuição de vários endereços IPv4 a uma interface de rede é útil em cenários como:
  - Alojar vários Web Sites ou serviços com diferentes endereços IP e certificados SSL num único servidor.
  - Uma máquina virtual que serve como uma solução de virtualização de rede, como um firewall ou um balanceador de carga.
  - A capacidade de adicionar qualquer um dos endereços IPv4 privados para qualquer uma das interfaces de rede a um pool de back-ends Azure Load Balancer. No passado, somente o endereço IPv4 primário para o adaptador de rede primário poderia ser adicionado a um pool de back-ends. Para saber mais sobre como balancear a carga de várias configurações de IPv4, confira o artigo [balanceamento de carga de várias configurações de IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . 
  - A capacidade de balancear a carga de um endereço IPv6 atribuído a uma interface de rede. Para saber mais sobre como balancear a carga para um endereço IPv6 privado, confira o artigo [balanceamento de carga de endereços IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .

## <a name="address-types"></a>Tipos de endereço

Você pode atribuir os seguintes tipos de endereços IP a uma [configuração de IP](#ip-configurations):

### <a name="private"></a>Privado

Os endereços [IPv4](#ipv4) ou IPv6 privados permitem que uma máquina virtual se comunique com outros recursos em uma rede virtual ou em outras redes conectadas. 

Por padrão, os servidores DHCP do Azure atribuem o endereço IPv4 privado para a [configuração de IP primário](#primary) da interface de rede do Azure para o adaptador de rede no sistema operacional da máquina virtual. A menos que necessário, você nunca deve definir manualmente o endereço IP de uma interface de rede no sistema operacional da máquina virtual.

> [!WARNING]
> Se o endereço IPv4 definido como o endereço IP primário de uma interface de rede dentro do sistema operacional de uma máquina virtual for diferente do endereço IPv4 privado atribuído à configuração de IP primário da interface de rede primária anexada a uma máquina virtual no Azure, você perde a conectividade com a máquina virtual.

Há cenários em que é necessário definir manualmente o endereço IP de uma interface de rede no sistema operacional da máquina virtual. Por exemplo, você deve definir manualmente os endereços IP primários e secundários de um sistema operacional Windows ao adicionar vários endereços IP a uma máquina virtual do Azure. Para uma máquina virtual do Linux, você pode precisar apenas definir manualmente os endereços IP secundários. Consulte [adicionar endereços IP a um sistema operacional da VM](virtual-network-multiple-ip-addresses-portal.md#os-config) para obter detalhes. Se você precisar alterar o endereço atribuído a uma configuração de IP, é recomendável que você:

1. Verifique se a máquina virtual está recebendo um endereço dos servidores DHCP do Azure. Depois de fazer isso, altere a atribuição do endereço IP de volta para DHCP no sistema operacional e reinicie a máquina virtual.
2. Pare (desaloque) a máquina virtual.
3. Altere o endereço IP para a configuração de IP no Azure.
4. Inicie a máquina virtual.
5. [Configure manualmente](virtual-network-multiple-ip-addresses-portal.md#os-config) os endereços IP secundários dentro do sistema operacional (e também o endereço IP primário dentro do Windows) para corresponder ao que você definiu no Azure.

Seguindo as etapas anteriores, o endereço IP privado atribuído à interface de rede no Azure e no sistema operacional de uma máquina virtual permanece o mesmo. Para manter o controle de quais máquinas virtuais em sua assinatura você definiu manualmente os endereços IP em um sistema operacional para o, considere adicionar uma [marca](../azure-resource-manager/management/tag-resources.md) do Azure às máquinas virtuais. Você pode usar "atribuição de endereço IP: estático", por exemplo. Dessa forma, você pode encontrar facilmente as máquinas virtuais em sua assinatura em que você definiu manualmente o endereço IP para dentro do sistema operacional.

Além de habilitar uma máquina virtual para se comunicar com outros recursos dentro das mesmas, ou redes virtuais conectadas, um endereço IP privado também permite que uma máquina virtual comunique a saída para a Internet. As conexões de saída são o endereço de rede de origem traduzido pelo Azure para um endereço IP público imprevisível. Para saber mais sobre a conectividade de Internet de saída do Azure, leia o artigo [conectividade de Internet de saída do Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Não é possível comunicar a entrada para o endereço IP privado de uma máquina virtual da Internet. Se suas conexões de saída exigirem um endereço IP público previsível, associe um recurso de endereço IP público a uma interface de rede.

### <a name="public"></a>Público

Endereços IP públicos atribuídos por meio de um recurso de endereço IP público habilitam a conectividade de entrada para uma máquina virtual da Internet. Conexões de saída para a Internet usam um endereço IP previsível. Consulte [noções básicas sobre conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter detalhes. Você pode atribuir um endereço IP público a uma configuração de IP, mas não é necessário. Se você não atribuir um endereço IP público a uma máquina virtual associando um recurso de endereço IP público, a máquina virtual ainda poderá comunicar a saída à Internet. Nesse caso, o endereço IP privado é o endereço de rede de origem traduzido pelo Azure para um endereço IP público imprevisível. Para saber mais sobre os recursos de endereço IP público, consulte [recurso de endereço IP público](virtual-network-public-ip-address.md).

Há limites para o número de endereços IP públicos e privados que você pode atribuir a uma interface de rede. Para obter detalhes, leia o artigo [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .

> [!NOTE]
> O Azure traduz o endereço IP privado de uma máquina virtual para um endereço IP público. Como resultado, o sistema operacional de uma máquina virtual não está ciente de qualquer endereço IP público atribuído a ele, portanto, não há necessidade de atribuir manualmente um endereço IP público no sistema operacional.

## <a name="assignment-methods"></a>Métodos de atribuição

Os endereços IP públicos e privados são atribuídos usando um dos seguintes métodos de atribuição:

### <a name="dynamic"></a>Dinâmico

Endereços IPv4 e IPv6 privados dinâmicos (opcionalmente) são atribuídos por padrão.

- **Somente público**: o Azure atribui o endereço de um intervalo exclusivo a cada região do Azure. Para saber quais intervalos são atribuídos a cada região, confira [Microsoft Azure intervalos de IP do datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço pode ser alterado quando uma máquina virtual é parada (desalocada) e, em seguida, iniciada novamente. Você não pode atribuir um endereço IPv6 público a uma configuração de IP usando um dos métodos de atribuição.
- **Somente particular**: o Azure reserva os primeiros quatro endereços em cada intervalo de endereços de sub-rede e não atribui os endereços. O Azure atribui o endereço disponível seguinte a um recurso do intervalo de endereços da sub-rede. Por exemplo, se o intervalo de endereços da sub-rede for 10.0.0.0/16 e os endereços 10.0.0.0.4-10.0.0.14 já estiverem atribuídos (.0-.3 está reservado), o Azure atribui o 10.0.0.15 ao recurso. Dinâmico é o método de alocação predefinido. Após a atribuição, os endereços IP dinâmicos só são lançados se uma interface de rede for eliminada, atribuída a uma sub-rede diferente dentro da mesma rede virtual, ou se o método de alocação for alterado para estático e for especificado um endereço IP diferente. Por predefinição, o Azure atribui o endereço atribuído de forma dinâmica anterior como endereço estático quando o utilizador altera o método de alocação de dinâmico para estático. 

### <a name="static"></a>Estático

Você pode (opcionalmente) atribuir um endereço IPv4 ou IPv6 estático público ou privado a uma configuração de IP. Para saber mais sobre como o Azure atribui endereços IPv4 públicos estáticos, consulte [endereços IP públicos](virtual-network-public-ip-address.md).

- **Somente público**: o Azure atribui o endereço de um intervalo exclusivo a cada região do Azure. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure. O endereço não é alterado até que o recurso de endereço IP público ao qual ele está atribuído seja excluído ou o método de atribuição seja alterado para dinâmico. Se o recurso de endereço IP público estiver associado a uma configuração de IP, ele deverá ser dissociado da configuração de IP antes de alterar seu método de atribuição.
- **Somente particular**: você seleciona e atribui um endereço do intervalo de endereços da sub-rede. O endereço que atribuir pode ser qualquer endereço dentro do intervalo de endereços da sub-rede que não seja um dos primeiros quatro endereços no intervalo de endereços da sub-rede e que não esteja atualmente atribuído a qualquer outro recurso na sub-rede. Os endereços estáticos só são lançados se uma interface de rede for eliminada. Se você alterar o método de alocação para estático, o Azure atribuirá dinamicamente o endereço IP dinâmico atribuído anteriormente como o endereço estático, mesmo que o endereço não seja o próximo endereço disponível no intervalo de endereços da sub-rede. O endereço também muda se a interface de rede for atribuída a uma sub-rede diferente dentro da mesma rede virtual, mas, para atribuir a interface de rede a uma sub-rede diferente, tem, primeiro, de alterar o método de alocação de estático para dinâmico. Assim que tiver atribuído a interface de rede a uma sub-rede diferente, pode alterar o método de alocação novamente para estático e atribuir um endereço IP do intervalo de endereços da nova sub-rede.

## <a name="ip-address-versions"></a>Versões de endereço IP

Você pode especificar as seguintes versões ao atribuir endereços:

### <a name="ipv4"></a>IPv4

Cada interface de rede deve ter uma configuração de IP [primário](#primary) com um endereço [IPv4](#ipv4) [privado](#private) atribuído. Você pode adicionar uma ou mais configurações de IP [secundário](#secondary) que têm um IPv4 privado e (opcionalmente) um endereço IP [público](#public) IPv4.

### <a name="ipv6"></a>IPv6

Você pode atribuir zero ou um endereço [IPv6](#ipv6) privado a uma configuração de IP secundário de uma interface de rede. A interface de rede não pode ter nenhuma configuração de IP secundário existente. Cada interface de rede pode ter no máximo um endereço IPv6 privado. Opcionalmente, você pode adicionar um endereço IPv6 público a uma configuração de interface de rede IPv6.

> [!NOTE]
> Embora você possa criar um adaptador de rede com um endereço IPv6 usando o portal, não é possível adicionar um adaptador de rede existente a uma máquina virtual nova ou existente usando o Portal. Use o PowerShell ou o CLI do Azure para criar um adaptador de rede com um endereço IPv6 privado e, em seguida, anexe a interface de rede ao criar uma máquina virtual. Não é possível anexar um adaptador de rede com um endereço IPv6 privado atribuído a ele a uma máquina virtual existente. Você não pode adicionar um endereço IPv6 privado a uma configuração de IP para qualquer interface de rede anexada a uma máquina virtual usando qualquer ferramenta (portal, CLI ou PowerShell).

Você não pode atribuir um endereço IPv6 público a uma configuração de IP primário ou secundário.

## <a name="skus"></a>SKUs

Um endereço IP público é criado com o SKU básico ou Standard. Para obter mais informações sobre as diferenças de SKU, consulte [gerenciar endereços IP públicos](virtual-network-public-ip-address.md).

> [!NOTE]
> Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.

## <a name="next-steps"></a>Passos seguintes
Para criar uma máquina virtual com diferentes configurações de IP, leia os seguintes artigos:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com várias interfaces de rede|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM de NIC única com vários endereços IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM de NIC única com um endereço IPv6 privado (por trás de um Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager modelo](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
