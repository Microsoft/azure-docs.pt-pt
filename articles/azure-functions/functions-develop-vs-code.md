---
title: Desenvolver as funções do Azure com o Visual Studio Code | Documentos da Microsoft
description: Saiba como desenvolver e testar as funções do Azure com a extensão de funções do Azure para Visual Studio Code.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: glenga
ms.openlocfilehash: 17550e148ea61eb69a20fc6a3215dfb63b65f18e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452699"
---
# <a name="develop-azure-functions-using-visual-studio-code"></a>Desenvolver as funções do Azure com o Visual Studio Code

O [Extensão das Funções do Azure para o Visual Studio Code] permite localmente desenvolver e implementar as funções para o Azure. Se esta experiência é o primeiro com as funções do Azure, pode saber mais em [uma introdução às funções do Azure](functions-overview.md).

A extensão de funções do Azure fornece as seguintes vantagens: 

* Editar, criar e executar as funções no seu computador de desenvolvimento local. 
* Publicar o seu projeto de funções do Azure diretamente no Azure. 
* Escreva as suas funções em várias linguagens, enquanto tem todos os benefícios do Visual Studio Code. 

A extensão pode ser utilizada com os seguintes idiomas suportados pelo runtime das funções do Azure versão 2.x: 

* [C#compilado](functions-dotnet-class-library.md) 
* [Script c#](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Requer que [definir C# script como o idioma predefinido do projeto](#c-script-projects).

Neste artigo, exemplos só estão atualmente disponíveis para JavaScript (node. js) e C# funções de biblioteca de classe.  

Este artigo fornece detalhes sobre como utilizar a extensão de funções do Azure para desenvolver as funções e publique-os para o Azure. Antes de ler este artigo, deve [criar a sua primeira função com o Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Não misture o desenvolvimento local com o desenvolvimento de portais na mesma function app. Quando publicar a partir de um projeto local para uma aplicação de funções, o processo de implantação substitui quaisquer funções que desenvolveu no portal.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e executar o [extensão de funções do Azure][extensão das funções do azure para o visual studio code], tem de cumprir os seguintes requisitos:

* [Visual Studio Code](https://code.visualstudio.com/) instalado em um da [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Outros recursos de que precisa, tal como uma conta de armazenamento do Azure, são criados na sua subscrição quando [publicar com o Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Pode desenvolver as funções localmente e publicar no Azure sem ter de iniciar e executá-los localmente. Existem requisitos adicionais para executar seu funcional localmente, incluindo uma transferência automática de ferramentas de núcleo de funções do Azure. Para obter mais informações, consulte [requisitos adicionais para executar localmente](#additional-requirements-to-run-locally). 

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Criar um projeto das Funções do Azure

A extensão de funções permite-lhe criar um projeto de aplicação de função, juntamente com a sua primeira função. Os passos seguintes mostram como criar uma função acionada por HTTP num novo projeto de funções. [Acionador HTTP](functions-bindings-http-webhook.md) é o modelo de Acionador de função mais simples para demonstrar.

1. De **Azure: As funções**, escolha o ícone de Create Function.

    ![Criar uma função](./media/functions-develop-vs-code/create-function.png)

1. Selecione a pasta para o seu projeto de aplicação de função e, em seguida **selecione um idioma para o projeto de função**. 

1. Selecione o **acionador HTTP** modelo de função, ou pode optar por **ignorar por agora** para criar um projeto sem uma função. Pode sempre [adicionar uma função ao seu projeto](#add-a-function-to-your-project) num momento posterior. 

    ![Escolher o modelo do acionador HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Tipo `HTTPTrigger` para o nome de função e prima Enter, em seguida, selecione **função** autorização. Este nível de autorização exige que forneça uma [tecla de função](functions-bindings-http-webhook.md#authorization-keys) ao chamar o ponto final de função.

    ![Escolher a autenticação de função](./media/functions-develop-vs-code/create-function-auth.png)

    É criada uma função na linguagem que escolheu com o modelo de função acionada por HTTP.

    ![Modelo de função acionada por HTTP no Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

O modelo de projeto cria um projeto na linguagem que escolheu, instala as dependências necessárias. Para qualquer idioma, o novo projeto tem os seguintes ficheiros:

* **host.json**: Permite-lhe configurar o anfitrião de funções. Estas definições aplicam-se tanto ao executar localmente e no Azure. Para obter mais informações, consulte [referência de Host. JSON](functions-host-json.md).

* **local.settings.json**: Mantém as definições utilizadas ao executar as funções localmente. Estas definições só são utilizadas ao executar localmente. Para obter mais informações, consulte [arquivo de configurações Local](#local-settings-file).

    >[!IMPORTANT]
    >Porque o ficheiro Settings pode conter segredos, deve exclui-lo de seu controle de origem do projeto.

Neste momento, pode adicionar a entrada e ligações de saída para a sua função por [modifica o ficheiro de Function](#javascript-2), ou pelo [adicionando um parâmetro para um C# função de biblioteca de classe](#c-class-library-2).

Também pode [adicionar uma nova função ao seu projeto](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalar as extensões de enlace

Exceto para acionadores HTTP e Timer, enlaces são implementados nos pacotes de extensão. Tem de instalar os pacotes de extensão para acionadores e enlaces que dele necessitem. A maneira que instale as extensões de ligação depende da sua linguagem de projeto.

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# biblioteca de classes

Executar o [dotnet Adicionar pacote](/dotnet/core/tools/dotnet-add-package) comando na janela de Terminal para instalar os pacotes de extensão que precisa no seu projeto. O exemplo seguinte instala a extensão de armazenamento do Azure, que implementa enlaces para o armazenamento de BLOBs, filas e tabelas.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

## <a name="add-a-function-to-your-project"></a>Adicionar uma função ao seu projeto

Pode adicionar uma nova função a um projeto existente, utilizando um dos modelos de Acionador de funções predefinidos. Para adicionar um acionador de função nova, prima a tecla F1 para abrir a paleta de comandos, em seguida, procure e execute o comando **as funções do Azure: Crie função...** . Siga as instruções para escolher o tipo de Acionador e definir os atributos necessários do acionador. Se o acionador necessitar de uma cadeia de ligação ou chave de acesso para ligar a um serviço, prepará-lo antes de criar o acionador de função. 

Os resultados desta operação dependem de sua linguagem de projeto:

### <a name="javascript"></a>JavaScript

É criada uma nova pasta no projeto, que contém um novo ficheiro de Function e o novo arquivo de código do JavaScript.

### <a name="c-class-library"></a>C\# biblioteca de classes

Um novo C# ficheiro de biblioteca (. CS) de classe é adicionado ao seu projeto.

## <a name="add-input-and-output-bindings"></a>Adicionar entrada e saída enlaces

Pode expandir o que a função ao adicionar enlaces de entrada e saída. A forma como o que fazer isso depende da sua linguagem de projeto. Para saber mais sobre ligações, veja [acionadores de funções do Azure e conceitos de enlaces](functions-triggers-bindings.md). 

Os exemplos seguintes ligar a uma fila de armazenamento com o nome `outqueue`, em que a cadeia de ligação para a conta de armazenamento está definida no `MyStorageConnection` definição da aplicação na Settings. 

### <a name="javascript"></a>JavaScript

Código do Visual Studio permite-lhe adicionar enlaces a seu arquivo de Function ao seguir um conjunto prático de pedidos. Para criar uma ligação, botão direito do mouse (Ctrl + clique em macOS) a `function.json` de ficheiros na sua pasta de função e escolha **Adicionar enlace de...** . 

![Adicionar um enlace a uma função de JavaScript existente ](media/functions-develop-vs-code/function-add-binding.png)

Seguem-se instruções de exemplo para definir um novo enlace de saída de armazenamento:

| Mensagem | Value | Descrição |
| -------- | ----- | ----------- |
| **Selecione a direção de ligação** | `out` | A associação é um enlace de saída. |
| **Selecione a ligação com a direção...** | `Azure Queue Storage` | A associação é um enlace de fila de armazenamento do Azure. |
| **O nome utilizado para identificar este enlace no código** | `msg` | Nome que identifica o parâmetro de ligação referenciado no seu código. |
| **A fila para o qual a mensagem será enviada** | `outqueue` | O nome da fila na qual o enlace escreve. Quando o *queueName* não existir, o enlace cria-o na primeira utilização. |
| **Selecione a definição de "local.setting.json"** | `MyStorageConnection` | O nome de uma definição da aplicação que contém a cadeia de ligação para a conta de armazenamento. O `AzureWebJobsStorage` definição contém a cadeia de ligação para a conta de armazenamento que criou com a aplicação de funções. |

Neste exemplo, a ligação seguinte é adicionada ao `bindings` matriz no seu ficheiro Function:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Também pode adicionar a mesma definição de ligação diretamente para a sua Function.

No código da função, o `msg` vinculação é acessada a partir do `context`, como no exemplo a seguir:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Para obter mais informações, consulte a [enlace de saída do armazenamento de filas](functions-bindings-storage-queue.md#output---javascript-example) referência.

### <a name="c-class-library"></a>C\# biblioteca de classes

Atualizar o método de função para adicionar o seguinte parâmetro para o `Run` definição de método:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Este código exige que adicione o seguinte `using` instrução:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

O `msg` parâmetro é um `ICollector<T>` tipo, que representa uma coleção de mensagens que são escritos para uma saída de ligação quando a função for concluída. Adicionar uma ou mais mensagens à coleção, que são enviadas para a fila quando a função for concluída.

Para obter mais informações, consulte a [enlace de saída do armazenamento de filas](functions-bindings-storage-queue.md#output---c-example) referência.

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Publicar no Azure

O Visual Studio Code permite-lhe publicar o projeto de funções diretamente no Azure. No processo, vai criar uma aplicação de funções e recursos relacionados na sua subscrição do Azure. A aplicação de funções proporciona um contexto de execução para as suas funções. O projeto é empacotado e implementado na nova aplicação de funções na sua subscrição do Azure.

Durante a publicação do Visual Studio Code, são utilizados dois métodos de implementação:

* [Zip implantar com a execução do pacote habilitado](functions-deployment-technologies.md#zip-deploy): utilizado para a maioria das implementações de funções do Azure.
* [O URL do pacote externo](functions-deployment-technologies.md#external-package-url): utilizado para a implementação de aplicações do Linux num [plano de consumo](functions-scale.md#consumption-plan).

### <a name="quick-function-app-creation"></a>Criação de aplicações de função rápida

Por predefinição, o código do Visual Studio gera automaticamente valores para os recursos do Azure necessários para a aplicação de funções. Estes valores baseiam-se o nome de aplicação de função que escolher. Para obter um exemplo do uso de padrões para publicar o seu projeto para uma nova aplicação de função no Azure, consulte a [artigo de início rápido do Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Se quiser fornecer nomes explícitos para os recursos criados, tem de ativar a publicação utilizar as opções avançadas.

### <a name="enabled-publishing-with-advanced-create-options"></a>Opções de criação de publicação ativada com avançadas

Para lhe dar controlo sobre as definições com as funções do Azure criar aplicações associadas, atualize a extensão de funções do Azure para ativar as definições avançadas.

1. Clique em **ficheiro > Preferências > definições**

1. Navegar pelos **as definições de utilizador > extensões > as funções do Azure**

1. Marque a caixa de verificação para **função do Azure: Criação avançada**

### <a name="publish-to-a-new-function-app-in-azure-with-advanced-creation"></a>Publicar uma nova aplicação de função no Azure com a criação avançada

Os seguintes passos publicar o seu projeto para uma nova aplicação de funções criada usando o advanced criar opções.

1. Na **Azure: As funções** área, selecione a implementar no ícone de aplicação de funções.

    ![Definições da aplicação de função](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Se não sessão iniciada, deverá **iniciar sessão no Azure**. Também pode **criar uma conta gratuita do Azure**. Depois de concluída com êxito de início de sessão do navegador, volte ao Visual Studio Code.

1. Se tiver várias subscrições, **Selecione uma subscrição** para a aplicação de função, em seguida, escolha **+ criar nova aplicação de funções no Azure**.

1. Siga as instruções, forneça as seguintes informações:

    | Mensagem | Value | Descrição |
    | ------ | ----- | ----------- |
    | Selecione a aplicação de funções no Azure | + Criar nova aplicação de funções no Azure | Na linha de comandos seguinte, escreva um nome globalmente exclusivo que identifica a sua aplicação function app nova e prima Enter. Os carateres válidos para um nome de aplicação de funções são `a-z`, `0-9` e `-`. |
    | Selecione um sistema operacional | Windows | Aplicação de função for executada no Windows |
    | Selecione um plano de alojamento | Plano de consumo | Sem servidor [alojamento do plano de consumo](functions-scale.md#consumption-plan) é utilizado. |
    | Selecione um tempo de execução para a nova aplicação | O idioma de projeto | O tempo de execução tem de corresponder ao projeto que está a publicar. |
    | Selecione um grupo de recursos para novos recursos | Criar novo grupo de recursos | Na linha de comandos seguinte, escreva um nome de grupo de recursos, como `myResourceGroup`, e prima enter. Também pode escolher um grupo de recursos existente. |
    | Selecione uma conta de armazenamento | Criar nova conta de armazenamento | Na linha de comandos seguinte, o tipo de um nome globalmente exclusivo da nova conta de armazenamento utilizado pela sua aplicação de funções e prima Enter. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode escolher uma conta existente. |
    | Selecione uma localização para novos recursos | Região | Escolha uma localização numa [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. Selecione **vista de saída** na notificação para ver a criação e os resultados de implementação, incluindo os recursos do Azure que criou.

## <a name="republish-project-files"></a>Voltar a publicar ficheiros de projeto

Quando configura [implementação contínua](functions-continuous-deployment.md), a aplicação de função no Azure é atualizada sempre que os ficheiros de origem são atualizados na localização de origem ligada. Mesmo que recomendemos essa prática de desenvolvimento, pode também voltar a publicar as atualizações de arquivo de projeto do Visual Studio Code. 

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure.

1. No Visual Studio Code, prima F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Deploy to function app...`.

1. Se não sessão iniciada, deverá **iniciar sessão no Azure**. Depois de concluída com êxito de início de sessão do navegador, volte ao Visual Studio Code. Se tiver várias subscrições, **Selecione uma subscrição** que contém a aplicação de funções.

1. Escolha a sua aplicação de função no Azure. Quando avisado sobre Sobrescrever todos os ficheiros na aplicação de função, escolha **Deploy** para reconhecer o aviso e continuar. 

O projeto é recriado, reempacotado e carregado para o Azure. O projeto existente é substituído pelo novo pacote e reinicia a aplicação de funções.

## <a name="get-deployed-function-url"></a>Obter URL de função implementada

Para poder chamar uma função acionada por HTTP, terá do URL da função quando implementado a sua aplicação de funções. Este URL inclui necessários [chaves de função](functions-bindings-http-webhook.md#authorization-keys). Pode utilizar a extensão para obter estes URLs para as suas funções implementadas.

1. chave de prima F1 para abrir a paleta de comandos, em seguida, procure e execute o comando **as funções do Azure: Copie o URL da função**.

1. Siga as indicações para escolher a sua aplicação de funções no Azure e, em seguida, o acionador HTTP específico que deseja invocar. 

A função de URL é copiado para a área de transferência, juntamente com quaisquer chaves necessários passado com o `code` parâmetro de consulta. Utilize uma ferramenta HTTP para enviar pedidos POST ou um navegador para pedidos GET para a função remota.  

## <a name="run-functions-locally"></a>Executar funções localmente

A extensão de funções do Azure permite-lhe executar um projeto de funções no seu computador de desenvolvimento local. O tempo de execução local é o tempo de execução mesmo que aloja a aplicação de funções no Azure. As definições locais são lidos a partir da [Settings ficheiro](#local-settings-file).

### <a name="additional-requirements-to-run-locally"></a>Requisitos adicionais para executar localmente

Ser capaz de executar o projeto de funções localmente, também deve cumprir estes requisitos adicionais:

* Instalar a versão 2.x dos [ferramentas de núcleo de funções do Azure](functions-run-local.md#v2). O pacote de ferramentas de núcleo é transferido e instalado para sua automaticamente quando inicia o projeto localmente. As ferramentas de núcleo incluem o tempo de execução de funções do Azure inteiro, para que a transferência e instalação podem demorar algum tempo.

* Instale os requisitos específicos para a linguagem escolhida:

    | Idioma | Requisito |
    | -------- | --------- |
    | **C#** | [Extensão c#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Ferramentas da CLI do .NET core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Depurador para a extensão de Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Extensão de Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6 +](https://www.python.org/downloads/)|

    <sup>*</sup>Active Directory LTS e manutenção LTS versões (8.11.1 e 10.14.1 recomendado).

### <a name="configure-the-project-to-run-locally"></a>Configurar o projeto para executar localmente

O runtime das funções utiliza uma conta de armazenamento do Azure internamente para todos os tipos de Acionador diferentes de HTTP e webhooks. Isso significa que tem de definir o **Values.AzureWebJobsStorage** chave para uma cadeia de ligação de conta de armazenamento do Azure válida.

Esta secção utiliza o [extensão de armazenamento do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) com [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com/) para ligar e obter a cadeia de ligação de armazenamento.   

Para definir a cadeia de ligação de conta de armazenamento:

1. No Visual Studio, abra **Cloud Explorer**, expanda **conta de armazenamento** > **sua conta de armazenamento**, em seguida, selecione **propriedades**e copie o **cadeia de ligação primária** valor.

2. No seu projeto, abra o ficheiro Settings e defina o valor do **AzureWebJobsStorage** chave para a cadeia de ligação que copiou.

3. Repita o passo anterior para adicionar chaves exclusivas para o **valores** matriz para todas as outras ligações necessária para as suas funções.

Para obter mais informações, consulte [arquivo de configurações Local](#local-settings-file).

### <a name="debugging-functions-locally"></a>Depuração de funções localmente  

Para depurar as suas funções, pressione F5. Se ainda não tiver transferido o [ferramentas de núcleo][ferramentas de núcleo das funções do azure], lhe for pedido para fazer isso. Quando as ferramentas de núcleo está instalada e em execução, saída é mostrada no Terminal. Este é o mesmo que a execução `func host start` comando de ferramentas de núcleo, a partir do Terminal, mas com adicionais criar tarefas e um depurador anexado.  

Com o projeto em execução, pode acionar as suas funções como faria quando implementada no Azure. Quando em execução no modo de depuração, pontos de interrupção são atingidos no Visual Studio Code, conforme o esperado.

O URL do pedido para acionadores HTTP é apresentado na saída no terminal. Teclas de função para acionadores HTTP não são usadas ao executar localmente. Para obter mais informações, consulte [estratégias para testar seu código nas funções do Azure](functions-test-a-function.md).  

Para obter mais informações, consulte [trabalhar com as ferramentas de núcleo de funções do Azure][ferramentas de núcleo das funções do azure].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por predefinição, estas definições não são migradas automaticamente quando o projeto é publicado para o Azure. Após a conclusão da publicação, é-lhe dada a opção de publicar as definições do Settings para a aplicação de funções no Azure. Para obter mais informações, consulte [definições de aplicação de publicação](#publish-application-settings).

Os valores na **ConnectionStrings** nunca são publicados.

Os valores de definições de aplicação de função também podem ser lidos em seu código como variáveis de ambiente. Para obter mais informações, consulte a secção de variáveis de ambiente dos seguintes artigos de referência de idioma específico:

* [C# pré-compiladas](functions-dotnet-class-library.md#environment-variables)
* [Script do c# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Definições da aplicação no Azure

As definições no arquivo Settings no seu projeto devem ser o mesmo que as definições da aplicação na aplicação de função no Azure. Todas as definições de que adicionar para o Settings tem de ser adicionadas também para a aplicação de funções no Azure. Estas definições não são carregadas automaticamente quando publica o projeto. Da mesma forma, todas as definições que criou na sua aplicação de função [no portal do](functions-how-to-use-azure-function-app-settings.md#settings) deverão ser transferidos para o seu projeto local.

### <a name="publish-application-settings"></a>Definições de aplicação de publicação

A maneira mais fácil para publicar as definições necessárias para a aplicação de funções no Azure está a utilizar o **carregar definições** ligação que é apresentada depois de publicar seu projeto com êxito.

![Implementação concluída as definições da aplicação de carregamento](./media/functions-develop-vs-code/upload-app-settings.png)

Também pode publicar definições utilizando o `Azure Functions: Upload Local Setting` comando na paleta de comandos. As definições individuais são adicionadas às definições da aplicação no Azure utilizando o `Azure Functions: Add New Setting...` comando. 

> [!TIP]
> Certifique-se de que guarde o ficheiro Settings antes de o publicar.

Se o ficheiro local está encriptado, é desencriptado, publicado e encriptado novamente. Se existirem definições com valores diferentes em ambos os locais, é-lhe perguntado para escolher como proceder.

Ver as definições da aplicação existente no **Azure: As funções** área ao expandir a sua subscrição, a aplicação de função, e **definições da aplicação**.

![Definição de aplicação de função de visualização no Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Definições de transferência do Azure

Se tiver criado as definições da aplicação no Azure, pode transferi-los em seu arquivo Settings. ao utilizar o `Azure Functions: Download Remote Settings...` comando. 

Tal como acontece com carregamento, se o ficheiro local está encriptado, é desencriptado, atualizada e encriptado novamente. Se existirem definições com valores diferentes em ambos os locais, é-lhe perguntado para escolher como proceder.

## <a name="monitoring-functions"></a>Funções de monitorização

Quando [executar localmente](#run-functions-locally), dados de registo são transmitidos para a consola de Terminal. Também pode obter dados de registo quando o projeto de funções está em execução numa aplicação de funções no Azure. A ligar a transmissão em fluxo registos no Azure para ver dados de registo quase em tempo real, ou pode ativar o Application Insights para uma compreensão mais completa de como a sua aplicação function app está se comportando.

### <a name="streaming-logs"></a>Registos de transmissão em fluxo

Ao desenvolver um aplicativo, geralmente é útil ver informações de registo em tempo quase real. Pode ver um fluxo de ficheiros de registo que está a ser gerados pelas suas funções. O resultado seguinte é a função acionada por um exemplo de transmissão em fluxo registos de uma solicitação para um HTTP:

![Transmissão em fluxo registos de saída para o acionador HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png) 

Para obter mais informações, consulte [registos de transmissão em fluxo](functions-monitoring.md#streaming-logs). 

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Registos de transmissão em fluxo suportam apenas uma única instância do host de funções. Quando a sua função é dimensionada para várias instâncias, os dados a partir de outras instâncias não são apresentados no fluxo de registo. O [Stream de métricas em direto](../azure-monitor/app/live-stream.md) no Application Insights suportados várias instâncias. Embora também em quase em tempo real, análise de transmissão em fluxo também baseiam-se [diversas amostras de dados](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

A forma recomendada para monitorizar a execução das suas funções é integrar a sua aplicação de funções com o Azure Application Insights. Quando cria uma aplicação de funções no portal do Azure, esta integração é feita para, por predefinição. No entanto, quando criar a sua aplicação de função durante a publicação do Visual Studio, não é feita a integração na sua aplicação de função no Azure.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Para obter mais informações, consulte [as funções do Azure de Monitor](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# projetos do script

Por predefinição, todos os C# projetos são criados como [ C# compilado projetos de biblioteca de classe](functions-dotnet-class-library.md). Se preferir em vez disso trabalhar com o C# projetos de script, tem de selecionar C# script como o idioma padrão nas definições de extensão de funções do Azure.

1. Clique em **ficheiro > Preferências > definições**.

1. Navegar pelos **definições de utilizador > extensões > as funções do Azure**.

1. Escolher **C #Script** partir **função do Azure: Idioma do projeto**.

Neste momento, as chamadas feitas para as ferramentas de núcleo subjacente incluem o `--csx` opção, que gera e publica C# arquivos de projeto (. csx) de script. Com um idioma padrão especificado, todos criados padrão de projetos para C# script de projetos. Não é-lhe pedido para escolher uma linguagem de projeto, quando uma predefinição está definida. Para criar outros projetos de idioma, tem de alterar esta definição ou removê-lo a partir do ficheiro Settings JSON utilizador. Depois de remover esta definição, é-lhe perguntado novamente para escolher seu idioma, quando cria um projeto.

## <a name="command-palette-reference"></a>Referência de paleta de comandos

A extensão de funções do Azure fornece uma interface gráfica útil na área de segurança do Azure para interagir com as suas aplicações de função no Azure. A mesma funcionalidade também está disponível como comandos na paleta de comandos (F1). Os seguintes comandos específicos de funções do Azure estão disponíveis:

|Comando de funções do Azure  | Descrição  |
|---------|---------|
|**Adicione novas definições...**  |  Cria uma nova definição de aplicação no Azure. Para obter mais informações, consulte [definições de aplicação de publicação](#publish-application-settings). Também poderá [transferir esta definição para suas definições locais](#download-settings-from-azure). |
| **Configure a origem de implementação...** | Ligar a sua aplicação de função no Azure para um repositório de Git local. Para obter mais informações, consulte [implementação contínua para funções do Azure](functions-continuous-deployment.md). |
| **Ligar para o repositório do GitHub...** | Ligar a sua aplicação de função um repositório do GitHub. |
| **Copie o URL da função** | Obtém o URL remoto de um HTTP função acionada por um em execução no Azure. Para obter mais informações, consulte como [obter o URL de função implementada](#get-deployed-function-url). |
| **Crie aplicação de funções no Azure...** | Cria uma nova aplicação de função na sua subscrição no Azure. Para obter mais informações, consulte como [publicar uma nova aplicação de função no Azure](#publish-to-azure).        |
| **Desencriptar as definições** | Utilize para desencriptar [configurações locais](#local-settings-file) que tem sido criptografado usando `Azure Functions: Encrypt Settings`.  |
| **Eliminar a aplicação de funções...** | Remove uma aplicação de função existente da sua subscrição no Azure. Quando não há nenhuma outra aplicação no plano do serviço de aplicações, é-lhe fornecido a opção de eliminar que demasiado. Outros recursos, como contas de armazenamento e de grupos de recursos, não são eliminados. Para remover todos os recursos, em vez disso, deve [eliminar o grupo de recursos](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Não é afetado o seu projeto local. |
|**Elimine a função...**  | Remove uma função já existente de uma aplicação de funções no Azure. Porque essa eliminação não afeta o seu projeto local, em vez disso, considere remover a função localmente e, em seguida [republicar o seu projeto](#republish-project-files). |
| **Elimine Proxy...** | Remove um proxy de funções do Azure da sua aplicação de função no Azure. Para saber mais sobre proxies, veja [trabalhar com os Proxies de funções do Azure](functions-proxies.md). |
| **Elimine definição de...** | Elimina uma definição de aplicação de função no Azure. Não afeta as definições no seu arquivo Settings. |
| **Desligar do repositório...**  | Remover os [implementação contínua](functions-continuous-deployment.md) ligação entre uma aplicação de funções no Azure e um repositório de controle de origem. |
| **Transferir as definições remotas...** | Transfere as definições a partir da aplicação de função escolhida no Azure em seu arquivo Settings. Se o ficheiro local está encriptado, é desencriptado, atualizada e encriptado novamente. Se existirem definições com valores diferentes em ambos os locais, é-lhe perguntado para escolher como proceder. Certifique-se de que guardar as alterações ao ficheiro Settings antes de executar este comando. |
| **Edite definições...** | Altera o valor de uma configuração existente para a aplicação de função no Azure. Não afeta as definições no seu arquivo Settings.  |
| **Encriptar definições** | Encripta os itens individuais no `Values` obsahuje pole o [configurações locais](#local-settings-file). Nesse arquivo, `IsEncrypted` também está definida como `true`, que informa o tempo de execução local para desencriptar as definições antes de os utilizar. Encripte definições locais para reduzir o risco de vazamento de informações valiosas. No Azure, aplicação de definições são sempre armazenadas encriptadas. |
| **Executar a função agora** | Inicia um [função acionada por temporizador](functions-bindings-timer.md) no Azure manualmente para fins de teste. Para saber mais sobre o acionar as funções de não-HTTP no Azure, veja [executar manualmente uma função não acionada por HTTP](functions-manually-run-non-http.md). |
| **Inicialize o projeto para utilização com o VS Code...** | Adiciona os ficheiros de projeto do Visual Studio Code necessários para um projeto de funções existente. Utilize este comando para trabalhar com um projeto que criou com as ferramentas de núcleo. |
| **Instalação de ferramentas de núcleo de funções do Azure de atualização** | Instala ou atualiza a [Ferramentas de núcleo das funções do Azure] que são utilizados para executar localmente. |
| **Redeploy**  | Permite-lhe implementar novamente os arquivos de projeto a partir de um repositório de Git ligado para uma implementação específica no Azure. Para voltar a publicar atualizações locais do Visual Studio Code [voltar a publicar o projeto](#republish-project-files). |
| **Mudar o nome de definições...** | Muda o nome da chave de uma configuração existente para a aplicação de função no Azure. Não afeta as definições no seu arquivo Settings. Depois de mudar as definições no Azure, deve [baixar essas alterações para o projeto local](#download-settings-from-azure). |
| **Reiniciar** | Reinicia a aplicação de funções no Azure. Implantando atualizações também é reiniciado a aplicação de funções. |
| **Conjunto AzureWebJobStorage...**| Define o valor do `AzureWebJobStorage` definição da aplicação. Esta definição é necessária para as funções do Azure e é definida quando a aplicação de funções é criada no Azure. |
| **Começar** | Inicia uma aplicação de funções parado no Azure. | 
| **Iniciar transmissão em fluxo registos** | Inicia os registos de fluxo para a aplicação de funções no Azure. Utilize registos de transmissão em fluxo durante a solução de problemas remota no Azure se precisar de ver estas informações em tempo quase real. Para obter mais informações, consulte [registos de transmissão em fluxo](#streaming-logs). |
| **Parar** | É encerrado pendente uma aplicação de funções em execução no Azure. |
| **Parar a transmissão em fluxo registos** | Interrompe os registos de fluxo para a aplicação de funções no Azure. |
| **Botão de alternar como definição de ranhura** | Quando ativada, torna-se de que uma definição da aplicação persiste durante um bloco de implementação fornecido. |
| **Desinstalar as ferramentas de núcleo das funções do Azure** | Remove as ferramentas de núcleo de funções do Azure, que é necessário para a extensão. |
| **Carregar as definições locais...** | Carrega as definições do seu arquivo Settings para a aplicação de função escolhida no Azure. Se o ficheiro local está encriptado, é desencriptado, carregado e encriptado novamente. Se existirem definições com valores diferentes em ambos os locais, é-lhe perguntado para escolher como proceder. Certifique-se de que guardar as alterações ao ficheiro Settings antes de executar este comando. |
| **Consolidação de exibição no GitHub** | Mostra-lhe a consolidação mais recente numa implementação específica quando a aplicação function app está ligada a um repositório. |
| **Ver registos de implementação** | Mostra os registos para uma implementação específica à aplicação de funções no Azure. |

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as ferramentas de núcleo de funções do Azure, veja [código e testar as funções do Azure localmente](functions-run-local.md).

Para saber mais sobre o desenvolvimento das funções como bibliotecas de classes do .NET, veja [referência para programadores do funções do Azure c#](functions-dotnet-class-library.md). Este artigo também contém ligações para exemplos de como utilizar atributos para declarar os vários tipos de ligações compatíveis com as funções do Azure.    

[Extensão das Funções do Azure para o Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Ferramentas de núcleo das funções do Azure]: functions-run-local.md