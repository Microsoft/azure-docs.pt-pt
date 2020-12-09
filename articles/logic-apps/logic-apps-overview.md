---
title: Visão geral para apps da Azure Logic
description: A azure Logic Apps é a solução cloud para construir e orquestrar fluxos de trabalho automatizados que integram apps, dados, serviços e sistemas com código mínimo para cenários de nível empresarial.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: overview
ms.custom: mvc
ms.date: 03/11/2020
ms.openlocfilehash: 4c4e8c8f809452286fee493e20dbef2cc538c8bd
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921535"
---
# <a name="what-is-azure-logic-apps"></a>O que é o Azure Logic Apps?

[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps) é um serviço de cloud que o ajuda a agendar, automatizar e orquestrar tarefas, processos de negócios e [fluxos](#logic-app-concepts) de trabalho quando precisa de integrar apps, dados, sistemas e serviços entre empresas ou organizações. A Logic Apps simplifica a forma como concebe e constrói soluções escaláveis para [integração](https://azure.microsoft.com/product-categories/integration/)de aplicações, integração de dados, integração de sistemas, integração de aplicações empresariais (EAI) e comunicação business-to-business (B2B), seja na nuvem, nas instalações, ou em ambas.

Por exemplo, seguem-se algumas cargas de trabalho que pode automatizar com as aplicações lógicas:

* Processar e encaminhar encomendas em sistemas no local e serviços cloud.

* Envie notificações de e-mail com o Office 365 quando ocorrem eventos em vários sistemas, aplicações e serviços.

* Mover ficheiros carregados de um servidor SFTP ou FTP para o Armazenamento do Azure.

* Monitorizar tweets sobre um assunto específico, analisar sentimentos e criar alertas ou tarefas para itens que precisam de revisão.

Para construir soluções de integração empresarial com apps Azure Logic, pode escolher entre uma galeria em crescimento com [centenas de conectores prontos a usar,](../connectors/apis-list.md)que incluem serviços como Azure Service Bus, Azure Functions, Azure Storage, SQL Server, Office 365, Dynamics, Salesforce, BizTalk, SAP, Oracle DB, ações de ficheiros, entre outras. Os [conectores](#logic-app-concepts) disponibilizam [acionadores](#logic-app-concepts), [ações](#logic-app-concepts) ou ambos para criar aplicações lógicas que acedem aos dados em segurança e os processam em tempo real.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Introducing-Azure-Logic-Apps/player]

## <a name="how-do-logic-apps-work"></a>Como funcionam as aplicações lógicas? 

Cada fluxo de trabalho de aplicação lógica começa com um acionador, que é acionado quando um evento específico ocorre ou quando novos dados disponíveis cumprem critérios específicos. Muitos gatilhos fornecidos pelos conectores em Aplicações Lógicas incluem capacidades básicas de agendamento para que possa configurar a regularidade com que as suas cargas de trabalho funcionam. Para agendamentos mais complexos ou recorrências avançadas, pode utilizar um gatilho de Recorrência como primeiro passo em qualquer fluxo de trabalho. Saiba mais sobre [fluxos de trabalho baseados em horários.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)

Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que executa as ações no fluxo de trabalho. Estas ações também podem incluir conversões de dados e controlos de fluxo de trabalho, tais como declarações condicionais, declarações de switch, loops e ramificação. Por exemplo, esta aplicação lógica começa com um acionador do Dynamics 365 com os critérios “Quando um registo é atualizado” incorporados. Se o acionador detetar um evento que corresponde a este critério, o acionador é acionado e executa as ações do fluxo de trabalho. Neste exemplo, estas ações incluem transformação XML, atualizações de dados, ramificação de decisões e notificações por e-mail.

![Estruturador de Aplicações Lógicas - aplicação lógica de exemplo](./media/logic-apps-overview/azure-logic-apps-designer.png)

Pode criar as aplicações lógicas visualmente com o Estruturador de Aplicações Lógicas, que se encontra disponível no portal do Azure através do browser e no Visual Studio. Para aplicações lógicas mais personalizadas, pode criar ou editar as definições das aplicações lógicas em JavaScript Object Notation (JSON), ao trabalhar no editor "vista de código". Também pode utilizar comandos do Azure PowerShell e modelos do Azure Resource Manager para tarefas selecionadas. O Logic apps é implementado e executado na cloud no Azure. Para uma introdução mais detalhada, veja este vídeo: [Utilização do Azure Enterprise Integration Services para executar aplicações cloud à escala](https://channel9.msdn.com/Events/Connect/2017/T119/)

## <a name="why-use-logic-apps"></a>Porquê usar aplicativos lógicos?

Com a passagem das empresas para o mundo digital, as aplicações lógicas ajudam-no a ligar sistemas legados e modernos e de vanguarda, de forma mais fácil e rapidamente, ao disponibilizar APIs pré-criadas como conectores geridos pela Microsoft. Dessa forma, poder concentrar-se na lógica de negócio e na funcionalidade das suas aplicações. Não tem de se preocupar com a criação, alojamento, dimensionamento, gestão, manutenção e monitorização das aplicações. O Logic Apps trata destas questões por si. Além disso, paga apenas o que utilizar, com base num [modelo de preços](../logic-apps/logic-apps-pricing.md) de consumo.

Em muitos casos, não terá de escrever código. No entanto, se tiver de escrever algum código, pode criar fragmentos de código com as [Funções do Azure](../azure-functions/functions-overview.md) e executar esse código a pedido, a partir das aplicações lógicas. Além disso, se as suas aplicações lógicas tiverem de interagir com eventos de serviços do Azure, aplicações personalizadas ou outras soluções, pode utilizar o [Azure Event Grid](../event-grid/overview.md) com as aplicações lógicas para monitorização, encaminhamento e publicação de eventos.

O Logic Apps, as Funções e o Event Grid são totalmente geridos pelo Microsoft Azure, libertando-o de preocupações relacionadas com a criação, alojamento, dimensionamento, gestão e monitorização das suas soluções. Com a capacidade de criar [aplicações e soluções “sem servidor”](../logic-apps/logic-apps-serverless-overview.md), pode concentrar-se apenas na lógica de negócio. Estes serviços dimensionam automaticamente para responder às suas necessidades, agilizam as integrações e ajudam-no a criar aplicações cloud robustas, com o mínimo de código.

Para saber como as empresas melhoraram a agilidade e aumentaram o foco nos negócios principais ao combinarem o Logic Apps com outros serviços do Azure e produtos da Microsoft, veja estas [histórias de clientes](https://aka.ms/logic-apps-customer-stories).

Seguem-se mais detalhes sobre as capacidades e vantagens que obtém com o Logic Apps:

### <a name="visually-build-workflows-with-easy-to-use-tools"></a>Criar fluxos de trabalho visualmente, com ferramentas de fácil utilização

Poupar tempo e simplificar processos complexos com ferramentas de design visual. Criar aplicações lógicas do início ao fim, utilizando o Estruturador de Aplicações Lógicas através do browser no portal do Azure ou no Visual Studio. Iniciar o fluxo de trabalho com um acionador e adicionar qualquer número de ações da [galeria de conectores](../connectors/apis-list.md).

### <a name="get-started-faster-with-logic-app-templates"></a>Começar a produzir mais depressa com os modelos de aplicações lógicas

Crie soluções frequentemente utilizadas mais rapidamente, escolhendo fluxos de trabalho predefinidos da [galeria de modelos](../logic-apps/logic-apps-create-logic-apps-from-templates.md). Os modelos vão desde a conectividade simples para aplicações software como um serviço (SaaS) até soluções avançadas de B2B, além de modelos “apenas por divertimento”. Saiba como [criar aplicações lógicas a partir de modelos pré-criados](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

### <a name="connect-disparate-systems-across-different-environments"></a>Ligar sistemas díspares entre ambientes diferentes

Alguns padrões e fluxos de trabalho são fáceis de descrever mas difíceis de implementar em código. As aplicações lógicas ajudam a ligar sistemas díspares no local e ambientes cloud de forma totalmente integrada. Por exemplo, pode ligar uma solução de marketing na cloud a um sistema de faturação no local ou centralizar o processamento de mensagens através de APIs e sistemas, com um Enterprise Service Bus. As aplicações lógicas proporcionam uma forma rápida, fiável e consistente de fornecer soluções reutilizáveis e reconfiguráveis nestes cenários.

### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>Suporte de primeira classe para integração empresarial e cenários B2B

As empresas e organizações comunicam eletronicamente entre si através da utilização de protocolos e formatos de mensagem norma da indústria mas que são diferentes, como o EDIFACT, AS2 e X12. Com as funcionalidades do [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md), pode criar aplicações lógicas que transformam os formatos de mensagem utilizados pelos seus parceiros em formatos que os sistemas da sua organização conseguem interpretar e processar. O Logic Apps processa estas trocas de forma fácil e segura, com a encriptação e assinaturas digitais.

Comece por algo pequeno com os seus sistemas e serviços atuais, e aumente progressivamente ao seu ritmo. Quando estiver pronto, o Logic Apps e o EIP ajudam-no a implementar e dimensionar para cenários de integração mais maduros, ao fornecer estas capacidades e muito mais:

* Criar estes produtos e serviços:

  * [Microsoft BizTalk Server](/biztalk/core/introducing-biztalk-server)
  * [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)
  * [Funções do Azure](../azure-functions/functions-overview.md)
  * [Gestão de API do Azure](../api-management/api-management-key-concepts.md)

* Processar [mensagens XML](../logic-apps/logic-apps-enterprise-integration-xml.md)

* Processar [ficheiros planos](../logic-apps/logic-apps-enterprise-integration-flatfile.md)

* Trocar mensagens com protocolos [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md), [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md) e [X12](../logic-apps/logic-apps-enterprise-integration-x12.md)

* Armazenar e gerir estes artefactos B2B e muito mais num local, com [contas de integração](./logic-apps-enterprise-integration-create-integration-account.md):

  * [Parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md)
  * [Contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md) 
  * [Mapas de transformação XML](../logic-apps/logic-apps-enterprise-integration-maps.md)
  * [Esquemas de validação XML](../logic-apps/logic-apps-enterprise-integration-schemas.md)

Por exemplo, se estiver a utilizar o Microsoft BizTalk Server, as aplicações lógicas podem comunicar com o BizTalk Server através do [conector do BizTalk Server](../connectors/apis-list.md#on-premises-connectors). Em seguida, pode estender ou realizar operações semelhantes ao BizTalk nas suas aplicações lógicas, incluindo [conectores de conta de integração](../connectors/apis-list.md#integration-account-connectors), que estão disponíveis com o Enterprise Integration Pack.

Por outro lado, o BizTalk Server pode ligar e comunicar com aplicações lógicas com o [Adaptador do BizTalk Server da Microsoft para o Logic Apps](https://www.microsoft.com/download/details.aspx?id=54287). Saiba como [configurar e utilizar o Adaptador do BizTalk Server](/biztalk/core/logic-app-adapter) no seu BizTalk Server.

### <a name="write-once-reuse-often"></a>Escreva uma vez, reutilize frequentemente

Crie as suas aplicações lógicas como modelos do Azure Resource Manager para que possa [automatizar a implementação de aplicações lógicas](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) em vários ambientes e regiões.

### <a name="access-resources-inside-azure-virtual-networks"></a>Aceder a recursos dentro das redes virtuais Azure

As aplicações lógicas podem aceder a recursos seguros, como máquinas virtuais (VMs) e outros sistemas ou serviços, que estão dentro de uma [rede virtual Azure](../virtual-network/virtual-networks-overview.md) quando cria um [ *ambiente de serviço de integração* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Um ISE é uma instância dedicada do serviço Logic Apps que utiliza recursos dedicados e funciona separadamente do serviço "global" multi-inquilino Logic Apps.

Executar aplicações lógicas em seu próprio caso dedicado separado ajuda a reduzir o impacto que outros inquilinos do Azure podem ter no desempenho das suas apps, também conhecido como o [efeito "vizinhos barulhentos".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Um ISE também fornece estes benefícios:

* Os seus próprios endereços IP estáticos, separados dos endereços IP estáticos que são partilhados pelas aplicações lógicas no serviço multi-inquilino. Também pode configurar um único endereço IP de saída público, estático e previsível para comunicar com os sistemas de destino. Desta forma, não é preciso configurar aberturas de firewall adicionais nesses sistemas de destino para cada ISE.

* Limites acrescidos na duração de execução, retenção de armazenamento, produção, pedidos HTTP e intervalos de resposta, tamanhos de mensagem e pedidos de conector personalizado. Para obter mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md).

Quando cria um ISE, o Azure *injeta* ou implementa esse ISE na sua rede virtual Azure. Em seguida, pode utilizar este ISE como localização para as aplicações lógicas e contas de integração que precisam de acesso. Para obter mais informações sobre a criação de um ISE, consulte [redes virtuais Connect to Azure a partir de Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

### <a name="built-in-extensibility"></a>Extensibilidade incorporada

Se não encontrar o conector que pretende executar código personalizado, pode alargar as aplicações lógicas criando e chamando os seus próprios códigos de corte a pedido através de [Funções Azure](../azure-functions/functions-overview.md). Crie as suas próprias [APIs](../logic-apps/logic-apps-create-api-app.md) e [conectores personalizados](../logic-apps/custom-connector-overview.md) aos quais pode ligar a partir das aplicações lógicas.

### <a name="pay-only-for-what-you-use"></a>Paga apenas o que utilizar
  
O Logic Apps utiliza [preços e medição](../logic-apps/logic-apps-pricing.md) baseados no consumo, a não ser que tenha aplicações lógicas criadas anteriormente com planos do Serviço de Aplicações.

Saiba mais sobre o Logic Apps com estes vídeos introdutórios:

* [Integração com o Logic Apps - expanda os seus conhecimentos](https://channel9.msdn.com/Events/Build/2017/C9R17)
* [Integração empresarial com o Microsoft Azure Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2188)
* [Criar processos empresariais avançados com o Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3179)

## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>Como é que as Aplicações Lógicas diferem das Funções, WebJobs e Power Automamate?

Todos os estes serviços ajudam a “colar” e ligar sistemas diferentes. Cada serviço tem vantagens e benefícios, pelo que combinar as respetivas capacidades é a melhor forma de criar rapidamente um sistema de integração dimensionável e completo. Para obter mais informações, consulte [Escolha entre Aplicações lógicas, funções, WebJobs e Power Automamate.](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>Termos-chave

* **Fluxo de trabalho**: visualize, estruture, crie, automatize e implemente os processos empresariais como uma série de passos.

* **Conectores geridos**: as suas aplicações lógicas precisam de aceder a dados, serviços e sistemas. Pode utilizar conectores pré-criados geridos pela Microsoft, que foram concebidos para criar, aceder e trabalhar com os seus dados. Consulte [conectores para aplicações lógicas Azure](../connectors/apis-list.md).

* **Acionadores**: vários conectores geridos pela Microsoft fornecem acionadores que são acionados quando eventos ou novos dados cumprem condições específicas. Por exemplo, um evento poderá estar a receber um e-mail ou a detetar alterações na sua conta de Armazenamento do Azure. Sempre que o acionador é acionado, o motor do Logic Apps cria uma nova instância da aplicação lógica que executa o fluxo de trabalho.

* **Ações**: as ações são todos os passos que ocorrem após o acionador. Normalmente, cada ação mapeia para uma operação que está definida por um conector gerido, uma API personalizada ou um conector personalizado.

* **Enterprise Integration Pack** - para cenários de integração mais avançados, o Logic Apps inclui capacidades do BizTalk Server. O Enterprise Integration Pack fornece conectores que ajudam as aplicações lógicas a efetuar facilmente a validação, transformação, etc.

## <a name="get-started"></a>Introdução

O Logic Apps é um dos muitos serviços alojados no Microsoft Azure. Por isso, antes de começar, precisa de uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

Se tiver uma subscrição do Azure, experimente este [início rápido para criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md), que monitoriza o novo conteúdo num site através de um feed RSS e envia um e-mail quando aparecer novo conteúdo.

## <a name="next-steps"></a>Passos seguintes

* [Verificar o tráfego com uma aplicação lógica baseada numa agenda](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
* Saiba mais sobre as [soluções sem servidor com o Azure](../logic-apps/logic-apps-serverless-overview.md)
* Saiba mais sobre a [integração B2B no Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
