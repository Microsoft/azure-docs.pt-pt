---
title: Quickstart - Create registry - Powershell
description: Quickly learn to create a private Docker registry in Azure Container Registry with PowerShell
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 872b2a29444e5278db34ce44741e2ca90d885702
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456369"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Quickstart: Create a private container registry using Azure PowerShell

O Azure Container Registry é um serviço de registo de contentor do Docker privado e gerido que serve para criar, armazenar e fornecer imagens de contentor do Docker. Neste guia de início rápido, irá aprender a criar um registo de contentor do Azure com o PowerShell. Then, use Docker commands to push a container image into the registry, and finally pull and run the image from your registry.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

This quickstart requires Azure PowerShell module. Execute `Get-Module -ListAvailable Az` para determinar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell).

Também tem de ter o Docker instalado localmente. Docker provides packages for [macOS][docker-mac], [Windows][docker-windows], and [Linux][docker-linux] systems.

Uma vez que o Azure Cloud Shell não inclui todos os componentes do Docker necessários (o daemon `dockerd`), não é possível utilizar o Cloud Shell para este guia de início rápido.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Sign in to your Azure subscription with the [Connect-AzAccount][Connect-AzAccount] command, and follow the on-screen directions.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Once you're authenticated with Azure, create a resource group with [New-AzResourceGroup][New-AzResourceGroup]. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Criar registo de contentor

Next, create a container registry in your new resource group with the [New-AzContainerRegistry][New-AzContainerRegistry] command.

O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. O exemplo seguinte cria um registo com o nome "myContainerRegistry007". Substitua *myContainerRegistry007* no comando seguinte e execute-o para criar o registo:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

In this quickstart you create a *Basic* registry, which is a cost-optimized option for developers learning about Azure Container Registry. For details on available service tiers, see [Container registry SKUs][container-registry-skus].

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de enviar e solicitar imagens de contentor, tem de iniciar sessão no registo. In production scenarios you should use an individual identity or service principal for container registry access, but to keep this quickstart brief, enable the admin user on your registry with the [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] command:

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Next, run [docker login][docker-login] to log in:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

O comando devolve `Login Succeeded` depois de estar concluído.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Once you're done working with the resources you created in this quickstart, use the [Remove-AzResourceGroup][Remove-AzResourceGroup] command to remove the resource group, the container registry, and the container images stored there:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you created an Azure Container Registry with Azure PowerShell, pushed a container image, and pulled and ran the image from the registry. Continue to the Azure Container Registry tutorials for a deeper look at ACR.

> [!div class="nextstepaction"]
> [Azure Container Registry tutorials][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
