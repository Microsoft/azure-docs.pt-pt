---
title: Perguntas frequentes (FAQ) sobre o barramento de serviço do Azure | Microsoft Docs
description: Responde a algumas perguntas frequentes sobre o barramento de serviço do Azure.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c303e2b691f8e7aa7ea3c8fcc69e39d7970ef54e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75352878"
---
# <a name="service-bus-faq"></a>Perguntas Frequentes do Service Bus

Este artigo aborda algumas perguntas frequentes sobre Barramento de Serviço do Microsoft Azure. Você também pode visitar as [perguntas frequentes](https://azure.microsoft.com/support/faq/) sobre o suporte do Azure para obter informações gerais sobre preços e suporte do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Perguntas gerais sobre o barramento de serviço do Azure
### <a name="what-is-azure-service-bus"></a>O que é o Azure Service Bus?
O [barramento de serviço do Azure](service-bus-messaging-overview.md) é uma plataforma de nuvem de mensagens assíncronas que permite que você envie dados entre sistemas separados. A Microsoft oferece esse recurso como um serviço, o que significa que você não precisa hospedar seu próprio hardware para usá-lo.

### <a name="what-is-a-service-bus-namespace"></a>O que é um namespace do barramento de serviço?
Um [namespace](service-bus-create-namespace-portal.md) fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo. A criação de um namespace é necessária para usar o barramento de serviço e é uma das primeiras etapas na introdução.

### <a name="what-is-an-azure-service-bus-queue"></a>O que é uma fila do barramento de serviço do Azure?
Uma [fila do barramento de serviço](service-bus-queues-topics-subscriptions.md) é uma entidade na qual as mensagens são armazenadas. As filas são úteis quando você tem vários aplicativos ou várias partes de um aplicativo distribuído que precisam se comunicar entre si. A fila é semelhante a um centro de distribuição no sentido de que vários produtos (mensagens) são recebidos e enviados desse local.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as assinaturas do barramento de serviço do Azure?
Um tópico pode ser visualizado como uma fila e, ao usar várias assinaturas, torna-se um modelo de mensagens mais avançado; essencialmente, uma ferramenta de comunicação um-para-muitos. Esse modelo de publicação/assinatura (ou *pub/sub*) permite que um aplicativo que envia uma mensagem a um tópico com várias assinaturas tenha essa mensagem recebida por vários aplicativos.

### <a name="what-is-a-partitioned-entity"></a>O que é uma entidade particionada?
Uma fila ou um tópico convencional é tratado por um único agente de mensagem e armazenado em um repositório de mensagens. Com suporte apenas nas camadas de sistema de mensagens básica e Standard, uma [fila ou um tópico particionado](service-bus-partitioning.md) é manipulado por vários agentes de mensagens e armazenados em vários repositórios de mensagens. Esse recurso significa que a taxa de transferência geral de uma fila ou tópico particionado não é mais limitada pelo desempenho de um único agente de mensagens ou repositório de mensagens. Além disso, uma interrupção temporária de um repositório de mensagens não processa uma fila ou tópico particionado não está disponível.

A ordenação não é garantida ao usar entidades particionadas. Caso uma partição não esteja disponível, você ainda poderá enviar e receber mensagens de outras partições.

 Não há mais suporte para entidades particionadas no [SKU Premium](service-bus-premium-messaging.md). 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Quais portas eu preciso abrir no firewall? 
Você pode usar os seguintes protocolos com o barramento de serviço do Azure para enviar e receber mensagens:

- Avançadas Message Queuing Protocol (AMQP)
- Protocolo de mensagens do barramento de serviço (SBMP)
- HTTP

Consulte a tabela a seguir para as portas de saída que você precisa abrir para usar esses protocolos para se comunicar com os hubs de eventos do Azure. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte o [Guia do protocolo AMQP](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 a 9354 | Consulte [modo de conectividade](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Quais endereços IP preciso para a lista de permissões?
Para localizar os endereços IP corretos para a lista de permissões de suas conexões, siga estas etapas:

1. Execute o seguinte comando em um prompt de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anote o endereço IP retornado em `Non-authoritative answer`. Esse endereço IP é estático. O único ponto no tempo que seria alterado seria se você restaurasse o namespace em um cluster diferente.

Se você usar a redundância de zona para seu namespace, precisará executar algumas etapas adicionais: 

1. Primeiro, execute nslookup no namespace.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anote o nome na seção de **resposta não autoritativa** , que está em um dos seguintes formatos: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Execute nslookup para cada um com sufixos S1, S2 e S3 para obter os endereços IP de todas as três instâncias em execução em três zonas de disponibilidade, 


## <a name="best-practices"></a>Melhores práticas
### <a name="what-are-some-azure-service-bus-best-practices"></a>Quais são algumas das práticas recomendadas do barramento de serviço do Azure?
Consulte [práticas recomendadas para melhorias de desempenho usando o barramento de serviço][Best practices for performance improvements using Service Bus] – este artigo descreve como otimizar o desempenho ao trocar mensagens.

### <a name="what-should-i-know-before-creating-entities"></a>O que devo saber antes de criar entidades?
As propriedades a seguir de uma fila e um tópico são imutáveis. Considere essa limitação ao provisionar suas entidades, pois essas propriedades não podem ser modificadas sem a criação de uma nova entidade de substituição.

* Criação de partições
* Sessões
* Deteção de duplicados
* Entidade expressa

## <a name="pricing"></a>Preços
Esta seção responde a algumas perguntas frequentes sobre a estrutura de preços do barramento de serviço.

O artigo [preços e cobrança do barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/) explica os medidores de cobrança no barramento de serviço. Para obter informações específicas sobre as opções de preços do barramento de serviço, consulte [detalhes de preços do barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/).

Você também pode visitar as [perguntas frequentes](https://azure.microsoft.com/support/faq/) sobre o suporte do Azure para obter informações gerais sobre preços do Azure. 

### <a name="how-do-you-charge-for-service-bus"></a>Como você cobra pelo barramento de serviço?
Para obter informações completas sobre os preços do barramento de serviço, consulte [detalhes de preços do barramento de serviço][Pricing overview]. Além dos preços indicados, você será cobrado pelas transferências de dados associadas para saída fora do data center em que seu aplicativo é provisionado.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Qual o uso do barramento de serviço está sujeito à transferência de dados? O que não é?
Qualquer transferência de dados dentro de uma determinada região do Azure é fornecida gratuitamente, bem como qualquer transferência de dados de entrada. A transferência de dados fora de uma região está sujeita a encargos de saída, que podem ser encontrados [aqui](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>O barramento de serviço cobra pelo armazenamento?
Não, o barramento de serviço não cobra pelo armazenamento. No entanto, há uma cota que limita a quantidade máxima de dados que podem persistir por fila/tópico. Consulte as próximas perguntas frequentes.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Tenho um namespace standard do barramento de serviço. Por que vejo encargos no grupo de recursos ' $system '?
O barramento de serviço do Azure atualizou os componentes de cobrança recentemente. Devido a isso, se você tiver um namespace padrão do barramento de serviço, poderá ver itens de linha para o recurso '/subscriptions/< azure_subscription_id >/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system ' no grupo de recursos ' $system '.

Esses encargos representam o encargo base por assinatura do Azure que provisionou um namespace padrão do barramento de serviço. 

É importante observar que esses não são novos encargos, ou seja, eles já existiam no modelo de cobrança anterior. A única alteração é que agora eles estão listados em ' $system '. Isso é feito devido a restrições no novo sistema de cobrança que agrupa encargos de nível de assinatura, não vinculado a um recurso específico, sob a ID de recurso ' $system '.

## <a name="quotas"></a>Quotas

Para obter uma lista de cotas e limites do barramento de serviço, consulte [visão geral das cotas do barramento de serviço][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>O barramento de serviço tem cotas de uso?
Por padrão, para qualquer serviço de nuvem, a Microsoft define uma cota de uso mensal agregada que é calculada em todas as assinaturas de um cliente. Se precisar de mais do que esses limites, você poderá entrar em contato com o atendimento ao cliente a qualquer momento para entender suas necessidades e ajustar esses limites adequadamente. Para o barramento de serviço, a cota de uso agregado é 5.000.000.000 mensagens por mês.

Enquanto a Microsoft se reserva o direito de desabilitar uma conta de cliente que excedeu suas cotas de uso em um determinado mês, as notificações por email são enviadas e várias tentativas são feitas para entrar em contato com um cliente antes de realizar qualquer ação. Os clientes que excederem essas cotas ainda serão responsáveis pelos encargos que excederem as cotas.

Assim como ocorre com outros serviços no Azure, o barramento de serviço impõe um conjunto de cotas específicas para garantir que haja um uso justo de recursos. Você pode encontrar mais detalhes sobre essas cotas na [visão geral cotas do barramento de serviço][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Como lidar com mensagens de tamanho > 1 MB?
Os serviços de mensagens do barramento de serviço (filas e tópicos/assinaturas) permitem que o aplicativo envie mensagens de tamanho até 256 KB (camada Standard) ou 1 MB (camada Premium). Se você estiver lidando com mensagens de tamanho maior que 1 MB, use o padrão de verificação de declaração descrito nesta [postagem no blog](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não consigo criar um namespace depois de excluí-lo de outra assinatura? 
Quando você exclui um namespace de uma assinatura, aguarde 4 horas antes de recriá-lo com o mesmo nome em outra assinatura. Caso contrário, você pode receber a seguinte mensagem de erro: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelas APIs do barramento de serviço do Azure e suas ações sugeridas?
Para obter uma lista de possíveis exceções do barramento de serviço, consulte [visão geral de exceções][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma assinatura de acesso compartilhado e quais idiomas dão suporte à geração de uma assinatura?
As assinaturas de acesso compartilhado são um mecanismo de autenticação baseado em hashes seguros SHA-256 ou URIs. Para obter informações sobre como gerar suas próprias assinaturas em node. js, PHP, Java, Python e C#, consulte o artigo [assinaturas de acesso compartilhado][Shared Access Signatures] .

## <a name="subscription-and-namespace-management"></a>Gerenciamento de assinatura e namespace
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como fazer migrar um namespace para outra assinatura do Azure?

Você pode mover um namespace de uma assinatura do Azure para outra, usando os comandos [portal do Azure](https://portal.azure.com) ou PowerShell. Para executar a operação, o namespace já deve estar ativo. O usuário que executa os comandos deve ser um administrador nas assinaturas de origem e de destino.

#### <a name="portal"></a>Portal

Para usar o portal do Azure para migrar namespaces do barramento de serviço para outra assinatura, siga as instruções [aqui](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

A sequência de comandos do PowerShell a seguir move um namespace de uma assinatura do Azure para outra. Para executar essa operação, o namespace já deve estar ativo e o usuário que está executando os comandos do PowerShell deve ser um administrador nas assinaturas de origem e de destino.

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
Para saber mais sobre o barramento de serviço, confira os seguintes artigos:

* [Apresentando o barramento de serviço Premium do Azure (postagem de blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Apresentando o channel9 (barramento de serviço Premium) do Azure](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do barramento de serviço](service-bus-messaging-overview.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
