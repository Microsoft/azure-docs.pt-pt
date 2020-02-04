---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 8c63d314c253152d2815a70831870fe331071c68
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964168"
---
## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta secção, cria uma aplicação de função e recursos relacionados na subscrição do Azure e, em seguida, implementa o seu código. 

1. Escolha o ícone Azure na barra de Atividades e, em seguida, na área **Azure: Funções,** escolha o **Botão Deploy para funcionar...** botão.

    ![Publique o seu projeto na Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nas instruções:

    + **Selecione subscrição**: Escolha a subscrição para utilizar. Não verá isto se tiver apenas uma assinatura.

    + **Selecione App de Funções em Azure**: Escolha `+ Create new Function App` (não `Advanced`). Este artigo não suporta o [fluxo de publicação avançado.](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options) 
    
    >[!IMPORTANT]
    > A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure. 
    
    + **Introduza um nome globalmente único para a aplicação de funções**: Escreva um nome válido num caminho de URL. O nome que digita é validado para se certificar de que é único nas Funções Azure. 
    
    ::: zone pivot="programming-language-python"
    + **Selecione um tempo**de execução : Escolha a versão de Python em que tem estado a funcionar localmente. Pode utilizar o comando `python --version` para verificar a sua versão.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Selecione um tempo**de execução : Escolha a versão do Node.js em que tem estado a correr localmente. Pode utilizar o comando `node --version` para verificar a sua versão.
    ::: zone-end

    + **Selecione um local para novos recursos**: Para um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de si. 
    
1.  Quando concluído, os seguintes recursos Azure são criados na sua subscrição:

    + **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** : contém todos os recursos do Azure criados. O nome é baseado no nome do aplicativo de funções.
    + **[Conta de armazenamento](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : uma conta de armazenamento padrão é criada com um nome exclusivo com base no nome do aplicativo de funções.
    + **[Plano de hospedagem](../articles/azure-functions/functions-scale.md)** : um plano de consumo é criado na região oeste dos EUA para hospedar seu aplicativo de funções sem servidor.
    + **Aplicativo de funções**: seu projeto é implantado e executado nesse novo aplicativo de funções.
    + **[Insights de aplicação]()** : Uma instância, que está ligada à sua aplicação de funções, é criada com base no seu nome de função.

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. 
    
1. Selecione **Exibir saída** nesta notificação para exibir os resultados de criação e implantação, incluindo os recursos do Azure que você criou. Se perder a notificação, selecione o ícone do sino no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](media/functions-publish-project-vscode/function-create-notifications.png)
