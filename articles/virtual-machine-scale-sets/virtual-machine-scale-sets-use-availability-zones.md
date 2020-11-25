---
title: Crie um conjunto de escala Azure que utiliza Zonas de Disponibilidade
description: Saiba como criar conjuntos de escala de máquina virtual Azure que usam Zonas de Disponibilidade para aumentar a redundância contra interrupções
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 08/08/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c5ddd5846be91e9fc99a251d6ad45ade8bde2937
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016663"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Crie um conjunto de escala de máquina virtual que utilize Zonas de Disponibilidade

Para proteger os conjuntos de escala de máquina virtual contra falhas de nível de datacenter, pode criar um conjunto de escala em zonas de disponibilidade. As regiões azure que suportam Zonas de Disponibilidade têm um mínimo de três zonas distintas, cada uma com a sua própria fonte de energia independente, rede e arrefecimento. Para mais informações, consulte [a Visão Geral das Zonas de Disponibilidade.](../availability-zones/az-overview.md)

## <a name="availability-considerations"></a>Considerações de disponibilidade

Quando implementar uma escala regional (não zonal) definida em uma ou mais zonas a partir da versão API *2017-12-01,* tem as seguintes opções de disponibilidade:
- Propagação máxima (plataformaFaultDomainCount = 1)
- Propagação fixa estática (plataformaFaultDomainCount = 5)
- Propagação alinhada com domínios de falha do disco de armazenamento (platforFaultDomainCount = 2 ou 3)

Com a propagação máxima, o conjunto de escala espalha os seus VMs pelo maior número possível de domínios de avaria dentro de cada zona. Esta propagação pode ser através de maiores ou menos de cinco domínios de avaria por zona. Com a propagação estática fixa, o conjunto de escala espalha os seus VMs em exatamente cinco domínios de avaria por zona. Se o conjunto de escala não encontrar cinco domínios de avarias distintos por zona para satisfazer o pedido de atribuição, o pedido falha.

**Recomendamos a implantação com propagação máxima para a maioria das cargas de trabalho,** uma vez que esta abordagem proporciona a melhor propagação na maioria dos casos. Se precisar de réplicas para ser espalhada por unidades distintas de isolamento de hardware, recomendamos que se espalhe pelas Zonas de Disponibilidade e utilize a propagação máxima dentro de cada zona.

> [!NOTE]
> Com a propagação máxima, só se vê um domínio de falha na visão de instância de VM definida na escala e nos metadados de exemplo, independentemente de quantos domínios de avaria os VMs estão espalhados. A propagação dentro de cada zona está implícita.

### <a name="placement-groups"></a>Grupos de colocação

Quando implementa um conjunto de escala, também tem a opção de implementar com um único [grupo de colocação](./virtual-machine-scale-sets-placement-groups.md) por Zona de Disponibilidade, ou com vários por zona. Para os conjuntos regionais (não zonais), a escolha é ter um único grupo de colocação na região ou ter múltiplos na região. Para a maioria das cargas de trabalho, recomendamos vários grupos de colocação, o que permite uma maior escala. Na versão API *2017-12-01,* a escala define o padrão em vários grupos de colocação para conjuntos de escala de zona única e de zona cruzada, mas eles default para um único grupo de colocação para conjuntos regionais (não zonais).

> [!NOTE]
> Se utilizar a propagação máxima, deve utilizar vários grupos de colocação.

### <a name="zone-balancing"></a>Equilíbrio de zona

Finalmente, para conjuntos de escala implantados em várias zonas, você também tem a opção de escolher "melhor equilíbrio de zona de esforço" ou "equilíbrio de zona rígida". Um conjunto de escala é considerado "equilibrado" se cada zona tiver o mesmo número de VMs ou + \\ - 1 VM em todas as outras zonas para o conjunto de escala. Por exemplo:

- Uma escala definida com 2 VMs na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerada equilibrada. Há apenas uma zona com uma contagem de VM diferente e é apenas 1 menos do que as outras zonas. 
- Uma escala definida com 1 VM na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerada desequilibrada. A zona 1 tem menos 2 VMs do que as zonas 2 e 3.

