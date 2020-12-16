---
title: 'Quickstart: Ligue-se à Base de Dados SQL do Azure com as ações do GitHub'
description: Use O SQL Azure de um fluxo de trabalho de ações GitHub
author: juliakm
services: sql-database
ms.service: sql-database
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 216658b5f5443409e7bd44cbd29bff40cd56c75f
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606985"
---
# <a name="use-github-actions-to-connect-to-azure-sql-database"></a>Utilize ações do GitHub para ligar à Base de Dados Azure SQL

Inicie com [as ações do GitHub](https://docs.github.com/en/free-pro-team@latest/actions) utilizando um fluxo de trabalho para implementar atualizações de bases de dados para [a Base de Dados Azure SQL](../azure-sql-iaas-vs-paas-what-is-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos

Vai precisar de: 
- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um repositório GitHub com um pacote dacpac `Database.dacpac` (). Se não tiver uma conta GitHub, [inscreva-se gratuitamente.](https://github.com/join)  
- Uma base de dados Azure SQL.
    - [Quickstart: Criar uma base de dados única Azure SQL Database](single-database-create-quickstart.md)
    - [Como criar um pacote dacpac a partir da sua base de dados de servidor SQL existente](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="workflow-file-overview"></a>Visão geral do ficheiro do fluxo de trabalho

Um fluxo de trabalho de GitHub Actions é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

O ficheiro tem duas secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço. <br /> 2. Criar um segredo do GitHub. |
|**Implementar** | 1. Implementar a base de dados. |

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

Pode criar um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md) com o comando [ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) no [Azure CLI](/cli/azure/). Executar este comando com [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Try it.**

Substitua os espaços reservados `server-name` pelo nome do seu servidor SQL alojado no Azure. Substitua o `subscription-id` iD e `resource-group` o grupo de recursos de subscrição ligados ao seu servidor SQL.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua base de dados semelhante a este exemplo. Copie o seu objeto JSON de saída para mais tarde.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> É sempre uma boa prática conceder o mínimo acesso. O âmbito no exemplo anterior está limitado ao servidor específico e não a todo o grupo de recursos.

## <a name="copy-the-sql-connection-string"></a>Copie a cadeia de ligação SQL 

No portal Azure, aceda à sua Base de **Dados** Azure SQL e abra as  >  **cordas de ligação** de definições . Copie a cadeia de ligação de **ADO.NET**. Substitua os valores de espaço reservado `your_database` para e `your_password` . A cadeia de ligação será semelhante a esta saída. 

```output
    Server=tcp:my-sql-server.database.windows.net,1433;Initial Catalog={your-database};Persist Security Info=False;User ID={admin-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Vais usar a cadeia de ligação como segredo do GitHub. 

## <a name="configure-the-github-secrets"></a>Configure os segredos do GitHub

1. No [GitHub,](https://github.com/)navegue no seu repositório.

1. Selecione **Definições > Segredos > Novo segredo**.

1. Cole toda a saída JSON do comando Azure CLI para o campo de valor do segredo. Dê o nome ao `AZURE_CREDENTIALS` segredo.

    Quando configurar o ficheiro de fluxo de trabalho mais tarde, utilize o segredo para a entrada `creds` da ação Azure Login. Por exemplo:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Selecione **novo segredo** novamente. 

1. Cole o valor da cadeia de ligação no campo de valor do segredo. Dê o nome ao `AZURE_SQL_CONNECTION_STRING` segredo.


## <a name="add-your-workflow"></a>Adicione o seu fluxo de trabalho

1. Vá às **ações** para o seu repositório GitHub. 

2. Selecione **Configurar o seu fluxo de trabalho por si mesmo**. 

2. Elimine tudo após a `on:` secção do seu ficheiro de fluxo de trabalho. Por exemplo, o seu fluxo de trabalho restante pode ser assim. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Mude o nome do seu fluxo de trabalho `SQL for GitHub Actions` e adicione as ações de check-out e login. Estas ações irão verificar o código do seu site e autenticar-se com o Azure usando o `AZURE_CREDENTIALS` segredo GitHub que criou anteriormente. 

    ```yaml
    name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Utilize a ação Azure SQL Implementar para ligar à sua instância SQL. `SQL_SERVER_NAME`Substitua-o pelo nome do seu servidor. Deve ter um pacote dacpac `Database.dacpac` ao nível da raiz do seu repositório. 

    ```yaml
    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'
    ``` 

1. Complete o seu fluxo de trabalho adicionando uma ação ao logout da Azure. Aqui está o fluxo de trabalho completo. O ficheiro aparecerá na `.github/workflows` pasta do seu repositório.

    ```yaml
   name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Reveja a sua implementação

1. Vá às **ações** para o seu repositório GitHub. 

1. Abra o primeiro resultado para ver os registos detalhados do funcionaamento do seu fluxo de trabalho. 
 
   :::image type="content" source="media/quickstart-sql-github-actions/github-actions-run-sql.png" alt-text="Log of GitHub actions run":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando a sua base de dados EST E o repositório Azure já não forem necessários, limpe os recursos que implementou eliminando o grupo de recursos e o seu repositório GitHub. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça a integração de Azure e GitHub](/azure/developer/github/)