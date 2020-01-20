---
title: Criar um conjunto de dimensionamento do Azure que usa Zonas de Disponibilidade
description: Saiba como criar conjuntos de dimensionamento de máquinas virtuais do Azure que usam Zonas de Disponibilidade para maior redundância contra interrupções
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 11695eb889a10dc689b00399a37382a3b9772eae
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274413"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Criar um conjunto de dimensionamento de máquinas virtuais que usa Zonas de Disponibilidade

Para proteger seus conjuntos de dimensionamento de máquinas virtuais contra falhas no nível do datacenter, você pode criar um conjunto de dimensionamento entre Zonas de Disponibilidade. As regiões do Azure que dão suporte a Zonas de Disponibilidade têm um mínimo de três zonas separadas, cada uma com sua própria fonte de energia, rede e resfriamento independentes. Para obter mais informações, consulte [visão geral do zonas de disponibilidade](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Considerações de disponibilidade

Ao implantar um conjunto de dimensionamento em uma ou mais zonas a partir da versão *2017-12-01*da API, você tem a opção de implantar com "distribuição máxima" ou "difusão estática de 5 domínios de falha". Com a distribuição máxima, o conjunto de dimensionamento distribui suas VMs entre o máximo possível de domínios de falha dentro de cada zona. Essa disseminação pode ser entre mais ou menos de cinco domínios de falha por zona. Com "distribuição estática de 5 domínios de falha", o conjunto de dimensionamento distribui suas VMs em exatamente cinco domínios de falha por zona. Se o conjunto de dimensionamento não puder encontrar cinco domínios de falha distintos por zona para atender à solicitação de alocação, a solicitação falhará.

**É recomendável implantar com distribuição máxima para a maioria das cargas de trabalho**, pois essa abordagem fornece a melhor difusão na maioria dos casos. Se você precisar que as réplicas sejam distribuídas entre unidades de isolamento de hardware distintas, é recomendável distribuir entre Zonas de Disponibilidade e utilizar a distribuição máxima em cada zona.

Com a distribuição máxima, você vê apenas um domínio de falha no modo de exibição de instância VM do conjunto de dimensionamento e nos metadados da instância, independentemente de quantos domínios de falha as VMs estão espalhadas. A difusão dentro de cada zona é implícita.

Para usar a distribuição máxima, defina *platformFaultDomainCount* como *1*. Para usar a distribuição estática de cinco domínios de falha, defina *platformFaultDomainCount* como *5*. Na API versão *2017-12-01*, *platformFaultDomainCount* usa como padrão *1* para conjuntos de dimensionamento de zona única e entre zonas. Atualmente, somente a distribuição estática de cinco domínios de falha tem suporte para conjuntos de dimensionamento regionais (não zonais).

### <a name="placement-groups"></a>Grupos de colocação

Ao implantar um conjunto de dimensionamento, você também tem a opção de implantar com um único [grupo de posicionamento](./virtual-machine-scale-sets-placement-groups.md) por zona de disponibilidade ou com vários por zona. Para conjuntos de dimensionamento regionais (não zonais), a escolha é ter um único grupo de posicionamento na região ou ter vários na região. Para a maioria das cargas de trabalho, recomendamos vários grupos de posicionamento, o que permite maior escala. Na API versão *2017-12-01*, os conjuntos de dimensionamento usam como padrão vários grupos de posicionamento para conjuntos de dimensionamento de zona única e entre zonas, mas eles assumem como padrão um grupo de posicionamento único para conjuntos de dimensionamento regionais (não zonais).

> [!NOTE]
> Se você usar a distribuição máxima, deverá usar vários grupos de posicionamento.

### <a name="zone-balancing"></a>Balanceamento de zona

Por fim, para conjuntos de dimensionamento implantados em várias zonas, você também tem a opção de escolher "melhor equilíbrio de zona de esforço" ou "balanceamento de zona estrito". Um conjunto de dimensionamento é considerado "balanceado" se cada zona tiver o mesmo número de VMs ou +\\-1 VM em todas as outras zonas para o conjunto de dimensionamento. Por exemplo:

- Um conjunto de dimensionamento com 2 VMs na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerado balanceado. Há apenas uma zona com uma contagem de VM diferente e é apenas 1 a menos que as outras zonas. 
- Um conjunto de dimensionamento com 1 VM na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerado desbalanceado. Zona 1 tem 2 menos VMs do que as zonas 2 e 3.

É possível que as VMs no conjunto de dimensionamento sejam criadas com êxito, mas as extensões nessas VMs falham ao serem implantadas. Essas VMs com falhas de extensão ainda são contadas ao determinar se um conjunto de dimensionamento é equilibrado. Por exemplo, um conjunto de dimensionamento com 3 VMs na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerado balanceado mesmo se todas as extensões falharem na zona 1 e todas as extensões tiverem êxito nas zonas 2 e 3.

Com o melhor balanceamento de zona de esforço, o conjunto de dimensionamento tenta reduzir e reduzir enquanto mantém o equilíbrio. No entanto, se por algum motivo isso não for possível (por exemplo, se uma zona ficar inativa, o conjunto de dimensionamento não poderá criar uma nova VM nessa zona), o conjunto de dimensionamento permitirá o desequilíbrio temporário para reduzir ou reduzir com êxito. Em tentativas de expansão subsequentes, o conjunto de dimensionamento adiciona VMs a zonas que precisam de mais VMs para que o conjunto de dimensionamento seja balanceado. Da mesma forma, em tentativas subsequentes de escala, o conjunto de dimensionamento remove as VMs de zonas que precisam de menos VMs para que o conjunto de dimensionamento seja balanceado. Com "balanceamento de zona estrito", o conjunto de dimensionamento falhará em qualquer tentativa de reduzir ou reduzir, caso isso cause desequilíbrio.

Para usar o equilíbrio de zona de melhor esforço, defina *zoneBalance* como *false*. Essa configuração é o padrão na versão de API *2017-12-01*. Para usar o balanceamento de zona estrito, defina *zoneBalance* como *true*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Conjuntos de dimensionamento de zona única e com redundância de zona

Ao implantar um conjunto de dimensionamento de máquinas virtuais, você pode optar por usar uma única zona de disponibilidade em uma região ou em várias zonas.

Quando você cria um conjunto de dimensionamento em uma única zona, controla em qual zona todas as instâncias de VM são executadas e o conjunto de dimensionamento é gerenciado e dimensionado somente dentro dessa zona. Um conjunto de dimensionamento com redundância de zona permite criar um único conjunto de dimensionamento que abrange várias zonas. Como as instâncias de VM são criadas, por padrão elas são balanceadas uniformemente entre as zonas. Se ocorrer uma interrupção em uma das zonas, um conjunto de dimensionamento não será dimensionado automaticamente para aumentar a capacidade. Uma prática recomendada seria configurar regras de dimensionamento automático com base no uso de CPU ou memória. As regras de dimensionamento automático permitiriam que o conjunto de dimensionamento respondesse a uma perda das instâncias de VM nessa zona, expandindo novas instâncias nas zonas operacionais restantes.

Para usar Zonas de Disponibilidade, seu conjunto de dimensionamento deve ser criado em uma [região do Azure com suporte](../availability-zones/az-overview.md#services-support-by-region). Você pode criar um conjunto de dimensionamento que usa Zonas de Disponibilidade com um dos seguintes métodos:

- [Portal do Azure](#use-the-azure-portal)
- CLI do Azure
- [O Azure PowerShell](#use-azure-powershell)
- [Modelos de Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Utilizar o Portal do Azure

O processo para criar um conjunto de dimensionamento que usa uma zona de disponibilidade é o mesmo detalhado no [artigo de introdução](quick-create-portal.md). Ao selecionar uma região do Azure com suporte, você pode criar um conjunto de dimensionamento em uma ou mais zonas disponíveis, conforme mostrado no exemplo a seguir:

![Criar um conjunto de dimensionamento em uma única zona de disponibilidade](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

O conjunto de dimensionamento e os recursos de suporte, como o Azure Load Balancer e o endereço IP público, são criados na zona única que você especificar.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

O processo para criar um conjunto de dimensionamento que usa uma zona de disponibilidade é o mesmo detalhado no [artigo de introdução](quick-create-cli.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte.

Adicione o parâmetro `--zones` ao comando [AZ vmss Create](/cli/azure/vmss) e especifique qual zona usar (como zona *1*, *2*ou *3*). O exemplo a seguir cria um conjunto de dimensionamento de zona única chamado *Myscalemodeset* na zona *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Para obter um exemplo completo de um conjunto de dimensionamento de zona única e recursos de rede, consulte [este script CLI de exemplo](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Conjunto de dimensionamento com redundância de zona

Para criar um conjunto de dimensionamento com redundância de zona, você usa um endereço IP público de SKU *padrão* e um balanceador de carga. Para redundância avançada, o SKU *Standard* cria recursos de rede com redundância de zona. Para obter mais informações, consulte [visão geral padrão Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md) e [Standard Load Balancer e zonas de disponibilidade](../load-balancer/load-balancer-standard-availability-zones.md).

Para criar um conjunto de dimensionamento com redundância de zona, especifique várias zonas com o parâmetro `--zones`. O exemplo a seguir cria um conjunto de dimensionamento com redundância de zona chamado *Myscalemodeset* nas zonas *1, 2, 3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Leva alguns minutos para criar e configurar todos os recursos do conjunto de dimensionamento e as VMs nas zonas que você especificar. Para obter um exemplo completo de um conjunto de dimensionamento com redundância de zona e recursos de rede, consulte [este script CLI de exemplo](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte. Adicione o parâmetro `-Zone` ao comando [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) e especifique qual zona usar (como zona *1*, *2*ou *3*).

O exemplo a seguir cria um conjunto de dimensionamento de zona única chamado *Myscaleset* no *leste dos EUA 2* zona *1*. Os recursos de rede do Azure para a rede virtual, o endereço IP público e o balanceador de carga são criados automaticamente. Quando solicitado, forneça as suas próprias credenciais administrativas pretendidas para as instâncias de VM no conjunto de dimensionamento:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Conjunto de dimensionamento com redundância de zona

Para criar um conjunto de dimensionamento com redundância de zona, especifique várias zonas com o parâmetro `-Zone`. O exemplo a seguir cria um conjunto de dimensionamento com redundância de zona chamado *Myscaleset* no *leste dos EUA 2* zonas *1, 2, 3*. Os recursos de rede do Azure com redundância de zona para rede virtual, endereço IP público e balanceador de carga são criados automaticamente. Quando solicitado, forneça as suas próprias credenciais administrativas pretendidas para as instâncias de VM no conjunto de dimensionamento:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Use Azure Resource Manager templates (Utilizar modelos do Azure Resource Manager)

O processo para criar um conjunto de dimensionamento que usa uma zona de disponibilidade é o mesmo detalhado no artigo de introdução para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Para usar Zonas de Disponibilidade, você deve criar seu conjunto de dimensionamento em uma região do Azure com suporte. Adicione a propriedade `zones` ao tipo de recurso *Microsoft. Compute/virtualMachineScaleSets* em seu modelo e especifique qual zona usar (como zona *1*, *2*ou *3*).

O exemplo a seguir cria um conjunto de dimensionamento de zona única do Linux chamado *Myscaleset* no *leste dos EUA 2* , zona *1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Para obter um exemplo completo de um conjunto de dimensionamento de zona única e recursos de rede, consulte [este modelo do Resource Manager de exemplo](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Conjunto de dimensionamento com redundância de zona

Para criar um conjunto de dimensionamento com redundância de zona, especifique vários valores na propriedade `zones` para o tipo de recurso *Microsoft. Compute/virtualMachineScaleSets* . O exemplo a seguir cria um conjunto de dimensionamento com redundância de zona chamado *Myscaleset* no *leste dos EUA 2* zonas *1, 2, 3*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Se você criar um endereço IP público ou um balanceador de carga, especifique a propriedade *"SKU": {"Name": "Standard"} "* para criar recursos de rede com redundância de zona. Você também precisa criar um grupo de segurança de rede e regras para permitir qualquer tráfego. Para obter mais informações, consulte [visão geral padrão Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md) e [Standard Load Balancer e zonas de disponibilidade](../load-balancer/load-balancer-standard-availability-zones.md).

Para obter um exemplo completo de um conjunto de dimensionamento com redundância de zona e recursos de rede, consulte [este modelo do Resource Manager de exemplo](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Passos seguintes

Agora que você criou um conjunto de dimensionamento em uma zona de disponibilidade, você pode aprender a [implantar aplicativos em conjuntos de dimensionamento de máquinas virtuais](tutorial-install-apps-cli.md) ou [usar o dimensionamento automático com conjuntos de dimensionamento de máquinas virtuais](tutorial-autoscale-cli.md).
