---
title: O Azure Service Bus fazia frequentemente perguntas (FAQ) / Microsoft Docs
description: Este artigo fornece respostas a algumas das perguntas frequentes (FAQ) sobre o Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3cd4e69481fb452391e6dc027cb41fd6dae71b7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76760254"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus - Perguntas frequentes (FAQ)

Este artigo discute algumas perguntas frequentes sobre o Microsoft Azure Service Bus. Também pode visitar as [FAQs](https://azure.microsoft.com/support/faq/) de Suporte Azure para obter preços e informações de suporte gerais do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Perguntas gerais sobre o ônibus de serviço azure
### <a name="what-is-azure-service-bus"></a>O que é o Azure Service Bus?
[O Azure Service Bus](service-bus-messaging-overview.md) é uma plataforma de nuvem de mensagens assíncrona que lhe permite enviar dados entre sistemas dissociados. A Microsoft oferece esta funcionalidade como um serviço, o que significa que não precisa de hospedar o seu próprio hardware para usá-lo.

### <a name="what-is-a-service-bus-namespace"></a>O que é um espaço de nome de ônibus de serviço?
Um [espaço de nome](service-bus-create-namespace-portal.md) fornece um recipiente de deteção para endereçar recursos de ônibus de serviço dentro da sua aplicação. Criar um espaço de nome é necessário para usar o Service Bus e é um dos primeiros passos para começar.

### <a name="what-is-an-azure-service-bus-queue"></a>O que é uma fila azure service bus?
Uma [fila de ônibus de serviço](service-bus-queues-topics-subscriptions.md) é uma entidade na qual as mensagens são armazenadas. As filas são úteis quando se tem múltiplas aplicações, ou várias partes de uma aplicação distribuída que precisam de comunicar entre si. A fila é semelhante a um centro de distribuição na medida em que vários produtos (mensagens) são recebidos e depois enviados a partir desse local.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>O que são tópicos e subscrições do Azure Service Bus?
Um tópico pode ser visualizado como uma fila e quando se utiliza várias subscrições, torna-se um modelo de mensagens mais rico; essencialmente uma ferramenta de comunicação de um a muitos. Este modelo de publicação/subscrição (ou *pub/sub)* permite que uma aplicação que envia uma mensagem para um tópico com múltiplas subscrições tenha essa mensagem recebida por múltiplas aplicações.

### <a name="what-is-a-partitioned-entity"></a>O que é uma entidade dividida?
Uma fila ou tópico convencional é tratado por um único corretor de mensagens e armazenado numa loja de mensagens. Suportado apenas nos níveis de mensagens Basic e Standard, uma [fila ou tópico dividido](service-bus-partitioning.md) é tratado por vários corretores de mensagens e armazenado em várias lojas de mensagens. Esta funcionalidade significa que a produção global de uma fila ou tópico dividido já não é limitada pelo desempenho de um único corretor de mensagens ou loja de mensagens. Além disso, uma interrupção temporária de uma loja de mensagens não torna indisponível uma fila divisória ou tópico.

A encomenda não é assegurada quando se utilizam entidades divididas. Caso uma partição não esteja disponível, ainda pode enviar e receber mensagens das outras divisórias.

 As entidades divididas deixaram de ser apoiadas no [SKU Premium.](service-bus-premium-messaging.md) 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Que portas preciso abrir na firewall? 
Pode utilizar os seguintes protocolos com o Azure Service Bus para enviar e receber mensagens:

- Avançadas Message Queuing Protocol (AMQP)
- Protocolo de Mensagens de Autocarro de Serviço (SBMP)
- HTTP

Consulte a tabela seguinte para as portas de saída que precisa de abrir para utilizar estes protocolos para comunicar com o Azure Event Hubs. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte [o guia de protocolo amqp](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 a 9354 | Ver [modo conectividade](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Que endereços IP preciso para whitelist?
Para encontrar os endereços IP certos para a lista branca para as suas ligações, siga estes passos:

1. Executar o seguinte comando a partir de um pedido de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Note o endereço IP `Non-authoritative answer`devolvido em . Este endereço IP é estático. O único ponto no tempo que mudaria é se restaurar o espaço de nome num cluster diferente.

Se utilizar a redundância da zona para o seu espaço de nome, precisa de fazer alguns passos adicionais: 

1. Primeiro, verificas o espaço do nome.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Note o nome na secção de resposta não **autorizada,** que se encontra num dos seguintes formatos: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Executar nslookup para cada um com sufixos sufixos s1, s2 e s3 para obter os endereços IP dos três casos em execução em três zonas de disponibilidade, 


## <a name="best-practices"></a>Melhores práticas
### <a name="what-are-some-azure-service-bus-best-practices"></a>Quais são as melhores práticas do Azure Service Bus?
Consulte [as melhores práticas para melhorias][Best practices for performance improvements using Service Bus] de desempenho utilizando o Service Bus – este artigo descreve como otimizar o desempenho ao trocar mensagens.

### <a name="what-should-i-know-before-creating-entities"></a>O que devo saber antes de criar entidades?
As seguintes propriedades de uma fila e tópico são imutáveis. Considere esta limitação ao fornecer as suas entidades, uma vez que estas propriedades não podem ser modificadas sem criar uma nova entidade de substituição.

* Criação de partições
* Sessões
* Deteção de duplicados
* Entidade expressa

## <a name="pricing"></a>Preços
Esta secção responde a algumas perguntas frequentes sobre a estrutura de preços dos autocarros de serviço.

O artigo de [preços e faturação](https://azure.microsoft.com/pricing/details/service-bus/) do Autocarro de Serviço explica os contadores de faturação no Bus de Serviço. Para obter informações específicas sobre as opções de preços do Ônibus de serviço, consulte os detalhes dos [preços do Autocarro de Serviço.](https://azure.microsoft.com/pricing/details/service-bus/)

Também pode visitar as [FAQs](https://azure.microsoft.com/support/faq/) de Suporte Azure para obter informações gerais sobre preços do Azure. 

### <a name="how-do-you-charge-for-service-bus"></a>Como se cobra o Autocarro de Serviço?
Para obter informações completas sobre os preços dos ônibus de serviço, consulte os detalhes dos [preços do Ônibus de serviço.][Pricing overview] Além dos preços notados, é cobrado por transferências de dados associadas para saída do centro de dados em que a sua aplicação é aprovisionada.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Que utilização do Service Bus está sujeito à transferência de dados? O que não é?
Qualquer transferência de dados dentro de uma dada região do Azure é fornecida gratuitamente, bem como qualquer transferência de dados de entrada. A transferência de dados para fora de uma região está sujeita a encargos de saída, que podem ser consultados [aqui.](https://azure.microsoft.com/pricing/details/bandwidth/)

### <a name="does-service-bus-charge-for-storage"></a>O Ônibus de serviço cobra para armazenamento?
Não, o Ônibus de Serviço não cobra pelo armazenamento. No entanto, existe uma quota que limita a quantidade máxima de dados que pode ser percecionada por fila/tópico. Veja as próximas FAQ.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Tenho um espaço de nome padrão de autocarro de serviço. Por que vejo acusações no grupo de recursos "$system"?
A Azure Service Bus atualizou recentemente os componentes de faturação. Devido a isso, se tiver um espaço de nome padrão de ônibus de serviço, poderá ver itens de linha para o recurso '/subscrições/<azure_subscription_id>/recursosGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system' no grupo de recursos '$system'.

Estes encargos representam a taxa base por subscrição azure que forprovisionou um espaço de nome Service Bus Standard. 

É importante notar que não se trata de novas taxas, ou seja, também existentes no modelo de faturação anterior. A única mudança é que estão agora listados no âmbito do "$system". Isto é feito devido a contraordenações no novo sistema de faturação que agrupa taxas de nível de subscrição, não ligadas a um recurso específico, no âmbito do id de recurso "$system".

## <a name="quotas"></a>Quotas

Para obter uma lista dos limites e quotas dos ônibus de serviço, consulte a visão geral das quotas de [ônibus de serviço.][Quotas overview]

### <a name="does-service-bus-have-any-usage-quotas"></a>O Ônibus de Serviço tem alguma quota de utilização?
Por padrão, para qualquer serviço na nuvem a Microsoft define uma quota de utilização mensal agregada que é calculada em todas as subscrições de um cliente. Se precisar de mais do que estes limites, pode contactar o atendimento ao cliente a qualquer momento para compreender as suas necessidades e ajustar adequadamente estes limites. Para o Service Bus, a quota de utilização agregada é de 5 mil milhões de mensagens por mês.

Embora a Microsoft se reservessem do direito de desativar uma conta de cliente que tenha excedido as suas quotas de utilização num determinado mês, as notificações de e-mail são enviadas e são feitas múltiplas tentativas para contactar um cliente antes de tomar qualquer ação. Os clientes que excedem estas quotas continuam a ser responsáveis por encargos que excedem as quotas.

Tal como acontece com outros serviços no Azure, a Service Bus impõe um conjunto de quotas específicas para garantir uma utilização justa dos recursos. Pode encontrar mais detalhes sobre estas quotas na visão geral das quotas de [ônibus de serviço.][Quotas overview]

### <a name="how-to-handle-messages-of-size--1-mb"></a>Como lidar com mensagens de tamanho > 1 MB?
Os serviços de mensagens de ônibus de serviço (filas e tópicos/subscrições) permitem que a aplicação envie mensagens de tamanho até 256 KB (nível padrão) ou 1 MB (nível premium). Se estiver a lidar com mensagens de tamanho superiora a 1 MB, utilize o padrão de verificação de reclamações descrito [nesta publicação de blog](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não sou capaz de criar um espaço de nome depois de aapagar de outra subscrição? 
Quando eliminar um espaço de nome de uma subscrição, aguarde 4 horas antes de o recriar com o mesmo nome noutra subscrição. Caso contrário, poderá receber a `Namespace already exists`seguinte mensagem de erro: . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelas APIs do Azure Service Bus e as suas ações sugeridas?
Para obter uma lista de possíveis exceções ao Service Bus, consulte a visão geral das [exceções.][Exceptions overview]

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma Assinatura de Acesso Partilhado e quais os idiomas suportam gerar uma assinatura?
As assinaturas de acesso partilhado são um mecanismo de autenticação baseado em hashes ou URIs seguros SHA-256. Para obter informações sobre como gerar as suas próprias assinaturas em Node.js, PHP, Java, Python e C#, consulte o artigo Assinaturas de [Acesso Partilhado.][Shared Access Signatures]

## <a name="subscription-and-namespace-management"></a>Gestão de subscrição e espaço de nome
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como migrar um espaço de nome para outra subscrição do Azure?

Pode mover um espaço de nome de uma subscrição Azure para outra, utilizando o [portal Azure](https://portal.azure.com) ou os comandos PowerShell. Para executar a operação, o espaço de nomejá deve estar ativo. O utilizador que executa os comandos deve ser um administrador tanto na fonte como nas subscrições-alvo.

#### <a name="portal"></a>Portal

Para utilizar o portal Azure para migrar espaços de nome sinuosos de ônibus de serviço para outra subscrição, siga as instruções [aqui](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

A sequência seguinte dos comandos PowerShell move um espaço de nome de uma subscrição Azure para outra. Para executar esta operação, o espaço de nome já deve estar ativo e o utilizador que executa os comandos PowerShell deve ser um administrador tanto na fonte como nas subscrições-alvo.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o Service Bus, consulte os seguintes artigos:

* [Introdução do Azure Service Bus Premium (post de blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução do Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do ônibus de serviço](service-bus-messaging-overview.md)
* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