É possível que os VMs no conjunto de escala sejam criados com sucesso, mas as extensões desses VMs não conseguem ser implementadas. Estes VMs com falhas de extensão ainda são contados para determinar se um conjunto de escala é equilibrado. Por exemplo, uma escala definida com 3 VMs na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerada equilibrada mesmo que todas as extensões tenham falhado na zona 1 e todas as extensões tenham sido bem sucedidas nas zonas 2 e 3.

Com o melhor equilíbrio da zona de esforço, a escala tenta escalar para dentro e para fora, mantendo o equilíbrio. No entanto, se por alguma razão isso não for possível (por exemplo, se uma zona descer, o conjunto de escala não pode criar um novo VM nessa zona), o conjunto de escala permite que o desequilíbrio temporário se escalone com sucesso dentro ou fora. Nas tentativas de escala subsequentes, o conjunto de escala adiciona VMs a zonas que precisam de mais VMs para que a escala definida seja equilibrada. Da mesma forma, na escala subsequente em tentativas, o conjunto de escala remove VMs de zonas que precisam de menos VMs para que a escala definida seja equilibrada. Com "equilíbrio de zona rígida", o conjunto de escalas falha qualquer tentativa de escalar dentro ou fora se isso causar desequilíbrio.

Para utilizar o melhor equilíbrio da zona de esforço, deslobre *a zonaBalança* a *falso*. Esta definição é o padrão na versão API *2017-12-01*. Para utilizar um equilíbrio de zona rígido, deslobre *a zonaBalança* para *ser verdadeiro*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Conjuntos de escala de zona única e de zona redundante

Quando implementar um conjunto de escala de máquina virtual, pode optar por utilizar uma única Zona de Disponibilidade numa região ou em várias zonas.

Quando se cria uma escala definida numa única zona, controla-se em que zona todas as instâncias VM funcionam, e o conjunto de escala é gerido e as escalas automáticas apenas dentro dessa zona. Um conjunto de escala redundante de zona permite criar um conjunto de escala única que se estende por várias zonas. À medida que os casos de VM são criados, por padrão são equilibrados em todas as zonas. Se ocorrer uma interrupção numa das zonas, um conjunto de escala não escala automaticamente para aumentar a capacidade. Uma boa prática seria configurar regras de autoescalação baseadas no USO OU OU OU OU OU. As regras de autoescala permitiriam que a escala definida respondesse a uma perda das instâncias VM nessa zona, escalando novos casos nas zonas operacionais restantes.

Para utilizar Zonas de Disponibilidade, o seu conjunto de escalas deve ser criado numa [região de Azure suportada.](../availability-zones/az-region.md) Pode criar um conjunto de escala que utilize Zonas de Disponibilidade com um dos seguintes métodos:

