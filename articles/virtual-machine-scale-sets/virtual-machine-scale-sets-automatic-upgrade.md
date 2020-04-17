---
title: Atualizações automáticas de imagem do OS com conjuntos de escala de máquinas virtuais Azure
description: Aprenda a atualizar automaticamente a imagem de OS em instâncias VM num conjunto de escala
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mimckitt
ms.openlocfilehash: 70810b21def1672758683abd49f92b86776c9d7b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458986"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>A escala de máquina virtual Azure define atualizações automáticas de imagem de OS

Permitir atualizações automáticas de imagem de OS no seu conjunto de escala ajuda a facilitar a gestão da atualização, atualizando de forma segura e automática o disco OS para todas as instâncias do conjunto de escala.

A atualização automática do OS tem as seguintes características:

- Uma vez configurada, a mais recente imagem de OS publicada pelos editores de imagem é automaticamente aplicada à escala definida sem a intervenção do utilizador.
- Atualiza os lotes de instâncias de forma rolante cada vez que uma nova imagem é publicada pela editora.
- Integra-se com sondas de saúde de aplicação e extensão de saúde de [aplicação.](virtual-machine-scale-sets-health-extension.md)
- Funciona para todos os tamanhos vm, e para imagens Windows e Linux.
- Pode optar por não fazer upgrades automáticos a qualquer momento (as atualizações de OS podem ser iniciadas manualmente também).
- O Disco OS de um VM é substituído pelo novo Disco OS criado com a versão mais recente da imagem. As extensões configuradas e os scripts de dados personalizados são executados, enquanto os discos de dados persistidos são mantidos.
- [A sequenciação](virtual-machine-scale-sets-extension-sequencing.md) da extensão é suportada.
- A atualização automática da imagem do OS pode ser ativada num conjunto de escala de qualquer tamanho.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Como funciona a atualização automática de imagem do OS?

Uma atualização funciona substituindo o disco OS de um VM por um novo disco criado utilizando a versão mais recente da imagem. Quaisquer extensões configuradas e scripts de dados personalizados são executados no disco OS, enquanto os discos de dados persistidos são retidos. Para minimizar o tempo de inatividade da aplicação, as atualizações ocorrem em lotes, com não mais de 20% da escala configurada em qualquer momento. Também pode integrar uma sonda de saúde de aplicação Azure Load Balancer ou extensão de saúde de [aplicação.](virtual-machine-scale-sets-health-extension.md) Recomendamos incorporar um batimento cardíaco de aplicação e validar o sucesso de upgrade para cada lote no processo de upgrade.

O processo de atualização funciona da seguinte forma:
1. Antes de iniciar o processo de atualização, o orquestrador garantirá que não mais de 20% dos casos em toda a escala não são saudáveis (por qualquer motivo).
2. O orquestrador de upgrade identifica o lote de instâncias VM para atualizar, com qualquer lote com um máximo de 20% da contagem total de instâncias, sujeito a um tamanho mínimo de lote de uma máquina virtual.
3. O disco OS do lote selecionado de instâncias VM é substituído por um novo disco OS criado a partir da imagem mais recente. Todas as extensões e configurações especificadas no modelo de conjunto de escala são aplicadas à instância atualizada.
4. Para conjuntos de escala com sondas de saúde de aplicação configuradas ou extensão de saúde de aplicação, a atualização aguarda até 5 minutos para que a instância fique saudável, antes de avançar para a atualização do próximo lote. Se uma instância não recuperar a sua saúde em 5 minutos após uma atualização, então, por padrão, o disco de SO anterior para a instância é restaurado.
5. O orquestrador de upgrade também rastreia a percentagem de casos que se tornam insalubres após uma atualização. A atualização irá parar se mais de 20% das instâncias atualizadas ficarem insalubres durante o processo de atualização.
6. O processo acima continua até que todas as instâncias do conjunto de escala tenham sido atualizadas.

