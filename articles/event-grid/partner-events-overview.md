---
title: Grelha de Eventos Azure - Eventos de Parceiros
description: Envie eventos de parceiros de eventos de terceiros SaaS e PaaS diretamente para os serviços da Azure com a Azure Event Grid.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 31a5fe611871eb4734b6a68e3818592028ebc75c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506151"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Eventos de Parceiros em Azure Event Grid (pré-visualização)
A funcionalidade **Eventos Parceiros** permite a um fornecedor de SaaS de terceiros publicar eventos a partir dos seus serviços para que os consumidores possam subscrever esses eventos. Esta funcionalidade oferece uma experiência de primeira parte a fontes de eventos de terceiros expondo um tipo [de tópico,](concepts.md#topics) um **tópico de parceiro**. Os subscritores criam subscrições deste tema para consumir eventos. Também fornece um modelo de pub-sub limpo, separando preocupações e propriedade de recursos que são usados por editores de eventos e assinantes.

> [!NOTE]
> Se você é novo na utilização de Event Grid, consulte [a visão geral,](overview.md) [conceitos](concepts.md)e [manipuladores de eventos.](event-handlers.md)

## <a name="what-is-partner-events-to-a-publisher"></a>O que é Partner Events para uma editora?
Para uma editora de eventos, a funcionalidade Partner Events permite que os editores façam as seguintes tarefas:

- A bordo das suas fontes de eventos para a Grade de Eventos
- Criar um espaço de nome (ponto final) ao qual podem publicar eventos
- Criar tópicos parceiros no Azure que os assinantes possuem e usam para consumir eventos

## <a name="what-is-partner-events-to-a-subscriber"></a>O que é Partner Events para um assinante?
Para um assinante, a funcionalidade Eventos parceiros permite-lhes criar tópicos parceiros no Azure para consumir eventos de fontes de eventos de terceiros. O consumo de eventos é realizado através da criação de subscrições de eventos que enviam (empurre) eventos para o manipulador de eventos de um assinante.

## <a name="why-should-i-use-partner-events"></a>Por que devo usar eventos de parceiros?
Pode querer utilizar os Eventos parceiros se tiver um ou mais dos seguintes requisitos.

### <a name="for-publishers"></a>Para editores

- Quer um mecanismo para disponibilizar os seus eventos no Azure. Os seus utilizadores podem filtrar e encaminhar esses eventos utilizando tópicos de parceiros e subscrições de eventos que possuem e gerem. Pode utilizar outras abordagens de integração, tais como [tópicos](custom-topics.md) e [domínios.](event-domains.md) Mas não permitiriam uma separação limpa da propriedade, gestão e faturação de recursos (tópicos parceiros) entre editores e assinantes. Além disso, esta abordagem proporciona uma experiência mais intuitiva do utilizador que facilita a descoberta e utilização de tópicos de parceiros.
- Quer publicar eventos num único ponto final, o ponto final do espaço de nome. E, você quer a capacidade de filtrar esses eventos para que apenas um subconjunto deles esteja disponível. 
- Quer ter visibilidade em métricas relacionadas com eventos publicados.
- Você quer usar o esquema [cloud events 1.0](https://cloudevents.io/) para os seus eventos.

### <a name="for-subscribers"></a>Para assinantes

- Você deseja subscrever eventos de editores de terceiros e lidar com os [eventos](#available-third-party-event-publishers) usando os manipuladores de eventos que estão em Azure ou em qualquer outro lugar.
- Você deseja aproveitar o rico conjunto de funcionalidades de encaminhamento e [destinos/manipuladores de eventos](overview.md#event-handlers) para processar eventos de fontes de terceiros. 
- Pretende implementar arquiteturas pouco acopladas onde o seu assinante/manipulador de eventos desconhece a existência do corretor de mensagens utilizado. 
- Você precisa de um mecanismo de entrega de impulso resiliente com suporte de recandidologia e pelo menos uma vez semântica.
- Você quer usar o esquema [cloud events 1.0](https://cloudevents.io/) para os seus eventos. 


## <a name="available-third-party-event-publishers"></a>Editores de eventos de terceiros disponíveis
Um editor de eventos de terceiros deve passar por um [processo de embarque](partner-onboarding-overview.md) antes que um assinante possa começar a consumir os seus eventos. 

Se você é um assinante e gostaria que um serviço de terceiros para expor os seus eventos através da Grade de Eventos, 

### <a name="auth0"></a>Auth0
**AUth0** é a primeira editora parceira disponível. Pode criar um [tópico de parceiro Auth0](auth0-overview.md) para ligar as suas contas Auth0 e Azure. Esta integração permite-lhe reagir, registar e monitorizar eventos Auth0 em tempo real. Para experimentá-lo, consulte [Integrar a grelha de eventos do Azure com Auto0](auth0-how-to.md)

Se pretender que um serviço de terceiros exponha os seus eventos através da Grade de Eventos, submeta a ideia no [portal User Voice](https://feedback.azure.com/forums/909934-azure-event-grid).
 
## <a name="resources-managed-by-event-publishers"></a>Recursos geridos por editores de eventos
Os editores de eventos criam e gerem os seguintes recursos:

### <a name="partner-registration"></a>Inscrição de parceiros
O registo contém informações gerais relacionadas com uma editora. Define um tipo de tópico de parceiro que mostra no portal Azure como uma opção quando os utilizadores tentam criar um tópico de parceiro. Um editor pode expor mais de um ou mais tipos de tópicos parceiros para se adaptar às necessidades dos seus subscritores. Ou seja, um editor pode criar registos separados (tipos de tópicos parceiros) para eventos de diferentes serviços. Por exemplo, para o serviço de recursos humanos (RH), o editor pode definir um tópico parceiro para eventos como a associação de colaboradores, colaboradores promovidos e funcionários deixaram a empresa. 

Tenha em consideração os seguintes pontos:

- Apenas são visíveis registos de parceiros aprovados pela Azure. 
- As inscrições são globais. Ou seja, não estão associados a uma região particular de Azure.
- Um registo é um recurso opcional. Mas recomendamos que (como editora) crie um registo. Permite que os utilizadores descubram os seus tópicos na página **'Criar Tópico de Parceiro'** no [portal Azure.](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic) Em seguida, o utilizador pode selecionar tipos de eventos (por exemplo, funcionários unidos, empregado à esquerda, e assim por diante.) enquanto cria subscrições de eventos.

### <a name="namespace"></a>Espaço de Nomes
Como tópicos e [domínios personalizados,](event-domains.md)um espaço de nome de parceiros é um ponto final regional para publicar eventos. [custom topics](custom-topics.md) É através de espaços de nome que as editoras criam e gerem canais de eventos. Um espaço de nome também funciona como o recurso de recipiente para canais de eventos.

### <a name="event-channels"></a>Canais de Eventos
Um canal de eventos é um recurso espelhado para um tópico parceiro. Quando uma editora cria um canal de eventos na subscrição Azure da editora, também cria um tópico parceiro sob a subscrição do Azure de um assinante. As operações es feitas contra um canal de eventos (exceto GET) serão aplicadas ao tópico parceiro de subscrição correspondente, mesmo eliminação. No entanto, apenas os tópicos parceiros são o tipo de recursos em que as subscrições e a entrega de eventos podem ser configuradas.

## <a name="resources-managed-by-subscribers"></a>Recursos geridos por assinantes 
Os assinantes podem usar tópicos parceiros definidos por uma editora e é o único tipo de recurso que vêem e gerem. Uma vez criado um tópico de parceiro, um utilizador assinante pode criar subscrições de eventos que definam regras de filtro para [destinos/manipuladores de eventos](overview.md#event-handlers). Para os subscritores, um tópico parceiro e as subscrições de eventos associados fornecem as mesmas capacidades ricas que [os tópicos personalizados](custom-topics.md) e as suas subscrições relacionadas fazem com uma diferença notável: os tópicos parceiros suportam apenas o [esquema Cloud Events 1.0,](cloudevents-schema.md)que fornece um conjunto de capacidades mais rico do que outros esquemas suportados.

A imagem a seguir mostra o fluxo das operações do avião de controlo.

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="Partner Events - controlar o fluxo do avião":::

1. A editora cria um **registo de parceiros.** Os registos de parceiros são globais. Ou seja, não estão associados a uma região particular de Azure. Este passo é opcional.
1. A editora cria um **espaço de nomes de parceiros** numa região específica.
1. Quando o Assinante 1 tenta criar um tópico de parceiro, um canal de **eventos** , Event Channel 1, é criado primeiro na subscrição Azure da editora.
1. Em seguida, um **tópico parceiro** , Partner Topic 1, é criado na subscrição do Azure do assinante. O assinante precisa de ativar o tópico do parceiro. 
1. O Assinante 1 cria uma **subscrição de Azure Logic Apps** para Partner Topic 1.
1. O Assinante 1 cria uma **subscrição de Armazenamento Azure Blob** para Partner Topic 1. 
1. Quando o Assinante 2 tenta criar um tópico de parceiro, outro canal de **eventos** , Event Channel 2, é criado primeiro na subscrição Azure da editora. 
1. Em seguida, o **tópico parceiro** , Partner Topic 2, é criado na subscrição Azure do segundo subscritor. O assinante precisa de ativar o tópico do parceiro. 
1. O Assinante 2 cria uma **subscrição de Funções Azure** para O Tópico 2 do Parceiro. 

## <a name="pricing"></a>Preços
Os tópicos do parceiro são cobrados pelo número de operações efetuadas ao utilizar a Grade de Eventos. Para obter mais informações sobre todos os tipos de operações que são usadas como base para faturação e informações detalhadas sobre preços, consulte [os preços da Grade de Eventos](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="limits"></a>Limites
Consulte [os limites do Serviço de Grelha de Eventos](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) para obter informações detalhadas sobre os limites em vigor para os tópicos dos parceiros.


## <a name="next-steps"></a>Passos seguintes

- [Tópico parceiro Auth0](auth0-overview.md)
- [Como usar o tópico parceiro Auth0](auth0-how-to.md)
- [Torne-se um parceiro de Grade de Eventos](partner-onboarding-overview.md)