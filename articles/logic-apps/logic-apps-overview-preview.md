---
title: Descrição geral da Pré-visualização do Azure Logic Apps
description: A Azure Logic Apps Preview é uma solução em nuvem para construir fluxos de trabalho automatizados e apátridas que integram apps, dados, serviços e sistemas com código mínimo para cenários de nível empresarial.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9d8d3cb4bf68f7da2bddabd21272d1011ce92f66
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715212"
---
# <a name="overview-azure-logic-apps-preview"></a>Visão geral: Pré-visualização de apps da Azure Logic Apps

> [!IMPORTANT]
> Esta capacidade está disponível em pré-visualização pública, é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Com a Azure Logic Apps Preview, pode construir soluções de automação e integração através de apps, dados, serviços na nuvem e sistemas, criando e executando aplicações lógicas que incluem [fluxos de trabalho *apátridas* e *apátridas*](#stateful-stateless) utilizando o novo tipo de recurso **Logic App (Preview).** Com este novo tipo de aplicação lógica, pode construir múltiplos fluxos de trabalho que são alimentados pelo redesenhado Azure Logic Apps Preview tempo de funcionamento, que proporciona portabilidade, melhor desempenho e flexibilidade para implantar e executar em vários ambientes de hospedagem, não só Azure, mas também recipientes Docker.

Como é que isto é possível? O tempo de execução redesenhado utiliza o [modelo de extensibilidade Azure Functions](../azure-functions/functions-bindings-register.md) e é hospedado como uma extensão no tempo de funcionamento das Funções Azure. Esta arquitetura significa que você pode executar o novo tipo de aplicativo lógica em qualquer lugar que Azure Functions executa. Pode hospedar o tempo de funcionaamento redesenhado em quase qualquer topologia de rede e escolher qualquer tamanho de computação disponível para lidar com a carga de trabalho necessária que é exigida pelos seus fluxos de trabalho. Para obter mais informações, consulte [Introdução às Funções Azure](../azure-functions/functions-overview.md) e [Funções Azure aciona e encaderna .](../azure-functions/functions-triggers-bindings.md)

Pode criar o recurso **Logic App (Preview)** quer [a partir do portal Azure,](create-stateful-stateless-workflows-azure-portal.md) quer através da [criação de um projeto no Código do Estúdio Visual com a extensão Azure Logic Apps (Preview).](create-stateful-stateless-workflows-visual-studio-code.md) Além disso, no Código do Estúdio Visual, pode construir *e executar localmente* os seus fluxos de trabalho no seu ambiente de desenvolvimento. Quer utilize o portal ou o Código do Estúdio Visual, pode implementar e executar o novo tipo de aplicação lógica nos mesmos tipos de ambientes de hospedagem.

Esta visão geral abrange as seguintes áreas:

* [Diferenças entre a Azure Logic Apps Preview, o ambiente multi-inquilino de Apps Azure Logic Apps e o ambiente de serviço de integração.](#preview-differences)

* [Diferenças entre fluxos de trabalho apátridas e apátridas,](#stateful-stateless)incluindo diferenças de comportamento entre [fluxos de trabalho aninhados e apátridas.](#nested-behavior)

* [Capacidades nesta pré-visualização pública.](#public-preview-contents)

* [Como funciona o modelo de preços.](#pricing-model)

* [Capacidades alteradas, limitadas, indisponíveis ou não suportadas.](#limited-unavailable-unsupported)

* [Limites na pré-visualização de aplicações lógicas Azure](#limits).

Para mais informações, reveja estes outros tópicos:

* [Aplicativos Azure Logic Running Anywhere - Runtime Deep Dive](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Aplicativos lógicos Visualização Pública Problemas Conhecidos (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Como é que o Azure Logic Apps Preview difere?

O tempo de pré-visualização de apps Azure Logic usa a extensibilidade [das funções Azure](../azure-functions/functions-overview.md) e é hospedado como uma extensão no tempo de funcionamento das Funções Azure. Esta arquitetura significa que você pode executar o novo tipo de aplicativo lógica em qualquer lugar que Azure Functions executa. Pode hospedar o tempo de pré-visualização de aplicações Azure Logic em quase todas as topologias de rede que desejar e escolher qualquer tamanho de computação disponível para lidar com a carga de trabalho necessária de que o seu fluxo de trabalho necessita. Para obter mais informações sobre a extensibilidade das funções Azure, consulte [WebJobs SDK: Criando entradas personalizadas e encadernações de saída](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

Com esta nova abordagem, o tempo de pré-visualização de apps Azure Logic e os seus fluxos de trabalho fazem parte da sua app que pode embalar em conjunto. Esta capacidade permite-lhe implementar e executar os seus fluxos de trabalho simplesmente copiando artefactos para o ambiente de hospedagem e iniciando a sua aplicação. Esta abordagem também proporciona uma experiência mais padronizada para a construção de oleodutos em torno dos projetos de fluxo de trabalho para executar os testes e validações necessários antes de implementar alterações em ambientes de produção. Para obter mais informações, consulte [Azure Logic Apps Running Anywhere - Runtime Deep Dive](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564).

O quadro seguinte resume brevemente as diferenças na forma como os fluxos de trabalho partilham recursos, com base no ambiente onde funcionam. Para obter diferenças de limites, consulte [Limits in Azure Logic Apps Preview](#limits).

| Ambiente | Partilha e consumo de recursos |
|-------------|----------------------------------|
| Azure Logic Apps (Multi-inquilino) | Fluxos de trabalho *de clientes em vários inquilinos* partilham o mesmo processamento (computação), armazenamento, rede, e assim por diante. |
| Aplicativos Azure Logic (Pré-visualização) | Os fluxos de trabalho *na mesma aplicação lógica* partilham o mesmo processamento (computação), armazenamento, rede, e assim por diante. |
| Ambiente de serviço de integração (indisponível em Pré-visualização) | Os fluxos de trabalho no *mesmo ambiente* partilham o mesmo processamento (computação), armazenamento, rede, e assim por diante. |
||||

Entretanto, ainda é possível criar o tipo de aplicação lógica original no portal Azure e no Código do Estúdio Visual utilizando a extensão original das Apps Azure Logic. Embora as experiências de desenvolvimento diferam entre os tipos de aplicações lógicas originais e novas, a sua subscrição Azure pode incluir ambos os tipos. Pode ver e aceder a todas as aplicações lógicas implementadas na sua subscrição Azure, mas as aplicações estão organizadas nas suas próprias categorias e secções.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Fluxos de trabalho apátridas e apátridas

* *Com monitorização de estado*

  Crie fluxos de trabalho estatais quando necessitar de guardar, rever ou fazer dados de referência de eventos anteriores. Estes fluxos de trabalho guardam as entradas e saídas para cada ação e seus estados em armazenamento externo, o que torna possível a revisão dos detalhes de execução e história após cada execução termina. Fluxos de trabalho imponentes proporcionam alta resiliência se as interrupções acontecerem. Após a restauração dos serviços e sistemas, pode reconstruir as execuções interrompidas do estado guardado e refazer os fluxos de trabalho até à conclusão. Fluxos de trabalho imponentes podem continuar a funcionar até um ano.

* *Sem estado*

  Crie fluxos de trabalho apátridas quando não precisa de guardar, rever ou fazer dados de referência de eventos anteriores em armazenamento externo para posterior revisão. Estes fluxos de trabalho guardam as entradas e saídas para cada ação e os seus estados *apenas na memória*, em vez de transferir estes dados para o armazenamento externo. Como resultado, os fluxos de trabalho apátridas têm percursos mais curtos que normalmente não são mais do que 5 minutos, desempenho mais rápido com tempos de resposta mais rápidos, maior produção e custos de funcionamento reduzidos porque os detalhes de execução e histórico não são mantidos em armazenamento externo. No entanto, se as interrupções ocorrerem, as execuções interrompidas não são automaticamente restauradas, pelo que o chamador precisa de reenviar as execuções interrompidas manualmente. Estes fluxos de trabalho só podem funcionar de forma sincronizada.

  Para facilitar a depuração, pode permitir a execução da história de um fluxo de trabalho apátrida, que tem algum impacto no desempenho, e depois desativar o histórico de execução quando terminar. Para obter mais informações, consulte [Criar fluxos de trabalho apátridas e apátridas no Código do Estúdio Visual](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) ou criar [fluxos de trabalho apátridas e apátridas no portal Azure](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > Atualmente, os fluxos de trabalho apátridas suportam apenas *ações* para [conectores geridos](../connectors/apis-list.md#managed-api-connectors), que são implantados em Azure, e não gatilhos. Para iniciar o seu fluxo de trabalho, selecione o [pedido incorporado, os centros de eventos ou](../connectors/apis-list.md#built-ins)o gatilho do Service Bus . Estes gatilhos são executados de forma nativa no tempo de pré-visualização das aplicações lógicas Azure. Para obter mais informações sobre gatilhos, ações e conectores limitados, indisponíveis ou não [suportados, consulte capacidades alteradas, limitadas, indisponíveis ou não suportadas](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Diferenças de comportamento aninhadas entre fluxos de trabalho apátridas e apátridas

Pode [tornar um fluxo](../logic-apps/logic-apps-http-endpoint.md) de trabalho chamado a partir de outros fluxos de trabalho que existem no mesmo recurso **Logic App (Preview)** utilizando o gatilho do pedido , [detonador](../connectors/connectors-native-reqres.md) [HTTP Webhook](../connectors/connectors-native-webhook.md), ou gatilhos de conector geridos que tenham o [tipo ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e possam receber pedidos HTTPS.

Aqui estão os padrões de comportamento que os fluxos de trabalho aninhados podem seguir depois de um fluxo de trabalho dos pais chamar um fluxo de trabalho infantil:

* Padrão de sondagem assíncronos

  O progenitor não espera por uma resposta à sua chamada inicial, mas verifica continuamente o histórico da criança até que a criança termine de correr. Por predefinição, os fluxos de trabalho estatais seguem este padrão, que é ideal para fluxos de trabalho infantis de longa duração que podem exceder [os limites de tempo de pedido](../logic-apps/logic-apps-limits-and-config.md).

* Padrão sincronizado ("fogo e esquecimento")

  A criança reconhece o apelo devolvendo imediatamente uma `202 ACCEPTED` resposta, e o progenitor continua para a próxima ação sem esperar pelos resultados da criança. Em vez disso, o progenitor recebe os resultados quando a criança termina de correr. Os fluxos de trabalho de crianças que não incluem uma ação de resposta seguem sempre o padrão sincronizado. Para fluxos de trabalho de crianças, o histórico de execução está disponível para você rever.

  Para permitir este comportamento, na definição JSON do fluxo de trabalho, definir a `operationOptions` propriedade para `DisableAsyncPattern` . Para obter mais informações, consulte [o Gatilho e os tipos de ação - Opções de operação](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Desencadear e esperar

  Para um fluxo de trabalho apátrida infantil, o progenitor aguarda uma resposta que devolva os resultados da criança. Este padrão funciona semelhante ao uso do gatilho HTTP incorporado [ou ação](../connectors/connectors-native-http.md) para chamar um fluxo de trabalho infantil. Os fluxos de trabalho apátridas infantis que não incluem uma ação de resposta devolvem imediatamente uma `202 ACCEPTED` resposta, mas o progenitor espera que a criança termine antes de continuar para a próxima ação. Estes comportamentos aplicam-se apenas aos fluxos de trabalho apátridas infantis.

Esta tabela especifica o comportamento do fluxo de trabalho da criança com base no facto de os pais e a criança serem estatais, apátridas ou se são tipos de fluxo de trabalho mistos:

| Fluxo de trabalho dos pais | Fluxo de trabalho infantil | Comportamento infantil |
|-----------------|----------------|----------------|
| Com monitorização de estado | Com monitorização de estado | Assíncronos ou sincronizados com `"operationOptions": "DisableAsyncPattern"` a definição |
| Com monitorização de estado | Sem estado | Desencadear e esperar |
| Sem estado | Com monitorização de estado | Synchronous (Síncrono) |
| Sem estado | Sem estado | Desencadear e esperar |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Capacidades

A Azure Logic Apps Preview inclui muitas capacidades atuais e adicionais, por exemplo:

* Crie aplicações lógicas e seus fluxos de trabalho a partir de mais de [390 conectores](/connectors/connector-reference/connector-reference-logicapps-connectors) para aplicações e serviços de Software-as-a-Service (SaaS) e Platform-as-a-Service (PaaS) além de conectores para sistemas no local.

  * Alguns conectores geridos, como o Azure Service Bus, a Azure Event Hubs e o SQL Server funcionam de forma semelhante aos gatilhos e ações incorporados que são nativos do tempo de pré-visualização de apps Azure Logic, por exemplo, o gatilho do Pedido e a ação HTTP. Para obter mais informações, consulte [Azure Logic Apps Running Anywhere - Extensibilidade do conector incorporado](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

  * Pode utilizar as ações B2B para Operações Líquidas e Operações XML sem uma conta de integração. Para utilizar estas ações, precisa de ter mapas líquidos, mapas XML ou esquemas XML que pode fazer o upload através das respetivas ações no portal Azure ou adicionar à pasta de Artefactos do seu código de estúdio visual utilizando as **respetivas pastas** **Maps** e **Schemas.**

  * Crie e implemente aplicações lógicas que podem ser executadas em qualquer lugar porque o serviço Azure Logic Apps gera cadeias de conexão Shared Access Signature (SAS) que estas aplicações lógicas podem usar para enviar pedidos para o ponto final de tempo de execução da ligação na nuvem. O serviço De aplicações lógicas guarda estas cadeias de ligação com outras definições de aplicação para que possa armazenar facilmente estes valores no Cofre da Chave Azure quando implementar no Azure.

    > [!NOTE]
    > Por predefinição, um recurso **Logic App (Preview)** tem a sua [identidade gerida atribuída ao sistema](../logic-apps/create-managed-service-identity.md) automaticamente habilitada a autenticar ligações em tempo de execução. Esta identidade difere das credenciais de autenticação ou da cadeia de ligação que utiliza quando cria uma ligação. Se desativar esta identidade, as ligações não funcionarão em tempo de execução. Para ver esta definição, no menu da sua aplicação lógica, em **Definições**, selecione **Identidade**.

* Criar aplicativos lógicos com fluxos de trabalho apátridas que funcionam apenas na memória para que terminem mais rapidamente, respondam mais rapidamente, tenham maior produção e custem menos para executar porque as histórias de execução e dados entre ações não persistem no armazenamento externo. Opcionalmente, pode ativar o histórico de execução para facilitar a depuragem. Para obter mais informações, consulte [aplicações lógicas stateful versus apátridas](#stateful-stateless).

* Executar, testar e desorfiar as suas aplicações lógicas e os seus fluxos de trabalho no ambiente de desenvolvimento do Código do Estúdio Visual.

  Antes de executar e testar a sua aplicação lógica, pode facilitar a depuração adicionando e usando pontos de rutura dentro do **workflow.jsem** ficheiro para um fluxo de trabalho. No entanto, os pontos de rutura são suportados apenas para ações neste momento, não para gatilhos. Para obter mais informações, consulte [Criar fluxos de trabalho apátridas e apátridas no Código do Estúdio Visual.](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)

* Publicar ou implementar diretamente aplicações lógicas e seus fluxos de trabalho do Código do Estúdio Visual para vários ambientes de hospedagem, tais como recipientes Azure e [Docker.](/dotnet/core/docker/introduction)

* Habilita capacidades de registo e rastreio de diagnósticos para a sua aplicação lógica utilizando [o Application Insights](../azure-monitor/app/app-insights-overview.md) quando suportado pelas definições de subscrição e aplicação lógica do Azure.

* Aceder a capacidades de rede, tais como conectar e integrar-se privadamente com redes virtuais Azure, semelhantes às Funções Azure quando cria e implementa as suas aplicações lógicas utilizando o [plano Azure Functions Premium](../azure-functions/functions-premium-plan.md). Para mais informações, reveja estes tópicos:

  * [Opções de rede das Funções do Azure](../azure-functions/functions-networking-options.md)

  * [Aplicativos Azure Logic Running Anywhere - Possibilidades de networking com pré-visualização de apps Azure Logic](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Regenerar chaves de acesso para ligações geridas utilizadas por fluxos de trabalho individuais num recurso **de Aplicação Lógica (Preview).** Para esta tarefa, [siga os mesmos passos para o recurso **Logic Apps** mas ao nível do fluxo de trabalho individual](logic-apps-securing-a-logic-app.md#regenerate-access-keys), não ao nível de recursos de aplicações lógicas.

* Adicione ramos paralelos no novo designer seguindo os mesmos passos que o designer de não-pré-visualização.
 
Para obter mais informações, consulte [capacidades alteradas, limitadas, indisponíveis e não suportadas](#limited-unavailable-unsupported) e a [página de Visualização Pública de Aplicações Lógicas Conhecidas em GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modelo preços

Quando criar o novo tipo de aplicação lógica no portal Azure ou implementar a partir do Código do Estúdio Visual, tem de escolher um plano de hospedagem, seja [o App Service ou o Premium,](../azure-functions/functions-scale.md)para que a sua aplicação lógica possa ser utilizada. Este plano determina o modelo de preços que se aplica à execução da sua aplicação lógica. Se selecionar o plano de Serviço de Aplicações, também deve escolher um [nível de preços.](../app-service/overview-hosting-plans.md)

*Fluxos de* trabalho estatais utilizam [armazenamento externo,](../azure-functions/storage-considerations.md#storage-account-requirements)por isso o [preço do Armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/) aplica-se às transações de armazenamento que o tempo de pré-visualização de apps Azure Logic executa. Por exemplo, as filas são usadas para agendamento, enquanto mesas e bolhas são usadas para armazenar estados de fluxo de trabalho.

> [!NOTE]
> Durante a pré-visualização pública, executar aplicações lógicas no Serviço de Aplicações não incorre em custos *adicionais* em cima do seu plano selecionado.

Para obter mais informações sobre os modelos de preços aplicáveis a este novo tipo de recurso, reveja estes tópicos:

* [Dimensionamento e alojamento de Funções do Azure](../azure-functions/functions-scale.md)
* [Dimensione uma aplicação no Azure App Service](../app-service/manage-scale-up.md)
* [Detalhes de preços de funções Azure](https://azure.microsoft.com/pricing/details/functions/)
* [Detalhes dos preços do Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/)
* [Detalhes dos preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Capacidades alteradas, limitadas, indisponíveis ou não suportadas

Na pré-visualização de apps Azure Logic, estas capacidades mudaram, ou estão atualmente limitadas, indisponíveis ou não suportadas:

* **Suporte ao OS**: Atualmente, o designer no Visual Studio Code não funciona no Linux OS, mas ainda pode implementar aplicações lógicas que usam o tempo de pré-visualização de Apps Lógicas para máquinas virtuais baseadas em Linux. Por enquanto, pode construir as suas aplicações lógicas no Código do Estúdio Visual no Windows ou macOS e depois implementar para uma máquina virtual baseada em Linux.

* **Triggers e ações**: Os gatilhos e ações incorporados são executados de forma nativa no tempo de pré-visualização das aplicações lógicas Azure, enquanto os conectores geridos são implantados em Azure. Alguns gatilhos incorporados não estão disponíveis, como janela deslizante e lote.

  Para iniciar o seu fluxo de trabalho, utilize o [recorrence incorporado, request, HTTP, HTTP Webhook, Event Hubs ou Service Bus trigger](../connectors/apis-list.md). No designer, os gatilhos e ações incorporados aparecem sob o separador **Incorporado,** enquanto os gatilhos e ações geridos aparecem sob o separador **Azure.**

  > [!NOTE]
  > Para executar localmente no Código do Estúdio Visual, os gatilhos e ações baseados em webhook requerem configuração adicional. Para obter mais informações, consulte [Criar fluxos de trabalho apátridas e apátridas no Código do Estúdio Visual.](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)

  * Para *fluxos de trabalho apátridas,* o separador **Azure** não aparece quando seleciona um gatilho porque só pode selecionar [ *ações* de conector geridas , e não acionamentos](../connectors/apis-list.md#managed-api-connectors). Embora possa ativar conectores geridos implantados pelo Azure para fluxos de trabalho apátridas, o designer não mostra nenhum gatilho de conector gerido para adicionar.

  * Para *fluxos de trabalho estatais*, para além dos gatilhos e ações que estão listados como indisponíveis abaixo, ambos os [gatilhos e ações do conector gerido estão](../connectors/apis-list.md#managed-api-connectors) disponíveis para você usar.

  * Estes gatilhos e ações mudaram ou estão atualmente limitados, não suportados ou indisponíveis:

    * [Os gatilhos de gateway de *dados* no local](../connectors/apis-list.md#on-premises-connectors) não estão disponíveis, mas as ações de gateway *estão* disponíveis.

    * [Os conectores personalizados](../connectors/apis-list.md#custom-apis-and-connectors) não estão disponíveis.

    * A ação incorporada, [Funções Azure - Escolha uma função Azure](logic-apps-azure-functions.md) é agora **Operações de Função Azure - Chame uma função Azure**. Esta ação funciona atualmente apenas para funções que são criadas a partir do modelo **de gatilho HTTP.**

      No portal Azure, pode selecionar uma função de gatilho HTTP onde tem acesso, criando uma ligação através da experiência do utilizador. Se inspecionar a definição JSON da ação de função na visão de código ou a **workflow.jsem** ficheiro, a ação refere-se à função utilizando uma `connectionName` referência. Esta versão abstra a informação da função como uma ligação, que pode encontrar noconnections.jsdo seu projeto **no** ficheiro, que está disponível depois de criar uma ligação.

      > [!NOTE]
      > Na versão Preview, a ação de função suporta apenas a autenticação de cadeias de consulta. A Azure Logic Apps Preview obtém a chave predefinida da função ao fazer a ligação, armazena essa chave nas definições da sua aplicação e utiliza a chave para autenticação ao ligar para a função.
      >
      > Tal como na versão original, se renovar esta chave, por exemplo, através da experiência Azure Functions no portal, a ação de função já não funciona devido à chave inválida. Para corrigir este problema, precisa de recriar a ligação à função que pretende ligar ou atualizar as definições da sua aplicação com a nova tecla.

    * A ação incorporada, [Código Inline - Executar Código JavaScript](logic-apps-add-run-inline-code.md) é agora **Inline Code Operations - Executar javaScript em linha**.

      * As ações de Operações de Código Inline já não requerem uma conta de integração.

      * Para o macOS e o Linux, **as Operações de Código Inline** passam a ser suportadas quando utilizar a extensão Azure Logic Apps (Preview) no Código do Estúdio Visual.

      * Já não precisa de reiniciar a sua aplicação lógica se fizer alterações numa ação **inline Code Operations.**

      * As ações **de Operações de Código Inline** [atualizaram os limites](logic-apps-overview-preview.md#inline-code-limits).

    * Alguns [gatilhos B2B incorporados e ações para contas de integração não](../connectors/apis-list.md#integration-account-connectors) estão disponíveis, por exemplo, as ações de codificação e descodamento do **Ficheiro Plano.**

    * A ação incorporada, [Azure Logic Apps - Escolha um fluxo](logic-apps-http-endpoint.md) de trabalho de aplicação lógica é agora **Workflow Operations - Invoque um fluxo de trabalho nesta aplicação de fluxo de trabalho**.

* **Disponibilidade do plano de hospedagem**: Quer crie um novo tipo de recurso **Logic App (Preview)** no portal Azure ou implantado a partir do Código do Estúdio Visual, só pode utilizar o plano de hospedagem Premium ou App Service em Azure. Os planos de hospedagem de consumo não estão disponíveis e não são suportados para a implementação deste tipo de recurso. Pode implantar do Código do Estúdio Visual para um recipiente Docker, mas não para um [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

* **Depuração de breakpoint no Código do Estúdio Visual**: Embora possa adicionar e utilizar pontos de rutura dentro doworkflow.js **no** ficheiro para um fluxo de trabalho, os breakpoints são suportados apenas para ações neste momento, e não para gatilhos. Para obter mais informações, consulte [Criar fluxos de trabalho apátridas e apátridas no Código do Estúdio Visual.](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)

* **Controlo de** zoom : O controlo de zoom não está disponível atualmente no designer.

* **Desencadear história e executar a história**: Para o tipo de recurso Logic App **(Preview),** desencadear a história e executar a história no portal Azure aparece ao nível do fluxo de trabalho, e não ao nível da aplicação lógica. Para encontrar estes dados históricos, siga estes passos:

   * Para ver o histórico de execução, abra o fluxo de trabalho na sua aplicação lógica. No menu de fluxo de trabalho, em **Programador,** selecione **Monitor**.

   * Para rever o histórico do gatilho, abra o fluxo de trabalho na sua aplicação lógica. No menu de fluxo de trabalho, em **Programador,** selecione **Trigger Histories**.

<a name="limits"></a>

## <a name="updated-limits"></a>Limites atualizados

Embora muitos limites para Azure Logic Apps Preview permaneçam os mesmos que os [limites para aplicações lógicas Azure multi-arrendantes](logic-apps-limits-and-config.md), estes limites mudaram para a Azure Logic Apps Preview.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Limites de tempo HTTP

Para uma única chamada de entrada ou saída, o prazo limite de tempo é de 230 segundos (3,9 minutos) para estes gatilhos e ações:

* Pedido de saída: http trigger, AÇÃO HTTP
* Pedido de entrada: Pedido de gatilho, detonador HTTP Webhook, ação HTTP Webhook

Em comparação, eis os limites de tempo para estes gatilhos e ações noutros ambientes onde as aplicações lógicas e os seus fluxos de trabalho funcionam:

* Aplicativos lógicos Azure Multi-inquilinos: 120 segundos (2 minutos)
* Ambiente de serviço de integração: 240 segundos (4 minutos)

Para mais informações, consulte [os limites HTTP](logic-apps-limits-and-config.md#http-limits).

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Conectores geridos

Os conectores geridos estão limitados a 50 pedidos por minuto por ligação. Para trabalhar com problemas de estrangulamento do conector, consulte [problemas de estrangulamento do Handle (429 - erro de "Demasiados pedidos") em Azure Logic Apps](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Operações de código inline (executar código JavaScript)

Para uma definição de aplicação lógica única, a ação Inline Code Operations, [**Execute o Código JavaScript,**](logic-apps-add-run-inline-code.md)tem estes limites atualizados:

* O número máximo de caracteres de código aumenta de 1.024 caracteres para 100.000 caracteres.

* A duração máxima do código de funcionamento aumenta de cinco segundos para 15 segundos.

Para obter mais informações, consulte [os limites de definição de aplicações logicos.](logic-apps-limits-and-config.md#definition-limits)

## <a name="next-steps"></a>Passos seguintes

* [Criar fluxos de trabalho apátridas e apátridas no portal Azure](create-stateful-stateless-workflows-azure-portal.md)
* [Criar fluxos de trabalho apátridas e apátridas no Código do Estúdio Visual](create-stateful-stateless-workflows-visual-studio-code.md)
* [Página de visualização pública de aplicativos de lógica conhecida em GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Além disso, gostaríamos de ouvir a sua parte sobre as suas experiências com a Azure Logic Apps Preview!

* Para bugs ou problemas, [crie os seus problemas no GitHub](https://github.com/Azure/logicapps/issues).
* Para perguntas, pedidos, comentários e outros comentários, [utilize este formulário de feedback](https://aka.ms/lafeedback).
