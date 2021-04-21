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
ms.openlocfilehash: 592f62eaf1627733f8cf20460b57e3f474f17963
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800022"
---
## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para criar um principal de serviço com acesso ao seu registo de contentores, execute o seguinte script na [Azure Cloud Shell](../articles/cloud-shell/overview.md) ou numa instalação local do [Azure CLI](/cli/azure/install-azure-cli). O guião está formatado para a casca bash.

Antes de executar o script, atualize a `ACR_NAME` variável com o nome do seu registo de contentores. O `SERVICE_PRINCIPAL_NAME` valor deve ser único dentro do seu inquilino Azure Ative Directory. Se receber um `'http://acr-service-principal' already exists.` erro de " ", especifique um nome diferente para o principal de serviço.

Pode modificar opcionalmente o `--role` valor no comando [ad sp create-for-rbac][az-ad-sp-create-for-rbac] se quiser conceder diferentes permissões. Para obter uma lista completa de funções, consulte [as funções e permissões da ACR](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Depois de executar o script, tome nota da **identificação** e **palavra-passe** do diretor de serviço. Assim que tiver as suas credenciais, pode configurar as suas aplicações e serviços para autenticar o seu registo de contentores como o principal do serviço.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Utilize um principal de serviço existente

Para conceder acesso ao registo a um diretor de serviço existente, deve atribuir uma nova função ao diretor de serviço. Tal como com a criação de um novo diretor de serviço, pode conceder puxar, empurrar e puxar, e o acesso ao proprietário, entre outros.

O seguinte script usa a [atribuição de funções az criar][az-role-assignment-create] comando para conceder permissões de *pull* a um principal de serviço que especifica na `SERVICE_PRINCIPAL_ID` variável. Ajuste o `--role` valor se quiser conceder um nível de acesso diferente.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
