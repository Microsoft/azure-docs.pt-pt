---
title: 'Quickstart: Criar um grupo de gestão com REST API'
description: Neste quickstart, você usa a API REST para criar um grupo de gestão para organizar os seus recursos numa hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 26a1c0a8025a00155bcf0498e6dfa89a8a73d983
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259137"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>Quickstart: Criar um grupo de gestão com REST API

Os grupos de gestão são contentores que o ajudam a gerir o acesso, a política e o cumprimento em várias subscrições. Crie estes recipientes para construir uma hierarquia eficaz e eficiente que possa ser usada com [a Azure Policy](../policy/overview.md) e [os Controlos de Acesso Baseados em Funções Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](overview.md)

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos para ser concluído. Há processos que correm pela primeira vez para configurar o serviço de grupos de gestão dentro do Azure para o seu diretório. Recebe uma notificação quando o processo estiver concluído. Para mais informações, consulte [a configuração inicial dos grupos de gestão.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Se ainda não o fez, instale [o ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia pedidos HTTP para AZure Resource Manager-based REST APIs. Em vez disso, pode utilizar a função "Try It" na documentação REST ou ferramentas como [o Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) ou [o Carteiro](https://www.postman.com)da PowerShell.

- Qualquer utilizador Azure AD no arrendatário pode criar um grupo de gestão sem que o grupo de gestão escreva a permissão atribuída a esse utilizador se a [proteção da hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver ativada. Este novo grupo de gestão torna-se filho do Grupo de Gestão de Raízes ou do [grupo de gestão padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e o criador recebe uma atribuição de função de "Proprietário". O serviço de grupo de gestão permite esta capacidade para que as atribuições de funções não sejam necessárias ao nível da raiz. Nenhum utilizadores tem acesso ao Grupo de Gestão de Raízes quando este é criado. Para evitar o obstáculo de encontrar os Azure AD Global Admins para começar a usar grupos de gestão, permitimos a criação dos grupos de gestão iniciais ao nível da raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>Criar em REST API

Para rest API, utilize os [Grupos de Gestão - Criar ou Atualizar](/rest/api/managementgroups/managementgroups/createorupdate) ponto final para criar um novo grupo de gestão. Neste exemplo, o grupo de gestão **grupoId** é _Contoso._

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Nenhum corpo de pedido

O **groupId** é um identificador único que está a ser criado. Este ID é usado por outros comandos para referenciar este grupo e não pode ser alterado mais tarde.

Se pretender que o grupo de gestão apresente um nome diferente dentro do portal Azure, adicione a propriedade **properties.displayName** no organismo de pedido. Por exemplo, para criar um grupo de gestão com o **grupoId** de _Contoso_ e o nome de exibição do _Grupo Contoso,_ utilize o seguinte ponto final e peça ao organismo:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Corpo do Pedido

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

Nos exemplos anteriores, o novo grupo de gestão é criado no âmbito do grupo de gestão de raiz. Para especificar um grupo de gestão diferente como o progenitor, utilize a **propriedade properties.parent.id.**

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Corpo do Pedido

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o grupo de gestão acima criado, utilize os [Grupos de Gestão - Eliminar](/rest/api/managementgroups/managementgroups/delete) ponto final:

- URI da API REST

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Nenhum corpo de pedido

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um grupo de gestão para organizar a sua hierarquia de recursos. O grupo de gestão pode realizar subscrições ou outros grupos de gestão.

Para saber mais sobre grupos de gestão e como gerir a sua hierarquia de recursos, continue a:

> [!div class="nextstepaction"]
> [Gerir os seus recursos com grupos de gestão](./manage.md)
