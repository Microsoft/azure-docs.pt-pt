---
title: Criar um conjunto de escala Azure que utiliza zonas de disponibilidade
description: Saiba como criar conjuntos de escala de máquinas virtuais Azure que utilizam Zonas de Disponibilidade para maior redundância contra interrupções
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 11695eb889a10dc689b00399a37382a3b9772eae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274413"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Criar um conjunto de escala de máquina virtual que utiliza Zonas de Disponibilidade

Para proteger os conjuntos de escala de máquinas virtuais de falhas ao nível do datacenter, pode criar uma escala definida em todas as Zonas de Disponibilidade. As regiões azure que suportam as Zonas de Disponibilidade têm um mínimo de três zonas separadas, cada uma com a sua própria fonte de energia independente, rede e arrefecimento. Para mais informações, consulte [a visão geral das Zonas de Disponibilidade](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Considerações de disponibilidade

Quando se implanta uma escala definida numa ou mais zonas a partir da versão API *2017-12-01,* tem a opção de implantar com "propagação máxima" ou "propagação de domínio de avaria stática 5". Com a propagação máxima, o conjunto de escala espalha os seus VMs pelo maior número possível de domínios de avaria dentro de cada zona. Esta propagação pode ser em mais ou menos de cinco domínios de falha por zona. Com a "propagação do domínio de avaria estático 5", o conjunto de escala espalha os seus VMs através de exatamente cinco domínios de falha por zona. Se o conjunto de escala não encontrar cinco domínios de avaria distintos por zona para satisfazer o pedido de atribuição, o pedido falha.

**Recomendamos a implantação com a máxima propagação para**a maioria das cargas de trabalho, uma vez que esta abordagem proporciona a melhor propagação na maioria dos casos. Se precisar de réplicas para ser espalhada por unidades distintas de isolamento de hardware, recomendamos que se espalhe por Zonas de Disponibilidade e utilize a propagação máxima em cada zona.

Com a propagação máxima, basta ver um domínio de falha na visão de instância vM definido pela escala e, por exemplo, metadados, independentemente de quantos domínios de falha os VMs estão espalhados. A propagação dentro de cada zona está implícita.

Para utilizar a propagação máxima, coloque a *plataformaFaultDomainCount* a *1*. Para utilizar a propagação estática de cinco falhas, coloque a *plataformaFaultDomainCount* a *5*. Na versão API *2017-12-01,* *a plataformaFaultDomainCount* falha em *1* para conjuntos de escala de zona única e zona cruzada. Atualmente, apenas a propagação de cinco porões estáticos é suportada para conjuntos regionais (não zonais).

### <a name="placement-groups"></a>Grupos de colocação

Ao implementar um conjunto de escala, também tem a opção de implantar com um único grupo de [colocação](./virtual-machine-scale-sets-placement-groups.md) por Zona de Disponibilidade, ou com vários por zona. Para os conjuntos regionais (não zonais), a escolha é ter um único grupo de colocação na região ou ter múltiplos na região. Para a maioria das cargas de trabalho, recomendamos vários grupos de colocação, o que permite uma maior escala. Na versão API *2017-12-01,* a escala define o padrão de vários grupos de colocação para conjuntos de escala de zona única e zona cruzada, mas eles padrão para um único grupo de colocação para conjuntos regionais (não zonais).

> [!NOTE]
> Se utilizar a propagação máxima, deve utilizar vários grupos de colocação.

### <a name="zone-balancing"></a>Equilíbrio de zona

Finalmente, para conjuntos de escala implantados em várias zonas, você também tem a opção de escolher "o melhor equilíbrio de zona de esforço" ou "equilíbrio de zona rígida". Um conjunto de escala é considerado "equilibrado" se cada\\zona tiver o mesmo número de VMs ou + - 1 VM em todas as outras zonas para o conjunto de escala. Por exemplo:

- Uma escala definida com 2 VMs na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerada equilibrada. Há apenas uma zona com uma contagem de VM diferente e é apenas menos 1 do que as outras zonas. 
- Uma escala definida com 1 VM na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerada desequilibrada. A Zona 1 tem menos 2 VMs do que as zonas 2 e 3.

É possível que os VMs no conjunto de escala sejam criados com sucesso, mas as extensões desses VMs não conseguem implementar. Estes VMs com falhas de extensão ainda são contados para determinar se um conjunto de escala é equilibrado. Por exemplo, uma balança definida com 3 VMs na zona 1, 3 VMs na zona 2 e 3 VMs na zona 3 é considerada equilibrada mesmo que todas as extensões falhassem na zona 1 e todas as extensões tivessem sucesso nas zonas 2 e 3.

Com o melhor equilíbrio de zona de esforço, a escala definida tenta entrar e sair, mantendo o equilíbrio. No entanto, se por alguma razão isso não for possível (por exemplo, se uma zona descer, o conjunto de escala não pode criar um novo VM nessa zona), o conjunto de escala permite que o desequilíbrio temporário entre ou saia. Nas tentativas subsequentes de escala para fora, o conjunto de escala adiciona VMs a zonas que precisam de mais VMs para a escala definida para ser equilibrada. Da mesma forma, em escala subsequente nas tentativas, o conjunto de escala remove VMs de zonas que precisam de menos VMs para a escala definida para ser equilibrada. Com o "equilíbrio de zona estrito", o conjunto de escala falha qualquer tentativa de escala dentro ou fora se o fizer causaria desequilíbrio.

Para utilizar o melhor equilíbrio de zona de esforço, coloque *a zonaBalance* a *falsa*. Esta definição é o padrão na versão API *2017-12-01*. Para utilizar o equilíbrio de zona rigoroso, coloque *a zonaBalance* *a verdade*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Conjuntos de escala seleções de zona única e de zona redundante

Ao implementar um conjunto de escala de máquina virtual, pode optar por utilizar uma única Zona de Disponibilidade numa região, ou em várias zonas.

Quando se cria uma escala definida numa única zona, controla-se a zona de todas as instâncias vm e o conjunto de escala é gerido e a escala automática apenas dentro dessa zona. Um conjunto de escala redundante permite criar um conjunto de escala única que se estende por várias zonas. À medida que as instâncias vm são criadas, por padrão são equilibradas uniformemente em todas as zonas. Se ocorrer uma interrupção numa das zonas, um conjunto de escala não aumenta automaticamente a capacidade. Uma boa prática seria configurar regras de escala automática com base no CPU ou no uso da memória. As regras de escala automática permitiriam que a escala definida respondesse a uma perda dos casos vm naquela zona, eliminando novas instâncias nas restantes zonas operacionais.

Para utilizar as Zonas de Disponibilidade, o seu conjunto de escala deve ser criado numa [região azure apoiada.](../availability-zones/az-overview.md#services-support-by-region) Pode criar um conjunto de escala que utiliza Zonas de Disponibilidade com um dos seguintes métodos:

- [Portal Azure](#use-the-azure-portal)
- CLI do Azure
- [Azure PowerShell](#use-azure-powershell)
- [Modelos do Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

O processo de criação de um conjunto de escala que utiliza uma Zona de Disponibilidade é o mesmo que detalhado no [artigo de início.](quick-create-portal.md) Ao selecionar uma região Azure suportada, pode criar uma escala definida numa ou mais zonas disponíveis, como mostra o seguinte exemplo:

![Criar uma escala definida numa única Zona de Disponibilidade](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Os recursos de escala definidos e de apoio, como o equilibrador de carga Azure e o endereço IP público, são criados na zona única que especifica.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

O processo de criação de um conjunto de escala que utiliza uma Zona de Disponibilidade é o mesmo que detalhado no [artigo de início.](quick-create-cli.md) Para utilizar as Zonas de Disponibilidade, tem de criar a sua escala definida numa região azure apoiada.

Adicione `--zones` o parâmetro ao [az vmss criar](/cli/azure/vmss) comando e especificar qual a zona a utilizar (como zona *1,* *2*, ou *3*). O exemplo seguinte cria um conjunto de escala de zona única chamado *myScaleSet* na zona *1:*

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

Para um exemplo completo de um conjunto de escala de zona única e recursos de rede, consulte [este script CLI da amostra](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Conjunto de escala redundante de zona

Para criar um conjunto de escala redundante, utilize um endereço IP público *Padrão* SKU e um equilibrador de carga. Para um maior despedimento, o *SKU Standard* cria recursos de rede redundantes em zonas. Para mais informações, consulte a [visão geral do Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md) e as Zonas de Equilíbrio de Carga Padrão e Zonas de [Disponibilidade.](../load-balancer/load-balancer-standard-availability-zones.md)

Para criar um conjunto de escala seletiva, especifique várias zonas com o `--zones` parâmetro. O exemplo seguinte cria um conjunto de escala redundante chamado *myScaleSet* através das zonas *1,2,3*:

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

Leva alguns minutos para criar e configurar todos os recursos definidos de escala e VMs na ou área que especifica. Para um exemplo completo de um conjunto de escala seletiva e recursos de rede, consulte [este script CLI da amostra](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Para utilizar as Zonas de Disponibilidade, tem de criar a sua escala definida numa região azure apoiada. Adicione `-Zone` o parâmetro ao comando [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) e especifique qual a zona a utilizar (como zona *1,* *2*, ou *3*).

O exemplo seguinte cria um conjunto de escala de zona única chamado *myScaleSet* na zona 1 *do Leste DOS EUA* *2*. Os recursos de rede do Azure para a rede virtual, o endereço IP público e o balanceador de carga são criados automaticamente. Quando solicitado, forneça as suas próprias credenciais administrativas pretendidas para as instâncias de VM no conjunto de dimensionamento:

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

Para criar um conjunto de escala seletiva, especifique várias zonas com o `-Zone` parâmetro. O exemplo seguinte cria um conjunto de escala redundante chamado *myScaleSet* através das zonas *1,* *2, 3*. Os recursos de rede Azure redundantes para rede virtual, endereço IP público e equilibrador de carga são automaticamente criados. Quando solicitado, forneça as suas próprias credenciais administrativas pretendidas para as instâncias de VM no conjunto de dimensionamento:

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

O processo para criar um conjunto de escala que utiliza uma Zona de Disponibilidade é o mesmo que detalhado no artigo de início para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Para utilizar as Zonas de Disponibilidade, tem de criar a sua escala definida numa região azure apoiada. Adicione `zones` a propriedade ao tipo de recurso *Microsoft.Compute/virtualMachineScaleSets* no seu modelo e especifique qual a zona a utilizar (como zona *1,* *2*, ou *3*).

O exemplo seguinte cria um conjunto de escala de zona única Linux chamado *myScaleSet* na zona *1* *do Leste DOS 2* :

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

Para um exemplo completo de um conjunto de escala de uma zona única e recursos de rede, consulte [este modelo de Gestor](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json) de Recursos da amostra

### <a name="zone-redundant-scale-set"></a>Conjunto de escala redundante de zona

Para criar um conjunto de escala seletiva, especifique vários valores na `zones` propriedade para o tipo de recurso *Microsoft.Compute/virtualMachineScaleSets.* O exemplo seguinte cria um conjunto de escala redundante chamado *myScaleSet* em 2 zonas leste dos EUA *1,2,3*: *East US 2*

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

Se criar um endereço IP público ou um equilibrador de carga, especifique o *"sku": { "nome": propriedade "Standard" }"* para criar recursos de rede redundantes em zonas. Também precisa de criar um Grupo de Segurança de Rede e regras para permitir qualquer tráfego. Para mais informações, consulte a [visão geral do Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md) e as Zonas de Equilíbrio de Carga Padrão e Zonas de [Disponibilidade.](../load-balancer/load-balancer-standard-availability-zones.md)

Para um exemplo completo de um conjunto de escala saca-redundante e recursos de rede, consulte [este modelo de Gestor](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json) de Recursos da amostra

## <a name="next-steps"></a>Passos seguintes

Agora que criou uma escala definida numa Zona de Disponibilidade, pode aprender a [implementar aplicações em conjuntos de escala](tutorial-install-apps-cli.md) de máquinas virtuais ou utilizar a escala automática com [conjuntos de escala de máquina virtual](tutorial-autoscale-cli.md).
