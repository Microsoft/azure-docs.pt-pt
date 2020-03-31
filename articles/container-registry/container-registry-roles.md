---
title: Funções e permissões RBAC
description: Utilize o controlo de acesso baseado em funções azure (RBAC) e a gestão de identidade e acesso (IAM) para fornecer permissões de grãos finos aos recursos num registo de contentores Azure.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74893489"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Funções e permissões do Registo de Contentores Azure

O serviço de registo de contentores Azure suporta um conjunto de [funções azure incorporadas](../role-based-access-control/built-in-roles.md) que fornecem diferentes níveis de permissões a um registo de contentores Azure. Utilize o [controlo de acesso baseado em funções](../role-based-access-control/index.yml) azure (RBAC) para atribuir permissões específicas aos utilizadores, diretores de serviço ou outras identidades que necessitem de interagir com um registo. 

| Papel/Permissão       | [Gestor de Recursos de Acesso](#access-resource-manager) | [Criar/excluir registo](#create-and-delete-registry) | [Imagem de empurrar](#push-image) | [Puxar imagem](#pull-image) | [Eliminar dados de imagem](#delete-image-data) | [Mudar políticas](#change-policies) |   [Assine imagens](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Proprietário | X | X | X | X | X | X |  |  
| Contribuinte | X | X | X |  X | X | X |  |  
| Leitor | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Diferenciar utilizadores e serviços

Sempre que são aplicadas permissões, a melhor prática é fornecer o conjunto mais limitado de permissões para uma pessoa, ou serviço, para realizar uma tarefa. Os seguintes conjuntos de permissões representam um conjunto de capacidades que podem ser usadas por humanos e serviços sem cabeça.

### <a name="cicd-solutions"></a>Soluções CI/CD

Ao automatizar `docker build` comandos a partir de soluções CI/CD, precisa de `docker push` capacidades. Para estes cenários de serviço sem cabeça, sugerimos atribuir o papel **AcrPush.** Esta função, ao contrário do papel mais amplo do **Contribuinte,** impede que a conta realize outras operações de registo ou aceda ao Gestor de Recursos Azure.

### <a name="container-host-nodes"></a>Nódoa de hospedeiro de contentores

Da mesma forma, os nós que executam os seus contentores precisam do papel **AcrPull,** mas não devem necessitar de capacidades do **Leitor.**

### <a name="visual-studio-code-docker-extension"></a>Extensão visual do Código docker do estúdio

Para ferramentas como a extensão Visual Studio Code [Docker,](https://code.visualstudio.com/docs/azure/docker)é necessário um acesso adicional ao fornecedor de recursos para listar os registos de contentores azure disponíveis. Neste caso, forneça aos seus utilizadores acesso à função **de Leitor** ou **Colaborador.** Estas funções `docker push` `az acr list`permitem, `az acr build` `docker pull`e outras capacidades. 

## <a name="access-resource-manager"></a>Gestor de Recursos de Acesso

O acesso ao Azure Resource Manager é necessário para o portal Azure e para a gestão do registo com o [Azure CLI](/cli/azure/). Por exemplo, para obter uma lista `az acr list` de registos usando o comando, você precisa deste conjunto de permissões. 

## <a name="create-and-delete-registry"></a>Criar e eliminar o registo

A capacidade de criar e eliminar os registos de contentores Azure.

## <a name="push-image"></a>Imagem de empurrar

A capacidade `docker push` de uma imagem, ou empurrar outro [artefacto apoiado,](container-registry-image-formats.md) como um gráfico helm, para um registo. Requer [autenticação](container-registry-authentication.md) com o registo utilizando a identidade autorizada. 

## <a name="pull-image"></a>Puxar imagem

A capacidade `docker pull` de uma imagem não colocada em quarentena, ou de retirar outro [artefacto apoiado,](container-registry-image-formats.md) como um gráfico helm, de um registo. Requer [autenticação](container-registry-authentication.md) com o registo utilizando a identidade autorizada.

## <a name="delete-image-data"></a>Eliminar dados de imagem

A capacidade de [apagar imagens](container-registry-delete.md)de contentores , ou apagar outros [artefactos suportados,](container-registry-image-formats.md) como gráficos Helm, de um registo.

## <a name="change-policies"></a>Mudar políticas

A capacidade de configurar políticas num registo. As políticas incluem a purga de imagem, permitindo a quarentena e a assinatura de imagem.

## <a name="sign-images"></a>Assine imagens

A capacidade de assinar imagens, normalmente atribuídas a um processo automatizado, que usaria um diretor de serviço. Esta permissão é tipicamente combinada com [a imagem push](#push-image) para permitir empurrar uma imagem de confiança para um registo. Para mais detalhes, consulte a confiança do Conteúdo no Registo de [Contentores De Azure](container-registry-content-trust.md).

## <a name="custom-roles"></a>Funções personalizadas

Tal como acontece com outros recursos azure, pode criar as suas próprias [funções personalizadas](../role-based-access-control/custom-roles.md) com permissões de grãos finos para o Registo de Contentores Azure. Em seguida, atribuir as funções personalizadas aos utilizadores, diretores de serviço ou outras identidades que precisam interagir com um registo. 

Para determinar quais as permissões a aplicar a uma função personalizada, consulte a lista das [ações](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)do Microsoft.ContainerRegistry, reveja as ações permitidas das [funções ACR incorporadas,](../role-based-access-control/built-in-roles.md)ou execute o seguinte comando:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Para definir um papel personalizado, consulte [Passos para criar um papel personalizado.](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)

> [!IMPORTANT]
> Numa função personalizada, o Registo de Contentores Azure `Microsoft.ContainerRegistry/*` não `Microsoft.ContainerRegistry/registries/*` suporta atualmente wildcards como ou que concedem acesso a todas as ações correspondentes. Especifique qualquer ação necessária individualmente no papel.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a atribuição de funções RBAC a uma identidade Azure utilizando o [portal Azure,](../role-based-access-control/role-assignments-portal.md)o [Azure CLI](../role-based-access-control/role-assignments-cli.md)ou outras ferramentas Azure.

* Conheça [as opções](container-registry-authentication.md) de autenticação para o Registo de Contentores Azure.

* Aprenda a permitir [permissões de revisão](container-registry-repository-scoped-permissions.md) (pré-visualização) num registo de contentores.