---
title: Criar uma função usando Azure para iniciação de estudantes
description: Saiba como criar uma função Azure a partir de dentro de uma subscrição Azure for Student Starter
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: f40405c9325743da5d1963e3baea781606d2d8d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182534"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Criar uma função usando Azure para iniciação de estudantes

Neste tutorial, criaremos uma função HTTP "hello world" numa subscrição Azure for Students Starter. Também vamos percorrer o que está disponível em Funções Azure neste tipo de subscrição.

O Microsoft *Azure for Students Starter* começa com os produtos Azure que precisa para desenvolver na nuvem sem custos para si. [Saiba mais sobre esta oferta aqui.](https://azure.microsoft.com/offers/ms-azr-0144p/)

As Funções Azure permitem executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/) sem ter de criar primeiro um VM ou publicar uma aplicação web. [Saiba mais sobre as Funções aqui.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Criar uma função

 Neste artigo, aprenda a usar as Funções Azure para criar uma função de gatilho HTTP "olá mundo" no portal Azure.

![Criar uma aplicação de função no portal do Azure](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de funções

Precisa de uma aplicação Function App para alojar a execução das suas funções. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para facilitar a gestão, implementação, escala e partilha de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, vai criar uma função na aplicação Function App nova.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Criar uma função de gatilho HTTP

1. A partir do menu esquerdo da janela **Funções,** selecione **Funções** e selecione **Adicione** no menu superior. 
 
1. A partir da janela **Nova Função,** selecione **'Desír do gatilho'.**

    ![Escolha a função de gatilho HTTP](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. Na janela **Nova Função,** aceite o nome predefinido para **Nova Função,** ou introduza um novo nome. 

1. Escolha **o Anónimo** na lista de abandono do **nível** de autorização e, em seguida, selecione **Criar Função**.

    Azure cria a função de gatilho HTTP. Agora, pode enviar um pedido HTTP para executar a função nova.

## <a name="test-the-function"></a>Testar a função

1. Na sua nova função de gatilho HTTP, selecione **Código + Teste** a partir do menu esquerdo e, em seguida, selecione Obter URL de **função** a partir do menu superior.

    ![Selecione Obter URL de função](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. Na caixa de diálogo URL da **função Get,** selecione o **predefinimento** da lista de drop-down e, em seguida, selecione a Cópia para o ícone **da área de transferência.** 

    ![Copiar o URL da função a partir do portal do Azure](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Cole o URL da função na barra de endereço do navegador. Adicione o valor da cadeia de consulta `?name=<your_name>` ao final deste URL e prima Enter para executar o pedido. 

    O exemplo seguinte mostra a resposta no browser:

    ![Resposta da função no browser.](./media/functions-create-student-starter/function-app-browser-testing.png)

    O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP.

1. Quando a sua função é executada, são escritas informações de rastreio nos registos. Para ver a saída de **vestígios,** volte à página **Code + Test** no portal e expanda a seta de Logs na parte inferior da página.

   ![Visualizador de registo de funções no portal do Azure.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Funcionalidades suportadas em Azure for Students Starter

No Azure for Students Starter, você tem acesso à maioria das funcionalidades do tempo de execução das Funções Azure, com várias limitações chave listadas abaixo:

* O gatilho HTTP é o único tipo de gatilho suportado.
    * Todos os inputs e todas as ligações de saída são suportados! [Veja a lista completa aqui.](functions-triggers-bindings.md)
* Línguas Suportadas: 
    * C. (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [Ver línguas suportadas em planos mais altos aqui](supported-languages.md)
* O Windows é o único sistema operativo suportado.
* A balança é restrita a [uma instância de nível livre](https://azure.microsoft.com/pricing/details/app-service/windows/) que funciona até 60 minutos por dia. Escalará sem servidor de 0 a 1 instância automaticamente à medida que o tráfego HTTP for recebido, mas não mais.
* Apenas a [versão 2.x e posterior](functions-versions.md) do tempo de execução de Funções é suportada.
* Todas as ferramentas do desenvolvedor são suportadas para funções de edição e publicação. Isto inclui O Código VS, O Estúdio Visual, o CLI Azure e o portal Azure. Se quiser utilizar outra coisa que não o portal, terá primeiro de criar uma aplicação no portal e, em seguida, escolher essa aplicação como alvo de implementação na sua ferramenta preferida.

## <a name="next-steps"></a>Passos seguintes

Já terminou de criar uma aplicação de função com uma função de gatilho HTTP simples. Em seguida, você pode explorar ferramentas locais, mais idiomas, monitorização e integrações.

 * [Criar a sua primeira função com o Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Criar a sua primeira função com o Visual Studio Code](./create-first-function-vs-code-csharp.md)
 * [Azure Functions JavaScript guia de desenvolvedores](./functions-reference-node.md)
 * [Utilize funções Azure para ligar a uma Base de Dados Azure SQL](./functions-scenario-database-table-cleanup.md)
 * [Saiba mais sobre as ligações Azure Functions HTTP](./functions-bindings-http-webhook.md).
 * [Monitorize as suas Funções Azure](./functions-monitoring.md)