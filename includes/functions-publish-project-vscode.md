---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842190"
---
## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta secção, cria uma aplicação de função e recursos relacionados na subscrição do Azure e, em seguida, implementa o seu código. 

1. Escolha o ícone Azure na barra de Atividades e, em seguida, na área **Azure: Funções,** escolha o **Botão Deploy para funcionar...** botão.

    ![Publique o seu projeto na Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nas instruções:

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | Mensagem | Valor | Descrição |
    | ------ | ----- | ----- |
    | Selecionar subscrição | A sua subscrição | Mostrado quando tem várias subscrições. |
    | Selecione App de Funções em Azure | + Criar nova App de Funções | A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure. |
    | Insira um nome globalmente único para a aplicação de funções | Nome exclusivo | Os carateres válidos para um nome de aplicação de funções são `a-z`, `0-9` e `-`. |
    | Selecione um local para novos recursos | Região | Escolher uma [região](https://azure.microsoft.com/regions/) próxima de si. | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | Mensagem | Valor | Descrição |
    | ------ | ----- | ----- |
    | Selecionar subscrição | A sua subscrição | Mostrado quando tem várias subscrições. |
    | Selecione App de Funções em Azure | + Criar nova App de Funções | A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure. |
    | Insira um nome globalmente único para a aplicação de funções | Nome exclusivo | Os carateres válidos para um nome de aplicação de funções são `a-z`, `0-9` e `-`. |
    | Selecione um tempo de execução | A sua versão | Escolha a versão linguística em que tem estado a correr localmente. |
    | Selecione um local para novos recursos | Região | Escolher uma [região](https://azure.microsoft.com/regions/) próxima de si. | 

    ::: zone-end

    
1.  Quando concluído, os seguintes recursos Azure são criados na sua subscrição:

    + **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** : contém todos os recursos do Azure criados. O nome é baseado no nome do aplicativo de funções.
    + **[Conta de armazenamento](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : uma conta de armazenamento padrão é criada com um nome exclusivo com base no nome do aplicativo de funções.
    + **[Plano de hospedagem](../articles/azure-functions/functions-scale.md)** : um plano de consumo é criado na região oeste dos EUA para hospedar seu aplicativo de funções sem servidor.
    + **Aplicativo de funções**: seu projeto é implantado e executado nesse novo aplicativo de funções.
    + **[Insights de aplicação]()** : Uma instância, que está ligada à sua aplicação de funções, é criada com base no seu nome de função.

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. 
    
1. Selecione **Exibir saída** nesta notificação para exibir os resultados de criação e implantação, incluindo os recursos do Azure que você criou.

    ![Criar notificação completa](media/functions-publish-project-vscode/function-create-notifications.png)

1. De volta à área **Azure: Funções** na barra lateral, expanda a nova aplicação de funções sob a sua subscrição. Expandir **funções,** clicar à direita (Windows) ou Ctrl + clicar (MacOS) em **HttpExample**e, em seguida, escolher **Copy function URL**.

    ![Copiar a URL da função para o novo gatilho HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
