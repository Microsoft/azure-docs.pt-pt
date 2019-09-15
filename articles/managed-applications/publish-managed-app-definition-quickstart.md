---
title: Criar definições de aplicações geridas do Azure | Microsoft Docs
description: Mostra como criar uma aplicação gerida do Azure que se destina aos membros da sua organização.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: b8c5a99a74446fcd126606b34135bba315ca1473
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995418"
---
# <a name="publish-an-azure-managed-application-definition"></a>Publicar uma definição de uma aplicação gerida do Azure

Este início rápido disponibiliza uma introdução ao funcionamento das aplicações geridas. Começa por adicionar uma definição de aplicação gerida a um catálogo interno de utilizadores da sua organização. Para simplificar a introdução, já criámos os ficheiros para a aplicação gerida. Estes ficheiros estão disponíveis através do GitHub. Pode aprender a criar estes ficheiros no tutorial [Criar uma aplicação de catálogo de serviço](publish-service-catalog-app.md).

Quando tiver terminado, tem um grupo de recursos chamado **appDefinitionGroup** que tem a definição da aplicação gerida.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Criar um grupo de recursos para a definição

A definição da sua aplicação gerida existe num grupo de recursos. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Para criar um grupo de recursos, utilize o comando seguinte:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Criar a definição da aplicação gerida

Ao definir a aplicação gerida, é selecionado um utilizador, um grupo ou uma aplicação que gere os recursos para o consumidor. Esta identidade tem permissões no grupo de recursos gerido, de acordo com a função que é atribuída. Normalmente, para gerir os recursos, é criado um grupo do Azure Active Directory. No entanto, neste artigo, utilize a sua própria identidade.

Para obter o ID de objeto da sua identidade, indique o seu nome principal de utilizador no comando seguinte:

```azurecli-interactive
userid=$(az ad user show --id example@contoso.org --query objectId --output tsv)
```

Em seguida, precisa do ID da definição da função da função incorporada RBAC a que pretende conceder acesso ao utilizador. O comando seguinte mostra como obter o ID da definição da função da função Proprietário:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Agora, crie o recurso de definição da aplicação gerida. A aplicação gerida contém apenas uma conta de armazenamento.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://github.com/Azure/azure-managedapp-samples/raw/master/Managed%20Application%20Sample%20Packages/201-managed-storage-account/managedstorage.zip"
```

Após a conclusão do comando, terá uma definição de aplicação gerida no seu grupo de recursos. 

Alguns dos parâmetros utilizados no exemplo anterior são:

* **resource-group**: O nome do grupo de recursos em que a definição de aplicativo gerenciado é criada.
* **lock-level**: O tipo de bloqueio colocado no grupo de recursos gerenciado. Impede o cliente de realizar operações não desejadas neste grupo. Atualmente, o único nível de bloqueio suportado é ReadOnly. Quando é especificado ReadOnly, o cliente só consegue ler os recursos presentes no grupo de recursos gerido. As entidades editoras que têm acesso ao grupo de recurso gerido estão isentos do bloqueio.
* **autorizações**: Descreve a ID da entidade de segurança e a ID de definição de função que são usadas para conceder permissão ao grupo de recursos gerenciado. É especificado com formato `<principalId>:<roleDefinitionId>`. Se for necessário mais do que um valor, especifique-os no formato `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Os valores são separados por espaço.
* **package-file-uri**: O local de um pacote. zip que contém os arquivos necessários. O pacote tem de ter os ficheiros **mainTemplate.json** e **createUiDefinition.json**. **mainTemplate.json** define os recursos do Azure que são criados como parte da aplicação gerida. O modelo é igual aos modelos normais do Resource Manager. **createUiDefinition.json** gera a interface de utilizador para os utilizadores que criem a aplicação gerida através do portal.

## <a name="next-steps"></a>Passos Seguintes

Publicou a definição da aplicação gerida. Agora, saiba como implementar uma instância dessa definição.

> [!div class="nextstepaction"]
> [Quickstart: Implantar aplicativo do catálogo de serviços](deploy-service-catalog-quickstart.md)