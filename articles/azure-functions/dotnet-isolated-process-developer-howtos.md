---
title: Desenvolver e publicar funções .NET 5 utilizando funções Azure
description: Aprenda a criar e depurar funções C# utilizando .NET 5.0 e, em seguida, desloque o projeto local para hospedagem sem servidor em Funções Azure.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 9751d5d354d738a64d6b02e8153464491d78ec14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201374"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Desenvolver e publicar funções .NET 5 utilizando funções Azure 

Este artigo mostra-lhe como trabalhar com funções C# utilizando .NET 5.0, que fica sem processo a partir do tempo de funcionamento das Funções Azure. Você vai aprender a criar, depurar localmente, e publicar estas funções de processo isolado .NET para Azure. Em Azure, estas funções funcionam num processo isolado que suporta .NET 5.0. Para saber mais, consulte [o Guia para executar funções em .NET 5.0 em Azure](dotnet-isolated-process-guide.md).

Se não precisar de suportar .NET 5.0 ou executar as suas funções fora de processo, poderá antes criar uma função de [biblioteca de classe C.](functions-create-your-first-function-visual-studio.md)

>[!NOTE]
>O desenvolvimento de funções de processo isolado .NET no portal Azure não é suportado atualmente. Tem de utilizar a publicação Azure CLI ou Visual Studio Code para criar uma aplicação de função no Azure que suporta a execução de aplicações .NET 5.0 fora do processo.   

