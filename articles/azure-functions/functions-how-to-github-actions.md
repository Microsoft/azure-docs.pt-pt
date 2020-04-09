---
title: Use as ações do GitHub para fazer atualizações de código em Funções Azure
description: Aprenda a usar as Ações GitHub para definir um fluxo de trabalho para construir e implementar projetos de Funções Azure no GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: 54010269e5b61ebf28a29dd3165c4310f3472817
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878209"
---
# <a name="continuous-delivery-by-using-github-action"></a>Entrega contínua utilizando gitHub Action

[As Ações GitHub](https://github.com/features/actions) permitem definir um fluxo de trabalho para construir e implementar automaticamente o código de funções para funcionar a app em Azure. 

Nas Ações GitHub, um [fluxo de trabalho](https://help.github.com/articles/about-github-actions#workflow) é um processo automatizado que define no seu repositório GitHub. Este processo diz ao GitHub como construir e implementar o projeto de aplicações de funções no GitHub. 

Um fluxo de trabalho é definido por um ficheiro `/.github/workflows/` YAML (.yml) no caminho do seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho. 

Para um fluxo de trabalho de Funções Azure, o ficheiro tem três secções: 

| Section | Tarefas |
| ------- | ----- |
| **Autenticação** | <ol><li>Defina um diretor de serviço.</li><li>Descarregue o perfil de publicação.</li><li>Crie um segredo GitHub.</li></ol>|
| **Compilação** | <ol><li>Instale o ambiente.</li><li>Construa a aplicação de funções.</li></ol> |
| **Implementar** | <ol><li>Implemente a aplicação de função.</li></ol>|

> [!NOTE]
> Não precisa de criar um diretor de serviço se decidir utilizar o perfil editorial para autenticação.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Pode criar um diretor de [serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) utilizando o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](/cli/azure/). Pode executar este comando utilizando a [Azure Cloud Shell](https://shell.azure.com) no portal Azure ou selecionando o botão **Experimente..**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Neste exemplo, substitua os espaços reservados do recurso pelo seu ID de subscrição, grupo de recursos e nome de aplicação de função. A saída são as credenciais de atribuição de funções que fornecem acesso à sua aplicação de funções. Copie este objeto JSON, que pode usar para autenticar a partir do GitHub.

> [!IMPORTANT]
> É sempre uma boa prática conceder o mínimo de acesso. É por isso que o âmbito no exemplo anterior se limita à aplicação de funções específicas e não a todo o grupo de recursos.

## <a name="download-the-publishing-profile"></a>Descarregue o perfil editorial

Pode descarregar o perfil de publicação da sua aplicação de funções, indo para a página **de overview** da sua aplicação e clicando **em publicar**o perfil .

   ![Descarregue o perfil de publicação](media/functions-how-to-github-actions/get-publish-profile.png)

Copie o conteúdo do ficheiro.

## <a name="configure-the-github-secret"></a>Configure o segredo GitHub

1. No [GitHub,](https://github.com)navegue até ao seu repositório, selecione**Segredos** > de **Definições** > **Adicione um novo segredo**.

   ![Adicionar Segredo](media/functions-how-to-github-actions/add-secret.png)

1. Adicione um novo segredo.

   * Se estiver a utilizar o diretor de serviço que criou `AZURE_CREDENTIALS` utilizando o Azure CLI, utilize para o **Nome**. Em seguida, colhe a saída de objetos JSON copiada para **Valor**, e selecione **Adicionar segredo**.
   * Se estiver a utilizar um perfil `SCM_CREDENTIALS` editorial, utilize para o **Nome**. Em seguida, utilize o conteúdo de ficheiro do perfil editorial para **Valor**, e selecione **Adicionar segredo**.

O GitHub já pode autenticar a sua aplicação de funções no Azure.

## <a name="set-up-the-environment"></a>Configurar o ambiente 

A criação do ambiente é feita através de uma ação de configuração de publicação específica da linguagem.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo que se segue mostra a `actions/setup-node` parte do fluxo de trabalho que utiliza a ação para criar o ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

O exemplo que se segue mostra a `actions/setup-python` parte do fluxo de trabalho que utiliza a ação para criar o ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

O exemplo que se segue mostra a `actions/setup-dotnet` parte do fluxo de trabalho que utiliza a ação para criar o ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

O exemplo que se segue mostra a `actions/setup-java` parte do fluxo de trabalho que utiliza a ação para criar o ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Construir a aplicação de função

Isto depende da linguagem e das línguas suportadas pelas Funções Azure, esta secção deve ser os passos padrão de construção de cada língua.

O exemplo seguinte mostra a parte do fluxo de trabalho que constrói a aplicação de função, que é específica da linguagem:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="java"></a>[Java](#tab/java)

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
---

## <a name="deploy-the-function-app"></a>Implementar a aplicação de função

Para implementar o seu código numa aplicação `Azure/functions-action` de função, terá de utilizar a ação. Esta ação tem dois parâmetros:

|Parâmetro |Explicação  |
|---------|---------|
|**_nome de aplicativo_** | (Obrigatório) O nome da sua aplicação de funções. |
|_**slot-name**_ | (Opcional) O nome da ranhura de [implantação](functions-deployment-slots.md) para onde quer implantar. A ranhura já deve ser definida na sua aplicação de funções. |


O exemplo seguinte utiliza `functions-action`a versão 1 do :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Passos seguintes

Para ver um fluxo de trabalho completo .yaml, consulte um dos ficheiros nas `functionapp` amostras de fluxo de trabalho do [Azure GitHub Actions repo](https://aka.ms/functions-actions-samples) que têm no nome. Pode utilizar estas amostras como ponto de partida para o seu fluxo de trabalho.

> [!div class="nextstepaction"]
> [Saiba mais sobre as ações do GitHub](https://help.github.com/en/articles/about-github-actions)
