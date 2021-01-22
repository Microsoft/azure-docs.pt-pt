---
title: Upgrades automáticos de imagem de SO com conjuntos de escala de máquina virtual Azure
description: Saiba como atualizar automaticamente a imagem do SO em instâncias VM num conjunto de escala
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ff1a29577c0778d6ef88d3523c726f7a48739cdc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684615"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Atualizações automáticas da imagem do SO do conjunto de dimensionamento de máquinas virtuais do Azure

Permitir atualizações automáticas de imagens de SO no seu conjunto de escala ajuda a facilitar a gestão da atualização, atualizando de forma segura e automática o disco DE para todos os casos no conjunto de escala.

A atualização automática do SO tem as seguintes características:

- Uma vez configurada, a mais recente imagem de SO publicada pelos editores de imagem é automaticamente aplicada à escala definida sem a intervenção do utilizador.
- Atualiza os lotes de casos de forma rolante cada vez que uma nova imagem é publicada pela editora.
- Integra-se com sondas de saúde de aplicação e [extensão de Saúde de Aplicação.](virtual-machine-scale-sets-health-extension.md)
- Funciona para todos os tamanhos VM, e para imagens Windows e Linux.
- Pode optar por não fazer upgrades automáticos a qualquer momento (as atualizações de OS também podem ser iniciadas manualmente).
- O disco OS de um VM é substituído pelo novo DISCO OS criado com a versão de imagem mais recente. Extensões configuradas e scripts de dados personalizados são executados, enquanto os discos de dados persistidos são mantidos.
- [A sequência de extensão](virtual-machine-scale-sets-extension-sequencing.md) é suportada.
- A atualização automática de imagens de SO pode ser ativada num conjunto de escala de qualquer tamanho.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Como funciona a atualização automática de imagem so?

Uma atualização funciona substituindo o disco DE de um VM por um novo disco criado utilizando a versão de imagem mais recente. Quaisquer extensões configuradas e scripts de dados personalizados são executados no disco de SO, enquanto os discos de dados persistidos são mantidos. Para minimizar o tempo de inatividade da aplicação, as atualizações ocorrem em lotes, não mais de 20% da escala configurada a atualizar-se em qualquer momento. Também pode integrar uma sonda de saúde de aplicação Azure Load Balancer ou [extensão de Saúde de Aplicação.](virtual-machine-scale-sets-health-extension.md) Recomendamos incorporar um batimento cardíaco de aplicação e validar o sucesso de upgrade para cada lote no processo de upgrade.

O processo de atualização funciona da seguinte forma:
1. Antes de iniciar o processo de upgrade, o orquestrador garantirá que não mais de 20% das instâncias em toda a escala definida não são saudáveis (por qualquer motivo).
2. O orquestrador de upgrade identifica o lote de instâncias VM para atualizar, com qualquer lote com um máximo de 20% da contagem total de instâncias, sujeito a um tamanho mínimo de lote de uma máquina virtual.
3. O disco DE do lote selecionado de instâncias VM é substituído por um novo disco DE criado a partir da imagem mais recente. Todas as extensões e configurações especificadas no modelo de conjunto de escala são aplicadas à instância atualizada.
4. Para conjuntos de escala com sondas de saúde de aplicação configuradas ou extensão de Saúde de Aplicação, a atualização espera até 5 minutos para que o caso fique saudável, antes de passar a atualizar o próximo lote. Se uma instância não recuperar a sua saúde em 5 minutos após uma atualização, então, por padrão, o disco de so anterior, para o exemplo, é restaurado.
5. O orquestrador de upgrade também acompanha a percentagem de casos que se tornam insalubres após uma atualização. A atualização irá parar se mais de 20% das instâncias atualizadas ficarem insalubres durante o processo de upgrade.
6. O processo acima continua até que todas as instâncias do conjunto de escala tenham sido atualizadas.

