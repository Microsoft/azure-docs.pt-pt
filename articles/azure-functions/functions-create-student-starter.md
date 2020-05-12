---
title: Criar uma função usando Azure para entrada de estudantes
description: Saiba como criar uma Função Azure a partir de uma subscrição azure para starter de estudante
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 04/29/2020
ms.author: alkarche
ms.openlocfilehash: ffb6378d3dc4cc3fb23ea62157aad393d8ae6642
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122882"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Criar uma função usando Azure para entrada de estudantes

Neste tutorial, criaremos uma função HTTP "hello world" numa subscrição Azure for Students Starter. Também vamos percorrer o que está disponível nas Funções Azure neste tipo de subscrição.

O Microsoft *Azure para Estudantes Starter* inicia-te com os produtos Azure que precisas de desenvolver na nuvem sem qualquer custo para ti. [Saiba mais sobre esta oferta aqui.](https://azure.microsoft.com/offers/ms-azr-0144p/)

As Funções Azure permitem executar o seu código num ambiente [sem servidores](https://azure.microsoft.com/solutions/serverless/) sem ter de criar primeiro um VM ou publicar uma aplicação web. [Saiba mais sobre as Funções aqui.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Criar uma função

 Neste artigo, aprenda a usar funções Azure para criar uma função de gatilho HTTP "olá mundo" no portal Azure.

![Criar uma aplicação de função no portal do Azure](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação, escalae e partilha de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, vai criar uma função na aplicação Function App nova.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Criar uma função de gatilho HTTP

1. A partir do menu esquerdo da janela **Funções,** selecione **Funções**e, em seguida, selecione **Adicionar** a partir do menu superior. 
 
1. A partir da janela **New Function,** selecione **Http trigger**.

    ![Escolha função de gatilho HTTP](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. Na janela **New Function,** aceite o nome predefinido para **New Function**, ou introduza um novo nome. 

1. Escolha o **Anonymous** na lista de desistência do **nível** de autorização e, em seguida, selecione **Criar Função**.

    O Azure cria a função de gatilho HTTP. Agora, pode enviar um pedido HTTP para executar a função nova.

## <a name="test-the-function"></a>Testar a função

1. Na sua nova função de gatilho HTTP, selecione **Código + Teste** a partir do menu esquerdo e, em seguida, selecione Obter URL de **função** a partir do menu superior.

    ![Selecione Obter URL de função](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. Na caixa de diálogo URL da **função Get,** selecione **predefinido** da lista de drop-down e, em seguida, selecione o ícone **copy para clipboard.** 

    ![Copiar o URL da função a partir do portal do Azure](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Cole o URL da função na barra de endereço do navegador. Adicione o valor de cadeia de consulta `?name=<your_name>` ao final deste URL e prima Introduza para executar o pedido. 

    O exemplo seguinte mostra a resposta no browser:

    ![Resposta da função no browser.](./media/functions-create-student-starter/function-app-browser-testing.png)

    O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP.

1. Quando a sua função é executada, são escritas informações de rastreio nos registos. Para ver a saída do rastreio, volte à página **Code + Test** no portal e expanda a seta de **Registos** na parte inferior da página.

   ![Visualizador de registo de funções no portal do Azure.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Funcionalidades suportadas em Azure para Entrada de Estudantes

Em Azure for Students Starter, tem acesso à maioria das funcionalidades do tempo de funcionamento das Funções Azure, com várias limitações chave listadas abaixo:

* O gatilho HTTP é o único tipo de gatilho suportado.
    * Todas as inputs e todas as encadernações de saída são suportadas! [Veja a lista completa aqui.](functions-triggers-bindings.md)
* Línguas suportadas: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [Ver línguas suportadas em planos mais elevados aqui](supported-languages.md)
* O Windows é o único sistema operativo suportado.
* A escala é restrita a [uma instância de nível livre](https://azure.microsoft.com/pricing/details/app-service/windows/) que funciona até 60 minutos por dia. Escalará de 0 a 1 de forma automática à medida que o tráfego HTTP é recebido, mas não mais.
* Apenas a [versão 2.x e posterior](functions-versions.md) mente suportada pelo tempo de funcionamento das Funções.
* Todas as ferramentas do desenvolvedor são suportadas para editar e publicar funções. Isto inclui o Código VS, o Estúdio Visual, o Azure CLI e o portal Azure. Se quiser usar outra coisa que não o portal, terá primeiro de criar uma aplicação no portal e, em seguida, escolher essa aplicação como alvo de implementação na sua ferramenta preferida.

## <a name="next-steps"></a>Passos seguintes

Terminou agora a criação de uma aplicação de função com uma simples função de gatilho HTTP. Em seguida, você pode explorar ferramentas locais, mais idiomas, monitorização e integrações.

 * [Criar a sua primeira função com o Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Criar a sua primeira função com o Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Guia de desenvolvedores de funções azure JavaScript](./functions-reference-node.md)
 * [Utilize funções Azure para ligar a uma base de dados Azure SQL](./functions-scenario-database-table-cleanup.md)
 * [Saiba mais sobre as ligações Azure Functions HTTP](./functions-bindings-http-webhook.md).
 * [Monitorize as suas Funções Azure](./functions-monitoring.md)
