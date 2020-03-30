---
title: incluir ficheiro
description: incluir ficheiro
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9e4f2e355240ba8682cbe9f86f2be94e7dd0d92d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70032352"
---
## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para criar um diretor de serviço com acesso ao seu registo de contentores, execute o seguinte script na [Casca de Nuvem Azure](../articles/cloud-shell/overview.md) ou numa instalação local do [Azure CLI](/cli/azure/install-azure-cli). O guião está formatado para a concha bash.

Antes de executar o `ACR_NAME` script, atualize a variável com o nome do seu registo de contentores. O `SERVICE_PRINCIPAL_NAME` valor deve ser único dentro do seu inquilino azure Ative Directory. Se receber um`'http://acr-service-principal' already exists.`erro " " especifique um nome diferente para o diretor de serviço.

Pode alterar opcionalmente `--role` o valor no comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] se quiser conceder permissões diferentes. Para obter uma lista completa de funções, consulte [funções e permissões ACR](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Depois de executar o script, tome nota da **identificação** e **da senha**do diretor de serviço . Assim que tiver as suas credenciais, pode configurar as suas aplicações e serviços para autenticar o seu registo de contentores como diretor de serviço.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Utilize um diretor de serviço existente

Para conceder acesso ao registo a um diretor de serviço existente, deve atribuir uma nova função ao diretor de serviço. Tal como na criação de um novo diretor de serviço, pode conceder puxão, empurrão e puxão, e acesso do proprietário, entre outros.

O seguinte script usa a atribuição de [funções az criar][az-role-assignment-create] comando `SERVICE_PRINCIPAL_ID` para conceder permissões de *puxar* para um diretor de serviço que especifica na variável. Ajuste `--role` o valor se quiser conceder um nível diferente de acesso.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
