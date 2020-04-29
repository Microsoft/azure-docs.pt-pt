---
title: Criar ou atualizar funções personalizadas do Azure utilizando o portal Azure (Pré-visualização) - Azure RBAC
description: Aprenda a criar funções personalizadas azure para o controlo de acesso baseado em funções Azure (Azure RBAC) utilizando o portal Azure. Isto inclui como listar, criar, atualizar e eliminar funções personalizadas.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77674873"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Criar ou atualizar funções personalizadas do Azure utilizando o portal Azure (Pré-visualização)

> [!IMPORTANT]
> As funções personalizadas azure utilizando o portal Azure estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Se os [papéis integrados](built-in-roles.md) do Azure não atenderem às necessidades específicas da sua organização, pode criar os seus próprios papéis personalizados Azure. Tal como as funções incorporadas, pode atribuir funções personalizadas a utilizadores, grupos e diretores de serviços nos âmbitos de grupode subscrição e recursos. As funções personalizadas são armazenadas num diretório Azure Ative Directory (Azure AD) e podem ser partilhadas através de subscrições. Cada diretório pode ter até 5000 papéis personalizados. As funções personalizadas podem ser criadas através do portal Azure, Azure PowerShell, Azure CLI ou da REST API. Este artigo descreve como criar papéis personalizados usando o portal Azure (atualmente em pré-visualização).

## <a name="prerequisites"></a>Pré-requisitos

Para criar papéis personalizados, precisa de:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Passo 1: Determine as permissões necessárias

O Azure tem milhares de permissões que pode incluir potencialmente no seu papel personalizado. Aqui estão quatro maneiras que você pode determinar as permissões que você vai querer adicionar ao seu papel personalizado:

