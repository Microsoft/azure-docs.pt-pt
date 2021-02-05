---
title: 'Quickstart: Criar um grupo de gestão com o Azure CLI'
description: Neste arranque rápido, você usa o CLI Azure para criar um grupo de gestão para organizar os seus recursos numa hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe38882bd3b025635662e228ae919a24b03dee78
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592471"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>Quickstart: Criar um grupo de gestão com o Azure CLI

Os grupos de gestão são contentores que o ajudam a gerir o acesso, a política e o cumprimento em várias subscrições. Crie estes recipientes para construir uma hierarquia eficaz e eficiente que possa ser usada com [a Azure Policy](../policy/overview.md) e [os Controlos de Acesso Baseados em Funções Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](overview.md)

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos para ser concluído. Há processos que correm pela primeira vez para configurar o serviço de grupos de gestão dentro do Azure para o seu diretório. Recebe uma notificação quando o processo estiver concluído. Para mais informações, consulte [a configuração inicial dos grupos de gestão.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Este arranque rápido requer que execute a versão 2.0.76 do Azure CLI ou posterior para instalar e utilizar o CLI localmente. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

- Qualquer utilizador Azure AD no arrendatário pode criar um grupo de gestão sem que o grupo de gestão escreva a permissão atribuída a esse utilizador se a [proteção da hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver ativada. Este novo grupo de gestão torna-se filho do Grupo de Gestão de Raízes ou do [grupo de gestão padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e o criador recebe uma atribuição de função de "Proprietário". O serviço de grupo de gestão permite esta capacidade para que as atribuições de funções não sejam necessárias ao nível da raiz. Nenhum utilizadores tem acesso ao Grupo de Gestão de Raízes quando este é criado. Para evitar o obstáculo de encontrar os Azure AD Global Admins para começar a usar grupos de gestão, permitimos a criação dos grupos de gestão iniciais ao nível da raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>Criar no Azure CLI

Para o Azure CLI, utilize o [grupo de gestão de conta az criar](/cli/azure/account/management-group#az_account_management_group_create) comando para criar um novo grupo de gestão. Neste exemplo, o **nome** do grupo de gestão é _Contoso._

```azurecli-interactive
az account management-group create --name 'Contoso'
```

O **nome** é um identificador único que está a ser criado. Este ID é usado por outros comandos para referenciar este grupo e não pode ser alterado mais tarde.

Se quiser que o grupo de gestão apresente um nome diferente dentro do portal Azure, adicione o parâmetro **do nome de visualização.** Por exemplo, para criar um grupo de gestão com o Nome do Grupo de Contoso e o nome de exibição de "Grupo Contoso", utilize o seguinte comando:

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gestão é criado no âmbito do grupo de gestão de raiz. Para especificar um grupo de gestão diferente como o progenitor, use o parâmetro **principal** e forneça o nome do grupo-mãe.

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o grupo de gestão acima criado, utilize o comando [az-grupo de gestão de conta:](/cli/azure/account/management-group#az_account_management_group_delete)

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um grupo de gestão para organizar a sua hierarquia de recursos. O grupo de gestão pode realizar subscrições ou outros grupos de gestão.

Para saber mais sobre grupos de gestão e como gerir a sua hierarquia de recursos, continue a:

> [!div class="nextstepaction"]
> [Gerir os seus recursos com grupos de gestão](./manage.md)