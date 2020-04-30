---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 41dfb809cdab00f4f9bee335d92522f37a438c68
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82109746"
---
## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta secção, cria uma aplicação de função e recursos relacionados na subscrição do Azure e, em seguida, implementa o seu código. 

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure. 


1. Escolha o ícone Azure na barra de Atividades e, em seguida, na área **Azure: Funções,** escolha o **Botão Deploy para funcionar...** botão.

    ![Publique o seu projeto na Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nas instruções:

    + **Selecione pasta**: Escolha uma pasta do seu espaço de trabalho ou navegue para uma que contenha a sua aplicação de função. Não verá isto se já tiver uma aplicação de função válida aberta.

    + **Selecione subscrição**: Escolha a subscrição para utilizar. Não verá isto se tiver apenas uma assinatura.

    + **Selecione App de Funções em Azure**: Escolha `+ Create new Function App`. (Não escolha a `Advanced` opção, que não está abrangida neste artigo.)
      
    + **Introduza um nome globalmente único para a aplicação de funções**: Escreva um nome válido num caminho de URL. O nome que digita é validado para se certificar de que é único nas Funções Azure. 
    
    ::: zone pivot="programming-language-python"
    + **Selecione um tempo**de execução : Escolha a versão de Python em que tem estado a funcionar localmente. Pode usar `python --version` o comando para verificar a sua versão.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Selecione um tempo**de execução : Escolha a versão do Node.js em que tem estado a correr localmente. Pode usar `node --version` o comando para verificar a sua versão.
    ::: zone-end

    + **Selecione um local para novos recursos**: Para um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de si. 
    
1.  Quando concluído, os seguintes recursos Azure são criados na sua subscrição, utilizando nomes baseados no nome da sua app de função:
    
    + Um grupo de recursos, que é um recipiente lógico para recursos relacionados.
    + Uma conta de Armazenamento Azure padrão, que mantém informações estatais e outras sobre os seus projetos.
    + Um plano de consumo, que define o anfitrião subjacente para a sua aplicação de função sem servidores. 
    + Uma aplicação de função, que fornece o ambiente para executar o seu código de função. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação e partilha de recursos dentro do mesmo plano de hospedagem.
    + Uma instância de Insights de Aplicação ligada à aplicação de função, que rastreia o uso da sua função sem servidor.

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. 
    
1. Selecione **Ver Output** nesta notificação para visualizar os resultados da criação e implementação, incluindo os recursos Azure que criou. Se perder a notificação, selecione o ícone do sino no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](media/functions-publish-project-vscode/function-create-notifications.png)
