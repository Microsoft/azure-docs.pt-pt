---
title: Ativar a extensão VM utilizando o CLI Azure
description: Este artigo descreve como implementar extensões de máquinas virtuais para O Arco Azure habilitados a funcionar em ambientes de nuvem híbrida usando o Azure CLI.
ms.date: 01/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6edb7d55e542f963c75693d535fa3b50dc5b827b
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916206"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Ativar extensões Azure VM utilizando o CLI Azure

Este artigo mostra-lhe como implantar e desinstalar extensões VM, suportadas por servidores ativados pelo Azure Arc, para uma máquina híbrida Linux ou Windows utilizando o CLI Azure.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Instale a extensão Azure CLI

Os comandos ConnectedMachine não são enviados como parte do CLI Azure. Antes de utilizar o CLI Azure para gerir as extensões VM no seu servidor híbrido gerido por servidores ativados pelo Arc, é necessário carregar a extensão ConnectedMachine. Executar o seguinte comando para obtê-lo:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Permitir extensão

Para ativar uma extensão VM no seu servidor ativado pelo Arco, utilize [a extensão az connectedmachine create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) com os `--machine-name` , e `--extension-name` `--location` `--type` `settings` `--publisher` parâmetros.

O exemplo a seguir permite a extensão VM do Log Analytics num servidor ativado pelo Arco:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

O exemplo a seguir permite a extensão de script personalizado num servidor ativado pelo Arco:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

O exemplo a seguir permite a extensão VM do Cofre de Chaves (pré-visualização) num servidor ativado pelo Arco:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Extensões de lista instaladas

Para obter uma lista das extensões VM no seu servidor ativado pelo Arco, utilize [a lista de extensões az connectedmachine](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) com os `--machine-name` parâmetros e `--resource-group` parâmetros.

Exemplo:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Por predefinição, a saída dos comandos Azure CLI encontra-se em JSON (Notação de Objetos JavaScript). Para alterar a saída padrão para uma lista ou tabela, por exemplo, use [az configuração --saída](/cli/azure/reference-index). Também pode adicionar `--output` a qualquer comando para uma alteração única no formato de saída.

O exemplo a seguir mostra a saída parcial de JSON do `az connectedmachine extension -list` comando:

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

Para remover uma extensão VM instalada no seu servidor ativado pelo Arco, utilize [a extensão az connectedmachine delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) com os `--extension-name` , e `--machine-name` `--resource-group` parâmetros.

Por exemplo, para remover a extensão VM do Log Analytics para o Linux, executar o seguinte comando:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

- Pode implementar, gerir e remover extensões VM utilizando o [Azure PowerShell](manage-vm-extensions-powershell.md), a partir do [portal Azure](manage-vm-extensions-portal.md), ou [modelos Azure Resource Manager](manage-vm-extensions-template.md).

- As informações relativas à resolução de problemas podem ser encontradas no guia de [extensões VM de resolução de problemas.](troubleshoot-vm-extensions.md)

- Reveja o artigo geral [de](/cli/azure/ext/connectedmachine/connectedmachine/extension) extensão Azure CLI VM para obter mais informações sobre os comandos.
