---
title: Início rápido - criar um registo privado do Docker no Azure - PowerShell
description: Aprenda rapidamente a criar um registo de contentor do Docker no Azure com o PowerShell.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 23a62a9d8d24bbf2223fb5295190ad9acb39faf0
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806601"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Início rápido: Criar um registo de contentor privado com o Azure PowerShell

O Azure Container Registry é um serviço de registo de contentor do Docker privado e gerido que serve para criar, armazenar e fornecer imagens de contentor do Docker. Neste guia de início rápido, irá aprender a criar um registo de contentor do Azure com o PowerShell. Em seguida, utilize os comandos do Docker para enviar por push uma imagem de contentor para o registro e finalmente extrair e executar a imagem a partir do registo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este início rápido requer o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para determinar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell).

Também tem de ter o Docker instalado localmente. O Docker fornece pacotes para os sistemas [macOS][docker-mac], [Windows][docker-windows] e [Linux][docker-linux].

Uma vez que o Azure Cloud Shell não inclui todos os componentes do Docker necessários (o daemon `dockerd`), não é possível utilizar o Cloud Shell para este guia de início rápido.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o [Connect-AzAccount] [ Connect-AzAccount] de comando e siga na tela as direções.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Quando estiver autenticado com o Azure, crie um grupo de recursos com [New-AzResourceGroup][New-AzResourceGroup]. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Criar registo de contentor

Em seguida, crie um registo de contentor no seu novo grupo de recursos com o [New-AzContainerRegistry] [ New-AzContainerRegistry] comando.

O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. O exemplo seguinte cria um registo com o nome "myContainerRegistry007". Substitua *myContainerRegistry007* no comando seguinte e execute-o para criar o registo:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Neste início rápido de criar uma *básica* registo, o que é uma opção com otimização de custos para os desenvolvedores a aprender sobre o Azure Container Registry. Para obter detalhes sobre os escalões de serviço disponíveis, consulte [SKUs do registo de contentor][container-registry-skus].

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de enviar e solicitar imagens de contentor, tem de iniciar sessão no registo. Em cenários de produção deve usar uma identidade individual ou um principal de serviço para acesso ao registo de contentor, mas para manter este início rápido em breve, permitir que o utilizador de administrador no seu registo com o [Get-AzContainerRegistryCredential] [ Get-AzContainerRegistryCredential] comando:

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

Depois de concluir a trabalhar com os recursos que criou neste início rápido, utilize o [Remove-AzResourceGroup] [ Remove-AzResourceGroup] comando para remover o grupo de recursos, o registo de contentor e as imagens de contentor armazenadas nele:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um Azure Container Registry com o Azure PowerShell, enviou uma imagem de contentor e extraídos e executou a imagem do registo. Avance para os tutoriais de registo de contentor do Azure para uma análise mais profunda ACR.

> [!div class="nextstepaction"]
> [Tutoriais de registo de contentor do Azure][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.profile/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
