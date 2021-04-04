---
title: Conecte funções ao Azure Storage usando o Visual Studio
description: Saiba como adicionar uma ligação de saída para ligar as funções da sua biblioteca de classe C# a uma fila de armazenamento Azure utilizando o Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 635392212027c73e5aa954eb671be31228796a0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185135"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Conecte funções ao Azure Storage usando o Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra-lhe como usar o Visual Studio para ligar a função que criou no [artigo de arranque rápido anterior] ao Azure Storage. A vinculação de saída que adiciona a esta função escreve dados do pedido HTTP para uma mensagem numa fila de armazenamento da Fila Azure. 

A maioria das ligações requer uma cadeia de ligação armazenada que as funções usam para aceder ao serviço vinculado. Para facilitar, utiliza a conta de Armazenamento que criou com a sua aplicação de função. A ligação a esta conta já está armazenada numa definição de aplicação chamada `AzureWebJobsStorage` .  

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, deve: 

 - Complete [a primeira parte do quickstart do Estúdio Visual.](./functions-create-your-first-function-visual-studio.md) 

- Inscreva-se na subscrição do Azure do Visual Studio.

## <a name="download-the-function-app-settings"></a>Descarregue as definições da aplicação de funções

No [artigo de arranque rápido anterior,](./create-first-function-vs-code-csharp.md)criou uma aplicação de função em Azure juntamente com a conta de Armazenamento necessária. O fio de ligação desta conta é armazenado de forma segura nas definições de aplicações em Azure. Neste artigo, você escreve mensagens para uma fila de armazenamento na mesma conta. Para se ligar à sua conta de Armazenamento ao executar a função localmente, tem de transferir as definições da aplicação para o *local.settings.jsno* ficheiro. 

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**. 

1. Em **Ações**, **selecione Editar Azure App Service Service Settings**. 

    ![Editar as definições de aplicação](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. Sob **AzureWebJobsStorage,** copie o valor da cadeia **remota** para **local**, e, em seguida, selecione **OK**. 

A ligação de armazenamento, que utiliza a `AzureWebJobsStorage` definição para a ligação, pode agora ligar-se ao armazenamento da sua fila quando funciona localmente.

## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Como está a utilizar uma ligação de saída de armazenamento de fila, precisa da extensão de encadernações de armazenamento instalada antes de executar o projeto. Com exceção dos gatilhos HTTP e timer, as ligações são implementadas como pacotes de extensão. 

1. No menu **Ferramentas,** selecione **NuGet Package**  >  **Manager Package Manager Consola**. 

1. Na consola, executar o seguinte comando [Pacote de Instalação](/nuget/tools/ps-ref-install-package) para instalar as extensões de armazenamento:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Agora, pode adicionar a ligação de saída de armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Depois de definido o encadernação, pode utilizar `name` a ligação para aceder a ela como um atributo na assinatura da função. Ao utilizar uma ligação de saída, não é preciso utilizar o código Azure Storage SDK para autenticação, obter uma referência de fila ou escrever dados. O tempo de funcionamento e a ligação de saída de fila das funções fazem essas tarefas para si.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Executar a função localmente

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Uma nova fila nomeada `outqueue` é criada na sua conta de armazenamento pelo tempo de execução de Funções quando a ligação de saída é usada pela primeira vez. Usará o Cloud Explorer para verificar se a fila foi criada juntamente com a nova mensagem.

## <a name="examine-the-output-queue"></a>Examinar a fila de saída

1. No Estúdio Visual a partir do menu **Ver,** selecione **Cloud Explorer**.

1. No **Cloud Explorer,** expanda as suas contas de subscrição e **armazenamento do** Azure, em seguida, expanda a conta de armazenamento utilizada pela sua função. Se não se lembrar do nome da conta de armazenamento, verifique a `AzureWebJobsStorage` definição do fio de ligação no *local.settings.jsno* ficheiro.  

1. Expanda o nó **de Filas** e, em seguida, clique duas vezes na fila **nomeada** para ver o conteúdo da fila no Estúdio Visual. 

   A fila contém a mensagem que a fila de enlace de saída da fila criou quando executou a função acionada por HTTP. Se invocou a função com o valor predefinido `name` do *Azure*, a mensagem de fila é *Nome transmitido para a função: Azure*.

    ![Mensagem de fila mostrada no Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Executar a função novamente, enviar outro pedido, e verá uma nova mensagem aparecer na fila.  

Agora, é hora de reeditar a aplicação de função atualizada para a Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Reimplantar e verificar a aplicação atualizada

1. No **Solution Explorer,** clique com o botão direito do projeto e selecione **Publicar,** em seguida, escolha **publicar** para reeditar o projeto para Azure.

1. Após a implementação concluída, pode voltar a utilizar o navegador para testar a função reafectada. Como antes, apeia a cadeia de consulta `&name=<yourname>` ao URL.

1. Volte [a ver a mensagem na fila de armazenamento](#examine-the-output-queue) para verificar se a ligação de saída gera novamente uma nova mensagem na fila.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função HTTP para escrever dados numa fila de armazenamento. Para saber mais sobre o desenvolvimento de funções, consulte [Desenvolver Funções Azure utilizando o Estúdio Visual.](functions-develop-vs.md)

Em seguida, deve ativar a monitorização do Application Insights para a sua aplicação de função:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](configure-monitoring.md#add-to-an-existing-function-app)

[Azure Storage Explorer]: https://storageexplorer.com/
[artigo de arranque rápido anterior]: functions-create-your-first-function-visual-studio.md