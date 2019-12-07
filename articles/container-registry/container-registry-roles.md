---
title: Funções e permissões de RBAC
description: Use o RBAC (controle de acesso baseado em função) do Azure e IAM (gerenciamento de identidade e acesso) para fornecer permissões refinadas aos recursos em um registro de contêiner do Azure.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893489"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Funções e permissões do registro de contêiner do Azure

O serviço de registro de contêiner do Azure dá suporte a um conjunto de [funções internas do Azure](../role-based-access-control/built-in-roles.md) que fornecem diferentes níveis de permissões para um registro de contêiner do Azure. Use o RBAC ( [controle de acesso baseado em função](../role-based-access-control/index.yml) ) do Azure para atribuir permissões específicas a usuários, entidades de serviço ou outras identidades que precisam interagir com um registro. 

| Função/permissão       | [Acessar o Resource Manager](#access-resource-manager) | [Criar/Excluir registro](#create-and-delete-registry) | [Enviar imagem por push](#push-image) | [Imagem de pull](#pull-image) | [Excluir dados de imagem](#delete-image-data) | [Alterar políticas](#change-policies) |   [Assinar imagens](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Proprietário | X | X | X | X | X | X |  |  
| Contribuinte | X | X | X |  X | X | X |  |  
| Leitor | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Diferenciar usuários e serviços

Sempre que as permissões são aplicadas, uma prática recomendada é fornecer o conjunto mais limitado de permissões para uma pessoa, ou serviço, para realizar uma tarefa. Os conjuntos de permissões a seguir representam um conjunto de recursos que podem ser usados por seres humanos e serviços sem periféricos.

### <a name="cicd-solutions"></a>Soluções de CI/CD

Ao automatizar comandos de `docker build` de soluções de CI/CD, você precisa de recursos de `docker push`. Para esses cenários de serviço sem periféricos, sugerimos atribuir a função **AcrPush** . Essa função, diferentemente da função de **colaborador** mais ampla, impede que a conta execute outras operações de registro ou acessando Azure Resource Manager.

### <a name="container-host-nodes"></a>Nós de host do contêiner

Da mesma forma, os nós que executam seus contêineres precisam da função **AcrPull** , mas não devem exigir recursos de **leitor** .

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code extensão do Docker

Para ferramentas como a [extensão do docker](https://code.visualstudio.com/docs/azure/docker)Visual Studio Code, o acesso ao provedor de recursos adicional é necessário para listar os registros de contêiner do Azure disponíveis. Nesse caso, forneça aos usuários acesso à função **leitor** ou **colaborador** . Essas funções permitem `docker pull`, `docker push`, `az acr list`, `az acr build`e outros recursos. 

## <a name="access-resource-manager"></a>Acessar o Resource Manager

O acesso ao Azure Resource Manager é necessário para o gerenciamento de portal do Azure e registro com o [CLI do Azure](/cli/azure/). Por exemplo, para obter uma lista de registros usando o comando `az acr list`, você precisa desse conjunto de permissões. 

## <a name="create-and-delete-registry"></a>Criar e excluir registro

A capacidade de criar e excluir registros de contêiner do Azure.

## <a name="push-image"></a>Enviar imagem por push

A capacidade de `docker push` uma imagem ou enviar por push outro [artefato com suporte](container-registry-image-formats.md) , como um gráfico Helm, para um registro. Requer [autenticação](container-registry-authentication.md) com o registro usando a identidade autorizada. 

## <a name="pull-image"></a>Imagem de pull

A capacidade de `docker pull` uma imagem não em quarentena ou efetuar pull de outro [artefato com suporte](container-registry-image-formats.md) , como um gráfico Helm, de um registro. Requer [autenticação](container-registry-authentication.md) com o registro usando a identidade autorizada.

## <a name="delete-image-data"></a>Excluir dados de imagem

A capacidade de [Excluir imagens de contêiner](container-registry-delete.md)ou excluir outros [artefatos com suporte](container-registry-image-formats.md) , como gráficos Helm, de um registro.

## <a name="change-policies"></a>Alterar políticas

A capacidade de configurar políticas em um registro. As políticas incluem a limpeza de imagens, a habilitação de quarentena e a assinatura de imagens.

## <a name="sign-images"></a>Assinar imagens

A capacidade de assinar imagens, geralmente atribuída a um processo automatizado, que usaria uma entidade de serviço. Essa permissão normalmente é combinada com a [imagem Push](#push-image) para permitir o envio por push de uma imagem confiável para um registro. Para obter detalhes, consulte [confiança de conteúdo no registro de contêiner do Azure](container-registry-content-trust.md).

## <a name="custom-roles"></a>Funções personalizadas

Assim como acontece com outros recursos do Azure, você pode criar suas próprias [funções personalizadas](../role-based-access-control/custom-roles.md) com permissões refinadas para o registro de contêiner do Azure. Em seguida, atribua as funções personalizadas a usuários, entidades de serviço ou outras identidades que precisam interagir com um registro. 

Para determinar quais permissões aplicar a uma função personalizada, consulte a lista de [ações](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)Microsoft. ContainerRegistry, examine as ações permitidas das [funções ACR internas](../role-based-access-control/built-in-roles.md)ou execute o seguinte comando:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Para definir uma função personalizada, consulte [etapas para criar uma função personalizada](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> Em uma função personalizada, o registro de contêiner do Azure atualmente não dá suporte a curingas como `Microsoft.ContainerRegistry/*` ou `Microsoft.ContainerRegistry/registries/*` que concedem acesso a todas as ações correspondentes. Especifique qualquer ação necessária individualmente na função.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como atribuir funções RBAC a uma identidade do Azure usando o [portal do Azure](../role-based-access-control/role-assignments-portal.md), o [CLI do Azure](../role-based-access-control/role-assignments-cli.md)ou outras ferramentas do Azure.

* Saiba mais sobre [as opções de autenticação](container-registry-authentication.md) para o registro de contêiner do Azure.

* Saiba como habilitar [permissões no escopo do repositório](container-registry-repository-scoped-permissions.md) (versão prévia) em um registro de contêiner.