---
title: Compreenda as IDs de instância para vm sde de escala Azure
description: Entenda que os IDs de instância para a escala De VM Azure define as máquinas virtuais e as várias formas de superfície.
author: mimckitt
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: aa8b88bde4b6f8f02f6c9c81d0742d0dede761ac
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273690"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Compreenda as IDs de instância para vm sde de escala Azure
Este artigo descreve iDs de exemplo para conjuntos de escala e as várias maneiras que aparecem.

## <a name="scale-set-instance-ids"></a>IDs de instância de conjunto de escala

Cada VM num conjunto de escala obtém um ID de instância que o identifica exclusivamente. Este ID de instância é utilizado na escala definida APIs para fazer operações num VM específico no conjunto de escala. Por exemplo, pode especificar um ID de instância específica para reimagem ao utilizar a API de reimagem:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (para mais informações, consulte a documentação REST [API)](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)

Powershell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (para mais informações, consulte a [documentação Powershell)](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm)

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (para mais informações, consulte a [documentação CLI).](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)

Você pode obter a lista de iDs de instância, listando todas as instâncias em um conjunto de escala:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (para mais informações, consulte a documentação REST [API)](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list)

Powershell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (para mais informações, consulte a [documentação Powershell)](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm)

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (para mais informações, consulte a [documentação CLI).](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)

Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [SDKs Azure](https://azure.microsoft.com/downloads/) para listar os VMs num conjunto de escala.

A apresentação exata da saída depende das opções que fornece ao comando, mas aqui está uma saída de amostra do CLI:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
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

Como pode ver, a propriedade "instânciaId" é apenas um número decimal. Os iDs podem ser reutilizados para novos casos uma vez eliminados casos antigos.

>[!NOTE]
> Não há **garantias** sobre a forma como as identificações são atribuídas aos VMs no conjunto de escala. Podem parecer, por vezes, um aumento sequencial, mas nem sempre é assim. Não assuma uma dependência da forma específica como as identificações são atribuídas aos VMs.

## <a name="scale-set-vm-names"></a>Nomes vm definidos em escala

Na saída da amostra acima, existe também um "nome" para o VM. Este nome toma o formulário "{scale-set-name}_{instance-id}". Este nome é o que se vê no portal Azure quando enumera os casos num conjunto de escala:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

A parte {instance-id} do nome é o mesmo número decimal que a propriedade "instânciaId" discutida anteriormente.

## <a name="instance-metadata-vm-name"></a>Nome VM de metadados de instância

Se consultar os [metadados](../virtual-machines/windows/instance-metadata-service.md) da ocorrência dentro de um conjunto de escala VM, verá um "nome" na saída:

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Este nome é o mesmo que o nome discutido anteriormente.

## <a name="scale-set-vm-computer-name"></a>Nome do computador VM definido em escala

Cada VM num conjunto de escala também recebe um nome de computador atribuído a ele. Este nome de computador é o nome de anfitrião do VM na resolução de [nomeDNs fornecida pelo Azure dentro da rede virtual](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Este nome de computador é do formulário "{computer-name-prefix}{base-36-instância-id}".

O {base-36-instância-id} está na [base 36](https://en.wikipedia.org/wiki/Base36) e tem sempre seis dígitos de comprimento. Se a representação base 36 do número demorar menos de seis dígitos, o {base-36-instância-id} é acolchoado com zeros para torná-lo de seis dígitos de comprimento. Por exemplo, uma instância com {computer-name-prefix} "nsgvmss" e, por exemplo, ID 85 terá o nome de computador "nsgvmss0000002D".

>[!NOTE]
> O prefixo de nome do computador é uma propriedade do modelo de conjunto de escala que você pode definir, para que possa ser diferente do nome definido em escala em si.
