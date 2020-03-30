---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77029021"
---
## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta secção, cria uma aplicação de função e recursos relacionados na subscrição do Azure e, em seguida, implementa o seu código. 

1. Escolha o ícone Azure na barra de Atividades e, em seguida, na área **Azure: Funções,** escolha o **Botão Deploy para funcionar...** botão.

    ![Publique o seu projeto na Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nas instruções:

    + **Selecione subscrição**: Escolha a subscrição para utilizar. Não verá isto se tiver apenas uma assinatura.

    + **Selecione App de Funções em Azure:** Escolha `+ Create new Function App` (não `Advanced`). Este artigo não suporta o [fluxo de publicação avançado.](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options) 
    
    >[!IMPORTANT]
    > A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure. 
    
    + **Introduza um nome globalmente único para a aplicação de funções**: Escreva um nome válido num caminho de URL. O nome que digita é validado para se certificar de que é único nas Funções Azure. 
    
    ::: zone pivot="programming-language-python"
    + **Selecione um tempo**de execução : Escolha a versão de Python em que tem estado a funcionar localmente. Pode usar `python --version` o comando para verificar a sua versão.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Selecione um tempo**de execução : Escolha a versão do Node.js em que tem estado a correr localmente. Pode usar `node --version` o comando para verificar a sua versão.
    ::: zone-end

    + **Selecione um local para novos recursos**: Para um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de si. 
    
1.  Quando concluído, os seguintes recursos Azure são criados na sua subscrição:

    + **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)**: Contém todos os recursos azure criados. O nome baseia-se no nome da sua aplicação de função.
    + **[Conta de armazenamento](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)**: Uma conta de armazenamento padrão é criada com um nome único que é baseado no nome da sua app de função.
    + **[Plano de hospedagem](../articles/azure-functions/functions-scale.md)**: Um plano de consumo é criado na região oeste dos EUA para acolher a sua aplicação de função sem servidores.
    + **App de funções**: O seu projeto está implantado e funciona nesta nova aplicação de funções.
    + **Insights de aplicação**: Uma instância, que está ligada à sua aplicação de funções, é criada com base no seu nome de função.

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. 
    
1. Selecione **Ver Output** nesta notificação para visualizar os resultados da criação e implementação, incluindo os recursos Azure que criou. Se perder a notificação, selecione o ícone do sino no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](media/functions-publish-project-vscode/function-create-notifications.png)
