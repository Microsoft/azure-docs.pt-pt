---
title: Atualizações automáticas de imagem do sistema operacional com conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba como atualizar automaticamente a imagem do sistema operacional em instâncias de VM em um conjunto de dimensionamento
services: virtual-machine-scale-sets
documentationcenter: ''
author: shandilvarun
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2019
ms.author: vashan
ms.openlocfilehash: 95a313b3c6995d55b86561c685641b447edae127
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240930"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Conjunto de dimensionamento de máquinas virtuais do Azure atualizações automáticas de imagem de so

A habilitação de atualizações automáticas de imagem de so no conjunto de dimensionamento ajuda a facilitar o gerenciamento de atualizações, atualizando com segurança e automaticamente o disco do sistema operacional para todas as instâncias no conjunto de dimensionamento

A atualização automática do sistema operacional tem as seguintes características:

- Uma vez configurado, a imagem de sistema operacional mais recente publicada por editores de imagem é aplicada automaticamente ao conjunto de dimensionamento sem intervenção do usuário.
- Atualiza lotes de instâncias de forma contínua toda vez que uma nova imagem de plataforma é publicada pelo Publicador.
- Integra-se com investigações de integridade do aplicativo e [extensão de integridade do aplicativo](virtual-machine-scale-sets-health-extension.md).
- Funciona para todos os tamanhos de VM e para imagens de plataforma Windows e Linux.
- Você pode recusar atualizações automáticas a qualquer momento (as atualizações do so também podem ser iniciadas manualmente).
- O disco do sistema operacional de uma VM é substituído pelo novo disco do sistema operacional criado com a versão mais recente da imagem. As extensões configuradas e os scripts de dados personalizados são executados, enquanto os discos de dados persistentes são mantidos.
- Há suporte para [sequenciamento de extensão](virtual-machine-scale-sets-extension-sequencing.md) .
- A atualização automática da imagem do sistema operacional pode ser habilitada em um conjunto de dimensionamento de qualquer tamanho.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Como funciona a atualização automática da imagem do sistema operacional?

Uma atualização funciona substituindo o disco do sistema operacional de uma VM por um novo disco criado usando a versão mais recente da imagem. Todas as extensões configuradas e scripts de dados personalizados são executados no disco do sistema operacional, enquanto os discos de dados persistentes são mantidos. Para minimizar o tempo de inatividade do aplicativo, as atualizações ocorrem em lotes, com no máximo 20% do conjunto de dimensionamento atualizando a qualquer momento. Também é possível integrar uma investigação de integridade do aplicativo ou uma extensão de integridade do [aplicativo](virtual-machine-scale-sets-health-extension.md)do Azure Load Balancer. Recomendamos incorporar uma pulsação de aplicativo e validar o êxito da atualização para cada lote no processo de atualização.

O processo de atualização funciona da seguinte maneira:
1. Antes de iniciar o processo de atualização, o orquestrador garantirá que no máximo 20% das instâncias em todo o conjunto de dimensionamento não estejam íntegros (por qualquer motivo).
2. O orquestrador de atualização identifica o lote de instâncias de VM a serem atualizadas, com um lote com um máximo de 20% da contagem total de instâncias. Para conjuntos de dimensionamento menores com 5 ou menos instâncias, o tamanho do lote para uma atualização é uma instância de máquina virtual.
3. O disco do sistema operacional do lote selecionado de instâncias de VM é substituído por um novo disco do sistema operacional criado a partir da imagem mais recente. Todas as extensões e configurações especificadas no modelo do conjunto de dimensionamento são aplicadas à instância atualizada.
4. Para conjuntos de dimensionamento com investigações de integridade de aplicativo configuradas ou extensão de integridade de aplicativos, a atualização aguarda até 5 minutos para que a instância se torne íntegra, antes de prosseguir para atualizar o próximo lote. Se uma instância não recuperar sua integridade em 5 minutos após uma atualização, por padrão, o disco do sistema operacional anterior para a instância será restaurado.
5. O orquestrador de atualização também controla a porcentagem de instâncias que se tornam não íntegras após uma atualização. A atualização será interrompida se mais de 20% das instâncias atualizadas se tornarem não íntegras durante o processo de atualização.
6. O processo acima continua até que todas as instâncias no conjunto de dimensionamento tenham sido atualizadas.

O orquestrador de atualização do so do conjunto de dimensionamento verifica a integridade geral do conjunto de dimensionamento antes de atualizar cada lote. Durante a atualização de um lote, pode haver outras atividades de manutenção planejadas ou não planejadas simultâneas que poderiam afetar a integridade das instâncias do conjunto de dimensionamento. Nesses casos, se mais de 20% das instâncias do conjunto de dimensionamento se tornarem não íntegros, a atualização do conjunto de dimensionamento será interrompida no final do lote atual.

