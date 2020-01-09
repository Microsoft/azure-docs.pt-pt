---
title: Perguntas frequentes sobre retransmissão do Azure | Microsoft Docs
description: Obtenha respostas para algumas perguntas frequentes sobre a retransmissão do Azure.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: 066ac1080f7ea378efe1665e7ebc70e57118191c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459098"
---
# <a name="azure-relay-faqs"></a>Perguntas frequentes sobre retransmissão do Azure

Este artigo responde a algumas perguntas frequentes (FAQs) sobre a [retransmissão do Azure](https://azure.microsoft.com/services/service-bus/). Para obter informações gerais sobre preços e suporte do Azure, consulte [perguntas frequentes](https://azure.microsoft.com/support/faq/)sobre o suporte do Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-relay"></a>O que é o Reencaminhamento do Azure?
O [serviço de retransmissão do Azure](relay-what-is-it.md) facilita seus aplicativos híbridos, ajudando você a expor com mais segurança os serviços que residem em uma rede corporativa corporativa para a nuvem pública. Você pode expor os serviços sem abrir uma conexão de firewall e sem exigir alterações intrusivas em uma infraestrutura de rede corporativa.

### <a name="what-is-a-relay-namespace"></a>O que é um namespace de retransmissão?
Um [namespace](relay-create-namespace-portal.md) é um contêiner de escopo que você pode usar para endereçar recursos de retransmissão dentro de seu aplicativo. Você deve criar um namespace para usar a retransmissão. Esta é uma das primeiras etapas da introdução.

### <a name="what-happened-to-service-bus-relay-service"></a>O que aconteceu com o serviço de retransmissão do barramento de serviço?
O serviço de retransmissão do barramento de serviço anteriormente chamado agora é chamado de [retransmissão do WCF](service-bus-relay-tutorial.md). Você pode continuar a usar esse serviço como de costume. O recurso Conexões Híbridas é uma versão atualizada de um serviço que foi transplantado dos serviços BizTalk do Azure. Retransmissão do WCF e Conexões Híbridas continuam com suporte.

## <a name="pricing"></a>Preços
Esta seção responde a algumas perguntas frequentes sobre a estrutura de preços de retransmissão. Você também pode ver as [perguntas frequentes](https://azure.microsoft.com/support/faq/) sobre o suporte do Azure para obter informações gerais sobre preços do Azure. Para obter informações completas sobre os preços de retransmissão, consulte [detalhes de preços do barramento de serviço][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Como você cobra por Conexões Híbridas e Retransmissão do WCF?
Para obter informações completas sobre preços de retransmissão, consulte a tabela [conexões híbridas e retransmissões do WCF][Pricing overview] na página detalhes de preços do barramento de serviço. Além dos preços observados nessa página, você será cobrado pelas transferências de dados associadas para saída fora do datacenter no qual seu aplicativo é provisionado.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Como sou cobrado por Conexões Híbridas?
Aqui estão três exemplos de cenários de cobrança para Conexões Híbridas:

*   Cenário 1:
    *   Você tem um único ouvinte, como uma instância do Gerenciador de Conexões Híbridas instalado e em execução contínua no mês inteiro.
    *   Você envia 3 GB de dados pela conexão durante o mês. 
    *   Seu encargo total é de $5.
*   Cenário 2:
    *   Você tem um único ouvinte, como uma instância do Gerenciador de Conexões Híbridas instalado e em execução contínua no mês inteiro.
    *   Você envia 10 GB de dados pela conexão durante o mês.
    *   Seu encargo total é de $7.50. Isso é $5 para a conexão e os primeiros 5 GB + $2.50 para os 5 GB adicionais de dados.
*   Cenário 3:
    *   Você tem duas instâncias, A e B, do Gerenciador de Conexões Híbridas instalado e em execução contínua no mês inteiro.
    *   Você envia 3 GB de dados pela conexão A durante o mês.
    *   Você envia 6 GB de dados pela conexão B durante o mês.
    *   Seu encargo total é de $10.50. Isso é $5 para a conexão A + $5 para a conexão B + $0.50 (para o sexto Gigabyte na conexão B).

Observe que os preços usados nos exemplos são aplicáveis somente durante o período de visualização de Conexões Híbridas. Os preços estão sujeitos a alterações após a disponibilidade geral do Conexões Híbridas.

### <a name="how-are-hours-calculated-for-relay"></a>Como as horas são calculadas para retransmissão?

Retransmissão do WCF está disponível somente em namespaces de camada Standard. Os preços e as [cotas de conexão](../service-bus-messaging/service-bus-quotas.md) para retransmissões, caso contrário, não foram alteradas. Isso significa que as retransmissões continuam a ser cobradas com base no número de mensagens (não operações) e horas de retransmissão. Para obter mais informações, consulte a tabela ["retransmissões do conexões híbridas e do WCF"](https://azure.microsoft.com/pricing/details/service-bus/) na página de detalhes de preços.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>E se eu tiver mais de um ouvinte conectado a uma retransmissão específica?
Em alguns casos, uma única retransmissão pode ter vários ouvintes conectados. Uma retransmissão é considerada aberta quando pelo menos um ouvinte de retransmissão está conectado a ela. Adicionar ouvintes a uma retransmissão aberta resulta em horas de retransmissão adicionais. O número de remetentes de retransmissão (clientes que chamam ou enviam mensagens para retransmissões) que estão conectados a uma retransmissão não afeta o cálculo de horas de retransmissão.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Como o medidor de mensagens é calculado para retransmissões do WCF?
(**Isso se aplica somente às retransmissões do WCF. As mensagens não são um custo para Conexões Híbridas.** )

Em geral, as mensagens faturáveis para retransmissões são calculadas usando o mesmo método usado para entidades orientadas (filas, tópicos e assinaturas), descritas anteriormente. No entanto, há algumas diferenças notáveis.

O envio de uma mensagem para uma retransmissão do barramento de serviço é tratado como um envio "completo por" para o ouvinte de retransmissão que recebe a mensagem. Ele não é tratado como uma operação de envio para a retransmissão do barramento de serviço, seguido de uma entrega para o ouvinte de retransmissão. Uma invocação de serviço de estilo de solicitação/resposta (de até 64 KB) em relação a um ouvinte de retransmissão resulta em duas mensagens faturáveis: uma mensagem Faturável para a solicitação e uma mensagem Faturável para a resposta (supondo que a resposta também seja 64 KB ou menor). Isso é diferente de usar uma fila para mediar entre um cliente e um serviço. Se você usar uma fila para mediar entre um cliente e um serviço, o mesmo padrão de solicitação-resposta exigirá uma solicitação de envio para a fila, seguido por uma remoção da fila/entrega da fila para o serviço. Isso é seguido por uma resposta enviada para outra fila e uma remoção da fila/entrega dessa fila para o cliente. Usando as mesmas suposições de tamanho em todo (até 64 KB), o padrão de fila mediado resulta em 4 mensagens faturáveis. Você será cobrado por duas vezes o número de mensagens para implementar o mesmo padrão que você realiza usando a retransmissão. É claro que há benefícios em usar filas para atingir esse padrão, como durabilidade e nivelamento de carga. Esses benefícios podem justificar a despesa adicional.

As retransmissões que são abertas usando a associação do WCF **netTCPRelay** tratam mensagens não como mensagens individuais, mas como um fluxo de dados que fluem pelo sistema. Quando você usa essa associação, somente o remetente e o ouvinte têm visibilidade do enquadramento das mensagens individuais enviadas e recebidas. Para retransmissões que usam a associação **netTCPRelay** , todos os dados são tratados como um fluxo para calcular mensagens faturáveis. Nesse caso, o barramento de serviço calcula a quantidade total de dados enviados ou recebidos por meio de cada retransmissão individual em uma base de 5 minutos. Em seguida, ele divide a quantidade total de dados de 64 KB para determinar o número de mensagens faturáveis para essa retransmissão durante esse período de tempo.

## <a name="quotas"></a>Quotas
| Nome da cota | Âmbito |  Notas | Valor |
| --- | --- | --- | --- |
| Ouvintes simultâneos em uma retransmissão |Entidade |As solicitações subsequentes para conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |25 |
| Conexões de retransmissão simultâneas por todos os pontos de extremidade de retransmissão em um namespace de serviço |Espaço de Nomes |- |5000 |
| Pontos de extremidade de retransmissão por namespace de serviço |Espaço de Nomes |- |10,000 |
| Tamanho da mensagem para retransmissões [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) e [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) |Espaço de Nomes |Mensagens de entrada que excedem essas cotas são rejeitadas e uma exceção é recebida pelo código de chamada. |64 KB |
| Tamanho da mensagem para retransmissões [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) e [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) |Espaço de Nomes |Sem limite para o tamanho da mensagem. |Ilimitado |

### <a name="does-relay-have-any-usage-quotas"></a>A retransmissão tem cotas de uso?
Por padrão, para qualquer serviço de nuvem, a Microsoft define uma cota de uso mensal agregada que é calculada em todas as assinaturas de um cliente. Entendemos que, às vezes, suas necessidades podem exceder esses limites. Você pode entrar em contato com o atendimento ao cliente a qualquer momento, para que possamos entender suas necessidades e ajustar esses limites adequadamente. Para o barramento de serviço, as cotas de uso agregado são as seguintes:

* 5\.000.000.000 mensagens
* 2 milhões horas de retransmissão

Apesar de reservarmos o direito de desabilitar uma conta que exceda suas cotas de uso mensal, fornecemos uma notificação por email e fazemos várias tentativas de entrar em contato com o cliente antes de realizar qualquer ação. Os clientes que excederem essas cotas ainda serão responsáveis pelo excesso de encargos.

### <a name="naming-restrictions"></a>Restrições de nomenclatura
Um nome de namespace de retransmissão deve ter entre 6 e 50 caracteres de comprimento.

## <a name="subscription-and-namespace-management"></a>Gerenciamento de assinatura e namespace
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como fazer migrar um namespace para outra assinatura do Azure?

Para mover um namespace de uma assinatura do Azure para outra assinatura, você pode usar o [portal do Azure](https://portal.azure.com) ou usar comandos do PowerShell. Para mover um namespace para outra assinatura, o namespace já deve estar ativo. O usuário que executa os comandos deve ser um usuário administrador nas assinaturas de origem e de destino.

#### <a name="azure-portal"></a>Portal do Azure

Para usar o portal do Azure para migrar namespaces de retransmissão do Azure de uma assinatura para outra, consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Para usar o PowerShell para mover um namespace de uma assinatura do Azure para outra assinatura, use a seguinte sequência de comandos. Para executar essa operação, o namespace já deve estar ativo e o usuário que está executando os comandos do PowerShell deve ser um usuário administrador nas assinaturas de origem e de destino.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Quais são algumas das exceções geradas pelas APIs de retransmissão do Azure e as ações sugeridas que você pode tomar?
Para obter uma descrição das exceções comuns e as ações sugeridas que você pode tomar, consulte [exceções de retransmissão][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>O que é uma assinatura de acesso compartilhado e quais idiomas posso usar para gerar uma assinatura?
As SAS (assinaturas de acesso compartilhado) são um mecanismo de autenticação baseado em hashes seguros SHA-256 ou URIs. Para obter informações sobre como gerar suas próprias assinaturas em node. js, PHP, Python, Java, C e C#, consulte Autenticação do [barramento de serviço com assinaturas de acesso compartilhado][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>É possível para os pontos de extremidade de retransmissão da lista de permissões?
Sim. O cliente de retransmissão faz conexões com o serviço de retransmissão do Azure usando nomes de domínio totalmente qualificados. Os clientes podem adicionar uma entrada para `*.servicebus.windows.net` em firewalls que dão suporte à lista de permissões de DNS.

## <a name="next-steps"></a>Passos seguintes
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Nó](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
