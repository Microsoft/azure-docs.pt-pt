---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: f8b3e541dfd55bbd613af2791329a08402cf8670
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581996"
---
## <a name="configure-the-data-source"></a>Configurar a fonte de dados

Execute as etapas a seguir para configurar o Azure Data Explorer como uma fonte de dados para sua ferramenta de painel. Abordaremos essas etapas mais detalhadamente nesta seção:

1. Crie uma entidade de serviço do Azure Active Directory (AD do Azure). A entidade de serviço é usada pela ferramenta de painel para acessar o serviço de Data Explorer do Azure.

1. Adicione a entidade de serviço do Azure AD à função *visualizadores* no banco de dados data Explorer do Azure.

1. Especifique suas propriedades de conexão da ferramenta de painel com base nas informações da entidade de serviço do Azure AD e teste a conexão.

### <a name="create-a-service-principal"></a>Criar um principal de serviço

Você pode criar a entidade de serviço no [portal do Azure](#azure-portal) ou usando a experiência de linha de comando [CLI do Azure](#azure-cli) . Independentemente do método usado, após a criação, você obtém valores para quatro propriedades de conexão que serão usadas em etapas posteriores.

#### <a name="azure-portal"></a>Portal do Azure

1. Para criar a entidade de serviço, siga as instruções na [documentação do portal do Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Na seção [atribuir o aplicativo a uma função](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) , atribua um tipo de função de **leitor** ao cluster de data Explorer do Azure.

    1. Na seção [obter valores para entrar](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) , copie os três valores de propriedade abordados nas etapas: **ID do diretório** (ID do locatário), **ID do aplicativo**e **senha**.

1. No portal do Azure, selecione **assinaturas** e, em seguida, copie a ID da assinatura na qual você criou a entidade de serviço.

    ![ID da assinatura-Portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>CLI do Azure

1. Crie uma entidade de serviço. Defina um escopo apropriado e um tipo de função de `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Para obter mais informações, consulte [criar uma entidade de serviço do Azure com CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. O comando retorna um conjunto de resultados como o seguinte. Copie os três valores de propriedade: **AppID**, **senha**e **locatário**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Obtenha uma lista de suas assinaturas.

    ```azurecli
    az account list --output table
    ```

    Copie a ID da assinatura apropriada.

    ![ID da assinatura-CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Adicionar a entidade de serviço à função de visualizadores

Agora que você tem uma entidade de serviço, adicione-a à função *visualizadores* no banco de dados data Explorer do Azure. Você pode executar essa tarefa em **permissões** no portal do Azure ou em **consulta** usando um comando de gerenciamento.

#### <a name="azure-portal---permissions"></a>Portal do Azure-permissões

1. Na portal do Azure, vá para o cluster de Data Explorer do Azure.

1. Na seção **visão geral** , selecione o banco de dados com os StormEvents de exemplo.

    ![Selecionar Banco de dados](media/data-explorer-configure-data-source/select-database.png)

1. Selecione **permissões** e **Adicionar**.

    ![Permissões de banco de dados](media/data-explorer-configure-data-source/database-permissions.png)

1. Em **adicionar permissões de banco de dados**, selecione a função de **Visualizador** e, em seguida, **selecione entidades de segurança**.

    ![Adicionar permissões de banco de dados](media/data-explorer-configure-data-source/add-permission.png)

1. Pesquise a entidade de serviço que você criou. Selecione o principal e, em seguida, **selecione**.

    ![Gerenciar permissões no portal do Azure](media/data-explorer-configure-data-source/new-principals.png)

1. Selecione **Guardar**.

    ![Gerenciar permissões no portal do Azure](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Comando de gerenciamento – consulta

1. Na portal do Azure, vá para o cluster de Data Explorer do Azure e selecione **consulta**.

    ![Consulta](media/data-explorer-configure-data-source/query.png)

1. Execute o comando a seguir na janela de consulta. Use a ID do aplicativo e a ID do locatário do portal do Azure ou da CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    O comando retorna um conjunto de resultados como o seguinte. Neste exemplo, a primeira linha é para um usuário existente no banco de dados e a segunda linha é para a entidade de serviço que acabou de ser adicionada.

    ![Conjunto de resultados](media/data-explorer-configure-data-source/result-set.png)