- [Portal do Azure](#use-the-azure-portal)
- CLI do Azure
- [Azure PowerShell](#use-azure-powershell)
- [Modelos do Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

O processo para criar um conjunto de escala que utilize uma Zona de Disponibilidade é o mesmo que detalhado no [artigo de início.](quick-create-portal.md) Quando selecionar uma região de Azure suportada, pode criar uma escala definida numa ou mais zonas disponíveis, como mostra o seguinte exemplo:

![Criar uma escala definida numa única Zona de Disponibilidade](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

O conjunto de escalas e recursos de suporte, como o equilibrador de carga Azure e o endereço IP público, são criados na única zona que especifica.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

O processo para criar um conjunto de escala que utilize uma Zona de Disponibilidade é o mesmo que detalhado no [artigo de início.](quick-create-cli.md) Para utilizar Zonas de Disponibilidade, tem de criar a sua escala definida numa região de Azure suportada.

Adicione o `--zones` parâmetro ao [az vmss criar](/cli/azure/vmss) comando e especificar qual zona a utilizar (como a zona *1,* *2*, ou *3*). O exemplo a seguir cria um conjunto de escala de uma única zona denominado *myScaleSet* na zona *1*:

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

Para um exemplo completo de um conjunto de escala única e recursos de rede, consulte [esta amostra de script CLI](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Conjunto de escala redundante de zona

Para criar um conjunto de escala redundante de zona, utilize um endereço IP público *Standard* SKU e um equilibrador de carga. Para uma redundância reforçada, o *SKU Standard* cria recursos de rede redundantes de zona. Para obter mais informações, consulte [a visão geral do balançor de carga Azure e as](../load-balancer/load-balancer-overview.md) [zonas padrão de balançador de carga e disponibilidade](../load-balancer/load-balancer-standard-availability-zones.md).

Para criar um conjunto de escala de zona redundante, especifique várias zonas com o `--zones` parâmetro. O exemplo a seguir cria um conjunto de escala redundante de zona denominado *myScaleSet* entre as zonas *1,2,3*:

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

Leva alguns minutos para criar e configurar todos os recursos definidos de escala e VMs na zona(s) que especifica. Para um exemplo completo de um conjunto de escala de zona redundante e recursos de rede, consulte [esta amostra de script CLI](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Para utilizar Zonas de Disponibilidade, tem de criar a sua escala definida numa região de Azure suportada. Adicione o `-Zone` parâmetro ao comando [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) e especifique qual a zona a utilizar (como a zona *1,* *2* ou *3*).

O exemplo a seguir cria um conjunto de escala de uma única zona chamado *myScaleSet* na zona *2 leste dos EUA* *1*. Os recursos de rede do Azure para a rede virtual, o endereço IP público e o balanceador de carga são criados automaticamente. Quando solicitado, forneça as suas próprias credenciais administrativas pretendidas para as instâncias de VM no conjunto de dimensionamento:

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

### <a name="zone-redundant-scale-set"></a>Conjunto de escala redundante de zona

Para criar um conjunto de escala de zona redundante, especifique várias zonas com o `-Zone` parâmetro. O exemplo a seguir cria um conjunto de escala redundante de zona chamado *myScaleSet* através das zonas *1, 2, 3*. *East US 2* Os recursos de rede Azure redundantes para rede virtual, endereço IP público e balanceador de carga são automaticamente criados. Quando solicitado, forneça as suas próprias credenciais administrativas pretendidas para as instâncias de VM no conjunto de dimensionamento:

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

O processo para criar um conjunto de escala que utilize uma Zona de Disponibilidade é o mesmo que detalhado no artigo de início para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Para utilizar Zonas de Disponibilidade, tem de criar a sua escala definida numa região de Azure suportada. Adicione a `zones` propriedade ao tipo de recurso *Microsoft.Compute/virtualMachineScaleSets* no seu modelo e especifique qual a zona a utilizar (como a zona *1*, *2* ou *3*).

O exemplo a seguir cria um conjunto de escala de zona única Linux denominado *myScaleSet* na zona *2 leste dos EUA* *1*:

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

Para um exemplo completo de um conjunto de escala única e recursos de rede, consulte [este modelo de gestor de recursos de amostra](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Conjunto de escala redundante de zona

Para criar um conjunto de escala redundante de zona, especifique vários valores na `zones` propriedade para o tipo de recurso *Microsoft.Compute/virtualMachineScaleSets.* O exemplo a seguir cria um conjunto de escala redundante de zona chamado *myScaleSet* através das zonas *1,2,3* dos *EUA:*

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

Se criar um endereço IP público ou um equilibrador de carga, especifique o *"sku": { "name": "Standard" }"* propriedade para criar recursos de rede redundantes de zona. Também precisa de criar um Grupo de Segurança de Rede e regras para permitir qualquer tráfego. Para obter mais informações, consulte [a visão geral do balançor de carga Azure e as](../load-balancer/load-balancer-overview.md) [zonas padrão de balançador de carga e disponibilidade](../load-balancer/load-balancer-standard-availability-zones.md).

Para um exemplo completo de um conjunto de escalas redundantes de zona e recursos de rede, consulte [este modelo de gestor de recursos de amostra](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Passos seguintes

Agora que criou uma escala definida numa Zona de Disponibilidade, pode aprender a [implementar aplicações em conjuntos de escala de máquinas virtuais](tutorial-install-apps-cli.md) ou [utilizar uma escala automática com conjuntos de escala de máquina virtual](tutorial-autoscale-cli.md).
