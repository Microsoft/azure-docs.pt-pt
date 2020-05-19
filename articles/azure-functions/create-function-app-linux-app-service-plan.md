---
title: Criar uma aplicação de função no Linux a partir do portal Azure
description: Saiba como criar a sua primeira Função do Azure para execução sem servidor através do portal do Azure.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 5aae60900a61c28f6c53f89b8e273daccb047eef
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83116208"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Crie uma aplicação de função no Linux num plano de Serviço de Aplicações Azure

As Funções do Azure permitem-lhe alojar as suas funções no Linux num contentor predefinido do Serviço de Aplicações do Azure. Este artigo explica-o como usar o [portal Azure](https://portal.azure.com) para criar uma aplicação de função hospedada pelo Linux que funciona num plano de Serviço de [Aplicações.](functions-scale.md#app-service-plan) Também pode [trazer o seu próprio contentor personalizado](functions-create-function-linux-custom-image.md).

![Criar uma aplicação de função no portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com> com a sua conta do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação de funções para alojar a execução das suas funções no Linux. A aplicação de funções proporciona um ambiente para a execução do código da sua função. Permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implantação, escalação e partilha de recursos. Neste artigo, cria um plano de Serviço de Aplicações quando cria a sua aplicação de funções.

1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na **nova** página, **selecione Compute**  >  **Function App**.

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Criar uma aplicação de função no portal do Azure":::

1. Na página **Basics,** utilize as definições da aplicação de função conforme especificado na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome da app de função** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os caracteres válidos são `a-z` (caso insensível), `0-9` e `-` .  |
    |**Publicar**| **Código** (padrão) | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Escolha **o Núcleo .NET** para funções C# e F#. |
    |**Versão**| Número da versão | Escolha a versão do seu tempo de execução instalado.  |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="Página básica":::

1. Selecione **Seguinte : Hospedagem**. Na página **de Hospedagem,** introduza as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e podem conter números e letras minúsculas apenas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da conta de [armazenamento.](../azure-functions/functions-scale.md#storage-account-requirements) |
    |**Sistema operativo**| **Linux** | Um sistema operativo é pré-selecionado para si com base na sua seleção de pilhas de tempo de funcionamento, mas pode alterar a definição se necessário. |
    | **[Planear](../azure-functions/functions-scale.md)** | **Consumo (Servidor)** | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. No plano de **consumo** padrão, os recursos são adicionados dinamicamente conforme exigido pelas suas funções. Neste alojamento [sem servidor,](https://azure.microsoft.com/overview/serverless-computing/) paga-se apenas pelo tempo em que as suas funções funcionam. Quando executa num plano do Serviço de Aplicações, tem de gerir o [dimensionamento da sua aplicação de funções](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Página de hospedagem":::

1. Selecione **Seguinte : Monitorização**. Na página **de Monitorização,** introduza as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Sim** (padrão) | Cria um recurso Application Insights com o mesmo *nome app* na região mais próxima suportada. Ao expandir esta definição ou selecionar **Criar novos,** pode alterar o nome 'Insights de Aplicação' ou escolher uma região diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Página de monitorização":::

1. Selecione **Review + crie** para rever as seleções de configuração da aplicação.

1. Na página **Review + criar,** rever as suas definições e, em seguida, selecionar **Criar** para fornecer e implementar a aplicação de funções.

1. Selecione o ícone **notificações** no canto superior direito do portal e observe a mensagem bem sucedida da **Implementação.**

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos**. A fixação facilita o regresso a esta função de recurso da aplicação a partir do seu painel de instrumentos.

    ![Notificação de implantação](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Mesmo depois de a sua aplicação de funções estar disponível, pode demorar alguns minutos a ser totalmente inicializada.

Em seguida, vai criar uma função na aplicação Function App nova.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Criar uma função de gatilho HTTP

Esta secção mostra-lhe como criar uma função na sua nova aplicação de funções no portal.

> [!NOTE]
> A experiência de desenvolvimento do portal pode ser útil para experimentar funções Azure. Para a maioria dos cenários, considere desenvolver as suas funções localmente e publicar o projeto na sua aplicação de funções usando o [Código do Estúdio Visual](functions-create-first-function-vs-code.md#create-an-azure-functions-project) ou as [Ferramentas Core funções do Azure](functions-run-local.md#create-a-local-functions-project).  

1. A partir do menu esquerdo da janela **Funções,** selecione **Funções**e, em seguida, selecione **Adicionar** a partir do menu superior. 
 
1. A partir da janela **New Function,** selecione **Http trigger**.

    ![Escolha função de gatilho HTTP](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. Na janela **New Function,** aceite o nome predefinido para **New Function**, ou introduza um novo nome. 

1. Escolha o **Anonymous** na lista de desistência do **nível** de autorização e, em seguida, selecione **Criar Função**.

    O Azure cria a função de gatilho HTTP. Agora, pode enviar um pedido HTTP para executar a função nova.

## <a name="test-the-function"></a>Testar a função

1. Na sua nova função de gatilho HTTP, selecione **Código + Teste** a partir do menu esquerdo e, em seguida, selecione Obter URL de **função** a partir do menu superior.

    ![Selecione Obter URL de função](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. Na caixa de diálogo URL da **função Get,** selecione **predefinido** da lista de drop-down e, em seguida, selecione o ícone **copy para clipboard.** 

    ![Copiar o URL da função a partir do portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Cole o URL da função na barra de endereço do navegador. Adicione o valor de cadeia de consulta `?name=<your_name>` ao final deste URL e prima Introduza para executar o pedido. 

    O exemplo seguinte mostra a resposta no browser:

    ![Resposta da função no browser.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP.

1. Quando a sua função é executada, são escritas informações de rastreio nos registos. Para ver a saída do rastreio, volte à página **Code + Test** no portal e expanda a seta de **Registos** na parte inferior da página.

   ![Visualizador de registo de funções no portal do Azure.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma aplicação de função com uma função simples de gatilho HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações, veja [Enlaces de HTTP das Funções do Azure](functions-bindings-http-webhook.md).