## <a name="supported-os-images"></a>Imagens do sistema operacional com suporte
No momento, só há suporte para determinadas imagens da plataforma do sistema operacional. Atualmente, não há suporte para imagens personalizadas.

Atualmente, há suporte para os seguintes SKUs (e mais são adicionados periodicamente):

| Publicador               | Oferta do sistema operacional      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Wave não autorizado (OpenLogic)  | CentOS        | 7,5                |
| CoreOS                  | CoreOS        | Estável             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-datacenter-com-contêineres |
| Microsoft Corporation   | WindowsServer | 2019-datacenter |
| Microsoft Corporation   | WindowsServer | 2019-datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-datacenter-com-contêineres |
| Microsoft Corporation   | WindowsServer | Datacenter-Core-1903-com-containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Requisitos para configurar a atualização automática da imagem do sistema operacional

- A propriedade *version* da imagem da plataforma deve ser definida como *mais recente*.
- Use investigações de integridade do aplicativo ou [extensão de integridade do aplicativo](virtual-machine-scale-sets-health-extension.md) para conjuntos de dimensionamento não Service Fabric.
- Use a API de computação versão 2018-10-01 ou superior.
- Verifique se os recursos externos especificados no modelo do conjunto de dimensionamento estão disponíveis e atualizados. Os exemplos incluem o URI de SAS para carga de inicialização em Propriedades de extensão de VM, carga na conta de armazenamento, referência a segredos no modelo e muito mais.
- Para conjuntos de dimensionamento usando máquinas virtuais do Windows, começando com a API de computação versão 2019-03-01, a propriedade *virtualMachineProfile. osProfile. windowsConfiguration. enableAutomaticUpdates* deve ser definida como *false* no modelo de conjunto de dimensionamento defini. A propriedade acima habilita atualizações na VM em que "Windows Update" aplica patches do sistema operacional sem substituir o disco do sistema operacional. Com as atualizações automáticas de imagem do sistema operacional habilitadas no conjunto de dimensionamento, uma atualização adicional por meio de "Windows Update" não é necessária.

### <a name="service-fabric-requirements"></a>Requisitos de Service Fabric

Se você estiver usando Service Fabric, verifique se as seguintes condições foram atendidas:
-   Service Fabric [nível de durabilidade](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) é prata ou ouro, e não bronze.
-   A extensão de Service Fabric na definição do modelo do conjunto de dimensionamento deve ter TypeHandlerVersion 1,1 ou superior.
-   O nível de durabilidade deve ser o mesmo no Cluster Service Fabric e na extensão Service Fabric na definição do modelo do conjunto de dimensionamento.

Verifique se as configurações de durabilidade não são correspondentes no Cluster Service Fabric e na extensão de Service Fabric, pois uma incompatibilidade resultará em erros de atualização. Os níveis de durabilidade podem ser modificados de acordo com as diretrizes descritas nesta [página](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).

## <a name="configure-automatic-os-image-upgrade"></a>Configurar a atualização automática da imagem do sistema operacional
Para configurar a atualização automática da imagem do sistema operacional, verifique se a propriedade *automaticOSUpgradePolicy. enableAutomaticOSUpgrade* está definida como *true* na definição do modelo do conjunto de dimensionamento.