| Método | Descrição |
| --- | --- |
| Veja os papéis existentes | Pode ver os papéis existentes para ver que permissões estão a ser usadas. Para mais informações, consulte [as funções de Azure incorporadas.](built-in-roles.md) |
| Procurar permissões por palavra-chave | Quando cria uma função personalizada utilizando o portal Azure, pode procurar permissões por palavra-chave. Por exemplo, pode pesquisar por *máquinavirtual* ou permissões *de faturação.* Esta funcionalidade de pesquisa é descrita mais tarde no [Passo 4: Permissões](#step-4-permissions). |
| Descarregue todas as permissões | Quando criar uma função personalizada utilizando o portal Azure, pode descarregar todas as permissões como ficheiro CSV e, em seguida, pesquisar este ficheiro. No painel **de permissões Adicionar,** clique no botão **Descarregue todas as permissões** para descarregar todas as permissões. Para mais informações sobre o painel de permissões Adicionar, consulte [passo 4: Permissões](#step-4-permissions). |
| Ver as permissões nos doutoros | Pode visualizar as permissões disponíveis nas operações de fornecedor de recursos do Gestor de [Recursos do Azure.](resource-provider-operations.md) |

## <a name="step-2-choose-how-to-start"></a>Passo 2: Escolha como começar

Há três maneiras de começar a criar um papel personalizado. Pode clonar um papel existente, começar do zero ou começar com um ficheiro JSON. A maneira mais fácil é encontrar um papel existente que tenha a maioria das permissões que precisa e depois cloná-lo e modificá-lo para o seu cenário. 

### <a name="clone-a-role"></a>Clonar um papel

Se uma função existente não tiver as permissões de que necessita, pode cloná-la e depois modificar as permissões. Siga estes passos para começar a clonar um papel.

1. No portal Azure, abra uma subscrição ou grupo de recursos onde pretende que o papel personalizado seja atribuído e, em seguida, abra o controlo de **acesso (IAM)**.

    A imagem seguinte mostra a página de controlo de acesso (IAM) aberta para uma subscrição.

    ![Página de controlo de acesso (IAM) para uma subscrição](./media/custom-roles-portal/access-control-subscription.png)

1. Clique no separador **Roles** para ver uma lista de todas as funções incorporadas e personalizadas.

1. Procure um papel que queira clonar, como o papel do Billing Reader.

1. No final da linha, clique na elipse**e,** em seguida, clique em **Clone**.

    ![Menu de contexto clone](./media/custom-roles-portal/clone-menu.png)

    Isto abre o editor de papéis personalizados com o **Clone uma** opção de função selecionada.

1. Siga para o [passo 3: Básicos](#step-3-basics).

### <a name="start-from-scratch"></a>Começar do zero

Se preferir, pode seguir estes passos para iniciar um papel personalizado do zero.

1. No portal Azure, abra uma subscrição ou grupo de recursos onde pretende que o papel personalizado seja atribuído e, em seguida, abra o controlo de **acesso (IAM)**.

1. Clique em **Adicionar** e, em seguida, clique em **Adicionar a função personalizada (pré-visualização)**.

    ![Adicionar menu de papel personalizado](./media/custom-roles-portal/add-custom-role-menu.png)

    Isto abre o editor de papéis personalizados com a opção **Iniciar a partir do scratch** selecionada.

1. Siga para o [passo 3: Básicos](#step-3-basics).

### <a name="start-from-json"></a>Comece a partir da JSON

Se preferir, pode especificar a maioria dos seus valores de papel personalizados num ficheiro JSON. Pode abrir o ficheiro no editor de papéis personalizados, fazer alterações adicionais e, em seguida, criar a função personalizada. Siga estes passos para começar com um ficheiro JSON.

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

1. No ficheiro JSON, especifique valores para as várias propriedades. Aqui está um exemplo com alguns valores adicionados. Para obter informações sobre as diferentes propriedades, consulte Compreender definições de [papéis.](role-definitions.md)

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
    
1. No portal Azure, abra a página de Controlo de **Acesso (IAM).**

1. Clique em **Adicionar** e, em seguida, clique em **Adicionar a função personalizada (pré-visualização)**.

    ![Adicionar menu de papel personalizado](./media/custom-roles-portal/add-custom-role-menu.png)

    Isto abre o editor de papéis personalizados.

1. No separador Basics, em **permissões de Base,** selecione **Iniciar a partir de JSON**.

1. Ao lado da caixa de ficheiros Selecione, clique no botão da pasta para abrir a caixa de diálogo Open.

1. Selecione o seu ficheiro JSON e, em seguida, clique em **Abrir**.

1. Siga para o [passo 3: Básicos](#step-3-basics).

## <a name="step-3-basics"></a>Passo 3: Básicos

No separador **Basics,** especifica o nome, descrição e permissões de base para o seu papel personalizado.

1. Na caixa de **nomes Personalizado,** especifique um nome para o papel personalizado. O nome deve ser único para o diretório Azure AD. O nome pode incluir letras, números, espaços e caracteres especiais.

1. Na caixa **Descrição,** especifique uma descrição opcional para o papel personalizado. Esta será a dica de ferramentapara o papel personalizado.

    A opção de **permissões de Base** já deve ser definida com base no passo anterior, mas pode alterar.

    ![Separador básico com valores especificados](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Passo 4: Permissões

No separador **Permissões,** especifice as permissões para o seu papel personalizado. Dependendo se clonou um papel ou se começou com a JSON, o separador Permissões pode já listar algumas permissões.

![Separador de permissões de criar papel personalizado](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Adicionar ou remover permissões

Siga estes passos para adicionar ou remover permissões para o seu papel personalizado.

1. Para adicionar permissões, clique **em Adicionar permissões** para abrir o painel de permissões Adicionar.

    Este painel lista todas as permissões disponíveis agruparadas em diferentes categorias num formato de cartão. Cada categoria representa um fornecedor de *recursos,* que é um serviço que fornece recursos Azure.

1. Na Procura de uma caixa de **permissão,** digite uma corda para procurar permissões. Por exemplo, *procure* fatura para encontrar permissões relacionadas com a fatura.

    Uma lista de cartões de fornecedor de recursos será exibida com base na sua cadeia de pesquisa. Para obter uma lista de como os fornecedores de recursos mapeiam os serviços do Azure, consulte os fornecedores de [recursos para os serviços Azure.](../azure-resource-manager/management/azure-services-resource-providers.md)

    ![Adicione painel de permissões com fornecedor de recursos](./media/custom-roles-portal/add-permissions-provider.png)

1. Clique num cartão de fornecedor de recursos que possa ter as permissões que pretende adicionar à sua função personalizada, como **o Microsoft Billing**.

    Uma lista das permissões de gestão desse fornecedor de recursos é apresentada com base na sua cadeia de pesquisa.

    ![Adicionar lista de permissões](./media/custom-roles-portal/add-permissions-list.png)

1. Se estiver à procura de permissões que se apliquem ao plano de dados, clique em **Ações de Dados**. Caso contrário, deixe as ações de alternância definidas em **Ações** para listar permissões que se aplicam ao plano de gestão. Para mais informações, sobre as diferenças entre o plano de gestão e o plano de dados, consulte [gestão e operações](role-definitions.md#management-and-data-operations)de dados.

1. Se necessário, atualize a cadeia de pesquisa para aperfeiçoar ainda mais a sua procura.

1. Assim que encontrar uma ou mais permissões, pretende adicionar ao seu papel personalizado, adicione uma marca de verificação ao lado das permissões. Por exemplo, adicione uma marca de verificação ao lado de **Outra : Descarregue** a Fatura para adicionar a permissão para descarregar faturas.

1. Clique em **Adicionar** a permissão à sua lista de permissão.

    A permissão é `Actions` adicionada `DataActions`como um ou um .

    ![Permissão adicionada](./media/custom-roles-portal/permissions-list-add.png)

1. Para remover permissões, clique no ícone de exclusão no final da linha. Neste exemplo, uma vez que um utilizador não necessitará da capacidade de criar bilhetes de apoio, a `Microsoft.Support/*` permissão pode ser eliminada.

### <a name="add-wildcard-permissions"></a>Adicione permissões wildcard

Dependendo da forma como escolheu começar, poderá ter\*permissões com wildcards na sua lista de permissões. Um wildcard\*() estende uma permissão a tudo o que corresponde à corda que fornece. Por exemplo, suponha que queria adicionar todas as permissões relacionadas com a Azure Cost Management e exportações. Pode adicionar todas estas permissões:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Em vez de adicionar todas estas permissões, pode adicionar uma permissão de wildcard. Por exemplo, a seguinte permissão de wildcard é equivalente às cinco permissões anteriores. Isto incluiria igualmente quaisquer futuras autorizações de exportação que pudessem ser adicionadas.

```
Microsoft.CostManagement/exports/*
```

Se quiser adicionar uma nova permissão wildcard, não pode adicioná-la usando o painel de **permissões Add.** Para adicionar uma permissão wildcard, tem de adicioná-la manualmente utilizando o separador **JSON.** Para mais informações, consulte [passo 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Excluir permissões

Se o seu papel\*tiver uma permissão wildcard () e quiser excluir ou subtrair permissões específicas dessa permissão wildcard, pode excluí-las. Por exemplo, digamos que tem a seguinte permissão de wildcard:

```
Microsoft.CostManagement/exports/*
```

Se não quiser permitir que uma exportação seja eliminada, pode excluir a seguinte autorização de eliminação:

```
Microsoft.CostManagement/exports/delete
```

Quando se exclui uma permissão, `NotActions` `NotDataActions`é adicionada como a ou . As permissões de gestão eficazes `Actions` são calculadas adicionando `NotActions`todas as e, em seguida, subtraindo toda a . As permissões de dados eficazes `DataActions` são calculadas adicionando `NotDataActions`todas as e, em seguida, subtraindo toda a .

> [!NOTE]
> Excluir uma permissão não é o mesmo que um negação. Excluir permissões é simplesmente uma forma conveniente de subtrair permissões de uma permissão wildcard.

1. Para excluir ou subtrair uma permissão de uma permissão de wildcard permitida, clique em **Excluir permissões** para abrir o painel de permissões Excluir.

    Neste painel, especifica as permissões de gestão ou dados que são excluídas ou subtraídas.

1. Assim que encontrar uma ou mais permissões que pretende excluir, adicione uma marca de verificação ao lado das permissões e, em seguida, clique no botão **Adicionar.**

    ![Excluir painel de permissões - permissão selecionada](./media/custom-roles-portal/exclude-permissions-select.png)

    A permissão é `NotActions` `NotDataActions`adicionada como a ou .

    ![Autorização excluída](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Passo 5: Âmbitos atribuíveis

No separador de **âmbitos designados,** especifica onde está disponível a sua função personalizada para atribuição, como por exemplo, subscrição ou grupo de recursos. Dependendo da forma como escolheu começar, este separador pode listar o âmbito onde abriu a página de controlo de acesso (IAM). A definição do âmbito atribuível ao âmbito raiz ("/") não é suportada. Para esta pré-visualização, não é possível adicionar um grupo de gestão como um âmbito atribuível.

1. Clique **em Adicionar âmbitos atribuíveis** para abrir o painel de âmbitos atribuíveis Adicionar.

    ![Separador de âmbitos atribuíveis](./media/custom-roles-portal/assignable-scopes.png)

1. Clique num ou mais âmbitos que pretende utilizar, tipicamente a sua subscrição.

    ![Adicionar âmbitos atribuíveis](./media/custom-roles-portal/add-assignable-scopes.png)

1. Clique no botão **Adicionar** para adicionar o seu âmbito atribuível.

## <a name="step-6-json"></a>Passo 6: JSON

No separador **JSON,** vê o seu papel personalizado formatado em JSON. Se quiser, pode editar diretamente o JSON. Se quiser adicionar uma\*permissão de wildcard, deve utilizar este separador.

1. Para editar o JSON, clique em **Editar**.

    ![Separador JSON mostrando papel personalizado](./media/custom-roles-portal/json.png)

1. Faça alterações no JSON.

    Se o JSON não for formatado corretamente, verá uma linha vermelha irregular e um indicador na calha vertical.

1. Quando terminar a edição, clique em **Guardar**.

## <a name="step-7-review--create"></a>Passo 7: Rever + criar

No **separador Review + criar,** pode rever as definições de funções personalizadas.

1. Reveja as definições de funções personalizadas.

    ![Rever + criar separador](./media/custom-roles-portal/review-create.png)

1. Clique em **Criar** para criar o seu papel personalizado.

    Após alguns momentos, uma caixa de mensagens aparece indicando que o seu papel personalizado foi criado com sucesso.

    ![Criar mensagem de papel personalizada](./media/custom-roles-portal/custom-role-success.png)

    Se forem detetados erros, será apresentada uma mensagem.

    ![Rever + criar erro](./media/custom-roles-portal/review-create-error.png)

1. Veja o seu novo papel personalizado na lista **de Papéis.** Se não vir o seu papel personalizado, clique em **Refresh**.

     Pode levar alguns minutos para o seu papel personalizado aparecer em todos os lugares.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Siga estes passos para ver os seus papéis personalizados.

1. Abra uma subscrição ou grupo de recursos e, em seguida, abra **o controlo de acesso (IAM)**.

1. Clique no separador **Roles** para ver uma lista de todas as funções incorporadas e personalizadas.

1. Na lista **de Tipo,** selecione **CustomRole** para ver apenas as suas funções personalizadas.

    Se acabou de criar o seu papel personalizado e não o vê na lista, clique em **Refresh**.

    ![Lista de papéis personalizado](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

1. Como descrito anteriormente neste artigo, abra a sua lista de papéis personalizados.

1. Clique na elipse **(...)** para obter o papel personalizado que pretende atualizar e, em seguida, clique em **Editar**. Note que não pode atualizar funções incorporadas.

    O papel personalizado é aberto no editor.

    ![Menu de papéis personalizado](./media/custom-roles-portal/edit-menu.png)

1. Utilize os diferentes separadores para atualizar a função personalizada.

1. Assim que terminar as alterações, clique no separador **Rever + para** rever as suas alterações.

1. Clique no botão **'Actualizar'** para atualizar a sua função personalizada.

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

1. Como descrito anteriormente neste artigo, abra a sua lista de papéis personalizados.

1. Remova quaisquer atribuições de funções que utilizem a função personalizada.

1. Clique na elipse **(...)** para obter a função personalizada que pretende eliminar e, em seguida, clique em **Apagar**.

    ![Menu de papéis personalizado](./media/custom-roles-portal/delete-menu.png)

    Pode levar alguns minutos para que o seu papel personalizado seja completamente eliminado.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar uma função personalizada com o Azure PowerShell](tutorial-custom-role-powershell.md)
- [Custom roles in Azure](custom-roles.md) (Funções personalizadas no Azure)
- [Operações de fornecedor de recursos do Gestor de Recursos Azure](resource-provider-operations.md)
