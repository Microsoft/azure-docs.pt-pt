---
title: Criar aplicações lógicas Pré-visualizar fluxos de trabalho no portal Azure
description: Construa e execute fluxos de trabalho para cenários de automação e integração com a Azure Logic Apps Preview no portal Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: ff938d29d998b6fcf0b2cfae72a9a9e685a10dc5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563969"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Criar fluxos de trabalho apátridas e apátridas no portal Azure com pré-visualização de apps Azure Logic

> [!IMPORTANT]
> Esta capacidade está disponível em pré-visualização pública, é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Com [a Azure Logic Apps Preview,](logic-apps-overview-preview.md)pode construir soluções de automação e integração através de apps, dados, serviços na nuvem e sistemas, criando e executando aplicações lógicas que incluem [fluxos de trabalho *apátridas* e *apátridas*](logic-apps-overview-preview.md#stateful-stateless) no portal Azure, a começar pelo novo tipo de recurso **Logic App (Preview).** Com este novo tipo de aplicação lógica, pode construir múltiplos fluxos de trabalho que são alimentados pelo redesenhado Azure Logic Apps Preview tempo de funcionamento, que proporciona portabilidade, melhor desempenho e flexibilidade para implantar e executar em vários ambientes de hospedagem, não só Azure, mas também recipientes Docker. Para saber mais sobre o novo tipo de aplicação lógica, consulte [a Visão geral para pré-visualização de apps Azure Logic.](logic-apps-overview-preview.md)

![Screenshot que mostra o portal Azure com o designer de fluxo de trabalho para o recurso "Logic App (Preview)".](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

No portal Azure, pode começar por criar um novo recurso **Logic App (Preview).** Embora também possa começar por [criar um projeto no Código do Estúdio Visual com a extensão Azure Logic Apps (Preview),](create-stateful-stateless-workflows-visual-studio-code.md)ambas as abordagens fornecem a capacidade para implementar e executar a sua aplicação lógica nos mesmos tipos de ambientes de hospedagem.

Entretanto, ainda pode criar o tipo de aplicação lógica original. Embora as experiências de desenvolvimento no portal diferam entre os tipos de aplicações lógicas originais e novas, a sua subscrição Azure pode incluir ambos os tipos. Pode ver e aceder a todas as aplicações lógicas implementadas na sua subscrição Azure, mas as aplicações estão organizadas nas suas próprias categorias e secções.

Este artigo mostra como construir a sua aplicação lógica e fluxo de trabalho no portal Azure utilizando o tipo de recurso **Logic App (Preview)** e executando estas tarefas de alto nível:

* Crie o novo recurso de aplicação lógica e adicione um fluxo de trabalho em branco.

* Adicione um gatilho e ação.

* Desencadeie uma fuga de fluxo de trabalho.

* Veja a execução do fluxo de trabalho e desencadeie a história.

* Ativar ou abrir os Insights de Aplicação após a implementação.

* Permitir a história da execução para fluxos de trabalho apátridas.

> [!NOTE]
> Para obter informações sobre os problemas atuais conhecidos, reveja a [página de Questões Conhecidas de Pré-Visualização Pública de Aplicações Lógicas no GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Uma [conta de Armazenamento Azure](../storage/common/storage-account-overview.md) porque o recurso Logic App **(Preview)** é alimentado por Azure Functions e tem [requisitos de armazenamento semelhantes às aplicações de função.](../azure-functions/storage-considerations.md) Pode utilizar uma conta de armazenamento existente, ou pode criar uma conta de armazenamento com antecedência ou durante a criação de aplicações lógicas.

  > [!NOTE]
  > [Aplicações lógicas imponentes](logic-apps-overview-preview.md#stateful-stateless) realizam transações de armazenamento, tais como a utilização de filas para agendar e armazenar estados de fluxo de trabalho em mesas e bolhas. Estas transações incorrem em [encargos de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/). Para obter mais informações sobre como as aplicações lógicas imponentes armazenam dados em armazenamento externo, consulte [Stateful versus apátrida.](logic-apps-overview-preview.md#stateful-stateless)

* Para se deslocar para um contentor Docker, precisa de uma imagem de contentor docker existente. Por exemplo, pode criar esta imagem através [do Registo do Contentor Azure,](../container-registry/container-registry-intro.md)do Serviço de [Aplicações](../app-service/overview.md)ou [da Instância do Contentor Azure.](../container-instances/container-instances-overview.md) 

* Para construir a mesma aplicação lógica de exemplo neste artigo, precisa de uma conta de e-mail do Office 365 Outlook que utilize uma conta de trabalho ou escola da Microsoft para iniciar scontabilidade.

  Se optar por utilizar um [conector de e-mail diferente que seja suportado por Azure Logic Apps](/connectors/)– como Outlook.com ou [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)– ainda pode seguir o exemplo, e os passos gerais gerais são os mesmos, mas a interface e as opções do utilizador podem diferir de alguma forma. Por exemplo, se utilizar o conector Outlook.com, use a sua conta pessoal da Microsoft para iniciar scontabilidade.

* Para testar a aplicação lógica de exemplo que cria neste artigo, precisa de uma ferramenta que possa enviar chamadas para o gatilho 'Pedido', que é o primeiro passo na aplicação lógica de exemplo. Se não tiver tal ferramenta, pode descarregar, instalar e utilizar [o Carteiro](https://www.postman.com/downloads/).

* Se criar a sua aplicação lógica com configurações que suportam a utilização de [Application Insights,](../azure-monitor/app/app-insights-overview.md)pode opcionalmente ativar o registo de diagnósticos e o rastreio da sua aplicação lógica. Pode fazê-lo quando cria a sua aplicação lógica ou após a implementação. Precisa de ter uma instância de Insights de Aplicação, mas pode criar este recurso [com antecedência](../azure-monitor/app/create-workspace-resource.md), quando criar a sua aplicação lógica, ou após a implementação.

## <a name="create-the-logic-app-resource"></a>Crie o recurso de aplicação lógica

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Na caixa de pesquisa do portal Azure, insira `logic app preview` e selecione **a Aplicação Lógica (Preview)**.

   ![Screenshot que mostra a caixa de pesquisa do portal Azure com o termo de pesquisa "logic app preview" e o recurso "Logic App (Preview)" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. Na página **Logic App (Preview),** selecione **Adicionar**.

1. Na página **Create Logic App (Preview),** no separador **Basics,** forneça estas informações sobre a sua aplicação lógica.

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Subscrição** | Yes | <*Nome de subscrição Azure*> | A subscrição Azure para usar para a sua aplicação lógica. |
   | **Grupo de recursos** | Yes | <*Nome de grupo Azure-recursos*> | O grupo de recursos Azure onde cria a sua aplicação lógica e recursos relacionados. Este nome de recurso deve ser único entre regiões e pode conter apenas letras, números, hífens **-** (, sublinha **(_ ,** parênteses (**(()**- e períodos **(. . . .** <p><p>Este exemplo cria um grupo de recursos chamado `Fabrikam-Workflows-RG` . |
   | **Nome de aplicativo lógico** | Yes | <*lógica-app-nome*> | O nome a usar para a sua aplicação lógica. Este nome de recurso deve ser único entre regiões e pode conter apenas letras, números, hífens **-** (, sublinha **(_ ,** parênteses (**(()**- e períodos **(. . . .** <p><p>Este exemplo cria uma aplicação lógica chamada `Fabrikam-Workflows` . <p><p>**Nota:** O nome da sua aplicação lógica obtém automaticamente o sufixo, `.azurewebsites.net` porque o recurso Logic App **(Preview)** é alimentado por Azure Functions, que utiliza a mesma convenção de nomeação de aplicações. |
   | **Publicar** | Yes | <*implantação-ambiente*> | O destino de implementação para a sua aplicação lógica. Pode deslocar-se para Azure selecionando **workflow** ou **Docker Container**. <p><p>Este exemplo utiliza **o Workflow,** que implementa o recurso **Logic App (Preview)** para o portal Azure. <p><p>**Nota:** Antes de selecionar **o Docker Container,** certifique-se de que cria a sua imagem do recipiente Docker. Por exemplo, pode criar esta imagem através [do Registo do Contentor Azure,](../container-registry/container-registry-intro.md)do Serviço de [Aplicações](../app-service/overview.md)ou [da Instância do Contentor Azure.](../container-instances/container-instances-overview.md) Desta forma, depois de selecionar o **Docker Container,** pode [especificar o recipiente que pretende utilizar nas definições da sua aplicação lógica](#set-docker-container). |
   | **Região** | Yes | <*Região de Azure*> | A região de Azure deve utilizar ao criar o seu grupo de recursos e recursos. <p><p>Este exemplo usa **o Oeste dos EUA.** |
   |||||

   Eis um exemplo:

   ![Screenshot que mostra o portal Azure e a página "Create Logic App (Preview)".](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Em seguida, no **separador Hospedagem,** forneça esta informação sobre a solução de armazenamento e plano de hospedagem para usar para a sua aplicação lógica.

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Conta de armazenamento** | Yes | <*Nome da conta de armazenamento Azure*> | A [conta de armazenamento Azure](../storage/common/storage-account-overview.md) para usar para transações de armazenamento. Este nome de recurso deve ser único em todas as regiões e ter 3-24 caracteres com apenas números e letras minúsculas. Ou selecione uma conta existente ou crie uma nova conta. <p><p>Este exemplo cria uma conta de armazenamento chamada `fabrikamstorageacct` . |
   | **Tipo de plano** | Yes | <*Plano de acolhimento de Azure*> | O [plano de hospedagem](../app-service/overview-hosting-plans.md) para implementar a sua aplicação lógica, que é ou o plano de serviço [**de funções Premium**](../azure-functions/functions-premium-plan.md) ou [ **App** (Dedicado)](../azure-functions/dedicated-plan.md). A sua escolha afeta as capacidades e os níveis de preços que mais tarde estão disponíveis para si. <p><p>Este exemplo utiliza o **plano de serviço da App.** <p><p>**Nota:** Semelhante às Funções Azure, o tipo de recurso **Logic App (Preview)** requer um plano de hospedagem e um nível de preços. Os planos de consumo não são suportados nem disponíveis para este tipo de recurso. Para mais informações, reveja estes tópicos: <p><p>- [Escala de funções Azure e hospedagem](../azure-functions/functions-scale.md) <br>- [Detalhes dos preços do Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/) <p><p>Por exemplo, o plano Functions Premium fornece acesso a capacidades de networking, tais como conectar e integrar-se privadamente com redes virtuais Azure, semelhantes às Funções Azure quando cria e implementa as suas aplicações lógicas. Para mais informações, reveja estes tópicos: <p><p>- [Opções de networking de funções Azure](../azure-functions/functions-networking-options.md) <br>- [Aplicativos Azure Logic Running Anywhere - Possibilidades de networking com pré-visualização de apps Azure Logic](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047) |
   | **Plano do Windows** | Yes | <*nome do plano*> | O nome do plano para usar. Ou selecione um plano existente ou forneça o nome para um novo plano. <p><p>Este exemplo usa o `Fabrikam-Service-Plan` nome. |
   | **SKU e tamanho** | Yes | <*nível de preços*> | O [nível de preços](../app-service/overview-hosting-plans.md) a utilizar para hospedar a sua aplicação lógica. As suas escolhas são afetadas pelo tipo de plano que escolheu anteriormente. Para alterar o nível predefinido, selecione **Alterar o tamanho**. Em seguida, pode selecionar outros níveis de preços, com base na carga de trabalho de que necessita. <p><p>Este exemplo utiliza o nível de preços de **F1** gratuito para cargas de trabalho **Dev/Test.** Para mais informações, reveja [os detalhes dos preços do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/) |
   |||||

1. Em seguida, se as definições de criação e implementação suportam usando [o Application Insights,](../azure-monitor/app/app-insights-overview.md)pode opcionalmente ativar o registo de diagnósticos e o rastreio da sua aplicação lógica.

   1. No **separador Monitor,** no âmbito **de Insights de Aplicação,** desembaia-se **"Ativar os Insights de Aplicação"** para **Sim,** se ainda não tiver sido selecionado.

   1. Para a definição **de Insights de Aplicação,** selecione uma instância de Insights de Aplicação existente, ou se pretende criar uma nova instância, selecione **Criar novo** e fornecer o nome que pretende utilizar.

1. Depois de o Azure validar as definições da sua aplicação lógica, no **separador 'Rever + criar',** selecione **Criar**.

   Por exemplo:

   ![Screenshot que mostra o portal Azure e novas configurações de recursos de aplicativos lógicos.](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > Se tiver um erro de validação depois de selecionar **Criar,** abra e reveja os detalhes de erro. Por exemplo, se a sua região selecionada atingir uma quota de recursos que está a tentar criar, poderá ter de experimentar uma região diferente.

   Depois de o Azure terminar a implementação, a sua aplicação lógica está automaticamente ao vivo e a funcionar, mas ainda não faz nada porque não existem fluxos de trabalho.

1. Na página de conclusão da implementação, selecione **Ir para o recurso para** que possa começar a construir o seu fluxo de trabalho. Se selecionou **o Docker Container** para a implementação da sua aplicação lógica, continue com os [passos para fornecer informações sobre o contentor Docker.](#set-docker-container)

   ![Screenshot que mostra o portal Azure e a implementação acabada.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>Especificar o recipiente Docker para a implantação

Antes de começar estes passos, precisa de uma imagem de contentor Docker. Por exemplo, pode criar esta imagem através [do Registo do Contentor Azure,](../container-registry/container-registry-intro.md)do Serviço de [Aplicações](../app-service/overview.md)ou [da Instância do Contentor Azure.](../container-instances/container-instances-overview.md) Em seguida, pode fornecer informações sobre o seu recipiente Docker depois de criar a sua aplicação lógica.

1. No portal Azure, aceda ao recurso da sua aplicação lógica.

1. No menu de aplicativos lógicos, em **Definições,** selecione **Centro de Implementação**.

1. No painel **do Centro de Implantação,** siga as instruções para fornecer e gerir os detalhes do seu recipiente Docker.

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Adicione um fluxo de trabalho em branco

1. Depois de o Azure abrir o recurso, no menu da sua aplicação lógica, selecione **Workflows**. Na barra **de ferramentas Workflows,** selecione **Adicionar**.

   ![Screenshot que mostra o menu de recursos de aplicação lógica com "Workflows" selecionados e, em seguida, hen na barra de ferramentas, "Add" é selecionado.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. Depois de abrir o painel de **trabalho Novo,** forneça um nome para o seu fluxo de trabalho e escolha o tipo de fluxo de trabalho [ **stateful** ou **apátrida.**](logic-apps-overview-preview.md#stateful-stateless) Quando concluir, selecione **Criar**.

   Este exemplo adiciona um fluxo de trabalho em branco chamado `Fabrikam-Stateful-Workflow` . Por predefinição, o fluxo de trabalho está ativado mas não faz nada até adicionar um gatilho e ações.

   ![Screenshot que mostra o novo fluxo de trabalho em branco "Fabrikam-Stateful-Workflow".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. Em seguida, abra o fluxo de trabalho em branco no designer para que possa adicionar um gatilho e uma ação.

   1. A partir da lista de fluxos de trabalho, selecione o fluxo de trabalho em branco.

   1. No menu de fluxo de trabalho, em **Developer,** **selecione Designer**.

      Na superfície do designer, o pedido **de operação Escolha** já aparece e é selecionado por padrão para que o painel **de gatilho adicionar um painel de gatilho** também apareça aberto.

      ![Screenshot que mostra o designer de fluxo de trabalho aberto com "Escolha uma operação" selecionada na superfície do designer.](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Adicione um gatilho e uma ação

Este exemplo constrói um fluxo de trabalho que tem estes passos:

* O gatilho [de pedido](../connectors/connectors-native-reqres.md)incorporado , **Quando é recebido um pedido HTTP**, que recebe chamadas ou pedidos de entrada e cria um ponto final que outros serviços ou aplicações lógicas podem ligar.

* A [ação Do Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), Enviar um **e-mail**.

* A ação [Resposta](../connectors/connectors-native-reqres.md)incorporada, que utiliza para enviar uma resposta e devolver dados ao chamador.

### <a name="add-the-request-trigger"></a>Adicione o gatilho do pedido

Antes de poder adicionar um gatilho a um fluxo de trabalho em branco, certifique-se de que o designer de fluxo de trabalho está aberto e que o pedido **de operação Escolha** é selecionado na superfície do designer.

1. Junto à superfície do designer, no painel **De gatilho Adicionar um painel de gatilho,** sob a caixa de pesquisa de **operação,** verifique se o separador **Incorporado** está selecionado. Este separador mostra gatilhos que funcionam de forma nativa em Azure Logic Apps.

1. Na caixa de pesquisa de **operação,** insira `when a http request` e selecione o gatilho de pedido incorporado que está nomeado **Quando um pedido HTTP é recebido**.

   ![Screenshot que mostra o designer e **Adicione um painel de gatilho** com o gatilho "Quando um pedido HTTP é recebido" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   Quando o gatilho aparece no designer, o painel de detalhes do gatilho abre-se para mostrar as propriedades, configurações e outras ações do gatilho.

   ![Screenshot que mostra o designer com o gatilho "Quando um pedido HTTP é recebido" selecionado e detona detalhes abertos.](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Se o painel de detalhes não aparecer, certifique-se de que o gatilho é selecionado no designer.

1. Se precisar de eliminar um item do designer, [siga estes passos para eliminar itens do designer.](#delete-from-designer)

1. Para guardar o seu trabalho, na barra de ferramentas do designer, **selecione Save**.

   Quando se guarda um fluxo de trabalho pela primeira vez, e esse fluxo de trabalho começa com um gatilho De pedido, o serviço De aplicações lógicas gera automaticamente um URL para um ponto final criado pelo gatilho 'Pedido'. Mais tarde, quando testar o seu fluxo de trabalho, envia um pedido a este URL, que dispara o gatilho e inicia o fluxo de trabalho.

### <a name="add-the-office-365-outlook-action"></a>Adicione a ação do Office 365 Outlook

1. No designer, sob o gatilho que adicionou, selecione **Novo passo**.

   O pedido **de operação Escolha** aparece no designer e o **painel de ação Add abre** para que possa selecionar a próxima ação.

   > [!NOTE]
   > Se o painel **de ação Adicionar um** painel de ação mostrar a mensagem de erro, "Não é possível ler o 'filtro' da propriedade de indefinido", guardar o seu fluxo de trabalho, recarregar a página, reabrir o seu fluxo de trabalho e tentar novamente.

1. No painel de ação Adicionar um painel de **ação,** sob a caixa de pesquisa de **operação,** selecione **Azure**. Este separador mostra os conectores geridos que estão disponíveis e implantados em Azure.

   > [!NOTE]
   > Se o **painel de ação Adicionar um** painel de ação mostrar a mensagem de erro, `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` guarde o seu fluxo de trabalho, recarregue a página, reabra o seu fluxo de trabalho e tente adicionar novamente a ação.

   Este exemplo utiliza a ação Do Office 365 Outlook denominada **Enviar um e-mail (V2)**.

   ![Screenshot que mostra o designer e o painel **Adicionar um painel de ação** com a ação do Office 365 Outlook "Enviar um e-mail" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. No painel de detalhes da ação, no separador **Criar Ligação,** selecione **Iniciar sessão** para que possa criar uma ligação à sua conta de e-mail.

   ![Screenshot que mostra o designer e o painel de detalhes "Enviar um e-mail (V2)" com "Iniciar" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. Quando lhe for solicitado o consentimento para aceder à sua conta de e-mail, inscreva-se com as credenciais da sua conta.

   > [!NOTE]
   > Se receber a mensagem de erro, `Failed with error: 'The browser is closed.'. Please sign in again` verifique se o seu navegador bloqueia cookies de terceiros. Se estes cookies estiverem bloqueados, tente adicionar `https://portal.azure.com` à lista de sites que podem usar cookies. Se estiver a utilizar o modo incógnito, certifique-se de que os cookies de terceiros não estão bloqueados durante o funcionamento nesse modo.
   > 
   > Se necessário, recarregue a página, abra o seu fluxo de trabalho, adicione novamente a ação de e-mail e tente criar a ligação.

   Depois de Azure criar a ligação, o Enviar uma ação **de e-mail** aparece no designer e é selecionado por padrão. Se a ação não for selecionada, selecione a ação para que o painel de detalhes também esteja aberto.

1. No painel de pormenores da ação, no separador **Parâmetros,** forneça as informações necessárias para a ação, por exemplo:

   ![Screenshot que mostra o designer e o painel de detalhes "Enviar um e-mail" com o separador "Parâmetros" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Para** | Yes | <*seu endereço de e-mail*> | O destinatário do e-mail, que pode ser o seu endereço de e-mail para efeitos de teste. Este exemplo utiliza o e-mail fictício, `sophiaowen@fabrikam.com` . |
   | **Assunto** | Yes | `An email from your example workflow` | O assunto do e-mail |
   | **Corpo** | Yes | `Hello from your example workflow!` | O conteúdo do corpo de e-mail |
   ||||

   > [!NOTE]
   > Ao escoar quaisquer alterações no painel de detalhes nas **definições**, **Resultado Estático** ou Separadores **De Execução,** certifique-se de que seleciona **Feito** para escoar essas alterações antes de mudar de separador ou alterar o foco para o designer. Caso contrário, o designer não vai manter as suas mudanças.

1. Guarde o seu trabalho. Na barra de ferramentas do designer, **selecione Save**.

1. Se o seu ambiente tiver requisitos de rede rígidos ou firewalls que limitem o tráfego, tem de configurar permissões para quaisquer ligações de gatilho ou ação que existam no seu fluxo de trabalho. Para encontrar o totalmente qualificado 

   Caso contrário, para testar o seu fluxo de trabalho, [desencadeie manualmente uma execução](#trigger-workflow).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Encontre nomes de domínio para acesso a firewall

Antes de implementar a sua aplicação lógica e executar o seu fluxo de trabalho no portal Azure, se o seu ambiente tiver requisitos de rede ou firewalls rigorosos que limitem o tráfego, tem de configurar permissões de rede ou firewall para quaisquer ligações de gatilho ou ação nos fluxos de trabalho que existam na sua aplicação lógica.

Para encontrar os nomes de domínio totalmente qualificados (FQDNs) para estas ligações, siga estes passos:

1. No menu de aplicativos logic, em **Workflows,** selecione **Connections**. No separador **API Connections,** selecione o nome de recursos da ligação, por exemplo:

   ![Screenshot que mostra o portal Azure e menu de aplicativos lógico com o nome de recurso de conexão "Connections" e "offic365" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connections.png)

1. Expanda o seu navegador de largura suficientemente grande para que quando o **JSON View** aparecer no canto superior direito do navegador, selecione **JSON View**.

   ![Screenshot que mostra o portal Azure e o painel de conexão API com "JSON View" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-view-json.png)

1. Encontre, copie e guarde o valor da `connectionRuntimeUrl` propriedade em algum lugar seguro para que possa configurar a sua firewall com esta informação.

   ![Screenshot que mostra o valor da propriedade "connectionRuntimeUrl" selecionado.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. Para cada ligação, repita os passos relevantes.

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>Desencadear o fluxo de trabalho

Neste exemplo, o fluxo de trabalho funciona quando o gatilho do Pedido recebe um pedido de entrada, que é enviado para o URL para o ponto final que é criado pelo gatilho. Quando guardou o fluxo de trabalho pela primeira vez, o serviço De aplicações lógicas gerou automaticamente este URL. Portanto, antes de enviar este pedido para desencadear o fluxo de trabalho, precisa encontrar este URL.

1. No designer de fluxo de trabalho, selecione o gatilho Request que é nomeado **Quando um pedido HTTP é recebido**.

1. Depois de o painel de detalhes abrir, no separador **Parâmetros,** encontre a propriedade **URL HTTP POST.** Para copiar o URL gerado, selecione o **Url copy** (ícone de ficheiro de cópia) e guarde o URL em outro lugar por enquanto. O URL segue este formato:

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Screenshot que mostra o designer com o trigger de pedido e URL de ponto final na propriedade "HTTP POST URL".](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   Para este exemplo, o URL é assim:

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > Também pode encontrar o URL de ponto final no painel de **visão geral** da sua aplicação lógica na propriedade **URL workflow.**
   >
   > 1. No menu de recursos, selecione **Overview**.
   > 1. No **painel de visão** geral, encontre a propriedade **URL workflow.**
   > 1. Para copiar o URL do ponto final, mova o ponteiro sobre a extremidade do texto URL do ponto final e selecione **Copy to clipboard** (ícone de ficheiro de cópia).

1. Para testar o URL enviando um pedido, abra [o Carteiro](https://www.postman.com/downloads/) ou a sua ferramenta preferida para criar e enviar pedidos.

   Este exemplo continua usando o Carteiro. Para mais informações, consulte [o Carteiro A Começar.](https://learning.postman.com/docs/getting-started/introduction/)

   1. Na barra de ferramentas do Carteiro, selecione **New**.

      ![Screenshot que mostra Carteiro com novo botão selecionado](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. No painel **Create New,** em **Blocos de Construção,** selecione **Request**.

   1. Na janela **Pedido de Salvamento,** no **nome Pedido,** forneça um nome para o pedido, por exemplo, `Test workflow trigger` .

   1. Em **Seleção de uma coleção ou pasta para guardar para**, selecione Create **Collection**.

   1. Em **Todas as Coleções,** forneça um nome para a coleção para a organização dos seus pedidos, prima Enter e selecione **Save to <*nome* > de coleção**. Este exemplo usa `Logic Apps requests` como nome de coleção.

      O painel de pedido do carteiro abre para que possa enviar um pedido para o URL do ponto final para o gatilho do pedido.

      ![Screenshot que mostra carteiro com o painel de pedido aberto](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. No painel de pedidos, na caixa de endereços que está ao lado da lista de métodos, que atualmente mostra **GET** como o método de pedido predefinido, cole o URL que copiou anteriormente e selecione **Enviar**.

      ![Screenshot que mostra O Carteiro e URL de ponto final na caixa de endereço com botão enviar selecionado](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      Quando o gatilho dispara, o fluxo de trabalho do exemplo corre e envia um e-mail que aparece semelhante a este exemplo:

      ![Screenshot que mostra o email do Outlook como descrito no exemplo](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>Rever o histórico de execuções

Para um fluxo de trabalho imponente, após cada execução de fluxo de trabalho, você pode ver o histórico de execução, incluindo o estado para a execução geral, para o gatilho, e para cada ação juntamente com as suas entradas e saídas. No portal Azure, a história da execução e as histórias de gatilho aparecem ao nível do fluxo de trabalho, e não ao nível da aplicação lógica. Para rever as histórias do gatilho fora do contexto da história da execução, consulte [as histórias do gatilho do gatilho.](#view-trigger-histories)

1. No portal Azure, no menu do seu fluxo de trabalho, selecione **Monitor**.

   O **painel monitor** mostra o histórico de execução para o fluxo de trabalho.

   ![Screenshot que mostra o painel "Monitor" do fluxo de trabalho e executar a história.](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > Se o estado de execução mais recente não aparecer na barra de ferramentas do **painel monitor,** selecione **Refresh**. Nenhuma corrida acontece para um gatilho que é ignorado devido a critérios não atendidos ou não encontrar dados.

   | Estado de execução | Description |
   |------------|-------------|
   | **Abortada** | A execução parou ou não terminou devido a problemas externos, por exemplo, uma falha do sistema ou subscrição do Azure caducado. |
   | **Cancelado** | A corrida foi desencadeada e começou, mas recebeu um pedido de cancelamento. |
   | **Com falhas** | Pelo menos uma ação na corrida falhou. Não foram criadas ações subsequentes no fluxo de trabalho para lidar com a falha. |
   | **Em Execução** | A corrida foi desencadeada e está em curso, mas este estatuto também pode aparecer para uma corrida que é acelerada devido a [limites](logic-apps-limits-and-config.md) de ação ou ao [atual plano de preços.](https://azure.microsoft.com/pricing/details/logic-apps/) <p><p>**Dica**: Se configurar [o registo de diagnósticos,](monitor-logic-apps-log-analytics.md)pode obter informações sobre quaisquer eventos de aceleração que ocorram. |
   | **Com êxito** | A corrida conseguiu. Se alguma ação falhou, uma ação subsequente no fluxo de trabalho tratou dessa falha. |
   | **Esgotado** | O tempo de execução foi esgotado porque a duração atual excedeu o limite de duração de execução, que é controlado pela [ **retenção**](logic-apps-limits-and-config.md#run-duration-retention-limits)do histórico run em dias de fixação . A duração de uma corrida é calculada utilizando o tempo de início da execução e o limite de duração da execução nessa hora de início. <p><p>**Nota:** Se a duração da execução também exceder o limite atual de retenção do histórico de *execução*, que também é controlado pela [ **retenção**](logic-apps-limits-and-config.md#run-duration-retention-limits)do histórico run em dias de fixação , a execução é limpa do histórico de execuções por um trabalho de limpeza diário. Quer os tempos de funcionação se esgotam ou completem, o período de retenção é sempre calculado utilizando o tempo de início da execução e o limite de retenção *atual.* Então, se reduzir o limite de duração para uma corrida de voo, o tempo de execução se esgota. No entanto, a execução permanece ou é retirada do histórico de corridas com base no facto de a duração da corrida ter excedido o limite de retenção. |
   | **Em espera** | A execução não começou ou está interrompida, por exemplo, devido a uma instância de fluxo de trabalho anterior que ainda está em funcionamento. |
   |||

1. Para rever o estado de cada passo numa corrida, selecione a execução que pretende rever.

   A vista de detalhes de execução abre e mostra o estado para cada passo na corrida.

   ![Screenshot que mostra a vista de detalhes de execução com o estado de cada passo no fluxo de trabalho.](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   Aqui estão os possíveis estatutos que cada passo no fluxo de trabalho pode ter:

   | Estado de ação | Ícone | Description |
   |---------------|------|-------------|
   | **Abortada** | ![Ícone para estado de ação "abortado"][aborted-icon] | A ação parou ou não terminou devido a problemas externos, por exemplo, uma falha no sistema ou subscrição do Azure caducado. |
   | **Cancelado** | ![Ícone para estado de ação "cancelado"][cancelled-icon] | A ação estava a decorrer, mas recebeu um pedido de cancelamento. |
   | **Com falhas** | ![Ícone para estado de ação "falhado"][failed-icon] | A ação falhou. |
   | **Em Execução** | ![Ícone para o estado de ação "Running"][running-icon] | A ação está em curso. |
   | **Ignorado** | ![Ícone para estado de ação "ignorado"][skipped-icon] | A ação foi ignorada porque a ação imediatamente anterior falhou. Uma ação tem uma `runAfter` condição que exige que a ação anterior termine com sucesso antes que a ação atual possa ser executada. |
   | **Com êxito** | ![Ícone para o estado de ação "Bem sucedido"][succeeded-icon] | A ação foi bem sucedida. |
   | **Conseguiu com recauchutagens** | ![Ícone para estatuto de ação "Bem sucedido com recauchutagens"][succeeded-with-retries-icon] | A ação foi bem sucedida, mas só depois de uma ou mais retrólmas. Para rever o histórico de relemisso, na visão de detalhes do histórico de execução, selecione essa ação para que possa ver as entradas e saídas. |
   | **Esgotado** | ![Ícone para estado de ação "Timed out"][timed-out-icon] | A ação parou devido ao limite de tempo especificado pelas definições dessa ação. |
   | **Em espera** | ![Ícone para estado de ação "espera"][waiting-icon] | Aplica-se a uma ação webhook que está à espera de um pedido de entrada de um chamador. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. Para rever as entradas e saídas para um passo específico, selecione esse passo.

   ![Screenshot que mostra as entradas e saídas na ação selecionada "Enviar um e-mail".](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. Para rever ainda mais as entradas e saídas brutas para esse passo, selecione **Mostrar entradas brutas** ou **mostrar saídas brutas**.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>Rever histórias de gatilho

Para um fluxo de trabalho imponente, pode rever o histórico do gatilho para cada execução, incluindo o estado do gatilho juntamente com entradas e saídas, separadamente do contexto de história de [execução](#view-run-history). No portal Azure, o histórico de gatilhos e a história de execução aparecem ao nível do fluxo de trabalho, não ao nível da aplicação lógica. Para encontrar estes dados históricos, siga estes passos:

1. No portal Azure, no menu do seu fluxo de trabalho, em **Programador,** selecione **Trigger Histories**.

   O **painel De trigger Histories** mostra as histórias do gatilho para as execuções do seu fluxo de trabalho.

1. Para rever um histórico específico do gatilho, selecione o ID para essa execução.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Ativar ou abrir Insights de Aplicação após a implementação

Durante a execução do fluxo de trabalho, a sua aplicação lógica emite telemetria juntamente com outros eventos. Você pode usar esta telemetria para obter uma melhor visibilidade sobre como o seu fluxo de trabalho funciona bem e como o tempo de funcionamento das Apps Lógicas funciona de várias maneiras. Pode monitorizar o seu fluxo de trabalho utilizando [o Application Insights,](../azure-monitor/app/app-insights-overview.md)que fornece telemetria em tempo real (métricas vivas). Esta capacidade pode ajudá-lo a investigar falhas e problemas de desempenho mais facilmente quando utiliza estes dados para diagnosticar problemas, configurar alertas e construir gráficos.

Se as configurações de criação e implementação da sua aplicação lógica suportam usando [o Application Insights,](../azure-monitor/app/app-insights-overview.md)pode opcionalmente permitir o registo de diagnósticos e o rastreio da sua aplicação lógica. Pode fazê-lo quando criar a sua aplicação lógica no portal Azure ou após a implementação. Precisa de ter uma instância de Insights de Aplicação, mas pode criar este recurso [com antecedência](../azure-monitor/app/create-workspace-resource.md), quando criar a sua aplicação lógica, ou após a implementação.

Para ativar o Application Insights numa aplicação lógica implementada ou abrir o painel de insights de aplicação, se já estiver ativado, siga estes passos:

1. No portal Azure, encontre a sua aplicação lógica implementada.

1. No menu de aplicativos lógicos, em **Definições,** selecione **Application Insights**.

1. Se os Insights de Aplicação não estiverem ativados, no painel **de Insights de Aplicação,** selecione **Ligue os Insights de Aplicação**. Depois de atualizar o painel, na parte inferior, **selecione Aplicar**.

   Se os Insights de Aplicação estiverem ativados, no painel **de Insights de Aplicação,** selecione **Ver Dados de Insights de Aplicação**.

Após a abertura do Application Insights, pode rever várias métricas para a sua aplicação lógica. Para mais informações, reveja estes tópicos:

* [Aplicativos Azure Logic Running Anywhere - Monitor com Insights de Aplicação - parte 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Aplicativos Azure Logic Running Anywhere - Monitor com Insights de Aplicação - parte 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Permitir a história de execução para fluxos de trabalho apátridas

Para depurar mais facilmente um fluxo de trabalho apátrida, pode ativar a história de execução para esse fluxo de trabalho e, em seguida, desativar o histórico de execução quando terminar. Siga estes passos para o portal Azure, ou se estiver a trabalhar no Código do Estúdio Visual, consulte [Criar fluxos de trabalho stateful e apátridas no Código do Estúdio Visual.](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)

1. No [portal Azure,](https://portal.azure.com)encontre e abra o seu recurso **Logic App (Preview).**

1. No menu da aplicação lógica, em **Definições,** selecione **Configuração**.

1. No **separador Definições de Aplicação,** selecione **nova definição de aplicação**.

1. No painel de definição de **aplicação Add/Edit,** na caixa **Nome,** insira este nome de opção de funcionamento: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Na caixa **Valor,** insira o seguinte valor: `WithStatelessRunHistory`

   Por exemplo:

   ![Screenshot que mostra o portal Azure e o recurso Logic App (Preview) com a "Configuração" > "Nova definição de aplicação" < painel de "Adicionar/Editar a definição de aplicação" aberta e os "Fluxos de Trabalho". {yourWorkflowName}. Opção OperationOptions" definida como "WithStatelessRunHistory".](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. Para terminar esta tarefa, selecione **OK**. Na barra de ferramentas do painel de **configuração,** **selecione Guardar**.

1. Para desativar o histórico de execução quando terminar, ou desativar a `Workflows.{yourWorkflowName}.OperationOptions` propriedade ou apagar o `None` imóvel e o seu valor.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Eliminar itens do designer

Para eliminar um item no seu fluxo de trabalho do designer, siga qualquer um destes passos:

* Selecione o item, abra o menu de atalho do item (Shift+F10) e selecione **Delete**. Para confirmar, selecione **OK**.

* Selecione o item e prima a tecla de eliminação. Para confirmar, selecione **OK**.

* Selecione o item de modo a que os detalhes abram o painel para esse item. No canto superior direito do painel, abra o menu de elipses **(...**) e selecione **Delete**. Para confirmar, selecione **OK**.

  ![Screenshot que mostra um item selecionado no designer com o painel de detalhes abertos mais o botão de elipses selecionado e o comando "Delete".](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Se o menu de elipses não for visível, expanda a janela do seu navegador suficientemente larga para que o painel de detalhes mostre o botão elipses **(...**) no canto superior direito.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Problemas e erros de resolução de problemas

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Faltam novos gatilhos e ações do selecionador de designers para fluxos de trabalho previamente criados

A Azure Logic Apps Preview suporta ações incorporadas para operações de função Azure, Operações Líquidas e Operações XML, tais como **validação XML** e **Transform XML**. No entanto, para aplicações lógicas previamente criadas, estas ações podem não aparecer no designer para que possa selecionar se a sua aplicação lógica utiliza uma versão desatualizada do pacote de extensão, `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Para corrigir este problema, siga estes passos para eliminar a versão desatualizada para que o pacote de extensão possa atualizar automaticamente para a versão mais recente.

> [!NOTE]
> Esta solução específica aplica-se apenas aos recursos **da Logic App (Preview)** que cria utilizando o portal Azure, e não às aplicações lógicas que cria e implementa utilizando o Código do Estúdio Visual e a extensão Azure Logic Apps (Preview). Veja [os gatilhos suportados e faltam ações do designer no Código do Estúdio Visual.](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions)

1. No portal Azure, pare a sua aplicação lógica.

   1. No menu de aplicativos logico, selecione **Overview**.

   1. Na barra de **ferramentas** do painel de ferramentas do painel de ferramentas da visão geral, selecione **Stop**.

1. No menu de aplicativos logic, em **Ferramentas de Desenvolvimento,** selecione **Advanced Tools**.

1. No painel **Ferramentas Avançadas,** selecione **Go,** que abre o ambiente Kudu para a sua aplicação lógica.

1. Na barra de ferramentas Kudu, abra o menu da **consola Debug** e selecione **CMD**. 

   Abre-se uma janela da consola para que possa navegar na pasta do pacote utilizando o pedido de comando. Ou, pode navegar na estrutura do diretório que aparece na janela da consola.

1. Navegue para a seguinte pasta, que contém pastas em versão para o pacote existente:

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Elimine a pasta de versão para o pacote existente. Na janela da consola, pode executar este comando onde substitui `{bundle-version}` pela versão existente:

   `rm -rf {bundle-version}`

   Por exemplo: `rm -rf 1.1.3`

   > [!TIP]
   > Se tiver um erro como "permissão negada" ou "ficheiro em uso", revebre a página no seu navegador e tente novamente os passos anteriores até que a pasta seja eliminada.

1. No portal Azure, volte à página geral da sua aplicação **lógica** e selecione **Restart**.

   O portal recebe e utiliza automaticamente o mais recente pacote.

## <a name="next-steps"></a>Passos seguintes

Gostaríamos de ouvir sobre as suas experiências com esta pré-estreia pública!

* Para bugs ou problemas, [crie os seus problemas no GitHub](https://github.com/Azure/logicapps/issues).
* Para perguntas, pedidos, comentários e outros comentários, [utilize este formulário de feedback](https://aka.ms/lafeedback).
