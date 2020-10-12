---
title: Azure Relay FAQs / Microsoft Docs
description: Este artigo fornece respostas a algumas das perguntas frequentes sobre o serviço Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9d967d926c6ab59e027fe4d4cf98e8418a8ff9bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89299291"
---
# <a name="azure-relay-faqs"></a>Perguntas frequentes de retransmissão de Azure

Este artigo responde a algumas perguntas frequentes (FAQs) sobre [Azure Relay](https://azure.microsoft.com/services/service-bus/). Para obter informações gerais sobre preços e suporte da Azure, consulte as [FAQs de suporte do Azure](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-relay"></a>O que é o Azure Relay?
O [serviço Azure Relay](relay-what-is-it.md) facilita as suas aplicações híbridas ajudando-o a expor de forma mais segura os serviços que residem dentro de uma rede empresarial para a nuvem pública. Pode expor os serviços sem abrir uma ligação de firewall e sem exigir alterações intrusivas numa infraestrutura de rede corporativa.

### <a name="what-is-a-relay-namespace"></a>O que é um espaço de nomes de Retransmissor?
Um [espaço de nome](relay-create-namespace-portal.md) é um recipiente de deteção que pode usar para tratar recursos de Retransmissão dentro da sua aplicação. Tem de criar um espaço de nome para utilizar o Relay. Este é um dos primeiros passos para começar.

### <a name="what-happened-to-service-bus-relay-service"></a>O que aconteceu ao serviço Service Bus Relay?
O serviço de retransmissão de autocarros de serviço anteriormente chamado [Azure Relay.](service-bus-relay-tutorial.md) Pode continuar a utilizar este serviço como de costume. A funcionalidade Conexões Híbridas é uma versão atualizada de um serviço que foi transplantado a partir dos Serviços Azure BizTalk. As ligações WCF Relay e Hybrid continuam a ser suportadas.

## <a name="pricing"></a>Preços
Esta secção responde a algumas perguntas frequentes sobre a estrutura de preços do Relay. Também pode ver as [FAQs de suporte Azure](https://azure.microsoft.com/support/faq/) para obter informações sobre preços gerais do Azure. Para obter informações completas sobre os preços da Relay, consulte [os detalhes dos preços do Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Como cobra por Conexões Híbridas e Retransmissão WCF?
Para obter informações completas sobre os preços dos Retransmissores, consulte a tabela [Hybrid Connections e WCF Relays][Pricing overview] na página de detalhes de preços do Service Bus. Além dos preços indicados nessa página, é cobrado por transferências de dados associadas para saídas fora do datacenter em que a sua aplicação é anteque.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Como é que eu sou cobrado para ligações híbridas?
Aqui estão três cenários de faturação de exemplo para conexões híbridas:

*   Cenário 1:
    *   Tem um único ouvinte, como um exemplo do Gestor de Ligações Híbridas instalado e continuamente em execução durante todo o mês.
    *   Envia 3 GB de dados através da ligação durante o mês. 
    *   O seu custo total é de $5.
*   Cenário 2:
    *   Tem um único ouvinte, como um exemplo do Gestor de Ligações Híbridas instalado e continuamente em execução durante todo o mês.
    *   Envia 10 GB de dados através da ligação durante o mês.
    *   O seu custo total é de $7,50. São $5 pela ligação e primeiro 5 GB + $2,50 para os 5 GB adicionais de dados.
*   Cenário 3:
    *   Tem duas instâncias, A e B, do Gestor de Ligações Híbridas instaladas e continuamente em execução durante todo o mês.
    *   Envia 3 GB de dados através da ligação A durante o mês.
    *   Envia 6 GB de dados através da ligação B durante o mês.
    *   O seu custo total é de $10,50. São $5 para a ligação A + $5 para a ligação B + $0,50 (para o sexto gigabyte na ligação B).

Note que os preços utilizados nos exemplos são aplicáveis apenas durante o período de pré-visualização das Ligações Híbridas. Os preços estão sujeitos a alterações na disponibilidade geral de Conexões Híbridas.

### <a name="how-are-hours-calculated-for-relay"></a>Como são calculadas as horas para o Relay?

O WCF Relay está disponível apenas em espaços de nome de nível Standard. As quotas de preços e [de ligação](../service-bus-messaging/service-bus-quotas.md) para retransmissores não mudaram. Isto significa que os retransmissores continuam a ser carregados com base no número de mensagens (não operações) e nas horas de retransmissão. Para obter mais informações, consulte a tabela ["Conexões Híbridas e Transmissores WCF"](https://azure.microsoft.com/pricing/details/service-bus/) na página de detalhes de preços.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>E se eu tiver mais de um ouvinte ligado a um retransmissor específico?
Em alguns casos, um único retransmissor pode ter vários ouvintes ligados. Um relé é considerado aberto quando pelo menos um ouvinte está ligado a ele. Adicionar ouvintes a um retransmissor aberto resulta em horas de retransmissão adicionais. O número de remetentes de retransmissão (clientes que invocam ou enviam mensagens para retransmissores) que estão ligados a um relé não afeta o cálculo das horas de retransmissão.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Como é calculado o medidor de mensagens para os Relés WCF?
(Isto**aplica-se apenas aos relés WCF. As mensagens não são um custo para ligações híbridas.**)

Em geral, as mensagens faturadas para retransmissores são calculadas utilizando o mesmo método que é usado para entidades intermediadas (filas, tópicos e subscrições), descrito anteriormente. No entanto, existem algumas diferenças notáveis.

O envio de uma mensagem para um Relé Azure é tratado como um envio "completo" ao ouvinte que recebe a mensagem. Não é tratado como uma operação de envio para o Relé Azure, seguido de uma entrega ao ouvinte. Uma invocação de serviço de estilo de resposta de pedido (até 64 KB) contra um ouvinte transmite resulta em duas mensagens faturadas: uma mensagem faturada para o pedido e uma mensagem de faturação para a resposta (assumindo que a resposta também é de 64 KB ou menor). Isto é diferente de usar uma fila para mediar entre um cliente e um serviço. Se utilizar uma fila para mediar entre um cliente e um serviço, o mesmo padrão de resposta de pedido requer um pedido enviado para a fila, seguido de um dequeue/entrega da fila para o serviço. Isto é seguido por uma resposta enviada para outra fila, e um dequeue/entrega dessa fila para o cliente. Utilizando pressupostos de tamanho igual ao longo (até 64 KB), o padrão de fila mediado resulta em 4 mensagens faturadas. Seria cobrado o dobro do número de mensagens para implementar o mesmo padrão que consegue usando o retransmissor. É claro que há benefícios em usar filas para atingir este padrão, como durabilidade e nivelamento de carga. Estes benefícios podem justificar as despesas adicionais.

Os retransmissores que são abertos utilizando as mensagens de tratamento de ligação **wcf netTCPRelay** não como mensagens individuais, mas como um fluxo de dados que fluem através do sistema. Quando utiliza esta ligação, apenas o remetente e o ouvinte têm visibilidade no enquadramento das mensagens individuais enviadas e recebidas. Para os retransmissores que utilizam a ligação **netTCPRelay,** todos os dados são tratados como um fluxo para calcular mensagens faturadas. Neste caso, a Service Bus calcula a quantidade total de dados enviados ou recebidos através de cada retransmissão individual numa base de 5 minutos. Em seguida, divide essa quantidade total de dados por 64 KB para determinar o número de mensagens faturadas para esse retransmissor durante esse período de tempo.

## <a name="quotas"></a>Quotas
| Nome da quota | Âmbito |  Notas | Valor |
| --- | --- | --- | --- |
| Ouvintes simultâneos num retransmissor |Entidade |Os pedidos subsequentes de ligações adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |25 |
| Ligações de retransmissão simultâneas por todos os pontos finais de retransmissão num espaço de nome de serviço |Espaço de Nomes |- |5000 |
| Retransmitir pontos finais por espaço de nome de serviço |Espaço de Nomes |- |10,000 |
| Tamanho da mensagem para [relés NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) e [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) |Espaço de Nomes |As mensagens recebidas que excedam estas quotas são rejeitadas e uma exceção é recebida pelo código de chamada. |64 KB |
| Tamanho da mensagem para [relés HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) e [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) |Espaço de Nomes |Sem limite no tamanho da mensagem. |Ilimitado |

### <a name="does-relay-have-any-usage-quotas"></a>A Relay tem quotas de utilização?
Por padrão, para qualquer serviço na nuvem, a Microsoft define uma quota de utilização mensal agregada que é calculada em todas as subscrições de um cliente. Entendemos que, por vezes, as suas necessidades podem ultrapassar estes limites. Pode contactar o serviço de apoio ao cliente a qualquer momento, para que possamos compreender as suas necessidades e ajustar esses limites adequadamente. Para o Service Bus, as quotas de utilização agregadas são as seguintes:

* 5 mil milhões de mensagens
* 2 milhões de horas de retransmissão

Embora nos reservemos o direito de desativar uma conta que exceda as suas quotas de utilização mensais, fornecemos uma notificação por e-mail, e fazemos várias tentativas de contactar o cliente antes de tomar qualquer ação. Os clientes que excedam estas quotas são ainda responsáveis por encargos excessivos.

### <a name="naming-restrictions"></a>Restrições de nomeação
Um nome de espaço de retransmissão deve ter entre 6 e 50 caracteres de comprimento.

## <a name="subscription-and-namespace-management"></a>Gestão de subscrição e espaço de nome
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um espaço de nome para outra subscrição do Azure?

Para mover um espaço de nome de uma subscrição Azure para outra subscrição, pode utilizar o [portal Azure](https://portal.azure.com) ou utilizar comandos PowerShell. Para mover um espaço de nome para outra subscrição, o espaço de nomes já deve estar ativo. O utilizador que executa os comandos deve ser um utilizador administrador nas assinaturas de origem e alvo.

#### <a name="azure-portal"></a>Portal do Azure

Para utilizar o portal Azure para migrar espaços de nomeS Azure Relay de uma subscrição para outra subscrição, consulte [os recursos movem-se para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Para utilizar o PowerShell para mover um espaço de nome de uma subscrição do Azure para outra subscrição, utilize a seguinte sequência de comandos. Para executar esta operação, o espaço de nomes já deve estar ativo, e o utilizador que executa os comandos PowerShell deve ser um utilizador administrador tanto nas subscrições de origem como de destino.

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
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Quais são algumas das exceções geradas pelas APIs do Azure Relay e as ações sugeridas que pode tomar?
Para uma descrição de exceções comuns e ações sugeridas que você pode tomar, consulte [exceções de Retransitido][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>O que é uma assinatura de acesso partilhado, e que línguas posso usar para gerar uma assinatura?
As Assinaturas de Acesso Partilhado (SAS) são um mecanismo de autenticação baseado em hashes ou URIs seguros SHA-256. Para obter informações sobre como gerar as suas próprias assinaturas em Node.js, PHP, Python, Java, C e C#, consulte [a autenticação do Service Bus com assinaturas de acesso partilhado][Shared Access Signatures].

### <a name="is-it-possible-to-allow-only-some-relay-endpoints"></a>É possível permitir apenas alguns pontos finais de retransmissão?
Sim. O cliente de retransmissão faz ligações ao serviço Azure Relay utilizando nomes de domínio totalmente qualificados. Os clientes podem adicionar uma entrada para `*.servicebus.windows.net` firewalls que suportam a listagem de aprovação de DNS.

## <a name="next-steps"></a>Passos seguintes
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Começar com .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Nó](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
