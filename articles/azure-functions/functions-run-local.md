---
title: Trabalhar com ferramentas centrais de funções azure
description: Aprenda a codificar e testar as funções azure a partir do pedido de comando ou terminal no seu computador local antes de executá-las em Funções Azure.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 559c9ee237f95f9d175aaefb487131466700e899
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190794"
---
# <a name="work-with-azure-functions-core-tools"></a>Trabalhar com ferramentas centrais de funções azure

Funções Azure As ferramentas nucleares permitem desenvolver e testar as suas funções no computador local a partir do pedido de comando ou terminal. As suas funções locais podem ligar-se aos serviços Azure ao vivo e pode desincomodá-lo das suas funções no computador local utilizando o tempo de funcionamento completo das Funções. Pode até implementar uma aplicação de função para a sua subscrição Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Desenvolver funções no seu computador local e publicá-las no Azure utilizando ferramentas core segue estes passos básicos:

> [!div class="checklist"]
> * [Instale as Ferramentas e dependências do Núcleo.](#v2)
> * [Crie um projeto de aplicação de função a partir de um modelo específico de linguagem.](#create-a-local-functions-project)
> * [Registe o gatilho e as extensões de encadernação.](#register-extensions)
> * [Defina o Armazenamento e outras ligações.](#local-settings-file)
> * [Crie uma função a partir de um gatilho e modelo específico da linguagem.](#create-func)
> * [Executar a função localmente.](#start)
> * [Publique o projeto para o Azure.](#publish)

## <a name="core-tools-versions"></a>Versões Core Tools

Existem três versões de Ferramentas Nucleares de Funções Azure. A versão que utiliza depende do seu ambiente de desenvolvimento local, [escolha da linguagem,](supported-languages.md)e nível de suporte necessário:

+ **Versão 1.x:** Suporta a versão 1.x do tempo de funcionamento das Funções Azure. Esta versão das ferramentas só é suportada em computadores Windows e está instalada a partir de um [pacote npm](https://www.npmjs.com/package/azure-functions-core-tools).

+ [**Versão 2.x/3.x**](#v2): Suporta a [versão 2.x ou 3.x do tempo de funcionamento das Funções Azure](functions-versions.md). Estas versões suportam [Windows](/azure/azure-functions/functions-run-local?tabs=windows#v2), [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)e [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) e utilizam gestores de pacotes específicos da plataforma ou npm para instalação.

Salvo indicação em contrário, os exemplos deste artigo são para a versão 3.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalar as Ferramentas de Núcleo de Funções do Azure

[Ferramentas centrais de funções azure] incluem uma versão do mesmo tempo de funcionamento que alimenta o tempo de funcionamento das Funções Azure que pode executar no seu computador de desenvolvimento local. Também fornece comandos para criar funções, ligar ao Azure e implementar projetos de função.

>[!IMPORTANT]
>Deve ter o [Azure CLI](/cli/azure/install-azure-cli) instalado localmente para poder publicar no Azure a partir de Ferramentas Nucleares de Funções Azure.  

### <a name="v2"></a>Versão 2.x e 3.x

A versão 2.x/3.x das ferramentas utiliza o tempo de funcionamento das Funções Azure que é construído em .NET Core. Esta versão é suportada em todas as plataformas .NET Core suportes, incluindo [Windows](/azure/azure-functions/functions-run-local?tabs=windows#v2), [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)e [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2). 

> [!IMPORTANT]
> Pode contornar a exigência de instalação do .NET Core SDK utilizando pacotes de [extensão].

# <a name="windows"></a>[Windows](#tab/windows)

Os seguintes passos utilizam a NPM para instalar as Ferramentas Core no Windows. Também pode usar [Chocolatey.](https://chocolatey.org/) Para mais informações, consulte a leitura das [Ferramentas Core.](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)

1. Instale [Node.js]que inclui o npm.
    - Para a versão 2.x das ferramentas, apenas são suportadas versões Node.js 8.5 e posteriores.
    - Para a versão 3.x das ferramentas, apenas são suportadas versões Nó.js 10 e posteriores.

1. Instale o pacote Core Tools:

    ##### <a name="v2x"></a>v2.x

    ```bash
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```bash
    npm install -g azure-functions-core-tools@3
    ```

   Pode levar alguns minutos para a NPM descarregar e instalar o pacote Core Tools.

1. Se não pretender utilizar [extensão]instale o [.NET Core 2.x SDK para windows](https://www.microsoft.com/net/download/windows).

# <a name="macos"></a>[MacOS](#tab/macos)

Os seguintes passos utilizam o Homebrew para instalar as Ferramentas Core no macOS.

1. Instale [Homebrew,](https://brew.sh/)se ainda não estiver instalado.

1. Instale o pacote Core Tools:

    ##### <a name="v2x"></a>v2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="linux"></a>[Linux](#tab/linux)

Os seguintes passos utilizam a [APT](https://wiki.debian.org/Apt) para instalar as Ferramentas Core na distribuição Ubuntu/Debian Linux. Para outras distribuições linux, consulte o [Core Tools readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Instale a chave GPG do pacote da Microsoft para validar a integridade do pacote:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Configurar a lista de fontes de desenvolvimento .NET antes de fazer uma atualização APT.

   Para configurar a lista de fonte apt para Ubuntu, executar este comando:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Para configurar a lista de fonte salébse para debian, execute este comando:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Verifique o ficheiro `/etc/apt/sources.list.d/dotnetdev.list` para obter uma das cadeias de versão Linux apropriadas listadas abaixo:

    | Distribuição do Linux | Versão |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Inicie a atualização de fonte apt:

    ```bash
    sudo apt-get update
    ```

1. Instale o pacote Core Tools:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Se não pretender utilizar [extensão]instale [.NET Core 2.x SDK para Linux](https://www.microsoft.com/net/download/linux).

---

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Um diretório de projetos de funções contém os [ficheiros host.json](functions-host-json.md) e [local.settings.json,](#local-settings-file)juntamente com subpastas que contêm o código para funções individuais. Este diretório é o equivalente a uma aplicação de função em Azure. Para saber mais sobre a estrutura da pasta Functions, consulte o guia de desenvolvedores de [Funções Azure](functions-reference.md#folder-structure).

A versão 2.x requer que selecione um idioma predefinido para o seu projeto quando este é inicializado. Na versão 2.x, todas as funções adicionam modelos de idioma padrão. Na versão 1.x, especifica o idioma cada vez que cria uma função.

Na janela do terminal ou a partir de um pedido de comando, execute o seguinte comando para criar o projeto e repositório git local:

```bash
func init MyFunctionProj
```

Quando fornece um nome de projeto, uma nova pasta com esse nome é criada e inicializada. Caso contrário, a pasta atual é inicializada.  
Na versão 2.x, quando executa o comando, tem de escolher um tempo de execução para o seu projeto. 

```output
Select a worker runtime:
dotnet
node
python 
powershell
```

Utilize as teclas de seta para cima/para baixo para escolher um idioma e, em seguida, prima Enter. Se planeia desenvolver funções JavaScript ou TypeScript, escolha **o nó**e selecione o idioma. TypeScript tem [alguns requisitos adicionais](functions-reference-node.md#typescript). 

A saída parece ser o seguinte exemplo para um projeto JavaScript:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` suporta as seguintes opções, que são apenas a versão 2.x, salvo indicação em contrário:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Inicializa um [ C# projeto de biblioteca de classes (.cs).](functions-dotnet-class-library.md) |
| **`--csx`** | Inicializa um [ C# projeto de script (.csx).](functions-reference-csharp.md) Deve especificar `--csx` em comandos posteriores. |
| **`--docker`** | Crie um Dockerfile para um recipiente utilizando uma imagem base baseada no `--worker-runtime`escolhido . Use esta opção quando planeia publicar num recipiente Linux personalizado. |
| **`--docker-only`** |  Adiciona um Dockerfile a um projeto existente. Solicitações para o tempo de execução do trabalhador se não especificadas ou definidas em local.settings.json. Use esta opção quando planeia publicar um projeto existente num recipiente Linux personalizado. |
| **`--force`** | Inicializar o projeto mesmo quando existem ficheiros existentes no projeto. Esta definição substitui os ficheiros existentes com o mesmo nome. Outros ficheiros na pasta do projeto não são afetados. |
| **`--java`**  | Inicializa um [projeto Java.](functions-reference-java.md) |
| **`--javascript`**<br/>**`--node`**  | Inicializa um [projeto JavaScript](functions-reference-node.md). |
| **`--no-source-control`**<br/>**`-n`** | Impede a criação padrão de um repositório Git na versão 1.x. Na versão 2.x, o repositório git não é criado por padrão. |
| **`--powershell`**  | Inicializa um [projeto PowerShell.](functions-reference-powershell.md) |
| **`--python`**  | Inicializa um [projeto Python.](functions-reference-python.md) |
| **`--source-control`** | Controla se é criado um repositório git. Por defeito, não é criado um repositório. Quando `true`, cria-se um repositório. |
| **`--typescript`**  | Inicializa um [projeto TypeScript](functions-reference-node.md#typescript). |
| **`--worker-runtime`** | Define o tempo de execução da linguagem para o projeto. Os valores suportados são: `csharp`, `dotnet`, `java`, `javascript`,`node` (JavaScript), `powershell`, `python`e `typescript`. Quando não estiver definido, é-lhe pedido que escolha o seu tempo de execução durante a inicialização. |

> [!IMPORTANT]
> Por padrão, a versão 2.x das Ferramentas Core cria projetos de aplicações de função para o tempo de execução .NET como [ C# projetos](functions-dotnet-class-library.md) de classe (.csproj). Estes C# projetos, que podem ser utilizados com visual studio ou visual studio code, são compilados durante os testes e na publicação para o Azure. Se, em vez disso, pretender C# criar e trabalhar com os mesmos ficheiros script (.csx) criados na versão 1.x e no portal, deve incluir o parâmetro `--csx` quando criar e implementar funções.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por predefinição, estas definições não são migradas automaticamente quando o projeto é publicado para o Azure. Utilize o interruptor `--publish-local-settings` [quando publicar](#publish) para se certificar de que estas definições são adicionadas à aplicação de funções no Azure. Note que os valores em **ConnectionStrings** nunca são publicados.

Os valores de definições da aplicação de função também podem ser lidos no seu código como variáveis ambientais. Para mais informações, consulte a secção de variáveis do Ambiente destes tópicos de referência específicos da linguagem:

* [C#pré-compilado](functions-dotnet-class-library.md#environment-variables)
* [C#script (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

Quando não for definida uma cadeia de ligação de armazenamento válida para [`AzureWebJobsStorage`] e o emulador não estiver a ser utilizado, é mostrada a seguinte mensagem de erro:

> Valor em falta para AzureWebJobsStorage em local.settings.json. Isto é necessário para todos os gatilhos que não o HTTP. Pode executar 'func azure functionapp fetch-app-settings \<functionAppName\>' ou especificar uma cadeia de ligação em local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Obtenha as suas cordas de ligação de armazenamento

Mesmo quando utilizar o Emulador de Armazenamento Microsoft Azure para desenvolvimento, pode querer testar com uma ligação de armazenamento real. Assumindo que já criou uma conta de [armazenamento,](../storage/common/storage-create-storage-account.md)pode obter uma cadeia de conexão de armazenamento válida de uma das seguintes formas:

- A partir do [Portal do Azure]procure e selecione contas de **Armazenamento.** 
  ![Selecionar contas de armazenamento do portal Azure](./media/functions-run-local/select-storage-accounts.png)
  
  Selecione a sua conta de armazenamento, selecione **teclas de acesso** em **Definições**e, em seguida, copie um dos valores de **cadeia de ligação.**
  ![copiar fio de conexão do portal Azure](./media/functions-run-local/copy-storage-connection-portal.png)

- Utilize o [Azure Storage Explorer](https://storageexplorer.com/) para se ligar à sua conta Azure. No **Explorer,** expanda a sua subscrição, expanda **as Contas de Armazenamento,** selecione a sua conta de armazenamento e copie a cadeia de ligação primária ou secundária.

  ![Cadeia de ligação de cópia do Explorador de Armazenamento](./media/functions-run-local/storage-explorer.png)

+ Utilize as Ferramentas Core para descarregar a cadeia de ligação de Azure com um dos seguintes comandos:

  + Descarregue todas as definições de uma aplicação de função existente:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Obtenha a cadeia de ligação para uma conta de armazenamento específica:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Quando ainda não estás inscrito no Azure, és instado a fazê-lo.

## <a name="create-func"></a>Criar uma função

Para criar uma função, execute o seguinte comando:

```bash
func new
```

Na versão 2.x, quando executa `func new` é-lhe solicitado que escolha um modelo no idioma padrão da sua aplicação de função, então também é solicitado a escolher um nome para a sua função. Na versão 1.x, também é solicitado a escolher o idioma.

```output
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
```

O código de função é gerado numa subpasta com o nome de função fornecido, como pode ver na saída do gatilho da fila seguinte:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Também pode especificar estas opções no comando utilizando os seguintes argumentos:

| Argumento     | Descrição                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Versão 2.x) Gera os C# mesmos modelos de script (.csx) usados na versão 1.x e no portal. |
| **`--language`,**  **`-l`**| A linguagem de programação C# F#do modelo, como, ou JavaScript. Esta opção é necessária na versão 1.x. Na versão 2.x, não utilize esta opção nem escolha um idioma que corresponda ao tempo de execução do trabalhador. |
| **`--name`,**  **`-n`** | O nome da função. |
| **`--template`,**  **`-t`** | Utilize o comando `func templates list` para ver a lista completa dos modelos disponíveis para cada idioma suportado.   |

Por exemplo, para criar um gatilho JavaScript HTTP num único comando, executar:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Para criar uma função desencadeada pela fila num único comando, corra:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Executar funções localmente

Para executar um projeto Funções, executar o anfitrião funções. O hospedeiro permite gatilhos para todas as funções do projeto. 

### <a name="version-2x"></a>Versão 2.x

Na versão 2.x do tempo de execução, o comando inicial varia, dependendo da linguagem do seu projeto.

#### <a name="c"></a>C\#

```command
func start --build
```

#### <a name="javascript"></a>JavaScript

```command
func start
```

#### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

### <a name="version-1x"></a>Versão 1.x

A versão 1.x das funções requer o comando `host`, como no seguinte exemplo:

```command
func host start
```

`func start` suporta as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Não construa o projeto atual antes de executar. Apenas para projetos de dotnet. O padrão está definido para falso. Versão 2.x apenas. |
| **`--cert`** | O caminho para um ficheiro .pfx que contém uma chave privada. Usado apenas com `--useHttps`. Versão 2.x apenas. |
| **`--cors-credentials`** | Permitir pedidos autenticados de origem cruzada (isto é, cookies e o cabeçalho de autenticação) versão 2.x apenas. |
| **`--cors`** | Uma lista separada de vírinas de origens cors, sem espaços. |
| **`--language-worker`** | Argumentos para configurar o trabalhador da linguagem. Por exemplo, pode permitir a depuração para o trabalhador linguístico fornecendo porta de [depuração e outros argumentos necessários](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Versão 2.x apenas. |
| **`--nodeDebugPort`,**  **`-n`** | A porta para o debugger node.js usar. Predefinição: Um valor do lançamento.json ou 5858. Versão 1.x apenas. |
| **`--password`** | Ou a palavra-passe ou um ficheiro que contém a palavra-passe para um ficheiro .pfx. Usado apenas com `--cert`. Versão 2.x apenas. |
| **`--port`,**  **`-p`** | O porto local para ouvir. Valor predefinido: 7071. |
| **`--pause-on-error`** | Faça uma pausa para obter uma entrada adicional antes de sair do processo. Utilizado apenas quando lança utensílios core a partir de um ambiente de desenvolvimento integrado (IDE).|
| **`--script-root`,**  **`--prefix`** | Usado para especificar o caminho para a raiz da aplicação de função que deve ser executado ou implementado. Isto é usado para projetos compilados que geram ficheiros de projeto numa subpasta. Por exemplo, quando C# se constrói um projeto de biblioteca de classes, o host.json, local.settings.json e os ficheiros function.json são gerados numa subpasta *raiz* com um caminho como `MyProject/bin/Debug/netstandard2.0`. Neste caso, detete o prefixo como `--script-root MyProject/bin/Debug/netstandard2.0`. Esta é a raiz da aplicação de funções quando se está a funcionar em Azure. |
| **`--timeout`,**  **`-t`** | O tempo de tempo para o hospedeiro funções começar, em segundos. Predefinição: 20 segundos.|
| **`--useHttps`** | Ligue-se a `https://localhost:{port}` em vez de `http://localhost:{port}`. Por padrão, esta opção cria um certificado fidedigno no seu computador.|

Quando o hospedeiro funções começa, produz o URL das funções desencadeadas pelo HTTP:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>Quando se corre localmente, a autorização não é executada para pontos finais HTTP. Isto significa que todos os pedidos de HTTP locais são tratados como `authLevel = "anonymous"`. Para mais informações, consulte o [artigo vinculativo](functions-bindings-http-webhook-trigger.md#authorization-keys)http .

### <a name="passing-test-data-to-a-function"></a>Passar dados de teste para uma função

Para testar as suas funções localmente, [inicie o anfitrião das Funções](#start) e ligue pontos finais no servidor local utilizando pedidos HTTP. O ponto final a que chama depende do tipo de função.

>[!NOTE]
> Exemplos neste tópico utilizam a ferramenta cURL para enviar pedidos HTTP do terminal ou de um pedido de comando. Pode utilizar uma ferramenta à sua escolha para enviar pedidos HTTP para o servidor local. A ferramenta cURL está disponível por padrão em sistemas baseados em Linux e o Windows 10 build 17063 e posteriormente. No Windows mais antigo, tem primeiro de descarregar e instalar a [ferramenta cURL](https://curl.haxx.se/).

Para obter informações mais gerais sobre as funções de teste, consulte [Estratégias para testar o seu código em Funções Azure](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>HTTP e webhook desencadearam funções

Ligue para o seguinte ponto final para executar localmente http e funções ativadas webhook:

    http://localhost:{port}/api/{function_name}

Certifique-se de que utiliza o mesmo nome de servidor e porta que o anfitrião funções está a ouvir. Vê-se isto na saída gerada ao iniciar o hospedeiro função. Pode chamar este URL utilizando qualquer método HTTP suportado pelo gatilho.

O comando cURL seguinte aciona a função `MyHttpTrigger` quickstart a partir de um pedido GET com o parâmetro de _nome_ passado na corda de consulta.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

O exemplo seguinte é a mesma função chamada de um pedido post a passar _nome_ no organismo de pedido:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Pode fazer pedidos GET a partir de um navegador que passa dados na cadeia de consulta. Para todos os outros métodos HTTP, deve utilizar cURL, Violinista, Carteiro ou uma ferramenta de teste HTTP semelhante.

#### <a name="non-http-triggered-functions"></a>Funções não-HTTP desencadeadas

Para todos os tipos de funções que não sejam gatilhos HTTP e webhooks, pode testar as suas funções localmente, chamando um ponto final da administração. Ligar para este ponto final com um pedido HTTP POST no servidor local despoleta a função. Pode, opcionalmente, passar os dados do teste para a execução no corpo do pedido do POST. Esta funcionalidade é semelhante ao separador **Test** no portal Azure.

Ligue para o seguinte ponto final do administrador para desencadear funções não HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Para passar os dados do teste ao ponto final do administrador de uma função, deve fornecer os dados no corpo de uma mensagem de pedido POST. O corpo da mensagem é necessário ter o seguinte formato JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

O valor `<trigger_input>` contém dados num formato esperado pela função. O exemplo cURL que se segue é um POST para uma função `QueueTriggerJS`. Neste caso, a entrada é uma corda equivalente à mensagem que se espera que seja encontrada na fila.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Usando o comando `func run` na versão 1.x

>[!IMPORTANT]
> O comando `func run` não é suportado na versão 2.x das ferramentas. Para mais informações, consulte o tópico Como direcionar as versões de execução das [Funções Azure.](set-runtime-version.md)

Também pode invocar uma função diretamente utilizando `func run <FunctionName>` e fornecer dados de entrada para a função. Este comando é semelhante ao funcionamento de uma função utilizando o separador **Test** no portal Azure.

`func run` suporta as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--content`,**  **`-c`** | Conteúdo inline. |
| **`--debug`,**  **`-d`** | Prenda um desbugger ao processo de anfitrião antes de executar a função.|
| **`--timeout`,**  **`-t`** | Tempo para esperar (em segundos) até que o anfitrião das Funções locais esteja pronto.|
| **`--file`,**  **`-f`** | O nome do ficheiro para usar como conteúdo.|
| **`--no-interactive`** | Não solicita a entrada. Útil para cenários de automação.|

Por exemplo, para ligar para uma função desencadeada pelo HTTP e passar o corpo de conteúdo, executar o seguinte comando:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publicar no Azure

As Ferramentas Core funções Do Azure suportam dois tipos de implementação: implementar ficheiros de projeto de função diretamente para a sua aplicação de funções através de [Zip Deploy](functions-deployment-technologies.md#zip-deploy) e [implementar um recipiente Docker personalizado](functions-deployment-technologies.md#docker-container). Já deve ter criado uma aplicação de [função na sua subscrição Azure](functions-cli-samples.md#create), para a qual irá implementar o seu código. Os projetos que exijam a compilação devem ser construídos para que os binários possam ser implantados.

>[!IMPORTANT]
>Deve ter o [Azure CLI](/cli/azure/install-azure-cli) instalado localmente para poder publicar no Azure a partir de Core Tools.  

Uma pasta do projeto pode conter ficheiros e diretórios específicos da linguagem que não devem ser publicados. Os itens excluídos estão listados num ficheiro .funcignore na pasta do projeto raiz.     

### <a name="project-file-deployment"></a>Implementar ficheiros de projeto

Para publicar o seu código local numa aplicação de função em Azure, utilize o comando `publish`:

```bash
func azure functionapp publish <FunctionAppName>
```

Este comando publica para uma aplicação de função existente em Azure. Terá um erro se tentar publicar para um `<FunctionAppName>` que não existe na sua subscrição. Para aprender a criar uma aplicação de função a partir do pedido de comando ou janela terminal utilizando o Azure CLI, consulte Criar uma Aplicação de [Função para execução sem servidores](./scripts/functions-cli-create-serverless.md). Por padrão, este comando utiliza [a construção remota](functions-deployment-technologies.md#remote-build) e implementa a sua aplicação para [executar a partir do pacote de implementação](run-functions-from-deployment-package.md). Para desativar este modo de implementação recomendado, utilize a opção `--nozip`.

>[!IMPORTANT]
> Quando cria uma aplicação de função no portal Azure, utiliza a versão 2.x do tempo de funcionamento da Função por padrão. Para que a aplicação de função utilize a versão 1.x do tempo de execução, siga as instruções em [Executar na versão 1.x](functions-versions.md#creating-1x-apps).
> Não é possível alterar a versão de tempo de execução para uma aplicação de função que tem funções existentes.

As seguintes opções de publicação aplicam-se para ambas as versões, 1.x e 2.x:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publique as definições em local.settings.json para Azure, levando a sobrepor se a definição já existe. Se estiver a utilizar o Emulador de Armazenamento Microsoft Azure, altere primeiro a definição da aplicação para uma [ligação de armazenamento real](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Suprimir a solicitação de sobrepor as definições da aplicação quando `--publish-local-settings -i` for utilizado.|

As seguintes opções de publicação são suportadas apenas na versão 2.x:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`,**  **`-o`** |  Só publique as definições e ignore o conteúdo. O padrão é rápido. |
|**`--list-ignored-files`** | Apresenta uma lista de ficheiros que são ignorados durante a publicação, que é baseado no ficheiro .funcignore. |
| **`--list-included-files`** | Apresenta uma lista de ficheiros que são publicados, que é baseado no ficheiro .funcignore. |
| **`--nozip`** | Desliga o modo `Run-From-Package` padrão. |
| **`--build-native-deps`** | Ignora a geração de uma pasta .wheels ao publicar aplicações de função Python. |
| **`--build`,**  **`-b`** | Executa ação de construção ao implantar para uma aplicação de função Linux. Aceita: `remote` e `local`. |
| **`--additional-packages`** | Lista de pacotes para instalar ao construir dependências nativas. Por exemplo: `python3-dev libevent-dev`. |
| **`--force`** | Ignore a verificação pré-publicação em determinados cenários. |
| **`--csx`** | Publique C# um projeto de script (.csx). |
| **`--no-build`** | Não construa funções de biblioteca de classes .NET. |
| **`--dotnet-cli-params`** | Ao publicar funções compiladas C# (.csproj), as ferramentas principais chamam de "dotnet build --output bin/publish". Quaisquer parâmetros passados a isto serão anexados à linha de comando. |

### <a name="deploy-custom-container"></a>Implementar recipiente personalizado

As Funções Azure permitem-lhe implementar o seu projeto de função num [recipiente personalizado do Docker](functions-deployment-technologies.md#docker-container). Para mais informações, consulte [Criar uma função no Linux utilizando uma imagem personalizada](functions-create-function-linux-custom-image.md). Os recipientes personalizados devem ter um Dockerfile. Para criar uma aplicação com um Dockerfile, utilize a opção --dockerfile no `func init`.

```bash
func deploy
```

Estão disponíveis as seguintes opções de implementação de contentores personalizados:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--registry`** | O nome de um Registo de Docker ao que o utilizador atual inscreveu. |
| **`--platform`** | Plataforma de hospedagem para a aplicação de funções. Opções válidas são `kubernetes` |
| **`--name`** | Nome da aplicação de função. |
| **`--max`**  | Opcionalmente, define o número máximo de instâncias de aplicações de função para implementar. |
| **`--min`**  | Opcionalmente, define o número mínimo de instâncias de aplicações de função para implementar. |
| **`--config`** | Define um ficheiro de configuração de implementação opcional. |

## <a name="monitoring-functions"></a>Funções de monitorização

A forma recomendada de monitorizar a execução das suas funções é integrando-se com insights de aplicação azure. Também pode transmitir registos de execução para o seu computador local. Para saber mais, consulte as [Funções Monitor Azure](functions-monitoring.md).

### <a name="application-insights-integration"></a>Integração de Insights de Aplicação

A integração de Insights de Aplicação deve ser ativada quando criar a sua aplicação de funções no Azure. Se por alguma razão a sua aplicação de funções não estiver ligada a um caso de Insights de Aplicação, é fácil fazer esta integração no portal Azure. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Ativar registos de streaming

Pode ver um fluxo de ficheiros de registo gerados pelas suas funções numa sessão de linha de comando no seu computador local. 

#### <a name="native-streaming-logs"></a>Registos de streaming nativos

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Este tipo de registos de streaming requer que a integração de Insights de Aplicação seja ativada para a sua aplicação de função.   


## <a name="next-steps"></a>Passos seguintes

Saiba como desenvolver, testar e publicar funções Azure utilizando ferramentas nucleares do Azure A [Microsoft aprende](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) o módulo Funções Core Tools é de código aberto [e hospedada no GitHub](https://github.com/azure/azure-functions-cli).  
Para arquivar um pedido de bug ou funcionalidade, [abra um problema GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Ferramentas centrais de funções azure]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal do Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
['AzureWebJobsStorage']: functions-app-settings.md#azurewebjobsstorage
[extensão]: functions-bindings-register.md#extension-bundles
