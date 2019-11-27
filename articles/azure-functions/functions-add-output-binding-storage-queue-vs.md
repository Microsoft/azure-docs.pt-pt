---
title: Conectar funções ao armazenamento do Azure usando o Visual Studio
description: Saiba como adicionar uma associação de saída para conectar suas C# funções de biblioteca de classes a uma fila de armazenamento do Azure usando o Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: bd899c5cc7aafc5b3349cf4cec9098a849665a2d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227431"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Conectar funções ao armazenamento do Azure usando o Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra como usar o Visual Studio para conectar a função que você criou no [artigo de início rápido anterior] ao armazenamento do Azure. A associação de saída que você adiciona a essa função grava dados da solicitação HTTP em uma mensagem em uma fila de armazenamento de filas do Azure. 

A maioria das associações requer uma cadeia de conexão armazenada que o Functions usa para acessar o serviço associado. Para facilitar, use a conta de armazenamento que você criou com seu aplicativo de funções. A conexão com essa conta já está armazenada em uma configuração de aplicativo chamada `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, você deve: 

 - Conclua [parte 1 do início rápido do Visual Studio] [./Functions-Create-First-function-vs-Code.MD]. 

- Entre em sua assinatura do Azure no Visual Studio.

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

No [artigo de início rápido anterior](functions-create-first-function-vs-code.md), você criou um aplicativo de funções no Azure junto com a conta de armazenamento necessária. A cadeia de conexão para essa conta é armazenada com segurança nas configurações do aplicativo no Azure. Neste artigo, você grava mensagens em uma fila de armazenamento na mesma conta. Para se conectar à sua conta de armazenamento ao executar a função localmente, você deve baixar as configurações do aplicativo para o arquivo *local. Settings. JSON* . 

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**. 

1. Em **ações**, selecione **Editar configurações do serviço de Azure app**. 

    ![Editar as configurações do aplicativo](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. Em **AzureWebJobsStorage**, copie o valor da cadeia de caracteres **remota** para **local**e, em seguida, selecione **OK**. 

A associação de armazenamento, que usa a configuração de `AzureWebJobsStorage` para a conexão, agora pode se conectar ao armazenamento de filas ao executar localmente.

## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Como você está usando uma associação de saída de armazenamento de filas, precisará da extensão de associações de armazenamento instalada antes de executar o projeto. Exceto para gatilhos HTTP e de temporizador, as associações são implementadas como pacotes de extensão. 

1. No menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** > **console do Gerenciador de pacotes**. 

1. No console do, execute o seguinte comando [install-Package](/nuget/tools/ps-ref-install-package) para instalar as extensões de armazenamento:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Agora, você pode adicionar a associação de saída de armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Depois que a associação é definida, você pode usar a `name` da Associação para acessá-la como um atributo na assinatura da função. Usando uma associação de saída, você não precisa usar o código do SDK de armazenamento do Azure para autenticação, obter uma referência de fila ou gravar dados. O tempo de execução de funções e a associação de saída de fila executam essas tarefas para você.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Executar localmente a função

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Uma nova fila chamada `outqueue` é criada na sua conta de armazenamento pelo tempo de execução do Functions quando a associação de saída é usada pela primeira vez. Você usará o Cloud Explorer para verificar se a fila foi criada junto com a nova mensagem.

## <a name="examine-the-output-queue"></a>Examinar a fila de saída

1. No Visual Studio, no menu **Exibir** , selecione **Cloud Explorer**.

1. No **Cloud Explorer**, expanda sua assinatura do Azure e **contas de armazenamento**e, em seguida, expanda a conta de armazenamento usada por sua função. Se você não se lembrar do nome da conta de armazenamento, verifique a configuração `AzureWebJobsStorage` cadeia de conexão no arquivo *local. Settings. JSON* .  

1. Expanda o nó **filas** e clique duas vezes na fila chamada **subfila** para exibir o conteúdo da fila no Visual Studio. 

   A fila contém a mensagem que a fila de enlace de saída da fila criou quando executou a função acionada por HTTP. Se invocou a função com o valor predefinido `name` do *Azure*, a mensagem de fila é *Nome transmitido para a função: Azure*.

    ![Mensagem da fila mostrada em Gerenciador de Armazenamento do Azure](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Execute a função novamente, envie outra solicitação e você verá uma nova mensagem aparecendo na fila.  

Agora, é hora de republicar o aplicativo de funções atualizado no Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Reimplantar e verificar o aplicativo atualizado

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **publicar**e, em seguida, escolha **publicar** para republicar o projeto no Azure.

1. Após a conclusão da implantação, você pode usar novamente o navegador para testar a função reimplantada. Como antes, acrescente a cadeia de caracteres de consulta `&name=<yourname>` à URL.

1. Novamente, [exiba a mensagem na fila de armazenamento](#examine-the-output-queue) para verificar se a associação de saída gera novamente uma nova mensagem na fila.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Você atualizou sua função disparada por HTTP para gravar dados em uma fila de armazenamento. Para saber mais sobre o desenvolvimento de funções, consulte [desenvolver Azure Functions usando o Visual Studio](functions-develop-vs.md).

Em seguida, você deve habilitar o monitoramento de Application Insights para seu aplicativo de funções:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[artigo de início rápido anterior]: functions-create-your-first-function-visual-studio.md
