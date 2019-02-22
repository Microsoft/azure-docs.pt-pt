---
title: Diagnosticar um problema de filtragem de tráfego de rede de máquina virtual | Documentos da Microsoft
description: Saiba como diagnosticar um problema de filtragem de tráfego de rede de máquina virtual ao visualizar as regras de segurança efetivas para uma máquina virtual.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: jdial
ms.openlocfilehash: fecab4dc3a0674b0b64638676f4538af145b52ac
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652650"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnosticar um problema de filtragem de tráfego de rede de máquina virtual

Neste artigo, saiba como diagnosticar um problema de filtragem de tráfego de rede ao visualizar as regras de segurança de grupo (NSG) de segurança de rede que estão em vigor a partir de uma máquina virtual (VM).

Os NSGs permitem controlar os tipos de tráfego esse fluxo dentro e fora de uma VM. Pode associar um NSG a uma sub-rede numa rede virtual do Azure, uma interface de rede ligada a uma VM, ou ambos. As regras de segurança efetivas aplicadas a uma interface de rede são uma agregação das regras que existem no NSG associado a uma interface de rede e a sub-rede a interface de rede está numa. As regras em NSGs diferentes, às vezes, podem entrar em conflito entre si e afetar a conectividade de rede de uma VM. Pode ver todas as regras de segurança efetivas de NSGs que são aplicadas em interfaces de rede da VM. Se não estiver familiarizado com a rede virtual, a interface de rede ou a conceitos NSG, consulte [descrição geral de rede Virtual](virtual-networks-overview.md), [interface de rede](virtual-network-network-interface.md), e [descrição geral dosdegruposdesegurançaderede](security-overview.md).

## <a name="scenario"></a>Cenário

