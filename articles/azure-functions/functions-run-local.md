---
title: Trabalhar com o Azure Functions Core Tools
description: Aprenda a codificar e a testar as funções Azure a partir da solicitação de comando ou terminal no seu computador local antes de as executar em Funções Azure.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 17daef18d87c4b5c906694ab1394694b32b6b130
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164873"
---
# <a name="work-with-azure-functions-core-tools"></a>Trabalhar com o Azure Functions Core Tools

O Azure Functions Core Tools permite desenvolver e testar as suas funções no computador local a partir da linha de comandos ou terminal. As suas funções locais podem ligar-se aos serviços Azure vivos e pode desormesar as suas funções no computador local utilizando o tempo de funcionamento completo das Funções. Pode até implementar uma aplicação de função na sua subscrição Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Desenvolver funções no seu computador local e publicá-las para Azure utilizando Ferramentas Core segue estes passos básicos:

> [!div class="checklist"]
> * [Instale as Ferramentas Centrais e as dependências.](#v2)
> * [Crie um projeto de aplicação de função a partir de um modelo específico do idioma.](#create-a-local-functions-project)
> * [Registar o gatilho e as extensões de ligação.](#register-extensions)
> * [Defina armazenamento e outras ligações.](#local-settings-file)
> * [Crie uma função a partir de um modelo específico do gatilho e da linguagem.](#create-func)
> * [Executar a função localmente.](#start)
> * [Publique o projeto ao Azure.](#publish)

## <a name="core-tools-versions"></a>Versões do Core Tools

Existem três versões de Azure Functions Core Tools. A versão que utiliza depende do ambiente de desenvolvimento local, [da escolha da linguagem](supported-languages.md)e do nível de suporte necessário:

+ [**Versão 3.x/2.x**](#v2): Suporta a [versão 3.x ou 2.x do tempo de funcionamento das Funções Azure](functions-versions.md). Estas versões suportam [o Windows](?tabs=windows#v2), [macOS](?tabs=macos#v2)e [Linux](?tabs=linux#v2) e utilizam gestores de pacotes específicos da plataforma ou npm para instalação.

+ **Versão 1.x**: Suporta a versão 1.x do tempo de funcionamento das Funções Azure. Esta versão das ferramentas só é suportada em computadores Windows e é instalada a partir de um [pacote npm](https://www.npmjs.com/package/azure-functions-core-tools).

Só é possível instalar uma versão do Core Tools num determinado computador. Salvo indicação em contrário, os exemplos deste artigo são para a versão 3.x.

## <a name="prerequisites"></a>Pré-requisitos

A Azure Functions Core Tools atualmente depende do CLI Azure para autenticar com a sua conta Azure. Isto significa que deve [instalar o Azure CLI localmente](/cli/azure/install-azure-cli) para poder [publicar no Azure](#publish) a partir de Ferramentas Principais de Funções Azure. 

## <a name="install-the-azure-functions-core-tools"></a>Instalar as Ferramentas de Núcleo de Funções do Azure

[As Ferramentas Principais do Azure Functions] incluem uma versão do mesmo tempo de execução que alimenta o tempo de execução das Funções Azure que pode executar no seu computador de desenvolvimento local. Também fornece comandos para criar funções, ligar ao Azure e implementar projetos de função.

### <a name="version-3x-and-2x"></a><a name="v2"></a>Versão 3.x e 2.x

Versão 3.x/2.x das ferramentas utiliza o tempo de execução das Funções Azure que é construído em .NET Core. Esta versão é suportada em todas as plataformas suportes .NET Core, incluindo [Windows,](?tabs=windows#v2) [macOS](?tabs=macos#v2)e [Linux.](?tabs=linux#v2) 

> [!IMPORTANT]
> Pode contornar o requisito de instalação do .NET Core SDK utilizando [pacotes de extensão].

# <a name="windows"></a>[Windows](#tab/windows)

Os seguintes passos utilizam um instalador do Windows (MSI) para instalar o Core Tools v3.x. Para obter mais informações sobre outros instaladores baseados em pacotes, que são obrigados a instalar Ferramentas Core v2.x, consulte a [leitura de Ferramentas Principais](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Descarregue e execute o instalador Core Tools, com base na sua versão do Windows:

    - [v3.x - Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2135274) (Recomendado. [A depuragem visual do Código estúdio](functions-develop-vs-code.md#debugging-functions-locally) requer 64 bits.)
    - [v3.x - Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2135275)

1. Se não pretender utilizar [pacotes de extensão,](functions-bindings-register.md#extension-bundles)instale o [.NET Core 3.x SDK para windows](https://dotnet.microsoft.com/download).

# <a name="macos"></a>[macOS](#tab/macos)

Os passos seguintes utilizam o Homebrew para instalar as Ferramentas Core no macOS.

1. Instale [homebrew,](https://brew.sh/)se ainda não estiver instalado.

1. Instale o pacote Ferramentas Principais:

    ##### <a name="v3x-recommended"></a>v3.x (recomendado)

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```
    
    ##### <a name="v2x"></a>v2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. Se não pretender utilizar [pacotes de extensão,](functions-bindings-register.md#extension-bundles)instale o [.NET Core 3.x SDK para o macOS](https://dotnet.microsoft.com/download).

# <a name="linux"></a>[Linux](#tab/linux)

Os passos seguintes utilizam [o APT](https://wiki.debian.org/Apt) para instalar ferramentas nucleares na sua distribuição Ubuntu/Debian Linux. Para outras distribuições linux, consulte o [core tools readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Instale a chave GPG do repositório de pacotes da Microsoft para validar a integridade do pacote:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Configurar a lista de origem APT antes de fazer uma atualização APT.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Verifique se o `/etc/apt/sources.list.d/dotnetdev.list` ficheiro é uma das cadeias de versão Linux apropriadas listadas abaixo:

    | Distribuição linux | Versão |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Inicie a atualização de origem APT:

    ```bash
    sudo apt-get update
    ```

1. Instale o pacote Ferramentas Principais:

    ##### <a name="v3x-recommended"></a>v3.x (recomendado)
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```
    
    ##### <a name="v2x"></a>v2.x
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. Se não pretender utilizar [pacotes de extensão,](functions-bindings-register.md#extension-bundles)instale [.NET Core 3.x SDK para o Linux](https://dotnet.microsoft.com/download).

---

## <a name="create-a-local-functions-project"></a>Criar um projeto de Funções Locais

Um diretório de projetos de funções contém os ficheiros [host.jse](functions-host-json.md) [local.settings.js,](#local-settings-file)juntamente com sub-dobradores que contêm o código para funções individuais. Este diretório é o equivalente a uma aplicação de função em Azure. Para saber mais sobre a estrutura da pasta Funções, consulte o [guia de desenvolvedores de Funções Azure](functions-reference.md#folder-structure).

A versão 3.x/2.x requer que selecione um idioma predefinido para o seu projeto quando este é inicializado. Na versão 3.x/2.x, todas as funções adicionadas utilizam modelos de linguagem predefinidos. Na versão 1.x, especifica o idioma sempre que cria uma função.

Na janela do terminal ou a partir de um pedido de comando, executar o seguinte comando para criar o projeto e o repositório local git:

```
func init MyFunctionProj
```

>[!IMPORTANT]
> Java usa um arquétipo Maven para criar o projeto Funções locais, juntamente com a sua primeira função http desencadeada. Utilize o seguinte comando para criar o seu projeto Java: `mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype` . Por exemplo, utilizando o arquétipo Maven, consulte a [linha de comando de arranque rápido](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java).  

Quando fornece um nome de projeto, uma nova pasta com esse nome é criada e inicializada. Caso contrário, a pasta atual é inicializada.  
Na versão 3.x/2.x, quando executar o comando, deve escolher um tempo de execução para o seu projeto. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Utilize as teclas de seta para cima/para baixo para escolher um idioma e, em seguida, prima Enter. Se planeia desenvolver funções JavaScript ou TypeScript, escolha **o nó**e, em seguida, selecione o idioma. O TypeScript tem [alguns requisitos adicionais.](functions-reference-node.md#typescript) 

A saída parece o seguinte exemplo para um projeto JavaScript:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init` suporta as seguintes opções, que são apenas a versão 3.x/2.x, salvo indicação em contrário:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Cria funções .NET como script C#, que é o comportamento da versão 1.x. Válido apenas com `--worker-runtime dotnet` . |
| **`--docker`** | Cria um Dockerfile para um recipiente utilizando uma imagem base que se baseia no escolhido `--worker-runtime` . Utilize esta opção quando pretender publicar num recipiente Linux personalizado. |
| **`--docker-only`** |  Adiciona um Dockerfile a um projeto existente. Indicações para o tempo de funcionação do trabalhador se não for especificado ou definido em local.settings.jsligado. Utilize esta opção quando pretender publicar um projeto existente num recipiente Linux personalizado. |
| **`--force`** | Inicialize o projeto mesmo quando existam ficheiros existentes no projeto. Esta definição substitui os ficheiros existentes com o mesmo nome. Outros ficheiros na pasta do projeto não são afetados. |
| **`--language`** | Inicializa um projeto específico da linguagem. Atualmente suportado quando `--worker-runtime` definido para `node` . As opções são `typescript` `javascript` e. Também pode usar `--worker-runtime javascript` ou `--worker-runtime typescript` . .  |
| **`--managed-dependencies`**  | Instala dependências geridas. Atualmente, apenas o tempo de funcionamento do trabalhador powerShell suporta esta funcionalidade. |
| **`--source-control`** | Controla se um repositório de git é criado. Por defeito, um repositório não é criado. Quando, `true` um repositório é criado. |
| **`--worker-runtime`** | Define o tempo de execução da linguagem para o projeto. Os valores suportados são: `csharp` `dotnet` , , , `javascript` `node` (JavaScript), `powershell` e `python` `typescript` . Para Java, use [Maven.](functions-reference-java.md#create-java-functions) Quando não estiver definido, é-lhe pedido que escolha o seu tempo de funcionação durante a inicialização. |
|
> [!IMPORTANT]
> Por predefinição, as versões 2.x e posteriores das Ferramentas Core criam projetos de aplicações de função para o tempo de execução .NET como [projetos de classe C#(.csproj).](functions-dotnet-class-library.md) Estes projetos C#, que podem ser usados com Visual Studio ou Visual Studio Code, são compilados durante os testes e na publicação para a Azure. Se, em vez disso, pretender criar e trabalhar com os mesmos ficheiros de script C# (.csx) criados na versão 1.x e no portal, deve incluir o `--csx` parâmetro quando criar e implementar funções.

## <a name="register-extensions"></a>Extensões de registo

Com exceção dos disparadores HTTP e timer, as ligações de funções na versão 2.x e superior são implementadas como pacotes de extensão. As ligações HTTP e os gatilhos do temporizador não requerem extensões. 

Para reduzir as incompatibilidades entre os vários pacotes de extensão, as funções permitem-lhe fazer referência a um pacote de extensão no seu host.jsno ficheiro do projeto. Se optar por não utilizar pacotes de extensão, também precisa de instalar .NET Core 2.x SDK localmente e manter um extensions.csproj com o seu projeto de funções.  

Na versão 2.x e além do tempo de funcionamento das Funções Azure, tem de registar explicitamente as extensões para os tipos de encadernação utilizados nas suas funções. Pode optar por instalar extensões de ligação individualmente, ou pode adicionar uma referência ao pacote de extensão à host.jsno ficheiro do projeto. Os pacotes de extensão eliminam a possibilidade de ter problemas de compatibilidade do pacote quando se utilizam vários tipos de ligação. É a abordagem recomendada para o registo de extensões vinculativas. Os pacotes de extensão também removem a exigência de instalação do .NET Core 2.x SDK. 

### <a name="use-extension-bundles"></a>Use pacotes de extensão

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

Para saber mais, consulte [as extensões de ligação do Registo Azure Functions](functions-bindings-register.md#extension-bundles). Deve adicionar pacotes de extensão ao host.jsantes de adicionar encadernações ao function.jsno ficheiro.

### <a name="explicitly-install-extensions"></a>Instalar explicitamente extensões

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por predefinição, estas definições não são migradas automaticamente quando o projeto é publicado para o Azure. Utilize o `--publish-local-settings` interruptor [quando publicar](#publish) para se certificar de que estas definições são adicionadas à aplicação de função em Azure. Note que os valores em **ConnectionStrings** nunca são publicados.

Os valores de definição de aplicações de função também podem ser lidos no seu código como variáveis ambientais. Para obter mais informações, consulte a secção de variáveis ambientais destes tópicos de referência específicos da linguagem:

* [C# pré-compreitado](functions-dotnet-class-library.md#environment-variables)
* [Script C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

Quando não está definida nenhuma cadeia de ligação de armazenamento válida [`AzureWebJobsStorage`] e o emulador não está a ser utilizado, é mostrada a seguinte mensagem de erro:

> Valor em falta para a AzureWebJobsStorage em local.settings.js. Isto é necessário para todos os gatilhos que não http. Pode executar 'func azure functionapp-app-configurações' ou especificar uma cadeia de \<functionAppName\> ligação em local.settings.jsligado.

### <a name="get-your-storage-connection-strings"></a>Obtenha as cordas de conexão de armazenamento

Mesmo quando utilizar o Emulador de Armazenamento Microsoft Azure para desenvolvimento, é melhor testar com uma ligação de armazenamento real. Assumindo que já [criou uma conta de armazenamento,](../storage/common/storage-account-create.md)pode obter uma cadeia de ligação de armazenamento válida de uma das seguintes formas:

- A partir do [portal Azure,]procure e selecione **contas de Armazenamento.** 
  ![Selecione contas de armazenamento do portal Azure](./media/functions-run-local/select-storage-accounts.png)
  
  Selecione a sua conta de armazenamento, selecione **as teclas de acesso** em **Definições**e, em seguida, copie um dos valores da **cadeia De ligação.**
  ![Cadeia de ligação de cópia do portal Azure](./media/functions-run-local/copy-storage-connection-portal.png)

- Utilize [o Azure Storage Explorer](https://storageexplorer.com/) para ligar à sua conta Azure. No **Explorer,** expanda a sua subscrição, expanda **as Contas de Armazenamento,** selecione a sua conta de armazenamento e copie a cadeia de ligação primária ou secundária.

  ![Cadeia de ligação de cópia do Explorador de Armazenamento](./media/functions-run-local/storage-explorer.png)

+ Utilize ferramentas core da raiz do projeto para descarregar a cadeia de ligação do Azure com um dos seguintes comandos:

  + Descarregue todas as definições de uma aplicação de função existente:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + Obtenha o string Connection para uma conta de armazenamento específica:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Quando ainda não estás inscrito no Azure, és solicitado a fazê-lo. Estes comandos substituem quaisquer definições existentes no local.settings.jsem ficheiro. 

## <a name="create-a-function"></a><a name="create-func"></a>Criar uma função

Para criar uma função, executar o seguinte comando:

```
func new
```

Na versão 3.x/2.x, quando corre `func new` é-lhe pedido que escolha um modelo no idioma predefinido da sua aplicação de função, então também é solicitado que escolha um nome para a sua função. Na versão 1.x, também é solicitado a escolher o idioma.

<pre>
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
</pre>

O código de função é gerado numa sub-dobragem com o nome de função fornecido, como pode ver na seguinte saída do gatilho da fila:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Também pode especificar estas opções no comando utilizando os seguintes argumentos:

| Argumento     | Descrição                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Versão 2.x e versões posteriores.) Gera os mesmos modelos de script C# (.csx) utilizados na versão 1.x e no portal. |
| **`--language`**, **`-l`**| A linguagem de programação do modelo, como C#, F#ou JavaScript. Esta opção é necessária na versão 1.x. Nas versões 2.x e posteriores, não utilize esta opção nem escolha um idioma que corresponda ao tempo de funcionação do trabalhador. |
| **`--name`**, **`-n`** | O nome da função. |
| **`--template`**, **`-t`** | Utilize o `func templates list` comando para ver a lista completa de modelos disponíveis para cada idioma suportado.   |


Por exemplo, para criar um gatilho JAVAScript HTTP num único comando, corra:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Para criar uma função acionada por fila num único comando, corra:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Executar funções localmente

Para executar um projeto De Funções, executar o anfitrião Funções. O hospedeiro permite gatilhos para todas as funções do projeto. O comando inicial varia, dependendo da linguagem do seu projeto.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Este comando deve ser [executado num ambiente virtual](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> A versão 1.x do tempo de execução das funções requer o `host` comando, como no exemplo seguinte:
>
> ```
> func host start
> ```

`func start` suporta as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Não construa o projeto atual antes de funcionar. Apenas para projetos de dotnet. O padrão está definido como falso. Não suportado para a versão 1.x. |
| **`--cors-credentials`** | Permitir pedidos autenticados de origem cruzada (isto é, cookies e o cabeçalho autenticação) Não suportado para a versão 1.x. |
| **`--cors`** | Uma lista separada por vírgulas de origens cors, sem espaços. |
| **`--language-worker`** | Argumentos para configurar o trabalhador da língua. Por exemplo, pode permitir a depuragem para o trabalhador da língua fornecendo [porta de depuragem e outros argumentos necessários](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Não suportado para a versão 1.x. |
| **`--cert`** | O caminho para um ficheiro .pfx que contém uma chave privada. Só utilizado `--useHttps` com. . Não suportado para a versão 1.x. |
| **`--password`** | Ou a palavra-passe ou um ficheiro que contém a palavra-passe para um ficheiro .pfx. Só utilizado `--cert` com. . Não suportado para a versão 1.x. |
| **`--port`**, **`-p`** | O porto local para ouvir. Valor predefinido: 7071. |
| **`--pause-on-error`** | Faça uma pausa para obter uma entrada adicional antes de sair do processo. Utilizado apenas no lançamento de Ferramentas Core a partir de um ambiente de desenvolvimento integrado (IDE).|
| **`--script-root`**, **`--prefix`** | Usado para especificar o caminho para a raiz da aplicação de função que deve ser executado ou implementado. Isto é usado para projetos compilados que geram ficheiros de projeto em uma sub-dobra. Por exemplo, quando se constrói um projeto de biblioteca de classes C', o host.jsligado, local.settings.jse function.jsem ficheiros são gerados numa sub-página *de raiz* com um caminho como `MyProject/bin/Debug/netstandard2.0` . Neste caso, desacorra o prefixo como `--script-root MyProject/bin/Debug/netstandard2.0` . Esta é a raiz da aplicação de função quando está a correr em Azure. |
| **`--timeout`**, **`-t`** | O intervalo para o anfitrião das Funções começar, em segundos. Padrão: 20 segundos.|
| **`--useHttps`** | Ligar-se ao `https://localhost:{port}` invés `http://localhost:{port}` de. Por predefinição, esta opção cria um certificado de confiança no seu computador.|

Quando o anfitrião funções começa, produz o URL das funções acionadas http:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Ao correr localmente, a autorização não é aplicada para pontos finais HTTP. Isto significa que todos os pedidos http locais são tratados como `authLevel = "anonymous"` . Para mais informações, consulte o [artigo de vindca HTTP](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Passar dados de teste para uma função

Para testar as suas funções localmente, [inicia o hoste de Funções](#start) e liga pontos finais no servidor local utilizando pedidos HTTP. O ponto final a que chama depende do tipo de função.

>[!NOTE]
> Exemplos neste tópico utilizam a ferramenta cURL para enviar pedidos HTTP a partir do terminal ou uma solicitação de comando. Pode utilizar uma ferramenta à sua escolha para enviar pedidos HTTP para o servidor local. A ferramenta cURL está disponível por padrão em sistemas baseados no Linux e o Windows 10 constrói 17063 e mais tarde. No Windows mais antigo, tem primeiro de descarregar e instalar a [ferramenta cURL](https://curl.haxx.se/).

Para obter informações mais gerais sobre funções de teste, consulte [Estratégias para testar o seu código em Funções Azure](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>HTTP e webhook desencadearam funções

Você chama o seguinte ponto final para executar localmente HTTP e webhook funções desencadeadas:

```
http://localhost:{port}/api/{function_name}
```

Certifique-se de que utiliza o mesmo nome de servidor e porta que o anfitrião funções está a ouvir. Vê-se isto na saída gerada ao iniciar o anfitrião função. Pode chamar este URL utilizando qualquer método HTTP suportado pelo gatilho.

O seguinte comando cURL ativa a `MyHttpTrigger` função quickstart a partir de um pedido GET com o parâmetro _de nome_ passado na cadeia de consulta.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Segue-se o exemplo seguinte da mesma função chamada de um _nome_ de passagem de pedido do CORREIO no organismo de pedido:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Pode fazer pedidos GET a partir de um navegador que passa dados na cadeia de consulta. Para todos os outros métodos HTTP, deve utilizar cURL, Violinista, Carteiro ou uma ferramenta de teste HTTP semelhante.

#### <a name="non-http-triggered-functions"></a>Funções desencadeadas não-HTTP

Para todos os tipos de funções que não sejam os detonadores HTTP e webhooks e detonadores de Grade de Eventos, pode testar as suas funções localmente, chamando um ponto final de administração. Ligar para este ponto final com um pedido HTTP POST no servidor local despoleta a função. 

Para testar as funções ativadas pela Grade de Eventos localmente, consulte [testes locais com aplicação web do espectador.](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app)

Pode opcionalmente passar dados de teste para a execução no corpo do pedido DOM. Esta funcionalidade é semelhante ao separador **Teste** no portal Azure.

Ligue para o seguinte ponto final do administrador para desencadear funções não-HTTP:

```
http://localhost:{port}/admin/functions/{function_name}
```

Para passar os dados de teste ao ponto final de uma função, deve fornecer os dados no corpo de uma mensagem de pedido DO POST. O corpo da mensagem é obrigado a ter o seguinte formato JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

O `<trigger_input>` valor contém dados num formato esperado pela função. O exemplo cURL a seguir é um POST para uma `QueueTriggerJS` função. Neste caso, a entrada é uma cadeia que é equivalente à mensagem que se espera que seja encontrada na fila.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Utilizando o `func run` comando (apenas versão 1.x)

>[!IMPORTANT]
> O `func run` comando só é suportado na versão 1.x das ferramentas. Para obter mais informações, consulte o tópico [Como direcionar as versões de tempo de execução do Azure Functions](set-runtime-version.md).

Na versão 1.x, também pode invocar uma função diretamente utilizando `func run <FunctionName>` e fornecendo dados de entrada para a função. Este comando é semelhante ao funcionamento de uma função utilizando o **separador Teste** no portal Azure.

`func run` suporta as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Conteúdo em linha. |
| **`--debug`**, **`-d`** | Prenda um depurar ao processo de hospedeiro antes de executar a função.|
| **`--timeout`**, **`-t`** | Tempo para esperar (em segundos) até que o anfitrião local das funções esteja pronto.|
| **`--file`**, **`-f`** | O nome do ficheiro para usar como conteúdo.|
| **`--no-interactive`** | Não solicita a entrada. Útil para cenários de automação.|

Por exemplo, para chamar uma função desencadeada por HTTP e passar o corpo do conteúdo, executar o seguinte comando:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Publicar no Azure

As Ferramentas Principais de Funções Azure suportam dois tipos de implementação: implantar ficheiros de projetos de função diretamente para a sua aplicação de função através do [Zip Deploy](functions-deployment-technologies.md#zip-deploy) e [implantar um recipiente Docker personalizado](functions-deployment-technologies.md#docker-container). Já deve ter [criado uma aplicação de função na sua subscrição Azure,](functions-cli-samples.md#create)para a qual irá implementar o seu código. Os projetos que exigem a compilação devem ser construídos para que os binários possam ser implantados.

>[!IMPORTANT]
>Você deve ter o [Azure CLI](/cli/azure/install-azure-cli) instalado localmente para ser capaz de publicar para Azure a partir de Core Tools.  

Uma pasta de projeto pode conter ficheiros e diretórios específicos da linguagem que não devem ser publicados. Os itens excluídos estão listados num ficheiro .funcignore na pasta do projeto raiz.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Implementar ficheiros de projeto

Para publicar o seu código local numa aplicação de função em Azure, utilize o `publish` comando:

```
func azure functionapp publish <FunctionAppName>
```

>[!IMPORTANT]
> Java usa Maven para publicar o seu projeto local para Azure. Utilize o seguinte comando para publicar no Azure: `mvn azure-functions:deploy` . Os recursos Azure são criados durante a implantação inicial.

Este comando publica a uma aplicação de função existente em Azure. Terá um erro se tentar publicar para um `<FunctionAppName>` que não existe na sua subscrição. Para aprender a criar uma aplicação de função a partir da solicitação de comando ou janela do terminal utilizando o Azure CLI, consulte [Criar uma App de função para execução sem servidor](./scripts/functions-cli-create-serverless.md). Por predefinição, este comando utiliza [a construção remota](functions-deployment-technologies.md#remote-build) e implementa a sua aplicação para [executar a partir do pacote de implementação.](run-functions-from-deployment-package.md) Para desativar este modo de implementação recomendado, utilize a `--nozip` opção.

>[!IMPORTANT]
> Quando cria uma aplicação de função no portal Azure, utiliza a versão 3.x do tempo de execução da Função por predefinição. Para fazer com que a aplicação de função utilize a versão 1.x do tempo de execução, siga as instruções em [Executar na versão 1.x](functions-versions.md#creating-1x-apps).
> Não é possível alterar a versão de tempo de execução para uma aplicação de função que tenha funções existentes.

As seguintes opções de publicação aplicam-se a todas as versões:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publique as definições em local.settings.jspara o Azure, levando a substituir se a definição já existe. Se estiver a utilizar o Emulador de Armazenamento Microsoft Azure, altere primeiro a definição da aplicação para uma [ligação de armazenamento real](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Suprimir o pedido para substituir as definições das aplicações quando `--publish-local-settings -i` for utilizado.|

As seguintes opções de publicação são suportadas apenas para versões 2.x e posteriores:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Publique apenas as definições e ignore o conteúdo. O padrão é rápido. |
|**`--list-ignored-files`** | Apresenta uma lista de ficheiros que são ignorados durante a publicação, que se baseia no ficheiro .funcignore. |
| **`--list-included-files`** | Apresenta uma lista de ficheiros que são publicados, que se baseia no ficheiro .funcignore. |
| **`--nozip`** | Desliga o `Run-From-Package` modo predefinido. |
| **`--build-native-deps`** | Salta a geração de pasta .wheels ao publicar aplicações de função Python. |
| **`--build`**, **`-b`** | Executa a ação de construção ao implementar para uma aplicação de função Linux. Aceita: `remote` e `local` . |
| **`--additional-packages`** | Lista de pacotes para instalar quando se constrói dependências nativas. Por exemplo: `python3-dev libevent-dev`. |
| **`--force`** | Ignore a verificação de pré-publicação em certos cenários. |
| **`--csx`** | Publique um projeto de script C# (.csx). |
| **`--no-build`** | O projeto não foi construído durante a publicação. Para Python, `pip install` não é realizado. |
| **`--dotnet-cli-params`** | Ao publicar funções compiladas C# (.csproj), as ferramentas centrais chamam "dotnet build --output bin/publish". Quaisquer parâmetros passados a isto serão anexados à linha de comando. |

### <a name="deploy-custom-container"></a>Implementar recipiente personalizado

As Funções Azure permitem-lhe implantar o seu projeto de função num [recipiente personalizado do Docker](functions-deployment-technologies.md#docker-container). Para obter mais informações, consulte [Criar uma função no Linux utilizando uma imagem personalizada](functions-create-function-linux-custom-image.md). Os recipientes personalizados devem ter um Dockerfile. Para criar uma aplicação com um Dockerfile, utilize a opção --estivador em `func init` .

```
func deploy
```

Estão disponíveis as seguintes opções de implantação de contentores personalizados:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--registry`** | O nome de um Registo Docker que o atual utilizador inscreveu. |
| **`--platform`** | Plataforma de hospedagem para a aplicação de função. As opções válidas são `kubernetes` |
| **`--name`** | Nome de aplicativo de função. |
| **`--max`**  | Opcionalmente, define o número máximo de instâncias de aplicação de função para implementar. |
| **`--min`**  | Opcionalmente, define o número mínimo de instâncias de aplicação de função para implementar. |
| **`--config`** | Define um ficheiro de configuração de implementação opcional. |

## <a name="monitoring-functions"></a>Funções de monitorização

A forma recomendada de monitorizar a execução das suas funções é integrando-se com a Azure Application Insights. Também pode transmitir registos de execução para o seu computador local. Para saber mais, consulte [as Funções Do Monitor Azure](functions-monitoring.md).

### <a name="application-insights-integration"></a>Integração de Insights de Aplicação

A integração do Application Insights deve ser ativada quando criar a sua aplicação de função no Azure. Se por alguma razão a sua aplicação de função não estiver ligada a uma instância de Insights de Aplicação, é fácil fazer esta integração no portal Azure. Para saber mais, consulte [a integração de Enable Application Insights](configure-monitoring.md#enable-application-insights-integration).

### <a name="enable-streaming-logs"></a>Ativar registos de streaming

Pode ver um fluxo de ficheiros de registo gerado pelas suas funções numa sessão de linha de comando no computador local. 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Este tipo de registos de streaming requer que a integração do Application Insights seja ativada para a sua aplicação de função.   


## <a name="next-steps"></a>Passos seguintes

Aprenda a desenvolver, testar e publicar Funções Azure utilizando as ferramentas principais do Azure Functions [A Microsoft learn module](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools é open source e hospedado no [GitHub](https://github.com/azure/azure-functions-cli).  
Para arquivar um pedido de bug ou recurso, [abra um problema do GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal do Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
['AzureWebJobsStorage']: functions-app-settings.md#azurewebjobsstorage
[pacotes de extensão]: functions-bindings-register.md#extension-bundles