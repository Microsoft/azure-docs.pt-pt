---
title: Criar uma aplicação de funções no Linux a partir do portal do Azure | Documentos da Microsoft
description: Saiba como criar a sua primeira Função do Azure para execução sem servidor através do portal do Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: glenga
ms.custom: ''
ms.openlocfilehash: cc99bc4345c388f22e72957590f3917a85e214e0
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339816"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Criar uma aplicação de funções no Linux num plano do serviço de aplicações do Azure

As Funções do Azure permitem-lhe alojar as suas funções no Linux num contentor predefinido do Serviço de Aplicações do Azure. Este artigo explica-lhe como utilizar o [portal do Azure](https://portal.azure.com) para criar uma aplicação de funções alojadas em Linux que é executado num [plano do App Service](functions-scale.md#app-service-plan). Também pode [trazer o seu próprio contentor personalizado](functions-create-function-linux-custom-image.md).

![Criar uma aplicação de função no portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com> com a sua conta do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação de funções para alojar a execução das suas funções no Linux. A aplicação de funções proporciona um ambiente para a execução do código da sua função. Permite-lhe agrupar funções como unidades lógicas para uma gestão, implementação e partilha de recursos mais fácil. Neste artigo, vai criar um plano do serviço de aplicações quando criar a sua aplicação de função.

1. Selecione o **criar um recurso** botão encontrado no canto superior esquerdo do portal do Azure, em seguida, selecione **computação** > **aplicação de funções**.

    ![Criar uma aplicação de função no portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Utilize as definições da aplicação Function App, conforme especificado na tabela abaixo da imagem.

    ![Definir novas definições da aplicação de funções](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação**  | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. | 
    | **[Grupo de Recursos](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **OS** | Linux | A aplicação de função é executada no Linux. |
    | **Publicar** | Código | O contentor do Linux predefinida para o seu **pilha de Runtime** é utilizado. É tudo o que precisa para fornecer o código de projeto de aplicação de função. Outra opção consiste em publicar um personalizado [imagem do Docker](functions-create-function-linux-custom-image.md). |
    | **[Plano de alojamento](functions-scale.md)** | Plano do App Service | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. Quando executa num plano do serviço de aplicações, pode controlar a [dimensionamento da sua aplicação de função](functions-scale.md).  |
    | **Plano do serviço de aplicações/localização** | Criar plano | Escolher **criar novo** e forneça uma **plano do App Service** nome. Escolher uma **localização** num [região](https://azure.microsoft.com/regions/) perto de ou outros serviços as suas funções de acesso. Escolha sua pretendido  **[escalão de preço](https://azure.microsoft.com/pricing/details/app-service/linux/)**. <br/>Não é possível executar o Linux e Windows, as aplicações de funções no mesmo plano de serviço de aplicações. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. [Podpora Pythonu](functions-reference-python.md) está em pré-visualização neste momento. |
    | **[Armazenamento](../storage/common/storage-quickstart-create-account.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, a qual tem de cumprir os [requisitos de contas de armazenamento](functions-scale.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Ativado | O Application Insights está desativada por predefinição. É recomendado ativar agora a integração do Application Insights e escolher uma localização perto de sua localização do plano de serviço de aplicações de alojamento. Se quiser fazê-lo mais tarde, consulte [as funções do Azure de Monitor](functions-monitoring.md).  |

3. Selecione **Criar** para aprovisionar e implementar a aplicação de função.

4. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Definir novas definições da aplicação de funções](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções.

> [!TIP]
> Está com dificuldade em encontrar as suas aplicações de funções no portal? Experimente [adicionar as Aplicações de Funções aos favoritos no portal do Azure](functions-how-to-use-azure-function-app-settings.md#favorite).

Em seguida, vai criar uma função na aplicação Function App nova. Mesmo depois da aplicação function app está disponível, poderá demorar alguns minutos a ser totalmente inicializado.

## <a name="create-function"></a>Criar uma função acionada por HTTP

Esta secção mostra-lhe como criar uma função na sua nova aplicação de função no portal.

> [!NOTE]
> A experiência de desenvolvimento de portais pode ser útil para experimentar as funções do Azure. Na maioria dos cenários, considere desenvolver as suas funções localmente e publicando o projeto para a sua aplicação de função usando [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) ou o [ferramentas de núcleo de funções do Azure](functions-run-local.md#create-a-local-functions-project).  

1. Na sua nova aplicação de função, escolha o **descrição geral** separador e, depois, ele carrega completamente escolha **+ nova função**.

    ![Criar uma nova função do separador de descrição geral](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Na **início rápido** separador, escolha **-portal**e selecione **continuar**.

    ![Escolha a sua plataforma de desenvolvimento de função.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

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

## <a name="next-steps"></a>Passos Seguintes

Criou uma aplicação App Function com uma função acionada por HTTP simples.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações, veja [Enlaces de HTTP das Funções do Azure](functions-bindings-http-webhook.md).
