---
title: Utilize ações do GitHub para fazer atualizações de código em Funções Azure
description: Saiba como usar as ações do GitHub para definir um fluxo de trabalho para construir e implementar projetos de Funções Azure no GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 02f5399e89900a438fb94f973c497a54dc05cfee
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210161"
---
# <a name="continuous-delivery-by-using-github-action"></a>Entrega contínua utilizando a GitHub Action

[GitHub Actions](https://github.com/features/actions) permite definir um fluxo de trabalho para construir e implementar automaticamente o código de funções para funcionar app em Azure. 

Em GitHub Actions, um [fluxo de trabalho](https://help.github.com/articles/about-github-actions#workflow) é um processo automatizado que você define no seu repositório GitHub. Este processo diz ao GitHub como construir e implementar o seu projeto de aplicações de funções no GitHub. 

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho. 

Para um fluxo de trabalho de Funções Azure, o ficheiro tem três secções: 

| Section | Tarefas |
| ------- | ----- |
| **Autenticação** | <ol><li>Defina um diretor de serviço.</li><li>Descarregue o perfil de publicação.</li><li>Criar um segredo do GitHub.</li></ol>|
| **Compilar** | <ol><li>Crie o ambiente.</li><li>Construa o aplicativo de função.</li></ol> |
| **Implementar** | <ol><li>Implemente a aplicação de função.</li></ol>|

> [!NOTE]
> Não precisa de criar um principal de serviço se decidir utilizar o perfil de publicação para autenticação.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) utilizando o comando [ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](/cli/azure/). Pode executar este comando utilizando [a Azure Cloud Shell](https://shell.azure.com) no portal Azure ou selecionando o botão **Tentar.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Neste exemplo, substitua os espaços reservados no recurso pelo ID de subscrição, grupo de recursos e nome de aplicação de funções. A saída são as credenciais de atribuição de funções que fornecem acesso à sua aplicação de função. Copie este objeto JSON, que pode utilizar para autenticar a partir do GitHub.

> [!IMPORTANT]
> É sempre uma boa prática conceder o mínimo acesso. É por isso que o âmbito do exemplo anterior está limitado à aplicação de função específica e não a todo o grupo de recursos.

## <a name="download-the-publishing-profile"></a>Descarregue o perfil de publicação

Para descarregar o perfil de publicação da sua aplicação de função:

1. Selecione a página **geral** da aplicação de funções e, em seguida, selecione **Obtenha o perfil de publicação**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Baixar perfil de publicação":::

1. Guarde e copie o conteúdo do ficheiro de definições de publicação.

## <a name="configure-the-github-secret"></a>Configure o segredo do GitHub

1. No [GitHub,](https://github.com)navegue pelo seu repositório, selecione **Settings**  >  **Secrets**  >  **Add a new secret**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Adicionar Segredo":::

1. Adicione um novo segredo.

   * Se estiver a utilizar o principal de serviço que criou utilizando o CLI Azure, utilize `AZURE_CREDENTIALS` para o **Nome**. Em seguida, cole a saída de objeto JSON copiada para **valor**, e selecione **Adicionar segredo**.
   * Se estiver a utilizar um perfil de publicação, utilize `SCM_CREDENTIALS` o **nome**. Em seguida, utilize o conteúdo de ficheiro do perfil de publicação para **valor**, e selecione **Adicionar segredo**.

O GitHub pode agora autenticar a sua aplicação de função em Azure.

## <a name="set-up-the-environment"></a>Configure o ambiente 

A configuração do ambiente é feita utilizando uma ação de configuração de publicação específica da linguagem.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra a parte do fluxo de trabalho que utiliza a `actions/setup-node` ação para configurar o ambiente:

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

O exemplo a seguir mostra a parte do fluxo de trabalho que utiliza a `actions/setup-python` ação para configurar o ambiente:

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

O exemplo a seguir mostra a parte do fluxo de trabalho que utiliza a `actions/setup-dotnet` ação para configurar o ambiente:

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

O exemplo a seguir mostra a parte do fluxo de trabalho que utiliza a  `actions/setup-java` ação para configurar o ambiente:

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

Isto depende da linguagem e das línguas suportadas pelas Funções Azure, esta secção deve ser os passos padrão de construção de cada idioma.

O exemplo a seguir mostra a parte do fluxo de trabalho que constrói a aplicação de função, que é específica da linguagem:

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

## <a name="deploy-the-function-app"></a>Implementar a aplicação de funções

Para implementar o seu código numa aplicação de função, terá de utilizar a `Azure/functions-action` ação. Esta ação tem dois parâmetros:

|Parâmetro |Explicação  |
|---------|---------|
|**_nome de aplicativo_** | (Obrigatório) O nome da sua aplicação de função. |
|_**nome slot**_ | (Opcional) O nome da ranhura de [implantação](functions-deployment-slots.md) para a que pretendes implantar. A ranhura já deve ser definida na sua aplicação de função. |


O exemplo a seguir utiliza a versão 1 do `functions-action` :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Passos seguintes

Para visualizar um ficheiro de fluxo de trabalho completo .yaml, consulte um dos ficheiros do repo de amostras de [fluxo de trabalho Azure GitHub Actions](https://aka.ms/functions-actions-samples) que têm no `functionapp` nome. Pode utilizar estas amostras como ponto de partida para o seu fluxo de trabalho.

> [!div class="nextstepaction"]
> [Learn more about GitHub Actions](https://help.github.com/en/articles/about-github-actions) (Saiba mais sobre o GitHub Actions)