### <a name="rest-api"></a>API REST
O exemplo a seguir descreve como definir atualizações automáticas do sistema operacional em um modelo de conjunto de dimensionamento:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) para configurar atualizações automáticas de imagem do sistema operacional para seu conjunto de dimensionamento. O exemplo a seguir configura as atualizações automáticas para o conjunto de dimensionamento chamado *Myscalemodeset* no grupo de recursos chamado *MyResource*Group:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Use [AZ vmss Update](/cli/azure/vmss#az-vmss-update) para configurar atualizações automáticas de imagem do sistema operacional para seu conjunto de dimensionamento. Use CLI do Azure 2.0.47 ou superior. O exemplo a seguir configura as atualizações automáticas para o conjunto de dimensionamento chamado *Myscalemodeset* no grupo de recursos chamado *MyResource*Group:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Usando investigações de integridade do aplicativo

Durante uma atualização do sistema operacional, as instâncias de VM em um conjunto de dimensionamento são atualizadas em um lote por vez. A atualização deve continuar somente se o aplicativo do cliente estiver íntegro nas instâncias de VM atualizadas. Recomendamos que o aplicativo forneça sinais de integridade para o mecanismo de atualização do sistema operacional do conjunto de dimensionamento. Por padrão, durante as atualizações do so, a plataforma considera o estado de energia da VM e o estado de provisionamento da extensão para determinar se uma instância de VM está íntegra após uma atualização. Durante a atualização do sistema operacional de uma instância de VM, o disco do sistema operacional em uma instância de VM é substituído por um novo disco baseado na versão mais recente da imagem. Depois que a atualização do sistema operacional for concluída, as extensões configuradas serão executadas nessas VMs. O aplicativo é considerado Íntegro somente quando todas as extensões na instância são provisionadas com êxito.

Um conjunto de dimensionamento pode, opcionalmente, ser configurado com investigações de integridade do aplicativo para fornecer à plataforma informações precisas sobre o estado em andamento do aplicativo. As investigações de integridade do aplicativo são investigações de Load Balancer personalizadas que são usadas como um sinal de integridade. O aplicativo em execução em uma instância de VM do conjunto de dimensionamento pode responder a solicitações HTTP ou TCP externas indicando se ele está íntegro. Para obter mais informações sobre como funcionam as investigações de Load Balancer personalizadas, consulte para [entender as investigações do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Não há suporte para investigações de integridade de aplicativo para Service Fabric conjuntos de dimensionamento. Os conjuntos de dimensionamento não Service Fabric exigem investigações de integridade do aplicativo Load Balancer ou a [extensão de integridade do aplicativo](virtual-machine-scale-sets-health-extension.md).

Se o conjunto de dimensionamento estiver configurado para usar vários grupos de posicionamento, as investigações usando um [Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) precisarão ser usadas.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configurando uma investigação de Load Balancer personalizada como investigação de integridade do aplicativo em um conjunto de dimensionamento
Como prática recomendada, crie uma investigação de balanceador de carga explicitamente para integridade do conjunto de dimensionamento. O mesmo ponto de extremidade para uma investigação HTTP ou investigação TCP existente pode ser usado, mas uma investigação de integridade pode exigir um comportamento diferente de uma investigação de balanceador de carga tradicional. Por exemplo, uma investigação de balanceador de carga tradicional poderia retornar não íntegro se a carga na instância for muito alta, mas isso não seria apropriado para determinar a integridade da instância durante uma atualização automática do sistema operacional. Configure a investigação para ter uma alta taxa de investigação de menos de dois minutos.

A investigação do balanceador de carga pode ser referenciada no *networkProfile* do conjunto de dimensionamento e pode ser associada a um balanceador de carga interno ou público, como a seguir:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Ao usar atualizações automáticas do sistema operacional com o Service Fabric, a nova imagem do sistema operacional é distribuída no domínio de atualização por domínio de atualização para manter a alta disponibilidade dos serviços em execução no Service Fabric. Para utilizar atualizações automáticas do sistema operacional no Service Fabric o cluster deve ser configurado para usar a camada de durabilidade prateada ou superior. Para obter mais informações sobre as características de durabilidade de clusters Service Fabric, consulte [esta documentação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Manter as credenciais atualizadas
Se o conjunto de dimensionamento usar quaisquer credenciais para acessar recursos externos, como uma extensão de VM configurada para usar um token SAS para a conta de armazenamento, verifique se as credenciais estão atualizadas. Se alguma credencial, incluindo certificados e tokens, tiver expirado, a atualização falhará e o primeiro lote de VMs será deixado em um estado de falha.

As etapas recomendadas para recuperar VMs e reabilitar a atualização automática do sistema operacional se houver uma falha de autenticação de recurso:

* Gere novamente o token (ou qualquer outra credencial) passado para suas extensões.
* Certifique-se de que qualquer credencial usada de dentro da VM para se comunicar com entidades externas esteja atualizada.
* Atualize as extensões no modelo do conjunto de dimensionamento com quaisquer tokens novos.
* Implante o conjunto de dimensionamento atualizado, que atualizará todas as instâncias de VM, incluindo as que falharam.

## <a name="using-application-health-extension"></a>Usando a extensão de integridade do aplicativo
A extensão de integridade do aplicativo é implantada em uma instância do conjunto de dimensionamento de máquinas virtuais e relata a integridade da VM de dentro da instância do conjunto de dimensionamento. Você pode configurar a extensão para investigação em um ponto de extremidade do aplicativo e atualizar o status do aplicativo nessa instância. Esse status de instância é verificado pelo Azure para determinar se uma instância está qualificada para operações de atualização.

Como a extensão relata a integridade de dentro de uma VM, a extensão pode ser usada em situações em que investigações externas, como investigações de integridade do aplicativo (que utilizam [investigações](../load-balancer/load-balancer-custom-probe-overview.md)de Azure Load Balancer personalizadas), não podem ser usadas.

Há várias maneiras de implantar a extensão de integridade do aplicativo em seus conjuntos de dimensionamento, conforme detalhado nos exemplos [deste artigo](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Obter o histórico de atualizações automáticas de imagem do sistema operacional
Você pode verificar o histórico da atualização do sistema operacional mais recente executada em seu conjunto de dimensionamento com Azure PowerShell, CLI do Azure 2,0 ou as APIs REST. Você pode obter o histórico das últimas cinco tentativas de atualização do sistema operacional nos últimos dois meses.

### <a name="rest-api"></a>API REST
O exemplo a seguir usa a [API REST](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) para verificar o status do conjunto de dimensionamento chamado *myscaleset* no grupo de recursos chamado *MyResource*Group:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

A chamada GET retorna propriedades semelhantes à seguinte saída de exemplo:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) para verificar o histórico de atualização do sistema operacional para seu conjunto de dimensionamento. O exemplo a seguir fornece detalhes sobre como examinar o status de atualização do sistema operacional de um conjunto de dimensionamento chamado *Myscalemodeset* no grupo de recursos chamado *MyResource*Group:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Use [AZ vmss Get-os-upgrade-History](/cli/azure/vmss#az-vmss-get-os-upgrade-history) para verificar o histórico de atualização do sistema operacional de seu conjunto de dimensionamento. Use CLI do Azure 2.0.47 ou superior. O exemplo a seguir fornece detalhes sobre como examinar o status de atualização do sistema operacional de um conjunto de dimensionamento chamado *Myscalemodeset* no grupo de recursos chamado *MyResource*Group:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Como obter a versão mais recente de uma imagem do sistema operacional da plataforma?

Você pode obter as versões de imagem disponíveis para SKUs de atualização automática do so com suporte usando os exemplos abaixo:

### <a name="rest-api"></a>API REST
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Disparar manualmente atualizações de imagem do so
Com a atualização automática de imagem do sistema operacional habilitada no conjunto de dimensionamento, não é necessário disparar manualmente as atualizações de imagem no conjunto de dimensionamento. O orquestrador de atualização do so aplicará automaticamente a versão de imagem mais recente disponível às instâncias do conjunto de dimensionamento sem qualquer intervenção manual.

Para casos específicos em que você não deseja esperar que o orquestrador aplique a imagem mais recente, você pode disparar uma atualização de imagem do sistema operacional manualmente usando os exemplos abaixo.

> [!NOTE]
> O gatilho manual de atualizações de imagem do sistema operacional não fornece recursos de reversão automática. Se uma instância não recuperar sua integridade após uma operação de atualização, o disco do so anterior não poderá ser restaurado.

### <a name="rest-api"></a>API REST
Use a chamada à API de [atualização do sistema operacional](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) para iniciar uma atualização sem interrupção para mover todas as instâncias do conjunto de dimensionamento de máquinas virtuais para a versão mais recente do sistema operacional da imagem de plataforma disponível. As instâncias que já estão executando a versão mais recente do so disponível não são afetadas. O exemplo a seguir fornece detalhes sobre como você pode iniciar uma atualização do sistema operacional sem interrupção em um conjunto de dimensionamento chamado *Myscalemodeset* no grupo de recursos chamado *MyResource*Group:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) para verificar o histórico de atualização do sistema operacional para seu conjunto de dimensionamento. O exemplo a seguir fornece detalhes sobre como você pode iniciar uma atualização do sistema operacional sem interrupção em um conjunto de dimensionamento chamado *Myscalemodeset* no grupo de recursos chamado *MyResource*Group:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Use [AZ vmss upgradeing-upgrade Start](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) para verificar o histórico de atualização do sistema operacional de seu conjunto de dimensionamento. Use CLI do Azure 2.0.47 ou superior. O exemplo a seguir fornece detalhes sobre como você pode iniciar uma atualização do sistema operacional sem interrupção em um conjunto de dimensionamento chamado *Myscalemodeset* no grupo de recursos chamado *MyResource*Group:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Implantar com um modelo

Você pode usar modelos para implantar um conjunto de dimensionamento com atualizações automáticas do sistema operacional para imagens com suporte, como [Ubuntu 16, 4-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Passos seguintes
Para obter mais exemplos de como usar atualizações automáticas do sistema operacional com conjuntos de dimensionamento, examine o [repositório GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
