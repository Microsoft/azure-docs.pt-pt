---
title: Ver a topologia de rede virtual do Azure | Documentos da Microsoft
description: Saiba como ver os recursos de uma rede virtual e as relações entre os recursos.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: jdial
ms.openlocfilehash: eb98fc2da95f1aa2b7294d09ec2a3145bdb5c789
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112743"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Ver a topologia de rede virtual do Azure

Neste artigo, irá aprender a ver os recursos de uma rede virtual do Microsoft Azure e as relações entre os recursos. Por exemplo, redes virtuais contenham sub-redes. Sub-redes contenham recursos, tais como máquinas virtuais (VM) do Azure. As VMs têm uma ou mais interfaces de rede. Cada sub-rede pode ter um grupo de segurança de rede e uma tabela de rotas associada a ele. A capacidade de topologia do observador de rede do Azure permite-lhe ver todos os recursos numa rede virtual, os recursos associados a recursos numa rede virtual e as relações entre os recursos.

Pode utilizar o [portal do Azure](#azure-portal), o [CLI do Azure](#azure-cli), ou [PowerShell](#powershell) para ver uma topologia.

## <a name = "azure-portal"></a>Ver a topologia - portal do Azure

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) com uma conta que tem as informações necessárias [permissões](required-rbac-permissions.md).
2. Na parte superior, à esquerda do portal, selecione **todos os serviços**.
3. Na **todos os serviços** filtrar caixa, introduza *observador de rede*. Quando a opção **Observador de Rede** aparecer nos resultados, selecione-a.
4. Selecione **topologia**. Gerar uma topologia requer um observador de rede na mesma região que a rede virtual que pretende gerar a topologia para existe no. Se não tiver um observador de rede ativado na região que a rede virtual que pretende gerar uma topologia para pertença, observadores de rede são criadas automaticamente para em todas as regiões. Observadores de rede são criados num grupo de recursos com o nome **NetworkWatcherRG**.
5. Selecione uma subscrição, o grupo de recursos de uma rede virtual que pretende ver a topologia, e, em seguida, selecione a rede virtual. Na imagem seguinte, uma topologia é apresentada para uma rede virtual denominada *MyVnet*, no grupo de recursos com o nome *MyResourceGroup*:

    ![Ver topologia](./media/view-network-topology/view-topology.png)

    Como pode ver na imagem anterior, a rede virtual contém três sub-redes. Uma sub-rede tem uma VM implementada na mesma. A VM tem uma interface de rede ligados ao mesmo e um endereço IP público associado a si. As outras duas sub-redes têm uma tabela de rotas associada a eles. Cada tabela de rotas contém duas rotas. Uma sub-rede tem um grupo de segurança de rede associado à mesma. Informações de topologia são mostradas apenas para recursos que são:
    
    - Dentro do mesmo grupo de recursos e região que o *myVnet* rede virtual. Por exemplo, um grupo de segurança de rede existe num grupo de recursos diferente *MyResourceGroup*, não é mostrado, mesmo que o grupo de segurança de rede está associado a uma sub-rede na *MyVnet* rede virtual .
    - Dentro de, ou associados a recursos no, a *myVnet* rede virtual. Por exemplo, um grupo de segurança de rede não está associado a uma interface de rede ou sub-rede no *myVnet* rede virtual não é mostrada, mesmo se o grupo de segurança de rede estiver no *MyResourceGroup* grupo de recursos.

   A topologia de mostrado na imagem é para a rede virtual criada depois de implementar o **encaminhar o tráfego através de um exemplo de script de aplicação virtual de rede**, que pode implementar utilizando o [CLI do Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), ou [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Selecione **transferir topologia** para transferir a imagem como um arquivo editável, no formato de svg.

Os recursos mostrados no diagrama são um subconjunto dos componentes do sistema de rede na rede virtual. Por exemplo, embora um grupo de segurança de rede é mostrado, as regras de segurança dentro da mesma não são apresentadas no diagrama. Embora não diferenciados no diagrama, as linhas representam uma das duas relações: *Contenção* ou *associados*. Para ver a lista completa de recursos na rede virtual e o tipo de relação entre os recursos, gerar a topologia com [PowerShell](#powershell) ou o [CLI do Azure](#azure-cli).

## <a name = "azure-cli"></a>Ver a topologia - CLI do Azure

Pode executar os comandos nos passos que se seguem:
- No Azure Cloud Shell, selecionando **experimentar** na parte superior direita do qualquer comando. O Azure Cloud Shell é um shell interativo gratuito que tenha ferramentas comuns do Azure pré-instaladas e configuradas para utilizar com a sua conta.
- Ao executar a CLI do seu computador. Se executar a CLI do seu computador, os passos neste artigo requerem 2.0.31 da versão da CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.

A conta que utilizar tem de ter as informações necessárias [permissões](required-rbac-permissions.md).

1. Se já tiver um observador de rede na mesma região que a rede virtual que pretende criar uma topologia para, avance para o passo 3. Criar um grupo de recursos para conter um observador de rede com [criar grupo az](/cli/azure/group). O exemplo seguinte cria o grupo de recursos no *eastus* região:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Criar um observador de rede com [configurar o observador de rede de az](/cli/azure/network/watcher#az-network-watcher-configure). O exemplo seguinte cria um observador de rede na *eastus* região:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Ver a topologia com [show-topologia do observador de rede de az](/cli/azure/network/watcher#az-network-watcher-show-topology). O seguinte exemplo mostra a topologia para um grupo de recursos chamado *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informações de topologia só são devolvidas para recursos que estejam no mesmo grupo de recursos como o *MyResourceGroup* grupo de recursos e a mesma região que o observador de rede. Por exemplo, um grupo de segurança de rede existe num grupo de recursos diferente *MyResourceGroup*, não é mostrado, mesmo que o grupo de segurança de rede está associado a uma sub-rede na *MyVnet* rede virtual .

   Saiba mais sobre as relações e [propriedades](#properties) no resultado retornado. Se não tiver uma rede virtual existente para ver uma topologia para, pode criar uma com o [encaminhar o tráfego através de uma aplicação virtual de rede](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) exemplo do script. Para ver um diagrama da topologia e transferi-lo num arquivo editável, utilize o [portal](#azure-portal).

## <a name = "powershell"></a>Ver a topologia - PowerShell

Pode executar os comandos nos passos que se seguem:
- No Azure Cloud Shell, selecionando **experimentar** na parte superior direita do qualquer comando. O Azure Cloud Shell é um shell interativo gratuito que tenha ferramentas comuns do Azure pré-instaladas e configuradas para utilizar com a sua conta.
- Ao executar o PowerShell do seu computador. Se executar o PowerShell a partir do seu computador, os passos neste artigo requerem a versão 5.7.0 ou posterior do módulo AzureRm. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzureRmAccount` para criar uma ligação com o Azure.

A conta que utilizar tem de ter as informações necessárias [permissões](required-rbac-permissions.md).

1. Se já tiver um observador de rede na mesma região que a rede virtual que pretende criar uma topologia para, avance para o passo 3. Criar um grupo de recursos para conter um observador de rede com [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). O exemplo seguinte cria o grupo de recursos no *eastus* região:

    ```azurepowershell-interactive
    New-AzureRmResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Criar um observador de rede com [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher). O exemplo seguinte cria um observador de rede na região eastus:

    ```azurepowershell-interactive
    New-AzureRmNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Recuperar uma instância de observador de rede com [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher). O exemplo seguinte obtém um observador de rede na região E.U. a leste:

    ```azurepowershell-interactive
    $nw = Get-AzurermResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzureRmNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Obter uma topologia com [Get-AzureRmNetworkWatcherTopology](/powershell/module/azurerm.network/get-azurermnetworkwatchertopology). O exemplo seguinte obtém uma topologia para uma rede virtual no grupo de recursos chamado *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzureRmNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informações de topologia só são devolvidas para recursos que estejam no mesmo grupo de recursos como o *MyResourceGroup* grupo de recursos e a mesma região que o observador de rede. Por exemplo, um grupo de segurança de rede existe num grupo de recursos diferente *MyResourceGroup*, não é mostrado, mesmo que o grupo de segurança de rede está associado a uma sub-rede na *MyVnet* rede virtual .

   Saiba mais sobre as relações e [propriedades](#properties) no resultado retornado. Se não tiver uma rede virtual existente para ver uma topologia para, pode criar uma com o [encaminhar o tráfego através de uma aplicação virtual de rede](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) exemplo do script. Para ver um diagrama da topologia e transferi-lo num arquivo editável, utilize o [portal](#azure-portal).

## <a name="relationships"></a>Relações

Todos os recursos retornados numa topologia de tem um dos seguintes tipos de relação para outro recurso:

| Tipo de relação | Exemplo                                                                                                |
| ---               | ---                                                                                                    |
| Contenção       | Uma rede virtual contém uma sub-rede. Uma sub-rede contém uma interface de rede.                            |
| Associado        | Uma interface de rede está associada uma VM. Um endereço IP público é associado a uma interface de rede. |

## <a name="properties"></a>Propriedades

Todos os recursos retornados numa topologia de tem as seguintes propriedades:

- **Nome**: O nome do recurso
- **Id**: O URI do recurso.
- **Localização**: A região do Azure que o recurso existe.
- **As associações**: Uma lista de associações para o objeto referenciado. Cada associação tem as seguintes propriedades:
    - **AssociationType**: Faz referência a relação entre o objeto filho e pai. Os valores válidos são *Contains* ou *associados*.
    - **Nome**: O nome do recurso referenciado.
    - **ResourceId**:-o URI do recurso referenciado na associação.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [diagnosticar um problema de filtragem de tráfego rede para ou a partir de uma VM](diagnose-vm-network-traffic-filtering-problem.md) utilizando o fluxo de IP do observador de rede Verifique a capacidade
- Saiba como [diagnosticar um problema de encaminhamento de tráfego de rede a partir de uma VM](diagnose-vm-network-routing-problem.md) com a capacidade de salto seguinte do observador de rede
