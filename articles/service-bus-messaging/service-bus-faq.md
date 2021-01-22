---
title: Azure Service Bus frequentemente fez perguntas (FAQ) | Microsoft Docs
description: Este artigo fornece respostas a algumas das perguntas frequentes (FAQ) sobre a Azure Service Bus.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 3a96cf94ca4a7edd115f12b3e2eded11a5894e04
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693410"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus - Perguntas frequentes (FAQ)

Este artigo discute algumas perguntas frequentes sobre a Microsoft Azure Service Bus. Também pode visitar as [FAQs de Apoio Azure](https://azure.microsoft.com/support/faq/) para obter informações sobre preços e suporte geral da Azure.


## <a name="general-questions-about-azure-service-bus"></a>Perguntas gerais sobre o Azure Service Bus
### <a name="what-is-azure-service-bus"></a>O que é o Azure Service Bus?
[A Azure Service Bus](service-bus-messaging-overview.md) é uma plataforma de nuvem de mensagens assíncrona que lhe permite enviar dados entre sistemas dissociados. A Microsoft oferece esta funcionalidade como um serviço, o que significa que não precisa de hospedar o seu próprio hardware para o utilizar.

### <a name="what-is-a-service-bus-namespace"></a>O que é um espaço de nomes de autocarros de serviço?
Um [espaço de nome](service-bus-create-namespace-portal.md) fornece um recipiente de deteção para abordar os recursos do Service Bus dentro da sua aplicação. Criar um espaço de nome é necessário para usar o Service Bus e é um dos primeiros passos para começar.

### <a name="what-is-an-azure-service-bus-queue"></a>O que é uma fila de autocarros da Azure Service?
Uma [fila de autocarros de serviço](service-bus-queues-topics-subscriptions.md) é uma entidade na qual as mensagens são armazenadas. As filas são úteis quando se tem múltiplas aplicações, ou várias partes de uma aplicação distribuída que precisam de comunicar entre si. A fila é semelhante a um centro de distribuição na medida em que vários produtos (mensagens) são recebidos e depois enviados a partir desse local.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Quais são os tópicos e subscrições do Azure Service Bus?
Um tópico pode ser visualizado como uma fila e ao usar várias subscrições, torna-se um modelo de mensagens mais rico; essencialmente uma ferramenta de comunicação de um para muitos. Este modelo de publicação/subscrição *(pub/sub)* permite que uma aplicação que envie uma mensagem para um tópico com várias subscrições tenha essa mensagem recebida por várias aplicações.

### <a name="what-is-a-partitioned-entity"></a>O que é uma entidade dividida?
Uma fila ou tópico convencional é tratado por um único corretor de mensagens e armazenado numa loja de mensagens. Suportado apenas nos níveis de mensagens Basic e Standard, uma [fila dividida ou tópico](service-bus-partitioning.md) é tratado por vários corretores de mensagens e armazenado em várias lojas de mensagens. Esta funcionalidade significa que o rendimento geral de uma fila ou tópico dividido já não é limitado pelo desempenho de um único corretor de mensagens ou loja de mensagens. Além disso, uma paragem temporária de uma loja de mensagens não torna uma fila ou tópico dividido indisponíveis.

A encomenda não é assegurada quando se utiliza entidades divididas. No caso de uma partição não existir, pode ainda enviar e receber mensagens das outras divisórias.

 As entidades divididas deixaram de ser apoiadas no [SKU Premium.](service-bus-premium-messaging.md) 

### <a name="where-does-azure-service-bus-store-data"></a><a name="in-region-data-residency"></a>Onde é que a Azure Service Bus armazena os dados?
O nível padrão do Azure Service Bus utiliza a Base de Dados Azure SQL para a sua camada de armazenamento de backend. Para todas as regiões, com exceção do Brasil Sul e Sudeste Asiático, a base de dados de apoio está hospedada numa região diferente (geralmente a região emparelhada Azure). Para as regiões do Sul e do Sudeste Asiático, as cópias de dados são armazenadas na mesma região para acomodar os requisitos de residência de dados para estas regiões.

O nível premium da Azure Service Bus armazena metadados e dados em regiões que seleciona. Quando a recuperação de geo-desastres é configurada para um espaço de nome premium Azure Service Bus, os metadados são copiados para a região secundária que seleciona.


### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Que portas preciso para abrir na firewall? 
Pode utilizar os seguintes protocolos com a Azure Service Bus para enviar e receber mensagens:

- Protocolo avançado de fila de mensagens 1.0 (AMQP)
- Protocolo de Transferência de Hipertexto 1.1 com TLS (HTTPS)

Consulte a tabela seguinte para as portas TCP de saída que precisa de abrir para utilizar estes protocolos para comunicar com a Azure Service Bus:

| Protocolo | Porta | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 | AMQP com TLS. Consulte o [guia de protocolo amQP](service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Esta porta é utilizada para a API HTTP/REST e para amqp-over-WebSockets |

A porta HTTPS é geralmente necessária para comunicação de saída também quando amQP é usado sobre o porto 5671, porque várias operações de gestão realizadas pelos SDKs cliente e a aquisição de tokens da Azure Ative Directy (quando usado) atropelam HTTPS. 

Os Azure SDKs oficiais geralmente usam o protocolo AMQP para o envio e receção de mensagens da Service Bus. 

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

O antigo pacote WindowsAzure.ServiceBus para o Quadro .NET tem a opção de usar o legado "Service Bus Messaging Protocol" (SBMP), também referido como "NetMessaging". Este protocolo utiliza as portas TCP 9350-9354. O modo predefinido para este pacote é detetar automaticamente se essas portas estão disponíveis para comunicação e mudarão para WebSockets com TLS sobre a porta 443, se não for esse o caso. Pode anular esta definição e forçar este modo definindo o `Https` [ConnectivityMode](/dotnet/api/microsoft.servicebus.connectivitymode) na [`ServiceBusEnvironment.SystemConnectivity`](/dotnet/api/microsoft.servicebus.servicebusenvironment.systemconnectivity) definição, que se aplica globalmente à aplicação.

### <a name="what-ip-addresses-do-i-need-to-add-to-allow-list"></a>Que endereços IP preciso de adicionar para permitir a lista?
Para encontrar os endereços IP certos para adicionar para permitir a lista para as suas ligações, siga estes passos:

1. Executar o seguinte comando a partir de um pedido de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Note o endereço IP devolvido em `Non-authoritative answer` . 

Se utilizar a **redundância** da zona para o seu espaço de nome, tem de fazer alguns passos adicionais: 

1. Primeiro, corres nslookup no espaço de nomes.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Note o nome na secção **de resposta não autorizada,** que se encontra num dos seguintes formatos: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Executar nslookup para cada um com sufixos s1, s2 e s3 para obter os endereços IP dos três casos em execução em três zonas de disponibilidade, 

    > [!NOTE]
    > O endereço IP devolvido pelo `nslookup` comando não é um endereço IP estático. No entanto, permanece constante até que a implementação subjacente seja eliminada ou transferida para um cluster diferente.

### <a name="where-can-i-find-the-ip-address-of-the-client-sendingreceiving-messages-tofrom-a-namespace"></a>Onde posso encontrar o endereço IP do cliente enviando/recebendo mensagens de/para um espaço de nome? 
Não registamos os endereços IP dos clientes que enviam ou recebem mensagens de/para o seu espaço de nome. Regenerar as chaves para que todos os clientes existentes não autentiquem e revejam as definições [de controlo de acesso baseado em funções (Azure RBAC)](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)para garantir que apenas os utilizadores ou aplicações autorizados tenham acesso ao espaço de nome. 

Se estiver a utilizar um espaço de nome **premium,** utilize [a filtragem IP,](service-bus-ip-filtering.md) [os pontos finais do serviço de rede virtual](service-bus-service-endpoints.md)e os [pontos finais privados](private-link-service.md) para limitar o acesso ao espaço de nomes. 

## <a name="best-practices"></a>Melhores práticas
### <a name="what-are-some-azure-service-bus-best-practices"></a>Quais são as melhores práticas do Azure Service Bus?
Veja [as melhores práticas para melhorias de desempenho usando o Service Bus][Best practices for performance improvements using Service Bus] – este artigo descreve como otimizar o desempenho ao trocar mensagens.

### <a name="what-should-i-know-before-creating-entities"></a>O que devo saber antes de criar entidades?
As seguintes propriedades de uma fila e tópico são imutáveis. Considere esta limitação quando forte as suas entidades, uma vez que estas propriedades não podem ser modificadas sem criar uma nova entidade de substituição.

* Criação de partições
* Sessões
* Deteção de duplicados
* Entidade expressa

## <a name="pricing"></a>Preços
Esta secção responde a algumas perguntas frequentes sobre a estrutura de preços do Service Bus.

O artigo [de preços e faturação](https://azure.microsoft.com/pricing/details/service-bus/) do Service Bus explica os contadores de faturação em Service Bus. Para obter informações específicas sobre as opções de preços do Service [Bus,](https://azure.microsoft.com/pricing/details/service-bus/)consulte os detalhes dos preços do Service Bus .

Também pode visitar as [FAQs de Suporte Azure](https://azure.microsoft.com/support/faq/) para obter informações sobre preços gerais do Azure. 

### <a name="how-do-you-charge-for-service-bus"></a>Como cobra pela Service Bus?
Para obter informações completas sobre os preços do Service Bus, consulte [os detalhes dos preços do Service Bus][Pricing overview]. Além dos preços anotados, é cobrado por transferências de dados associadas para saídas fora do centro de dados em que a sua aplicação é a provisionada.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-isnt"></a>Que utilização da Service Bus está sujeita a transferência de dados? O que não é?
Qualquer transferência de dados dentro de uma determinada região de Azure é fornecida gratuitamente, bem como qualquer transferência de dados de entrada. A transferência de dados para fora de uma região está sujeita a encargos de saída, que podem ser encontrados [aqui.](https://azure.microsoft.com/pricing/details/bandwidth/)

### <a name="does-service-bus-charge-for-storage"></a>O Service Bus cobra pelo armazenamento?
N.º A Service Bus não cobra pelo armazenamento. No entanto, há uma quota que limita a quantidade máxima de dados que podem ser persistidos por fila/tópico. Veja as próximas FAQ.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Tenho um espaço de nomes padrão de autocarro de serviço. Por que vejo acusações no âmbito do grupo de recursos "$system"?
A Azure Service Bus atualizou recentemente os componentes de faturação. Devido a esta alteração, se tiver um espaço de nomes Service Bus Standard, poderá ver itens de linha para o recurso '/subscrições/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system' no grupo de recursos '$system'.

Estes encargos representam a taxa base por subscrição Azure que adpôs um espaço de nome Service Bus Standard. 

É importante notar que estas acusações não são novas, ou seja, também existiam no modelo de faturação anterior. A única mudança é que estão agora listados em "$system". É feito devido a restrições no novo sistema de faturação que agrum taxas de nível de subscrição, não ligadas a um recurso específico, sob o ID de recursos "$system".

## <a name="quotas"></a>Quotas

Para obter uma lista dos limites e quotas do Service Bus, consulte a [visão geral][Quotas overview]das quotas do Service Bus .

### <a name="how-to-handle-messages-of-size--1-mb"></a>Como lidar com mensagens de tamanho > 1 MB?
Os serviços de mensagens Service Bus (filas e tópicos/subscrições) permitem que a aplicação envie mensagens de tamanho até 256 KB (nível padrão) ou 1 MB (nível premium). Se estiver a lidar com mensagens de tamanho superior a 1 MB, utilize o padrão de verificação de reclamações descrito [neste post de blog](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não posso criar um espaço de nome depois de o eliminar de outra subscrição? 
Quando eliminar um espaço de nome de uma subscrição, aguarde 4 horas antes de recriá-lo com o mesmo nome noutra subscrição. Caso contrário, poderá receber a seguinte mensagem de erro: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelas APIs do Azure Service Bus e as suas ações sugeridas?
Para obter uma lista de possíveis exceções ao Service Bus, consulte [a visão geral das exceções.][Exceptions overview]

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma Assinatura de Acesso Partilhado e quais os idiomas que suportam gerar uma assinatura?
As Assinaturas de Acesso Partilhado são um mecanismo de autenticação baseado em hashes ou URIs seguros SHA-256. Para obter informações sobre como gerar as suas próprias assinaturas em Node.js, PHP, Java, Python e C#, consulte o artigo [Assinaturas de Acesso Partilhado.][Shared Access Signatures]

## <a name="subscription-and-namespace-management"></a>Gestão de subscrição e espaço de nome
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um espaço de nome para outra subscrição do Azure?

Pode mover um espaço de nome de uma subscrição Azure para outra, utilizando os comandos [do portal Azure](https://portal.azure.com) ou powerShell. Para executar a operação, o espaço de nome já deve estar ativo. O utilizador que executa os comandos deve ser um administrador tanto nas assinaturas de origem como no alvo.

#### <a name="portal"></a>Portal

Para utilizar o portal Azure para migrar os espaços de nomes do Service Bus para outra subscrição, siga as instruções [aqui.](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal) 

#### <a name="powershell"></a>PowerShell

A seguinte sequência de comandos PowerShell move um espaço de nome de uma subscrição de Azure para outra. Para executar esta operação, o espaço de nomes já deve estar ativo, e o utilizador que executa os comandos PowerShell deve ser um administrador tanto nas subscrições de origem como de destino.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```
## <a name="is-it-possible-to-disable-tls-10-or-11-on-service-bus-namespaces"></a>É possível desativar tLS 1.0 ou 1.1 em espaços de nomes de Service Bus?
N.º Não é possível desativar tLS 1.0 ou 1.1 em espaços de nomes de Service Bus. Nas aplicações do seu cliente que ligam o Service Bus, utilize o TLS 1.2 ou superior. Para obter mais informações, consulte [a utilização do TLS 1.2 com o Azure Service Bus - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/messaging-on-azure/enforcing-tls-1-2-use-with-azure-service-bus/ba-p/370912).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a Service Bus, consulte os seguintes artigos:

* [Apresentando Azure Service Bus Premium (blog post)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Apresentando O Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do ônibus de serviço](service-bus-messaging-overview.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