A escala configurada os sistemas de atualização do sistema operativo verificam a saúde global da escala definida antes de atualizar cada lote. Ao atualizar um lote, pode haver outras atividades de manutenção planeadas ou não planeadas que possam afetar a saúde da sua escala. Nestes casos, se mais de 20% dos casos do conjunto de escalas ficarem insalubres, então a escala de atualização para no final do lote atual.

> [!NOTE]
>A atualização automática do SO não atualiza a imagem de referência Sku no conjunto de escala. Para alterar o Sku (tal como Ubuntu 16.04-LTS para 18.04-LTS), tem de atualizar o [modelo de conjunto de escala](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model) diretamente com a imagem desejada Sku. O editor de imagem e a oferta não podem ser alterados para um conjunto de escala existente.  

## <a name="supported-os-images"></a>Imagens de SO suportadas
Apenas algumas imagens da plataforma OS são suportadas atualmente. As imagens personalizadas [são suportadas](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images) se o conjunto de escalas utilizar imagens personalizadas através [da Galeria de Imagens Partilhadas.](../virtual-machines/shared-image-galleries.md)

As seguintes plataformas SKUs são atualmente suportadas (e mais são adicionadas periodicamente):

| Publisher               | Oferta de OS      |  Sku               |
|-------------------------|---------------|--------------------|
| Canónico               | UbuntuServer  | 16.04-LTS          |
| Canónico               | UbuntuServer  | 18.04-LTS          |
| OpenLogic               | CentOS        | 7,5                |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | Centro de Dados de 2016    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | Centro de Dados 2019 |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | Datacenter-Core-1903-with-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Requisitos para configurar a atualização automática de imagem de SO

- A propriedade da *versão* da imagem deve ser definida para o *mais tardar*.
- Utilize sondas de saúde de aplicação ou [extensão de saúde de aplicação](virtual-machine-scale-sets-health-extension.md) para conjuntos de escala de tecido não servido.
- Use a versão API compute 2018-10-01 ou superior.
- Certifique-se de que os recursos externos especificados no modelo de conjunto de escala estão disponíveis e atualizados. Exemplos incluem SAS URI para a carga útil de botas em propriedades de extensão VM, carga útil na conta de armazenamento, referência a segredos no modelo, e muito mais.
- Para conjuntos de escalas que utilizem máquinas virtuais Do Windows, a partir da versão API compute 2019-03-01, a propriedade *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* a propriedade deve ser *definida como falsa* na definição do modelo definido em escala. A propriedade acima permite atualizações in-VM onde "Windows Update" aplica patches do sistema operativo sem substituir o disco OS. Com atualizações automáticas de imagens DE Ativadas no seu conjunto de escala, não é necessária uma atualização adicional através do "Windows Update".

### <a name="service-fabric-requirements"></a>Requisitos de tecido de serviço

Se estiver a utilizar o Service Fabric, certifique-se de que estão reunidas as seguintes condições:
-   O [nível de durabilidade do](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) tecido de serviço é prata ou ouro, e não bronze.
-   A extensão do Tecido de Serviço na definição do modelo definidor de escala deve ter TypeHandlerVersion 1.1 ou superior.
-   O nível de durabilidade deve ser o mesmo na extensão do cluster de tecido de serviço e do tecido de serviço na definição do modelo definido em escala.
- Não é necessária uma sonda de saúde adicional ou a utilização de uma extensão de saúde da aplicação.

Certifique-se de que as definições de durabilidade não são desajustadas na extensão do cluster de tecido de serviço e do tecido de serviço, uma vez que uma incompatibilidade resultará em erros de atualização. Os níveis de durabilidade podem ser modificados de acordo com as diretrizes descritas [nesta página.](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)


## <a name="automatic-os-image-upgrade-for-custom-images"></a>Atualização automática de imagem do SO para imagens personalizadas

A atualização automática de imagens de SO é suportada para imagens personalizadas implementadas através [da Galeria de Imagens Partilhadas.](../virtual-machines/shared-image-galleries.md) Outras imagens personalizadas não são suportadas para atualizações automáticas de imagem de SO.

