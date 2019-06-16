---
title: Criar um conjunto de dimensionamento do Azure que utiliza as VMs de baixa prioridade (pré-visualização) | Documentos da Microsoft
description: Saiba como criar conjuntos de dimensionamento de máquina virtual do Azure que utilizar VMs de baixa prioridade para poupar nos custos
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 861c68ae8163e0ba8c2af2a3d96153ac3e84855f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803211"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>VMs de baixa prioridade em conjuntos de dimensionamento (pré-visualização)

Utilizar VMs de baixa prioridade em conjuntos de dimensionamento permite-lhe tirar partido da nossa capacidade unutilized numa economia significativa de custos. Em qualquer altura, quando o Azure necessita da capacidade de volta, a infraestrutura do Azure irá expulsar VMs de baixa prioridade. Por conseguinte, VMs de baixa prioridade são ideais para cargas de trabalho que podem lidar com interrupções, como o processamento de tarefas, ambientes dev/test, cargas de trabalho de computação grandes e muito mais em lotes.

A quantidade de capacidade unutilized disponível pode variar com base no tamanho, região, hora do dia e muito mais. Quando define a implementação de VMs de baixa prioridade em escala, o Azure irá alocar as VMs se há capacidade disponível, mas não existe nenhum SLA para estas VMs. Um conjunto de dimensionamento de baixa prioridade é implementado num domínio de falha único e oferece que não garantias de nenhuma elevada disponibilidade.

## <a name="eviction-policy"></a>Política de expulsão

Ao criar conjuntos de dimensionamento de baixa prioridade, pode definir a política de expulsão *Desalocação* (predefinição) ou *eliminar*. 

O *Desalocação* política move as suas VMs expulsos para o estado parada-desalocada, permitindo-lhe voltar a implementar instâncias expulsos. No entanto, não há nenhuma garantia de que a alocação será concluída com êxito. As VMs desalocadas irão contar para a sua quota de instância de conjunto de dimensionamento e lhe serão cobrados os discos subjacentes. 

Se desejar que as suas VMs no seu conjunto ser eliminado quando eles são expulsos de dimensionamento de baixa prioridade, pode definir a política de expulsão *eliminar*. Com a política de expulsão definida para eliminar, pode criar novas VMs, aumentando a propriedade de contagem de instância de conjunto de dimensionamento. As VMs expulsos são eliminadas juntamente com os discos subjacentes e, portanto, não são cobradas para o armazenamento. Também pode utilizar a funcionalidade de dimensionamento automático dos conjuntos de dimensionamento para automaticamente tentar compensar VMs expulsos, no entanto, não há nenhuma garantia de que a alocação será concluída com êxito. Recomenda-se que usar apenas o recurso de dimensionamento automático em conjuntos de dimensionamento de baixa prioridade ao definir a política de expulsão eliminar para evitar o custo de seus discos e a atingir os limites de quota. 

> [!NOTE]
> Durante a pré-visualização, poderá definir a política de expulsão ao utilizar o [portal do Azure](#use-the-azure-portal) e [modelos Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Implementar VMs de baixa prioridade em escala define

Para implementar baixa prioridade VMs em conjuntos de dimensionamento, pode definir a nova *prioridade* sinalizador para *baixa*. Todas as VMs no conjunto de dimensionamento serão definidas para baixa prioridade. Para criar um conjunto de dimensionamento com VMs de baixa prioridade, utilize um dos seguintes métodos:
- [Portal do Azure](#use-the-azure-portal)
- CLI do Azure
- [Azure PowerShell](#use-azure-powershell)
- [Modelos Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

O processo de criação de um conjunto de dimensionamento que utiliza as VMs de baixa prioridade é o mesmo, conforme detalhado no [artigo de introdução](quick-create-portal.md). Quando estiver a implementar um conjunto de dimensionamento, pode optar por definir o sinalizador de baixa prioridade e a política de expulsão: ![Criar um conjunto de dimensionamento com VMs de baixa prioridade](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

O processo de criação de um conjunto de dimensionamento com VMs de baixa prioridade é o mesmo, conforme detalhado no [artigo de introdução](quick-create-cli.md). Basta adicionar o ' – prioridade "chamar de parâmetro para a cli e configurá-lo para *baixa* conforme mostrado no exemplo abaixo:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

O processo de criação de um conjunto de dimensionamento com VMs de baixa prioridade é o mesmo, conforme detalhado no [artigo de introdução](quick-create-powershell.md).
Basta adicionar o '-prioridade "parâmetro para o [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) e defina-o como *baixa* conforme mostrado no exemplo abaixo:

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Utilizar modelos Azure Resource Manager

O processo de criação de um conjunto de dimensionamento que utiliza as VMs de baixa prioridade é o mesmo, conforme detalhado no artigo de introdução para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Adicione a propriedade 'priority' para o *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* recurso escreva no seu modelo e especifique *baixa* como o valor. Certifique-se de que usar *2018-03-01* versão de API ou superior. 

Para definir a política de expulsão até à eliminação, adicione o parâmetro de "evictionPolicy" e defina-o como *eliminar*.

O exemplo seguinte cria um conjunto nomeado de dimensionamento do Linux prioridade baixa *myScaleSet* na *e.u.a. Centro-Oeste*, que será *eliminar* as VMs no conjunto no expulsão de dimensionamento:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
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
## <a name="faq"></a>FAQ

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>Pode converter os conjuntos de dimensionamento existente para conjuntos de dimensionamento de baixa prioridade?
Não, definir o sinalizador de baixa prioridade é apenas suportado no momento da criação.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>Pode criar um conjunto de dimensionamento com VMs regulares e VMs de baixa prioridade?
Não, um conjunto de dimensionamento não suporta mais de um tipo de prioridade.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Como é gerida a quota para as VMs de baixa prioridade?
VMs de baixa prioridade e VMs regulares compartilham o mesmo conjunto de quota. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>Posso utilizar o dimensionamento automático com conjuntos de dimensionamento de baixa prioridade?
Sim, pode definir regras de dimensionamento automático no seu conjunto de dimensionamento de baixa prioridade. Se as suas VMs são expulsos, dimensionamento automático pode tentar criar novas VMs de baixa prioridade. Lembre-se de que não são garantidos entanto esta capacidade. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>Funciona o dimensionamento automático com ambas as políticas de remoção (desalocar e eliminar)?
É recomendado que defina a política de expulsão para eliminar quando utilizo o dimensionamento automático. Isto acontece porque as instâncias desalocadas são contadas em relação a contagem de capacidade no conjunto de dimensionamento. Quando utilizo o dimensionamento automático, provavelmente, irá atingir o número de instâncias de destino rapidamente devido a instâncias de desalocada, expulsos. 

## <a name="next-steps"></a>Passos Seguintes
Agora que já criou um conjunto de dimensionamento com VMs de baixa prioridade, experimente implementar nosso [modelo de dimensionamento automático através de baixa prioridade](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Veja a [conjunto de dimensionamento de máquina virtual página de preços](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) para detalhes de preços.
