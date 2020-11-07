---
title: Ativar a extensão VM utilizando o CLI Azure
description: Este artigo descreve como implementar extensões de máquinas virtuais para O Arco Azure habilitados a funcionar em ambientes de nuvem híbrida usando o Azure CLI.
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 59c984f4adaec2261d1b08748aa5a91c8246418d
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359120"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Ativar extensões Azure VM utilizando o CLI Azure

Este artigo mostra-lhe como implantar e desinstalar extensões Azure VM, suportadas por servidores ativados pelo Azure Arc, para uma máquina híbrida Linux ou Windows utilizando o CLI Azure.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="enable-extension"></a>Permitir extensão

Para ativar uma extensão VM no seu servidor ativado pelo Arco, utilize [a extensão da máquina de conexão az](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) com os `--machine-name` `--extension-name` `--location` `--type` parâmetros , , `settings` e `--publisher` parâmetros.

O exemplo a seguir permite a extensão VM do Log Analytics num servidor Linux ativado pelo Arco:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

O exemplo a seguir permite a extensão de script personalizado num servidor ativado pelo Arco:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

O exemplo a seguir permite a extensão VM do Cofre de Chaves (pré-visualização) num servidor ativado pelo Arco:

```azurecli
az connectedmachine machine-extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Extensões de lista instaladas

Para obter uma lista das extensões VM no seu servidor ativado pelo Arco, utilize [a lista de extensão da máquina de conexão az](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) com os `--machine-name` `--resource-group` parâmetros e parâmetros.

Exemplo:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Por predefinição, a saída dos comandos Azure CLI encontra-se em JSON (Notação de Objetos JavaScript). Para alterar a saída padrão para uma lista ou tabela, por exemplo, use [az configuração --saída](/cli/azure/reference-index). Também pode adicionar `--output` a qualquer comando para uma alteração única no formato de saída.

O exemplo a seguir mostra a saída parcial de JSON do `az connectedmachine machine-extension -list` comando:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Remover uma extensão instalada

Para remover uma extensão VM instalada no seu servidor ativado pelo Arco, utilize [a extensão da máquina de conexão az com](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) o `--extension-name` , e `--machine-name` `--resource-group` parâmetros.

Por exemplo, para remover a extensão VM do Log Analytics para o Linux, executar o seguinte comando:

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

- Pode implementar, gerir e remover extensões VM utilizando o [Azure PowerShell](manage-vm-extensions-powershell.md), a partir do [portal Azure](manage-vm-extensions-portal.md), ou [modelos Azure Resource Manager](manage-vm-extensions-template.md).

- As informações relativas à resolução de problemas podem ser encontradas no guia de [extensões VM de resolução de problemas.](troubleshoot-vm-extensions.md)
