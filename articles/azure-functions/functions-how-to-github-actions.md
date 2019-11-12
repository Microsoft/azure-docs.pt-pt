---
title: Usar ações do GitHub para fazer atualizações de código no Azure Functions
description: Saiba como usar ações do GitHub para definir um fluxo de trabalho para criar e implantar Azure Functions projetos no GitHub.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: 486033ef4120d721458add7f23cdf9b78a44a388
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928357"
---
# <a name="continuous-delivery-by-using-github-action"></a>Entrega contínua usando a ação do GitHub

As [ações do GitHub](https://github.com/features/actions) permitem definir um fluxo de trabalho para criar e implantar automaticamente o código de funções no aplicativo de funções no Azure. 

> [!IMPORTANT]  
> No momento, as ações do GitHub estão em beta. Primeiro, você deve [se inscrever para ingressar na versão prévia](https://github.com/features/actions) usando sua conta do github.

Em ações do GitHub, um [fluxo de trabalho](https://help.github.com/articles/about-github-actions#workflow) é um processo automatizado que você define em seu repositório github. Esse processo informa ao GitHub como criar e implantar seu projeto de aplicativo do Functions no GitHub. 

Um fluxo de trabalho é definido por um arquivo YAML (. yml) no caminho `/.github/workflows/` em seu repositório. Essa definição contém as várias etapas e parâmetros que compõem o fluxo de trabalho. 

Para um fluxo de trabalho Azure Functions, o arquivo tem três seções: 

| Section | Tarefas |
| ------- | ----- |
| **Autenticação** | <ol><li>Defina uma entidade de serviço.</li><li>Baixar o perfil de publicação.</li><li>Crie um segredo do GitHub.</li></ol>|
| **Integrado** | <ol><li>Configure o ambiente.</li><li>Crie o aplicativo de funções.</li></ol> |
| **Implementar** | <ol><li>Implante o aplicativo de funções.</li></ol>|

> [!NOTE]
> Você não precisará criar uma entidade de serviço se decidir usar o perfil de publicação para autenticação.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Você pode criar uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando o comando [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) na [CLI do Azure](/cli/azure/). Você pode executar esse comando usando [Azure cloud Shell](https://shell.azure.com) na portal do Azure ou selecionando o botão **experimentar** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Neste exemplo, substitua os espaços reservados no recurso por sua ID de assinatura, grupo de recursos e nome do aplicativo de funções. A saída são as credenciais de atribuição de função que fornecem acesso ao seu aplicativo de funções. Copie esse objeto JSON, que você pode usar para autenticar do GitHub.

> [!IMPORTANT]
> É sempre uma boa prática conceder acesso mínimo. É por isso que o escopo no exemplo anterior é limitado ao aplicativo de funções específico e não ao grupo de recursos inteiro.

## <a name="download-the-publishing-profile"></a>Baixar o perfil de publicação

Você pode baixar o perfil de publicação de seu functionapp, acessando a página **visão geral** do seu aplicativo e clicando em **obter perfil de publicação**.

   ![Baixar o perfil de publicação](media/functions-how-to-github-actions/get-publish-profile.png)

Copie o conteúdo do arquivo.

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub

1. No [GitHub](https://github.com), procure seu repositório, selecione **configurações** > **segredos** > **Adicionar um novo segredo**.

   ![Adicionar segredo](media/functions-how-to-github-actions/add-secret.png)

1. Use `AZURE_CREDENTIALS` para o **nome** e a saída do comando copiado para **valor**, se você selecionar **Adicionar segredo**. Se você estiver usando o perfil de publicação, use `SCM_CREDENTIALS` para o **nome** e o conteúdo do arquivo para **valor**.

O GitHub agora pode se autenticar no seu aplicativo de funções no Azure.

## <a name="set-up-the-environment"></a>Configurar o ambiente 

A configuração do ambiente pode ser feita usando uma das ações de instalação de publicação.

|Idioma | Ação de instalação |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

Os exemplos a seguir mostram a parte do fluxo de trabalho que configura o ambiente para os vários idiomas com suporte:

**JavaScript**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

**Python**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-function-app"></a>Compilar o aplicativo de funções

Isso depende do idioma e dos idiomas com suporte pelo Azure Functions, esta seção deve ser as etapas de compilação padrão de cada idioma.

Os exemplos a seguir mostram a parte do fluxo de trabalho que cria o aplicativo de funções em vários idiomas com suporte.:

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```

## <a name="deploy-the-function-app"></a>Implementar a aplicação de função

Para implantar seu código em um aplicativo de funções, será necessário usar a ação `Azure/functions-action`. Esta ação tem dois parâmetros:

|Parâmetro |Explicação  |
|---------|---------|
|**_nome do aplicativo_** | Obrigatório O nome do seu aplicativo de funções. |
|_**nome do slot**_ | Adicional O nome do [slot de implantação](functions-deployment-slots.md) no qual você deseja implantar. O slot já deve estar definido em seu aplicativo de funções. |


O exemplo a seguir usa a versão 1 do `functions-action`:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Passos seguintes

Para exibir um Workflow. YAML completo, consulte um dos arquivos no repositório de [exemplos de fluxo de trabalho de ações do Azure GitHub](https://github.com/Azure/actions-workflow-samples) que têm `functionapp` no nome. Você pode usar esses exemplos em um ponto de partida para seu fluxo de trabalho.

> [!div class="nextstepaction"]
> [Saiba mais sobre as ações do GitHub](https://help.github.com/en/articles/about-github-actions)
