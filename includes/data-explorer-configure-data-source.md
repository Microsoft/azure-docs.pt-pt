---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78305000"
---
## <a name="configure-the-data-source"></a>Configure a fonte de dados

Executa os seguintes passos para configurar o Azure Data Explorer como fonte de dados para a sua ferramenta de painel de instrumentos. Cobriremos estes passos mais detalhadamente nesta secção:

1. Crie um diretor de serviço azure Ative Directory (Azure AD). O diretor de serviço é utilizado pela sua ferramenta de painel de instrumentos para aceder ao serviço Azure Data Explorer.

1. Adicione o principal serviço azure AD ao papel dos *espectadores* na base de dados do Azure Data Explorer.

1. Especifique as propriedades de ligação da ferramenta do painel de instrumentos com base em informações do diretor de serviço da AD Azure e, em seguida, teste a ligação.

### <a name="create-a-service-principal"></a>Criar um principal de serviço

Pode criar o diretor de serviço no [portal Azure](#azure-portal) ou utilizar a experiência da linha de comando [Azure CLI.](#azure-cli) Independentemente do método que utilizar, após a criação obtém valores para quatro propriedades de ligação que utilizará em etapas posteriores.

#### <a name="azure-portal"></a>Portal do Azure

1. Para criar o diretor de serviço, siga as instruções na documentação do [portal Azure.](/azure/active-directory/develop/howto-create-service-principal-portal)

    1. Na [Atribuição da aplicação a uma](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) secção de funções, atribua um tipo de papel de **Leitor** ao seu cluster Azure Data Explorer.

    1. Nos [valores Get para a assinatura na](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) secção, copie os três valores de propriedade abrangidos pelas etapas: ID do **Diretório** (ID do inquilino), ID de **aplicação**e **Palavra-passe**.

1. No portal Azure, selecione **Subscrições** e, em seguida, copie o ID para a subscrição em que criou o diretor de serviço.

    ![ID de subscrição - portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>CLI do Azure

1. Crie um diretor de serviço. Definir um âmbito adequado e `reader`um tipo de função de .

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Para mais informações, consulte Criar um diretor de [serviço Azure com o Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. O comando devolve um resultado definido como o seguinte. Copie os três valores de propriedade: **appID,** **password,** e **inquilino.**


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Obtenha uma lista das suas assinaturas.

    ```azurecli
    az account list --output table
    ```

    Copie o ID de subscrição apropriado.

    ![ID de subscrição - CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Adicione o principal de serviço ao papel dos espectadores

Agora que tem um diretor de serviço, adicione-o ao papel dos *espectadores* na base de dados do Azure Data Explorer. Pode executar esta tarefa sob **Permissões** no portal Azure, ou sob **consulta** utilizando um comando de gestão.

#### <a name="azure-portal---permissions"></a>Portal Azure - Permissões

1. No portal Azure, vá ao seu cluster Azure Data Explorer.

1. Na secção **Visão Geral,** selecione a base de dados com os dados da amostra StormEvents.

    ![Selecionar base de dados](media/data-explorer-configure-data-source/select-database.png)

1. Selecione **Permissões** **e,** em seguida, adicionar .

    ![Permissões de base de dados](media/data-explorer-configure-data-source/database-permissions.png)

1. Em **adicionar permissões**de base de dados, selecione a função **'Visualizar'** e, em seguida, **selecione os principais**.

    ![Adicionar permissões de base de dados](media/data-explorer-configure-data-source/add-permission.png)

1. Procure o diretor de serviço que criou. Selecione o comitente e, em seguida, **selecione**.

    ![Gerir permissões no portal Azure](media/data-explorer-configure-data-source/new-principals.png)

1. Selecione **Guardar**.

    ![Gerir permissões no portal Azure](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Comando de gestão - Consulta

1. No portal Azure, vá ao seu cluster Azure Data Explorer e selecione **Query**.

    ![Consulta](media/data-explorer-configure-data-source/query.png)

1. Executar o seguinte comando na janela de consulta. Utilize o ID de inscrição e identificação do inquilino do portal Azure ou CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    O comando devolve um resultado definido como o seguinte. Neste exemplo, a primeira linha é para um utilizador existente na base de dados, e a segunda linha é para o diretor de serviço que acabou de ser adicionado.

    ![Conjunto de resultados](media/data-explorer-configure-data-source/result-set.png)
