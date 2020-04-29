---
title: FaQs de Retransmissão Azure Microsoft Docs
description: Este artigo fornece respostas a algumas das perguntas frequentes sobre o serviço Azure Relay.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: d5032b427316a3c4e07013af4e8214e239a6efb3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76514005"
---
# <a name="azure-relay-faqs"></a>FaQs de retransmissão azure

Este artigo responde a algumas perguntas frequentes (PERGUNTAS) sobre [o Azure Relay](https://azure.microsoft.com/services/service-bus/). Para obter informações gerais sobre preços e suporte do Azure, consulte as [FAQs](https://azure.microsoft.com/support/faq/)de Suporte Azure .


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-relay"></a>O que é o Reencaminhamento do Azure?
O [serviço Azure Relay](relay-what-is-it.md) facilita as suas aplicações híbridas ajudando-o a expor de forma mais segura serviços que residem dentro de uma rede empresarial corporativa para a nuvem pública. Pode expor os serviços sem abrir uma ligação de firewall, e sem exigir alterações intrusivas numa infraestrutura de rede corporativa.

### <a name="what-is-a-relay-namespace"></a>O que é um espaço de nome retransmissor?
Um [espaço de nome](relay-create-namespace-portal.md) é um recipiente de deteção que pode utilizar para endereçar recursos de retransmissão dentro da sua aplicação. Tem de criar um espaço de nome para utilizar o Relé. Este é um dos primeiros passos para começar.

### <a name="what-happened-to-service-bus-relay-service"></a>O que aconteceu ao serviço de Retransmissão de Autocarros de Serviço?
O serviço de retransmissão de ônibus de serviço anteriormente chama-se [WCF Relay](service-bus-relay-tutorial.md). Pode continuar a utilizar este serviço como de costume. A funcionalidade Ligações Híbridas é uma versão atualizada de um serviço que foi transplantado dos Serviços Azure BizTalk. As ligações wCF Relay e Hybrid continuam a ser apoiadas.

## <a name="pricing"></a>Preços
Esta secção responde a algumas perguntas frequentes sobre a estrutura de preços do relé. Você também pode ver as [FAQs](https://azure.microsoft.com/support/faq/) de Suporte Azure para informações gerais de preços Azure. Para obter informações completas sobre os preços do Retransmissor, consulte os detalhes dos [preços do Autocarro de Serviço.][Pricing overview]

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Como se cobra ligações híbridas e relé WCF?
Para obter informações completas sobre os preços do Retransmissor, consulte a tabela de [Ligações Híbridas e Retransmissões WCF][Pricing overview] na página de detalhes de preços do Autocarro de Serviço. Além dos preços anotados nessa página, é cobrado por transferências de dados associadas para saída do datacenter em que a sua aplicação é aprovisionada.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Como é que eu sou cobrado por Ligações Híbridas?
Aqui estão três exemplos de cenários de faturação para Ligações Híbridas:

*   Cenário 1:
    *   Tem um único ouvinte, como um exemplo do Hybrid Connections Manager instalado e continuamente em execução durante todo o mês.
    *   Envia 3 GB de dados através da ligação durante o mês. 
    *   A sua taxa total é de $5.
*   Cenário 2:
    *   Tem um único ouvinte, como um exemplo do Hybrid Connections Manager instalado e continuamente em execução durante todo o mês.
    *   Envia 10 GB de dados através da ligação durante o mês.
    *   A sua taxa total é de $7,50. São $5 pela ligação e primeiro 5 GB + $2,50 para os 5 GB adicionais de dados.
*   Cenário 3:
    *   Tem duas instâncias, A e B, do Hybrid Connections Manager instalados e em execução contínua durante todo o mês.
    *   Envia 3 GB de dados através da ligação A durante o mês.
    *   Envia 6 GB de dados através da ligação B durante o mês.
    *   A sua taxa total é de $10,50. São $5 para a ligação A + $5 para a ligação B + $0,50 (para o sexto gigabyte na ligação B).

Note que os preços utilizados nos exemplos são aplicáveis apenas durante o período de pré-visualização das Ligações Híbridas. Os preços estão sujeitos a alterações mediante a disponibilidade geral de Ligações Híbridas.

### <a name="how-are-hours-calculated-for-relay"></a>Como são as horas calculadas para o Relé?

O WCF Relay está disponível apenas em espaços de nome standard. Os preços e as quotas de [ligação](../service-bus-messaging/service-bus-quotas.md) para os relés não mudaram. Isto significa que os retransmissores continuam a ser carregados com base no número de mensagens (não em operações) e nas horas de retransmissão. Para mais informações, consulte a tabela ["Ligações Híbridas e Relés WCF"](https://azure.microsoft.com/pricing/details/service-bus/) na página de detalhes de preços.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>E se eu tiver mais do que um ouvinte ligado a um retransmissor específico?
Em alguns casos, um único retransmissor pode ter vários ouvintes conectados. Um retransmissor é considerado aberto quando pelo menos um ouvinte está ligado a ele. Adicionar os ouvintes a um retransmissor aberto resulta em horas de retransmissão adicionais. O número de remetentes de retransmissão (clientes que invocam ou enviam mensagens para retransmissores) que estão ligados a um retransmissor não afeta o cálculo das horas de retransmissão.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Como é calculado o medidor de mensagens para relés WCF?
(**Isto aplica-se apenas aos relés WCF. As mensagens não são um custo para ligações híbridas.**

Em geral, as mensagens faturadas para retransmissores são calculadas utilizando o mesmo método que é usado para entidades intermediadas (filas, tópicos e subscrições), descritoanteriormente. No entanto, existem algumas diferenças notáveis.

O envio de uma mensagem para um retransmissor de ônibus de serviço é tratado como um envio "completo" para o ouvinte que recebe a mensagem. Não é tratado como uma operação de envio para o retransmissor do Ônibus de Serviço, seguido de uma entrega ao ouvinte de retransmissão. Uma invocação de serviço de estilo de resposta a pedido (de até 64 KB) contra um ouvinte de retransmissão resulta em duas mensagens faturadas: uma mensagem faturada para o pedido e uma mensagem faturada para a resposta (assumindo que a resposta também é de 64 KB ou menor). Isto é diferente de usar uma fila para mediar entre um cliente e um serviço. Se utilizar uma fila para mediar entre um cliente e um serviço, o mesmo padrão de resposta a pedidos requer um pedido enviado para a fila, seguido de uma defila/entrega da fila para o serviço. Isto é seguido por uma resposta enviada para outra fila, e uma fila/entrega dessa fila para o cliente. Utilizando as mesmas suposições de tamanho em todo (até 64 KB), o padrão mediado de fila resulta em 4 mensagens de faturação. Seria cobrado pelo dobro do número de mensagens para implementar o mesmo padrão que consegue usando o retransmissor. É claro que há benefícios em usar filas para atingir este padrão, como durabilidade e nivelamento de carga. Estes benefícios podem justificar as despesas adicionais.

Os retransmissores que são abertos utilizando a **redeTCPRelay** WCF tratam as mensagens não como mensagens individuais, mas como um fluxo de dados que fluem através do sistema. Quando utiliza esta ligação, apenas o remetente e o ouvinte têm visibilidade no enquadramento das mensagens individuais enviadas e recebidas. Para retransmissões que utilizam a ligação **netTCPRelay,** todos os dados são tratados como um fluxo para o cálculo de mensagens faturadas. Neste caso, a Service Bus calcula a quantidade total de dados enviados ou recebidos através de cada retransmissão individual numa base de 5 minutos. Em seguida, divide essa quantidade total de dados em 64 KB para determinar o número de mensagens faturadas para esse retransmissor durante esse período de tempo.

## <a name="quotas"></a>Quotas
| Nome de quota | Âmbito |  Notas | Valor |
| --- | --- | --- | --- |
| Ouvintes simultâneos em um retransmissor |Entidade |Os pedidos subsequentes de ligações adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |25 |
| Conexões de retransmissão simultâneas por todos os pontos finais de retransmissão num espaço de nome de serviço |Espaço de nomes |- |5000 |
| Pontos finais de retransmissão por espaço de nome de serviço |Espaço de nomes |- |10,000 |
| Tamanho da mensagem para [relés NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) e [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) |Espaço de nomes |As mensagens recebidas que excedam estas quotas são rejeitadas e uma exceção é recebida pelo código de chamada. |64 KB |
| Tamanho da mensagem para relés [httpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) e [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) |Espaço de nomes |Sem limite no tamanho da mensagem. |Ilimitado |

### <a name="does-relay-have-any-usage-quotas"></a>A Relay tem alguma quota de utilização?
Por padrão, para qualquer serviço na nuvem, a Microsoft define uma quota de utilização mensal agregada que é calculada em todas as subscrições de um cliente. Entendemos que, por vezes, as suas necessidades podem exceder estes limites. Pode contactar o atendimento ao cliente a qualquer momento, para que possamos compreender as suas necessidades e ajustar estes limites adequadamente. Para o Service Bus, as quotas de utilização agregadas são as seguintes:

* 5 mil milhões de mensagens
* 2 milhões de horas de retransmissão

Embora nos reservemos o direito de desativar uma conta que exceda as suas quotas de utilização mensal, fornecemos notificação por e-mail, e fazemos várias tentativas para contactar o cliente antes de tomar qualquer ação. Os clientes que excedem estas quotas continuam a ser responsáveis pelo excesso de encargos.

### <a name="naming-restrictions"></a>Restrições de nomeação
Um nome de espaço de identificação deve ter entre 6 e 50 caracteres de comprimento.

## <a name="subscription-and-namespace-management"></a>Gestão de subscrição e espaço de nome
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como migrar um espaço de nome para outra subscrição do Azure?

Para mover um espaço de nome de uma subscrição Azure para outra subscrição, pode utilizar o [portal Azure](https://portal.azure.com) ou utilizar comandos PowerShell. Para mover um espaço de nome para outra subscrição, o espaço de nome já deve estar ativo. O utilizador que executa os comandos deve ser um utilizador administrador tanto na fonte como nas subscrições de destino.

#### <a name="azure-portal"></a>Portal do Azure

Para utilizar o portal Azure para migrar espaços de nome sinuoso Azure de uma subscrição para outra subscrição, consulte [os recursos move para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Para utilizar o PowerShell para mover um espaço de nome de uma subscrição Azure para outra subscrição, utilize a seguinte sequência de comandos. Para executar esta operação, o espaço de nome já deve estar ativo e o utilizador que executa os comandos PowerShell deve ser um utilizador administrador tanto na fonte como nas subscrições-alvo.

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
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Quais são algumas das exceções geradas pelas APIs do Relé Azure e as ações sugeridas que pode tomar?
Para uma descrição de exceções comuns e ações sugeridas que pode tomar, consulte [as exceções do Relé][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>O que é uma assinatura de acesso partilhado, e que línguas posso usar para gerar uma assinatura?
As Assinaturas de Acesso Partilhado (SAS) são um mecanismo de autenticação baseado em hashes ou URIs seguros SHA-256. Para obter informações sobre como gerar as suas próprias assinaturas em Node.js, PHP, Python, Java, C e C#, consulte a [autenticação service Bus com assinaturas de acesso partilhado][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>É possível retransmitir pontos finais de retransmissão whitelist?
Sim. O cliente retransmissor faz ligações ao serviço Azure Relay utilizando nomes de domínio totalmente qualificados. Os clientes podem `*.servicebus.windows.net` adicionar uma entrada para firewalls que suportam a lista de dNS whitelisting.

## <a name="next-steps"></a>Passos seguintes
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Começar com .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Nó](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