## <a name="prerequisites"></a>Pré-requisitos

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [SDK .NET 5.0](https://www.microsoft.com/net/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) versão 3.0.3381, ou uma versão posterior.

+ Versão 2.20 [do Azure CLI,](/cli/azure/install-azure-cli) ou uma versão posterior.  
::: zone pivot="development-environment-vscode"
+ [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ A [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para o Visual Studio Code.  

+ A [extensão de Funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código de Estúdio Visual, versão 1.3.0 ou mais recente.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/), incluindo a carga de trabalho de **desenvolvimento do Azure.**  
.NET modelos de projeto de função isolados e publicação não estão atualmente disponíveis no Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

Em Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> Neste momento, não existem modelos de projetos do Visual Studio que suportem a criação de projetos de função isolados .NET. Este artigo mostra-lhe como usar as Ferramentas Core para criar o seu projeto C#, que depois pode executar localmente e depurar no Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Selecione o ícone do Azure na barra de Atividade e, em seguida, na área **Azure: Funções**, selecione o ícone **Criar novo projeto...**.

    ![Selecione Criar um novo projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Selecione uma localização do diretório para a área de trabalho do seu projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Forneça as seguintes informações nos pedidos:

    + **Selecione um idioma para o seu projeto de função**: Selecione `C#`.

    + **Selecione um tempo de execução .NET**: Escolha `.NET 5 isolated` .

    + **Selecione um modelo para a primeira função do seu projeto**: Selecione `HTTP trigger`.

    + **Forneça um nome de função**: Escreva `HttpExample`.

    + **Forneça um espaço de nomes**: Escreva `My.Functions`.

    + **Nível de autorização**: `Anonymous` Escolha, que permite a qualquer pessoa ligar para o ponto final da sua função. Para saber mais sobre o nível de autorização, consulte [as teclas de Autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como gostaria de abrir o seu projeto**: Selecione `Add to workspace`.

1. Utilizando esta informação, o Visual Studio Code gera um projeto Azure Functions com um gatilho HTTP. Pode ver os ficheiros de projeto locais no Explorer. Para saber mais sobre ficheiros que são criados, consulte [os ficheiros do projeto Gerados.](functions-develop-vs-code.md#generated-project-files)
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Executar o `func init` comando, da seguinte forma, para criar um projeto de funções numa pasta chamada *LocalFunctionProj*:  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    Especificar `dotnetisolated` cria um projeto que funciona em .NET 5.0.


1. Navegue na pasta do projeto:

    ```console
    cd LocalFunctionProj
    ```

    Esta pasta contém vários ficheiros para o projeto, incluindo o [local.settings.jse](functions-run-local.md#local-settings-file) [host.jsem](functions-host-json.md) ficheiros de configurações. Como *local.settings.jspode* conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*

1. Adicione uma função ao seu projeto utilizando o seguinte comando, onde o `--name` argumento é o nome único da sua função (HttpExample) e o argumento especifica o gatilho da `--template` função (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` cria um ficheiro de código httpExample.cs.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>Executar a função localmente

Neste ponto, pode executar o `func start` comando a partir da raiz da sua pasta de projeto para compilar e executar o projeto de funções isoladas C#. Atualmente, se pretender depurar o seu código de função fora do processo no Estúdio Visual, tem de anexar manualmente um depurar ao processo de execução de funções utilizando os seguintes passos:  

1. Abra o ficheiro do projeto (.csproj) em Visual Studio. Pode rever e modificar o seu código de projeto e definir quaisquer pontos de rutura desejados no código. 

1. A partir da pasta do projeto raiz, utilize o seguinte comando a partir do terminal ou um pedido de comando para iniciar o hospedeiro de tempo de execução:

    ```console
    func start --dotnet-isolated-debug
    ```

    A `--dotnet-isolated-debug` opção diz ao processo para esperar que um depurante se anexe antes de continuar. No final da saída, deverá ver algo como as seguintes linhas: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    Indica `PID: XXXXXX` o ID do processo (PID) do processo dotnet.exe que é o anfitrião das Funções em execução.
 
1. Na saída de tempo de execução do Azure Functions, tome nota do ID do processo de funcionamento do processo de anfitrião, ao qual irá anexar um depurador. Note também o URL da sua função local.

1. A partir do menu **Debug** em Visual Studio, **selecione Attach to Process...**, localize o processo que corresponde ao ID do processo e selecione **Attach**. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Anexar o depurar ao processo de anfitrião de funções":::    

    Com o depurar anexado pode depurar o seu código de função normalmente.

1. Na barra de endereços do seu navegador, digite o URL de função local, que se parece com o seguinte, e execute o pedido. 

    <http://localhost:7071/api/HttpExample>

    Deve ver vestígios de saída do pedido escrito para o terminal de funcionamento. A execução de código para em quaisquer pontos de rutura que definir no seu código de função.

1. Quando terminar, vá ao terminal e pressione Ctrl + C para parar o processo de anfitrião.
 
Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

> [!NOTE]  
> A publicação do Visual Studio não está atualmente disponível para aplicações de processo isoladas .NET. Depois de ter terminado o desenvolvimento do seu projeto no Visual Studio, tem de usar o CLI Azure para criar os recursos remotos do Azure. Em seguida, pode voltar a utilizar as Ferramentas Principais do Azure Functions da linha de comando para publicar o seu projeto no Azure.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Crie recursos Azure de suporte para a sua função

Antes de poder implantar o seu código de função para Azure, tem de criar três recursos:

- Um [grupo de recursos](../azure-resource-manager/management/overview.md), que é um recipiente lógico para recursos relacionados.
- Uma [conta de Armazenamento,](../storage/common/storage-account-create.md)que é usada para manter o estado e outras informações sobre as suas funções.
- Uma aplicação de função, que fornece o ambiente para a execução do seu código de função. Uma aplicação de função mapeia para o seu projeto de função local e permite-lhe agrupar funções como uma unidade lógica para uma gestão, implementação e partilha mais fácil de recursos.

Utilize os seguintes comandos para criar estes itens.

1. Se ainda não o fez, inscreva-se no Azure:

    ```azurecli
    az login
    ```

    O comando [de login az](/cli/azure/reference-index#az_login) assina-o na sua conta Azure.

1. Criar um grupo de recursos nomeado `AzureFunctionsQuickstart-rg` na `westeurope` região:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    O [grupo az cria](/cli/azure/group#az_group_create) um grupo de recursos. Geralmente cria o seu grupo de recursos e recursos numa região próxima de si, utilizando uma região disponível devolvida do `az account list-locations` comando.

1. Crie uma conta de armazenamento para fins gerais no seu grupo de recursos e região:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    A [conta de armazenamento az cria](/cli/azure/storage/account#az_storage_account_create) o comando cria a conta de armazenamento. 

    No exemplo anterior, `<STORAGE_NAME>` substitua-o por um nome que lhe seja adequado e único no Azure Storage. Os nomes devem conter apenas três a 24 caracteres e letras minúsculas. `Standard_LRS` especifica uma conta de finalidade geral, que é [suportada por Funções](storage-considerations.md#storage-account-requirements).

1. Crie a aplicação de função em Azure:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    O comando [az functionapp cria](/cli/azure/functionapp#az_functionapp_create) a aplicação de função em Azure. 
    
    No exemplo anterior, `<STORAGE_NAME>` substitua-o pelo nome da conta que usou no passo anterior e substitua-o `<APP_NAME>` por um nome globalmente único, adequado a si. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. 
    
    Este comando cria uma aplicação de função em execução .NET 5.0 no âmbito do [Plano de Consumo de Funções Azure](consumption-plan.md). Este plano deve ser gratuito pela quantidade de uso que incorrer neste artigo. O comando também fornece uma instância associada de Insights de Aplicação Azure no mesmo grupo de recursos. Utilize este caso para monitorizar a sua aplicação de função e visualizar registos. Para obter mais informações, consulte [as Funções Do Monitor Azure](functions-monitoring.md). O caso não incorre em custos até que o ative.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta secção, cria uma aplicação de função e recursos relacionados na sua subscrição Azure e, em seguida, implementa o seu código.

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure.


1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** escolha o **botão Implementar para funcionar...** botão.

    ![Publicar o projeto no Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nos pedidos:

    - **Selecione pasta**: Escolha uma pasta do seu espaço de trabalho ou navegue para uma que contenha a sua aplicação de função. Não verá este pedido quando já tiver uma aplicação de função válida aberta.

    - **Selecione subscrição**: Escolha a subscrição para usar. Não verá este pedido quando só tiver uma subscrição.

    - **Selecione Aplicação de Funções no Azure**: Selecione `- Create new Function App`. (Não escolha a `Advanced` opção, que não está abrangida por este artigo.)
      
    - **Introduza um nome globalmente único para a aplicação de função**: Escreva um nome que seja válido num caminho URL. O nome que digita é validado para se certificar de que é único em Funções Azure.
    
    - **Selecione uma pilha de tempo de execução**: Escolha `.NET 5 (non-LTS)` . 
    
    - **Selecione uma localização para novos recursos**: Para um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de si. 
    
    Na área de notificação, vê-se o estado dos recursos individuais à medida que são criados em Azure.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notificação da criação de recursos Azure":::
    
1.  Quando concluídos, os seguintes recursos Azure são criados na sua subscrição, utilizando nomes baseados no nome da aplicação da sua função:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Selecione **Ver Saída** nesta notificação para ver os resultados da criação e implementação, incluindo os recursos Azure que criou. Se perder a notificação, selecione o ícone da campainha no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Limpar os recursos

Criou recursos para completar este artigo. Poderá ser-lhe cobrado estes recursos, dependendo do seu [estado da conta](https://azure.microsoft.com/account/) e dos [preços dos serviços](https://azure.microsoft.com/pricing/). 

::: zone pivot="development-environment-cli"  
Utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Utilize as seguintes medidas para eliminar a aplicação de função e os seus recursos relacionados para evitar incorrer em custos adicionais.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Utilize as seguintes medidas para eliminar a aplicação de função e os seus recursos relacionados para evitar incorrer em custos adicionais.

1. No Cloud Explorer, expanda a sua subscrição > **Serviços de Aplicação,** clique com o botão direito na sua aplicação de função e escolha **Abrir no Portal**. 

1. Na página de aplicações da função, selecione o **separador 'Vista Geral'** e, em seguida, selecione o link no **grupo De recursos**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Selecione o grupo de recursos para eliminar a partir da página de aplicação de funções":::

2. Na página do **grupo Recursos,** reveja a lista de recursos incluídos e verifique se são eles que pretende eliminar.
 
3. Selecione **Eliminar grupo de recursos** e siga as instruções.

   A eliminação pode demorar alguns minutos. Quando terminar, é apresentada uma notificação durante alguns segundos. Também pode selecionar o ícone de sino na parte superior da página para ver a notificação.
::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções isoladas .NET](dotnet-isolated-process-guide.md)

