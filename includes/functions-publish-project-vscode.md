---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: f4e58f4f510450db13ae13d3beecba4d55e766bf
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408596"
---
## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta secção, cria uma aplicação de função e recursos relacionados na sua subscrição Azure e, em seguida, implementa o seu código.

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure.


1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** escolha o **botão Implementar para funcionar...** botão.

    ![Publique o seu projeto na Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Fornecer as seguintes informações nas instruções:

    - **Selecione pasta**: Escolha uma pasta do seu espaço de trabalho ou navegue para uma que contenha a sua aplicação de função. Não verá isto se já tiver uma aplicação de função válida aberta.

    - **Selecione subscrição**: Escolha a subscrição para usar. Não verá isto se tiver apenas uma assinatura.

    - **Selecione App de função em Azure**: Escolha `- Create new Function App` . (Não escolha a `Advanced` opção, que não está abrangida por este artigo.)
      
    - **Introduza um nome globalmente único para a aplicação de função**: Escreva um nome que seja válido num caminho URL. O nome que digita é validado para se certificar de que é único em Funções Azure.
    
    ::: zone pivot="programming-language-python"
    - **Selecione um tempo de execução**: Escolha a versão de Python que tem vindo a executar localmente. Pode utilizar o `python --version` comando para verificar a sua versão.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    - **Selecione um tempo de execução**: Escolha a versão de Node.js que tem vindo a executar localmente. Pode utilizar o `node --version` comando para verificar a sua versão.
    ::: zone-end

    - **Selecione uma localização para novos recursos**: Para um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de si. 
    
1.  Quando concluídos, os seguintes recursos Azure são criados na sua subscrição, utilizando nomes baseados no nome da aplicação da sua função:
    
    - Um grupo de recursos, que é um recipiente lógico para recursos relacionados.
    - Uma conta padrão de Armazenamento Azure, que mantém informações estatais e outras sobre os seus projetos.
    - Um plano de consumo, que define o anfitrião subjacente para a sua aplicação de função sem servidor. 
    - Uma aplicação de função, que fornece o ambiente para a execução do seu código de função. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para facilitar a gestão, implementação e partilha de recursos dentro do mesmo plano de hospedagem.
    - Uma instância de Insights de Aplicação ligada à aplicação de função, que rastreia o uso da sua função sem servidor.

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. 
    
1. Selecione **Ver Saída** nesta notificação para ver os resultados da criação e implementação, incluindo os recursos Azure que criou. Se perder a notificação, selecione o ícone da campainha no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](media/functions-publish-project-vscode/function-create-notifications.png)
