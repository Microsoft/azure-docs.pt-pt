---
title: Trabalhar com Conjuntos de Dimensionamento de Máquinas Virtuais de grande escala do Azure | Microsoft Docs
description: O que precisa saber para utilizar conjuntos de dimensionamento de máquinas virtuais de grande escala do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/9/2017
ms.author: rajraj
ms.openlocfilehash: 1ec1ec0fab6947f7f07b3c387f4c8af4a0f666cb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776347"
---
# <a name="working-with-large-virtual-machine-scale-sets"></a>Trabalhar com conjuntos de dimensionamento de máquinas virtuais de grande escala
Agora, pode criar [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/) do Azure com uma capacidade de até 1000 VMs. Neste documento, um _conjunto de dimensionamento de máquinas virtuais de grande escala_ está definido como um conjunto de dimensionamento com capacidade para dimensionar para mais do que 100 VMs. Esta capacidade é definida por uma propriedade de conjunto de dimensionamento (_singlePlacementGroup=False_). 

Determinados aspetos de conjuntos de dimensionamento de grande escala, como o balanceamento de carga e os domínios de falha, têm um comportamento diferente de um conjunto de dimensionamento padrão. Este documento explica as características dos conjuntos de dimensionamento de grande escala e descreve o que precisa de saber para utilizá-los com êxito nas aplicações. 

Uma abordagem comum para implementar a infraestrutura cloud de grande escala é criar um conjunto de _unidades de escala_, por exemplo, através da criação de vários conjuntos de dimensionamento de VMs em várias VNETs e contas de armazenamento. Esta abordagem permite uma gestão mais fácil, em comparação com VMs únicas, e várias unidades de escala são úteis para muitas aplicações, particularmente as que requerem outros componentes empilháveis, como várias redes virtuais e pontos finais. No entanto, se a sua aplicação precisar de um único cluster grande, pode ser mais fácil implementar um conjunto de dimensionamento único de até 1000 VMs. Os cenários de exemplo incluem implementações de macrodados centralizadas ou grelhas de computação que requerem uma gestão simples de um grande conjunto de nós de trabalho. Combinado com [discos de dados anexados](virtual-machine-scale-sets-attached-disks.md) de conjunto de dimensionamento de máquina virtual, os grandes conjuntos de dimensionamento permitem-lhe implementar uma infraestrutura dimensionável constituída por milhares de vCPUs e petabytes de armazenamento, como uma única operação.

## <a name="placement-groups"></a>Grupos de colocação 
O que faz com que um conjunto de dimensionamento _de grande escala_ seja especial não é o número de VMs, mas o número de _grupos de colocação_ nele contidos. Um grupo de colocação é uma construção semelhante a um conjunto de disponibilidade do Azure, com os seus próprios domínios de falha e domínios de atualização. Por predefinição, um conjunto de dimensionamento consiste num único grupo de colocação com o tamanho máximo de 100 VMs. Se a propriedade _singlePlacementGroup_ do grupo de dimensionamento estiver definida como _falso_, o conjunto de dimensionamento pode ser composto por vários grupos de colocação e tem um intervalo de 0-1000 VMs. Quando o valor predefinido for definido como _verdadeiro_, o conjunto de dimensionamento é composto por um único grupo de colocação, e tem um intervalo de 0-100 VMs.

## <a name="checklist-for-using-large-scale-sets"></a>Lista de verificação para utilizar conjuntos de dimensionamento de grande escala
Para decidir se a aplicação pode utilizar eficazmente os conjuntos de dimensionamento de grande escala, considere os seguintes requisitos:

