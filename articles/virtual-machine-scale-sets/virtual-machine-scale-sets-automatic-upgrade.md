---
title: Conjuntos de dimensionamento de atualizações automáticas de imagem de SO com a máquina virtual do Azure | Documentos da Microsoft
description: Saiba como atualizar automaticamente a imagem do SO nas instâncias VM num conjunto de dimensionamento
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: manayar
ms.openlocfilehash: 779a10257a94ed761572fdcc80a68059750342dd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57994463"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Atualizações automáticas da imagem de SO do conjunto de dimensionamento de máquina virtual do Azure

Ativar a imagem do SO automática é atualizado no seu gerenciamento de atualizações do dimensionamento conjunto ajuda a facilidade atualizando com segurança e automaticamente o disco do SO para todas as instâncias no conjunto de dimensionamento.

Atualização automática de SO tem as seguintes características:

- Depois de configurada, a imagem de SO mais recente, publicada por editores de imagem é aplicada automaticamente para o conjunto sem intervenção do utilizador de dimensionamento.
- Atualiza lotes de instâncias de uma maneira sem interrupção sempre que uma nova imagem de plataforma é publicada pelo editor.
- Integra-se com as sondas de estado de funcionamento do aplicativo e [extensão de estado de funcionamento do aplicativo](virtual-machine-scale-sets-health-extension.md).
- Funciona para todos os tamanhos VM e para imagens de plataforma do Windows e Linux.
- Pode desativar as atualizações automáticas em qualquer altura (atualizações de SO podem ser iniciadas manualmente também).
- O disco do SO de uma VM é substituído pelo novo disco de SO criado com a versão mais recente da imagem. Extensões configuradas e scripts de dados personalizados são executados ao mesmo tempo são retidos discos de dados persistentes.
- [Sequenciamento de extensão](virtual-machine-scale-sets-extension-sequencing.md) é suportada.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Como a SO automática trabalho de atualização de imagens?

Uma atualização funciona ao substituir o disco do SO de uma VM um novo disco criado com a versão mais recente da imagem. Quaisquer extensões configuradas e scripts de dados personalizados são executados no disco do SO, ao mesmo tempo são retidos discos de dados persistentes. Para minimizar o período de indisponibilidade de aplicação, as atualizações ocorrem em lotes, com mais do que 20% da escala definida a atualizar em qualquer altura. Também pode integrar uma sonda de estado de funcionamento da aplicação de Balanceador de carga do Azure ou [extensão de estado de funcionamento do aplicativo](virtual-machine-scale-sets-health-extension.md). É recomendado incorporar um heartbeat do aplicativo e validar o êxito da atualização para cada lote no processo de atualização.

O processo de atualização funciona da seguinte forma:
1. Antes de iniciar o processo de atualização, o orchestrator será Certifique-se de que não mais do que 20% das instâncias no conjunto de dimensionamento inteiro são mau estado de funcionamento (por qualquer motivo).
2. O atualização orchestrator identifica o lote de instâncias de VM para atualizar, com qualquer um lote de ter um máximo de 20% da contagem de instâncias total.
3. O disco do SO do lote selecionado de instâncias de VM é substituído por um novo disco de SO criado a partir da imagem mais recente e todas as extensões especificadas e configurações no modelo de conjunto de dimensionamento são aplicadas a instância atualizada.
4. Para conjuntos de dimensionamento com a extensão de estado de funcionamento do aplicativo ou de sondas de estado de funcionamento da aplicação configurada, a atualização tem de aguardar até 5 minutos para a instância para se tornar íntegros, antes de passar para atualizar o lote seguinte.
5. O orquestrador de atualização também controla a percentagem de instâncias que se tornam mau estado de funcionamento após uma atualização. A atualização será interrompida se mais de 20% das instâncias atualizadas de funcionamento incorreto durante o processo de atualização.
6. O processo acima continua até que todas as instâncias no conjunto de dimensionamento foram atualizadas.

O conjunto de dimensionamento verificações de orchestrator de atualização de SO para o estado de funcionamento de conjunto de dimensionamento global antes de atualizar todos os lotes. Durante a atualização de um lote, pode haver outras atividades simultâneas de manutenção planeado ou que podem afetar o estado de funcionamento das suas instâncias de conjunto de dimensionamento. Em tais casos se mais do que 20% das instâncias do conjunto de dimensionamento de funcionamento incorreto, em seguida, o conjunto de dimensionamento paradas de atualização no final do lote atual.

## <a name="supported-os-images"></a>Imagens de SO suportadas
Atualmente são suportadas apenas determinadas imagens de plataforma de SO. Imagens personalizadas não são atualmente suportadas.

Os SKUs seguintes são atualmente suportados (e mais são adicionadas periodicamente):

