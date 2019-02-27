---
title: Configurar endereços IP para uma interface de rede do Azure | Documentos da Microsoft
description: Saiba como adicionar, alterar e remover endereços IP públicos e privados para uma interface de rede.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 89b311edbae6b5f6679908b5d07b22b402b5c55e
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888071"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Adicionar, alterar ou remover endereços IP para uma interface de rede do Azure

Saiba como adicionar, alterar e remover endereços IP públicos e privados para uma interface de rede. Endereços IP privados atribuídos a uma interface de rede permitem uma máquina virtual para comunicar com outros recursos numa rede virtual do Azure e redes ligadas. Um endereço IP privado também permite a comunicação de saída à Internet através de um endereço IP imprevisível. R [endereço IP público](virtual-network-public-ip-address.md) atribuído a uma rede interface permite comunicação de entrada a uma máquina virtual da Internet. O endereço também permite a comunicação de saída a partir da máquina virtual para a Internet através de um endereço IP previsível. Para obter detalhes, consulte [compreender as ligações de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Se precisa para criar, alterar ou eliminar uma interface de rede, leia os [gerir uma interface de rede](virtual-network-network-interface.md) artigo. Se precisar de interfaces de rede para adicionar ou remover interfaces de rede a partir de uma máquina virtual, leia os [adicionar ou remover interfaces de rede](virtual-network-network-interface-vm.md) artigo.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Se ainda não tiver uma conta do Azure, inscreva-se para uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar o portal, abra https://portal.azure.come inicie sessão com a sua conta do Azure.
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este tutorial requer a versão 2.0.31 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.

A conta iniciar sessão no, ou ligar ao Azure, tem de ser atribuída para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações adequadas listadas na [rede permissões de interface](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Adicionar endereços IP

Pode adicionar tantas [privada](#private) e [público](#public) [IPv4](#ipv4) endereços conforme necessário para uma interface de rede, dentro dos limites listados no [delimitesdoAzure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artigo. Não é possível utilizar o portal para adicionar um endereço IPv6 a uma interface de rede existentes (que pode utilizar o portal para adicionar um endereço IPv6 privado a uma interface de rede, ao criar a interface de rede). Pode utilizar o PowerShell ou a CLI para adicionar um endereço IPv6 privado a um [configuração de IP secundário](#secondary) (desde que não há nenhuma configuração de IP secundária existente) para uma interface de rede existente que não está ligada a uma máquina virtual. Não é possível utilizar qualquer ferramenta para adicionar um endereço IPv6 público a uma interface de rede. Ver [IPv6](#ipv6) para obter detalhes sobre a utilização de endereços IPv6.

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione a interface de rede que pretende adicionar um endereço IPv4 para a partir da lista.
3. Sob **configurações**, selecione **configurações de IP**.
4. Sob **configurações de IP**, selecione **+ adicionar**.
5. Especifique o seguinte, em seguida, selecione **OK**:

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Name|Sim|Tem de ser exclusivo para a interface de rede|
   |Type|Sim|Uma vez que estiver a adicionar uma configuração de IP a uma interface de rede existente, e cada interface de rede tem de ter uma [primário](#primary) configuração de IP, sua única opção é **secundário**.|
   |Método de atribuição de endereço IP privado|Sim|[**Dinâmica**](#dynamic): O Azure atribui o endereço seguinte disponível para o intervalo de endereços de sub-rede que a interface de rede é implementada no. [**Estática**](#static): Atribuir um endereço não utilizado para o intervalo de endereços de sub-rede que a interface de rede é implementada no.|
   |Endereço IP público|Não|**Desativado:** Nenhum recurso de endereço IP público está atualmente associado à configuração de IP. **Ativado:** Selecione um endereço IP público IPv4 existente ou crie um novo. Para saber como criar um endereço IP público, leia os [endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address) artigo.|
6. Adicionar manualmente os endereços IP privados secundários para o sistema de operativo da máquina virtual ao concluir as instruções a [atribuir vários endereços IP aos sistemas operacionais de máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config) artigo. Ver [privada](#private) endereços IP para considerações especiais antes de adicionar manualmente os endereços IP para um sistema de operativo da máquina virtual. Não adicione quaisquer endereços IP públicos para o sistema de operativo da máquina virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Alterar as definições de endereço IP

Pode precisar para alterar o método de atribuição de um endereço IPv4, alterar o endereço IPv4 estático ou alterar o endereço IP público atribuído a uma interface de rede. Se estiver a alterar o endereço IPv4 privado de uma configuração de IP secundário associado com uma interface de rede secundárias numa máquina virtual (Saiba mais sobre [interfaces de rede primária e secundária](virtual-network-network-interface-vm.md)), coloque a máquina virtual no estado parado (desalocada) antes de concluir os seguintes passos:

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione a interface de rede que pretende ver ou alterar as definições de endereço IP para a partir da lista.
3. Sob **configurações**, selecione **configurações de IP**.
4. Selecione a configuração de IP que pretende modificar na lista.
5. Alterar as definições, conforme desejado, utilizando as informações sobre as definições no passo 5 da [adicionar uma configuração de IP](#add-ip-addresses).
6. Selecione **Guardar**.

>[!NOTE]
>Se a interface de rede principal tem várias configurações de IP e alterar o endereço IP privado da configuração de IP primária, precisa de reatribuir manualmente os endereços IP primários e secundários para a interface de rede dentro do Windows (não necessário para Linux) . Atribuir manualmente os endereços IP para uma interface de rede dentro de um sistema operativo, consulte [atribuir vários endereços IP para máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config). Para obter considerações especiais antes de adicionar manualmente os endereços IP para um sistema de operativo da máquina virtual, consulte [privada](#private) endereços IP. Não adicione quaisquer endereços IP públicos para o sistema de operativo da máquina virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Remover endereços IP

Pode remover [privada](#private) e [público](#public) endereços IP a partir de uma interface de rede, mas uma interface de rede sempre tem de ter pelo menos um endereço IPv4 privado atribuído a ele.

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione a interface de rede que pretende remover os endereços IP da lista.
3. Sob **configurações**, selecione **configurações de IP**.
4. Direito-selecione um [secundário](#secondary) configuração de IP (não é possível eliminar o [primário](#primary) configuração) que pretende eliminar, selecione **eliminar**, em seguida, selecione  **Sim**, para confirmar a eliminação. Se a configuração tinha um recurso de endereço IP público associado a si, o recurso é desassociado da configuração de IP, mas o recurso não é eliminado.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Configurações de IP

[Privada](#private) e (opcionalmente) [pública](#public) endereços IP são atribuídos a uma ou mais configurações de IP atribuídas a uma interface de rede. Existem dois tipos de configurações de IP:

### <a name="primary"></a>Primária

Cada interface de rede é atribuído uma configuração de IP primária. Uma configuração de IP primária:

- Tem um [privada](#private) [IPv4](#ipv4) endereço atribuído ao mesmo. Não é possível atribuir uma privada [IPv6](#ipv6) endereço para uma configuração de IP primária.
- Também pode ter uma [público](#public) endereço IPv4 atribuído a ele. Não é possível atribuir um endereço IPv6 público a uma configuração de IP primária ou secundária. No entanto, pode atribuir um endereço IPv6 público a um balanceador de carga do Azure, que pode balancear carga de tráfego para o endereço de IPv6 privado de uma máquina virtual. Para obter mais informações, consulte [detalhes e limitações do IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Secundária

Além de uma configuração de IP primária, uma interface de rede pode ter zero ou mais configurações de IP secundárias atribuídas ao mesmo. Uma configuração de IP secundária:

- Tem de ter um endereço IPv4 ou IPv6 privado atribuído ao mesmo. Se o endereço IPv6, a interface de rede só pode ter uma configuração de IP secundária. Se o endereço IPv4, a interface de rede pode ter várias configurações de IP secundárias atribuídas ao mesmo. Para saber mais sobre quantos endereços de IPv4 privados e públicos podem ser atribuídos a uma interface de rede, consulte a [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artigo.
- Pode também ter um endereço IPv4 público atribuído ao mesmo, se o endereço IP privado é IPv4. Se o endereço IP privado é o IPv6, não é possível atribuir um endereço IPv4 ou IPv6 público à configuração de IP. Atribuir vários endereços IP a uma interface de rede é útil em cenários, tais como:
  - Alojar vários Web Sites ou serviços com diferentes endereços IP e certificados SSL num único servidor.
  - Uma máquina virtual que serve como uma aplicação virtual de rede, como uma firewall ou Balanceador de carga.
  - A capacidade de adicionar qualquer um dos endereços IPv4 privados para qualquer uma das interfaces de rede a um conjunto de back-end de Balanceador de carga do Azure. No passado, apenas o endereço IPv4 primário para a interface de rede primária foi possível adicionar a um conjunto de back-end. Para saber mais sobre como balancear a carga de várias configurações de IPv4, consulte a [várias configurações de IP de balanceamento de carga](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo. 
  - A capacidade de carregar de balanceamento de um endereço de IPv6 atribuído a uma interface de rede. Para saber mais sobre como carregar saldo para um endereço IPv6 privado, consulte a [endereços IPv6 de balanceamento de carga](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo.

## <a name="address-types"></a>Tipos de endereços

Pode atribuir os seguintes tipos de endereços IP para uma [configuração do IP](#ip-configurations):

### <a name="private"></a>Privado

Privada [IPv4](#ipv4) endereços ativar uma máquina virtual para comunicar com outros recursos numa rede virtual ou a outras redes ligadas. Uma máquina virtual não pode ser comunicada a entrada, nem pode a máquina virtual comunicar saída com uma privada [IPv6](#ipv6) endereço, com uma exceção. Uma máquina virtual podem comunicar com o Balanceador de carga do Azure com um endereço IPv6. Para obter mais informações, consulte [detalhes e limitações do IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

Por predefinição, os servidores DHCP do Azure atribuir o endereço IPv4 privado para o [configuração de IP primária](#primary) da interface de rede do Azure para a interface de rede dentro do sistema de operativo da máquina virtual. A menos que necessário, nunca manualmente deve definir o endereço IP de uma interface de rede no sistema de operativo da máquina virtual.

> [!WARNING]
> Se o endereço IPv4 definido como o endereço IP primário de uma interface de rede no sistema operativo de uma máquina virtual está nunca diferente do que o endereço IPv4 privado atribuído a configuração de IP principal da interface de rede principal ligado a uma máquina virtual no Azure, perder a conectividade para a máquina virtual.

Existem cenários em que é necessário definir o endereço IP de uma interface de rede no sistema de operativo da máquina virtual manualmente. Por exemplo, tem de definir manualmente os endereços IP primários e secundários de um sistema operativo do Windows quando adicionar vários endereços IP a uma máquina virtual do Azure. Para uma máquina virtual do Linux, apenas terá de definir manualmente os endereços IP secundários. Ver [endereços IP de adicionar a um sistema de operativo VM](virtual-network-multiple-ip-addresses-portal.md#os-config) para obter detalhes. Se alguma vez precisar de alterar o endereço atribuído a uma configuração de IP, é recomendado que:

1. Certifique-se de que a máquina virtual está a receber um endereço dos servidores DHCP do Azure. Assim que tiver, alterar a atribuição do endereço IP de volta para o DHCP no sistema operativo e reinicie a máquina virtual.
2. Pare (desaloque) a máquina virtual.
3. Altere o endereço IP para a configuração de IP no Azure.
4. Inicia a máquina virtual.
5. [Configurar manualmente](virtual-network-multiple-ip-addresses-portal.md#os-config) os endereços IP secundários dentro do sistema operativo (e também o endereço IP primário dentro do Windows) para corresponder ao definido no Azure.

Ao seguir os passos anteriores, o endereço IP privado atribuído à interface de rede no Azure e no sistema operativo de uma máquina virtual, permanecem os mesmos. Para controlar quais as máquinas virtuais na sua subscrição que tiver definido manualmente a endereços IP dentro de um sistema operativo para, considere adicionar do Azure [marca](../azure-resource-manager/resource-group-using-tags.md) às máquinas virtuais. Pode utilizar "atribuição de endereços IP: "Estáticas", por exemplo. Dessa forma, pode encontrar facilmente as máquinas virtuais na sua subscrição que tiver definido manualmente o endereço IP para o sistema operativo.

Além de permitir a uma máquina virtual para comunicar com outros recursos dentro dos mesmo, ou ligados redes virtuais, um endereço IP privado também permite que uma máquina virtual de comunicações de saída à Internet. As ligações de saída são traduzido pelo Azure para um endereço IP público imprevisível endereço de rede de origem. Para saber mais sobre a conectividade de Internet de saída do Azure, leia os [conectividade de Internet de saída do Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo. Não pode comunicar internamente com endereço IP privado de uma máquina virtual da Internet. Se as suas ligações de saída exigirem um endereço IP público previsível, associe um recurso de endereço IP público a uma interface de rede.

### <a name="public"></a>Público

Endereços IP públicos atribuídos por meio de um recurso de endereço IP público ativar conectividade de entrada a uma máquina virtual a partir da Internet. As ligações de saída à Internet, utilize um endereço IP previsível. Ver [compreender as ligações de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter detalhes. Pode atribuir um endereço IP público a uma configuração de IP, mas não é obrigado a. Se não atribuir um endereço IP público a uma máquina virtual através da associação de um recurso de endereço IP público, a máquina virtual continua a poder comunicar com a Internet à saída. Neste caso, o endereço IP privado é o endereço de rede de origem traduzido pelo Azure para um endereço IP público imprevisível. Para saber mais sobre os recursos de endereço IP público, veja [recurso de endereço IP público](virtual-network-public-ip-address.md).

Existem limites ao número de endereços IP públicos e privados que pode atribuir a uma interface de rede. Para obter detalhes, leia os [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artigo.

> [!NOTE]
> Azure traduz-se o endereço IP privado de uma máquina virtual para um endereço IP público. Como resultado, o sistema operativo de uma máquina virtual tem conhecimento de qualquer endereço IP público atribuído ao mesmo, pelo que não é necessário atribuir alguma vez manualmente um endereço IP público no sistema operativo.

## <a name="assignment-methods"></a>Métodos de atribuição

Endereços IP públicos e privados são atribuídos através de um dos seguintes métodos de atribuição:

### <a name="dynamic"></a>Dinâmica

IPv4 privada dinâmico e IPv6 (opcionalmente) endereços são atribuídos por predefinição.

- **Público apenas**: Azure atribui o endereço a partir de um intervalo exclusivo para cada região do Azure. Para saber quais os intervalos são atribuídos a cada região, veja [intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). O endereço pode alterar quando uma máquina virtual está parada (desalocada), em seguida, iniciado novamente. Não é possível atribuir um endereço IPv6 público a uma configuração de IP utilizando um dos métodos de atribuição.
- **Apenas privada**: O Azure reserva os primeiros quatro endereços em cada intervalo de endereços da sub-rede e não atribui os endereços. O Azure atribui o endereço disponível seguinte a um recurso do intervalo de endereços da sub-rede. Por exemplo, se o intervalo de endereços da sub-rede for 10.0.0.0/16 e os endereços 10.0.0.0.4-10.0.0.14 já estiverem atribuídos (.0-.3 está reservado), o Azure atribui o 10.0.0.15 ao recurso. Dinâmico é o método de alocação predefinido. Após a atribuição, os endereços IP dinâmicos só são lançados se uma interface de rede for eliminada, atribuída a uma sub-rede diferente dentro da mesma rede virtual, ou se o método de alocação for alterado para estático e for especificado um endereço IP diferente. Por predefinição, o Azure atribui o endereço atribuído de forma dinâmica anterior como endereço estático quando o utilizador altera o método de alocação de dinâmico para estático. Só pode atribuir um endereço IPv6 privado usando o método de atribuição dinâmica.

### <a name="static"></a>Estático

(Opcionalmente) pode atribuir um endereço de IPv4 estático público ou privado para uma configuração de IP. Não é possível atribuir um endereço de IPv6 estático público ou privado para uma configuração de IP. Para saber mais sobre como o Azure atribui endereços IPv4 públicos estáticos, consulte [endereços IP públicos](virtual-network-public-ip-address.md).

- **Público apenas**: Azure atribui o endereço a partir de um intervalo exclusivo para cada região do Azure. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure. O endereço não é alterado até que o recurso de endereço IP público que está atribuída a é eliminado ou o método de atribuição é alterado para dinâmico. Se o recurso de endereço IP público está associado a uma configuração de IP, tem de ser desassociado da configuração de IP antes de alterar o seu método de atribuição.
- **Apenas privada**: Pode selecionar e atribuir um endereço do intervalo de endereços da sub-rede. O endereço que atribuir pode ser qualquer endereço dentro do intervalo de endereços da sub-rede que não seja um dos primeiros quatro endereços no intervalo de endereços da sub-rede e que não esteja atualmente atribuído a qualquer outro recurso na sub-rede. Os endereços estáticos só são lançados se uma interface de rede for eliminada. Se o utilizador alterar o método de alocação para estático, o Azure atribui de forma dinâmica o endereço IP estático atribuído anteriormente como endereço dinâmico, mesmo que o endereço não seja o endereço seguinte disponível do intervalo de endereços da sub-rede. O endereço também muda se a interface de rede for atribuída a uma sub-rede diferente dentro da mesma rede virtual, mas, para atribuir a interface de rede a uma sub-rede diferente, tem, primeiro, de alterar o método de alocação de estático para dinâmico. Assim que tiver atribuído a interface de rede a uma sub-rede diferente, pode alterar o método de alocação novamente para estático e atribuir um endereço IP do intervalo de endereços da nova sub-rede.

## <a name="ip-address-versions"></a>Versões de endereços IP

Ao atribuir endereços, pode especificar as seguintes versões:

### <a name="ipv4"></a>IPv4

Cada interface de rede tem de ter um [primário](#primary) configuração de IP com um atribuído [privada](#private) [IPv4](#ipv4) endereço. Pode adicionar um ou mais [secundário](#secondary) configurações de IP que tenham, cada um IPv4 privada e (opcionalmente) uma IPv4 [público](#public) endereço IP.

### <a name="ipv6"></a>IPv6

Pode atribuir zero ou um privada [IPv6](#ipv6) endereço para uma configuração de IP secundário de uma interface de rede. A interface de rede não pode ter quaisquer configurações de IP secundárias existentes. Não é possível adicionar uma configuração de IP com um endereço IPv6 com o portal. Utilize o PowerShell ou a CLI para adicionar uma configuração de IP com um endereço IPv6 privado a uma interface de rede existente. A interface de rede não pode ser ligada a uma VM existente.

> [!NOTE]
> Embora possa criar uma interface de rede com um endereço IPv6 com o portal, não é possível adicionar uma interface de rede existente a uma máquina virtual nova ou existente, com o portal. Utilizar o PowerShell ou a CLI do Azure para criar uma interface de rede com um endereço IPv6 privado, em seguida, anexar a interface de rede durante a criação de uma máquina virtual. Não é possível anexar uma interface de rede com um endereço IPv6 privado atribuído a uma máquina virtual existente. Não é possível adicionar um endereço IPv6 privado para uma configuração de IP para qualquer interface de rede ligado a uma máquina virtual através de quaisquer ferramentas (portal, da CLI ou PowerShell).

Não é possível atribuir um endereço IPv6 público a uma configuração de IP primária ou secundária.

## <a name="skus"></a>SKUs

Um endereço IP público é criado com o SKU básico ou padrão. Para obter mais informações sobre as diferenças SKU, consulte [gerir endereços IP públicos](virtual-network-public-ip-address.md).

> [!NOTE]
> Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.

## <a name="next-steps"></a>Passos Seguintes
Para criar uma máquina virtual com diferentes configurações de IP, leia os artigos seguintes:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com várias interfaces de rede|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM de NIC único com vários endereços de IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM de NIC único com um endereço IPv6 privado (por trás de um balanceador de carga do Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modelo Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
