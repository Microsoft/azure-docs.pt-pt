---
title: Entender as IDs de instância para VMs do conjunto de dimensionamento de VM do Azure
description: Entenda as IDs de instância para máquinas virtuais de conjuntos de escala de VM do Azure e as várias maneiras pelas quais elas se encontram.
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
ms.openlocfilehash: 1eb0cf1de3b0ebffac8810e19d69515224147bc7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355991"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Entender as IDs de instância para VMs do conjunto de dimensionamento de VM do Azure
Este artigo descreve as IDs de instância para conjuntos de dimensionamento e as várias maneiras como elas se estivem.

## <a name="scale-set-instance-ids"></a>IDs da instância do conjunto de dimensionamento

Cada VM em um conjunto de dimensionamento Obtém uma ID de instância que a identifica de forma exclusiva. Essa ID de instância é usada nas APIs do conjunto de dimensionamento para realizar operações em uma VM específica no conjunto de dimensionamento. Por exemplo, você pode especificar uma ID de instância específica para refazer a imagem ao usar a API de reimagem:

API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (para obter mais informações, consulte a [documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (para obter mais informações, consulte a [documentação do PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (para obter mais informações, consulte a [documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Você pode obter a lista de IDs de instância listando todas as instâncias em um conjunto de dimensionamento:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (para obter mais informações, consulte a [documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (para obter mais informações, consulte a [documentação do PowerShell](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (para obter mais informações, consulte a [documentação da CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Você também pode usar o [Resources.Azure.com](https://resources.azure.com) ou os [SDKs do Azure](https://azure.microsoft.com/downloads/) para listar as VMs em um conjunto de dimensionamento.

A apresentação exata da saída depende das opções fornecidas para o comando, mas aqui está alguns exemplos de saída da CLI:

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

Como você pode ver, a propriedade "instanceId" é apenas um número decimal. As IDs de instância podem ser reutilizadas para novas instâncias depois que instâncias antigas são excluídas.

>[!NOTE]
> Não há **nenhuma garantia** na forma como as IDs de instância são atribuídas às VMs no conjunto de dimensionamento. Eles podem parecer sequencialmente aumentando às vezes, mas esse não é sempre o caso. Não assuma uma dependência da maneira específica em que as IDs de instância são atribuídas às VMs.

## <a name="scale-set-vm-names"></a>Nomes de VM do conjunto de dimensionamento

Na saída de exemplo acima, também há um "nome" para a VM. Esse nome usa a forma "{scale-Set-name} _ {Instance-ID}". Esse nome é aquele que você vê na portal do Azure ao listar instâncias em um conjunto de dimensionamento:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

A parte {Instance-ID} do nome é o mesmo número decimal que a propriedade "instanceId" discutida anteriormente.

## <a name="instance-metadata-vm-name"></a>Nome da VM de metadados da instância

Se você consultar os [metadados da instância](../virtual-machines/windows/instance-metadata-service.md) de dentro de uma VM do conjunto de dimensionamento, verá um "nome" na saída:

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Esse nome é o mesmo que o nome discutido anteriormente.

## <a name="scale-set-vm-computer-name"></a>Nome do computador da VM do conjunto de dimensionamento

Cada VM em um conjunto de dimensionamento também obtém um nome de computador atribuído a ele. Esse nome de computador é o hostname da VM na [resolução de nomes DNS fornecida pelo Azure dentro da rede virtual](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Este nome de computador está no formato "{nome-do-computador-prefixo} {base-36-Instance-ID}".

A {base-36-Instance-ID} está na [base 36](https://en.wikipedia.org/wiki/Base36) e tem sempre seis dígitos de comprimento. Se a representação base 36 do número levar menos de seis dígitos, a {base-36-Instance-ID} será preenchida com zeros para torná-la seis dígitos. Por exemplo, uma instância com {Computer-name-prefix} "nsgvmss" e ID de instância 85 terá o nome do computador "nsgvmss00002D".

>[!NOTE]
> O prefixo do nome do computador é uma propriedade do modelo do conjunto de dimensionamento que você pode definir, de modo que ele pode ser diferente do próprio nome do conjunto de dimensionamento.
