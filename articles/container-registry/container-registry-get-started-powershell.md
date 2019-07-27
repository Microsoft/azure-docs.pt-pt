---
title: Início rápido-criar um registro privado do Docker no Azure-PowerShell
description: Aprenda rapidamente a criar um registo de contentor do Docker no Azure com o PowerShell.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: f99b4ee6dd11a109d1c563c84debc2157cb03337
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309500"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Início rápido: Criar um registro de contêiner privado usando Azure PowerShell

O Azure Container Registry é um serviço de registo de contentor do Docker privado e gerido que serve para criar, armazenar e fornecer imagens de contentor do Docker. Neste guia de início rápido, irá aprender a criar um registo de contentor do Azure com o PowerShell. Em seguida, use os comandos do Docker para enviar uma imagem de contêiner para o registro e, por fim, efetuar pull e executar a imagem do registro.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este início rápido requer Azure PowerShell módulo. Execute `Get-Module -ListAvailable Az` para determinar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell).

Também tem de ter o Docker instalado localmente. O Docker fornece pacotes para sistemas [MacOS][docker-mac], [Windows][docker-windows]e [Linux][docker-linux] .

Uma vez que o Azure Cloud Shell não inclui todos os componentes do Docker necessários (o daemon `dockerd`), não é possível utilizar o Cloud Shell para este guia de início rápido.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Entre em sua assinatura do Azure com o comando [Connect-AzAccount][Connect-AzAccount] e siga as instruções na tela.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Quando você estiver autenticado com o Azure, crie um grupo de recursos com [New-AzResourceGroup][New-AzResourceGroup]. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Criar registo de contentor

Em seguida, crie um registro de contêiner em seu novo grupo de recursos com o comando [New-AzContainerRegistry][New-AzContainerRegistry] .

O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. O exemplo seguinte cria um registo com o nome "myContainerRegistry007". Substitua *myContainerRegistry007* no comando seguinte e execute-o para criar o registo:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Neste início rápido, você cria um registro *básico* , que é uma opção com otimização de custo para os desenvolvedores aprenderem sobre o registro de contêiner do Azure. Para obter detalhes sobre as camadas de serviço disponíveis, consulte [SKUs de registro de contêiner][container-registry-skus].

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de enviar e solicitar imagens de contentor, tem de iniciar sessão no registo. Em cenários de produção, você deve usar uma identidade individual ou entidade de serviço para acesso ao registro de contêiner, mas para manter este resumo rápido, habilite o usuário administrador no registro com o comando [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] :

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Em seguida, execute o [logon][docker-login] do Docker para fazer logon:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

O comando devolve `Login Succeeded` depois de estar concluído.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de trabalhar com os recursos criados neste guia de início rápido, use o comando [Remove-AzResourceGroup][Remove-AzResourceGroup] para remover o grupo de recursos, o registro de contêiner e as imagens de contêiner armazenadas lá:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você criou um registro de contêiner do Azure com Azure PowerShell, enviou por push uma imagem de contêiner e puxau e executou a imagem do registro. Continue nos tutoriais do registro de contêiner do Azure para obter uma análise mais profunda do ACR.

> [!div class="nextstepaction"]
> [Tutoriais do registro de contêiner do Azure][container-registry-tutorial-quick-task]

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
