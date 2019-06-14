---
title: Compreender os IDs de instância para VMs do conjunto de dimensionamento de VM do Azure | Documentos da Microsoft
description: Compreender a que VMS do conjunto de IDs de instância para o dimensionamento de VM do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 6aeba722a0661979664f8d61efdb9b2bf47ad801
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60618507"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Compreender a que VMS do conjunto de IDs de instância para o dimensionamento de VM do Azure
Este artigo descreve os IDs de instância para conjuntos de dimensionamento e as várias formas de superfície eles.

## <a name="scale-set-instance-ids"></a>IDs de instância de conjunto de dimensionamento

Cada VM num conjunto de dimensionamento obtém um ID de instância que identifica de forma exclusiva. Esta instância que ID é usado no conjunto de dimensionamento APIs para fazer operações específicas de uma conjunto de VM à escala. Por exemplo, pode especificar um ID de instância específica de recriação de imagem, ao utilizar a API de recriação de imagem:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (para obter mais informações, consulte a [documentação da REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (para obter mais informações, consulte a [documentação do Powershell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (para obter mais informações, consulte a [documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Pode obter a lista de IDs de instância listando todas as instâncias num conjunto de dimensionamento:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (para obter mais informações, consulte a [documentação da REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (para obter mais informações, consulte a [documentação do Powershell](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (para obter mais informações, consulte a [documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Também pode utilizar [resources.azure.com](https://resources.azure.com) ou o [SDKs do Azure](https://azure.microsoft.com/downloads/) para listar as VMs num conjunto de dimensionamento.

A apresentação exata da saída depende as opções de que fornecer ao comando, mas eis algumas saídas de exemplo a partir da CLI:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Como pode ver, a propriedade de "instanceId" é apenas um número decimal. A instância IDs pode ser reutilizada para novas instâncias, uma vez antigas instâncias são eliminadas.

>[!NOTE]
> Há **nenhuma garantia** na instância de forma IDs são atribuídos às VMs no conjunto de dimensionamento. Eles podem parecer sequencialmente crescentes às vezes, mas isso nem sempre é o caso. Tem uma dependência de forma específica, em que os IDs de instância são atribuídos às VMs.

## <a name="scale-set-vm-names"></a>Nomes de VMS de conjunto de dimensionamento

Na saída do exemplo acima, também há um "name" para a VM. Este nome assume a forma "{dimensionamento-set-name} _ {instância-id}". Este nome é o que veem no portal do Azure quando listar instâncias num conjunto de dimensionamento:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

A parte de {id de instância} do nome é o mesmo número decimal que a propriedade de "instanceId" discutido anteriormente.

## <a name="instance-metadata-vm-name"></a>Nome da VM de metadados de instância

Se consultar a [metadados de instância](../virtual-machines/windows/instance-metadata-service.md) de dentro de um conjunto de dimensionamento de VM, verá um "name" na saída:

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Este nome é igual ao nome discutido anteriormente.

## <a name="scale-set-vm-computer-name"></a>Nome do computador VM do conjunto de dimensionamento

Cada VM num conjunto de dimensionamento obtém um nome de computador atribuído a ele. Este nome de computador é o nome do anfitrião da VM no [resolução dentro da rede virtual de nomes de DNS fornecida pelo Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Este nome de computador é o formato "{computer-name-prefix}{base-36-instance-id}".

O {base-36-instância-id} está [basear 36](https://en.wikipedia.org/wiki/Base36) e é sempre seis dígitos de comprimento. Se a representação de 36 base do número leva menos de seis dígitos, o {base-36-instância-id} é preenchido com zeros para torná-lo seis dígitos de comprimento. Por exemplo, uma instância com {prefixo de nome de computador} "nsgvmss" e instância ID 85 terão computador. o nome "nsgvmss00002D".

>[!NOTE]
> O prefixo do nome do computador é uma propriedade do modelo de conjunto de dimensionamento, que pode definir, pelo que pode ser diferente do nome do conjunto de dimensionamento em si.
