---
title: Criar ou atualizar funções personalizadas Azure usando o portal Azure - Azure RBAC
description: Saiba como criar funções personalizadas Azure utilizando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC). Isto inclui como listar, criar, atualizar e eliminar funções personalizadas.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/11/2020
ms.author: rolyon
ms.openlocfilehash: ecda0edcd34999e8cbb6c7ab9039953d17c119e5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369231"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>Criar ou atualizar funções personalizadas do Azure com o portal do Azure

Se os [papéis incorporados do Azure](built-in-roles.md) não corresponderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados Azure. Tal como as funções incorporadas, pode atribuir funções personalizadas aos utilizadores, grupos e principais de serviços nos âmbitos de subscrição e grupo de recursos. As funções personalizadas são armazenadas num diretório Azure Ative (Azure AD) e podem ser partilhadas através de subscrições. Cada diretório pode ter até 5000 funções personalizadas. As funções personalizadas podem ser criadas utilizando o portal Azure PowerShell, Azure CLI ou a API REST. Este artigo descreve como criar funções personalizadas usando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para criar funções personalizadas, é necessário:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Passo 1: Determinar as permissões de que precisa

O Azure tem milhares de permissões que pode potencialmente incluir no seu papel personalizado. Aqui estão alguns métodos que podem ajudá-lo a determinar as permissões que pretende adicionar à sua função personalizada:

