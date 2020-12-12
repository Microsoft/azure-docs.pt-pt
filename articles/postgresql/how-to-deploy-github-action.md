---
title: 'Quickstart: Ligue-se ao Azure PostgreSQL com as ações do GitHub'
description: Use Azure PostgreSQL a partir de um fluxo de trabalho de ações GitHub
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 2e546801f95d9d884bdfb3f09a18b3fa6e2d78a1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97365120"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>Quickstart: Use ações do GitHub para ligar ao Azure PostgreSQL

<Token>**APLICA-SE A:** :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Base de Dados Azure para PostgreSQL - Base de Dados Azure de servidor único :::image type="icon" source="./media/applies-to/yes.png" border="false"::: para PostgreSQL - Servidor Flexível</Token>

Inicie com [as ações do GitHub](https://docs.github.com/en/actions) utilizando um fluxo de trabalho para implementar atualizações de base de dados [para a Base de Dados Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql/).

## <a name="prerequisites"></a>Pré-requisitos

Vai precisar de:
- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um repositório GitHub com dados de amostra `data.sql` (). Se não tiver uma conta GitHub, [inscreva-se gratuitamente.](https://github.com/join)
- Uma base de dados Azure para o servidor PostgreSQL.
    - [Início Rápido: Criar um servidor da Base de Dados do Azure para PostgreSQL no portal do Azure](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>Visão geral do ficheiro do fluxo de trabalho

Um fluxo de trabalho de GitHub Actions é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

O ficheiro tem duas secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço. <br /> 2. Criar um segredo do GitHub. |
|**Implementar** | 1. Implementar a base de dados. |

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

Pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md) com o comando [ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) no [Azure CLI](/cli/azure/). Executar este comando com [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Try it.**

Substitua os espaços reservados `server-name` pelo nome do seu servidor PostgreSQL alojado no Azure. Substitua o iD e pelo grupo de recursos de `subscription-id` `resource-group` subscrição ligado ao seu servidor PostgreSQL.

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua base de dados semelhante abaixo. Copie este objeto JSON de saída para mais tarde.

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

## <a name="copy-the-postgresql-connection-string"></a>Copie a cadeia de ligação PostgreSQL

No portal Azure, aceda à sua Base de Dados Azure para servidor PostgreSQL e abra **as**  >  **cadeias de ligação** de definições . Copie a cadeia de ligação de **ADO.NET**. Substitua os valores de espaço reservado `your_database` para e `your_password` . A cadeia de ligação será semelhante a esta.

> [!IMPORTANT]
> - Para a utilização de um único servidor ```user=adminusername@servername```  . Note o ```@servername``` que é necessário.
> - Para o servidor flexível, utilize ```user= adminusername``` sem o  ```@servername``` .

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
```

Usará a cadeia de ligação como segredo do GitHub.

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

1. Cole o valor da cadeia de ligação no campo de valor do segredo. Dê o nome ao `AZURE_POSTGRESQL_CONNECTION_STRING` segredo.


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

1. Mude o nome do seu fluxo de trabalho `PostgreSQL for GitHub Actions` e adicione as ações de check-out e login. Estas ações irão verificar o código do seu site e autenticar-se com o Azure usando o `AZURE_CREDENTIALS` segredo GitHub que criou anteriormente.

    ```yaml
    name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

2. Utilize a ação Azure PostgreSQL Deploy para ligar à sua instância PostgreSQL. `POSTGRESQL_SERVER_NAME`Substitua-o pelo nome do seu servidor. Deve ter um ficheiro de dados PostgreSQL nomeado `data.sql` ao nível da raiz do seu repositório.

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. Complete o seu fluxo de trabalho adicionando uma ação ao logout da Azure. Aqui está o fluxo de trabalho completo. O ficheiro aparecerá na `.github/workflows` pasta do seu repositório.

    ```yaml
   name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/postgresql@v1
      with:
        server-name: POSTGRESQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Reveja a sua implementação

1. Vá às **ações** para o seu repositório GitHub.

1. Abra o primeiro resultado para ver os registos detalhados do funcionaamento do seu fluxo de trabalho.

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="Log of GitHub actions run":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando a sua base de dados e repositório Azure PostgreSQL já não forem necessárias, limpe os recursos que implementou eliminando o grupo de recursos e o seu repositório GitHub.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça a integração de Azure e GitHub](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [Saiba como se conectar ao servidor](how-to-connect-query-guide.md)
