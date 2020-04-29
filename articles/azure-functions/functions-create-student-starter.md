---
title: Criar uma função usando Azure para entrada de estudantes
description: Saiba como criar uma Função Azure a partir de uma subscrição azure para starter de estudante
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: c7dd88bf0ead558a0c4951baf38543566d805caa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756468"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Criar uma função usando Azure para entrada de estudantes

Neste tutorial, criaremos uma função HTTP "hello world" numa subscrição Azure for Students Starter. Também vamos percorrer o que está disponível nas Funções Azure neste tipo de subscrição.

O Microsoft *Azure para Estudantes Starter* inicia-te com os produtos Azure que precisas de desenvolver na nuvem sem qualquer custo para ti. [Saiba mais sobre esta oferta aqui.](https://azure.microsoft.com/offers/ms-azr-0144p/)

As Funções Azure permitem executar o seu código num ambiente [sem servidores](https://azure.microsoft.com/solutions/serverless/) sem ter de criar primeiro um VM ou publicar uma aplicação web. [Saiba mais sobre as Funções aqui.](./functions-overview.md)

## <a name="create-a-function"></a>Criar uma função

 Neste tópico, aprenda a usar funções para criar uma função de "hello world" desencadeada em HTTP no portal Azure.

![Criar uma aplicação de função no portal do Azure](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com> com a sua conta do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação, escalae e partilha de recursos.

1. Selecione o botão **Criar um botão de recurso** encontrado no canto superior esquerdo do portal Azure. Em seguida, selecione **Compute** > **Function App**.

    ![Criar uma aplicação de função no portal do Azure](./media/functions-create-student-starter/function-app-create-flow.png)

2. Utilize as definições da aplicação Function App, conforme especificado na tabela abaixo da imagem.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. | 
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
   | **[Plano de Serviço de Aplicações/Localização](./functions-scale.md)** | Novo | O plano de hospedagem que controla a região para onde a sua aplicação de função está implantada e a densidade dos seus recursos. Aplicações de funções múltiplas implementadas para o mesmo plano partilharão todos a mesma instância livre. Esta é uma restrição do plano de arranque de estudante. As opções de hospedagem completas são [explicadas aqui.](./functions-scale.md)|
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    |**[Application Insights](./functions-monitoring.md)**| Ativado | Application Insights é usado para armazenar e analisar os registos da sua aplicação de função. Está ativado por padrão se escolher um local que suporte os Insights da Aplicação. Os Insights de Aplicação podem ser ativados para qualquer função, escolhendo manualmente uma região próxima para implementar Insights de Aplicação. Sem Informações de Aplicação, só poderá ver registos de streaming ao vivo.

3. Selecione **plano/localização** do serviço de aplicações acima para escolher um local diferente

4. Selecione **Criar novo** e, em seguida, dar ao seu plano um nome único.

5. Selecione o local mais próximo de si. [Veja aqui um mapa completo das regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Selecione **Criar** para aprovisionar e implementar a aplicação de função.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Definir novas definições da aplicação de funções](./media/functions-create-student-starter/function-app-create-notification.png)

8. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções.

Em seguida, vai criar uma função na aplicação Function App nova.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Criar uma função acionada por HTTP

1. Expanda a sua nova **+** aplicação de funções e, em seguida, selecione o botão ao lado das **Funções,** escolha **o Portal In**, e selecione **Continuar**.

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

## <a name="supported-features-in-azure-for-students-starter"></a>Funcionalidades suportadas em Azure para Entrada de Estudantes

Em Azure for Students Starter tem acesso à maioria das funcionalidades do tempo de funcionamento das Funções Azure, com várias limitações chave listadas abaixo:

* O gatilho HTTP é o único tipo de gatilho suportado.
    * Todas as inputs e todas as encadernações de saída são suportadas! [Veja a lista completa aqui.](functions-triggers-bindings.md)
* Línguas suportadas: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [Ver línguas suportadas em planos mais elevados aqui](supported-languages.md)
* O Windows é o único sistema operativo suportado.
* A escala é restrita a [uma instância de nível livre](https://azure.microsoft.com/pricing/details/app-service/windows/) que funciona até 60 minutos por dia. Escalará de 0 a 1 de forma sem servidor automaticamente à medida que o tráfego HTTP é recebido, mas não mais.
* Apenas a [versão 2.x e posterior](functions-versions.md) mente suportada pelo tempo de funcionamento das Funções.
* Todas as ferramentas do desenvolvedor são suportadas para editar e publicar funções. Isto inclui o Código VS, o Estúdio Visual, o Azure CLI e o portal Azure. Se quiser utilizar outra coisa que não o portal, terá primeiro de criar uma aplicação no portal e, em seguida, escolher essa aplicação como alvo de implementação na sua ferramenta preferida.

## <a name="next-steps"></a>Passos seguintes

Criou uma aplicação de função com uma função simples de gatilho http! Agora você pode explorar ferramentas locais, mais idiomas, monitorização e integrações.

 * [Criar a sua primeira função com o Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Criar a sua primeira função com o Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Guia de desenvolvedores de funções azure JavaScript](./functions-reference-node.md)
 * [Utilize funções Azure para ligar a uma base de dados Azure SQL](./functions-scenario-database-table-cleanup.md)
 * [Saiba mais sobre as ligações Azure Functions HTTP](./functions-bindings-http-webhook.md).
 * [Monitorize as suas Funções Azure](./functions-monitoring.md)
