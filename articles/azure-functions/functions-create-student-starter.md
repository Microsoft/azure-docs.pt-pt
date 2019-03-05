---
title: Criar uma função a utilizar o Azure para estudantes iniciante | Documentos da Microsoft
description: Saiba como criar uma função do Azure a partir de uma subscrição do Azure para estudantes iniciante
Customer intent: As a student, I want to be able to create a HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
services: functions
documentationcenter: na
author: alexkarcher-msft
manager: ggailey777
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: 5fd9f51185b8461269d498b254d9e9d9f4118270
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344630"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Criar uma função a utilizar o Azure para estudantes iniciante

Neste tutorial, iremos criar uma função HTTP do hello world numa subscrição do Azure para estudantes iniciante. Vamos também explicar o que está disponível nas funções do Azure neste tipo de subscrição.

Microsoft *do Azure para estudantes iniciante* permite-lhe começar com os produtos do Azure que precisa para programar na cloud sem qualquer custo para. [Saiba mais sobre esta oferta aqui.](https://azure.microsoft.com/offers/ms-azr-0144p/)

As Funções do Azure permitem-lhe executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web. [Saiba mais sobre as funções aqui.](./functions-overview.md)

## <a name="create-a-function"></a>Criar uma função

 Neste tópico, saiba como utilizar as funções para criar uma função acionada por HTTP "hello world" no portal do Azure.

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
    | **Nome da aplicação**  | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. | 
    | **[Grupo de Recursos](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
   | **[Plano do serviço de aplicações/localização](./functions-scale.md)** | Novo | O plano de alojamento que controla em que região para a sua aplicação de função é implementada e a densidade dos seus recursos. Várias aplicações de funções implementadas para o mesmo plano irá partilhar a mesma instância gratuita única. Esta é uma restrição do plano para estudantes iniciante. São as opções de hospedagem completas [explicadas a seguir.](./functions-scale.md)|
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    |**[Application Insights](./functions-monitoring.md)**| Ativado | O Application Insights é utilizado para armazenar e analisar registos da sua aplicação de função. Ele é habilitado por padrão se escolher uma localização que suporta o Application Insights. O Application Insights pode ser ativado para qualquer função escolhendo manualmente uma região próximas para implementar o Application Insights. Sem o Application Insights, só poderá ver os registos de transmissão em fluxo em direto.

3. Selecione **plano do serviço de aplicações/localização** acima para escolher uma localização diferente

4. Selecione **criar novo** e, em seguida, dê um nome exclusivo ao seu plano.

5. Selecione a localização mais próxima de si. [Ver um mapa completo de regiões do Azure aqui.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Selecione **Criar** para aprovisionar e implementar a aplicação de função.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Definir novas definições da aplicação de funções](./media/functions-create-student-starter/function-app-create-notification.png)

8. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções.

> [!TIP]
> Está com dificuldade em encontrar as suas aplicações de funções no portal? Experimente [adicionar as Aplicações de Funções aos favoritos no portal do Azure](./functions-how-to-use-azure-function-app-settings.md#favorite).
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

## <a name="supported-features-in-azure-for-students-starter"></a>Funcionalidades suportadas no Azure para estudantes iniciante

No Azure para estudantes iniciante, tem acesso à maioria dos recursos do tempo de execução das funções do Azure, com várias limitações principais listadas abaixo:

* O acionador HTTP é o único tipo de Acionador suportado.
    * Toda entrada e saída de todas as ligações são suportadas! [Veja aqui a lista completa.](functions-triggers-bindings.md)
* Idiomas com suporte: 
    * C#(.NET core 2)
    * JavaScript (node. js 8 e 10)
    * F#(.NET core 2)
    * [Ver os idiomas com suporte no superior planos aqui](supported-languages.md)
* Windows é o único sistema operativo suportado.
* Dimensionamento está limitado a [uma instância do escalão gratuito](https://azure.microsoft.com/pricing/details/app-service/windows/) em execução durante 60 minutos por dia. Será dimensionada serverlessly de 0 para 1 instância automaticamente à medida que o tráfego HTTP é recebido, mas não são necessárias mais.
* Apenas [o runtime 2.x](functions-versions.md) é suportada.
* Todas as ferramentas para programadores é suportada para edição e funções de publicação. Isto inclui o VS Code, Visual Studio, a CLI do Azure e o portal do Azure. Se desejar usar nada que não seja o portal, terá primeiro de criar uma aplicação no portal e, em seguida, escolha essa aplicação como um destino de implementação na sua ferramenta preferencial.

## <a name="next-steps"></a>Passos Seguintes

Criar uma aplicação de função com uma função acionada por HTTP simples! Agora, pode explorar o local de ferramentas, mais idiomas, monitorização e integrações.

 * [Criar a sua primeira função com o Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Criar a sua primeira função com o Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Guia do Programador de JavaScript de funções do Azure](./functions-reference-node.md)
 * [Utilize as funções do Azure para ligar a uma base de dados do SQL do Azure](./functions-scenario-database-table-cleanup.md)
 * [Saiba mais sobre os enlaces de HTTP de funções do Azure](./functions-bindings-http-webhook.md).
 * [Monitorizar as suas funções do Azure](./functions-monitoring.md)
