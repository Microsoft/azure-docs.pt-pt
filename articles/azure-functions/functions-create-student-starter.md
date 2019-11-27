---
title: Criar uma função usando o Azure for Students Starter
description: Saiba como criar uma função do Azure de dentro de uma assinatura inicial do Azure for Student
Customer intent: As a student, I want to be able to create a HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: b86ea2367a17baa8c57b8d0c0331c0cd3cfbb481
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227108"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Criar uma função usando o Azure for Students Starter

Neste tutorial, criaremos uma função HTTP Hello World em uma assinatura do Azure for Students Starter. Também vamos examinar o que está disponível em Azure Functions nesse tipo de assinatura.

O Microsoft *Azure for Students Starter* o inicia com os produtos do Azure que você precisa para desenvolver na nuvem sem nenhum custo para você. [Saiba mais sobre esta oferta aqui.](https://azure.microsoft.com/offers/ms-azr-0144p/)

As Funções do Azure permitem-lhe executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web. [Saiba mais sobre as funções aqui.](./functions-overview.md)

## <a name="create-a-function"></a>Criar uma função

 Neste tópico, saiba como usar funções para criar uma função "Hello World" disparada por HTTP no portal do Azure.

![Criar uma aplicação de função no portal do Azure](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com> com a sua conta do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. As aplicações App Function permitem-lhe agrupar funções como unidades lógicas para uma gestão, implementação e partilha de recursos mais fácil. 

1. Selecione o botão **Novo** no canto superior esquerdo do portal do Azure e, em seguida, selecione **Computação** > **Function App**.

    ![Criar uma aplicação de função no portal do Azure](./media/functions-create-student-starter/function-app-create-flow.png)

2. Utilize as definições da aplicação Function App, conforme especificado na tabela abaixo da imagem.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. | 
    | **[Grupo de Recursos](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
   | **[Local/plano do serviço de aplicativo](./functions-scale.md)** | Novo | O plano de hospedagem que controla em qual região seu aplicativo de funções é implantado e a densidade de seus recursos. Vários aplicativos de funções implantados no mesmo plano compartilharão a mesma única instância gratuita. Essa é uma restrição do plano de início do aluno. As opções de hospedagem completa são [explicadas aqui.](./functions-scale.md)|
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    |**[Application Insights](./functions-monitoring.md)**| Ativado | Application Insights é usado para armazenar e analisar os logs do aplicativo de funções. Ele será habilitado por padrão se você escolher um local com suporte a Application Insights. Application Insights pode ser habilitado para qualquer função, escolhendo manualmente uma região próxima para implantar Application Insights. Sem Application Insights, você só poderá exibir logs de transmissão ao vivo.

3. Selecione o **plano/local do serviço de aplicativo** acima para escolher um local diferente

4. Selecione **criar novo** e dê um nome exclusivo ao seu plano.

5. Selecione o local mais próximo de você. [Veja aqui um mapa completo das regiões do Azure.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Selecione **Criar** para aprovisionar e implementar a aplicação de função.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Definir novas definições da aplicação de funções](./media/functions-create-student-starter/function-app-create-notification.png)

8. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções.

Em seguida, vai criar uma função na aplicação Function App nova.

## <a name="create-function"></a>Criar uma função acionada por HTTP

1. Expanda a sua nova aplicação de funções, selecione o botão **+** junto a **Funções**, escolha **No portal** e selecione **Continuar**.

    ![Início rápido das Funções, escolher a plataforma.](./media/functions-create-student-starter/function-app-quickstart-choose-portal.png)

1. Escolha **WebHook + API** e selecione **Criar**.

    ![Início rápido de funções no portal do Azure.](./media/functions-create-student-starter/function-app-quickstart-node-webhook.png)

É criada uma função com um modelo específico de linguagem para uma função acionada por HTTP.

Agora, pode enviar um pedido HTTP para executar a função nova.

## <a name="test-the-function"></a>Testar a função

1. Na sua nova função, clique em **</> Obter URL da função** no canto superior direito, selecione **predefinição (tecla de Função)** e, em seguida, clique em **Copiar**. 

    ![Copiar o URL da função a partir do portal do Azure](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. Cole o URL da função na barra de endereço do navegador. Adicione o valor da cadeia de consulta `&name=<yourname>` ao final deste URL e prima a tecla `Enter` no teclado para executar o pedido. Deverá ver a resposta devolvida pela função apresentada no browser.  

    O exemplo seguinte mostra a resposta no browser:

    ![Resposta da função no browser.](./media/functions-create-student-starter/function-app-browser-testing.png)

    O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP.

3. Quando a sua função é executada, são escritas informações de rastreio nos registos. Para ver a saída de rastreio da execução anterior, regresse à função no portal e clique na seta, na parte inferior do ecrã, para expandir os **Registos**.

   ![Visualizador de registo de funções no portal do Azure.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Recursos com suporte no Azure for Students Starter

No iniciador do Azure para estudantes, você tem acesso à maioria dos recursos do tempo de execução de Azure Functions, com várias limitações principais listadas abaixo:

* O gatilho HTTP é o único tipo de gatilho com suporte.
    * Todas as associações de entrada e de saída são suportadas! [Consulte a lista completa aqui.](functions-triggers-bindings.md)
* Idiomas com suporte: 
    * C#(.NET Core 2)
    * JavaScript (Node. js 8 & 10)
    * F#(.NET Core 2)
    * [Veja os idiomas com suporte em planos mais altos aqui](supported-languages.md)
* O Windows é o único sistema operacional com suporte.
* A escala é restrita a [uma instância de camada gratuita](https://azure.microsoft.com/pricing/details/app-service/windows/) em execução por até 60 minutos por dia. Você irá serverlessly dimensionar de 0 para 1 instância automaticamente, pois o tráfego HTTP é recebido, mas não há mais.
* Há suporte apenas para [o tempo de execução 2. x](functions-versions.md) .
* Todas as ferramentas de desenvolvedor têm suporte para edição e publicação de funções. Isso inclui VS Code, o Visual Studio, o CLI do Azure e o portal do Azure. Se você quiser usar algo diferente do portal, será necessário primeiro criar um aplicativo no portal e, em seguida, escolher esse aplicativo como um destino de implantação em sua ferramenta preferida.

## <a name="next-steps"></a>Passos seguintes

Você criou um aplicativo de funções com uma simples função disparada por HTTP! Agora você pode explorar ferramentas locais, mais linguagens, monitoramento e integrações.

 * [Criar a sua primeira função com o Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Criar sua primeira função usando Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Guia do desenvolvedor do Azure Functions JavaScript](./functions-reference-node.md)
 * [Usar Azure Functions para se conectar a um banco de dados SQL do Azure](./functions-scenario-database-table-cleanup.md)
 * [Saiba mais sobre associações HTTP Azure Functions](./functions-bindings-http-webhook.md).
 * [Monitorar seu Azure Functions](./functions-monitoring.md)
