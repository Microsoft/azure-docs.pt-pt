---
title: Compreenda os IDs de exemplo para VMs de escala Azure
description: Compreenda os IDs de exemplo para a escala VM Azure define máquinas virtuais e as várias maneiras que elas aparecem.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/22/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: a62c9bbde0726c8dec8fba1f69e221bd4e4b63bc
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209854"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Compreenda os IDs de exemplo para VMs de escala Azure
Este artigo descreve os IDs de exemplo para conjuntos de escala e as várias formas de superfície.

## <a name="scale-set-instance-ids"></a>IDs de instância definidas em escala

Cada VM em um conjunto de escala obtém uma identificação de exemplo que o identifica de forma única. Este ID de exemplo é utilizado nas APIs definidas na escala para fazer operações num VM específico no conjunto de escala. Por exemplo, pode especificar um ID de instância específico para reimagem quando utilizar a API de reimagem:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/reimage?api-version={apiVersion}` (para mais informações, consulte a [documentação REST API)](/rest/api/compute/virtualmachinescalesetvms/reimage)

Powershell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (para mais informações, consulte a [documentação powershell)](/powershell/module/az.compute/set-azvmssvm)

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (para mais informações, consulte a documentação do [CLI).](/cli/azure/vmss)

Pode obter a lista de IDs de instância listando todas as instâncias num conjunto de escala:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (para mais informações, consulte a [documentação REST API)](/rest/api/compute/virtualmachinescalesetvms/list)

Powershell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (para mais informações, consulte a [documentação powershell)](/powershell/module/az.compute/get-azvmssvm)

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (para mais informações, consulte a documentação do [CLI).](/cli/azure/vmss)

Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [Azure SDKs](https://azure.microsoft.com/downloads/) para listar os VMs num conjunto de escala.

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

Como pode ver, a propriedade "instanceId" é apenas um número decimal. Os IDs de caso podem ser reutilizados para novos casos uma vez que as instâncias antigas são eliminadas.

>[!NOTE]
> Não **há garantias** sobre a forma como os IDs são atribuídos aos VMs no conjunto de escala. Podem parecer sequencialmente aumentando às vezes, mas nem sempre é assim. Não se dependência da forma específica como os IDs são atribuídos aos VM.

## <a name="scale-set-vm-names"></a>Conjunto de escala nomes VM

Na produção da amostra acima, há também um "nome" para o VM. Este nome toma o formulário "{scale-set-name}_{instance-id}". Este nome é o que se vê no portal Azure quando lista casos numa escala definida:

![Screenshot mostrando uma lista de casos em uma escala de máquina virtual definida no portal Azure.](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

A parte {instance-id} do nome é o mesmo número decimal que a propriedade "instanceId" discutida anteriormente.

## <a name="instance-metadata-vm-name"></a>Nome VM de metdata de exemplo

Se consultar os [metadados](../virtual-machines/windows/instance-metadata-service.md) de exemplo dentro de um VM definido em escala, vê um "nome" na saída:

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

## <a name="scale-set-vm-computer-name"></a>Conjunto de escala VM nome de computador

Cada VM num conjunto de escala também recebe um nome de computador atribuído a ele. Este nome de computador é o nome de anfitrião do VM na [resolução de nome DNS fornecida pelo Azure dentro da rede virtual](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Este nome de computador é do formulário "{computer-name-prefix}{base-36-instance-id}".

O {base-36-instance-id} está na [base 36](https://en.wikipedia.org/wiki/Base36) e tem sempre seis dígitos de comprimento. Se a representação base 36 do número demorar menos de seis dígitos, o {base-36-instance-id} é acolchoado com zeros para torná-lo de seis dígitos de comprimento. Por exemplo, uma instância com {computer-name-prefix} "nsgvmsss" e exemplo ID 85 terá o nome de computador "nsgvmss0002D".

>[!NOTE]
> O prefixo de nome do computador é uma propriedade do modelo de conjunto de escala que pode definir, para que possa ser diferente do próprio nome definido em escala.
