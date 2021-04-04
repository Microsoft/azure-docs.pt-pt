---
title: Funções e permissões de registo
description: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) e a gestão de identidade e acesso (IAM) para fornecer permissões de grãos finos aos recursos num registo de contentores Azure.
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: 097ccf89caf63d2a504d072cf04c2b534a57a031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207959"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Funções e permissões do Registo de Contentores de Azure

O serviço de registo de contentores Azure suporta um conjunto de [funções Azure incorporadas](../role-based-access-control/built-in-roles.md) que fornecem diferentes níveis de permissões a um registo de contentores Azure. Utilize [o controlo de acesso baseado em funções (Azure RBAC)](../role-based-access-control/index.yml) para atribuir permissões específicas aos utilizadores, principais de serviço ou outras identidades que necessitem de interagir com um registo, por exemplo, para puxar ou empurrar imagens de contentores. Também pode definir [funções personalizadas](#custom-roles) com permissões de grãos finos para um registo para diferentes operações.

| Função/Permissão       | [Gestor de Recursos de Acesso](#access-resource-manager) | [Criar/apagar registo](#create-and-delete-registry) | [Imagem de empurrar](#push-image) | [Imagem de puxar](#pull-image) | [Eliminar dados de imagem](#delete-image-data) | [Mudar políticas](#change-policies) |   [Assinar imagens](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Proprietário | X | X | X | X | X | X |  |  
| Contribuinte | X | X | X |  X | X | X |  |  
| Leitor | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| Rio AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="assign-roles"></a>Atribuir funções

Consulte [passos para adicionar uma atribuição](../role-based-access-control/role-assignments-steps.md) de papel para etapas de alto nível para adicionar uma atribuição de papel a um utilizador, grupo, principal de serviço ou identidade gerida existente. Pode utilizar o portal Azure, O Azure CLI ou outras ferramentas Azure.

Ao criar um principal de serviço, também configura o seu acesso e permissões aos recursos da Azure, como um registo de contentores. Para um script de exemplo utilizando o Azure CLI, consulte [a autenticação do Registo do Contentor Azure com os principais serviços](container-registry-auth-service-principal.md#create-a-service-principal).

## <a name="differentiate-users-and-services"></a>Diferenciar utilizadores e serviços

Sempre que as permissões são aplicadas, a melhor prática é fornecer o conjunto mais limitado de permissões para uma pessoa, ou serviço, para realizar uma tarefa. Os seguintes conjuntos de permissões representam um conjunto de capacidades que podem ser usadas por humanos e serviços sem cabeça.

### <a name="cicd-solutions"></a>Soluções CI/CD

Ao automatizar `docker build` comandos a partir de soluções CI/CD, precisa de `docker push` capacidades. Para estes cenários de serviço sem cabeça, recomendamos a atribuição do papel **AcrPush.** Esta função, ao contrário do papel mais amplo **do Contribuinte,** impede que a conta realize outras operações de registo ou aceda ao Gestor de Recursos Azure.

### <a name="container-host-nodes"></a>Nódoas de hospedeiro de contentores

Da mesma forma, os nós que executam os seus recipientes precisam do papel **de AcrPull,** mas não devem exigir capacidades **de Reader.**

### <a name="visual-studio-code-docker-extension"></a>Extensão visual Studio Code Docker

Para ferramentas como a extensão Visual Studio Code [Docker,](https://code.visualstudio.com/docs/azure/docker)é necessário um acesso adicional do fornecedor de recursos para listar os registos de contentores Azure disponíveis. Neste caso, forneça aos seus utilizadores acesso à função **Reader** ou **Contribuinte.** Estas funções `docker pull` `docker push` permitem, `az acr list` e `az acr build` outras capacidades. 

## <a name="access-resource-manager"></a>Gestor de Recursos de Acesso

O acesso do Gestor de Recursos Azure é necessário para o portal Azure e gestão de registos com o [Azure CLI](/cli/azure/). Por exemplo, para obter uma lista de registos utilizando o `az acr list` comando, precisa deste conjunto de permissões. 

## <a name="create-and-delete-registry"></a>Criar e apagar registo

A capacidade de criar e eliminar registos de contentores Azure.

## <a name="push-image"></a>Imagem de empurrar

A capacidade de `docker push` uma imagem, ou empurrar outro [artefacto suportado,](container-registry-image-formats.md) como um gráfico helm, para um registo. Requer [autenticação](container-registry-authentication.md) com o registo utilizando a identidade autorizada. 

## <a name="pull-image"></a>Imagem de puxar

A capacidade `docker pull` de uma imagem não em quarentena, ou puxar outro artefacto [suportado,](container-registry-image-formats.md) como um gráfico helm, de um registo. Requer [autenticação](container-registry-authentication.md) com o registo utilizando a identidade autorizada.

## <a name="delete-image-data"></a>Eliminar dados de imagem

A capacidade de [eliminar imagens de contentores](container-registry-delete.md), ou eliminar [outros artefactos suportados,](container-registry-image-formats.md) como gráficos Helm, de um registo.

## <a name="change-policies"></a>Mudar políticas

A capacidade de configurar políticas num registo. As políticas incluem purga de imagem, ativação da quarentena e assinatura de imagem.

## <a name="sign-images"></a>Assinar imagens

A capacidade de assinar imagens, normalmente atribuídas a um processo automatizado, que usaria um principal de serviço. Esta permissão é tipicamente combinada com [a imagem push](#push-image) para permitir empurrar uma imagem de confiança para um registro. Para mais informações, consulte [a confiança do conteúdo no Registo do Contentor Azure](container-registry-content-trust.md).

## <a name="custom-roles"></a>Funções personalizadas

Tal como acontece com outros recursos Azure, pode criar [funções personalizadas](../role-based-access-control/custom-roles.md) com permissões de grãos finos para o Registo do Contentor de Azure. Em seguida, atribua as funções personalizadas aos utilizadores, principais de serviço ou outras identidades que precisem de interagir com um registo. 

Para determinar quais as permissões a aplicar a uma função personalizada, consulte a lista de [ações](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)do Microsoft.ContainerRegistry , reveja as ações permitidas das [funções ACR incorporadas,](../role-based-access-control/built-in-roles.md)ou executar o seguinte comando:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Para definir um papel personalizado, consulte [Passos para criar um papel personalizado.](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)

> [!IMPORTANT]
> Numa função personalizada, o Registo de Contentores Azure não suporta atualmente wildcards como `Microsoft.ContainerRegistry/*` ou `Microsoft.ContainerRegistry/registries/*` que concedem acesso a todas as ações correspondentes. Especifique qualquer ação necessária individualmente no papel.

### <a name="example-custom-role-to-import-images"></a>Exemplo: Papel personalizado para importar imagens

Por exemplo, o seguinte JSON define as ações mínimas para um papel personalizado que permite [importar imagens](container-registry-import-images.md) para um registo.

```json
{
   "assignableScopes": [
     "/subscriptions/<optional, but you can limit the visibility to one or more subscriptions>"
   ],
   "description": "Can import images to registry",
   "Name": "AcrImport",
   "permissions": [
     {
       "actions": [
         "Microsoft.ContainerRegistry/registries/push/write",
         "Microsoft.ContainerRegistry/registries/pull/read",
         "Microsoft.ContainerRegistry/registries/read",
         "Microsoft.ContainerRegistry/registries/importImage/action"
       ],
       "dataActions": [],
       "notActions": [],
       "notDataActions": []
     }
   ],
   "roleType": "CustomRole"
 }
```

Para criar ou atualizar uma função personalizada utilizando a descrição JSON, utilize o modelo [Azure CLI](../role-based-access-control/custom-roles-cli.md), [Azure Resource Manager](../role-based-access-control/custom-roles-template.md), [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)ou outras ferramentas Azure. Adicione ou remova atribuições de funções para um papel personalizado da mesma forma que gere atribuições de papéis para funções Azure incorporadas.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a atribuição de funções Azure a uma identidade Azure utilizando o [portal Azure](../role-based-access-control/role-assignments-portal.md), o [Azure CLI,](../role-based-access-control/role-assignments-cli.md)ou outras ferramentas Azure.

* Saiba mais [sobre as opções de autenticação](container-registry-authentication.md) do Registo do Contentor Azure.

* Saiba se permite autorizar [permissões com âmbito de repositório](container-registry-repository-scoped-permissions.md) (pré-visualização) num registo de contentores.
