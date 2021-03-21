---
title: 'Quickstart: Criar um grupo de gestão com Python'
description: Neste quickstart, você usa Python para criar um grupo de gestão para organizar os seus recursos em uma hierarquia de recursos.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e3c55cc14a8ac980318fd0de9485a3e0ca31b582
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101767"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Quickstart: Criar um grupo de gestão com Python

Os grupos de gestão são contentores que o ajudam a gerir o acesso, a política e o cumprimento em várias subscrições. Crie estes recipientes para construir uma hierarquia eficaz e eficiente que possa ser usada com [a Azure Policy](../policy/overview.md) e [os Controlos de Acesso Baseados em Funções Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](overview.md)

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos para ser concluído. Há processos que correm pela primeira vez para configurar o serviço de grupos de gestão dentro do Azure para o seu diretório. Recebe uma notificação quando o processo estiver concluído. Para mais informações, consulte [a configuração inicial dos grupos de gestão.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Qualquer utilizador Azure AD no arrendatário pode criar um grupo de gestão sem que o grupo de gestão escreva a permissão atribuída a esse utilizador se a [proteção da hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver ativada. Este novo grupo de gestão torna-se filho do Grupo de Gestão de Raízes ou do [grupo de gestão padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e o criador recebe uma atribuição de função de "Proprietário". O serviço de grupo de gestão permite esta capacidade para que as atribuições de funções não sejam necessárias ao nível da raiz. Nenhum utilizadores tem acesso ao Grupo de Gestão de Raízes quando este é criado. Para evitar o obstáculo de encontrar os Azure AD Global Admins para começar a usar grupos de gestão, permitimos a criação dos grupos de gestão iniciais ao nível da raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Adicione a biblioteca de Gráficos de Recursos

Para permitir que a Python gere grupos de gestão, a biblioteca deve ser adicionada. Esta biblioteca funciona onde quer que python possa ser usado, incluindo [bash no Windows 10](/windows/wsl/install-win10) ou instalado localmente.

1. Verifique se o último Python está instalado (pelo menos **3.8**). Se ainda não estiver instalado, descarregue-o em [Python.org](https://www.python.org/downloads/).

1. Verifique se o último CLI do Azure está instalado (pelo menos **2.5.1**). Se ainda não estiver instalado, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > O Azure CLI é necessário para permitir que a Python utilize a **autenticação baseada** em CLI nos seguintes exemplos. Para obter informações sobre outras opções, consulte [Authenticate utilizando as bibliotecas de gestão Azure para Python.](/azure/developer/python/azure-sdk-authenticate)

1. Autenticar através do Azure CLI.

   ```azurecli
   az login
   ```

1. No seu ambiente python de eleição, instale as bibliotecas necessárias para grupos de gestão:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Se o Python estiver instalado para todos os utilizadores, estes comandos devem ser executados a partir de uma consola elevada.

1. Valide que as bibliotecas foram instaladas. `azure-mgmt-managementgroups` deve ser **0.2.0** ou superior, `azure-mgmt-resource` deve ser **9.0.0** ou superior, e `azure-cli-core` deve ser **2.5.0** ou superior.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>Criar o grupo de gestão

1. Crie o script Python e guarde a seguinte fonte `mgCreate.py` como:

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. Autenticar com Azure CLI com `az login` .

1. Introduza o seguinte comando no terminal:

   ```bash
   py mgCreate.py
   ```

O resultado da criação do grupo de gestão é a saída para a consola como `LROPoller` um objeto.

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover as bibliotecas instaladas do seu ambiente Python, pode fazê-lo utilizando o seguinte comando:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um grupo de gestão para organizar a sua hierarquia de recursos. O grupo de gestão pode realizar subscrições ou outros grupos de gestão.

Para saber mais sobre grupos de gestão e como gerir a sua hierarquia de recursos, continue a:

> [!div class="nextstepaction"]
> [Gerir os seus recursos com grupos de gestão](./manage.md)