A escala definiu os controlos de atualização de osso para a escala geral definir a saúde antes de atualizar cada lote. Durante a atualização de um lote, pode haver outras atividades de manutenção planeadas ou não planeadas simultâneas que possam afetar a saúde das instâncias definidas pela sua escala. Nesses casos, se mais de 20% das instâncias do conjunto de escala se tornarem insalubres, então a atualização definida pela escala para no final do lote atual.

## <a name="supported-os-images"></a>Imagens de Os suportadas
Atualmente, apenas algumas imagens da plataforma OS são suportadas. O suporte personalizado de imagem está disponível [na pré-visualização](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images-preview) para imagens personalizadas através [da Galeria de Imagem Partilhada.](shared-image-galleries.md)

As seguintes plataformas SKUs são atualmente suportadas (e mais são adicionadas periodicamente):

| Publicador               | Oferta de OS      |  Sku               |
|-------------------------|---------------|--------------------|
| Canónico               | UbuntuServer  | 16.04-LTS          |
| Canónico               | UbuntuServer  | 18.04-LTS          |
| Onda Desonesto (OpenLogic)  | CentOS        | 7,5                |
| CoreOS                  | CoreOS        | Estável             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | Centro de Dados 2016    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | Centro de Dados 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | Datacenter-Core-1903-with-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Requisitos para configurar atualização automática de imagem do OS

- A propriedade da *versão* da imagem deve ser definida para o *mais recente*.
- Utilize sondas de saúde de aplicação ou extensão de saúde de [aplicação](virtual-machine-scale-sets-health-extension.md) para conjuntos de escala de tecido não-serviço.
- Utilize a versão Compute API 2018-10-01 ou superior.
- Certifique-se de que os recursos externos especificados no modelo de conjunto de escala estão disponíveis e atualizados. Exemplos incluem SAS URI para a carga útil de botas em propriedades de extensão VM, carga útil na conta de armazenamento, referência a segredos no modelo, e muito mais.
- Para conjuntos de escala utilizando máquinas virtuais do Windows, começando com a versão Compute API 2019-03-01, a propriedade *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* property deve definir *para falso* na definição de modelo definido em escala. A propriedade acima permite atualizações in-VM onde "Windows Update" aplica patches do sistema operativo sem substituir o disco OS. Com atualizações automáticas de imagem de OS ativadas no seu conjunto de escala, não é necessária uma atualização adicional através do "Windows Update".

### <a name="service-fabric-requirements"></a>Requisitos de Tecido de Serviço

Se estiver a utilizar o Tecido de Serviço, certifique-se de que estão reunidas as seguintes condições:
-   Serviço O [nível de durabilidade](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) do tecido é prata ou ouro, e não bronze.
-   A extensão do tecido de serviço na definição do modelo de conjunto de escala deve ter TypeHandlerVersion 1.1 ou superior.
-   O nível de durabilidade deve ser o mesmo no cluster de tecido de serviço e na extensão do tecido de serviço na definição do modelo de conjunto de escala.

Certifique-se de que as definições de durabilidade não são desajustadas no cluster de tecido de serviço e na extensão do tecido de serviço, uma vez que uma incompatibilidade resultará em erros de atualização. Os níveis de durabilidade podem ser modificados de acordo com as diretrizes descritas [nesta página](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).


## <a name="automatic-os-image-upgrade-for-custom-images-preview"></a>Upgrade automático de imagem osso para imagens personalizadas (pré-visualização)

> [!IMPORTANT]
> O upgrade automático de imagem do OS para imagens personalizadas está atualmente em Visualização Pública. É necessário um procedimento de opt-in para utilizar a funcionalidade de pré-visualização pública descrita abaixo.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

A atualização automática de imagem do OS está disponível na pré-visualização para imagens personalizadas implementadas através da [Galeria de Imagem Partilhada](shared-image-galleries.md). Outras imagens personalizadas não são suportadas para upgrades automáticos de imagem de OS.

Ativar a funcionalidade de pré-visualização requer um opt-in único para a funcionalidade *AutomaticOSUpgradeWithGalleryImage* por subscrição, conforme detalhado abaixo.

