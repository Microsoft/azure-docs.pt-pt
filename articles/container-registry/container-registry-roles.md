---
title: Registro de contêiner do Azure-funções e permissões
description: Use o RBAC (controle de acesso baseado em função) do Azure e IAM (gerenciamento de identidade e acesso) para fornecer permissões refinadas aos recursos em um registro de contêiner do Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.openlocfilehash: 69104cdaeb4abfc15e2ac4209e1ddbc610656c13
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793991"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Funções e permissões do registro de contêiner do Azure

O serviço de registro de contêiner do Azure dá suporte a um conjunto de funções do Azure que fornecem diferentes níveis de permissões para um registro de contêiner do Azure. Use o RBAC ( [controle de acesso baseado em função](../role-based-access-control/index.yml) ) do Azure para atribuir permissões específicas a usuários ou a entidades de serviço que precisam interagir com um registro.

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

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como atribuir funções RBAC a uma identidade do Azure usando o [portal do Azure](../role-based-access-control/role-assignments-portal.md), o [CLI do Azure](../role-based-access-control/role-assignments-cli.md)ou outras ferramentas do Azure.

* Saiba mais sobre [as opções de autenticação](container-registry-authentication.md) para o registro de contêiner do Azure.
