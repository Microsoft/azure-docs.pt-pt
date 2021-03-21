---
title: Criar uma aplicação de função no Linux a partir do portal Azure
description: Saiba como criar a sua primeira Função Azure no Linux utilizando o portal Azure.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d6787e1dd8312e368964a1f555025beff9a06a23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732263"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Criar uma aplicação de função no Linux num plano de Serviço de Aplicações Azure

As Funções do Azure permitem-lhe alojar as suas funções no Linux num contentor predefinido do Serviço de Aplicações do Azure. Este artigo percorre a forma de utilizar o [portal Azure](https://portal.azure.com) para criar uma aplicação de função hospedada em Linux que funciona num [plano de Serviço de Aplicações.](dedicated-plan.md) Também pode [trazer o seu próprio contentor personalizado](functions-create-function-linux-custom-image.md).

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com> com a sua conta do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de funções

Precisa de uma aplicação de funções para alojar a execução das suas funções no Linux. A aplicação de funções proporciona um ambiente para a execução do código da sua função. Permite-lhe agrupar funções como uma unidade lógica para facilitar a gestão, implantação, escala e partilha de recursos. Neste artigo, cria um plano de Serviço de Aplicações quando cria a sua aplicação de função.

1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na **nova** página, selecione App de Função **computacional**  >  .

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Criar uma aplicação de funções no portal do Azure":::

1. Na página **Basics,** utilize as definições da aplicação de função conforme especificado na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição no âmbito da qual esta nova aplicação de funções é criada. |
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome da Aplicação de Funções** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z` (não sensível a maiúsculas e minúsculas), `0-9` e `-`.  |
    |**Publicar**| **Código** (predefinição) | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | **Pilha de runtime** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Escolha **.NET Core** para funções C# e F#. |
    |**Versão**| Número da versão | Escolha a versão do seu tempo de execução instalado.  |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou perto de outros serviços a que as suas funções acedam. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="Página básica":::

1. Selecione **Seguinte: Alojamento**. Na página **'Hospedagem',** insira as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e podem conter apenas números e letras minúsculas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da [conta de armazenamento.](../azure-functions/storage-considerations.md#storage-account-requirements) |
    |**Sistema operativo**| **Linux** | Um sistema operativo é pré-selecionado para si com base na seleção da sua pilha de tempo de execução, mas pode alterar a definição se necessário. |
    | **[Planear](../azure-functions/functions-scale.md)** | **Consumo (Sem servidor)** | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. No **Plano de Consumo** predefinido, os recursos são adicionados dinamicamente, conforme necessário por parte das suas funções. Neste hospedagem [sem servidor,](https://azure.microsoft.com/overview/serverless-computing/) paga-se apenas pelo tempo que as suas funções são executadas. Quando executa num plano do Serviço de Aplicações, tem de gerir o [dimensionamento da sua aplicação de funções](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Página de hospedagem":::

1. Selecione **Seguinte : Monitorização**. Na página **'Monitor'** insira as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Sim** (padrão) | Cria um recurso Application Insights com o mesmo *nome app* na região suportada mais próxima. Ao expandir esta definição ou selecionar **Criar novos,** pode alterar o nome De Insights de Aplicação ou escolher uma região diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Página de monitorização":::

1. Selecione **Review + crie** para rever as seleções de configuração da aplicação.

1. Na página **'Rever + criar',** rever as definições e, em seguida, selecionar **Criar** para provisão e implementar a aplicação de função.

1. Selecione o ícone **notificações** no canto superior direito do portal e observe a mensagem **de implementação conseguida.**

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos.** A fixação facilita o regresso a esta função de recurso de aplicação a partir do seu dashboard.

    ![Notificação de implantação](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Mesmo depois de a sua aplicação de função estar disponível, pode demorar alguns minutos a ser totalmente inicializada.

Em seguida, vai criar uma função na aplicação Function App nova.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Criar uma função de gatilho HTTP

Esta secção mostra-lhe como criar uma função na sua nova aplicação de função no portal.

> [!NOTE]
> A experiência de desenvolvimento do portal pode ser útil para experimentar funções Azure. Para a maioria dos cenários, considere desenvolver as suas funções localmente e publicar o projeto na sua aplicação de função utilizando o [Código do Estúdio Visual](./create-first-function-vs-code-csharp.md#create-an-azure-functions-project) ou as [Ferramentas Principais de Funções Azure.](functions-run-local.md#create-a-local-functions-project)  

1. A partir do menu esquerdo da janela **Funções,** selecione **Funções** e selecione **Adicione** no menu superior. 
 
1. A partir da janela **Nova Função,** selecione **'Desír do gatilho'.**

    ![Escolha a função de gatilho HTTP](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. Na janela **Nova Função,** aceite o nome predefinido para **Nova Função,** ou introduza um novo nome. 

1. Escolha **o Anónimo** na lista de abandono do **nível** de autorização e, em seguida, selecione **Criar Função**.

    Azure cria a função de gatilho HTTP. Agora, pode enviar um pedido HTTP para executar a função nova.

## <a name="test-the-function"></a>Testar a função

1. Na sua nova função de gatilho HTTP, selecione **Código + Teste** a partir do menu esquerdo e, em seguida, selecione Obter URL de **função** a partir do menu superior.

    ![Selecione Obter URL de função](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. Na caixa de diálogo URL da **função Get,** selecione o **predefinimento** da lista de drop-down e, em seguida, selecione a Cópia para o ícone **da área de transferência.** 

    ![Copiar o URL da função a partir do portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Cole o URL da função na barra de endereço do navegador. Adicione o valor da cadeia de consulta `?name=<your_name>` ao final deste URL e prima Enter para executar o pedido. 

    O exemplo seguinte mostra a resposta no browser:

    ![Resposta da função no browser.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP.

1. Quando a sua função é executada, são escritas informações de rastreio nos registos. Para ver a saída de **vestígios,** volte à página **Code + Test** no portal e expanda a seta de Logs na parte inferior da página.

   ![Visualizador de registo de funções no portal do Azure.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma aplicação de função com uma função de gatilho HTTP simples.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações, veja [Enlaces de HTTP das Funções do Azure](functions-bindings-http-webhook.md).
