---
title: Benefício Híbrido Azure para conjuntos de escala de máquina virtual Linux
description: Saiba como o Azure Hybrid Benefit pode aplicar-se à escala de máquinas virtual definida para ajudá-lo a economizar dinheiro nas suas máquinas virtuais Linux que estão a funcionar no Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: a714434c39a0c40c2e908f2d0c424f02851921a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933683"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Benefício Híbrido Azure para conjunto de escala de máquina virtual Linux (Visualização pública)

**Azure Hybrid Benefit for Linux virtual machine scale set is in public preview now**. O benefício AHB pode ajudá-lo a reduzir o custo de funcionamento dos [seus conjuntos de balanças de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)RHEL e SLES .

Com este benefício, paga apenas o custo de infraestrutura da sua escala definida. O benefício está disponível para todas as imagens do RHEL e do SLES Marketplace pay-as-you-go (PAYG).


>[!NOTE]
> Este artigo descreve o Benefício Híbrido Azure para o Linux VMSS. Existe um [artigo disponível [aqui AHB para Os VMs Linux),](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux)que já está disponível para os clientes Azure desde novembro de 2020.

## <a name="benefit-description"></a>Descrição de benefícios
O Azure Hybrid permite-lhe utilizar as licenças de acesso à Cloud existentes a partir de Red Hat ou SUSE e converter flexívelmente as instâncias de conjunto de máquinas virtuais para fazer a faturação da sua própria subscrição (BYOS). 

O conjunto de escala de máquina virtual implantado a partir de imagens de mercado PAYG cobrará tanto a infraestrutura como a taxa de software. Com o AHB, as instâncias PAYG podem ser convertidas para um modelo de faturação BYOS sem redistribuição.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Azure Hybrid Benefit custou visualização em VMs Linux.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Âmbito de elegibilidade do Benefício Híbrido Azure para o Linux
A Azure Hybrid Benefit está disponível para todas as imagens RHEL e SLES PAYG do Azure Marketplace. O benefício ainda não está disponível para imagens RHEL ou SLES BYOS ou imagens personalizadas do Azure Marketplace.

Azure Casos de Anfitrião Dedicado, e benefícios híbridos SQL não são elegíveis para Azure Hybrid Benefit se você já estiver usando o benefício com Os VMs Linux.

## <a name="get-started"></a>Introdução

### <a name="red-hat-customers"></a>Clientes da Red Hat

A Azure Hybrid Benefit for RHEL está disponível para clientes da Red Hat que satisfaçam ambos estes critérios:

- Ter subscrições RHEL ativas ou não utilizadas que sejam elegíveis para utilização em Azure
- Ter ativado uma ou mais dessas subscrições para uso em Azure com o programa [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

> [!IMPORTANT]
> Certifique-se de que a subscrição correta foi ativada no programa de acesso à [nuvem.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

Para começar a usar o benefício para o Chapéu Vermelho:

1. Ative as suas subscrições RHEL elegíveis em Azure utilizando a interface de [cliente do Red Hat Cloud Access](https://access.redhat.com/management/cloud).

   As subscrições Azure que fornece durante o processo de ativação do Red Hat Cloud Access serão então autorizadas a utilizar a funcionalidade Azure Hybrid Benefit.
1. Aplique o Azure Hybrid Benefit em qualquer um dos seus conjuntos de balanças de máquinas virtuais RHEL PAYG existentes e novos RHEL. Pode utilizar o portal Azure ou o Azure CLI para permitir o benefício.
1. Siga [os próximos passos recomendados](https://access.redhat.com/articles/5419341) para configurar fontes de atualização para os seus VMs RHEL e para as diretrizes de conformidade com a subscrição RHEL.


### <a name="suse-customers"></a>Clientes SUSE

Para começar a utilizar o benefício para a SUSE:

1. Registe-se no Programa SUSE Public Cloud.
1. Aplique o benefício no seu conjunto de escala de máquina virtual recém-criada ou existente através do portal Azure ou do Azure CLI.
1. Registe os seus VMs que estão a receber o benefício com uma fonte separada de atualizações.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Ativar e desativar o benefício no Portal Azure 
A experiência portal para ativar e desativar o AHB em conjunto de escala de máquina virtual não está **atualmente disponível**.

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Ativar e desativar o benefício utilizando o Azure CLI

Pode utilizar o `az vmss update` comando para atualizar os VM existentes. Para os VMs RHEL, executar o comando com um `--license-type` parâmetro de `RHEL_BYOS` . Para os VMs SLES, executar o comando com um `--license-type` parâmetro de `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Exemplo do CLI para permitir o benefício
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Exemplo do CLI para desativar o benefício
Para desativar o benefício, utilize um `--license-type` valor `None` de:

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> Os conjuntos de escala têm uma ["política de upgrade"](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) que determina como os VMs são atualizados com o modelo de conjunto de escala mais recente. Assim, se o seu VMSS tiver uma política de atualização 'Automática', o benefício AHB será aplicado automaticamente à medida que as instâncias VM forem atualizadas. Se a VMSS tiver uma política de atualização 'Rolling', com base nas atualizações programadas, será aplicada a AHB.
Em caso de política de atualização 'Manual', terá de efetuar uma "atualização manual" de cada VM existente.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>Exemplo do CLI para atualizar instâncias de conjunto de escala de máquina virtual em caso de política de "Atualização Manual" 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>Aplicar o Benefício Híbrido Azure em conjunto de escala de máquina virtual criar tempo 
Além de aplicar o Azure Hybrid Benefit ao conjunto de escalas de máquinas virtuais pay-as-you-go existente, pode invocá-lo no momento da criação de conjunto de escala de máquina virtual. Os benefícios de fazê-lo são três vezes:
- Pode providenciar instâncias de conjunto de máquinas virtuais PAYG e BYOS utilizando a mesma imagem e processo.
- Permite futuras alterações no modo de licenciamento, algo que não está disponível com uma imagem apenas BYOS.
- As instâncias de conjunto de escala de máquina virtual serão ligadas à Infraestrutura de Atualização do Chapéu Vermelho (RHUI) por padrão, para garantir que permanece atualizada e segura. Pode alterar o mecanismo atualizado após a sua implantação a qualquer momento.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>Exemplo CLI para criar escala de máquina virtual definida com benefício AHB
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>Passos seguintes
* [Saiba como criar e atualizar VMs e adicionar tipos de licença (RHEL_BYOS, SLES_BYOS) para benefício híbrido Azure utilizando o CLI Azure](/cli/azure/vmss)
