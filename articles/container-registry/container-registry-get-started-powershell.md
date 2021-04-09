---
title: Quickstart - Criar registo - PowerShell
description: Aprenda rapidamente a criar um registo privado de Docker no Registo de Contentores Azure com PowerShell
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc, devx-track-azurepowershell
ms.openlocfilehash: 91d4209ccf558bf7c8038d8a753ec038428bc484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020021"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Quickstart: Criar um registo de contentores privados utilizando a Azure PowerShell

O Azure Container Registry é um serviço de registo de contentor do Docker privado e gerido que serve para criar, armazenar e fornecer imagens de contentor do Docker. Neste guia de início rápido, irá aprender a criar um registo de contentor do Azure com o PowerShell. Em seguida, use os comandos do Docker para empurrar uma imagem do contentor para o registo, e finalmente puxe e execute a imagem do seu registo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este arranque rápido requer módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para determinar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell).

Também tem de ter o Docker instalado localmente. O Docker fornece pacotes para os sistemas [macOS][docker-mac], [Windows][docker-windows] e [Linux][docker-linux].

Uma vez que o Azure Cloud Shell não inclui todos os componentes do Docker necessários (o daemon `dockerd`), não é possível utilizar o Cloud Shell para este guia de início rápido.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se na sua subscrição Azure com o comando [Connect-AzAccount][Connect-AzAccount] e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Assim que for autenticado com o Azure, crie um grupo de recursos com [o New-AzResourceGroup][New-AzResourceGroup]. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Criar registo de contentor

Em seguida, crie um registo de contentores no seu novo grupo de recursos com o comando [New-AzContainerRegistry.][New-AzContainerRegistry]

O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. O exemplo seguinte cria um registo com o nome "myContainerRegistry007". Substitua *myContainerRegistry007* no comando seguinte e execute-o para criar o registo:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Neste quickstart você cria um registo *Básico,* que é uma opção otimizada para custos para desenvolvedores aprendendo sobre o Registo de Contentores Azure. Para obter mais informações sobre os níveis de serviço disponíveis, consulte [os níveis de serviço de registo de contentores][container-registry-skus].

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de enviar e solicitar imagens de contentor, tem de iniciar sessão no registo. Em cenários de produção deve utilizar uma identidade ou um principal de serviço individual para acesso ao registo de contentores, mas para manter este resumo de arranque rápido, ative o utilizador administrativo no seu registo com o comando [Get-AzContainerRegistryCredential:][Get-AzContainerRegistryCredential]

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Em seguida, execute o [docker login][docker-login] para iniciar sessão:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

O comando devolve `Login Succeeded` depois de estar concluído.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Assim que terminar de trabalhar com os recursos que criou neste arranque rápido, utilize o comando [Remove-AzResourceGroup][Remove-AzResourceGroup] para remover o grupo de recursos, o registo do contentor e as imagens do contentor aí armazenadas:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um registo de contentores Azure com a Azure PowerShell, empurrou uma imagem de contentor, puxou e executou a imagem a partir do registo. Continue os tutoriais do Registo do Contentor de Azure para uma olhada mais profunda no ACR.

> [!div class="nextstepaction"]
> [Tutoriais do Registo de Contentores de Azure][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutoriais de tarefas de registo de contentores Azure][container-registry-tutorial-quick-task]

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
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
