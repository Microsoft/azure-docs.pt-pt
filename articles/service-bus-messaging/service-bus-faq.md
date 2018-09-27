---
title: O Azure Service Bus perguntas mais frequentes (FAQ) | Documentos da Microsoft
description: Responde a algumas perguntas mais frequentes sobre o Azure Service Bus.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: 312d92994e4932e854ec77681da2d4dbd14da2aa
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392006"
---
# <a name="service-bus-faq"></a>Perguntas Frequentes do Service Bus

Este artigo aborda algumas perguntas mais frequentes sobre o Microsoft Azure Service Bus. Também pode visitar o [FAQs do suporte do Azure](https://azure.microsoft.com/en-us/support/faq/) para gerais informações de preços e de suporte do Azure.

## <a name="general-questions-about-azure-service-bus"></a>Perguntas gerais sobre o Azure Service Bus
### <a name="what-is-azure-service-bus"></a>O que é o Azure Service Bus?
[O Azure Service Bus](service-bus-messaging-overview.md) é uma plataforma de cloud de mensagens assíncronas que lhe permite enviar dados entre sistemas desacoplados. A Microsoft oferece esse recurso como um serviço, o que significa que não é necessário alojar algum do seu próprio hardware para usá-lo.

### <a name="what-is-a-service-bus-namespace"></a>O que é um espaço de nomes do Service Bus?
R [espaço de nomes](service-bus-create-namespace-portal.md) fornece um contentor de âmbito para abordar os recursos do Service Bus na sua aplicação. Criar um espaço de nomes, é necessário utilizar o Service Bus e é uma das primeiras etapas na introdução.

### <a name="what-is-an-azure-service-bus-queue"></a>O que é uma fila do Service bus do Azure?
R [fila do Service Bus](service-bus-queues-topics-subscriptions.md) é uma entidade na qual as mensagens são armazenadas. As filas são úteis quando tiver várias aplicações ou várias partes de uma aplicação distribuída que precisam de comunicar entre si. A fila é semelhante a um centro de distribuição em que vários produtos (mensagens) sejam recebidos e enviados, em seguida, a partir dessa localização.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Quais são os tópicos de Service bus do Azure e as subscrições?
Um tópico pode ser visualizado como uma fila e ao utilizar várias subscrições, torna-se um modelo de mensagens mais avançado; essencialmente, uma ferramenta de comunicação de um-para-muitos. Esse modelo de publicação/subscrição (ou *pub/sub*) permite que uma aplicação que envia uma mensagem para um tópico com várias subscrições ter essa mensagem recebida por vários aplicativos.

### <a name="what-is-a-partitioned-entity"></a>O que é uma entidade com partições?
Uma fila convencional ou um tópico é manipulado por um mediador de mensagem única e armazenado num arquivo de mensagens. Suportado apenas nas Basic e Standard mensagens camadas, uma [particionada fila ou tópico](service-bus-partitioning.md) é manipulado por vários mediadores de mensagens e armazenados em vários arquivos de mensagens. Esta funcionalidade significa que o débito total de uma fila particionada ou um tópico já não está limitado pelo desempenho de um mediador de mensagem única ou arquivo de mensagens. Além disso, uma indisponibilidade temporária de um arquivo de mensagens não processa uma particionada fila ou tópico indisponível.

Não é garantida a ordenação quando utilizar entidades particionadas. No caso de uma partição não estiver disponível, ainda pode enviar e receber mensagens de outras partições.

 Entidades particionadas já não são suportadas no [Premium SKU](service-bus-premium-messaging.md). 

## <a name="best-practices"></a>Melhores práticas
### <a name="what-are-some-azure-service-bus-best-practices"></a>Quais são algumas práticas recomendadas do Azure Service Bus?
Ver [melhores práticas para melhoramentos do desempenho através do Service Bus] [ Best practices for performance improvements using Service Bus] – este artigo descreve como otimizar o desempenho quando troca de mensagens.

### <a name="what-should-i-know-before-creating-entities"></a>O que deve saber antes de criar entidades?
As seguintes propriedades de uma fila e tópico são imutáveis. Considere esta limitação quando Aprovisiona suas entidades, como estas propriedades não podem ser modificadas sem criar uma nova entidade de substituição.

* Criação de partições
* Sessões
* Deteção de duplicados
* Express entidade

## <a name="pricing"></a>Preços
Esta secção responde a algumas perguntas mais frequentes sobre a estrutura de preços do Service Bus.

O [do Service Bus preços e faturação](service-bus-pricing-billing.md) artigo explica os medidores de faturação no Service Bus. Para obter informações específicas sobre as opções de preços do Service Bus, consulte [os detalhes dos preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Também pode visitar o [FAQ do suporte do Azure](https://azure.microsoft.com/en-us/support/faq/) para o Azure geral informações sobre preços. 

### <a name="how-do-you-charge-for-service-bus"></a>Como é cobrado do Service Bus?
Para obter informações completas sobre os preços do Service Bus, consulte [os detalhes dos preços do Service Bus][Pricing overview]. Além dos preços indicados, é cobrado associados às transferências de dados de saída fora do Centro de dados na qual seu aplicativo é aprovisionado.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>O uso do Service Bus está sujeito a transferência de dados? O que não é?
Qualquer transferência de dados dentro de uma determinada região do Azure é fornecida sem custos, assim como qualquer transferência de dados de entrada. Transferência de dados fora de uma região está sujeito a custos de saída, que podem ser encontrados [aqui](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Do Service Bus cobra armazenamento?
Não, o Service Bus não cobra para armazenamento. No entanto, existe uma quota de limitar a quantidade máxima de dados que podem ser mantidos por fila/tópico. Consulte as FAQ seguintes.

## <a name="quotas"></a>Quotas

Para obter uma lista de quotas e limites do Service Bus, consulte a [descrição geral de quotas do Service Bus][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>O Service Bus tem quaisquer quotas de utilização?
Por predefinição, para qualquer cloud service Microsoft define uma quota de utilização mensal agregado que é calculada em todas as subscrições de um cliente. Se precisar de mais do que estes limites, pode contactar o suporte ao cliente em qualquer altura para avaliar as suas necessidades e ajustar estes limites em conformidade. Para o Service Bus, a quota de utilização de agregação é de 5 mil milhões de mensagens por mês.

Embora a Microsoft se reserva o direito de desativar uma conta de cliente que foi excedido o respetivas quotas de utilização num determinado mês, as notificações de email são enviadas e várias tentativas são feitas em contato com um cliente antes de efetuar qualquer ação. Os clientes que exceda destas quotas são ainda responsáveis pelos custos que excederem as quotas.

Tal como acontece com outros serviços no Azure, do Service Bus impõe um conjunto de quotas específicos para garantir que existe uma utilização razoável de recursos. Pode encontrar mais detalhes sobre estas quotas no [descrição geral de quotas do Service Bus][Quotas overview].

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelo APIs de barramento de serviço do Azure e respetivas ações sugeridas?
Para obter uma lista de possíveis exceções do Service Bus, consulte [descrição geral de exceções][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma assinatura de acesso partilhado e que idiomas suportam gerar uma assinatura?
Assinaturas de acesso partilhado são um mecanismo de autenticação com base no SHA-256 hashes seguros ou URIs. Para obter informações sobre como gerar seus próprios assinaturas no nó, PHP, Java e C\#, consulte a [assinaturas de acesso partilhado] [ Shared Access Signatures] artigo.

## <a name="subscription-and-namespace-management"></a>Gestão de subscrição e o espaço de nomes
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um espaço de nomes para outra subscrição do Azure?

Pode mover um espaço de nomes de uma subscrição do Azure para outra, através da [portal do Azure](https://portal.azure.com) ou comandos do PowerShell. Para executar a operação, o espaço de nomes já deve estar ativo. Utilizador que executa os comandos tem de ser um administrador em subscrições de origem e de destino.

#### <a name="portal"></a>Portal

Para utilizar o portal do Azure para migrar os espaços de nomes do Service Bus para outra subscrição, siga as indicações [aqui](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

A seguinte sequência de comandos do PowerShell move um espaço de nomes de uma subscrição do Azure para outra. Para executar esta operação, o espaço de nomes já deve estar ativo e o utilizador que executa os comandos do PowerShell tem de ser um administrador em subscrições de origem e de destino.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o Service Bus, consulte os artigos seguintes:

* [Introdução ao Azure Service Bus Premium (mensagem de blogue)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução ao Azure Premium do Service Bus) (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Descrição geral do Service Bus](service-bus-messaging-overview.md)
* [Descrição geral da arquitetura do Service Bus do Azure](service-bus-fundamentals-hybrid-solutions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
