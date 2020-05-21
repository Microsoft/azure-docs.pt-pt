---
title: Quickstart - Criar registo - PowerShell
description: Aprenda rapidamente a criar um registo privado de Docker no Registo de Contentores Azure com powerShell
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 792146abeaad137a29bfea25a59a957c5f69c5ac
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682771"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Quickstart: Criar um registo de contentores privados utilizando o Azure PowerShell

O Azure Container Registry é um serviço de registo de contentor do Docker privado e gerido que serve para criar, armazenar e fornecer imagens de contentor do Docker. Neste guia de início rápido, irá aprender a criar um registo de contentor do Azure com o PowerShell. Em seguida, use os comandos do Docker para empurrar uma imagem de recipiente para o registo, e finalmente puxe e execute a imagem do seu registo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este arranque rápido requer o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para determinar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell).

Também tem de ter o Docker instalado localmente. O Docker fornece pacotes para os sistemas [macOS][docker-mac], [Windows][docker-windows] e [Linux][docker-linux].

Uma vez que o Azure Cloud Shell não inclui todos os componentes do Docker necessários (o daemon `dockerd`), não é possível utilizar o Cloud Shell para este guia de início rápido.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se na subscrição do Azure com o comando [Connect-AzAccount][Connect-AzAccount] e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Assim que for autenticado com o Azure, crie um grupo de recursos com o [New-AzResourceGroup][New-AzResourceGroup]. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Criar registo de contentor

Em seguida, crie um registo de contentores no seu novo grupo de recursos com o comando [New-AzContainerRegistry.][New-AzContainerRegistry]

O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. O exemplo seguinte cria um registo com o nome "myContainerRegistry007". Substitua *myContainerRegistry007* no comando seguinte e execute-o para criar o registo:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Neste quickstart cria-se um registo *Básico,* que é uma opção otimizada para os desenvolvedores que aprendem sobre o Registo de Contentores Azure. Para mais informações sobre os níveis de serviço disponíveis, consulte os níveis de serviço de registo de [contentores][container-registry-skus].

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de enviar e solicitar imagens de contentor, tem de iniciar sessão no registo. Nos cenários de produção deve utilizar um gestor de identidade ou serviço individual para acesso ao registo de contentores, mas para manter este resumo de arranque rápido, ative o utilizador administrativo no seu registo com o comando [Get-AzContainerRegistryCredential:][Get-AzContainerRegistryCredential]

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

## <a name="clean-up-resources"></a>Limpar recursos

Uma vez que termine de trabalhar com os recursos que criou neste arranque rápido, utilize o comando [Remove-AzResourceGroup][Remove-AzResourceGroup] para remover o grupo de recursos, o registo do contentor e as imagens do recipiente aí armazenadas:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, criou um Registo de Contentores Azure com a Azure PowerShell, empurrou uma imagem de contentor, puxou e executou a imagem do registo. Continue aos tutoriais do Registo de Contentores Azure para uma olhada mais profunda na ACR.

> [!div class="nextstepaction"]
> [Tutoriais do Registo de Contentores de Azure][container-registry-tutorial-quick-task]

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
