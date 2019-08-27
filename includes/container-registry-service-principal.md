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
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032352"
---
## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para criar uma entidade de serviço com acesso ao registro de contêiner, execute o seguinte script no [Azure cloud Shell](../articles/cloud-shell/overview.md) ou em uma instalação local do [CLI do Azure](/cli/azure/install-azure-cli). O script é formatado para o shell bash.

Antes de executar o script, atualize `ACR_NAME` a variável com o nome do registro de contêiner. O `SERVICE_PRINCIPAL_NAME` valor deve ser exclusivo dentro de seu locatário de Azure Active Directory. Se você receber um erro`'http://acr-service-principal' already exists.`"", especifique um nome diferente para a entidade de serviço.

Opcionalmente, você pode modificar `--role` o valor no comando [AZ ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] se quiser conceder permissões diferentes. Para obter uma lista completa de funções, consulte [funções e permissões de ACR](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Depois de executar o script, anote a **ID** e a **senha**da entidade de serviço. Depois de ter suas credenciais, você pode configurar seus aplicativos e serviços para autenticar o registro de contêiner como a entidade de serviço.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Usar uma entidade de serviço existente

Para conceder acesso de registro a uma entidade de serviço existente, você deve atribuir uma nova função à entidade de serviço. Assim como na criação de uma nova entidade de serviço, você pode conceder acesso de pull, push e pull e proprietário, entre outras.

O script a seguir usa o comando [AZ role Assignment Create][az-role-assignment-create] para conceder permissões de *pull* a uma entidade de serviço que `SERVICE_PRINCIPAL_ID` você especifica na variável. Ajuste o `--role` valor se desejar conceder um nível diferente de acesso.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
