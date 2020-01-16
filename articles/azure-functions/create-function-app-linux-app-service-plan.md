---
title: Criar um aplicativo de funções no Linux por meio do portal do Azure
description: Saiba como criar a sua primeira Função do Azure para execução sem servidor através do portal do Azure.
ms.topic: quickstart
ms.date: 02/28/2019
ms.openlocfilehash: 7689b24a4f9bf17d495917bbf4078d6e5fe4fcff
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75971454"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Criar um aplicativo de funções no Linux em um plano de serviço Azure App

As Funções do Azure permitem-lhe alojar as suas funções no Linux num contentor predefinido do Serviço de Aplicações do Azure. Este artigo explica como usar o [portal do Azure](https://portal.azure.com) para criar um aplicativo de funções hospedado pelo Linux que é executado em um [plano do serviço de aplicativo](functions-scale.md#app-service-plan). Também pode [trazer o seu próprio contentor personalizado](functions-create-function-linux-custom-image.md).

![Criar uma aplicação de função no portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com> com a sua conta do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação de funções para alojar a execução das suas funções no Linux. A aplicação de funções proporciona um ambiente para a execução do código da sua função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos. Neste artigo, você cria um plano do serviço de aplicativo ao criar seu aplicativo de funções.

1. Selecione o botão **Criar um recurso**, no canto superior esquerdo do portal do Azure, e, em seguida, **Computação** > **Aplicação de Funções**.

    ![Criar uma aplicação de função no portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Utilize as definições da aplicação Function App, conforme especificado na tabela abaixo da imagem.

    ![Definir novas definições da aplicação de funções](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. | 
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **OS** | Linux | O aplicativo de funções é executado no Linux. |
    | **Publicar** | Código | O contêiner padrão do Linux para sua **pilha de tempo de execução** é usado. Tudo o que você precisa fornecer é o código do projeto do aplicativo de funções. Outra opção é publicar uma imagem personalizada do [Docker](functions-create-function-linux-custom-image.md). |
    | **[Plano de alojamento](functions-scale.md)** | Plano do App Service | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. Ao executar o em um plano do serviço de aplicativo, você pode controlar o [dimensionamento do seu aplicativo de funções](functions-scale.md).  |
    | **Localização/plano do Serviço de Aplicações** | Criar plano | Escolha **criar novo** e forneça um nome do **plano do serviço de aplicativo** . Escolha um **local** em uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. Escolha o **[tipo de preço](https://azure.microsoft.com/pricing/details/app-service/linux/)** desejado. <br/>Você não pode executar os aplicativos de funções do Linux e do Windows no mesmo plano do serviço de aplicativo. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    | **[Armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, a qual tem de cumprir os [requisitos de contas de armazenamento](functions-scale.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Ativado | O Application Insights está desabilitado por padrão. É recomendável habilitar a integração do Application Insights agora e escolher um local de hospedagem próximo ao local do plano do serviço de aplicativo. Se você quiser fazer isso mais tarde, consulte [monitorar Azure Functions](functions-monitoring.md).  |

3. Selecione **Criar** para aprovisionar e implementar a aplicação de função.

4. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Definir novas definições da aplicação de funções](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções.

Em seguida, vai criar uma função na aplicação Function App nova. Mesmo depois que seu aplicativo de funções estiver disponível, pode levar alguns minutos para ser totalmente inicializado.

## <a name="create-function"></a>Criar uma função acionada por HTTP

Esta seção mostra como criar uma função em seu novo aplicativo de funções no Portal.

> [!NOTE]
> A experiência de desenvolvimento do portal pode ser útil para experimentar Azure Functions. Para a maioria dos cenários, considere desenvolver suas funções localmente e publicar o projeto em seu aplicativo de funções usando [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) ou o [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. Em seu novo aplicativo de funções, escolha a guia **visão geral** e, depois que ele for carregado completamente, escolha **+ nova função**.

    ![Criar uma nova função na guia Visão geral](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Na guia **início rápido** , escolha **no portal**e selecione **continuar**.

    ![Escolha sua plataforma de desenvolvimento de funções.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Escolha **WebHook + API** e selecione **Criar**.

    ![Início rápido de funções no portal do Azure.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

É criada uma função com um modelo específico de linguagem para uma função acionada por HTTP.

Agora, pode enviar um pedido HTTP para executar a função nova.

## <a name="test-the-function"></a>Testar a função

1. Na sua nova função, clique em **</> Obter URL da função** no canto superior direito, selecione **predefinição (tecla de Função)** e, em seguida, clique em **Copiar**. 

    ![Copiar o URL da função a partir do portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. Cole o URL da função na barra de endereço do navegador. Adicione o valor da cadeia de consulta `&name=<yourname>` ao final deste URL e prima a tecla `Enter` no teclado para executar o pedido. Deverá ver a resposta devolvida pela função apresentada no browser.  

    O exemplo seguinte mostra a resposta no browser:

    ![Resposta da função no browser.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP.

3. Quando a sua função é executada, são escritas informações de rastreio nos registos. Para ver a saída de rastreio da execução anterior, regresse à função no portal e clique na seta, na parte inferior do ecrã, para expandir os **Registos**.

   ![Visualizador de registo de funções no portal do Azure.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma aplicação App Function com uma função acionada por HTTP simples.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações, veja [Enlaces de HTTP das Funções do Azure](functions-bindings-http-webhook.md).
