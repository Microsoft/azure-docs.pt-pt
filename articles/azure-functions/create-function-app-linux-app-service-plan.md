---
title: Criar uma aplicação de função no Linux a partir do portal Azure
description: Saiba como criar a sua primeira Função do Azure para execução sem servidor através do portal do Azure.
ms.topic: quickstart
ms.date: 02/28/2019
ms.openlocfilehash: 1492188f72eb4a691ddceb78aa269601f192e467
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76963840"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Crie uma aplicação de função no Linux num plano de Serviço de Aplicações Azure

As Funções do Azure permitem-lhe alojar as suas funções no Linux num contentor predefinido do Serviço de Aplicações do Azure. Este artigo explica-o como usar o [portal Azure](https://portal.azure.com) para criar uma aplicação de função hospedada pelo Linux que funciona num plano de Serviço de [Aplicações.](functions-scale.md#app-service-plan) Também pode [trazer o seu próprio contentor personalizado](functions-create-function-linux-custom-image.md).

![Criar uma aplicação de função no portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com> com a sua conta do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação de funções para alojar a execução das suas funções no Linux. A aplicação de funções proporciona um ambiente para a execução do código da sua função. Permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implantação, escalação e partilha de recursos. Neste artigo, cria um plano de Serviço de Aplicações quando cria a sua aplicação de funções.

1. Selecione o botão **Criar um recurso**, no canto superior esquerdo do portal do Azure, depois selecione **Computação** > **Function App**.

    ![Criar uma aplicação de função no portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Utilize as definições da aplicação Function App, conforme especificado na tabela abaixo da imagem.

    ![Definir novas definições da aplicação de funções](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Assinatura** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. | 
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **OS** | Linux | A aplicação de funções funciona no Linux. |
    | **Publicar** | Código | É utilizado o recipiente Linux padrão para o seu **Runtime Stack.** Tudo o que precisa fornecer é o código do projeto da sua app de funções. Outra opção é publicar uma imagem personalizada [do Docker.](functions-create-function-linux-custom-image.md) |
    | **[Plano de alojamento](functions-scale.md)** | Plano do App Service | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. Quando executa um plano de Serviço de Aplicações, pode controlar a [escala da sua aplicação de funções](functions-scale.md).  |
    | **Plano do Serviço de Aplicações/Localização** | Criar plano | Escolha **Criar novo** e forneça um nome de plano de serviço de **aplicações.** Escolha um **Local** numa [região](https://azure.microsoft.com/regions/) próxima ou perto de outros serviços a que as suas funções acedam. Escolha o seu **[nível](https://azure.microsoft.com/pricing/details/app-service/linux/)** de preços desejado . <br/>Não é possível executar aplicações de função Linux e Windows no mesmo plano de Serviço de Aplicações. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    | **[Armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, a qual tem de cumprir os [requisitos de contas de armazenamento](storage-considerations.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Ativado | Os Insights de Aplicação são desativados por defeito. Recomendamos permitir a integração de Insights de Aplicação agora e escolher um local de hospedagem perto da localização do seu plano de app service. Se quiser fazê-lo mais tarde, consulte as [Funções Monitor Azure](functions-monitoring.md).  |

3. Selecione **Criar** para aprovisionar e implementar a aplicação de função.

4. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Definir novas definições da aplicação de funções](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções.

Em seguida, vai criar uma função na aplicação Function App nova. Mesmo depois de a sua aplicação de funções estar disponível, pode demorar alguns minutos a ser totalmente inicializada.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Criar uma função acionada por HTTP

Esta secção mostra-lhe como criar uma função na sua nova aplicação de funções no portal.

> [!NOTE]
> A experiência de desenvolvimento do portal pode ser útil para experimentar funções Azure. Para a maioria dos cenários, considere desenvolver as suas funções localmente e publicar o projeto na sua aplicação de funções usando o [Código do Estúdio Visual](functions-create-first-function-vs-code.md#create-an-azure-functions-project) ou as [Ferramentas Core funções do Azure](functions-run-local.md#create-a-local-functions-project).  

1. Na sua nova aplicação de funções, escolha o separador **Overview** e, depois de carregar, escolha completamente **+ Nova função**.

    ![Criar uma nova função a partir do separador Overview](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. No separador **Quickstart,** escolha **o portal In**, e selecione **Continuar**.

    ![Escolha a sua plataforma de desenvolvimento de funções.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

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
