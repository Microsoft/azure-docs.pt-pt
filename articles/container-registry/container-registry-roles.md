---
title: O Azure Container Registry - funções e permissões
description: Utilize o controlo de acesso baseado em função do Azure (RBAC) e gestão de identidades e acessos (IAM) para fornecer permissões detalhadas para recursos num Azure container registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/20/2019
ms.author: danlep
ms.openlocfilehash: 0148894bb013dc9f8cce595f14919f87d6292df8
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593629"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Permissões e funções de registo de contentor do Azure

O serviço do Azure Container Registry suporta um conjunto de funções do Azure que fornecer diferentes níveis de permissões para um Azure container registry. Utilizar o Azure [controlo de acesso baseado em funções](../role-based-access-control/index.yml) (RBAC) para atribuir permissões específicas para os utilizadores ou principais que precisam para interagir com um registo de serviço.

| / Permissões da função       | [Access Resource Manager](#access-resource-manager) | [Criar/eliminar registo](#create-and-delete-registry) | [Enviar imagem](#push-image) | [Imagem de extração](#pull-image) | [Eliminar dados de imagem](#delete-image-data) | [Alterar as políticas](#change-policies) |   [Imagens de início de sessão](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Proprietário | X | X | X | X | X | X |  |  
| Contribuinte | X | X | X |  X | X | X |  |  
| Leitor | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | X |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Diferenciar a utilizadores e serviços

Quaisquer permissões de tempo são aplicadas, é recomendado fornecer o conjunto mais limitado de permissões para uma pessoa ou serviço realizar uma tarefa. Os seguintes conjuntos de permissão representam um conjunto de recursos que podem ser utilizados por seres humanos e de serviços sem periféricos.

### <a name="cicd-solutions"></a>Soluções de CI/CD

Ao automatizar `docker build` comandos de soluções de CI/CD, precisa `docker push` capacidades. Nestes cenários de serviço sem periféricos, sugerimos que atribuir a **AcrPush** função. Esta função, ao contrário do mais abrangente **contribuinte** função, impede que a conta de efetuar outras operações de registo ou aceder ao Azure Resource Manager.

### <a name="container-host-nodes"></a>Nós de anfitrião de contentor

Da mesma forma, nós os contentores em execução têm do **AcrPull** função, mas não deve exigir **leitor** capacidades.

### <a name="visual-studio-code-docker-extension"></a>Extensão do Visual Studio Docker de código

Para ferramentas como o Visual Studio Code [extensão Docker](https://code.visualstudio.com/docs/azure/docker), é necessário acesso de fornecedor de recursos adicionais para listar os registos de contentores do Azure disponíveis. Neste caso, fornecer aos utilizadores acesso para o **leitor** ou **contribuinte** função. Estas funções permitem `docker pull`, `docker push`, `az acr list`, `az acr build`e outros recursos. 

## <a name="access-resource-manager"></a>Gestor de recursos de acesso

Acesso de Gestor de recursos do Azure é necessário para o Azure management portal e do Registro com o [CLI do Azure](/cli/azure/). Por exemplo, para obter uma lista de registos ao utilizar o `az acr list` de comando, tem de ter esta permissão definida. 

## <a name="create-and-delete-registry"></a>Criar e eliminar o registo

A capacidade de criar e eliminar registos de contentores do Azure.

## <a name="push-image"></a>Enviar imagem

A capacidade de `docker push` uma imagem, ou emitir via push outra [suportado artefacto](container-registry-image-formats.md) como um gráfico do Helm, para um registo. Requer [autenticação](container-registry-authentication.md) com o registro usando a identidade autorizada. 

## <a name="pull-image"></a>Imagem de extração

A capacidade de `docker pull` um não-colocados em quarentena de imagem ou extrair outra [suportado artefacto](container-registry-image-formats.md) como um gráfico do Helm, de um registo. Requer [autenticação](container-registry-authentication.md) com o registro usando a identidade autorizada.

## <a name="delete-image-data"></a>Eliminar dados de imagem

A capacidade de [eliminar imagens de contentor ou de repositórios](container-registry-delete.md).

## <a name="change-policies"></a>Alterar as políticas

A capacidade para configurar políticas num registo. Políticas incluem a remoção de imagem, permitindo a quarentena e assinatura de imagem.

## <a name="sign-images"></a>Imagens de início de sessão

A capacidade de iniciar sessão imagens, normalmente, é atribuídas a um processo automatizado, que usaria um principal de serviço. Esta permissão é normalmente combinada com [imagem push](#push-image) para permitir o envio de uma imagem de fidedigna para um registo. Para obter detalhes, consulte [conteúdo de confiança no Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a atribuição de funções RBAC para uma identidade do Azure utilizando o [portal do Azure](../role-based-access-control/role-assignments-portal.md), o [CLI do Azure](../role-based-access-control/role-assignments-cli.md), ou outras ferramentas do Azure.

* Saiba mais sobre [opções de autenticação](container-registry-authentication.md) do Azure Container Registry.