- Se estiver a planear implementar um grande número de VMs, os limites de quota de vCPU de Computação poderão ter de ser aumentados. 
- Os conjuntos de dimensionamento criados a partir de imagens do Azure Marketplace podem aumentar verticalmente até 1000 VMs.
- Atualmente, os conjuntos de dimensionamento criados a partir de imagens personalizadas (imagens VM criadas e carregadas por si) podem ser aumentados verticalmente até 600 VMs.
- Os conjuntos de dimensionamento de grande escala requerem Managed Disks do Azure. Os conjuntos de dimensionamento que não são criados com discos geridos requerem várias contas de armazenamento (uma por cada 20 VMs). Os conjuntos de dimensionamento de grande escala são concebidos para funcionar exclusivamente com Managed Disks, para reduzir a sobrecarga da gestão do armazenamento e para evitar o risco de existirem limites de subscrição para contas de armazenamento. 
- O balanceamento de carga de camada 4 com conjuntos de dimensionamento compostos por vários grupos de colocação requer o [SKU Balanceador de Carga do Azure Standard](../load-balancer/load-balancer-standard-overview.md). O SKU Balanceador de Carga do Azure Standard oferece mais benefícios, como a capacidade de balancear a carga entre diversos conjuntos de dimensionamento. O SKU Standard também requer que os conjuntos de dimensionamento tenham um Grupo de Segurança de rede associado ao mesmo, caso contrário, os conjuntos NAT não funcionam corretamente. Se tiver de utilizar o SKU Balanceador de Carga do Azure Básico, confirme que o conjunto de dimensionamento está configurado para utilizar um único grupo de colocação, que é a predefinição.
- O balanceamento de carga de camada 7 com o Gateway de Aplicação do Azure é suportado para todos os conjuntos de dimensionamento.
- Um conjunto de dimensionamento é definido com uma única sub-rede - certifique-se de que a sub-rede tem um espaço de endereço suficientemente grande para todas as VMs necessárias. Por predefinição, um conjunto de dimensionamento aprovisiona em excesso (cria VMs adicionais no momento da implementação ou ao aumentar horizontalmente, o que não lhe é cobrado) para melhorar a fiabilidade e o desempenho da implementação. Permita um espaço de endereço 20% maior do que o número de VMs que planeia dimensionar.
- Os domínios de falha e os domínios de atualização só são consistentes dentro de um grupo de colocação. Esta arquitetura não altera a disponibilidade geral de um conjunto de dimensionamento, uma vez que as VMs são distribuídas por hardware físico distinto, mas tal não significa que, se precisar de garantir que duas VMs estão em hardware diferente, certifique-se de que estão em domínios de falha diferentes no mesmo grupo de colocação. Veja esta ligação [Regiões do Azure e disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability/). 
- O domínio de falha e o ID do grupo de colocação são apresentados na _vista de instância_ da VM do conjunto de dimensionamento. Pode ver a vista de instância da VM de um conjunto de dimensionamento no [Explorador de Recursos do Azure](https://resources.azure.com/).

## <a name="creating-a-large-scale-set"></a>Criar um conjunto de dimensionamento de grande escala
Quando cria um conjunto de dimensionamento no portal do Azure, basta especificar o valor da *Contagem de instâncias* até 1000. Se for mais de 100 instâncias, a opção *Ativar o dimensionamento acima de 100 instâncias* estará definida como *Sim*, o que irá permitir que ele seja dimensionado para vários grupos de colocação. 

![](./media/virtual-machine-scale-sets-placement-groups/portal-large-scale.png)

Pode criar um conjunto de dimensionamento de máquina virtual de grande escala com o comando da [CLI do Azure](https://github.com/Azure/azure-cli) _az vmss create_. Este comando configura as predefinições inteligentes, como a dimensão da sub-rede baseada no argumento _instance-count_:

```bash
az group create -l southcentralus -n biginfra
az vmss create -g biginfra -n bigvmss --image ubuntults --instance-count 1000
```
O comando _vmss create_ repõe as predefinições de certos valores de configuração, caso não os especifique. Para ver as opções disponíveis que pode ignorar, tente:
```bash
az vmss create --help
```

Se estiver a criar um conjunto de dimensionamento de grande escala ao compor um modelo do Azure Resource Manager, certifique-se de que o modelo cria um conjunto de dimensionamento com base em Managed Disks do Azure. Pode definir o _singlePlacementGroup_ propriedade para _false_ no _propriedades_ seção o _Compute/virtualmachinescalesets_ recursos. O fragmento JSON seguinte mostra o início de um modelo de conjunto de dimensionamento, incluindo a capacidade de 1000 VMs e a definição _"singlePlacementGroup" : falso_:
```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "location": "australiaeast",
  "name": "bigvmss",
  "sku": {
    "name": "Standard_DS1_v2",
    "tier": "Standard",
    "capacity": 1000
  },
  "properties": {
    "singlePlacementGroup": false,
    "upgradePolicy": {
      "mode": "Automatic"
    }
```
Para obter um exemplo completo de um modelo de conjunto de dimensionamento, veja [https://github.com/gbowerman/azure-myriad/blob/master/bigtest/bigbottle.json](https://github.com/gbowerman/azure-myriad/blob/master/bigtest/bigbottle.json).

## <a name="converting-an-existing-scale-set-to-span-multiple-placement-groups"></a>Converter um conjunto de dimensionamento existente para abranger vários grupos de colocação
Para que um conjunto de dimensionamento de máquina virtual existente seja capaz de dimensionar para mais de 100 VMs, é necessário alterar a propriedade _singlePlacementGroup_ para _falso_ no modelo do conjunto de dimensionamento. Pode testar a alteração desta propriedade com o [Explorador de Recursos do Azure](https://resources.azure.com/). Localize um conjunto de dimensionamento existente, selecione _Editar_ e altere a propriedade _singlePlacementGroup_. Se não visualizar esta propriedade, poderá estar a ver o conjunto de dimensionamento com uma versão anterior da API Microsoft.Compute.

>[!NOTE] 
Pode alterar um conjunto de dimensionamento para que não suporte apenas um único grupo de colocação (o comportamento padrão), mas para que suporte vários grupos de colocação. No entanto, não pode fazer a conversão inversa. Por conseguinte, certifique-se de que compreende as propriedades dos conjuntos de dimensionamento de grande escala antes de converter.


