---
title: Desenvolver as Funções do Azure com o Visual Studio Code
description: Aprenda a desenvolver e testar funções Azure utilizando a extensão de Funções Azure para Código do Estúdio Visual.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: c2869b2b30722495523a9f0dfb2d70a17a205854
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871278"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Desenvolver as Funções do Azure com o Visual Studio Code

A [extensão de Funções Azure para Código de Estúdio Visual] permite-lhe desenvolver localmente funções e implantá-las para Azure. Se esta experiência for a sua primeira com funções Azure, pode aprender mais em [Uma introdução às Funções Azure](functions-overview.md).

A extensão das funções Azure proporciona estes benefícios:

* Editar, construir e executar funções no seu computador de desenvolvimento local.
* Publique o seu projeto Azure Functions diretamente para a Azure.
* Escreva as suas funções em vários idiomas enquanto aproveita os benefícios do Código do Estúdio Visual.

A extensão pode ser utilizada com os seguintes idiomas, que são suportados pelo tempo de execução das Funções Azure, começando com a versão 2.x:

* [C# compilado](functions-dotnet-class-library.md)
* [Roteiro C#](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Requer que [estabeleça o script C# como o seu idioma de projeto predefinido](#c-script-projects).

Neste artigo, os exemplos estão atualmente disponíveis apenas para funções de biblioteca de classes JavaScript (Node.js) e C#.  

Este artigo fornece detalhes sobre como usar a extensão de Funções Azure para desenvolver funções e publicá-las para Azure. Antes de ler este artigo, deverá [criar a sua primeira função utilizando o Código de Estúdio Visual](./create-first-function-vs-code-csharp.md).

> [!IMPORTANT]
> Não misture desenvolvimento local e desenvolvimento de portal para uma única aplicação de função. Ao publicar de um projeto local para uma aplicação de função, o processo de implementação substitui quaisquer funções que desenvolveu no portal.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e executar a [extensão Azure Functions][Azure Functions para o Código do Estúdio Visual,]tem de cumprir estes requisitos:

* [Código visual do estúdio](https://code.visualstudio.com/) instalado numa das [plataformas suportadas.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Outros recursos de que necessita, como uma conta de armazenamento Azure, são criados na sua subscrição quando [publica usando o Código do Estúdio Visual.](#publish-to-azure) 

### <a name="run-local-requirements"></a>Executar requisitos locais

Estes pré-requisitos só são necessários para [executar e depurar as suas funções localmente.](#run-functions-locally) Não são obrigados a criar ou publicar projetos para as Funções Azure.

# <a name="c"></a>[C\#](#tab/csharp)

+ A versão 2.x ou mais tarde do [Azure Functions Core Tools.](functions-run-local.md#install-the-azure-functions-core-tools) O pacote Core Tools é descarregado e instalado automaticamente quando inicia o projeto localmente. As Ferramentas Core incluem todo o tempo de funcionamento das Funções Azure, pelo que o download e a instalação podem demorar algum tempo.

+ A [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para o Visual Studio Code. 

+ [.NET Core CLI ferramentas](/dotnet/core/tools/?tabs=netcore2x).  

# <a name="java"></a>[Java](#tab/java)

+ A versão 2.x ou mais tarde do [Azure Functions Core Tools.](functions-run-local.md#install-the-azure-functions-core-tools) O pacote Core Tools é descarregado e instalado automaticamente quando inicia o projeto localmente. As Ferramentas Core incluem todo o tempo de funcionamento das Funções Azure, pelo que o download e a instalação podem demorar algum tempo.

+ [Debugger para a extensão de Java.](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)

+ [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) recomendado. Para outras versões suportadas, consulte [as versões Java](functions-reference-java.md#java-versions).

+ [Maven 3 ou mais tarde](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ A versão 2.x ou mais tarde do [Azure Functions Core Tools.](functions-run-local.md#install-the-azure-functions-core-tools) O pacote Core Tools é descarregado e instalado automaticamente quando inicia o projeto localmente. As Ferramentas Core incluem todo o tempo de funcionamento das Funções Azure, pelo que o download e a instalação podem demorar algum tempo.

+ [Node.js](https://nodejs.org/), Versões LTS ativas e de manutenção (10.14.1 recomendado). Utilize o `node --version` comando para verificar a sua versão. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ A versão 2.x ou mais tarde do [Azure Functions Core Tools.](functions-run-local.md#install-the-azure-functions-core-tools) O pacote Core Tools é descarregado e instalado automaticamente quando inicia o projeto localmente. As Ferramentas Core incluem todo o tempo de funcionamento das Funções Azure, pelo que o download e a instalação podem demorar algum tempo.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows) recomendado. Para obter informações sobre a versão, consulte as [versões PowerShell](functions-reference-powershell.md#powershell-versions).

+ Ambos [.NET Core 3.1 runtime](https://dotnet.microsoft.com/download) e [.NET Core 2.1 runtime](https://dotnet.microsoft.com/download/dotnet/2.1)  

+ A [extensão PowerShell para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

# <a name="python"></a>[Python](#tab/python)

+ A versão 2.x ou mais tarde do [Azure Functions Core Tools.](functions-run-local.md#install-the-azure-functions-core-tools) O pacote Core Tools é descarregado e instalado automaticamente quando inicia o projeto localmente. As Ferramentas Core incluem todo o tempo de funcionamento das Funções Azure, pelo que o download e a instalação podem demorar algum tempo.

+ [Python 3.x](https://www.python.org/downloads/). Para obter informações sobre a versão, consulte as [versões Python](functions-reference-python.md#python-version) pelo tempo de funcionamento das Funções Azure.

+ [Extensão python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Código de Estúdio Visual.

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Criar um projeto das Funções do Azure

A extensão funções permite criar um projeto de aplicação de função, juntamente com a sua primeira função. Os passos a seguir mostram como criar uma função desencadeada por HTTP num novo projeto de Funções. [O gatilho HTTP](functions-bindings-http-webhook.md) é o modelo de gatilho de função mais simples para demonstrar.

1. A partir de **Azure: Funções**, selecione o ícone **Criar Função:**

    ![Criar uma função](./media/functions-develop-vs-code/create-function.png)

1. Selecione a pasta para o seu projeto de aplicação de função e, em seguida, **selecione um idioma para o seu projeto de função**.

1. Selecione o modelo de função **do gatilho HTTP** ou pode selecionar Skip por **agora** para criar um projeto sem uma função. Pode sempre [adicionar uma função ao seu projeto](#add-a-function-to-your-project) mais tarde.

    ![Escolher o modelo do acionador HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Digite **HttpExample** para o nome da função e selecione Enter e, em seguida, selecione a autorização **de função.** Este nível de autorização requer que forneça uma [chave de função](functions-bindings-http-webhook-trigger.md#authorization-keys) quando ligar para o ponto final da função.

    ![Selecione a autorização de função](./media/functions-develop-vs-code/create-function-auth.png)

    Uma função é criada no seu idioma escolhido e no modelo para uma função desencadeada por HTTP.

    ![Modelo de função acionado por HTTP no Código do Estúdio Visual](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Ficheiros de projeto gerados

O modelo do projeto cria um projeto no seu idioma escolhido e instala dependências necessárias. Para qualquer língua, o novo projeto tem estes ficheiros:

* **host.js:** Permite-lhe configurar o anfitrião funções. Estas definições aplicam-se quando está a executar funções localmente e quando as executa em Azure. Para mais informações, consulte [host.jsa referência.](functions-host-json.md)

* **local.settings.jsem**: Mantém as definições utilizadas quando está a executar funções localmente. Estas definições são utilizadas apenas quando estiver a executar funções localmente. Para obter mais informações, consulte [o ficheiro de definições locais](#local-settings-file).

    >[!IMPORTANT]
    >Como as local.settings.jsem ficheiro podem conter segredos, tens de o excluir do controlo de fontes do teu projeto.

Dependendo do seu idioma, estes outros ficheiros são criados:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs ficheiro de biblioteca de classes](functions-dotnet-class-library.md#functions-class-library-project) que implementa a função.

# <a name="java"></a>[Java](#tab/java)

+ Um ficheiro pom.xml na pasta raiz que define os parâmetros de projeto e implantação, incluindo as dependências do projeto e a [versão Java.](functions-reference-java.md#java-versions) O pom.xml também contém informações sobre os recursos Azure que são criados durante uma implantação.   

+ Um [ficheiro .java Funções](functions-reference-java.md#triggers-and-annotations) no seu caminho src que implementa a função.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Uma package.jsno ficheiro na pasta raiz.

* Uma pasta HttpExample que contém o [function.jsno ficheiro de definição](functions-reference-node.md#folder-structure) e no ficheiro [index.js](functions-reference-node.md#exporting-a-function), um ficheiro Node.js que contém o código de função.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* Uma pasta HttpExample que contém o [function.jsno ficheiro de definição](functions-reference-powershell.md#folder-structure) e no ficheiro run.ps1, que contém o código de função.
 
# <a name="python"></a>[Python](#tab/python)
    
* Um ficheiro de requirements.txt de nível de projeto que lista pacotes exigidos pelas Funções.
    
* Uma pasta HttpExample que contém o [function.jsno ficheiro de definição](functions-reference-python.md#folder-structure) e o ficheiro \_ \_ \_ \_ init .py, que contém o código de função.

---

Neste ponto, pode [adicionar entradas e ligações de saída](#add-input-and-output-bindings) à sua função. Também pode [adicionar uma nova função ao seu projeto.](#add-a-function-to-your-project)

## <a name="install-binding-extensions"></a>Instalar as extensões de enlace

Com exceção dos gatilhos HTTP e timer, as ligações são implementadas em pacotes de extensão. Tem de instalar as embalagens de extensão para os gatilhos e encadernações que os necessitem. O processo de instalação de extensões de ligação depende da linguagem do seu projeto.

# <a name="c"></a>[C\#](#tab/csharp)

Execute o comando [do pacote de adicionar dotnet](/dotnet/core/tools/dotnet-add-package) na janela Terminal para instalar os pacotes de extensão que necessita no seu projeto. O seguinte comando instala a extensão de Armazenamento Azure, que implementa encadernações para o armazenamento de Blob, Queue e Table.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Adicione uma função ao seu projeto

Pode adicionar uma nova função a um projeto existente utilizando um dos modelos de gatilho de Funções predefinidas. Para adicionar um novo gatilho de função, selecione F1 para abrir a paleta de comando e, em seguida, procure e execute as funções de comando **Azure: Criar Função**. Siga as indicações para escolher o tipo de gatilho e defina os atributos necessários do gatilho. Se o seu gatilho necessitar de uma chave de acesso ou de uma cadeia de ligação para ligar a um serviço, prepare-o antes de criar o gatilho da função.

Os resultados desta ação dependem da linguagem do seu projeto:

# <a name="c"></a>[C\#](#tab/csharp)

Um novo ficheiro da biblioteca de classes C# (.cs) é adicionado ao seu projeto.

# <a name="java"></a>[Java](#tab/java)

Um novo ficheiro Java (.java) é adicionado ao seu projeto.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Uma nova pasta é criada no projeto. A pasta contém um novo function.jsno ficheiro e o novo ficheiro de código JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Uma nova pasta é criada no projeto. A pasta contém um novo function.jsno ficheiro e o novo ficheiro de código PowerShell.

# <a name="python"></a>[Python](#tab/python)

Uma nova pasta é criada no projeto. A pasta contém um novo function.jsno ficheiro e o novo ficheiro de código Python.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Ligar aos serviços

Pode ligar a sua função a outros serviços Azure adicionando encadernações de entrada e saída. As ligações ligam a sua função a outros serviços sem ter de escrever o código de ligação. O processo de adição de encadernações depende da linguagem do seu projeto. Para saber mais sobre encadernações, consulte [a Azure Functions triggers e encaderna os conceitos.](functions-triggers-bindings.md)

Os exemplos a seguir ligam-se a uma fila de armazenamento denominada `outqueue` , onde o fio de ligação para a conta de armazenamento é definido na definição de `MyStorageConnection` aplicação local.settings.jsligado.

# <a name="c"></a>[C\#](#tab/csharp)

Atualizar o método de função para adicionar o seguinte parâmetro à definição do `Run` método:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

O `msg` parâmetro é um `ICollector<T>` tipo, que representa uma coleção de mensagens que são escritas para uma ligação de saída quando a função termina. O seguinte código adiciona uma mensagem à coleção:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 As mensagens são enviadas para a fila quando a função estiver concluída.

Para saber mais, consulte a documentação do [artigo de referência de ligação de saída de armazenamento da Fila.](functions-bindings-storage-queue-output.md?tabs=csharp) Para saber mais em geral quais as ligações que podem ser adicionadas a uma função, consulte [adicionar ligações a uma função existente em Funções Azure](add-bindings-existing-function.md?tabs=csharp). 

# <a name="java"></a>[Java](#tab/java)

Atualizar o método de função para adicionar o seguinte parâmetro à definição do `Run` método:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

O `msg` parâmetro é um `OutputBinding<T>` tipo, onde está uma corda que é `T` escrita para uma ligação de saída quando a função termina. O seguinte código define a mensagem na ligação de saída:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Esta mensagem é enviada para a fila quando a função estiver concluída.

Para saber mais, consulte a documentação do [artigo de referência de ligação de saída de armazenamento da Fila.](functions-bindings-storage-queue-output.md?tabs=java) Para saber mais em geral quais as ligações que podem ser adicionadas a uma função, consulte [adicionar ligações a uma função existente em Funções Azure](add-bindings-existing-function.md?tabs=java). 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

No seu código de função, a `msg` ligação é acedida a partir do `context` , como neste exemplo:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Esta mensagem é enviada para a fila quando a função estiver concluída.

Para saber mais, consulte a documentação do [artigo de referência de ligação de saída de armazenamento da Fila.](functions-bindings-storage-queue-output.md?tabs=javascript) Para saber mais em geral quais as ligações que podem ser adicionadas a uma função, consulte [adicionar ligações a uma função existente em Funções Azure](add-bindings-existing-function.md?tabs=javascript). 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Esta mensagem é enviada para a fila quando a função estiver concluída.

Para saber mais, consulte a documentação do [artigo de referência de ligação de saída de armazenamento da Fila.](functions-bindings-storage-queue-output.md?tabs=powershell) Para saber mais em geral quais as ligações que podem ser adicionadas a uma função, consulte [adicionar ligações a uma função existente em Funções Azure](add-bindings-existing-function.md?tabs=powershell). 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Atualize a `Main` definição para adicionar um parâmetro de saída `msg: func.Out[func.QueueMessage]` para que a definição se pareça com o seguinte exemplo:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

O seguinte código adiciona dados de cadeia do pedido à fila de saída:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Esta mensagem é enviada para a fila quando a função estiver concluída.

Para saber mais, consulte a documentação do [artigo de referência de ligação de saída de armazenamento da Fila.](functions-bindings-storage-queue-output.md?tabs=python) Para saber mais em geral quais as ligações que podem ser adicionadas a uma função, consulte [adicionar ligações a uma função existente em Funções Azure](add-bindings-existing-function.md?tabs=python). 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publicar no Azure

O Código do Estúdio Visual permite-lhe publicar o seu projeto Funções diretamente para o Azure. No processo, vai criar uma aplicação de funções e recursos relacionados na sua subscrição do Azure. A aplicação de funções proporciona um contexto de execução para as suas funções. O projeto é empacotado e implementado na nova aplicação de funções na sua subscrição do Azure.

Quando publica do Visual Studio Code para uma nova aplicação de função em Azure, pode escolher uma aplicação de função rápida criar caminho usando predefinições ou um caminho avançado, onde você tem mais controlo sobre os recursos remotos criados. 

Ao publicar a partir do Código do Estúdio Visual, aproveita-se a tecnologia [zip deploy.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>App de função rápida criar

Quando escolhe **+ Criar uma nova aplicação de função em Azure...** a extensão gera automaticamente valores para os recursos Azure necessários pela sua aplicação de função. Estes valores baseiam-se no nome da aplicação de função que escolher. Para um exemplo de utilização de incumprimentos para publicar o seu projeto numa nova aplicação de função em Azure, consulte o artigo de arranque rápido do [Código do Estúdio Visual](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure).

Se quiser fornecer nomes explícitos para os recursos criados, deve escolher o caminho avançado de criação.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publique um projeto para uma nova aplicação de função em Azure usando opções avançadas

Os seguintes passos publicam o seu projeto para uma nova app de função criada com opções avançadas de criação:

1. Na palete de comando, **insira funções Azure: Implementar para a aplicação de função**.

1. Se não tiver assinado, é solicitado que entre **em Azure.** Também pode **Criar uma conta Azure gratuita.** Depois de iniciar sessão no navegador, volte ao Código do Estúdio Visual.

1. Se tiver várias subscrições, **selecione uma subscrição** para a aplicação de função e, em seguida, selecione **+ Criar nova aplicação de função em Azure... _Avançado._** Esta opção _Avançada_ dá-lhe mais controlo sobre os recursos que cria no Azure. 

1. Seguindo as indicações, forneça estas informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione app de função em Azure | Criar nova aplicação de funções em Azure | Na próxima posição, escreva um nome globalmente único que identifique a sua nova aplicação de função e, em seguida, selecione Enter. Os carateres válidos para um nome de aplicação de funções são `a-z`, `0-9` e `-`. |
    | Selecione um SISTEMA | Windows | A aplicação de função é executado no Windows. |
    | Selecione um plano de hospedagem | Plano de consumo | É utilizado um alojamento sem [servidor.](consumption-plan.md) |
    | Selecione um tempo de execução para a sua nova aplicação | A sua linguagem de projeto | O tempo de execução deve coincidir com o projeto que vai publicar. |
    | Selecione um grupo de recursos para novos recursos | Criar novo grupo de recursos | Na próxima introdução, escreva um nome de grupo de recursos, `myResourceGroup` tipo, e, em seguida, selecione insira. Também pode selecionar um grupo de recursos existente. |
    | Selecione uma conta de armazenamento | Criar nova conta de armazenamento | Na próxima chamada, escreva um nome globalmente único para a nova conta de armazenamento utilizada pela sua aplicação de função e, em seguida, selecione Enter. Os nomes das contas de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Também pode selecionar uma conta existente. |
    | Selecione uma localização para novos recursos | region | Selecione uma localização numa [região](https://azure.microsoft.com/regions/) perto de si ou perto de outros serviços a que as suas funções acedam. |

    Uma notificação aparece após a criação da sua aplicação de função e o pacote de implementação é aplicado. Selecione **Ver Saída** nesta notificação para ver os resultados da criação e implementação, incluindo os recursos Azure que criou.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>Obtenha o URL de uma função http desencadeada em Azure

Para chamar uma função desencadeada por HTTP de um cliente, precisa do URL da função quando é implantado na sua aplicação de função. Este URL inclui todas as teclas de função necessárias. Pode utilizar a extensão para obter estes URLs para as suas funções implantadas. Se apenas pretender executar a função remota em Azure, [utilize agora a função Executar](#run-functions-in-azure) a funcionalidade da extensão.

1. Selecione F1 para abrir a paleta de comando e, em seguida, procure e execute as **funções de Azure de comando: Copiar URL de função**.

1. Siga as instruções para selecionar a sua aplicação de função em Azure e, em seguida, o gatilho HTTP específico que pretende invocar.

O URL de função é copiado para a área de transferência, juntamente com as teclas necessárias passadas pelo `code` parâmetro de consulta. Utilize uma ferramenta HTTP para submeter pedidos POST ou um browser para pedidos GET para a função remota.  

Ao obter o URL das funções em Azure, a extensão utiliza a sua conta Azure para recuperar automaticamente as teclas de que necessita para iniciar a função. [Saiba mais sobre as teclas de acesso à função.](security-concepts.md#function-access-keys) Iniciar funções ativadas não HTTP requer a utilização da tecla de administração.

## <a name="republish-project-files"></a>Reempele os ficheiros do projeto

Quando configurar [a implementação contínua,](functions-continuous-deployment.md)a sua aplicação de função no Azure é atualizada quando atualiza ficheiros de origem no local de origem conectado. Recomendamos a implementação contínua, mas também pode republicar as atualizações de ficheiros do seu projeto a partir do Código do Estúdio Visual.

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Executar funções

A extensão Azure Functions permite executar funções individuais, quer no seu projeto no seu computador de desenvolvimento local, quer na sua subscrição Azure. 

Para funções de gatilho HTTP, a extensão chama o ponto final HTTP. Para outros tipos de gatilhos, chama apis do administrador para iniciar a função. O corpo de mensagem do pedido enviado para a função depende do tipo de gatilho. Quando um gatilho requer dados de teste, é-lhe solicitado que introduza dados num formato JSON específico.

### <a name="run-functions-in-azure"></a>Executar funções em Azure

Para executar uma função em Azure a partir do Código de Estúdio Visual. 

1. Na palete de comando, **insira funções Azure: Execute a função agora** e escolha a sua subscrição Azure. 

1. Escolha a sua aplicação de função em Azure da lista. Se não vir a sua aplicação de função, certifique-se de que está inscrito na subscrição correta. 

1. Escolha a função que pretende executar a partir da lista e digite o corpo de mensagem do pedido no **formulário de pedido de enterr .** Prima Insira para enviar esta mensagem de pedido para a sua função. O texto predefinido no **corpo de pedido de introdução** deve indicar o formato do corpo. Se a sua aplicação de função não tiver funções, é mostrado um erro de notificação com este erro. 

1. Quando a função executa em Azure e devolve uma resposta, uma notificação é levantada no Código do Estúdio Visual.
 
Também pode executar a sua função a partir da área **Azure: Funções** clicando à direita (clicando em Mac) a função que pretende executar a partir da sua aplicação de função na sua subscrição Azure e escolhendo **executar função agora...**.

Ao executar funções em Azure, a extensão utiliza a sua conta Azure para recuperar automaticamente as teclas de que necessita para iniciar a função. [Saiba mais sobre as teclas de acesso à função.](security-concepts.md#function-access-keys) Iniciar funções ativadas não HTTP requer a utilização da tecla de administração.

### <a name="run-functions-locally"></a>Executar funções localmente

O tempo de execução local é o mesmo tempo de execução que acolhe a sua aplicação de função em Azure. As definições locais são lidas a partir do [local.settings.jsno ficheiro](#local-settings-file). Para executar o seu projeto Funções localmente, deve cumprir [requisitos adicionais.](#run-local-requirements)

#### <a name="configure-the-project-to-run-locally"></a>Configure o projeto para executar localmente

O tempo de execução das Funções utiliza uma conta de Armazenamento Azure internamente para todos os tipos de gatilhos que não http e webhooks. Por isso, é necessário definir a tecla **Values.AzureWebJobsStorage** para uma cadeia de ligação de conta Azure Storage válida.

Esta secção utiliza a [extensão de armazenamento Azure para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) com O Explorador de Armazenamento [Azure](https://storageexplorer.com/) para ligar e recuperar a cadeia de ligação de armazenamento.

Para definir o fio de ligação da conta de armazenamento:

1. No Estúdio Visual, abra **o Cloud Explorer,** expanda a **conta de armazenamento** da sua conta de  >  **armazenamento** e, em seguida, selecione **Propriedades** e copie o valor da cadeia **de ligação primária.**

2. No seu projeto, abra a local.settings.jsem arquivo e descreva o valor da chave **AzureWebJobsStorage** para a cadeia de ligação que copiou.

3. Repita o passo anterior para adicionar **teclas únicas** à matriz Valores para quaisquer outras ligações necessárias às suas funções.

Para obter mais informações, consulte [o ficheiro de definições locais](#local-settings-file).

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Debug funciona localmente  

Para depurar as suas funções, selecione F5. Se ainda não descarregou as Ferramentas Principais [do Core Tools][Azure Functions,]é-lhe pedido que o faça. Quando as Ferramentas Core são instaladas e em funcionamento, a saída é mostrada no Terminal. Isto é o mesmo que executar o `func host start` comando Core Tools a partir do Terminal, mas com tarefas de construção extra e um depurado anexado.  

Quando o projeto estiver em execução, pode utilizar a **função executar agora...** função da extensão para desencadear as suas funções como faria quando o projeto é implantado para Azure. Com o projeto em execução em modo depuração, os pontos de rutura são atingidos no Código do Estúdio Visual, como seria de esperar. 

1. Na palete de comando, insira **funções Azure: Execute a função agora** e escolha **projeto local**. 

1. Escolha a função que pretende executar no seu projeto e digite o corpo de mensagens do pedido no **corpo de pedido de Enter**. Prima Insira para enviar esta mensagem de pedido para a sua função. O texto predefinido no **corpo de pedido de introdução** deve indicar o formato do corpo. Se a sua aplicação de função não tiver funções, é mostrado um erro de notificação com este erro. 

1. Quando a função é executado localmente e após a resposta ser recebida, uma notificação é levantada no Código do Estúdio Visual. As informações sobre a execução da função são mostradas no painel **terminal.**

As funções de funcionamento local não requerem a utilização de chaves. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por padrão, estas definições não são migradas automaticamente quando o projeto é publicado para a Azure. Após a publicação de acabamentos, é-lhe dada a opção de publicar definições a partir de local.settings.jspara a sua aplicação de função em Azure. Para saber mais, consulte  [as definições de aplicação da Publicação](#publish-application-settings).

Os **valores em ConnectionStrings** nunca são publicados.

Os valores de definição de aplicação de função também podem ser lidos no seu código como variáveis ambientais. Para obter mais informações, consulte as secções de variáveis ambientais destes artigos de referência específicos da língua:

* [C# pré-compreitado](functions-dotnet-class-library.md#environment-variables)
* [Script C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Definições de aplicação em Azure

As definições no local.settings.jsem ficheiro no seu projeto devem ser as mesmas que as definições de aplicação na aplicação de função em Azure. Quaisquer definições que adicione a local.settings.jstambém devem adicionar à aplicação de função em Azure. Estas definições não são carregadas automaticamente quando publica o projeto. Da mesma forma, quaisquer configurações que crie na sua aplicação de função [no portal](functions-how-to-use-azure-function-app-settings.md#settings) devem ser descarregadas para o seu projeto local.

### <a name="publish-application-settings"></a>Publicar definições de aplicações

A forma mais fácil de publicar as definições necessárias para a sua aplicação de função no Azure é utilizar o link **de definições de upload** que aparece após a publicação do seu projeto:

![Definires de aplicações de upload](./media/functions-develop-vs-code/upload-app-settings.png)

Também pode publicar definições utilizando as **funções Azure: Carregar** o comando De Definição Local na paleta de comando. Pode adicionar definições individuais às definições de aplicação em Azure utilizando as **funções Azure: Adicionar o comando De Nova Definição.**

> [!TIP]
> Certifique-se de guardar o seu local.settings.jsarquivado antes de publicá-lo.

Se o ficheiro local for encriptado, é desencriptado, publicado e encriptado novamente. Se houver configurações que tenham valores contraditórios nos dois locais, é-lhe pedido que escolha como proceder.

Ver as definições de aplicações existentes na área **Azure: Funções** expandindo a sua subscrição, a sua aplicação de função e **as Definições de Aplicação**.

![Ver definições de aplicativo de função no Código do Estúdio Visual](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Baixar definições a partir de Azure

Se criou as definições de aplicações no Azure, pode descarregá-las para o seu local.settings.jsno ficheiro utilizando o comando **Azure Functions: Descarregue** o comando Definições Remotas.

Tal como no upload, se o ficheiro local for encriptado, é desencriptado, atualizado e encriptado novamente. Se houver configurações que tenham valores contraditórios nos dois locais, é-lhe pedido que escolha como proceder.

## <a name="monitoring-functions"></a>Funções de monitorização

Quando [executar funções locais,](#run-functions-locally)os dados de registo são transmitidos para a consola Terminal. Também pode obter dados de registo quando o seu projeto Funções está a decorrer numa aplicação de função em Azure. Pode ligar-se a registos de streaming em Azure para ver dados de registo em tempo real, ou pode ativar o Application Insights para uma compreensão mais completa de como a sua aplicação de função se está a comportar.

### <a name="streaming-logs"></a>Registos de transmissão em fluxo

Quando se está a desenvolver uma aplicação, é muitas vezes útil ver informações de registo em tempo quase real. Pode ver um fluxo de ficheiros de registo a ser gerado pelas suas funções. Esta saída é um exemplo de registos de streaming para um pedido a uma função desencadeada por HTTP:

![Saída de registos de streaming para o gatilho HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Para saber mais, consulte [os registos de streaming.](functions-monitoring.md#streaming-logs)

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Os registos de streaming suportam apenas uma única instância do anfitrião Funções. Quando a sua função é dimensionada para múltiplas instâncias, os dados de outras instâncias não são mostrados no fluxo de registo. [Live Metrics Stream](../azure-monitor/app/live-stream.md) in Application Insights suporta várias instâncias. Embora também em tempo quase real, a análise de streaming baseia-se em [dados amostrados.](configure-monitoring.md#configure-sampling)

### <a name="application-insights"></a>Application Insights

Recomendamos que monitorize a execução das suas funções integrando a sua aplicação de função com o Application Insights. Quando cria uma aplicação de função no portal Azure, esta integração ocorre por padrão. Quando criar a sua aplicação de função durante a publicação do Visual Studio, tem de integrar o Application Insights. Para saber como, consulte [a integração de Enable Application Insights](configure-monitoring.md#enable-application-insights-integration).

Para saber mais sobre a monitorização utilizando o Application Insights, consulte [as Funções Do Monitor Azure](functions-monitoring.md).

## <a name="c-script-projects"></a>Projetos \# de script C

Por predefinição, todos os projetos C# são criados como [projetos de biblioteca de classes compiladas C](functions-dotnet-class-library.md)# . Se preferir trabalhar com projetos de script C# em vez disso, deve selecionar o script C# como o idioma predefinido nas definições de extensão de Funções Azure:

1. Selecione   >  **Definições de Preferências de**  >  **Ficheiros**.

1. Ir para **as definições do utilizador**  >  **Extensões**  >  **Funções Azure**.

1. Selecione **C#Script** da **Função Azure: Linguagem do projeto**.

Depois de completar estes passos, as chamadas feitas para as Ferramentas Core subjacentes incluem a `--csx` opção, que gera e publica ficheiros de projeto de script C# (.csx). Quando tiver este idioma predefinido especificado, todos os projetos que cria padrão para projetos de script C#. Não é solicitado a escolher uma linguagem de projeto quando um padrão é definido. Para criar projetos em outros idiomas, tem de alterar esta definição ou removê-la do utilizador settings.jsficheiro. Depois de remover esta definição, é novamente solicitado a escolher o seu idioma quando criar um projeto.

## <a name="command-palette-reference"></a>Referência da paleta de comando

A extensão Azure Functions fornece uma interface gráfica útil na área para interagir com as suas aplicações de função em Azure. A mesma funcionalidade também está disponível como comandos na paleta de comandos (F1). Estes comandos Azure Functions estão disponíveis:

|Comando de Funções Azure  | Description  |
|---------|---------|
|**Adicionar novas definições**  |  Cria uma nova definição de aplicação em Azure. Para saber mais, consulte [as definições de aplicação da Publicação](#publish-application-settings). Também poderá ter de [descarregar esta definição para as suas definições locais](#download-settings-from-azure). |
| **Configure Fonte de Implantação** | Liga a sua aplicação de função em Azure a um repositório local de Git. Para saber mais, consulte [a implementação contínua para Funções Azure](functions-continuous-deployment.md). |
| **Ligue-se ao Repositório GitHub** | Liga a sua aplicação de função a um repositório GitHub. |
| **URL de função de cópia** | Obtém o URL remoto de uma função desencadeada por HTTP que está a funcionar em Azure. Para saber mais, consulte [obter o URL da função implantada](#get-the-url-of-the-deployed-function). |
| **Criar app de função em Azure** | Cria uma nova aplicação de função na sua subscrição em Azure. Para saber mais, consulte a secção sobre como [publicar para uma nova aplicação de funções em Azure](#publish-to-azure).        |
| **Definições de desencriptam** | Desencripta [as definições locais](#local-settings-file) que foram encriptadas por **Azure Functions: Configurações encriptadas**.  |
| **Eliminar App de função** | Remove uma aplicação de função da sua subscrição em Azure. Quando não há outras aplicações no plano do Serviço de Aplicações, é-lhe dada a opção de apagar isso também. Outros recursos, como contas de armazenamento e grupos de recursos, não são eliminados. Para remover todos os recursos, deve em vez disso [eliminar o grupo de recursos](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). O seu projeto local não foi afetado. |
|**Eliminar Função**  | Remove uma função existente de uma aplicação de função em Azure. Porque esta eliminação não afeta o seu projeto local, em vez disso considere remover a função localmente e, em seguida, [reeditar o seu projeto.](#republish-project-files) |
| **Excluir Proxy** | Remove um proxy Azure Functions da sua aplicação de função em Azure. Para saber mais sobre os proxies, consulte [Work with Azure Functions Proxies](functions-proxies.md). |
| **Excluir Definição** | Elimina uma definição de aplicação de função em Azure. Esta eliminação não afeta as definições do seu local.settings.jsno ficheiro. |
| **Desligar do Repo**  | Remove a ligação [de implementação contínua](functions-continuous-deployment.md) entre uma aplicação de função em Azure e um repositório de controlo de fonte. |
| **Baixar Definições remotas** | Descarrega as definições da aplicação de função escolhida em Azure para o seu local.settings.jsno ficheiro. Se o ficheiro local estiver encriptado, é desencriptado, atualizado e encriptado novamente. Se houver configurações que tenham valores contraditórios nos dois locais, é-lhe pedido que escolha como proceder. Certifique-se de que guarda alterações no seu local.settings.jsno ficheiro antes de executar este comando. |
| **Editar definições** | Altera o valor de uma definição de aplicação de função existente no Azure. Este comando não afeta as definições do seu local.settings.jsno ficheiro.  |
| **Configurações encriptadas** | Encripta itens individuais na `Values` matriz nas [definições locais](#local-settings-file). Neste ficheiro, `IsEncrypted` também é definido para , que especifica que o tempo de `true` execução local irá desencriptar as definições antes de as utilizar. Criptografe as definições locais para reduzir o risco de fuga de informação valiosa. No Azure, as definições de aplicações são sempre armazenadas encriptadas. |
| **Executar função agora** | Inicia manualmente uma função utilizando APIs de administração. Este comando é utilizado para testes, tanto localmente durante a depuração como contra funções em execução em Azure. Ao ativar uma função em Azure, a extensão obtém primeiro automaticamente uma tecla de administração, que utiliza para chamar as APIs de administração remota que iniciam funções em Azure. O corpo da mensagem enviada à API depende do tipo de gatilho. Os gatilhos do temporizador não exigem que passe nenhum dado. |
| **Inicializar projeto para uso com código VS** | Adiciona os ficheiros de projetos do Código do Estúdio Visual necessários a um projeto de Funções existente. Utilize este comando para trabalhar com um projeto que criou utilizando Ferramentas Core. |
| **Instalar ou atualizar funções Azure Funções Ferramentas principais** | Instala ou atualiza [Azure Functions Core Tools], que é utilizado para executar funções localmente. |
| **Voltar a implementar**  | Permite-lhe recolocar ficheiros de projetos de um repositório de Git ligado a uma implantação específica em Azure. Para republicar as atualizações locais do Código do Estúdio Visual, [reedite o seu projeto.](#republish-project-files) |
| **Definições de renome** | Altera o nome-chave de uma definição de aplicação de função existente no Azure. Este comando não afeta as definições do seu local.settings.jsno ficheiro. Depois de mudar o nome das definições em Azure, deverá [descarregar essas alterações para o projeto local.](#download-settings-from-azure) |
| **Reiniciar** | Reinicia a aplicação de função em Azure. A implementação de atualizações também reinicia a aplicação de função. |
| **Definir AzureWebJobsStorage**| Define o valor da definição de `AzureWebJobsStorage` aplicação. Esta definição é requerida pelas Funções Azure. É definido quando uma aplicação de função é criada em Azure. |
| **Iniciar** | Inicia uma aplicação de função parada em Azure. |
| **Iniciar registos de streaming** | Inicia os registos de streaming para a aplicação de função em Azure. Utilize registos de streaming durante a resolução remota de problemas em Azure, se precisar de ver informações de registo em tempo quase real. Para saber mais, consulte [os registos de streaming.](#streaming-logs) |
| **Parar** | Para uma aplicação de função que está a ser executada em Azure. |
| **Parar os registos de streaming** | Para os registos de streaming para a aplicação de função em Azure. |
| **Alternar como Definição de Ranhura** | Quando ativado, assegura-se de que a definição de aplicação persiste para uma determinada ranhura de implantação. |
| **Desinstalar as funções principais das funções** | Remove Azure Functions Core Tools, que é exigido pela extensão. |
| **Faça upload de configurações locais** | O upload das definições do seu local.settings.jsno ficheiro para a aplicação de função escolhida em Azure. Se o ficheiro local estiver encriptado, é desencriptado, carregado e encriptado novamente. Se houver configurações que tenham valores contraditórios nos dois locais, é-lhe pedido que escolha como proceder. Certifique-se de que guarda alterações no seu local.settings.jsno ficheiro antes de executar este comando. |
| **Ver Cometer no GitHub** | Mostra-lhe o mais recente compromisso numa implementação específica quando a sua aplicação de função está ligada a um repositório. |
| **Ver Registos de Implementação** | Mostra-lhe os registos para uma implementação específica para a aplicação de função em Azure. |

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as ferramentas principais do Azure Functions, consulte [Ferramentas principais do Trabalho com Funções Azure.](functions-run-local.md)

Para saber mais sobre o desenvolvimento de funções como bibliotecas de classe .NET, consulte [a referência do programador Azure Functions C#](functions-dotnet-class-library.md). Este artigo também fornece links para exemplos de como usar atributos para declarar os vários tipos de encadernações suportados por Funções Azure.

[Extensão das Funções do Azure para o Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md