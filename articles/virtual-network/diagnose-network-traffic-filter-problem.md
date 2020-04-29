---
title: Diagnosticar um problema de filtro de tráfego de rede de máquinas virtuais Microsoft Docs
description: Aprenda a diagnosticar um problema de filtro de tráfego de rede de máquinas virtuais visualizando as regras de segurança eficazes para uma máquina virtual.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: 6939ea2497a9f12321e1a6dfb9bf9fbb353bc7db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240768"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnosticar um problema de filtro de tráfego de rede de máquinas virtuais

Neste artigo, aprende-se a diagnosticar um problema de filtro de tráfego de rede, visualizando as regras de segurança do grupo de segurança da rede (NSG) que são eficazes para uma máquina virtual (VM).

As NSGs permitem controlar os tipos de tráfego que fluem dentro e fora de um VM. Pode associar um NSG a uma subrede numa rede virtual Azure, uma interface de rede ligada a um VM, ou ambos. As regras de segurança efetivas aplicadas a uma interface de rede são uma agregação das regras existentes no NSG associadas a uma interface de rede, e a subrede em que a interface de rede está. Regras em nsgs diferentes podem por vezes entrar em conflito entre si e afetar a conectividade da rede de uma VM. Pode ver todas as regras de segurança eficazes dos NSGs que são aplicadas nas interfaces de rede do seu VM. Se não estiver familiarizado com conceitos de rede virtual, interface de rede ou NSG, consulte [a visão geral da rede virtual,](virtual-networks-overview.md)interface de [rede](virtual-network-network-interface.md)e visão geral dos grupos de segurança [da rede.](security-overview.md)

## <a name="scenario"></a>Cenário

