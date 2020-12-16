---
title: 'Quickstart: Ligue-se ao Azure MySQL com as ações do GitHub'
description: Use Azure MySQL a partir de um fluxo de trabalho de ações GitHub
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 6e3dbb968d0dff8d8227518fe53d257bfc1fae4b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607971"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-mysql"></a>Quickstart: Use ações do GitHub para ligar ao Azure MySQL

**APLICA-SE A**: :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Base de Dados de Azure para PostgreSQL - Base de Dados Azure de servidor único :::image type="icon" source="./media/applies-to/yes.png" border="false"::: para PostgreSQL - Servidor Flexível

Inicie com [as ações do GitHub](https://docs.github.com/en/free-pro-team@latest/actions) utilizando um fluxo de trabalho para implementar atualizações de base de dados [para a Base de Dados Azure para o MySQL](https://azure.microsoft.com/services/mysql/).


## <a name="prerequisites"></a>Pré-requisitos

Vai precisar de: 
- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um repositório GitHub com dados de amostra `data.sql` (). Se não tiver uma conta GitHub, [inscreva-se gratuitamente.](https://github.com/join)  
- Uma base de dados Azure para o servidor MySQL.
    - [Quickstart: Criar uma Base de Dados Azure para servidor MySQL no portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>Visão geral do ficheiro do fluxo de trabalho

Um fluxo de trabalho de GitHub Actions é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

O ficheiro tem duas secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço. <br /> 2. Criar um segredo do GitHub. |
|**Implementar** | 1. Implementar a base de dados. |

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

Pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md) com o comando [ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) no [Azure CLI](/cli/azure/). Executar este comando com [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Try it.**

Substitua os espaços reservados `server-name` pelo nome do seu servidor MySQL alojado no Azure. Substitua o iD e pelo grupo de recursos de `subscription-id` `resource-group` subscrição ligado ao seu servidor MySQL.  

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

## <a name="copy-the-mysql-connection-string"></a>Copie a cadeia de ligação MySQL 

No portal Azure, aceda à base de dados Azure para o servidor MySQL e abra **as**  >  **cadeias de ligação** de definições . Copie a cadeia de ligação de **ADO.NET**. Substitua os valores de espaço reservado `your_database` para e `your_password` . A cadeia de ligação será semelhante a esta. 

> [!IMPORTANT]
> - Para o servidor único, **utilize Uid= adminusername@servername**. Note o **@servername** que é necessário.
> - Para o servidor flexível, utilize **uid= adminusername** sem o @servername . Note que o servidor flexível MySQL está em pré-visualização. 


```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
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

1. Cole o valor da cadeia de ligação no campo de valor do segredo. Dê o nome ao `AZURE_MYSQL_CONNECTION_STRING` segredo.


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

1. Mude o nome do seu fluxo de trabalho `MySQL for GitHub Actions` e adicione as ações de check-out e login. Estas ações irão verificar o código do seu site e autenticar-se com o Azure usando o `AZURE_CREDENTIALS` segredo GitHub que criou anteriormente. 

    ```yaml
    name: MySQL for GitHub Actions

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

1. Utilize a ação Azure MySQL Deploy para ligar à sua instância MySQL. `MYSQL_SERVER_NAME`Substitua-o pelo nome do seu servidor. Deve ter um ficheiro de dados MySQL nomeado `data.sql` ao nível da raiz do seu repositório. 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. Complete o seu fluxo de trabalho adicionando uma ação ao logout da Azure. Aqui está o fluxo de trabalho completo. O ficheiro aparecerá na `.github/workflows` pasta do seu repositório.

    ```yaml
   name: MySQL for GitHub Actions

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

    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Reveja a sua implementação

1. Vá às **ações** para o seu repositório GitHub. 

1. Abra o primeiro resultado para ver os registos detalhados do funcionaamento do seu fluxo de trabalho. 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="Log of GitHub actions run":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando a sua base de dados EsQL E o repositório Azure já não forem necessários, limpe os recursos que implementou eliminando o grupo de recursos e o seu repositório GitHub. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça a integração de Azure e GitHub](/azure/developer/github/)
