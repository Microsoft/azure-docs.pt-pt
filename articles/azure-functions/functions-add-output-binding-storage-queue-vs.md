---
title: Ligar funções ao Armazenamento Azure usando o Estúdio Visual
description: Aprenda a adicionar uma ligação de saída para ligar as funções da biblioteca da classe C# a uma fila de Armazenamento Azure utilizando o Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 171479a0f60741b545a171315e99cc5e4e8bc843
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74849212"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Ligar funções ao Armazenamento Azure usando o Estúdio Visual

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra-lhe como usar o Visual Studio para ligar a função que criou no artigo anterior do [Quickstart] ao Azure Storage. O encadernação de saída que adiciona a esta função escreve dados do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure. 

A maioria das ligações requer uma cadeia de ligação armazenada que as Funções usam para aceder ao serviço de encadernação. Para facilitar, utiliza a conta de Armazenamento que criou com a sua aplicação de funções. A ligação a esta conta já está `AzureWebJobsStorage`armazenada numa definição de aplicação chamada .  

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, deve: 

 - Complete a [parte 1 do Estúdio Visual quickstart](./functions-create-first-function-vs-code.md). 

- Inscreva-se na subscrição do Azure do Visual Studio.

## <a name="download-the-function-app-settings"></a>Descarregue as definições da aplicação de funções

No artigo anterior do [Quickstart,](functions-create-first-function-vs-code.md)criou uma aplicação de função em Azure juntamente com a conta de Armazenamento necessária. A cadeia de ligação para esta conta é armazenada de forma segura nas definições da aplicação em Azure. Neste artigo, escreve mensagens para uma fila de armazenamento na mesma conta. Para se ligar à sua conta de Armazenamento ao executar a função localmente, tem de descarregar as definições da aplicação para o ficheiro *local.settings.json.* 

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**. 

1. No âmbito **de Ações,** selecione Editar Definições de Serviço de **Aplicações Azure**. 

    ![Editar as definições de aplicação](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. Em **AzureWebJobsStorage,** copie o valor de cadeia **remoto** para **Local,** e, em seguida, selecione **OK**. 

A encadernação `AzureWebJobsStorage` de armazenamento, que utiliza a regulação para a ligação, pode agora ligar-se ao seu armazenamento de fila quando estiver a funcionar localmente.

## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Como está a utilizar uma encadernação de saída de armazenamento de fila, precisa da extensão de encadernação de armazenamento instalada antes de executar o projeto. Com exceção dos gatilhos HTTP e temporizador, as encadernações são implementadas como pacotes de extensão. 

1. No menu **Tools,** selecione **NuGet Package Manager** > **Manager Console**. 

1. Na consola, execute o seguinte comando [Instalação-Pacote](/nuget/tools/ps-ref-install-package) para instalar as extensões de armazenamento:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Agora, pode adicionar a ligação de saída de armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Após a definição da ligação, pode utilizar o `name` encadernação para aceder ao mesmo como atributo na assinatura da função. Ao utilizar uma ligação de saída, não é necessário utilizar o código SDK de Armazenamento Azure para autenticação, obtendo uma referência de fila ou escrevendo dados. O tempo de funcionamento das Funções e a ligação de saída da fila fazem essas tarefas para si.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Executar localmente a função

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Uma nova `outqueue` fila nomeada é criada na sua conta de armazenamento pelo tempo de funcionamento das Funções quando a ligação de saída é usada pela primeira vez. Utilizará o Cloud Explorer para verificar se a fila foi criada juntamente com a nova mensagem.

## <a name="examine-the-output-queue"></a>Examinar a fila de saída

1. No Estúdio Visual a partir do menu **'Ver',** selecione **Cloud Explorer**.

1. No **Cloud Explorer,** expanda a subscrição do Azure e **as Contas**de Armazenamento e, em seguida, expanda a conta de armazenamento utilizada pela sua função. Se não se lembrar do nome da `AzureWebJobsStorage` conta de armazenamento, verifique a definição de cadeia de ligação no ficheiro *local.settings.json.*  

1. Expanda o nó das filas e, em seguida, clique duas **vezes** na fila chamada **outqueue** para ver o conteúdo da fila no Estúdio Visual. 

   A fila contém a mensagem que a fila de enlace de saída da fila criou quando executou a função acionada por HTTP. Se invocou a função com o valor predefinido `name` do *Azure*, a mensagem de fila é *Nome transmitido para a função: Azure*.

    ![Mensagem de fila mostrada no Explorador de Armazenamento Azure](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Faça a função novamente, envie outro pedido, e verá uma nova mensagem aparecer na fila.  

Agora, é hora de reeditar a aplicação de funções atualizada para o Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Recolocar e verificar a aplicação atualizada

1. No **Solution Explorer,** clique no projeto e selecione **Publicar,** escolha **publicar** para reeditar o projeto para o Azure.

1. Após a implementação concluída, pode voltar a utilizar o navegador para testar a função reimplantada. Como antes, anexar a `&name=<yourname>` corda de consulta ao URL.

1. Volte [a ver a mensagem na fila de armazenamento](#examine-the-output-queue) para verificar se a ligação de saída gera novamente uma nova mensagem na fila.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função de http para escrever dados para uma fila de Armazenamento. Para saber mais sobre o desenvolvimento de Funções, consulte [Desenvolver funções azure utilizando o Estúdio Visual](functions-develop-vs.md).

Em seguida, deve ativar a monitorização de Insights de Aplicação para a sua aplicação de funções:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[artigo anterior quickstart]: functions-create-your-first-function-visual-studio.md
