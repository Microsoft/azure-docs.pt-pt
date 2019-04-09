---
title: Criar um conjunto de dimensionamento do Azure que utiliza as zonas de disponibilidade | Documentos da Microsoft
description: Saiba como criar conjuntos de dimensionamento de máquina virtual do Azure que utilizam as zonas de disponibilidade para maior redundância contra interrupções
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 24cff3a2ec4d0bed7a030ca430eaa698eb4a7325
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278773"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Criar um conjunto de dimensionamento de máquina virtual que utiliza as zonas de disponibilidade

Para proteger seus conjuntos de dimensionamento de máquina virtual de falhas de nível do datacenter, pode criar um conjunto em zonas de disponibilidade de dimensionamento. Regiões do Azure que suportam zonas de disponibilidade têm um mínimo de três zonas separadas, cada um com seus próprios independente de origem, rede e arrefecimento de energia. Para obter mais informações, consulte [descrição geral das zonas de disponibilidade](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Considerações de disponibilidade

Quando implementa um conjunto num ou mais zonas a partir da versão de API de dimensionamento *2017-12-01*, tem a opção de implementar com a "propagação máxima" ou "estático 5 falhas domínio propagando-se". Com a propagação máxima, o conjunto de dimensionamento se propaga as suas VMs em como vários domínios de falha possível em cada zona. Esta propagação poderia ser em mais ou menos de cinco domínios por zona de falha. Com "estático 5 falhas domínio propagar", o conjunto de dimensionamento propaga as suas VMs em domínios de falha de cinco exatamente por zona. Se o conjunto de dimensionamento não é possível localizar a cinco domínios de falha diferentes por zona para satisfazer o pedido de atribuição, o pedido falha.

**Recomendamos a implementação com a propagação máxima para a maioria das cargas de trabalho**, pois essa abordagem fornece o melhor propagando-se na maioria dos casos. Se precisar de réplicas sejam distribuídas em unidades de isolamento de hardware distintos, recomendamos a propagação em zonas de disponibilidade e utilizar a propagação máxima dentro de cada zona.

Com a propagação máxima, apenas verá um domínio de falha na vista de instância da VM do conjunto de dimensionamento e nos metadados, independentemente de quantos domínios de falha, as VMs são distribuídas por instância. Propagação dentro de cada zona é implícito.

Para utilizar a propagação máxima, defina *platformFaultDomainCount* ao *1*. Para utilizar a propagação de domínio falha cinco estáticos, defina *platformFaultDomainCount* ao *5*. Na versão de API *2017-12-01*, *platformFaultDomainCount* assume a predefinição *1* conjuntos de dimensionamento de zona única e entre zonas. Atualmente, apenas estático cinco falhas domínio propagando-se é suportada para conjuntos de dimensionamento regional.

### <a name="placement-groups"></a>Grupos de colocação

Quando implementa um conjunto de dimensionamento, tem também a opção de implementar com um único [grupo de colocação](./virtual-machine-scale-sets-placement-groups.md) por zona de disponibilidade ou com múltiplos por zona. Para conjuntos de dimensionamento regionais, a escolha é ter um único grupo de colocação na região ou ter vários na região. Para a maioria das cargas de trabalho, recomendamos que vários grupos de colocação, que permite a uma escala superior. Na versão de API *2017-12-01*, conjuntos de dimensionamento predefinido para vários grupos de colocação para conjuntos de dimensionamento de zona única e entre zonas, mas estas são predefinidas para único grupo de colocação para conjuntos de dimensionamento regional.

> [!NOTE]
> Se utilizar a propagação máxima, tem de utilizar vários grupos de colocação.

### <a name="zone-balancing"></a>Balanceamento de zona

Por fim, para conjuntos de dimensionamento implementados em várias zonas, tem também a opção de escolher "balance zona de melhor esforço" ou "balance zona strict". Um conjunto de dimensionamento é considerado "equilibrado" se o mesmo número de VMs de cada zona ou +\\-1 VM em todas as outras zonas para o conjunto de dimensionamento. Por exemplo:

- Com balanceamento de um conjunto de dimensionamento com 2 VMs na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerada. Existe apenas uma zona com uma contagem VM diferente e é apenas 1 menos do que as outras zonas. 
- Um conjunto de dimensionamento com 1 VM na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerada desequilibrado. Zona 1 tem 2 VMs menos do que as zonas de 2 e 3.

É possível que as VMs no conjunto de dimensionamento são criadas com êxito, mas extensões nessas VMS não conseguem implementar. Estas VMs com falhas de extensão ainda são contabilizados ao determinar se um conjunto de dimensionamento é balanceado. Por exemplo, um conjunto de dimensionamento com 3 VMs na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerado balanceada, mesmo se todas as extensões de falha na zona 1 e todas as extensões foi concluída com êxito em zonas de 2 e 3.

Com o balanceamento de zonas de melhor esforço, o conjunto de dimensionamento de tentativas para aumentar e reduzir, mantendo o equilíbrio. No entanto, se por alguma razão não for possível (por exemplo, se uma zona ficar inativo, o conjunto de dimensionamento não é possível criar uma nova VM em que zona), o conjunto de dimensionamento permite desequilíbrio temporário para com êxito aumentar e reduzir. Em tentativas subseqüentes de escalamento horizontal, o conjunto de dimensionamento adiciona as VMs para as zonas que precisam de mais VMs para o conjunto de dimensionamento para ser balanceado. Da mesma forma, em escala subsequente tentativas de início, o conjunto de dimensionamento remove as VMs de zonas que precisam de menos VMs para o conjunto de dimensionamento para ser balanceado. Com "balance zona strict", o conjunto de dimensionamento falha qualquer tentativa de aumentar e reduzir se fazer poderia causar unbalance.

Para utilizar o balanceamento de zonas de melhor esforço, defina *zoneBalance* ao *falso*. Esta é a predefinição na versão de API *2017-12-01*. Para utilizar o balanceamento de zonas strict, defina *zoneBalance* ao *verdadeiro*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Conjuntos de dimensionamento de zona única e com redundância de zona

Quando implementa um conjunto de dimensionamento de máquina virtual, pode optar por utilizar uma única zona de disponibilidade numa região ou em várias zonas.

Quando cria um conjunto de dimensionamento numa única zona, controle exatamente em qual zona todas essas instâncias de VM a executar no, e é gerido o conjunto de dimensionamento e é dimensionado automaticamente apenas dentro dessa zona. Um conjunto de dimensionamento com redundância de zona permite-lhe criar um conjunto de dimensionamento único que se estende por várias zonas. À medida que as instâncias de VM são criadas, por predefinição que eles são uniformemente Balanceados entre zonas. Deve ocorrer uma interrupção de uma das zonas, um conjunto de dimensionamento não automaticamente aumentar horizontalmente para aumentar a capacidade. Uma prática recomendada seria configurar regras de dimensionamento automático com base na utilização de CPU ou memória. As regras de dimensionamento automático permitiria que o conjunto de dimensionamento para responder a uma perda de instâncias de VM em que uma zona com a expansão de novas instâncias nas zonas da operacionais restantes.

Para utilizar zonas de disponibilidade, o conjunto de dimensionamento tem de ser criado num [região suportada do Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). Pode criar um conjunto de dimensionamento que utiliza as zonas de disponibilidade com um dos seguintes métodos:

- [Portal do Azure](#use-the-azure-portal)
- CLI do Azure
- [Azure PowerShell](#use-azure-powershell)
- [Modelos do Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

O processo de criação de um conjunto de dimensionamento que utiliza uma zona de disponibilidade é o mesmo, conforme detalhado no [artigo de introdução](quick-create-portal.md). Quando seleciona uma região do Azure suportada, pode criar um conjunto de dimensionamento num ou mais zonas de disponibilidade, conforme mostrado no exemplo a seguir:

![Criar um conjunto de dimensionamento numa única zona de disponibilidade](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

O conjunto de dimensionamento e recursos, como o Balanceador de carga do Azure e o endereço IP público, de suporte são criados na zona única que especificar.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

O processo de criação de um conjunto de dimensionamento que utiliza uma zona de disponibilidade é o mesmo, conforme detalhado no [artigo de introdução](quick-create-cli.md). Para utilizar zonas de disponibilidade, tem de criar o seu conjunto de dimensionamento numa região do Azure suportada.

Adicionar a `--zones` parâmetro para o [criar az vmss](/cli/azure/vmss) comando e especifique exatamente em qual zona a utilizar (como zona *1*, *2*, ou *3*). O exemplo seguinte cria um conjunto nomeado de dimensionamento de zona única *myScaleSet* na zona *1*:

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

Para obter um exemplo completo de um dimensionamento de zona única definida e os recursos de rede, consulte [este script da CLI de exemplo](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Conjunto de dimensionamento com redundância de zona

Para criar um dimensionamento com redundância de zona definida, utilizar um *padrão* SKU IP de carga e o endereço de Balanceador de público. Para maior redundância, o *padrão* SKU cria recursos de rede com redundância de zona. Para obter mais informações, consulte [descrição geral do Azure Balanceador de carga Standard](../load-balancer/load-balancer-standard-overview.md) e [Balanceador de carga Standard e zonas de disponibilidade](../load-balancer/load-balancer-standard-availability-zones.md).

Para criar um conjunto de dimensionamento com redundância de zona, especificar várias zonas com o `--zones` parâmetro. O exemplo seguinte cria um conjunto nomeado de dimensionamento com redundância de zona *myScaleSet* em zonas *SUM(1,2,3*:

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

Demora alguns minutos para criar e configurar todos os o conjunto de dimensionamento de VMs e recursos nas zona ou zonas que especificar. Para obter um exemplo completo de um dimensionamento com redundância de zona definida e os recursos de rede, consulte [este script da CLI de exemplo](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Para utilizar zonas de disponibilidade, tem de criar o seu conjunto de dimensionamento numa região do Azure suportada. Adicionar a `-Zone` parâmetro para o [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) comando e especifique exatamente em qual zona utilizar (como zona *1*, *2*, ou *3*).

O exemplo seguinte cria um conjunto nomeado de dimensionamento de zona única *myScaleSet* na *E.U.A. Leste 2* zona *1*. Os recursos de rede do Azure para a rede virtual, o endereço IP público e o balanceador de carga são criados automaticamente. Quando solicitado, forneça as suas próprias credenciais administrativas pretendidas para as instâncias de VM no conjunto de dimensionamento:

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

Para criar um conjunto de dimensionamento com redundância de zona, especificar várias zonas com o `-Zone` parâmetro. O exemplo seguinte cria um conjunto nomeado de dimensionamento com redundância de zona *myScaleSet* entre *E.U.A. Leste 2* zonas *1, 2, 3*. Os recursos de rede do Azure com redundância de zona para a rede virtual, o endereço IP público e o Balanceador de carga são criados automaticamente. Quando solicitado, forneça as suas próprias credenciais administrativas pretendidas para as instâncias de VM no conjunto de dimensionamento:

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

O processo de criação de um conjunto de dimensionamento que utiliza uma zona de disponibilidade é o mesmo, conforme detalhado no artigo de introdução para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Para utilizar zonas de disponibilidade, tem de criar o seu conjunto de dimensionamento numa região do Azure suportada. Adicionar a `zones` propriedade para o *Compute/virtualmachinescalesets* recurso escreva no seu modelo e especifique exatamente em qual zona usar (como zona *1*, *2*, ou *3*).

O exemplo seguinte cria um conjunto nomeado de dimensionamento do Linux zona única *myScaleSet* na *E.U.A. Leste 2* zona *1*:

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

Para obter um exemplo completo de um dimensionamento de zona única definida e os recursos de rede, consulte [este modelo do Resource Manager de exemplo](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Conjunto de dimensionamento com redundância de zona

Para criar um conjunto de dimensionamento com redundância de zona, especificar valores múltiplos no `zones` propriedade para o *Compute/virtualmachinescalesets* tipo de recurso. O exemplo seguinte cria um conjunto nomeado de dimensionamento com redundância de zona *myScaleSet* entre *E.U.A. Leste 2* zonas *SUM(1,2,3*:

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

Se criar um endereço IP público ou um balanceador de carga, especificar o *"sku": {"name": "Standard"} "* propriedade para criar recursos de rede com redundância de zona. Também terá de criar um grupo de segurança de rede e regras para permitir qualquer tráfego. Para obter mais informações, consulte [descrição geral do Azure Balanceador de carga Standard](../load-balancer/load-balancer-standard-overview.md) e [Balanceador de carga Standard e zonas de disponibilidade](../load-balancer/load-balancer-standard-availability-zones.md).

Para obter um exemplo completo de um dimensionamento com redundância de zona definida e os recursos de rede, consulte [este modelo do Resource Manager de exemplo](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Passos Seguintes

Agora que já criou um conjunto numa zona de disponibilidade de dimensionamento, pode saber como [conjuntos de dimensionamento de implementar aplicações na máquina virtual](tutorial-install-apps-cli.md) ou [utilize o dimensionamento automático com conjuntos de dimensionamento de máquinas virtuais](tutorial-autoscale-cli.md).
