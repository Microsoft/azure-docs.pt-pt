---
title: Configure CI/CD com ações do GitHub
description: Saiba como implementar o seu código no Azure App Service a partir de um pipeline CI/CD com ações GitHub. Personalize as tarefas de construção e execute implementações complexas.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 1ed2b007ae00516a030e67b7f6abacbd00a8d403
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772888"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Implementar no Serviço de Aplicações através do GitHub Actions

Começa com [as ações do GitHub](https://docs.github.com/en/actions/learn-github-actions) para automatizar o teu fluxo de trabalho e implementar para o Serviço de [Aplicações Azure](overview.md) do GitHub. 

## <a name="prerequisites"></a>Pré-requisitos 

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma conta do GitHub. Se não tiver um, inscreva-se [de graça.](https://github.com/join)  
- Uma aplicação de Serviço de Aplicações Azure em funcionamento. 
    - .NET: [Criar uma aplicação web core ASP.NET em Azure](quickstart-dotnetcore.md)
    - ASP.NET: [Criar uma aplicação web ASP.NET Framework em Azure](quickstart-dotnet-framework.md)
    - JavaScript: [Criar uma aplicação web Node.js no Azure App Service](quickstart-nodejs.md)  
    - Java: [Criar uma aplicação Java no Azure App Service](quickstart-java.md)
    - Python: [Crie uma aplicação Python no Azure App Service](quickstart-python.md)

## <a name="workflow-file-overview"></a>Visão geral do ficheiro do fluxo de trabalho

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

O ficheiro tem três secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um perfil principal de serviço ou de publicação. <br /> 2. Criar um segredo do GitHub. |
|**Compilar** | 1. Criar o ambiente. <br /> 2. Construa a aplicação web. |
|**Implementar** | 1. Implementar a aplicação web. |

## <a name="use-the-deployment-center"></a>Utilize o Centro de Implantação

Pode começar rapidamente com as ações do GitHub utilizando o Centro de Implementação do Serviço de Aplicações. Isto irá gerar automaticamente um ficheiro de fluxo de trabalho baseado na sua pilha de aplicações e compromete-o com o seu repositório GitHub no diretório correto.

1. Navegue para o seu webapp no portal Azure
1. No lado esquerdo, clique no **Centro de Implementação**
1. Em **Implementação Contínua (CI/ CD)**, selecione **GitHub**
1. Em seguida, selecione **GitHub Actions**
1. Use as dropdowns para selecionar o seu repositório GitHub, ramo e pilha de aplicações
    - Se o ramo selecionado estiver protegido, pode continuar a adicionar o ficheiro de fluxo de trabalho. Certifique-se de rever as proteções da sua filial antes de continuar.
1. No ecrã final, pode rever as suas seleções e visualizar o ficheiro de fluxo de trabalho que será comprometido com o repositório. Se as seleções estiverem corretas, clique em **Terminar**

Isto irá comprometer o ficheiro de fluxo de trabalho para o repositório. O fluxo de trabalho para construir e implementar a sua aplicação começará imediatamente.

## <a name="set-up-a-workflow-manually"></a>Configurar um fluxo de trabalho manualmente

Também pode implementar um fluxo de trabalho sem utilizar o Centro de Implantação. Para tal, terá de gerar primeiro credenciais de implantação. 

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

A forma recomendada de autenticar com os Serviços de Aplicações Azure para ações GitHub é com um perfil de publicação. Também pode autenticar com um diretor de serviço, mas o processo requer mais passos. 

Guarde a sua credencial de perfil de publicação ou principal de serviço como [um segredo do GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) para autenticar com a Azure. Vais aceder ao segredo dentro do teu fluxo de trabalho. 

# <a name="publish-profile"></a>[Publicar perfil](#tab/applevel)

Um perfil de publicação é uma credencial de nível de aplicação. Configura o teu perfil de publicação como um segredo do GitHub. 

1. Vá ao seu serviço de aplicações no portal Azure. 

1. Na página **'Vista Geral',** selecione **Obter Perfil De publicação**.

1. Guarde o ficheiro transferido. Vais usar o conteúdo do ficheiro para criar um segredo do GitHub.

> [!NOTE]
> A partir de outubro de 2020, as aplicações web do Linux precisarão da definição da aplicação `WEBSITE_WEBDEPLOY_USE_SCM` definida para antes de descarregar o perfil de `true` **publicação.** Este requisito será removido no futuro.

# <a name="service-principal"></a>[Service principal (Principal de serviço)](#tab/userlevel)

Pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) no [Azure CLI](/cli/azure/). Executar este comando com [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Try it.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

No exemplo acima, substitua os espaços reservados pelo seu ID de subscrição, nome de grupo de recursos e nome de aplicação. A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua aplicação de Serviço de Aplicações semelhante abaixo. Copie este objeto JSON para mais tarde.

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
> É sempre uma boa prática conceder o mínimo acesso. O âmbito no exemplo anterior está limitado à aplicação específica do Serviço de Aplicações e não a todo o grupo de recursos.

---

## <a name="configure-the-github-secret"></a>Configure o segredo do GitHub


# <a name="publish-profile"></a>[Publicar perfil](#tab/applevel)

No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

Para utilizar [credenciais de nível de aplicação,](#generate-deployment-credentials)cole o conteúdo do ficheiro de perfil de publicação descarregado no campo de valor do segredo. Diga o nome do `AZURE_WEBAPP_PUBLISH_PROFILE` segredo.

Ao configurar o seu fluxo de trabalho GitHub, utilize a ação `AZURE_WEBAPP_PUBLISH_PROFILE` da App Azure Web. Por exemplo:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Service principal (Principal de serviço)](#tab/userlevel)

No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

Para utilizar [credenciais ao nível do utilizador,](#generate-deployment-credentials)cole toda a saída JSON do comando Azure CLI para o campo de valor do segredo. Dê o nome ao `AZURE_CREDENTIALS` segredo.

Quando configurar o ficheiro de fluxo de trabalho mais tarde, utilize o segredo para a entrada `creds` da ação Azure Login. Por exemplo:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Configurar o ambiente

A configuração do ambiente pode ser feita utilizando uma das ações de configuração.

|**Linguagem**  |**Ação de configuração**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Os exemplos que se seguem mostram como configurar o ambiente para as diferentes línguas apoiadas:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>Construa a aplicação web

O processo de construção de uma aplicação web e implementação para o Azure App Service muda dependendo do idioma. 

Os exemplos a seguir mostram a parte do fluxo de trabalho que constrói a aplicação web, em diferentes idiomas suportados.

Para todos os idiomas, pode definir o diretório de raiz da aplicação web com `working-directory` . 

**.NET**

A variável ambiental `AZURE_WEBAPP_PACKAGE_PATH` define o caminho para o seu projeto de aplicação web. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Pode restaurar as dependências do NuGet e executar msbuild com `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Para Node.js, pode definir `working-directory` ou alterar para o diretório npm em `pushd` . 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>Implementar no Serviço de Aplicações

Para implementar o seu código numa aplicação do Serviço de Aplicações, utilize a `azure/webapps-deploy@v2` ação. Esta ação tem quatro parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **nome de aplicativo** | (Obrigatório) Nome da app App Service | 
| **perfil de publicação** | (Opcional) Publique conteúdos de ficheiros de perfil com segredos de implementação da Web |
| **pacote** | (Opcional) Caminho para embalagem ou pasta. O caminho pode incluir *.zip, *.war, *.jar, ou uma pasta para implantar |
| **nome slot** | (Opcional) Introduza uma ranhura existente que não seja a [ranhura](deploy-staging-slots.md) de produção |


# <a name="publish-profile"></a>[Publicar perfil](#tab/applevel)

### <a name="net-core"></a>.NET Core

Construa e implemente uma aplicação .NET Core para a Azure utilizando um perfil de publicação Azure. A `publish-profile` entrada refere o segredo que `AZURE_WEBAPP_PUBLISH_PROFILE` criaste anteriormente.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

Construa e implemente uma aplicação MVC ASP.NET que utiliza NuGet e `publish-profile` para autenticação. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@main  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Construa e implemente uma aplicação Java Spring para a Azure usando um perfil de publicação Azure. A `publish-profile` entrada refere o segredo que `AZURE_WEBAPP_PUBLISH_PROFILE` criaste anteriormente.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Para implementar um `war` em vez de `jar` um, alterar o `package` valor. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Construa e implemente uma aplicação Node.js para o Azure utilizando o perfil de publicação da aplicação. A `publish-profile` entrada refere o segredo que `AZURE_WEBAPP_PUBLISH_PROFILE` criaste anteriormente.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Construa e implemente uma aplicação Python para o Azure utilizando o perfil de publicação da aplicação. Note como a `publish-profile` entrada refere o segredo que criou `AZURE_WEBAPP_PUBLISH_PROFILE` anteriormente.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Service principal (Principal de serviço)](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Construa e implemente uma aplicação .NET Core para a Azure utilizando um diretor de serviço Azure. Note como a `creds` entrada refere o segredo que criou `AZURE_CREDENTIALS` anteriormente.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Construa e implemente uma aplicação MVC ASP.NET para a Azure utilizando um diretor de serviço Azure. Note como a `creds` entrada refere o segredo que criou `AZURE_CREDENTIALS` anteriormente.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@main
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Construa e implemente uma aplicação Java Spring para Azure usando um diretor de serviço Azure. Note como a `creds` entrada refere o segredo que criou `AZURE_CREDENTIALS` anteriormente.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Construa e implemente uma aplicação Node.js para a Azure usando um diretor de serviço Azure. Note como a `creds` entrada refere o segredo que criou `AZURE_CREDENTIALS` anteriormente.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Construa e implemente uma aplicação Python para Azure usando um diretor de serviço Azure. Note como a `creds` entrada refere o segredo que criou `AZURE_CREDENTIALS` anteriormente.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar o nosso conjunto de Ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar GitHub para CI/CD e implementar suas aplicações para Azure.

- [Fluxos de trabalho de ações para implantar para Azure](https://github.com/Azure/actions-workflow-samples)

- [Início de sessão no Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp para contentores](https://github.com/Azure/webapps-container-deploy)

- [Login/logout do Docker](https://github.com/Azure/docker-login)

- [Eventos que acionam fluxos de trabalho](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [Implantação de K8s](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho de arranque](https://github.com/actions/starter-workflows)