Tentar estabelecer ligação a uma VM através da porta 80 a partir da internet, mas a ligação falha. Para determinar por que não é possível aceder a porta 80 a partir da Internet, pode ver as regras de segurança efetivas para interfaces de rede através do Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), ou o [da CLI do Azure](#diagnose-using-azure-cli).

Os passos que se seguem partem do princípio de que tem uma VM existente para ver as regras de segurança efetivas para. Se não tiver uma VM existente, primeiro implementar um [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM para concluir as tarefas neste artigo com. Os exemplos neste artigo são para uma VM com o nome *myVM* com uma interface de rede com o nome *myVMVMNic*. A interface de rede e a VM estiver num grupo de recursos com o nome *myResourceGroup*e o *E.U.A. Leste* região. Altere os valores nos passos, conforme apropriado, para diagnosticar o problema para a VM.

## <a name="diagnose-using-azure-portal"></a>Diagnosticar através do portal do Azure

1. Inicie sessão no Azure [portal](https://portal.azure.com) com uma conta do Azure com o [as permissões necessárias](virtual-network-network-interface.md#permissions).
2. Na parte superior do portal do Azure, introduza o nome da VM na caixa de pesquisa. Quando o nome da VM for apresentada nos resultados da pesquisa, selecione-o.
3. Sob **configurações**, selecione **Networking**, conforme mostrado na imagem seguinte:

   ![Ver regras de segurança](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   As regras que vê listadas na imagem anterior são para uma interface de rede com o nome **myVMVMNic**. Ver o que há **regras de porta de entrada** para a interface de rede de dois grupos de segurança de rede diferente:
   
   - **mySubnetNSG**: Associado à sub-rede que está a interface de rede.
   - **myVMNSG**: Associado à interface de rede na VM com o nome **myVMVMNic**.

   A regra com o nome **DenyAllInBound** é o que está a impedir comunicação de entrada para a VM através da porta 80, a internet, conforme descrito no [cenário](#scenario). As listas de regra *0.0.0.0/0* para **origem**, que inclui a internet. Nenhuma outra regra com prioridade mais alta (número mais baixo) permite que a porta 80 entrada. Para permitir que a porta 80 para a VM de entrada da internet, consulte [resolver um problema](#resolve-a-problem). Para saber mais sobre as regras de segurança e como o Azure aplica-as, veja [grupos de segurança de rede](security-overview.md).

   Na parte inferior da imagem, verá também **regras de porta de saída**. Em que são as regras de porta de saída para a interface de rede. Embora a figura mostra apenas quatro regras de entrada para cada NSG, seus NSGs podem ter muitas regras mais de quatro. Na imagem, veja **VirtualNetwork** sob **origem** e **destino** e **AzureLoadBalancer** em  **ORIGEM**. **VirtualNetwork** e **AzureLoadBalancer** são [etiquetas de serviço](security-overview.md#service-tags). Etiquetas de serviço representam um grupo de prefixos de endereços IP para o ajudar a minimizar a complexidade para a criação de regra de segurança.

4. Certifique-se de que a VM está em execução, estadual e, em seguida, selecione **regras de segurança efetivas**, conforme mostrado na imagem anterior, para ver as regras de segurança efetivas, mostradas na imagem seguinte:

   ![Ver regras de segurança efetivas](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   As regras listadas são que as mesmas conforme vimos na passo 3, embora haja guias diferentes para o NSG associado à interface de rede e a sub-rede. Como pode ver na imagem, são apresentadas apenas as 50 primeiras regras. Para transferir um ficheiro. csv que contém todas as regras, selecione **transferir**.

   Para ver os prefixos que cada etiqueta de serviço representa, selecione uma regra, tal como a regra com o nome **AllowAzureLoadBalancerInbound**. A imagem seguinte mostra os prefixos para o **AzureLoadBalancer** etiqueta de serviço:

   ![Ver regras de segurança efetivas](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Embora o **AzureLoadBalancer** etiquetas de serviço apenas representam um prefixo, outras etiquetas de serviço representam vários prefixos.

5. Os passos anteriores mostraram as regras de segurança para uma interface de rede com o nome **myVMVMNic**, mas também viu uma interface de rede com o nome **myVMVMNic2** em algumas das imagens anteriores. A VM neste exemplo tem duas interfaces de rede ligados ao mesmo. As regras de segurança efetivas podem ser diferentes para cada interface de rede.

   Para ver as regras para o **myVMVMNic2** interface de rede, selecione-o. Como mostrado na imagem que se segue, a interface de rede tem as mesmas regras associadas à sua sub-rede como a **myVMVMNic** interface, de rede, uma vez que ambas as interfaces de rede estão na mesma sub-rede. Quando associa um NSG a uma sub-rede, as suas regras são aplicadas a todas as interfaces de rede na sub-rede.

   ![Ver regras de segurança](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Ao contrário do **myVMVMNic** interface de rede, o **myVMVMNic2** interface de rede não tem um grupo de segurança de rede associado à mesma. Cada interface de rede e sub-rede podem ter zero ou um NSG associado a si. O NSG associado a cada interface de rede ou sub-rede pode ser o mesmo, ou em diferentes. Pode associar o mesmo grupo de segurança de rede a tantas interfaces de rede e sub-redes que escolher.

Apesar de regras de segurança efetivas foram visualizadas através da VM, também pode ver as regras de segurança efetivas por meio de um indivíduo:
- **Interface de rede**: Saiba como [exibir uma interface de rede](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG**: Saiba como [ver um NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnosticar a utilizar o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que se seguem na [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do seu computador, terá do módulo Azure PowerShell, versão 1.0.0 ou posterior. Executar `Get-Module -ListAvailable Az` no seu computador, para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, terá também de ser executado `Connect-AzAccount` iniciar sessão no Azure com uma conta que tenha o [as permissões necessárias](virtual-network-network-interface.md#permissions)].

Obter as regras de segurança efetivas para uma interface de rede com [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). O exemplo seguinte obtém as regras de segurança efetivas para uma interface de rede com o nome *myVMVMNic*, que é um grupo de recursos chamado *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Resultado é devolvido no formato json. Para compreender a saída, veja [interpretar a saída do comando](#interpret-command-output).
Saída só é devolvida se um NSG é associado a interface de rede, a sub-rede na qual que a interface de rede se encontra ou ambos. A VM tem de ser em execução. Uma VM pode ter várias interfaces de rede com diferentes NSGs aplicados. Quando a resolução de problemas, execute o comando para cada interface de rede.

Se ainda estiver a ter um problema de conectividade, veja [diagnóstico adicional](#additional-diagnosis) e [considerações](#considerations).

Se não souber o nome de uma interface de rede, mas souber o nome da VM a interface de rede está ligada a, os seguintes comandos devolvem os IDs de todas as interfaces de rede ligadas a uma VM:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Recebe um resultado semelhante ao seguinte exemplo:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Na saída anterior, o nome de interface de rede está *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosticar com a CLI do Azure

Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este artigo requer a versão 2.0.32 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de ser executado `az login` e inicie sessão no Azure com uma conta que tenha o [as permissões necessárias](virtual-network-network-interface.md#permissions).

Obter as regras de segurança efetivas para uma interface de rede com [az network nic lista-em vigor-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). O exemplo seguinte obtém as regras de segurança efetivas para uma interface de rede com o nome *myVMVMNic* que está no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Resultado é devolvido no formato json. Para compreender a saída, veja [interpretar a saída do comando](#interpret-command-output).
Saída só é devolvida se um NSG é associado a interface de rede, a sub-rede na qual que a interface de rede se encontra ou ambos. A VM tem de ser em execução. Uma VM pode ter várias interfaces de rede com diferentes NSGs aplicados. Quando a resolução de problemas, execute o comando para cada interface de rede.

Se ainda estiver a ter um problema de conectividade, veja [diagnóstico adicional](#additional-diagnosis) e [considerações](#considerations).

Se não souber o nome de uma interface de rede, mas souber o nome da VM a interface de rede está ligada a, os seguintes comandos devolvem os IDs de todas as interfaces de rede ligadas a uma VM:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

Dentro de saída retornada, verá informações semelhantes ao seguinte exemplo:

```azurecli
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

Na saída anterior, o nome de interface de rede está *myVMVMNic interface*.

## <a name="interpret-command-output"></a>Interpretar a saída do comando

Independentemente se tiver usado o [PowerShell](#diagnose-using-powershell), ou o [CLI do Azure](#diagnose-using-azure-cli) para diagnosticar o problema, recebe um resultado que contém as seguintes informações:

- **NetworkSecurityGroup**: O ID do grupo de segurança de rede.
- **Associação**: Se o grupo de segurança de rede está associado a um *NetworkInterface* ou *sub-rede*. Se um NSG é associado a ambos, o resultado é devolvido com **NetworkSecurityGroup**, **associação**, e **EffectiveSecurityRules**, para cada NSG. Se o NSG é associado ou desassociar imediatamente antes de executar o comando para ver as regras de segurança efetivas, poderá ter de aguardar alguns segundos para que a alteração para refletir no resultado do comando.
- **EffectiveSecurityRules**: Obter uma explicação de cada propriedade é detalhada no [criar uma regra de segurança](manage-network-security-group.md#create-a-security-rule). Regra nomes prefaciados pela *defaultSecurityRules /* são padrão regras de segurança que existem em cada NSG. Regra nomes prefaciados pela *securityrules foi /* são regras que criou. As regras que especificam um [etiqueta de serviço](security-overview.md#service-tags), tal como **Internet**, **VirtualNetwork**, e **AzureLoadBalancer** para o  **destinationAddressPrefix** ou **sourceAddressPrefix** propriedades, também tem valores para o **expandedDestinationAddressPrefix** propriedade. O **expandedDestinationAddressPrefix** propriedade apresenta uma lista de todos os prefixos de endereço representados por etiqueta de serviço.

Se vir regras duplicadas listadas no resultado, é porque um NSG é associado à interface de rede e a sub-rede. Ambos os NSGs têm as mesmas regras padrão e podem ter regras duplicadas adicionais, se tiver criado as suas próprias regras que são os mesmos em ambos os NSGs.

A regra com o nome **defaultSecurityRules/DenyAllInBound** é o que está a impedir comunicação de entrada para a VM através da porta 80, a internet, conforme descrito no [cenário](#scenario). Nenhuma outra regra com prioridade mais alta (número mais baixo) permite que a porta 80 de entrada da internet.

## <a name="resolve-a-problem"></a>Resolver um problema

Se utilizar o Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), ou o [da CLI do Azure](#diagnose-using-azure-cli) para diagnosticar o problema apresentado a [cenário](#scenario) desta artigo, a solução é criar uma regra de segurança de rede com as seguintes propriedades:

| Propriedade                | Value                                                                              |
|---------                |---------                                                                           |
| Origem                  | Qualquer                                                                                |
| Intervalo de portas de origem      | Qualquer                                                                                |
| Destino             | O endereço IP da VM, um intervalo de endereços IP ou todos os endereços na sub-rede. |
| Intervalos de portas de destino | 80                                                                                 |
| Protocolo                | TCP                                                                                |
| Ação                  | Permitir                                                                              |
| Prioridade                | 100                                                                                |
| Name                    | Allow-HTTP-All                                                                     |

Depois de criar a regra, é permitida a porta 80 de entrada da internet, porque a prioridade da regra é maior do que a regra de segurança predefinida com o nome *DenyAllInBound*, que nega o tráfego. Saiba como [criar uma regra de segurança](manage-network-security-group.md#create-a-security-rule). Se diferentes NSGs estão associados à interface de rede e a sub-rede, tem de criar a mesma regra em ambos os NSGs.

Quando o Azure processa o tráfego de entrada, processa regras num NSG associado à sub-rede (se houver um NSG associado) e, em seguida, processa as regras num NSG associado à interface de rede. Se houver um NSG associado para a interface de rede e a sub-rede, a porta tem de estar aberta em ambos os NSGs, para o tráfego alcance a VM. Para facilitar a administração e comunicação com problemas, recomendamos que associa um NSG a uma sub-rede, em vez de interfaces de rede individuais. Se as VMs numa sub-rede têm regras de segurança diferentes, pode tornar a rede de membros de interfaces de um grupo de segurança de aplicação (ASG) e especifique um ASG como origem e destino de uma regra de segurança. Saiba mais sobre [grupos de segurança de aplicativo](security-overview.md#application-security-groups).

Se ainda estiver a ter problemas de comunicação, consulte [considerações](#considerations) e diagnóstico adicional.

## <a name="considerations"></a>Considerações

Na solução de problemas de conectividade, considere os seguintes pontos:

* Regras de segurança predefinidas bloquear o acesso de entrada da internet e permitem apenas tráfego de entrada a partir da rede virtual. Para permitir o tráfego de entrada da Internet, adicione regras de segurança com uma prioridade mais alta do que as regras predefinidas. Saiba mais sobre [regras de segurança predefinidas](security-overview.md#default-security-rules), ou como [adicionar uma regra de segurança](manage-network-security-group.md#create-a-security-rule).
* Se tiver executado o peering redes virtuais, por predefinição, o **VIRTUAL_NETWORK** etiqueta de serviço automaticamente se expande para incluir os prefixos de redes virtuais em modo de peering. Para resolver quaisquer problemas relacionados com o peering de rede virtual, pode ver os prefixos no **ExpandedAddressPrefix** lista. Saiba mais sobre [peering de rede virtual](virtual-network-peering-overview.md) e [etiquetas de serviço](security-overview.md#service-tags).
* Regras de segurança efetivas só são mostradas para uma interface de rede, se houver um NSG associado à interface de rede da VM e, em alternativa, sub-rede, e se a VM está em execução.
* Se não há nenhum NSGs associados com a interface de rede ou sub-rede e, se tiver um [endereço IP público](virtual-network-public-ip-address.md) atribuído a uma VM, a todas as portas estão abertas para acesso de entrada do e acesso de saída para qualquer lugar. Se a VM tiver um endereço IP público, recomendamos que aplicar um NSG à sub-rede da interface de rede.

## <a name="additional-diagnosis"></a>Diagnóstico adicional

* Para executar um teste rápido para determinar se o tráfego é permitido para ou a partir de uma VM, utilize o [fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) capacidade do observador de rede do Azure. Verificação do fluxo de IP indica se o tráfego é permitido ou negado. Se negado, fluxo de IP Certifique-se diz a quais regras de segurança é negar o tráfego.
* Se não existirem não existem regras de segurança, fazendo com que a conectividade de rede de uma VM efetuar a ativação, o problema pode ser devido a:
  * Software de firewall em execução no sistema de operativo da VM
  * Rotas configuradas para aplicações virtuais ou tráfego no local. O tráfego da Internet pode ser redirecionado para a sua rede no local através de [imposição de túnel](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se forçar o tráfego de internet de túnel para uma aplicação virtual ou no local, poderá não conseguir ligar à VM a partir da internet. Para saber como diagnosticar problemas de rotas que poderão impedir o fluxo de tráfego de VM, veja [diagnosticar um problema de encaminhamento de tráfego de rede virtual machine](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre todas as tarefas, propriedades e definições para um [grupo de segurança de rede](manage-network-security-group.md#work-with-network-security-groups) e [regras de segurança](manage-network-security-group.md#work-with-security-rules).
- Saiba mais sobre [regras de segurança predefinidas](security-overview.md#default-security-rules), [etiquetas de serviço](security-overview.md#service-tags), e [como o Azure processa as regras de segurança para o tráfego de entrada e saída](security-overview.md#network-security-groups) para uma VM.
