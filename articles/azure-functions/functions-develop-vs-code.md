---
title: Desenvolva Azure Functions usando Visual Studio Code
description: Saiba como desenvolver e testar Azure Functions usando a extensão Azure Functions para Visual Studio Code.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: f9ad40cd50863990b9af629c77321195dce5e97c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227057"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Desenvolva Azure Functions usando Visual Studio Code

A [Extensão das Funções do Azure para o Visual Studio Code] permite que você desenvolva localmente funções e as implante no Azure. Se essa experiência for sua primeira com Azure Functions, você poderá aprender mais em [uma introdução ao Azure Functions](functions-overview.md).

A extensão Azure Functions fornece estes benefícios:

* Edite, crie e execute funções em seu computador de desenvolvimento local.
* Publique seu projeto de Azure Functions diretamente no Azure.
* Escreva suas funções em várias linguagens, aproveitando os benefícios de Visual Studio Code.

A extensão pode ser usada com os seguintes idiomas, que têm suporte do tempo de execução do Azure Functions versão 2. x:

* [C#Compile](functions-dotnet-class-library.md)
* <sup>*de</sup> script [ C# ](functions-reference-csharp.md)
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup> Requer que você [defina C# script como seu idioma de projeto padrão](#c-script-projects).

Neste artigo, os exemplos estão disponíveis atualmente apenas para as funções JavaScript (Node. js C# ) e biblioteca de classes.  

Este artigo fornece detalhes sobre como usar a extensão Azure Functions para desenvolver funções e publicá-las no Azure. Antes de ler este artigo, você deve [criar sua primeira função usando Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Não combine desenvolvimento local e desenvolvimento de portal para um único aplicativo de funções. Quando você publica de um projeto local para um aplicativo de funções, o processo de implantação substitui todas as funções que você desenvolveu no Portal.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e executar a extensão de Azure Functions de [extensão de Azure Functions][extensão das funções do azure para o visual studio code], você deve atender a esses requisitos:

* [Visual Studio Code](https://code.visualstudio.com/) instalado em uma das [plataformas com suporte](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Outros recursos de que você precisa, como uma conta de armazenamento do Azure, são criados em sua assinatura quando você [publica usando Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Você pode desenvolver funções localmente e publicá-las no Azure sem precisar iniciá-las e executá-las localmente. Para executar suas funções localmente, você precisará atender a alguns requisitos adicionais, incluindo um download automático de Azure Functions Core Tools. Para saber mais, confira [requisitos adicionais para executar um projeto localmente](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Criar um projeto das Funções do Azure

A extensão Functions permite criar um projeto de aplicativo de funções, juntamente com sua primeira função. As etapas a seguir mostram como criar uma função disparada por HTTP em um novo projeto do functions. O [gatilho http](functions-bindings-http-webhook.md) é o modelo de gatilho de função mais simples para demonstrar.

1. No **Azure: Functions**, selecione o ícone **criar função** :

    ![Criar uma função](./media/functions-develop-vs-code/create-function.png)

1. Selecione a pasta para seu projeto de aplicativo de funções e, em seguida, **Selecione um idioma para seu projeto de função**.

1. Selecione o modelo de função de **gatilho http** ou selecione **ignorar agora** para criar um projeto sem uma função. Você sempre pode [Adicionar uma função ao seu projeto](#add-a-function-to-your-project) posteriormente.

    ![Escolher o modelo do acionador HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Digite **HTTPTrigger** para o nome da função e selecione Enter e, em seguida, selecione autorização de **função** . Esse nível de autorização exige que você forneça uma [chave de função](functions-bindings-http-webhook.md#authorization-keys) ao chamar o ponto de extremidade da função.

    ![Selecionar autorização de função](./media/functions-develop-vs-code/create-function-auth.png)

    Uma função é criada no idioma escolhido e no modelo para uma função disparada por HTTP.

    ![Modelo de função disparada por HTTP no Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

O modelo de projeto cria um projeto no idioma escolhido e instala as dependências necessárias. Para qualquer idioma, o novo projeto tem estes arquivos:

* **host. JSON**: permite configurar o host do functions. Essas configurações se aplicam quando você estiver executando o Functions localmente e quando estiver executando-os no Azure. Para obter mais informações, consulte [referência de host. JSON](functions-host-json.md).

* **local. Settings. JSON**: mantém as configurações usadas quando você está executando funções localmente. Essas configurações são usadas somente quando você está executando funções localmente. Para obter mais informações, consulte [Local Settings File](#local-settings-file).

    >[!IMPORTANT]
    >Como o arquivo local. Settings. JSON pode conter segredos, você precisa excluí-lo do seu controle de origem do projeto.

Neste ponto, você pode adicionar associações de entrada e saída à sua função [modificando o arquivo function. JSON](#add-a-function-to-your-project) ou [adicionando um parâmetro a uma C# função de biblioteca de classes](#add-a-function-to-your-project).

Você também pode [Adicionar uma nova função ao seu projeto](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalar as extensões de enlace

Exceto para gatilhos HTTP e de temporizador, as associações são implementadas em pacotes de extensão. Você deve instalar os pacotes de extensão para os gatilhos e as associações que precisam deles. O processo de instalação de extensões de associação depende do idioma do seu projeto.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Execute o comando [dotnet adicionar pacote](/dotnet/core/tools/dotnet-add-package) na janela do terminal para instalar os pacotes de extensão de que você precisa em seu projeto. O comando a seguir instala a extensão de armazenamento do Azure, que implementa associações para armazenamento de BLOBs, filas e tabelas.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

---

## <a name="add-a-function-to-your-project"></a>Adicionar uma função ao seu projeto

Você pode adicionar uma nova função a um projeto existente usando um dos modelos de gatilho de funções predefinidos. Para adicionar um novo gatilho de função, selecione F1 para abrir a paleta de comandos e, em seguida, procure e execute o comando **Azure Functions: criar função**. Siga os prompts para escolher o tipo de disparador e definir os atributos necessários do gatilho. Se o gatilho exigir uma chave de acesso ou uma cadeia de conexão para se conectar a um serviço, prepare-o antes de criar o gatilho de função.

Os resultados dessa ação dependem do idioma do seu projeto:

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

Uma nova pasta é criada no projeto. A pasta contém um novo arquivo function. JSON e o novo arquivo de código JavaScript.

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Um novo C# arquivo de biblioteca de classes (. cs) é adicionado ao seu projeto.

---

## <a name="add-input-and-output-bindings"></a>Adicionar associações de entrada e saída

Você pode expandir sua função adicionando associações de entrada e saída. O processo para adicionar associações depende do idioma do seu projeto. Para saber mais sobre associações, confira [Azure Functions os conceitos de gatilhos e associações](functions-triggers-bindings.md).

Os exemplos a seguir se conectam a uma fila de armazenamento chamada `outqueue`, em que a cadeia de conexão para a conta de armazenamento é definida na configuração do aplicativo `MyStorageConnection` em local. Settings. JSON.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

Visual Studio Code permite que você adicione associações ao seu arquivo function. JSON seguindo um conjunto conveniente de prompts. Para criar uma associação, clique com o botão direito do mouse (Ctrl + clique em macOS) o arquivo **Function. JSON** em sua pasta de funções e selecione **Adicionar Associação**:

![Adicionar uma associação a uma função JavaScript existente ](media/functions-develop-vs-code/function-add-binding.png)

Veja a seguir exemplos de prompts para definir uma nova associação de saída de armazenamento:

| Mensagem | Valor | Descrição |
| -------- | ----- | ----------- |
| **Selecionar direção da Associação** | `out` | A associação é uma associação de saída. |
| **Selecionar associação com direção** | `Azure Queue Storage` | A associação é uma associação de fila de armazenamento do Azure. |
| **O nome usado para identificar essa associação em seu código** | `msg` | Nome que identifica o parâmetro de associação referenciado em seu código. |
| **A fila para a qual a mensagem será enviada** | `outqueue` | O nome da fila na qual a associação é gravada. Quando o *QueueName* não existe, a associação o cria no primeiro uso. |
| **Selecione configuração em "local. setting. JSON"** | `MyStorageConnection` | O nome de uma configuração de aplicativo que contém a cadeia de conexão para a conta de armazenamento. A configuração `AzureWebJobsStorage` contém a cadeia de conexão para a conta de armazenamento que você criou com o aplicativo de funções. |

Neste exemplo, a seguinte associação é adicionada à matriz de `bindings` em seu arquivo function. JSON:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Você também pode adicionar a mesma definição de ligação diretamente ao seu function. JSON.

No seu código de função, a associação de `msg` é acessada da `context`, como neste exemplo:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Para saber mais, consulte a referência de [Associação de saída de armazenamento de filas](functions-bindings-storage-queue.md#output---javascript-example) .

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Atualize o método de função para adicionar o seguinte parâmetro à definição do método de `Run`:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Esse código exige que você adicione a seguinte instrução de `using`:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

---

O parâmetro `msg` é um tipo `ICollector<T>`, que representa uma coleção de mensagens que são gravadas em uma associação de saída quando a função é concluída. Você adiciona uma ou mais mensagens à coleção. Essas mensagens são enviadas para a fila quando a função é concluída.

Para saber mais, consulte a documentação de [Associação de saída de armazenamento de filas](functions-bindings-storage-queue.md#output---c-example) .

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Publicar no Azure

Visual Studio Code permite publicar seu projeto do Functions diretamente no Azure. No processo, vai criar uma aplicação de funções e recursos relacionados na sua subscrição do Azure. A aplicação de funções proporciona um contexto de execução para as suas funções. O projeto é empacotado e implementado na nova aplicação de funções na sua subscrição do Azure.

Quando você publica de Visual Studio Code em um novo aplicativo de funções no Azure, é oferecido um caminho de criação de aplicativo de função rápida e um caminho avançado. 

Ao publicar do Visual Studio Code, você tira proveito da tecnologia de [implantação de zip](functions-deployment-technologies.md#zip-deploy) . 

### <a name="quick-function-app-create"></a>Criação rápida de aplicativos de funções

Quando você escolhe **+ criar novo aplicativo de funções no Azure...** , a extensão gera automaticamente valores para os recursos do Azure necessários para seu aplicativo de funções. Esses valores se baseiam no nome do aplicativo de funções que você escolher. Para obter um exemplo de como usar os padrões para publicar seu projeto em um novo aplicativo de funções no Azure, consulte o [artigo Visual Studio Code início rápido](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Se você quiser fornecer nomes explícitos para os recursos criados, deverá escolher o caminho avançado de criação.

### <a name="enable-publishing-with-advanced-create-options"></a>Publicar um projeto em um novo aplicativo de funções no Azure usando opções avançadas

As etapas a seguir publicam seu projeto em um novo aplicativo de funções criado com opções de criação avançadas:

1. Na área **Azure: funções** , selecione o ícone **implantar para aplicativo de funções** .

    ![Definições da Aplicação de funções](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Se você não estiver conectado, você será solicitado a entrar no **Azure**. Você também pode **criar uma conta gratuita do Azure**. Depois de entrar no navegador, volte para Visual Studio Code.

1. Se você tiver várias assinaturas, **Selecione uma assinatura** para o aplicativo de funções e, em seguida, selecione **+ criar novo aplicativo de funções no Azure... _Avançado_** . Essa opção _avançada_ oferece mais controle sobre os recursos que você cria no Azure. 

1. Seguindo os prompts, forneça estas informações:

    | Mensagem | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecionar aplicativo de funções no Azure | Criar novos Aplicativo de funções no Azure | No prompt seguinte, digite um nome globalmente exclusivo que identifique o novo aplicativo de funções e selecione Enter. Os carateres válidos para um nome de aplicação de funções são `a-z`, `0-9` e `-`. |
    | Selecionar um sistema operacional | Windows | O aplicativo de funções é executado no Windows. |
    | Selecionar um plano de hospedagem | Plano de consumo | Uma hospedagem de [plano de consumo](functions-scale.md#consumption-plan) sem servidor é usada. |
    | Selecione um tempo de execução para seu novo aplicativo | A linguagem do projeto | O tempo de execução deve corresponder ao projeto que você está publicando. |
    | Selecionar um grupo de recursos para novos recursos | Criar novo grupo de recursos | No prompt seguinte, digite um nome de grupo de recursos, como `myResourceGroup`e, em seguida, selecione Enter. Você também pode selecionar um grupo de recursos existente. |
    | Selecionar uma conta de armazenamento | Criar nova conta de armazenamento | No prompt seguinte, digite um nome globalmente exclusivo para a nova conta de armazenamento usada pelo seu aplicativo de funções e selecione Enter. Os nomes de conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Você também pode selecionar uma conta existente. |
    | Selecione um local para novos recursos | Região | Selecione um local em uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |

    Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. Selecione **Exibir saída** nesta notificação para exibir os resultados de criação e implantação, incluindo os recursos do Azure que você criou.

## <a name="republish-project-files"></a>Republicar arquivos de projeto

Quando você configura a [implantação contínua](functions-continuous-deployment.md), seu aplicativo de funções no Azure é atualizado sempre que os arquivos de origem são atualizados no local de origem conectado. Recomendamos a implantação contínua, mas você também pode republicar as atualizações do arquivo de projeto de Visual Studio Code.

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure.

1. Em Visual Studio Code, selecione F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione **Azure Functions: implantar no aplicativo de funções**.

1. Se você não estiver conectado, você será solicitado a entrar no **Azure**. Depois de entrar no navegador, volte para Visual Studio Code. Se você tiver várias assinaturas, **Selecione uma assinatura** que contenha seu aplicativo de funções.

1. Selecione seu aplicativo de funções existente no Azure. Quando você for avisado sobre a substituição de todos os arquivos no aplicativo de funções, selecione **implantar** para confirmar o aviso e continuar.

O projeto é recriado, reempacotado e carregado no Azure. O projeto existente é substituído pelo novo pacote e o aplicativo de funções é reiniciado.

## <a name="get-the-url-of-the-deployed-function"></a>Obter a URL da função implantada

Para chamar uma função disparada por HTTP, você precisa da URL da função quando ela é implantada em seu aplicativo de funções. Essa URL inclui quaisquer [chaves de função](functions-bindings-http-webhook.md#authorization-keys)necessárias. Você pode usar a extensão para obter essas URLs para suas funções implantadas.

1. Selecione F1 para abrir a paleta de comandos e, em seguida, procure e execute o comando **Azure Functions: Copiar URL da função**.

1. Siga os prompts para selecionar seu aplicativo de funções no Azure e, em seguida, o gatilho HTTP específico que você deseja invocar.

A URL da função é copiada para a área de transferência, juntamente com as chaves necessárias passadas pelo parâmetro de consulta `code`. Use uma ferramenta HTTP para enviar solicitações POST ou um navegador para solicitações GET para a função remota.  

## <a name="run-functions-locally"></a>Executar funções localmente

A extensão Azure Functions permite executar um projeto do Functions em seu computador de desenvolvimento local. O tempo de execução local é o mesmo tempo de execução que hospeda seu aplicativo de funções no Azure. As configurações locais são lidas no [arquivo local. Settings. JSON](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Requisitos adicionais para executar um projeto localmente

Para executar o projeto do Functions localmente, você deve atender a estes requisitos adicionais:

* Instale a versão 2. x de [Azure Functions Core Tools](functions-run-local.md#v2). O pacote de ferramentas principais é baixado e instalado automaticamente quando você inicia o projeto localmente. As ferramentas principais incluem todo o tempo de execução de Azure Functions, portanto, o download e a instalação podem levar algum tempo.

* Instale os requisitos específicos para a linguagem escolhida:

    | Idioma | Requisito |
    | -------- | --------- |
    | **C#** | [C#extensão](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Ferramentas de CLI do .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Depurador para extensão Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 ou posterior](https://maven.apache.org/) |
    | **JavaScript** | <sup>*</sup> do [node. js](https://nodejs.org/) |  
    | **Python** | [Extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) recomendado|

    <sup>*</sup> Active LTS e manutenção LTS versões (8.11.1 e 10.14.1 recomendado).

### <a name="configure-the-project-to-run-locally"></a>Configurar o projeto para ser executado localmente

O tempo de execução do Functions usa uma conta de armazenamento do Azure internamente para todos os tipos de gatilho diferentes de HTTP e WebHooks. Portanto, você precisa definir a chave **Values. AzureWebJobsStorage** para uma cadeia de conexão válida da conta de armazenamento do Azure.

Esta seção usa a [extensão de armazenamento do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) com [Gerenciador de armazenamento do Azure](https://storageexplorer.com/) para se conectar e recuperar a cadeia de conexão de armazenamento.

Para definir a cadeia de conexão da conta de armazenamento:

1. No Visual Studio, abra o **Cloud Explorer**, expanda **conta de armazenamento** > **sua conta de armazenamento**e, em seguida, selecione **Propriedades** e copie o valor da **cadeia de conexão primária** .

2. Em seu projeto, abra o arquivo local. Settings. JSON e defina o valor da chave **AzureWebJobsStorage** para a cadeia de conexão que você copiou.

3. Repita a etapa anterior para adicionar chaves exclusivas à matriz de **valores** para quaisquer outras conexões exigidas por suas funções.

Para obter mais informações, consulte [Local Settings File](#local-settings-file).

### <a name="debugging-functions-locally"></a>Depuração de funções localmente  

Para depurar suas funções, selecione F5. Se você ainda não tiver baixado as [ferramentas principais][Azure Functions Core Tools], você será solicitado a fazê-lo. Quando as ferramentas principais estão instaladas e em execução, a saída é mostrada no terminal. Isso é o mesmo que executar o comando `func host start` Core Tools do terminal, mas com tarefas de compilação adicionais e um depurador anexado.  

Quando o projeto estiver em execução, você poderá disparar suas funções como faria quando o projeto for implantado no Azure. Quando o projeto está sendo executado no modo de depuração, os pontos de interrupção são atingidos em Visual Studio Code, conforme esperado.

A URL de solicitação para gatilhos HTTP é exibida na saída no terminal. As teclas de função para gatilhos HTTP não são usadas quando um projeto é executado localmente. Para obter mais informações, consulte [estratégias para testar seu código no Azure Functions](functions-test-a-function.md).  

Para saber mais, confira [trabalhar com Azure Functions Core Tools][Azure Functions Core Tools].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por padrão, essas configurações não são migradas automaticamente quando o projeto é publicado no Azure. Depois que a publicação for concluída, você terá a opção de publicar configurações de local. Settings. JSON em seu aplicativo de funções no Azure. Para saber mais, consulte [Publicar configurações do aplicativo](#publish-application-settings).

Os valores em **connectionStrings** nunca são publicados.

Os valores das configurações do aplicativo de funções também podem ser lidos em seu código como variáveis de ambiente. Para obter mais informações, consulte as seções de variáveis de ambiente desses artigos de referência específicos a um idioma:

* [C#pré-compilado](functions-dotnet-class-library.md#environment-variables)
* [C#script (. CSX)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Configurações do aplicativo no Azure

As configurações no arquivo local. Settings. JSON em seu projeto devem ser as mesmas que as configurações do aplicativo no aplicativo de funções no Azure. Todas as configurações adicionadas a local. Settings. JSON também devem ser adicionadas ao aplicativo de funções no Azure. Essas configurações não são carregadas automaticamente quando você publica o projeto. Da mesma forma, todas as configurações que você cria em seu aplicativo [de funções no portal](functions-how-to-use-azure-function-app-settings.md#settings) devem ser baixadas em seu projeto local.

### <a name="publish-application-settings"></a>Publicar configurações do aplicativo

A maneira mais fácil de publicar as configurações necessárias para seu aplicativo de funções no Azure é usar o link **carregar configurações** que aparece depois de publicar o projeto:

![Carregar configurações do aplicativo](./media/functions-develop-vs-code/upload-app-settings.png)

Você também pode publicar as configurações usando o comando **Azure Functions: carregar configuração local** na paleta de comandos. Você pode adicionar configurações individuais às configurações do aplicativo no Azure usando o comando **Azure Functions: Adicionar nova configuração** .

> [!TIP]
> Certifique-se de salvar seu arquivo local. Settings. JSON antes de publicá-lo.

Se o arquivo local for criptografado, ele será descriptografado, publicado e criptografado novamente. Se houver configurações com valores conflitantes nos dois locais, você será solicitado a escolher como proceder.

Exiba configurações de aplicativo existentes na área **Azure: funções** expandindo sua assinatura, seu aplicativo de funções e **as configurações do aplicativo**.

![Exibir configurações do aplicativo de funções no Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Baixar configurações do Azure

Se você tiver criado as configurações do aplicativo no Azure, poderá baixá-las no arquivo local. Settings. JSON usando o comando **Azure Functions: baixar configurações remotas** .

Assim como acontece com o carregamento, se o arquivo local for criptografado, ele será descriptografado, atualizado e criptografado novamente. Se houver configurações com valores conflitantes nos dois locais, você será solicitado a escolher como proceder.

## <a name="monitoring-functions"></a>Funções de monitoramento

Quando você [executa funções localmente](#run-functions-locally), os dados de log são transmitidos para o console do terminal. Você também pode obter dados de log quando seu projeto do Functions está em execução em um aplicativo de funções no Azure. Você pode se conectar aos logs de streaming no Azure para ver os dados de log quase em tempo real, ou pode habilitar Application Insights para um entendimento mais completo de como seu aplicativo de funções está se comportando.

### <a name="streaming-logs"></a>Logs de streaming

Quando você está desenvolvendo um aplicativo, muitas vezes é útil ver informações de log em tempo quase real. Você pode exibir um fluxo de arquivos de log que estão sendo gerados por suas funções. Essa saída é um exemplo de logs de streaming para uma solicitação para uma função disparada por HTTP:

![Saída de logs de streaming para gatilho HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Para saber mais, confira [logs de streaming](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Os logs de streaming dão suporte a apenas uma única instância do host do functions. Quando a função é dimensionada para várias instâncias, os dados de outras instâncias não são mostrados no fluxo de log. [Live Metrics Stream](../azure-monitor/app/live-stream.md) no Application insights oferece suporte a várias instâncias. Embora também esteja quase em tempo real, o Stream Analytics é baseado em [dados de amostra](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Recomendamos que você monitore a execução de suas funções integrando seu aplicativo de funções com Application Insights. Quando você cria um aplicativo de funções no portal do Azure, essa integração ocorre por padrão. Ao criar seu aplicativo de funções durante a publicação do Visual Studio, você precisa integrar Application Insights você mesmo.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Para saber mais, consulte [monitorar Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>Projetos de script C\#

Por padrão, todos C# os projetos são criados como [ C# projetos de biblioteca de classes compilados](functions-dotnet-class-library.md). Se você preferir trabalhar com C# projetos de script em vez disso, deverá C# selecionar script como o idioma padrão nas configurações de extensão de Azure Functions:

1. Selecione **arquivo** > **preferências** > **configurações**.

1. Vá para **configurações de usuário** > **extensões** > **Azure Functions**.

1. Selecione o **script C #** do **Azure Function: linguagem do projeto**.

Depois de concluir essas etapas, as chamadas feitas às ferramentas básicas subjacentes incluem a opção `--csx`, que gera e publica C# arquivos de projeto de script (. CSX). Quando você tem esse idioma padrão especificado, todos os projetos que você cria são C# padrão para criar scripts de projetos. Não será solicitado que você escolha uma linguagem de projeto quando um padrão for definido. Para criar projetos em outros idiomas, você deve alterar essa configuração ou removê-la do arquivo Settings. JSON do usuário. Depois de remover essa configuração, você será solicitado a escolher o idioma novamente quando criar um projeto.

## <a name="command-palette-reference"></a>Referência da paleta de comandos

A extensão Azure Functions fornece uma interface gráfica útil na área para interagir com seus aplicativos de funções no Azure. A mesma funcionalidade também está disponível como comandos na paleta de comandos (F1). Estes Azure Functions comandos estão disponíveis:

|Azure Functions comando  | Descrição  |
|---------|---------|
|**Adicionar novas configurações**  |  Cria uma nova configuração de aplicativo no Azure. Para saber mais, consulte [Publicar configurações do aplicativo](#publish-application-settings). Talvez você também precise [baixar essa configuração para suas configurações locais](#download-settings-from-azure). |
| **Configurar a origem da implantação** | Conecta seu aplicativo de funções no Azure a um repositório git local. Para saber mais, consulte [implantação contínua para Azure Functions](functions-continuous-deployment.md). |
| **Conectar ao repositório GitHub** | Conecta seu aplicativo de funções a um repositório GitHub. |
| **Copiar URL da função** | Obtém a URL remota de uma função disparada por HTTP que está sendo executada no Azure. Para saber mais, veja [obter a URL da função implantada](#get-the-url-of-the-deployed-function). |
| **Criar aplicativo de funções no Azure** | Cria um novo aplicativo de funções em sua assinatura no Azure. Para saber mais, consulte a seção sobre como [publicar em um novo aplicativo de funções no Azure](#publish-to-azure).        |
| **Descriptografar configurações** | Descriptografa [as configurações locais](#local-settings-file) que foram criptografadas por **Azure Functions: configurações de criptografia**.  |
| **Excluir Aplicativo de funções** | Remove um aplicativo de funções de sua assinatura no Azure. Quando não há outros aplicativos no plano do serviço de aplicativo, você tem a opção de excluí-lo também. Outros recursos, como contas de armazenamento e grupos de recursos, não são excluídos. Para remover todos os recursos, você deve, em vez disso, [excluir o grupo de recursos](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Seu projeto local não é afetado. |
|**Excluir função**  | Remove uma função existente de um aplicativo de funções no Azure. Como essa exclusão não afeta seu projeto local, considere remover a função localmente e, em seguida, [republicar seu projeto](#republish-project-files). |
| **Excluir proxy** | Remove um proxy Azure Functions do seu aplicativo de funções no Azure. Para saber mais sobre proxies, confira [trabalhar com proxies do Azure Functions](functions-proxies.md). |
| **Excluir configuração** | Exclui uma configuração de aplicativo de funções no Azure. Essa exclusão não afeta as configurações no arquivo local. Settings. JSON. |
| **Desconectar do repositório**  | Remove a conexão de [implantação contínua](functions-continuous-deployment.md) entre um aplicativo de funções no Azure e um repositório de controle do código-fonte. |
| **Baixar configurações remotas** | Baixa as configurações do aplicativo de funções escolhido no Azure no arquivo local. Settings. JSON. Se o arquivo local for criptografado, ele será descriptografado, atualizado e criptografado novamente. Se houver configurações com valores conflitantes nos dois locais, você será solicitado a escolher como proceder. Lembre-se de salvar as alterações no arquivo local. Settings. JSON antes de executar esse comando. |
| **Editar configurações** | Altera o valor de uma configuração de aplicativo de funções existente no Azure. Esse comando não afeta as configurações no arquivo local. Settings. JSON.  |
| **Configurações de criptografia** | Criptografa itens individuais na matriz de `Values` nas [configurações locais](#local-settings-file). Nesse arquivo, `IsEncrypted` também é definido como `true`, que especifica que o tempo de execução local descriptografará as configurações antes de usá-las. Criptografe as configurações locais para reduzir o risco de vazar informações valiosas. No Azure, as configurações do aplicativo sempre são armazenadas criptografadas. |
| **Executar função agora** | Inicia manualmente uma [função disparada por temporizador](functions-bindings-timer.md) no Azure. Esse comando é usado para teste. Para saber mais sobre como disparar funções não HTTP no Azure, consulte [executar manualmente uma função não disparada por http](functions-manually-run-non-http.md). |
| **Inicializar o projeto para uso com VS Code** | Adiciona os arquivos de projeto de Visual Studio Code necessários a um projeto de funções existente. Use esse comando para trabalhar com um projeto que você criou usando as ferramentas básicas. |
| **Instalar ou atualizar Azure Functions Core Tools** | Instala ou atualiza [Azure Functions Core Tools], que é usado para executar funções localmente. |
| **Reimplantar**  | Permite reimplantar arquivos de projeto de um repositório git conectado para uma implantação específica no Azure. Para republicar as atualizações locais do Visual Studio Code, [Republique o projeto](#republish-project-files). |
| **Renomear configurações** | Altera o nome da chave de uma configuração de aplicativo de funções existente no Azure. Esse comando não afeta as configurações no arquivo local. Settings. JSON. Depois de renomear as configurações no Azure, você deve [baixar essas alterações no projeto local](#download-settings-from-azure). |
| **Reiniciar** | Reinicia o aplicativo de funções no Azure. A implantação de atualizações também reinicia o aplicativo de funções. |
| **Definir AzureWebJobsStorage**| Define o valor da configuração do aplicativo `AzureWebJobsStorage`. Essa configuração é exigida pelo Azure Functions. Ele é definido quando um aplicativo de funções é criado no Azure. |
| **Começar** | Inicia um aplicativo de função parado no Azure. |
| **Iniciar logs de streaming** | Inicia os logs de streaming para o aplicativo de funções no Azure. Use logs de streaming durante a solução de problemas remota no Azure se você precisar ver informações de log em tempo quase real. Para saber mais, confira [logs de streaming](#streaming-logs). |
| **Deixar** | Interrompe um aplicativo de funções que está sendo executado no Azure. |
| **Parar logs de streaming** | Interrompe os logs de streaming para o aplicativo de funções no Azure. |
| **Alternar como configuração de slot** | Quando habilitado, o garante que uma configuração de aplicativo persista para um determinado slot de implantação. |
| **Desinstalar o Azure Functions Core Tools** | Remove Azure Functions Core Tools, que é exigido pela extensão. |
| **Carregar configurações locais** | Carrega as configurações do arquivo local. Settings. JSON para o aplicativo de funções escolhido no Azure. Se o arquivo local for criptografado, ele será descriptografado, carregado e criptografado novamente. Se houver configurações com valores conflitantes nos dois locais, você será solicitado a escolher como proceder. Lembre-se de salvar as alterações no arquivo local. Settings. JSON antes de executar esse comando. |
| **Exibir confirmação no GitHub** | Mostra a confirmação mais recente em uma implantação específica quando seu aplicativo de funções está conectado a um repositório. |
| **Exibir logs de implantação** | Mostra os logs de uma implantação específica para o aplicativo de funções no Azure. |

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre Azure Functions Core Tools, confira [trabalhar com Azure Functions Core Tools](functions-run-local.md).

Para saber mais sobre como desenvolver funções como bibliotecas de classes do .NET, confira [ C# Azure Functions referência do desenvolvedor](functions-dotnet-class-library.md). Este artigo também fornece links para exemplos de como usar atributos para declarar os vários tipos de associações com suporte pelo Azure Functions.

[Extensão das Funções do Azure para o Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md