### <a name="additional-requirements-for-custom-images"></a>Requisitos adicionais para imagens personalizadas
- O processo de configuração e configuração para a atualização automática de imagem de SO é o mesmo para todos os conjuntos de escala como detalhado na secção de [configuração](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) desta página.
- Os conjuntos de escalas configurados para atualizações automáticas de imagens de SO serão atualizados para a versão mais recente da imagem da Galeria de Imagens Partilhadas quando uma nova versão da imagem for publicada e [replicada](../virtual-machines/shared-image-galleries.md#replication) para a região desse conjunto de escala. Se a nova imagem não for replicada na região onde a escala é implantada, as instâncias definidas em escala não serão atualizadas para a versão mais recente. A replicação de imagem regional permite-lhe controlar o lançamento da nova imagem para os seus conjuntos de escala.
- A nova versão de imagem não deve ser excluída da versão mais recente para aquela imagem da galeria. As versões de imagem excluídas da versão mais recente da imagem da galeria não são lançadas para a escala definida através de uma atualização automática de imagens OS.

> [!NOTE]
>Pode levar até 3 horas para uma escala definida para desencadear o primeiro lançamento de atualização de imagem depois de o conjunto de escala ser configurado pela primeira vez para atualizações automáticas de SO. Este é um atraso único por escala definida. Os lançamentos de imagem subsequentes são ativados na escala definida dentro de 30-60 minutos.


## <a name="configure-automatic-os-image-upgrade"></a>Configurar atualização automática de imagem de OS
Para configurar a atualização automática de imagem de SO, certifique-se de que a propriedade *automáticaOSUpgradePolicy.enableAutomaticOSUpgrade* property is set to *true* in the scale set model definition.

### <a name="rest-api"></a>API REST
O exemplo a seguir descreve como definir atualizações automáticas de SO num modelo de conjunto de escala:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
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
Utilize o cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) para configurar atualizações automáticas de imagem DE SO para o seu conjunto de escala. O exemplo a seguir configura atualizações automáticas para o conjunto de escala denominado *myScaleSet* no grupo de recursos denominado *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Utilize [a atualização az vmss](/cli/azure/vmss#az-vmss-update) para configurar atualizações automáticas de imagem de SO para o seu conjunto de escala. Utilize O Azure CLI 2.0.47 ou superior. O exemplo a seguir configura atualizações automáticas para o conjunto de escala denominado *myScaleSet* no grupo de recursos denominado *myResourceGroup*:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>Depois de configurar atualizações automáticas de imagem de SO para o seu conjunto de escala, também deve levar os VMs definidos à escala mais recente se o seu conjunto de escala utilizar a política de [atualização](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)'Manual'.

## <a name="using-application-health-probes"></a>Utilização de sondas de saúde de aplicação

Durante uma atualização do SISTEMA, as instâncias VM num conjunto de escala são atualizadas um lote de cada vez. A atualização só deverá continuar se a aplicação do cliente for saudável nas instâncias VM atualizadas. Recomendamos que a aplicação forneça sinais de saúde ao motor de upgrade do sistema operativo de escala. Por padrão, durante as atualizações do OS, a plataforma considera o estado de potência vM e o estado de extensão do estado para determinar se uma instância VM é saudável após uma atualização. Durante a atualização do SISTEMA de um exemplo VM, o disco DE numa instância VM é substituído por um novo disco baseado na versão de imagem mais recente. Após a conclusão da atualização do SISTEMA, as extensões configuradas são executadas nestes VMs. O pedido só é considerado saudável quando todas as extensões do caso forem previstas com sucesso.

Um conjunto de escala pode opcionalmente ser configurado com Sondas de Saúde de Aplicação para fornecer à plataforma informações precisas sobre o estado em curso da aplicação. As sondas de saúde da aplicação são sondas de balanço de carga personalizadas que são usadas como um sinal de saúde. A aplicação em execução em uma instância VM definida em escala pode responder a pedidos externos de HTTP ou TCP indicando se é saudável. Para obter mais informações sobre o funcionaamento das sondas de balanço de carga personalizada, consulte [para compreender as sondas do balançador de carga](../load-balancer/load-balancer-custom-probe-overview.md). As sondas de saúde da aplicação não são suportadas para conjuntos de escala de tecido de serviço. Os conjuntos de escala de tecido não servido requerem sondas de saúde de aplicação de balanceadores de carga ou [extensão de Saúde de Aplicação](virtual-machine-scale-sets-health-extension.md).

Se o conjunto de balança estiver configurado para utilizar vários grupos de colocação, devem ser utilizadas sondas utilizando um [Balanceador de Carga Padrão.](../load-balancer/load-balancer-overview.md)

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configurar uma sonda de balanço de carga personalizada como sonda de saúde de aplicação em um conjunto de escala
Como uma boa prática, crie uma sonda de equilíbrio de carga explicitamente para a saúde definida em escala. O mesmo ponto final para uma sonda HTTP ou sonda TCP existente pode ser usado, mas uma sonda de saúde pode exigir um comportamento diferente de uma sonda tradicional de equilíbrio de carga. Por exemplo, uma sonda de balanço de carga tradicional poderia voltar pouco saudável se a carga no caso fosse demasiado elevada, mas isso não seria apropriado para determinar a saúde do caso durante uma atualização automática do SO. Configufique a sonda para ter uma alta taxa de sondagem de menos de dois minutos.

A sonda de compensação de carga pode ser referenciada na *redeProfile* do conjunto de escala e pode ser associada a um equilibrador de carga interno ou público da seguinte forma:

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
> Ao utilizar atualizações automáticas de OS com Tecido de Serviço, a nova imagem de SO é lançada domínio de atualização por Atualização do Domínio para manter uma elevada disponibilidade dos serviços em execução no Tecido de Serviço. Para utilizar atualizações automáticas de OS no Tecido de Serviço, o seu cluster deve ser configurado para utilizar o Nível de Durabilidade prateada ou superior. Para obter mais informações sobre as características de durabilidade dos clusters de Tecidos de Serviço, consulte [esta documentação.](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)

### <a name="keep-credentials-up-to-date"></a>Manter as credenciais atualizadas
Se o seu conjunto de escalas utilizar quaisquer credenciais para aceder a recursos externos, como uma extensão VM configurada para usar um token SAS para conta de armazenamento, então certifique-se de que as credenciais são atualizadas. Se alguma credencial, incluindo certificados e fichas, tiver expirado, a atualização falhará e o primeiro lote de VMs será deixado num estado de falha.

As medidas recomendadas para recuperar VMs e re-activar a atualização automática de SO se houver uma falha de autenticação de recursos são:

* Regenerar o símbolo (ou quaisquer outras credenciais) passado para a sua extensão( s).
* Certifique-se de que qualquer credencial utilizada de dentro do VM para falar com entidades externas está atualizada.
* Atualizar extensões no modelo de conjunto de escala com quaisquer fichas novas.
* Implemente o conjunto de escalas atualizados, que atualizará todas as instâncias VM, incluindo as falhadas.

## <a name="using-application-health-extension"></a>Utilização da extensão de saúde da aplicação
A extensão de Saúde da Aplicação é implantada dentro de uma instância de conjunto de escala de máquina virtual e relatórios sobre a saúde VM de dentro da instância definida em escala. Pode configurar a extensão para sondar um ponto final da aplicação e atualizar o estado da aplicação nesse caso. Este estado de instância é verificado pela Azure para determinar se um caso é elegível para operações de upgrade.

Como a extensão informa a saúde de dentro de um VM, a extensão pode ser usada em situações em que sondas externas como Sondas de Saúde de Aplicação (que utilizam sondas de balanço de carga Azure personalizadas) não podem ser [usadas.](../load-balancer/load-balancer-custom-probe-overview.md)

Existem várias formas de implementar a extensão de Saúde de Aplicação à sua escala, conforme detalhado nos exemplos [deste artigo.](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Obtenha o histórico de atualizações automáticas de imagem de SO
Pode verificar o histórico da mais recente atualização de SO realizada na sua escala definida com Azure PowerShell, Azure CLI 2.0 ou as APIs REST REST. Pode obter história nas últimas cinco tentativas de atualização do SISTEMA nos últimos dois meses.

### <a name="rest-api"></a>API REST
O exemplo a seguir utiliza [a API REST](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) para verificar o estado do conjunto de escala denominado *myScaleSet* no grupo de recursos denominado *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

A chamada GET devolve propriedades semelhantes à saída de exemplo a seguir:

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
Utilize o [cmdlet Get-AzVmss](/powershell/module/az.compute/get-azvmss) para verificar o histórico de atualização do SISTEMA para o seu conjunto de escala. O exemplo a seguir detalha como revê o estado de atualização do SISTEMA para um conjunto de escala denominado *myScaleSet* no grupo de recursos denominado *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Utilize [o histórico de upgrade do az vmss](/cli/azure/vmss#az-vmss-get-os-upgrade-history) para verificar o histórico de atualização do SISTEMA para o seu conjunto de escala. Utilize O Azure CLI 2.0.47 ou superior. O exemplo a seguir detalha como revê o estado de atualização do SISTEMA para um conjunto de escala denominado *myScaleSet* no grupo de recursos denominado *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Como obter a versão mais recente de uma imagem de sistema operativo da plataforma?

Pode obter as versões de imagem disponíveis para SKUs suportados por upgrade automático de SO utilizando os exemplos abaixo:

### <a name="rest-api"></a>API REST
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Detonar manualmente atualizações de imagem do SO
Com a atualização automática de imagem SO ativada no seu conjunto de escala, não precisa de ativar manualmente atualizações de imagem no seu conjunto de escala. O orquestrador de upgrade de SISTEMA aplicará automaticamente a versão de imagem mais recente disponível nas suas instâncias definidas em escala sem qualquer intervenção manual.

Para casos específicos em que não queira esperar que o orquestrador aplique a imagem mais recente, pode desencadear manualmente uma atualização de imagem do SO utilizando os exemplos abaixo.

> [!NOTE]
> O gatilho manual das atualizações de imagem do SO não fornece capacidades automáticas de reversão. Se um caso não recuperar a sua saúde após uma operação de atualização, o disco de so anterior não pode ser restaurado.

### <a name="rest-api"></a>API REST
Utilize a chamada API de [Atualização do Sistema operativo Start PARA](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) iniciar uma atualização rolante para mover todas as instâncias de escala de máquina virtual para a versão oss de imagem mais recente disponível. Os casos que já estão a executar a mais recente versão de SO disponível não são afetados. O exemplo a seguir detalha como pode iniciar uma atualização de SISTEMA rolante num conjunto de escala denominado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Utilize o [cmdlet Start-AzVmsSRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) para verificar o histórico de atualização do SISTEMA para o seu conjunto de escala. O exemplo a seguir detalha como pode iniciar uma atualização de SISTEMA rolante num conjunto de escala denominado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Utilize [o início de atualização de az vmss](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) para verificar o histórico de atualização do SISTEMA para o seu conjunto de escala. Utilize O Azure CLI 2.0.47 ou superior. O exemplo a seguir detalha como pode iniciar uma atualização de SISTEMA rolante num conjunto de escala denominado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Implementar com um modelo

Pode utilizar modelos para implementar uma escala definida com atualizações automáticas de SO para imagens suportadas como [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

## <a name="next-steps"></a>Passos seguintes
Para obter mais exemplos sobre como utilizar upgrades automáticos de SO com conjuntos de escala, reveja o [repo GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).