---
title: Desenvolver funções Azure utilizando o Código do Estúdio Visual
description: Aprenda a desenvolver e testar funções Azure utilizando a extensão de Funções Azure para Código de Estúdio Visual.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 41a1a64be4823769f6bf23b251fec94fd68eb0f0
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484796"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Desenvolver funções Azure utilizando o Código do Estúdio Visual

A [Extensão das Funções do Azure para o Visual Studio Code] de Estúdio Visual permite-lhe desenvolver funções localmente e implantá-las para o Azure. Se esta experiência for a primeira com as Funções Azure, pode aprender mais na [introdução às Funções Azure.](functions-overview.md)

A extensão das Funções Azure proporciona estes benefícios:

* Editar, construir e executar funções no seu computador de desenvolvimento local.
* Publique o seu projeto Funções Azure diretamente para o Azure.
* Escreva as suas funções em várias línguas enquanto aproveita os benefícios do Código do Estúdio Visual.

A extensão pode ser usada com os seguintes idiomas, que têm suporte do tempo de execução Azure Functions a partir da versão 2. x:

* [C#compilado](functions-dotnet-class-library.md)
* <sup>*do</sup> [ C# guião](functions-reference-csharp.md)
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [python](functions-reference-python.md)

<sup>*</sup> Requer que [detetete te C# scripts como a tua linguagem padrão do projeto.](#c-script-projects)

Neste artigo, os exemplos estão atualmente disponíveis apenas para C# funções javaScript (Node.js) e biblioteca de classes.  

Este artigo fornece detalhes sobre como usar a extensão das Funções Azure para desenvolver funções e publicá-las no Azure. Antes de ler este artigo, deve [criar a sua primeira função utilizando](functions-create-first-function-vs-code.md)o Código do Estúdio Visual .

> [!IMPORTANT]
> Não misture o desenvolvimento local e o desenvolvimento do portal para uma única aplicação de função. Quando publica de um projeto local para uma aplicação de função, o processo de implementação substitui quaisquer funções que desenvolveu no portal.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e executar a extensão de [funções Azure Funções Azure]para Código de[Estúdio Visual,]deve cumprir estes requisitos:

* [Código de estúdio visual](https://code.visualstudio.com/) instalado numa das [plataformas suportadas.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Outros recursos de que necessita, como uma conta de armazenamento Azure, são criados na sua subscrição quando [publica usando o Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Pode desenvolver funções localmente e publicá-las no Azure sem ter de as iniciar e executar localmente. Para executar as suas funções localmente, terá de cumprir alguns requisitos adicionais, incluindo um download automático de Ferramentas Core funções do Azure. Para saber mais, consulte [requisitos adicionais para executar um projeto localmente](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Criar um projeto das Funções do Azure

A extensão Functions permite criar um projeto de aplicação de função, juntamente com a sua primeira função. Os seguintes passos mostram como criar uma função desencadeada pelo HTTP num novo projeto funções. [O gatilho HTTP](functions-bindings-http-webhook.md) é o modelo de gatilho de função mais simples para demonstrar.

1. A partir de **Azure: Funções,** selecione o ícone **criar função:**

    ![Criar uma função](./media/functions-develop-vs-code/create-function.png)

1. Selecione a pasta para o seu projeto de aplicação de funções e, em seguida, **selecione um idioma para o seu projeto de função**.

1. Se ainda não instalou as Ferramentas Core, é-lhe pedido que **selecione uma versão** das Ferramentas Core para instalar. Escolha a versão 2.x ou uma versão posterior. 

1. Selecione o modelo de função de **gatilho HTTP** ou pode selecionar **Skip por enquanto** para criar um projeto sem uma função. Pode sempre [adicionar uma função ao seu projeto](#add-a-function-to-your-project) mais tarde.

    ![Escolher o modelo do acionador HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Digite **httpExample** para o nome da função e selecione Enter e, em seguida, selecione a autorização **de função.** Este nível de autorização requer que forneça uma chave de [função](functions-bindings-http-webhook-trigger.md#authorization-keys) quando chama o ponto final da função.

    ![Selecione autorização de função](./media/functions-develop-vs-code/create-function-auth.png)

    Uma função é criada no seu idioma escolhido e no modelo para uma função desencadeada por HTTP.

    ![Modelo de função desencadeado por HTTP no Código do Estúdio Visual](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Ficheiros de projeto gerados

O modelo do projeto cria um projeto no seu idioma escolhido e instala dependências necessárias. Para qualquer idioma, o novo projeto tem estes ficheiros:

* **host.json**: Permite-lhe configurar o anfitrião funções. Estas definições aplicam-se quando está a executar funções localmente e quando as executa em Azure. Para mais informações, consulte [host.json reference](functions-host-json.md).

* **local.settings.json**: Mantém as definições utilizadas quando está a executar funções localmente. Estas definições só são utilizadas quando se executa as funções localmente. Para mais informações, consulte o ficheiro de [definições locais](#local-settings-file).

    >[!IMPORTANT]
    >Como o ficheiro local.settings.json pode conter segredos, você precisa excluí-lo do seu controlo de fonte de projeto.

Dependendo da sua língua, estes outros ficheiros são criados:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs arquivo de biblioteca de classe](functions-dotnet-class-library.md#functions-class-library-project) que implementa a função.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Um ficheiro pacote.json na pasta raiz.

* Uma pasta HttpExample que contém o ficheiro de [definição function.json](functions-reference-node.md#folder-structure) e o [ficheiro index.js,](functions-reference-node.md#exporting-a-function)um ficheiro Node.js que contém o código de função.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

Neste ponto, pode adicionar ligações de entrada e saída à sua função [modificando o ficheiro função.json](#add-a-function-to-your-project) ou [adicionando um parâmetro a uma C# função](#add-a-function-to-your-project)de biblioteca de classe .

Também pode [adicionar uma nova função ao seu projeto.](#add-a-function-to-your-project)

## <a name="install-binding-extensions"></a>Instalar as extensões de enlace

Com exceção dos gatilhos HTTP e temporizador, as encadernações são implementadas em pacotes de extensão. Deve instalar as embalagens de extensão para os gatilhos e encadernações que deles necessitem. O processo de instalação de extensões de encadernação depende da linguagem do seu projeto.

# <a name="c"></a>[C\#](#tab/csharp)

Execute o comando de [pacote de dotnet](/dotnet/core/tools/dotnet-add-package) na janela do Terminal para instalar as embalagens de extensão que precisa no seu projeto. O comando seguinte instala a extensão de armazenamento Azure, que implementa encadernações para armazenamento de blob, fila e mesa.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Adicione uma função ao seu projeto

Pode adicionar uma nova função a um projeto existente utilizando um dos modelos de gatilho de funções predefinidas. Para adicionar um novo gatilho de função, selecione F1 para abrir a paleta de comando e, em seguida, procure e execute as **funções azure do comando: Criar função**. Siga as instruções para escolher o seu tipo de gatilho e defina os atributos exigidos do gatilho. Se o gatilho necessitar de uma chave de acesso ou de uma cadeia de ligação para ligar a um serviço, prepare-o antes de criar o gatilho de função.

Os resultados desta ação dependem da linguagem do seu projeto:

# <a name="c"></a>[C\#](#tab/csharp)

Um C# novo arquivo de biblioteca de classes (.cs) é adicionado ao seu projeto.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Uma nova pasta é criada no projeto. A pasta contém um novo ficheiro function.json e o novo ficheiro de código JavaScript.

---

## <a name="add-input-and-output-bindings"></a>Adicionar encadernações de entrada e saída

Pode expandir a sua função adicionando encadernações de entrada e saída. O processo de adição de encadernações depende da linguagem do seu projeto. Para saber mais sobre encadernações, consulte o [Azure Functions desencadeia e encaderna conceitos.](functions-triggers-bindings.md)

Os seguintes exemplos ligam-se a uma fila de armazenamento chamada `outqueue`, onde a cadeia de ligação para a conta de armazenamento é definida na definição de aplicação `MyStorageConnection` em local.settings.json.

# <a name="c"></a>[C\#](#tab/csharp)

Atualize o método de função para adicionar o seguinte parâmetro à definição de método `Run`:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Este código requer que adicione a seguinte declaração `using`:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

O parâmetro `msg` é um tipo `ICollector<T>`, que representa uma coleção de mensagens que são escritas para uma ligação de saída quando a função completa. Adicione uma ou mais mensagens à coleção. Estas mensagens são enviadas para a fila quando a função termina.

Para saber mais, consulte a documentação [de encadernação](functions-bindings-storage-queue-output.md) de saída de armazenamento de fila.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

O Visual Studio Code permite-lhe adicionar ligações ao ficheiro function.json seguindo um conjunto conveniente de solicitações. Para criar uma ligação, clique à direita (Ctrl+clique no macOS) o ficheiro **função.json** na sua pasta de função e selecione **Adicionar a ligação:**

![Adicione uma ligação a uma função JavaScript existente ](media/functions-develop-vs-code/function-add-binding.png)

Seguem-se os exemplos de instruções para definir uma nova encadernação de saída de armazenamento:

| Mensagem | Valor | Descrição |
| -------- | ----- | ----------- |
| **Selecione direção de ligação** | `out` | A ligação é uma ligação de saída. |
| **Selecione encadernação com direção** | `Azure Queue Storage` | A encadernação é uma ligação de fila de armazenamento Azure. |
| **O nome usado para identificar esta ligação no seu código** | `msg` | Nome que identifique o parâmetro de ligação referenciado no seu código. |
| **A fila para a qual a mensagem será enviada** | `outqueue` | O nome da fila a que o encadernação escreve. Quando o nome da *fila* não existe, a ligação cria-o na primeira utilização. |
| **Selecione a definição de "local.settings.json"** | `MyStorageConnection` | O nome de uma definição de aplicação que contém a cadeia de ligação para a conta de armazenamento. A definição `AzureWebJobsStorage` contém a cadeia de ligação para a conta de armazenamento que criou com a aplicação de função. |

Neste exemplo, é adicionado o seguinte encadernação à matriz `bindings` no seu ficheiro fun.json:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Também pode adicionar a mesma definição de ligação diretamente à sua função.json.

No seu código de funcionamento, o `msg` encadernação é acedido a partir do `context`, como neste exemplo:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Para saber mais, consulte a referência de ligação de saída de [armazenamento de fila.](functions-bindings-storage-queue-output.md)

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publicar no Azure

O Visual Studio Code permite-lhe publicar o seu projeto Funções diretamente no Azure. No processo, vai criar uma aplicação de funções e recursos relacionados na sua subscrição do Azure. A aplicação de funções proporciona um contexto de execução para as suas funções. O projeto é empacotado e implementado na nova aplicação de funções na sua subscrição do Azure.

Quando publica do Visual Studio Code para uma nova aplicação de funções no Azure, é-lhe oferecida uma aplicação de função rápida para criar caminho e um caminho avançado. 

Quando publica a partir do Visual Studio Code, tira partido da tecnologia de [implementação zip.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Aplicação de função rápida cria

Quando escolhe + Criar uma nova aplicação de **função no Azure...** a extensão gera automaticamente valores para os recursos Azure necessários pela sua aplicação de função. Estes valores baseiam-se no nome da aplicação de função que escolher. Para um exemplo de utilização de predefinições para publicar o seu projeto numa nova aplicação de funções no Azure, consulte o [artigo Quickstart Do Código](functions-create-first-function-vs-code.md#publish-the-project-to-azure)do Estúdio Visual .

Se quiser fornecer nomes explícitos para os recursos criados, deve escolher o caminho de criação avançado.

### <a name="enable-publishing-with-advanced-create-options"></a>Publique um projeto para uma nova app de funções no Azure utilizando opções avançadas

Os seguintes passos publicam o seu projeto numa nova app de funções criada com opções avançadas de criação:

1. Na área **Azure: Funções,** selecione o ícone **'Implementar para Função App'.**

    ![Definições da Aplicação de funções](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Se não tiver assinado, é solicitado que **entre no Azure.** Também pode **Criar uma conta Azure gratuita.** Depois de iniciar sessão no navegador, volte ao Visual Studio Code.

1. Se tiver várias subscrições, **selecione uma subscrição** para a aplicação de funções e, em seguida, selecione **+ Criar nova aplicação de função no Azure... _Avançado._** Esta opção _Avançada_ dá-lhe mais controlo sobre os recursos que cria em Azure. 

1. Seguindo as instruções, forneça estas informações:

    | Mensagem | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione app de funções em Azure | Criar nova app de funções em Azure | No próximo pedido, digite um nome globalmente único que identifique a sua nova aplicação de funções e, em seguida, selecione Enter. Os carateres válidos para um nome de aplicação de funções são `a-z`, `0-9` e `-`. |
    | Selecione um OS | Windows | A aplicação de funções funciona no Windows. |
    | Selecione um plano de hospedagem | Plano de consumo | É utilizado um plano de [consumo](functions-scale.md#consumption-plan) sem servidores. |
    | Selecione um tempo de execução para a sua nova aplicação | A sua linguagem de projeto | O prazo deve corresponder ao projeto que está a publicar. |
    | Selecione um grupo de recursos para novos recursos | Criar novo grupo de recursos | No próximo pedido, digite um nome de grupo de recursos, como `myResourceGroup`, e, em seguida, selecione entrar. Também pode selecionar um grupo de recursos existente. |
    | Selecione uma conta de armazenamento | Criar nova conta de armazenamento | Na próxima solicitação, digite um nome globalmente único para a nova conta de armazenamento utilizada pela sua aplicação de função e, em seguida, selecione Enter. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e podem conter apenas números e letras minúsculas. Também pode selecionar uma conta existente. |
    | Selecione uma localização para novos recursos | Região | Selecione uma localização numa [região](https://azure.microsoft.com/regions/) próxima ou perto de outros serviços a que as suas funções acedem. |

    Uma notificação aparece após a criação da sua aplicação de função e o pacote de implementação é aplicado. Selecione **Ver Output** nesta notificação para visualizar os resultados da criação e implementação, incluindo os recursos Azure que criou.

## <a name="republish-project-files"></a>Republique os ficheiros do projeto

Quando configura uma [implementação contínua,](functions-continuous-deployment.md)a sua aplicação de funções no Azure é atualizada sempre que os ficheiros de origem forem atualizados na localização de origem conectada. Recomendamos a implementação contínua, mas também pode reeditar as atualizações de ficheiros do seu projeto a partir do Visual Studio Code.

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure.

1. No Visual Studio Code, selecione F1 para abrir a paleta de comando. Na paleta de comando, procure e selecione **Funções Azure: Implemente para função da aplicação**.

1. Se não tiver assinado, é solicitado que **entre no Azure.** Depois de iniciar sessão no navegador, volte ao Visual Studio Code. Se tiver várias subscrições, **selecione uma subscrição** que contenha a sua aplicação de função.

1. Selecione a sua aplicação de função existente no Azure. Quando for avisado sobre a sobreposição de todos os ficheiros na aplicação de funções, selecione **'Enviar'** para reconhecer o aviso e continuar.

O projeto é reconstruído, reembalado e enviado para o Azure. O projeto existente é substituído pelo novo pacote e a aplicação de funções reinicia.

## <a name="get-the-url-of-the-deployed-function"></a>Obtenha o URL da função implantada

Para ligar para uma função desencadeada pelo HTTP, necessita do URL da função quando é implantado na sua aplicação de função. Este URL inclui as teclas de [função necessárias](functions-bindings-http-webhook-trigger.md#authorization-keys). Pode utilizar a extensão para obter estes URLs para as suas funções implementadas.

1. Selecione F1 para abrir a paleta de comando e, em seguida, procure e execute as funções de comando **Azure: Copy Function URL**.

1. Siga as instruções para selecionar a sua aplicação de função no Azure e, em seguida, o gatilho HTTP específico que pretende invocar.

O URL de função é copiado para a área de sobre-ré, juntamente com as teclas necessárias passadas pelo parâmetro de consulta `code`. Utilize uma ferramenta HTTP para submeter pedidos post, ou um navegador para pedidos GET para a função remota.  

## <a name="run-functions-locally"></a>Executar funções localmente

A extensão Funções Azure permite-lhe executar um projeto Funções no seu computador de desenvolvimento local. O tempo de funcionamento local é o mesmo tempo de funcionamento que acolhe a sua aplicação de funções em Azure. As definições locais são lidas a partir do [ficheiro local.settings.json](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Requisitos adicionais para executar um projeto localmente

Para executar o seu projeto Funções localmente, deve cumprir estes requisitos adicionais:

* Instale a versão 2.x ou posterior das [Ferramentas Core funções do Azure](functions-run-local.md#v2). O pacote Core Tools é descarregado e instalado automaticamente quando iniciar o projeto localmente. As Ferramentas Core incluem todo o tempo de funcionamento das Funções Azure, pelo que o download e a instalação podem demorar algum tempo.

* Instale os requisitos específicos para a linguagem escolhida:

    | Linguagem | Requisito |
    | -------- | --------- |
    | **C#** | [C#extensão](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[.NET Core CLI ferramentas](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Debugger para extensão java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 ou mais tarde](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/) <sup>*</sup> |  
    | **python** | [Extensão python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) recomendado|

    <sup>*</sup> Versões LTS ativas e de Manutenção LTS (8.11.1 e 10.14.1 recomendadas).

### <a name="configure-the-project-to-run-locally"></a>Configure o projeto para executar localmente

O tempo de funcionamento das Funções utiliza uma conta de Armazenamento Azure internamente para todos os tipos de gatilho sem http e webhooks. Por isso, tem de definir a chave **Values.AzureWebJobsStorage** para uma cadeia de ligação à conta Azure Storage válida.

Esta secção utiliza a extensão de [armazenamento Azure para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) com o [Azure Storage Explorer](https://storageexplorer.com/) para ligar e recuperar a cadeia de ligação de armazenamento.

Para definir o fio de ligação da conta de armazenamento:

1. No Estúdio Visual, abra o **Cloud Explorer,** expanda **a Conta** de Armazenamento > a Sua Conta de **Armazenamento,** e depois selecione **Propriedades** e copie o valor de string de **ligação primária.**

2. No seu projeto, abra o ficheiro local.settings.json e delineie o valor da chave **AzureWebJobsStorage** para a cadeia de ligação que copiou.

3. Repita o passo anterior para adicionar chaves únicas à matriz **Valores** para quaisquer outras ligações exigidas pelas suas funções.

Para mais informações, consulte o ficheiro de [definições locais](#local-settings-file).

### <a name="debugging-functions-locally"></a>Funções de depuração localmente  

Para desinserir as suas funções, selecione F5. Se ainda não descarregou as [Ferramentas Core Tools][ferramentas centrais de funções azure]é-lhe pedido que o faça. Quando as Ferramentas Core são instaladas e em funcionamento, a saída é mostrada no Terminal. Isto é o mesmo que executar o comando `func host start` Core Tools do Terminal, mas com tarefas de construção adicionais e um debugger anexado.  

Quando o projeto estiver em execução, pode desencadear as suas funções como faria quando o projeto for implantado para o Azure. Quando o projeto está em execução em modo dedepura, os breakpoints são atingidos no Código do Estúdio Visual, como esperado.

O URL de pedido para os gatilhos HTTP é apresentado na saída do Terminal. As teclas de função dos gatilhos HTTP não são usadas quando um projeto está a funcionar localmente. Para mais informações, consulte [Estratégias para testar o seu código em Funções Azure](functions-test-a-function.md).  

Para saber mais, consulte [Trabalhar com funções azure Ferramentas nucleares][ferramentas centrais de funções azure].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por predefinição, estas definições não são migradas automaticamente quando o projeto é publicado para o Azure. Após a publicação dos acabamentos, é-lhe dada a opção de publicar definições de local.settings.json para a sua aplicação de funções em Azure. Para saber mais, consulte as definições de [aplicação Publicar](#publish-application-settings).

Os **valores em ConnectionStrings** nunca são publicados.

Os valores de definições de aplicação de função também podem ser lidos no seu código como variáveis ambientais. Para mais informações, consulte as secções de variáveis do Ambiente destes artigos de referência específicos para a língua:

* [C#pré-compilado](functions-dotnet-class-library.md#environment-variables)
* [C#script (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Definições de aplicação em Azure

As definições no ficheiro local.settings.json no seu projeto devem ser as mesmas que as definições de aplicação na aplicação de função em Azure. Quaisquer configurações que adicione a locais.configurações.json também devem ser adicionadas à aplicação de funções em Azure. Estas definições não são carregadas automaticamente quando publica o projeto. Da mesma forma, quaisquer configurações que crie na sua aplicação de função [no portal](functions-how-to-use-azure-function-app-settings.md#settings) devem ser descarregadas para o seu projeto local.

### <a name="publish-application-settings"></a>Publicar as definições de aplicação

A forma mais fácil de publicar as definições necessárias para a sua aplicação de funções no Azure é utilizar o link de **definições de Upload** que aparece após a publicação do seu projeto:

![Carregar definições de aplicação](./media/functions-develop-vs-code/upload-app-settings.png)

Também pode publicar definições utilizando as **funções Azure: Carregar** o comando de definição local na paleta de comando. Pode adicionar configurações individuais às definições de aplicação no Azure utilizando as **funções Azure: Adicionar o comando de definição nova.**

> [!TIP]
> Certifique-se de que guarda o ficheiro local.settings.json antes de o publicar.

Se o ficheiro local estiver encriptado, é desencriptado, publicado e encriptado novamente. Se houver configurações que têm valores contraditórios nos dois locais, é-lhe pedido que escolha como proceder.

Ver as definições de aplicações existentes no **Azure: Área de funções** expandindo a sua subscrição, a sua aplicação de funções e definições de **aplicação**.

![Ver definições de aplicativo de função no Código do Estúdio Visual](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Descarregue as definições do Azure

Se criou definições de aplicação no Azure, pode descarregá-las no ficheiro local.settings.json utilizando as **Funções Azure: Descarregue** o comando Definições Remotas.

Tal como acontece com o upload, se o ficheiro local for encriptado, é desencriptado, atualizado e encriptado novamente. Se houver configurações que têm valores contraditórios nos dois locais, é-lhe pedido que escolha como proceder.

## <a name="monitoring-functions"></a>Funções de monitorização

Quando [executa funções localmente,](#run-functions-locally)os dados de registo são transmitidos para a consola Terminal. Também pode obter dados de registo quando o seu projeto Functions estiver a funcionar numa aplicação de função no Azure. Pode ligar-se a registos de streaming em Azure para ver dados de registo quase em tempo real, ou pode ativar os Insights de Aplicação para uma compreensão mais completa de como a sua aplicação de função se está a comportar.

### <a name="streaming-logs"></a>Registos de streaming

Quando se está a desenvolver uma aplicação, é muitas vezes útil ver informação de registo em tempo quase real. Pode ver um fluxo de ficheiros de registo gerados pelas suas funções. Esta saída é um exemplo de registos de streaming para um pedido a uma função desencadeada pelo HTTP:

![Saída de registos de streaming para gatilho HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Para saber mais, consulte os [registos de streaming.](functions-monitoring.md#streaming-logs)

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Os registos de streaming suportam apenas uma instância do hospedeiro funções. Quando a sua função é dimensionada para várias instâncias, os dados de outras instâncias não são mostrados no fluxo de registo. [Live Metrics Stream](../azure-monitor/app/live-stream.md) in Application Insights suporta várias instâncias. Embora também em tempo quase real, a análise de streaming baseia-se em [dados amostrados.](functions-monitoring.md#configure-sampling)

### <a name="application-insights"></a>Application Insights

Recomendamos que monitorize a execução das suas funções integrando a sua aplicação de funções com Insights de Aplicação. Quando se cria uma aplicação de função no portal Azure, esta integração ocorre por defeito. Quando criar a sua aplicação de funções durante a publicação do Estúdio Visual, precisa de integrar os Insights de Aplicação.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Para saber mais, consulte as [Funções Monitor Azure](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# projetos de script

Por padrão, C# todos os projetos são criados como [ C# projetos compilados](functions-dotnet-class-library.md)de biblioteca de classes. Se preferir trabalhar C# com projetos de C# script, deve selecionar o script como o idioma padrão nas definições de extensão das Funções Azure:

1. Selecione ** > ** Preferências de **ficheiro > ** **Definições**.

1. Vá às **Definições** do utilizador > **extensões** > **funções Azure**.

1. Selecione **C#Script** a partir da **função Azure: Linguagem do projeto**.

Depois de completar estes passos, as chamadas feitas para as Ferramentas Core subjacentes incluem a opção `--csx`, que gera e publica C# ficheiros de projetoscript (.csx). Quando tiver esta linguagem padrão especificada, todos C# os projetos que cria padrão para projetos de script. Não é solicitado a escolher uma linguagem de projeto quando um padrão é definido. Para criar projetos noutros idiomas, tem de alterar esta definição ou removê-la do ficheiro de definições do utilizador.json. Depois de remover esta definição, é novamente solicitado a escolher a sua língua quando criar um projeto.

## <a name="command-palette-reference"></a>Referência da paleta de comando

A extensão Funções Azure fornece uma interface gráfica útil na área para interagir com as suas aplicações de função em Azure. A mesma funcionalidade também está disponível como comandos na paleta de comandos (F1). Estes comandos De Funções Azure estão disponíveis:

|Comando funções azure  | Descrição  |
|---------|---------|
|**Adicionar Novas Definições**  |  Cria uma nova configuração de aplicação em Azure. Para saber mais, consulte as definições de [aplicação Publicar](#publish-application-settings). Também poderá ser necessário [descarregar esta definição para as suas definições locais](#download-settings-from-azure). |
| **Configurar fonte de implantação** | Liga a sua aplicação de funções em Azure a um repositório git local. Para saber mais, consulte a [implantação contínua para funções Azure](functions-continuous-deployment.md). |
| **Ligue-se ao Repositório GitHub** | Liga a sua aplicação de função a um repositório GitHub. |
| **URL de função de cópia** | Obtém o URL remoto de uma função desencadeada por HTTP que está a funcionar em Azure. Para saber mais, consulte [Obtenha o URL da função implantada](#get-the-url-of-the-deployed-function). |
| **Criar app de funções em Azure** | Cria uma nova aplicação de funções na sua subscrição no Azure. Para saber mais, consulte a secção sobre como publicar numa nova aplicação de [funções no Azure](#publish-to-azure).        |
| **Desencriptar Definições** | Desencripta [as definições locais](#local-settings-file) que foram encriptadas pelas **funções do Azure: Configurações de encriptação**.  |
| **Eliminar app de funções** | Remove uma aplicação de função da sua subscrição no Azure. Quando não há outras aplicações no plano do App Service, é-lhe dada a opção de apagar isso também. Outros recursos, como contas de armazenamento e grupos de recursos, não são eliminados. Para remover todos os recursos, [deve,](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources)em vez disso, eliminar o grupo de recursos . O seu projeto local não foi afetado. |
|**Excluir Função**  | Remove uma função existente de uma aplicação de função em Azure. Como esta eliminação não afeta o seu projeto local, em vez disso considere remover a função localmente e, em seguida, [reeditar o seu projeto](#republish-project-files). |
| **Eliminar procuração** | Remove um proxy de Funções Azure da sua aplicação de funções em Azure. Para saber mais sobre proxies, consulte [Trabalhar com Funções Azure Proxies](functions-proxies.md). |
| **Excluir definição** | Elimina uma definição de aplicação de função em Azure. Esta eliminação não afeta as definições no ficheiro local.settings.json. |
| **Desligar de Repo**  | Remove a ligação contínua de [implantação](functions-continuous-deployment.md) entre uma aplicação de função no Azure e um repositório de controlo de fonte. |
| **Descarregue as definições remotas** | Descarregue as definições da aplicação de função escolhida em Azure para o seu ficheiro local.settings.json. Se o ficheiro local estiver encriptado, é desencriptado, atualizado e encriptado novamente. Se houver configurações que têm valores contraditórios nos dois locais, é-lhe pedido que escolha como proceder. Certifique-se de que guarda alterações no ficheiro local.definições.json antes de executar este comando. |
| **Editar definições** | Altera o valor de uma definição de aplicação de função existente no Azure. Este comando não afeta as definições no ficheiro local.definições.json.  |
| **Encriptar definições** | Encripta itens individuais na matriz de `Values` nas [definições locais](#local-settings-file). Neste ficheiro, `IsEncrypted` também está definido para `true`, o que especifica que o tempo de execução local irá desencriptar as definições antes de as utilizar. Criptografe as definições locais para reduzir o risco de fuga de informação valiosa. No Azure, as definições de aplicação são sempre armazenadas encriptadas. |
| **Executar função agora** | Inicia manualmente uma [função acionada pelo temporizador](functions-bindings-timer.md) em Azure. Este comando é utilizado para testes. Para saber mais sobre o desencadeamento de funções não HTTP em Azure, consulte [Manualmente executar uma função não ativada](functions-manually-run-non-http.md)por HTTP . |
| **Inicializar projeto para uso com código VS** | Adiciona os ficheiros de projeto visual studio code necessários a um projeto de Funções existentes. Use este comando para trabalhar com um projeto que criou utilizando ferramentas core. |
| **Instalar ou atualizar ferramentas centrais de funções azure** | Instala ou atualiza [Ferramentas centrais de funções azure]que é usada para executar funções localmente. |
| **Reimplantação**  | Permite-lhe reimplantar ficheiros de projeto saem de um repositório Git ligado para uma implementação específica no Azure. Para republicar as atualizações locais do Visual Studio Code, [republique o seu projeto.](#republish-project-files) |
| **Renomear Definições** | Altera o nome-chave de uma definição de aplicação de função existente no Azure. Este comando não afeta as definições no ficheiro local.definições.json. Depois de mudar o nome das definições em Azure, deverá [descarregar essas alterações para o projeto local](#download-settings-from-azure). |
| **Reiniciar** | Reinicia a aplicação de funções em Azure. A implementação de atualizações também reinicia a aplicação de funções. |
| **Definir AzureWebJobsStorage**| Define o valor da definição de aplicação `AzureWebJobsStorage`. Esta definição é exigida pelas Funções Azure. É definido quando uma aplicação de função é criada em Azure. |
| **Começar** | Inicia uma aplicação de função parada em Azure. |
| **Iniciar registos de streaming** | Inicia os registos de streaming da aplicação de função em Azure. Utilize registos de streaming durante a resolução remota de problemas em Azure se precisar de ver informações de registo em tempo quase real. Para saber mais, consulte os [registos de streaming.](#streaming-logs) |
| **Parar** | Detém uma aplicação de função que está a funcionar em Azure. |
| **Parar os registos de streaming** | Para os registos de streaming da aplicação de função em Azure. |
| **Alternar como definição de ranhura** | Quando ativado, assegura-se de que persiste uma definição de aplicação para uma determinada ranhura de implantação. |
| **Desinstalar ferramentas centrais de funções azure** | Remove as ferramentas core funções do Azure, que são exigidas pela extensão. |
| **Carregar configurações locais** | Faça upload das definições do ficheiro local.settings.json para a aplicação de função escolhida em Azure. Se o ficheiro local estiver encriptado, é desencriptado, carregado e encriptado novamente. Se houver configurações que têm valores contraditórios nos dois locais, é-lhe pedido que escolha como proceder. Certifique-se de que guarda alterações no ficheiro local.definições.json antes de executar este comando. |
| **Ver comprometer-se no GitHub** | Mostra-lhe o mais recente compromisso numa implementação específica quando a sua aplicação de funções está ligada a um repositório. |
| **Ver Registos de Implementação** | Mostra-lhe os registos para uma implementação específica da aplicação de funções em Azure. |

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as Ferramentas Nucleares das Funções Azure, consulte [Trabalhar com ferramentas nucleares de funções azure](functions-run-local.md).

Para saber mais sobre o desenvolvimento de funções como bibliotecas de classes .NET, consulte a referência do desenvolvedor de [ C# Funções Azure](functions-dotnet-class-library.md). Este artigo também fornece links para exemplos de como usar atributos para declarar os vários tipos de encadernações suportadas pelas Funções Azure.

[Extensão das Funções do Azure para o Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Ferramentas centrais de funções azure]: functions-run-local.md