### <a name="rest-api"></a>API REST
O exemplo que se segue descreve como ativar a pré-visualização para a sua subscrição:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage/register?api-version=2015-12-01`
```

O registo de funcionalidades pode demorar até 15 minutos. Para verificar o estado de registo:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage?api-version=2015-12-01`
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração para o fornecedor de recursos Compute.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Utilize o [cmdlet Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para ativar a pré-visualização da sua subscrição.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

O registo de funcionalidades pode demorar até 15 minutos. Para verificar o estado de registo:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração para o fornecedor de recursos Compute.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Utilize o registo de [funcionalidades Az](/cli/azure/feature#az-feature-register) para ativar a pré-visualização da sua subscrição.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

O registo de funcionalidades pode demorar até 15 minutos. Para verificar o estado de registo:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração para o fornecedor de recursos Compute.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

### <a name="additional-requirements-for-custom-images"></a>Requisitos adicionais para imagens personalizadas
- O processo de opt-in acima descrito deve ser concluído apenas uma vez por subscrição. A realização de pós-opt-in, atualizações automáticas de SO podem ser ativadas para qualquer escala definida nessa subscrição.
- A Galeria de Imagem Partilhada pode estar em qualquer subscrição e não precisa de ser optada separadamente. Apenas a subscrição definida em escala requer o opt-in da funcionalidade.
- O processo de configuração para a atualização automática da imagem do OS é o mesmo para todos os conjuntos de escala, conforme detalhado na secção de [configuração](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) desta página.
- A escala define as instâncias configuradas para atualizações automáticas de imagem de OS serão atualizadas para a versão mais recente da imagem partilhada da Imagem Gallery quando uma nova versão da imagem for publicada e [replicada](shared-image-galleries.md#replication) para a região desse conjunto de escala. Se a nova imagem não for replicada para a região onde a escala é implantada, as instâncias de conjunto de escala não serão atualizadas para a versão mais recente. A replicação regional da imagem permite controlar o lançamento da nova imagem para os seus conjuntos de escala.
- A nova versão de imagem não deve ser excluída da versão mais recente para aquela imagem da galeria. As versões de imagem excluídas da versão mais recente da imagem da galeria não são lançadas para a escala definida através de atualização automática de imagem do OS.

> [!NOTE]
>Pode levar até 3 horas para uma escala definida para desencadear o lançamento da primeira atualização de imagem após o conjunto de escala estar configurado para atualizações automáticas de SO. Este é um atraso único por conjunto de escala. Os lançamentos de imagem subsequentes são ativados na balança definida dentro de 30 minutos.


## <a name="configure-automatic-os-image-upgrade"></a>Configure atualização automática de imagem do OS
Para configurar a atualização automática da imagem do OS, certifique-se de que a propriedade *automáticaOSUpgrade.enableOsUpgrade* está definida como *verdadeira* na definição do modelo de conjunto de escala.

### <a name="rest-api"></a>API REST
O exemplo seguinte descreve como definir atualizações automáticas de SO num modelo de conjunto de escala:

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
Utilize o cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) para configurar atualizações automáticas de imagem de OS para o seu conjunto de escala. O exemplo seguinte configura atualizações automáticas para o conjunto de escala denominado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Utilize a [atualização az vmss](/cli/azure/vmss#az-vmss-update) para configurar atualizações automáticas de imagem de OS para o seu conjunto de escala. Utilize o Azure CLI 2.0.47 ou superior. O exemplo seguinte configura atualizações automáticas para o conjunto de escala denominado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Utilização de sondas de saúde de aplicação

Durante uma atualização do OS, as instâncias vm num conjunto de escala são atualizadas um lote de cada vez. A atualização só deverá continuar se a aplicação do cliente for saudável nas instâncias vm atualizadas. Recomendamos que a aplicação forneça sinais de saúde ao motor de upgrade de osso definido em escala. Por padrão, durante as atualizações do OS, a plataforma considera o estado de alimentação vM e o estado de fornecimento de extensão para determinar se uma instância vm é saudável após uma atualização. Durante o Upgrade osS de uma instância VM, o disco OS numa instância VM é substituído por um novo disco baseado na versão mais recente da imagem. Após a atualização do OS concluída, as extensões configuradas são executadas nestes VMs. O pedido só é considerado saudável quando todas as extensões da instância forem aprovisionadas com sucesso.

Um conjunto de escala pode ser configurado opcionalmente com sondas de saúde de aplicação para fornecer à plataforma informações precisas sobre o estado em curso da aplicação. Aplicação As sondas de saúde são sondas de equilíbrio de carga personalizadas que são usadas como um sinal de saúde. A aplicação em execução numa instância vM definida em escala pode responder a pedidos externos de HTTP ou TCP indicando se é saudável. Para obter mais informações sobre como funcionam as sondas de equilíbrio de carga personalizadas, consulte para [compreender as sondas](../load-balancer/load-balancer-custom-probe-overview.md)de equilíbrio de carga . Aplicações As sondas de saúde não são suportadas para conjuntos de escala de tecido de serviço. Os conjuntos de balança de tecidos não-serviço requerem sondas de saúde de aplicação load Balancer ou extensão de saúde de [aplicação](virtual-machine-scale-sets-health-extension.md).

Se o conjunto de escala estiver configurado para utilizar vários grupos de colocação, as sondas que utilizam um [Balancer de Carga Padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) precisam de ser utilizadas.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configurar uma sonda de equilíbrio de carga personalizada como sonda de saúde de aplicação em um conjunto de escala
Como uma boa prática, crie uma sonda de equilíbrio de carga explicitamente para a saúde definida pela escala. O mesmo ponto final para uma sonda HTTP existente ou sonda TCP pode ser usado, mas uma sonda de saúde pode exigir um comportamento diferente de uma sonda tradicional de equilíbrio de carga. Por exemplo, uma sonda de equilíbrio de carga tradicional poderia regressar pouco saudável se a carga na instância for demasiado elevada, mas isso não seria apropriado para determinar a saúde por exemplo durante uma atualização automática do SO. Configure a sonda para ter uma alta taxa de sondagem de menos de dois minutos.

A sonda de equilíbrio de carga pode ser referenciada na *redePerfil* do conjunto de escala e pode ser associada a um equilibrador de carga interno ou público da seguinte forma:

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
> Ao utilizar atualizações automáticas de OS com tecido de serviço, a nova imagem de OS é lançada domínio de atualização por Domínio de Atualização para manter a elevada disponibilidade dos serviços em funcionamento no Tecido de Serviço. Para utilizar atualizações automáticas de OS em Tecido de Serviço, o seu cluster deve ser configurado para utilizar o Nível de Durabilidade prateada ou superior. Para obter mais informações sobre as características de durabilidade dos clusters de Tecido de Serviço, consulte [esta documentação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Mantenha as credenciais atualizadas
Se o seu conjunto de escala utilizar quaisquer credenciais para aceder a recursos externos, como uma extensão VM configurada para utilizar um token SAS para a conta de armazenamento, então certifique-se de que as credenciais são atualizadas. Se alguma suparticipaa, incluindo certificados e fichas, tiver expirado, a atualização falhará e o primeiro lote de VMs será deixado em estado falhado.

As medidas recomendadas para recuperar VMs e reativar a atualização automática de OS se houver uma falha de autenticação de recursos são:

* Regenerar o símbolo (ou quaisquer outras credenciais) passados para a sua extensão.
* Certifique-se de que qualquer credencial utilizada a partir de dentro do VM para falar com entidades externas está atualizada.
* Extensão de atualização no modelo de conjunto de escala com quaisquer novos tokens.
* Implementar o conjunto de escala atualizado, que atualizará todos os casos de VM, incluindo os falhados.

## <a name="using-application-health-extension"></a>Utilização da extensão da saúde da aplicação
A extensão de saúde de aplicação é implantada dentro de uma instância de conjunto de escala de máquina virtual e relatórios sobre a saúde vm de dentro da instância definida de escala. Pode configurar a extensão para sondar um ponto final da aplicação e atualizar o estado da aplicação nessa instância. Este estado de exemplo é verificado pelo Azure para determinar se uma instância é elegível para operações de atualização.

Como a extensão reporta a saúde de dentro de um VM, a extensão pode ser usada em situações em que sondas externas como sondas de saúde de aplicação (que utilizam [sondas personalizadas](../load-balancer/load-balancer-custom-probe-overview.md)do Equilíbrio de Carga Azure) não podem ser usadas.

Existem várias formas de implementar a extensão da Saúde de Aplicação aos seus conjuntos de escala, conforme detalhado nos exemplos [deste artigo.](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Obtenha o histórico de atualizações automáticas de imagem do OS
Pode verificar o histórico da mais recente atualização de OS realizada na sua escala com Azure PowerShell, Azure CLI 2.0 ou as APIs REST. Pode sacá-lo nas últimas cinco tentativas de upgrade de somas nos últimos dois meses.

### <a name="rest-api"></a>API REST
O exemplo seguinte utiliza [a API REST](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) para verificar o estado do conjunto de escala denominado *myScaleSet* no grupo de recursos chamado *myResourceGroup:*

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

A chamada GET devolve propriedades semelhantes à seguinte saída de exemplo:

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
Utilize o cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) para verificar o histórico de atualização do OS para o seu conjunto de escala. O exemplo seguinte detalha como revê o estado de atualização do OS para um conjunto de escala chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Use [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) para verificar o histórico de upgrade de SO para o seu conjunto de escala. Utilize o Azure CLI 2.0.47 ou superior. O exemplo seguinte detalha como revê o estado de atualização do OS para um conjunto de escala chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Como obter a versão mais recente de uma imagem de OS da plataforma?

Pode obter as versões de imagem disponíveis para a atualização automática de SKUs suportada pelo SKUs usando os exemplos abaixo:

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

## <a name="manually-trigger-os-image-upgrades"></a>Desencadear manualmente as atualizações de imagem do OS
Com a atualização automática da imagem OS ativada no seu conjunto de escala, não precisa de ativar manualmente atualizações de imagem no seu conjunto de escala. O orquestrador de atualização osso aplicará automaticamente a versão de imagem mais recente disponível nas instâncias do conjunto de escala sem qualquer intervenção manual.

Para casos específicos em que não pretende esperar que o orquestrador aplique a imagem mais recente, pode desencadear uma atualização de imagem de OS manualmente utilizando os exemplos abaixo.

> [!NOTE]
> O gatilho manual das atualizações de imagem de OS não fornece capacidades automáticas de reversão. Se uma instância não recuperar a sua saúde após uma operação de atualização, o disco de SO anterior não pode ser restaurado.

### <a name="rest-api"></a>API REST
Utilize a chamada Start [OS Upgrade](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) API para iniciar uma atualização rolante para mover todas as instâncias de conjunto de escala de máquina virtual para a mais recente versão ossia de imagem disponível. Os casos que já estão a executar a mais recente versão de SO disponível não são afetados. O exemplo seguinte detalha como pode iniciar uma atualização de OS rolante num conjunto de escala chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Utilize o [cmdlet Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) para verificar o histórico de atualização do OS para o seu conjunto de escala. O exemplo seguinte detalha como pode iniciar uma atualização de OS rolante num conjunto de escala chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Use [aaz vmss rolling-upgrade começar](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) para verificar o histórico de upgrade de SO para o seu conjunto de escala. Utilize o Azure CLI 2.0.47 ou superior. O exemplo seguinte detalha como pode iniciar uma atualização de OS rolante num conjunto de escala chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Implementar com um modelo

Pode utilizar modelos para implementar um conjunto de escala com atualizações automáticas de SO para imagens suportadas como [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Passos seguintes
Para mais exemplos sobre como utilizar atualizações automáticas de SO com conjuntos de escala, reveja o [repo GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
