---
title: O Azure Service Bus perguntas mais frequentes (FAQ) | Documentos da Microsoft
description: Responde a algumas perguntas mais frequentes sobre o Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 26609e7b21af8804a4b43039c84c04597035721c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706216"
---
# <a name="service-bus-faq"></a>Perguntas Frequentes do Service Bus

Este artigo aborda algumas perguntas mais frequentes sobre o Microsoft Azure Service Bus. Também pode visitar o [FAQs do suporte do Azure](https://azure.microsoft.com/support/faq/) para gerais informações de preços e de suporte do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Perguntas gerais sobre o Azure Service Bus
### <a name="what-is-azure-service-bus"></a>O que é o Azure Service Bus?
[O Azure Service Bus](service-bus-messaging-overview.md) é uma plataforma de cloud de mensagens assíncronas que lhe permite enviar dados entre sistemas desacoplados. A Microsoft oferece esse recurso como um serviço, o que significa que não é necessário alojar o seu próprio hardware usá-lo.

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

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Que portas preciso abrir na firewall? 
Pode utilizar os seguintes protocolos com o Azure Service Bus para enviar e receber mensagens:

- Avançadas Message Queuing Protocol (AMQP)
- (SBMP) do protocolo de mensagens do Service Bus
- HTTP

Veja a seguinte tabela para as portas de saída que é necessário abrir para utilizar estes protocolos para comunicar com os Hubs de eventos do Azure. 

| Protocol | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte [Guia do protocolo AMQP](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 a 9354 | Consulte [modo de conectividade](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Que endereços IP é necessário à lista de permissões?
Para localizar os endereços IP corretos para a lista de permissões para as suas ligações, siga estes passos:

1. Execute o seguinte comando numa linha de comandos: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anote o endereço IP devolvido na `Non-authoritative answer`. Este endereço IP é estático. O único ponto no tempo que ele seria alterado é se restaurar o espaço de nomes para um cluster diferente.

Se utilizar a redundância de zona para o espaço de nomes, precisa fazer algumas etapas adicionais: 

1. Em primeiro lugar, execute nslookup no espaço de nomes.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anote o nome na **resposta não autoritativa** seção, que se encontra dos seguintes formatos: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Execute nslookup para cada um com sufixos s1, s2 e s3 para obter os endereços IP de todos os três instâncias em execução em três zonas de disponibilidade, 


## <a name="best-practices"></a>Melhores práticas
### <a name="what-are-some-azure-service-bus-best-practices"></a>Quais são algumas práticas recomendadas do Azure Service Bus?
Ver [melhores práticas para melhoramentos do desempenho através do Service Bus][Best practices for performance improvements using Service Bus] – este artigo descreve como otimizar o desempenho quando troca de mensagens.

### <a name="what-should-i-know-before-creating-entities"></a>O que deve saber antes de criar entidades?
As seguintes propriedades de uma fila e tópico são imutáveis. Considere esta limitação quando Aprovisiona suas entidades, como estas propriedades não podem ser modificadas sem criar uma nova entidade de substituição.

* Criação de partições
* Sessões
* Deteção de duplicados
* Express entidade

## <a name="pricing"></a>Preços
Esta secção responde a algumas perguntas mais frequentes sobre a estrutura de preços do Service Bus.

O [do Service Bus preços e faturação](https://azure.microsoft.com/pricing/details/service-bus/) artigo explica os medidores de faturação no Service Bus. Para obter informações específicas sobre as opções de preços do Service Bus, consulte [os detalhes dos preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Também pode visitar o [FAQ do suporte do Azure](https://azure.microsoft.com/support/faq/) para o Azure geral informações sobre preços. 

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

### <a name="how-to-handle-messages-of-size--1-mb"></a>Como lidar com mensagens de tamanho > 1 MB?
Serviços de mensagens do Service Bus (filas e tópicos/subscrições) permitem que a aplicação para enviar mensagens de tamanho de até 256 KB (escalão standard) ou 1 MB (o escalão premium). Se estiver lidando com mensagens de tamanho superior a 1 MB, usar o padrão de verificação de afirmação descrito em [nesta mensagem de blogue](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que motivo não consigo criar um espaço de nomes depois excluí-lo a partir de outra subscrição? 
Quando elimina um espaço de nomes de uma subscrição, aguarde até quatro horas antes de recriá-lo com o mesmo nome noutra subscrição. Caso contrário, poderá receber a seguinte mensagem de erro: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelo APIs de barramento de serviço do Azure e respetivas ações sugeridas?
Para obter uma lista de possíveis exceções do Service Bus, consulte [descrição geral de exceções][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma assinatura de acesso partilhado e que idiomas suportam gerar uma assinatura?
Assinaturas de acesso partilhado são um mecanismo de autenticação com base nos hashes de seguros de SHA-256 ou URIs. Para obter informações sobre como gerar seus próprios assinaturas no node. js, PHP, Java e C\#, consulte a [assinaturas de acesso partilhado][Shared Access Signatures] artigo.

## <a name="subscription-and-namespace-management"></a>Gestão de subscrição e o espaço de nomes
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um espaço de nomes para outra subscrição do Azure?

Pode mover um espaço de nomes de uma subscrição do Azure para outra, através da [portal do Azure](https://portal.azure.com) ou comandos do PowerShell. Para executar a operação, o espaço de nomes já deve estar ativo. Utilizador que executa os comandos tem de ser um administrador em subscrições de origem e de destino.

#### <a name="portal"></a>Portal

Para utilizar o portal do Azure para migrar os espaços de nomes do Service Bus para outra subscrição, siga as indicações [aqui](../azure-resource-manager/resource-group-move-resources.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

A seguinte sequência de comandos do PowerShell move um espaço de nomes de uma subscrição do Azure para outra. Para executar esta operação, o espaço de nomes já deve estar ativo e o utilizador que executa os comandos do PowerShell tem de ser um administrador em subscrições de origem e de destino.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o Service Bus, consulte os artigos seguintes:

* [Introdução ao Azure Service Bus Premium (mensagem de blogue)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução ao Azure Premium do Service Bus) (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Descrição geral do Service Bus](service-bus-messaging-overview.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