| Publicador               | Oferta de SO      |  Sku               |
|-------------------------|---------------|--------------------|
| Canónico               | UbuntuServer  | 16.04-LTS          |
| Canónico               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Estável             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-with-Containers |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Requisitos para configurar a atualização automática de imagem do SO

- O *versão* propriedade da imagem de plataforma tem de ser definida como *mais recente*.
- Utilizar sondas de estado de funcionamento da aplicação ou [extensão de estado de funcionamento do aplicativo](virtual-machine-scale-sets-health-extension.md) para dimensionamento não Service Fabric conjuntos.
- Certifique-se de que os recursos externos especificados no modelo de conjunto de dimensionamento estão disponíveis e atualizados. Exemplos incluem o URI de SAS para efetuar o arranque do payload nas propriedades de extensão VM, payload na conta de armazenamento, de referência para segredos no modelo e muito mais.

## <a name="configure-automatic-os-image-upgrade"></a>Configurar a atualização automática de imagem do SO
Para configurar a atualização automática de imagem do SO, certifique-se de que o *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* estiver definida como *verdadeiro* na escala de definição de modelo do conjunto.

### <a name="rest-api"></a>API REST
O exemplo a seguir descreve como configurar atualizações automáticas de SO num modelo de conjunto de dimensionamento:

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
Utilize o [AzVmss atualização](/powershell/module/az.compute/update-azvmss) cmdlet para verificar o histórico de atualização de SO para o conjunto de dimensionamento. O exemplo seguinte configura as atualizações automáticas para o conjunto nomeado de dimensionamento *myVMSS* no grupo de recursos com o nome *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Uso [az vmss update](/cli/azure/vmss#az-vmss-update) para verificar o histórico de atualização de SO para o conjunto de dimensionamento. Utilizar a CLI do Azure 2.0.47 ou superior. O exemplo seguinte configura as atualizações automáticas para o conjunto nomeado de dimensionamento *myVMSS* no grupo de recursos com o nome *myResourceGroup*:

```azurecli-interactive
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Sondas de estado de funcionamento da aplicação a utilizar

Durante uma atualização de SO, instâncias de VM num conjunto de dimensionamento são atualizadas um lote a cada vez. A atualização deve continuar apenas se a aplicação de cliente está em bom estada nas instâncias de VM atualizadas. Recomendamos que o aplicativo fornece sinais de estado de funcionamento para o mecanismo de atualização de SO de conjunto de dimensionamento. Por predefinição, durante as atualizações de SO a plataforma considera o estado de energia VM e para determinar se uma instância de VM está em bom estada após uma atualização, o estado de aprovisionamento de extensão. Durante a atualização de SO de uma instância VM, o disco do SO numa instância VM é substituído por um novo disco com base na versão mais recente da imagem. Depois de concluída a atualização do sistema operacional, as extensões configuradas são executadas nestas VMS. O aplicativo é considerado em bom estado, apenas quando todas as extensões na instância estão aprovisionadas com êxito.

Opcionalmente, pode ser configurado um conjunto de dimensionamento com sondas de estado de funcionamento da aplicação para fornecer a plataforma com informações precisas sobre o estado atual do aplicativo. Sondas de estado de funcionamento do aplicativo são personalizado Load Balancer sonda que são utilizadas como um sinal de estado de funcionamento. O aplicativo em execução numa instância VM do conjunto de dimensionamento pode responder a pedidos HTTP ou TCP externos, que indica se está em bom estado. Para obter mais informações sobre como funcionam os sondas do Balanceador de carga personalizado, consulte a [as sondas do Balanceador de carga compreender](../load-balancer/load-balancer-custom-probe-overview.md). Uma sonda de estado de funcionamento de aplicação não é necessário para conjuntos de dimensionamento do Service Fabric, mas recomenda-se. Conjuntos de dimensionamento de recursos de infraestrutura de não-Service necessitam de sondas de estado de funcionamento da aplicação de qualquer Balanceador de carga ou [extensão de estado de funcionamento do aplicativo](virtual-machine-scale-sets-health-extension.md).

Se o conjunto de dimensionamento está configurado para utilizar vários grupos de colocação, sondas usando um [Balanceador de carga Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) tenha de ser utilizada.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configurar uma sonda de Balanceador de carga do personalizada como a sonda de estado de funcionamento do aplicativo numa escala definido
Como melhor prática, crie uma sonda de Balanceador de carga explicitamente para o estado de funcionamento do conjunto de dimensionamento. Pode ser utilizado o mesmo ponto final para uma sonda HTTP existente ou uma sonda TCP, mas uma sonda de estado de funcionamento pode exigir um comportamento diferente de uma sonda de Balanceador de carga tradicional. Por exemplo, uma sonda de Balanceador de carga tradicional poderia retornar mau estado de funcionamento se a carga na instância é demasiado elevada, mas que não poderá ser adequado para determinar o estado de funcionamento da instância durante uma atualização de SO automática. Configure a sonda de ter uma elevada taxa de pesquisa de menos de dois minutos.

A sonda de Balanceador de carga pode ser referenciada a *networkProfile* da escala definida e pode ser associado a qualquer um dos interno ou público com acesso-balanceadores de carga da seguinte forma:

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
> Ao utilizar as atualizações automáticas de SO com o Service Fabric, a nova imagem de sistema operacional é lançada domínio de atualização por domínio de atualização para manter uma elevada disponibilidade dos serviços em execução no Service Fabric. Para utilizar as atualizações automáticas de SO no Service Fabric cluster tem de ser configurado para utilizar o escalão de durabilidade Silver ou superior. Para obter mais informações sobre as características de durabilidade de clusters do Service Fabric, veja [esta documentação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Manter credenciais atualizados
Se o conjunto de dimensionamento utiliza quaisquer credenciais para aceder a recursos externos, por exemplo, se estiver configurada uma extensão de VM que utiliza um token SAS para a conta de armazenamento, certifique-se de que as credenciais são atualizadas. Se quaisquer credenciais, incluindo certificados e tokens, tiverem expirado, a atualização irá falhar e o primeiro batch de VMs será deixado no estado de falha.

Os passos recomendados para recuperar as VMs e volte a ativar a atualização automática do SO, se houver uma falha de autenticação de recursos são:

* Voltar a gerar o token (ou quaisquer outras credenciais) passados para sua extensão ou extensões.
* Certifique-se de que qualquer credencial utilizada de dentro da VM para comunicar com entidades externas está atualizada.
* Atualize extensão ou extensões de no modelo de conjunto de dimensionamento com quaisquer tokens de novo.
* Implemente o conjunto de dimensionamento atualizada, o qual irá atualizar todas as instâncias de VM, incluindo aqueles com falha.

## <a name="using-application-health-extension"></a>Com a extensão de estado de funcionamento da aplicação
A extensão de estado de funcionamento da aplicação é implementada no interior de uma instância de conjunto de dimensionamento de máquina virtual e relatórios sobre o estado de funcionamento VM de dentro da instância de conjunto de dimensionamento. Pode configurar a extensão para pesquisa num ponto de extremidade do aplicativo e atualizar o estado do aplicativo nessa instância. Este estado de instância é verificado pelo Azure para determinar se uma instância é elegível para operações de atualização.

Como o extensão relatórios Estado de funcionamento de dentro de uma VM, a extensão pode ser utilizada em situações onde externo as sondas como sondas de estado de funcionamento do aplicativo (que utilizam o Balanceador de carga do Azure personalizadas [sondas](../load-balancer/load-balancer-custom-probe-overview.md)) não pode ser utilizado.

Existem várias formas de implementar o estado de funcionamento do aplicativo extensão para seu dimensionamento define conforme explicado nos exemplos [este artigo](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Obter o histórico de atualizações automáticas da imagem de SO
Pode verificar o histórico de atualização de SO mais recente efetuada no seu conjunto de dimensionamento com o Azure PowerShell, CLI 2.0 do Azure ou as APIs REST. Pode obter o histórico para as tentativas de atualização de SO últimos cinco nos últimos dois meses.

### <a name="rest-api"></a>API REST
O exemplo seguinte utiliza [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) para verificar o estado para o conjunto nomeado de dimensionamento *myVMSS* no grupo de recursos com o nome *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

A chamada GET retorna propriedades semelhantes à saída de exemplo seguinte:

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
Utilize o [Get-AzVmss](/powershell/module/az.compute/get-azvmss) cmdlet para verificar o histórico de atualização de SO para o conjunto de dimensionamento. O exemplo a seguir detalha como rever o estado de atualização de SO para um conjunto nomeado de dimensionamento *myVMSS* no grupo de recursos com o nome *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Uso [az vmss get-SO--histórico de atualizações do](/cli/azure/vmss#az-vmss-get-os-upgrade-history) para verificar o histórico de atualização de SO para o conjunto de dimensionamento. Utilizar a CLI do Azure 2.0.47 ou superior. O exemplo a seguir detalha como rever o estado de atualização de SO para um conjunto nomeado de dimensionamento *myVMSS* no grupo de recursos com o nome *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Como obter a versão mais recente de uma imagem de plataforma de SO?

Pode obter as versões de imagem para os SKUs suportados através de atualização de SO automática a seguir exemplos:

### <a name="rest-api"></a>API REST
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Implementar com um modelo

Pode usar modelos para implementar um conjunto de dimensionamento com as atualizações automáticas de SO para imagens suportadas, como [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Passos Seguintes
Para obter mais exemplos sobre como utilizar as atualizações automáticas de SO com conjuntos de dimensionamento, reveja os [repositório do GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