- Veja os [papéis incorporados existentes.](built-in-roles.md)
- Enuse os serviços Azure a que pretende conceder acesso.
- Determine os [fornecedores de recursos que mapeiam para os serviços Azure](../azure-resource-manager/management/azure-services-resource-providers.md). Um método de pesquisa é descrito mais tarde no [passo 4: Permissões](#step-4-permissions).
- Procure as [permissões disponíveis](resource-provider-operations.md) para encontrar permissões que pretende incluir. Um método de pesquisa é descrito mais tarde no [passo 4: Permissões](#step-4-permissions).

## <a name="step-2-choose-how-to-start"></a>Passo 2: Escolha como começar

Há três maneiras de começar a criar um papel personalizado. Pode clonar um papel existente, começar do zero ou começar com um ficheiro JSON. A maneira mais fácil é encontrar um papel existente que tenha a maioria das permissões que você precisa e, em seguida, cloná-lo e modificá-lo para o seu cenário. 

### <a name="clone-a-role"></a>Clonar um papel

Se um papel existente não tiver as permissões necessárias, pode cloná-lo e, em seguida, modificar as permissões. Siga estes passos para começar a clonar um papel.

1. No portal Azure, abra um grupo de subscrição ou de recursos onde pretende que a função personalizada seja atribuível e, em seguida, abra **o controlo de acesso (IAM)**.

    A imagem que se segue mostra a página de Controlo de Acesso (IAM) aberta para uma subscrição.

    ![Página de controlo de acesso (IAM) para uma subscrição](./media/custom-roles-portal/access-control-subscription.png)

1. Clique no **separador Funções** para ver uma lista de todas as funções incorporadas e personalizadas.

1. Procure um papel que pretenda clonar, como o papel de Billing Reader.

1. No final da linha, clique na elipse **(...)** e, em seguida, clique em **Clone**.

    ![Menu de contexto de clone](./media/custom-roles-portal/clone-menu.png)

    Isto abre o editor de papéis personalizados com o Clone uma opção **de papel** selecionada.

1. Dirijam-se ao [Passo 3: Básicos](#step-3-basics).

### <a name="start-from-scratch"></a>Começar do zero

Se preferir, pode seguir estes passos para iniciar um papel personalizado do zero.

1. No portal Azure, abra um grupo de subscrição ou de recursos onde pretende que a função personalizada seja atribuível e, em seguida, abra **o controlo de acesso (IAM)**.

1. Clique **em Adicionar** e, em seguida, clique em Adicionar **função personalizada.**

    ![Adicionar menu de função personalizado](./media/custom-roles-portal/add-custom-role-menu.png)

    Isto abre o editor de funções personalizado com a opção **Start from scratch** selecionada.

1. Dirijam-se ao [Passo 3: Básicos](#step-3-basics).

### <a name="start-from-json"></a>Comece pelo JSON

Se preferir, pode especificar a maioria dos seus valores de função personalizados num ficheiro JSON. Pode abrir o ficheiro no editor de funções personalizados, fazer alterações adicionais e, em seguida, criar o papel personalizado. Siga estes passos para começar com um ficheiro JSON.

1. Crie um ficheiro JSON que tenha o seguinte formato:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. No ficheiro JSON, especifique os valores para as várias propriedades. Aqui está um exemplo com alguns valores adicionados. Para obter informações sobre as diferentes propriedades, consulte [as definições de funções Understand Azure](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. No portal Azure, abra a página **de Controlo de Acesso (IAM).**

1. Clique **em Adicionar** e, em seguida, clique em Adicionar **função personalizada.**

    ![Adicionar menu de função personalizado](./media/custom-roles-portal/add-custom-role-menu.png)

    Isto abre o editor de papéis personalizados.

1. No separador Básicos, nas **permissões de Base,** selecione **Start a partir de JSON**.

1. Ao lado da caixa de ficheiros, clique no botão de pasta para abrir a caixa de diálogo Open.

1. Selecione o seu ficheiro JSON e, em seguida, clique em **Abrir**.

1. Dirijam-se ao [Passo 3: Básicos](#step-3-basics).

## <a name="step-3-basics"></a>Passo 3: Básico

No **separador Básicos,** especifique o nome, descrição e permissões de base para o seu papel personalizado.

1. Na caixa **de nomes de função personalizada,** especifique um nome para o papel personalizado. O nome deve ser único para o diretório AD Azure. O nome pode incluir letras, números, espaços e caracteres especiais.

1. Na caixa **Descrição,** especifique uma descrição opcional para a função personalizada. Esta será a ponta da ferramenta para o papel personalizado.

    A opção **de permissões de base** já deve ser definida com base no passo anterior, mas pode alterar.

    ![Separador básico com valores especificados](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Passo 4: Permissões

No separador **Permissões,** especifique as permissões para o seu papel personalizado. Dependendo se clonou um papel ou se começou com o JSON, o separador Permissões pode já enumerar algumas permissões.

![Permissões separador de criar função personalizada](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Adicionar ou remover permissões

Siga estes passos para adicionar ou remover permissões para o seu papel personalizado.

1. Para adicionar permissões, clique **em Adicionar permissões** para abrir o painel de permissões Adicionar.

    Este painel lista todas as permissões disponíveis agrupadas em diferentes categorias num formato de cartão. Cada categoria representa um *fornecedor de recursos,* que é um serviço que fornece recursos da Azure.

1. Na caixa de pesquisa de uma caixa **de permissão,** digite um fio para procurar permissões. Por exemplo, procure *fatura* para encontrar permissões relacionadas com a fatura.

    Será apresentada uma lista de cartões fornecedores de recursos com base na sua cadeia de pesquisa. Para obter uma lista de como os fornecedores de recursos mapeiam para os serviços da Azure, consulte [os fornecedores de recursos para os serviços Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

    ![Adicione o painel de permissões com o fornecedor de recursos](./media/custom-roles-portal/add-permissions-provider.png)

1. Clique num cartão de fornecedor de recursos que possa ter as permissões que pretende adicionar à sua função personalizada, como **o Microsoft Billing.**

    Uma lista das permissões de gestão desse fornecedor de recursos é apresentada com base na sua cadeia de pesquisa.

    ![Adicionar lista de permissões](./media/custom-roles-portal/add-permissions-list.png)

1. Se estiver à procura de permissões que se apliquem ao plano de dados, clique em **Data Actions**. Caso contrário, deixe as ações definidas para **as Ações** para listar permissões que se aplicam ao plano de gestão. Para mais informações, sobre as diferenças entre o plano de gestão e o plano de dados, consulte [Gestão e operações de dados.](role-definitions.md#management-and-data-operations)

1. Se necessário, atualize o string de pesquisa para aperfeiçoar ainda mais a sua procura.

1. Assim que encontrar uma ou mais permissões que pretende adicionar à sua função personalizada, adicione uma marca de verificação ao lado das permissões. Por exemplo, adicione uma marca de verificação ao lado **de Outras : Baixar fatura** para adicionar a permissão para descarregar faturas.

1. Clique **em Adicionar** para adicionar a permissão à sua lista de permissões.

    A permissão é adicionada como um `Actions` ou um `DataActions` .

    ![Permissão adicionada](./media/custom-roles-portal/permissions-list-add.png)

1. Para remover permissões, clique no ícone de exclusão no final da linha. Neste exemplo, uma vez que um utilizador não precisará da capacidade de criar bilhetes de suporte, a `Microsoft.Support/*` permissão pode ser eliminada.

### <a name="add-wildcard-permissions"></a>Adicionar permissões wildcard

Dependendo da forma como escolheu começar, poderá ter permissões com wildcards `*` na sua lista de permissões. Um wildcard `*` () estende uma permissão a tudo o que corresponde à cadeia de ação que fornece. Por exemplo, o seguinte fio wildcard adiciona todas as permissões relacionadas com a Azure Cost Management e exportações. Isto incluiria igualmente eventuais futuras autorizações de exportação que possam ser adicionadas.

```
Microsoft.CostManagement/exports/*
```

Se quiser adicionar uma nova permissão wildcard, não pode adicioná-la usando o painel **de permissões Add.** Para adicionar uma permissão wildcard, tem de adicioná-la manualmente utilizando o separador **JSON.** Para mais informações, consulte [o passo 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Excluir permissões

Se a sua função tiver uma permissão wildcard `*` () e pretender excluir ou subtrair permissões específicas dessa permissão wildcard, pode excluí-las. Por exemplo, digamos que tem a seguinte permissão wildcard:

```
Microsoft.CostManagement/exports/*
```

Se não quiser permitir a eliminação de uma exportação, poderá excluir a seguinte permissão de eliminação:

```
Microsoft.CostManagement/exports/delete
```

Quando se exclui uma permissão, é adicionada como `NotActions` um ou `NotDataActions` . As permissões de gestão eficazes são calculadas adicionando todas as `Actions` e, em seguida, subtraindo todos os `NotActions` . As permissões de dados eficazes são calculadas adicionando todas as `DataActions` e, em seguida, subtraindo todos os `NotDataActions` .

> [!NOTE]
> Excluir uma permissão não é o mesmo que uma negação. Excluir permissões é simplesmente uma maneira conveniente de subtrair permissões de uma permissão wildcard.

1. Para excluir ou subtrair uma permissão de um wildcard permitido, clique em **Excluir permissões** para abrir o painel de permissões Excluir.

    Neste painel, especifique as permissões de gestão ou dados que são excluídas ou subtraídas.

1. Assim que encontrar uma ou mais permissões que pretende excluir, adicione uma marca de verificação ao lado das permissões e, em seguida, clique no botão **Adicionar.**

    ![Excluir o painel de permissões - permissão selecionada](./media/custom-roles-portal/exclude-permissions-select.png)

    A permissão é adicionada como um `NotActions` ou `NotDataActions` .

    ![Autorização excluída](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Passo 5: Âmbitos atribuíveis

No **separador âmbitos atribuíveis,** especifica onde a sua função personalizada está disponível para atribuição, como subscrição ou grupo de recursos. Dependendo da forma como escolheu iniciar, este separador pode listar o âmbito onde abriu a página de Controlo de Acesso (IAM). A definição do âmbito de aplicação do âmbito raiz ("/") não é suportada. Atualmente, não é possível adicionar um grupo de gestão como âmbito atribuível.

1. Clique **em Adicionar âmbitos atribuíveis** para abrir o painel de âmbitos atribuíveis Add.

    ![Separador de âmbitos atribuíveis](./media/custom-roles-portal/assignable-scopes.png)

1. Clique num ou mais âmbitos que pretende utilizar, normalmente a sua subscrição.

    ![Adicionar âmbitos atribuíveis](./media/custom-roles-portal/add-assignable-scopes.png)

1. Clique no botão **Adicionar** para adicionar o seu âmbito atribuível.

## <a name="step-6-json"></a>Passo 6: JSON

No separador **JSON,** vê o seu papel personalizado formatado no JSON. Se quiser, pode editar diretamente o JSON. Se quiser adicionar uma `*` permissão wildcard () deve utilizar este separador.

1. Para editar o JSON, clique em **Editar**.

    ![Separador JSON mostrando papel personalizado](./media/custom-roles-portal/json.png)

1. Faça alterações no JSON.

    Se o JSON não estiver formatado corretamente, verá uma linha irregular vermelha e um indicador na calha vertical.

1. Quando terminar a edição, clique em **Guardar**.

## <a name="step-7-review--create"></a>Passo 7: Revisão + criar

No **separador 'Rever +' criar,** pode rever as definições de funções personalizadas.

1. Reveja as definições de funções personalizadas.

    ![Rever + criar separador](./media/custom-roles-portal/review-create.png)

1. Clique em **Criar** para criar o seu papel personalizado.

    Após alguns momentos, aparece uma caixa de mensagens indicando que o seu papel personalizado foi criado com sucesso.

    ![Criar mensagem de papel personalizada](./media/custom-roles-portal/custom-role-success.png)

    Se forem detetados erros, será exibida uma mensagem.

    ![Rever + criar erro](./media/custom-roles-portal/review-create-error.png)

1. Veja o seu novo papel personalizado na lista **Roles.** Se não vir o seu papel personalizado, clique em **Refresh**.

     Pode levar alguns minutos para que o seu papel personalizado apareça em todos os lugares.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Siga estes passos para ver os seus papéis personalizados.

1. Abra um grupo de subscrição ou de recursos e, em seguida, abra **o controlo de acesso (IAM)**.

1. Clique no **separador Funções** para ver uma lista de todas as funções incorporadas e personalizadas.

1. Na lista **'Tipo',** selecione **CustomRole** para apenas ver as suas funções personalizadas.

    Se acabou de criar o seu papel personalizado e não o vê na lista, clique em **Refresh**.

    ![Lista de funções personalizadas](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

1. Como descrito anteriormente neste artigo, abra a sua lista de papéis personalizados.

1. Clique na elipse **(...)** para obter o papel personalizado que pretende atualizar e, em seguida, clique em **Editar.** Note que não pode atualizar funções incorporadas.

    O papel personalizado é aberto no editor.

    ![Menu de função personalizado](./media/custom-roles-portal/edit-menu.png)

1. Utilize os diferentes separadores para atualizar a função personalizada.

1. Assim que terminar as alterações, clique no **separador 'Rever +'** para rever as alterações.

1. Clique no botão **'Actualizar'** para atualizar a sua função personalizada.

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

1. Como descrito anteriormente neste artigo, abra a sua lista de papéis personalizados.

1. Remova quaisquer atribuições de funções que utilizem o papel personalizado.

1. Clique na elipse **(...**) para obter o papel personalizado que pretende eliminar e, em seguida, clique em **Eliminar**.

    ![Screenshot que mostra uma lista de funções personalizadas que podem ser selecionadas para eliminação.](./media/custom-roles-portal/delete-menu.png)

    Pode levar alguns minutos para que o seu papel personalizado seja completamente apagado.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar um papel personalizado Azure usando Azure PowerShell](tutorial-custom-role-powershell.md)
- [Funções personalizadas do Azure](custom-roles.md)
- [Operações de fornecedor de recursos Azure](resource-provider-operations.md)