Tenta-se ligar-se a um VM através da porta 80 da internet, mas a ligação falha. Para determinar por que não consegue aceder à porta 80 a partir da Internet, pode ver as regras de segurança eficazes para uma interface de rede utilizando o [portal](#diagnose-using-azure-portal)Azure, [PowerShell](#diagnose-using-powershell)ou o [Azure CLI](#diagnose-using-azure-cli).

Os passos que se seguem assumem que tem um VM existente para ver as regras de segurança eficazes para. Se não tiver um VM existente, implemente primeiro um [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM para completar as tarefas neste artigo com. Os exemplos deste artigo são para um VM chamado *myVM* com uma interface de rede chamada *myVVVMNic*. O VM e a interface de rede estão num grupo de recursos chamado *myResourceGroup*, e estão na região *leste dos EUA.* Mude os valores nos passos, se for caso disso, para o VM para o que está a diagnosticar o problema.

## <a name="diagnose-using-azure-portal"></a>Diagnosticar usando o portal Azure

1. Inicie sessão no [portal](https://portal.azure.com) Azure com uma conta Azure que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).
2. No topo do portal Azure, insira o nome do VM na caixa de pesquisa. Quando o nome do VM aparecer nos resultados da pesquisa, selecione-o.
3. Em **DEFINIÇÕES**, selecione **Networking**, como mostrado na seguinte imagem:

   ![Ver regras de segurança](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   As regras que vê listadas na imagem anterior são para uma interface de rede chamada **myVMVMNic**. Verifica que existem regras de entrada na **interface** da rede de dois grupos diferentes de segurança de rede:
   
   - **mySubnetNSG**: Associado à sub-rede em que a interface de rede está.
   - **myVMNSG**: Associado à interface de rede no VM denominado **myVMVMNic**.

   A regra chamada **DenyAllInBound** é o que impede a comunicação de entrada para o VM sobre a porta 80, a partir da internet, como descrito no [cenário](#scenario). A regra lista *0.0.0.0/0* para a **SOURCE,** que inclui a internet. Nenhuma outra regra com uma prioridade mais elevada (número inferior) permite a entrada da porta 80. Para permitir a entrada da porta 80 no VM a partir da internet, consulte [Resolver um problema](#resolve-a-problem). Para saber mais sobre as regras de segurança e como o Azure as aplica, consulte os grupos de [segurança da Rede.](security-overview.md)

   Na parte inferior da imagem, consulte também regras de **porta de saída**. Segundo estas são as regras de saída da porta para a interface da rede. Embora a imagem apenas mostre quatro regras de entrada para cada NSG, os seus NSGs podem ter muito mais do que quatro regras. Na imagem, **vê-se** a Rede Virtual em **FONTE** e **DESTINO** e **AzureLoadBalancer** em **SOURCE**. **VirtualNetwork** e **AzureLoadBalancer** são etiquetas de [serviço.](security-overview.md#service-tags) As etiquetas de serviço representam um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade para a criação de regras de segurança.

4. Certifique-se de que o VM está em estado de funcionamento e, em seguida, selecione regras de **segurança eficazes,** como mostra a imagem anterior, para ver as regras de segurança efetivas, mostradas na imagem seguinte:

   ![Ver regras de segurança eficazes](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   As regras listadas são as mesmas que viu no passo 3, embora existam separadores diferentes para o NSG associados à interface de rede e à sub-rede. Como pode ver na imagem, apenas as primeiras 50 regras são mostradas. Para descarregar um ficheiro .csv que contenha todas as regras, selecione **Download**.

   Para ver quais os prefixos que cada etiqueta de serviço representa, selecione uma regra, como a regra denominada **AllowAzureLoadBalancerInbound**. A imagem seguinte mostra os prefixos da etiqueta de serviço **AzureLoadBalancer:**

   ![Ver regras de segurança eficazes](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Embora a etiqueta de serviço **AzureLoadBalancer** represente apenas um prefixo, outras etiquetas de serviço representam vários prefixos.

5. Os passos anteriores mostraram as regras de segurança de uma interface de rede chamada **myVVVMNic,** mas também viu uma interface de rede chamada **myVMVMNic2** em algumas das imagens anteriores. O VM neste exemplo tem duas interfaces de rede ligadas ao mesmo. As regras de segurança eficazes podem ser diferentes para cada interface de rede.

   Para ver as regras da interface de rede **myVMVMNic2,** selecione-a. Como mostra a imagem que se segue, a interface de rede tem as mesmas regras associadas à sua subnet a interface de rede **myVMVMNic,** porque ambas as interfaces de rede estão na mesma sub-rede. Quando associa um NSG a uma subneta, as suas regras são aplicadas a todas as interfaces de rede na subrede.

   ![Ver regras de segurança](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Ao contrário da interface de rede **myVVVMNic,** a interface de rede **myVVVMNic2** não tem um grupo de segurança de rede associado a ele. Cada interface de rede e sub-rede podem ter zero, ou um, NSG associado a ela. O NSG associado a cada interface de rede ou sub-rede pode ser o mesmo, ou diferente. Pode associar o mesmo grupo de segurança de rede ao maior número de interfaces e subredes de rede que quiser.

Embora regras de segurança eficazes tenham sido vistas através do VM, você também pode ver regras de segurança eficazes através de um indivíduo:
- **Interface de rede**: Aprenda a [visualizar uma interface](virtual-network-network-interface.md#view-network-interface-settings)de rede .
- **NSG:** Aprenda a [ver um NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnosticar usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que se seguem na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando a PowerShell a partir do seu computador. A Casca de Nuvem Azure é uma concha interativa gratuita. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do seu computador, precisa do módulo Azure PowerShell, versão 1.0.0 ou posterior. Navere `Get-Module -ListAvailable Az` no seu computador, para encontrar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar a PowerShell `Connect-AzAccount` localmente, também precisa de correr para entrar no Azure com uma conta que tenha as [permissões necessárias].](virtual-network-network-interface.md#permissions)

Obtenha as regras de segurança eficazes para uma interface de rede com [get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). O exemplo seguinte obtém as regras de segurança eficazes para uma interface de rede chamada *myVMVMNic,* que está num grupo de recursos chamado *myResourceGroup:*

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

A saída é devolvida em formato Json. Para compreender a saída, consulte [a saída de comando .](#interpret-command-output)
A saída só é devolvida se um NSG estiver associado à interface de rede, a sub-rede em que a interface de rede está, ou ambos. O VM deve estar no estado de corrida. Um VM pode ter várias interfaces de rede com NSGs diferentes aplicados. Quando resolver problemas, execute o comando para cada interface de rede.

Se ainda tiver um problema de conectividade, consulte [um diagnóstico](#additional-diagnosis) adicional e [considerações.](#considerations)

Se não sabe o nome de uma interface de rede, mas sabe o nome do VM a interface de rede a que a interface de rede está ligada, os seguintes comandos devolvem os IDs de todas as interfaces de rede anexadas a um VM:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Recebe uma saída semelhante ao seguinte exemplo:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Na saída anterior, o nome da interface de rede é *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosticar usando Azure CLI

Se utilizar comandos de interface de linha de comando Azure (CLI) para completar tarefas neste artigo, execute os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este artigo requer a versão Azure CLI 2.0.32 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI `az login` localmente, também precisa de correr e entrar no Azure com uma conta que tenha as [permissões necessárias.](virtual-network-network-interface.md#permissions)

Obtenha as regras de segurança eficazes para uma interface de rede com [az rede nic lista-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). O exemplo seguinte obtém as regras de segurança eficazes para uma interface de rede chamada *myVMVMNic* que está num grupo de recursos chamado *myResourceGroup:*

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

A saída é devolvida em formato Json. Para compreender a saída, consulte [a saída de comando .](#interpret-command-output)
A saída só é devolvida se um NSG estiver associado à interface de rede, a sub-rede em que a interface de rede está, ou ambos. O VM deve estar no estado de corrida. Um VM pode ter várias interfaces de rede com NSGs diferentes aplicados. Quando resolver problemas, execute o comando para cada interface de rede.

Se ainda tiver um problema de conectividade, consulte [um diagnóstico](#additional-diagnosis) adicional e [considerações.](#considerations)

Se não sabe o nome de uma interface de rede, mas sabe o nome do VM a interface de rede a que a interface de rede está ligada, os seguintes comandos devolvem os IDs de todas as interfaces de rede anexadas a um VM:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

Dentro da saída devolvida, vê informações semelhantes ao seguinte exemplo:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

Na saída anterior, o nome da interface de rede é *interface myVMVMNic*.

## <a name="interpret-command-output"></a>Interpretar a saída de comando

Independentemente de ter usado o [PowerShell](#diagnose-using-powershell), ou o [Azure CLI](#diagnose-using-azure-cli) para diagnosticar o problema, recebe uma saída que contém as seguintes informações:

- **NetworkSecurityGroup**: O ID do grupo de segurança da rede.
- **Associação**: Se o grupo de segurança da rede está associado a uma Interface de *Rede* ou *subnet*. Se um NSG estiver associado a ambos, a saída é devolvida com **NetworkSecurityGroup**, **Association**, e **EffectiveSecurityRules**, para cada NSG. Se o NSG estiver associado ou dissociado imediatamente antes de executar o comando para ver as regras de segurança eficazes, poderá ter de esperar alguns segundos para que a alteração se reflita na saída do comando.
- **Regras de Segurança Eficazes**: Uma explicação de cada propriedade é detalhada em [Criar uma regra](manage-network-security-group.md#create-a-security-rule)de segurança . Os nomes de regras prefaciados com regras de *segurança padrão/* são regras de segurança padrão que existem em cada NSG. Nomes de regras prefaciados com *segurançaRegras/* são regras que criou. As regras que especificam uma [etiqueta de serviço](security-overview.md#service-tags), como **internet,** **VirtualNetwork,** e **AzureLoadBalancer** para as propriedades **DestinationAddressPrefix** ou **sourceAddressPrefix,** também têm valores para a propriedade **expandidaDestinationAddressPrefix.** A propriedade **expandidaDestinationAddressPrefix** lista todos os prefixos de endereço representados pela etiqueta de serviço.

Se vir regras duplicadas listadas na saída, é porque um NSG está associado tanto à interface de rede como à sub-rede. Ambos os NSGs têm as mesmas regras padrão, e podem ter regras duplicadas adicionais, se você criou as suas próprias regras que são as mesmas em ambos os NSGs.

A regra denominada **defaultSecurityRules/DenyAllInBound** é o que impede a comunicação de entrada para o VM sobre a porta 80, a partir da internet, conforme descrito no [cenário](#scenario). Nenhuma outra regra com uma prioridade maior (número inferior) permite a entrada da porta 80 a partir da internet.

## <a name="resolve-a-problem"></a>Resolver um problema

Quer utilize o [portal](#diagnose-using-azure-portal)Azure, [PowerShell,](#diagnose-using-powershell)ou o [Azure CLI](#diagnose-using-azure-cli) para diagnosticar o problema apresentado no [cenário](#scenario) neste artigo, a solução é criar uma regra de segurança de rede com as seguintes propriedades:

| Propriedade                | Valor                                                                              |
|---------                |---------                                                                           |
| Origem                  | Qualquer                                                                                |
| Intervalo de portas de origem      | Qualquer                                                                                |
| Destino             | O endereço IP do VM, uma gama de endereços IP, ou todos os endereços na sub-rede. |
| Intervalos de portas de destino | 80                                                                                 |
| Protocolo                | TCP                                                                                |
| Ação                  | Permitir                                                                              |
| Prioridade                | 100                                                                                |
| Nome                    | Permitir-HTTP-All                                                                     |

Depois de criar a regra, a porta 80 é permitida a entrada a partir da internet, porque a prioridade da regra é maior do que a regra de segurança padrão chamada *DenyAllInBound*, que nega o tráfego. Aprenda a criar uma regra de [segurança.](manage-network-security-group.md#create-a-security-rule) Se diferentes NSGs estiverem associados tanto à interface da rede como à sub-rede, deve criar a mesma regra em ambos os NSGs.

Quando o Azure processa o tráfego de entrada, processa as regras no NSG associadas à sub-rede (se houver um NSG associado), e depois processa as regras no NSG associadas à interface da rede. Se houver um NSG associado à interface de rede e à sub-rede, a porta deve estar aberta em ambas as NSGs, para que o tráfego chegue ao VM. Para aliviar os problemas de administração e comunicação, recomendamos que associe um NSG a uma subnet, em vez de interfaces individuais de rede. Se os VMs dentro de uma subnet precisarem de regras de segurança diferentes, pode fazer as interfaces de rede membros de um grupo de segurança de aplicações (ASG), e especificar um ASG como fonte e destino de uma regra de segurança. Saiba mais sobre grupos de segurança de [aplicações.](security-overview.md#application-security-groups)

Se ainda tiver problemas de comunicação, consulte [considerações](#considerations) e diagnóstico adicional.

## <a name="considerations"></a>Considerações

Considere os seguintes pontos ao resolver problemas de conectividade:

* As regras de segurança predefinidas bloqueiam o acesso à entrada a partir da internet e apenas permitem o tráfego de entrada a partir da rede virtual. Para permitir o tráfego de entrada a partir da Internet, adicione regras de segurança com uma prioridade maior do que as regras padrão. Saiba mais sobre [as regras](security-overview.md#default-security-rules)de segurança padrão , ou como adicionar uma regra de [segurança](manage-network-security-group.md#create-a-security-rule).
* Se tiver espreitado redes virtuais, por padrão, a etiqueta de serviço **VIRTUAL_NETWORK** expande-se automaticamente para incluir prefixos para redes virtuais com pares. Para resolver problemas relacionados com o peering virtual da rede, pode ver os prefixos na lista **DePrefixo expandido.** Saiba mais sobre [o peering de rede virtual](virtual-network-peering-overview.md) e [etiquetas de serviço.](security-overview.md#service-tags)
* Regras de segurança eficazes só são mostradas para uma interface de rede se houver um NSG associado à interface de rede do VM e, ou, sub-rede, e se o VM estiver em estado de funcionamento.
* Se não existirem NSGs associados à interface ou sub-rede de rede, e tiver um [endereço IP público](virtual-network-public-ip-address.md) atribuído a um VM, todas as portas estão abertas para acesso de entrada e saída a qualquer lugar. Se o VM tiver um endereço IP público, recomendamos a aplicação de um NSG na sub-rede da interface de rede.

## <a name="additional-diagnosis"></a>Diagnóstico adicional

* Para efelo um teste rápido para determinar se o tráfego é permitido de ou para um VM, utilize a capacidade de [verificação](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) de fluxo IP do Observador da Rede Azure. A verificação do fluxo IP diz-lhe se o tráfego é permitido ou negado. Se negado, a verificação do fluxo IP diz-lhe qual a regra de segurança que está a negar o tráfego.
* Se não houver regras de segurança que causem o fracasso da conectividade da rede de um VM, o problema pode dever-se a:
  * Software de firewall em execução dentro do sistema operativo da VM
  * Rotas configuradas para aparelhos virtuais ou tráfego no local. O tráfego de internet pode ser redirecionado para a sua rede no local através [de túneis forçados](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se forçar o tráfego de internet do túnel a um aparelho virtual, ou no local, poderá não conseguir ligar-se ao VM a partir da internet. Para aprender a diagnosticar problemas de rota que possam impedir o fluxo de tráfego para fora do VM, consulte [Diagnosticar um problema de encaminhamento](diagnose-network-routing-problem.md)de tráfego de rede de máquinas virtuais .

## <a name="next-steps"></a>Passos seguintes

- Conheça todas as tarefas, propriedades e configurações para um grupo de segurança de [rede](manage-network-security-group.md#work-with-network-security-groups) e regras de [segurança.](manage-network-security-group.md#work-with-security-rules)
- Conheça [as regras](security-overview.md#default-security-rules)de segurança padrão , etiquetas de [serviço,](security-overview.md#service-tags)e [como o Azure processa as regras](security-overview.md#network-security-groups) de segurança para o tráfego de entrada e saída para um VM.
