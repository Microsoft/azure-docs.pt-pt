---
title: Ver topologia de rede virtual Azure | Microsoft Docs
description: Saiba como ver os recursos numa rede virtual e as relações entre os recursos.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: c04da65af27ebd5ac654bc059ae004c157a20f33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84737534"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Ver a topologia de uma rede virtual Azure

Neste artigo, aprende-se a ver recursos numa rede virtual microsoft Azure e as relações entre os recursos. Por exemplo, uma rede virtual contém sub-redes. As sub-redes contêm recursos, tais como Máquinas Virtuais Azure (VM). Os VMs têm uma ou mais interfaces de rede. Cada sub-rede pode ter um grupo de segurança de rede e uma tabela de rotas associada a ela. A capacidade de topologia do Azure Network Watcher permite-lhe visualizar todos os recursos numa rede virtual, os recursos associados aos recursos numa rede virtual e as relações entre os recursos.

Pode utilizar o [portal Azure,](#azure-portal)o [Azure CLI](#azure-cli)ou [o PowerShell](#powershell) para ver uma topologia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Ver topologia - Portal Azure

1. Inicie sessão no [portal Azure](https://portal.azure.com) com uma conta que tenha as [permissões necessárias.](required-rbac-permissions.md)
2. No topo, canto esquerdo do portal, selecione **Todos os serviços**.
3. Na caixa de **filtros de todos os serviços,** *insira o Observador de Rede*. Quando **o Observador de Rede** aparecer nos resultados, selecione-o.
4. Selecione **Topologia**. Gerar uma topologia requer um observador de rede na mesma região em que a rede virtual que você quer gerar a topologia para existe. Se não tiver um observador de rede ativado na região que a rede virtual para a qual pretende gerar uma topologia está, os observadores de rede são automaticamente criados para si em todas as regiões. Os observadores de rede são criados num grupo de recursos chamado **NetworkWatcherRG**.
5. Selecione uma subscrição, o grupo de recursos de uma rede virtual para a quais deseja visualizar a topologia e, em seguida, selecione a rede virtual. Na imagem seguinte, é mostrada uma topologia para uma rede virtual chamada *MyVnet,* no grupo de recursos chamado *MyResourceGroup*:

    ![Ver topologia](./media/view-network-topology/view-topology.png)

    Como pode ver na imagem anterior, a rede virtual contém três sub-redes. Uma sub-rede tem um VM implantado nele. O VM tem uma interface de rede anexada a ela e um endereço IP público associado a ele. As outras duas sub-redes têm uma tabela de rotas associada a eles. Cada tabela de rotas contém duas rotas. Uma sub-rede tem um grupo de segurança de rede associado a ela. A informação sobre a topologia só é mostrada para os recursos que são:
    
    - Dentro do mesmo grupo de recursos e região que a rede virtual *myVnet.* Por exemplo, um grupo de segurança de rede que existe num grupo de recursos diferente do *MyResourceGroup*, não é mostrado, mesmo que o grupo de segurança da rede esteja associado a uma sub-rede na rede virtual *MyVnet.*
    - Dentro, ou associado a recursos dentro, a rede virtual *myVnet.* Por exemplo, não é apresentado um grupo de segurança de rede que não esteja associado a uma interface de sub-rede ou de rede na rede virtual *myVnet,* mesmo que o grupo de segurança da rede esteja no grupo de recursos *MyResourceGroup.*

   A topologia mostrada na imagem é para a rede virtual criada após a implantação do **tráfego da Rota através de uma amostra de script de aparelho virtual de rede**, que pode implementar usando o [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), ou [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Selecione **Baixar a topologia** para descarregar a imagem como um ficheiro editável, em formato svg.

Os recursos mostrados no diagrama são um subconjunto dos componentes de rede na rede virtual. Por exemplo, enquanto um grupo de segurança de rede é mostrado, as regras de segurança no seu interior não são mostradas no diagrama. Embora não diferenciadas no diagrama, as linhas representam uma de duas relações: *Contenção* ou *associadas*. Para ver a lista completa de recursos na rede virtual, e o tipo de relação entre os recursos, gere a topologia com [o PowerShell](#powershell) ou o [Azure CLI.](#azure-cli)

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Ver topologia - Azure CLI

Pode executar os comandos nos passos que se seguem:
- Na Azure Cloud Shell, selecionando **Try It** no canto superior direito de qualquer comando. O Azure Cloud Shell é uma concha interativa gratuita que tem ferramentas Azure comuns pré-instaladas e configuradas para usar com a sua conta.
- Executando o CLI a partir do seu computador. Se executar o CLI a partir do seu computador, os passos neste artigo requerem a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.

A conta que utiliza deve ter as [permissões necessárias.](required-rbac-permissions.md)

1. Se já tem um observador de rede na mesma região que a rede virtual para a que pretende criar uma topologia, salte para o passo 3. Crie um grupo de recursos para conter um observador de rede com [criação de grupo az](/cli/azure/group). O exemplo a seguir cria o grupo de recursos na região *leste:*

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Crie um observador de rede com [configuração de observador de rede az](/cli/azure/network/watcher#az-network-watcher-configure). O exemplo a seguir cria um observador de rede na região *leste:*

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Veja a topologia com [a az network watcher show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology). O exemplo a seguir vê a topologia de um grupo de recursos chamado *MyResourceGroup:*

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    A informação de topologia só é devolvida para recursos que estejam dentro do mesmo grupo de recursos que o grupo de recursos *MyResourceGroup* e a mesma região que o observador de rede. Por exemplo, um grupo de segurança de rede que existe num grupo de recursos diferente do *MyResourceGroup*, não é mostrado, mesmo que o grupo de segurança da rede esteja associado a uma sub-rede na rede virtual *MyVnet.*

   Saiba mais sobre as relações e [propriedades](#properties) na saída devolvida. Se não tiver uma rede virtual existente para visualizar uma topologia, pode criar uma utilizando o tráfego da Rota através de uma amostra de script [de aparelho virtual de rede.](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) Para ver um diagrama da topologia e descarregá-lo num ficheiro editável, utilize o [portal](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Ver topologia - PowerShell

Pode executar os comandos nos passos que se seguem:
- Na Azure Cloud Shell, selecionando **Try It** no canto superior direito de qualquer comando. O Azure Cloud Shell é uma concha interativa gratuita que tem ferramentas Azure comuns pré-instaladas e configuradas para usar com a sua conta.
- Ao executar o PowerShell a partir do seu computador. Se executar o PowerShell a partir do seu computador, este artigo requer o módulo Azure `Az` PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

A conta que utiliza deve ter as [permissões necessárias.](required-rbac-permissions.md)

1. Se já tem um observador de rede na mesma região que a rede virtual para a que pretende criar uma topologia, salte para o passo 3. Crie um grupo de recursos para conter um observador de rede com [o New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O exemplo a seguir cria o grupo de recursos na região *leste:*

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Criar um observador de rede com [o New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). O exemplo a seguir cria um observador de rede na região leste:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Recupere uma instância do Observador de Rede com [o Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). O exemplo a seguir recupera um observador de rede na região leste dos EUA:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Recupere uma topologia com [a Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). O exemplo a seguir recupera uma topologia para uma rede virtual no grupo de recursos chamado *MyResourceGroup:*

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   A informação de topologia só é devolvida para recursos que estejam dentro do mesmo grupo de recursos que o grupo de recursos *MyResourceGroup* e a mesma região que o observador de rede. Por exemplo, um grupo de segurança de rede que existe num grupo de recursos diferente do *MyResourceGroup*, não é mostrado, mesmo que o grupo de segurança da rede esteja associado a uma sub-rede na rede virtual *MyVnet.*

   Saiba mais sobre as relações e [propriedades](#properties) na saída devolvida. Se não tiver uma rede virtual existente para visualizar uma topologia, pode criar uma utilizando o tráfego da Rota através de uma amostra de script [de aparelho virtual de rede.](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) Para ver um diagrama da topologia e descarregá-lo num ficheiro editável, utilize o [portal](#azure-portal).

## <a name="relationships"></a>Relações

Todos os recursos devolvidos numa topologia têm um dos seguintes tipos de relação com outro recurso:

| Tipo de relação | Exemplo                                                                                                |
| ---               | ---                                                                                                    |
| Contenção       | Uma rede virtual contém uma sub-rede. Uma sub-rede contém uma interface de rede.                            |
| Associada        | Uma interface de rede está associada a um VM. Um endereço IP público está associado a uma interface de rede. |

## <a name="properties"></a>Propriedades

Todos os recursos devolvidos numa topologia têm as seguintes propriedades:

- **Nome**: O nome do recurso
- **Id**: O URI do recurso.
- **Localização**: A região Azure em que o recurso existe.
- **Associações**: Uma lista de associações ao objeto referenciado. Cada associação tem as seguintes propriedades:
    - **AssociationType**: Faz referência à relação entre o objeto da criança e o progenitor. Valores válidos são *Contém* ou *Associados.*
    - **Nome**: O nome do recurso referenciado.
    - **ResourceId**: - O URI do recurso referenciado na associação.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [diagnosticar um problema de filtro de tráfego de rede de ou para um VM](diagnose-vm-network-traffic-filtering-problem.md) utilizando a capacidade de verificação do fluxo IP do Observador de Rede
- Saiba como [diagnosticar um problema de encaminhamento de tráfego de rede a partir de um VM](diagnose-vm-network-routing-problem.md) utilizando a próxima capacidade de lúpulo do Network Watcher
