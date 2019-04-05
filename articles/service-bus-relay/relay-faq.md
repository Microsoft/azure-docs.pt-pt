---
title: FAQ de reencaminhamento do Azure | Documentos da Microsoft
description: Obtenha respostas para algumas perguntas mais frequentes sobre o reencaminhamento do Azure.
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
ms.openlocfilehash: 2433f4b3563cc8b301d1815cccf5ab24406e8662
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045582"
---
# <a name="azure-relay-faqs"></a>FAQ de reencaminhamento do Azure

Este artigo responde a algumas perguntas mais frequentes (FAQ) sobre [reencaminhamento do Azure](https://azure.microsoft.com/services/service-bus/). Para o Azure preços e de suporte informações, consulte a [FAQs do suporte do Azure](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-relay"></a>O que é o Reencaminhamento do Azure?
O [serviço de reencaminhamento do Azure](relay-what-is-it.md) facilita as aplicações híbridas ao ajudá-lo mais expõem em segurança os serviços que se encontram numa rede empresarial para a cloud pública. Pode expor os serviços sem abrir uma ligação de firewall e sem exigir alterações intrusivas na infraestrutura da rede empresarial.

### <a name="what-is-a-relay-namespace"></a>O que é um espaço de nomes do reencaminhamento?
R [espaço de nomes](relay-create-namespace-portal.md) é um contentor de âmbito que pode usar para recursos de reencaminhamento do endereço na sua aplicação. Tem de criar um espaço de nomes para utilizar o reencaminhamento. Essa é uma das primeiras etapas na introdução.

### <a name="what-happened-to-service-bus-relay-service"></a>O que aconteceu ao serviço de reencaminhamento do Service Bus?
O serviço de reencaminhamento do Service Bus anteriormente com o nome é agora designado [reencaminhamento do WCF](relay-wcf-dotnet-get-started.md). Pode continuar a utilizar este serviço como de costume. A funcionalidade ligações híbridas é uma versão atualizada de um serviço que é foi transplanted dos serviços BizTalk do Azure. Reencaminhamento do WCF e as ligações híbridas continuam a ser suportados.

## <a name="pricing"></a>Preços
Esta secção responde a algumas perguntas mais frequentes sobre o reencaminhamento de estrutura de preços. Também pode ver o [FAQ do suporte do Azure](https://azure.microsoft.com/support/faq/) para o Azure geral informações sobre preços. Para obter informações completas sobre os preços de reencaminhamento, consulte [os detalhes dos preços do Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Como é cobrado para ligações híbridas e de reencaminhamento do WCF?
Para obter informações completas sobre os preços de reencaminhamento, consulte a [ligações híbridas e reencaminhamentos do WCF] [ Pricing overview] tabela no barramento de serviço, a página de detalhes de preços. Além dos preços indicados nessa página, é-lhe cobrada transferências de dados associados para saída fora do datacenter em que seu aplicativo está aprovisionado.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Como me são faturadas as ligações híbridas?
Seguem-se três cenários de faturação de exemplo para ligações híbridas:

*   Cenário 1:
    *   Tem um único serviço de escuta, como uma instância do Gestor de ligações híbridas instalada e em execução contínua durante todo o mês.
    *   Enviar 3 GB de dados na conexão durante o mês. 
    *   O custo total é de US $5.
*   Cenário 2:
    *   Tem um único serviço de escuta, como uma instância do Gestor de ligações híbridas instalada e em execução contínua durante todo o mês.
    *   Enviar 10 GB de dados na conexão durante o mês.
    *   O custo total é 7,50. Isso é US $5 para a ligação e os primeiros 5 GB + us $2.50 para os 5 GB adicional de dados.
*   Cenário 3:
    *   Tem duas instâncias, A e B, do Gestor de ligações híbridas instalada e em execução contínua durante todo o mês.
    *   Enviar 3 GB de dados através de ligação A durante o mês.
    *   Enviar 6 GB de dados através de ligação B durante o mês.
    *   O custo total é de US $10,50. Isso é US $5 para ligação A + US $5 para a ligação B + US $0,50 (para o sexto gigabyte na ligação B).

Tenha em atenção que os preços utilizados nos exemplos apenas são aplicáveis durante o período de pré-visualização de ligações híbridas. Os preços são sujeitos a alteração após disponibilidade geral de ligações híbridas.

### <a name="how-are-hours-calculated-for-relay"></a>Como são calculadas as horas de reencaminhamento?

Reencaminhamento do WCF está disponível apenas em espaços de nomes do escalão Standard. Preços e [quotas de ligação](../service-bus-messaging/service-bus-quotas.md) para reencaminhamentos; caso contrário, não foram alterados. Isso significa que reencaminhamentos continuam a ser cobrado com base no número de mensagens (não operações) e as horas de reencaminhamento. Para obter mais informações, consulte a ["Ligações de híbridas e reencaminhamentos de WCF"](https://azure.microsoft.com/pricing/details/service-bus/) tabela na página de detalhes de preços.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>E se eu tiver mais do que um serviço de escuta ligado a um reencaminhamento específico?
Em alguns casos, um reencaminhamento único pode ter várias escutas ligadas. Um reencaminhamento é considerado aberto quando o serviço de escuta de reencaminhamento de pelo menos uma está ligado à mesma. Adicionar serviços de escuta para resultados uma retransmissão aberta em horas de reencaminhamento adicionais. O número de remetentes de reencaminhamento (clientes que invocam ou enviam mensagens para reencaminhamentos) que estão ligados a um reencaminhamento não afeta o cálculo das horas de reencaminhamento.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Como é calculado o medidor de mensagens para reencaminhamentos do WCF?
(**Isto aplica-se apenas aos reencaminhamentos do WCF. As mensagens não são um custo para ligações híbridas.** )

Em geral, as mensagens a cobrar para reencaminhamentos são calculadas com o mesmo método que é utilizado para entidades mediadas (filas, tópicos e subscrições), descritas anteriormente. No entanto, existem algumas diferenças notáveis.

Enviar uma mensagem para um reencaminhamento do Service Bus é tratado como um "total por meio de" Enviar para o serviço de escuta de reencaminhamento que recebe a mensagem. Não é tratado como uma operação de envio para o reencaminhamento do Service Bus, seguido de uma entrega para o serviço de escuta de reencaminhamento. Uma solicitação-resposta estilo invocação de serviço (de até 64 KB) em relação a um reencaminhamento resultados do serviço de escuta em duas mensagens a cobrar: uma mensagem a cobrar para o pedido e uma mensagem a cobrar pela resposta (supondo que a resposta também é de 64 KB ou mais pequeno). Isso é diferente de utilizar uma fila para mediar entre um cliente e um serviço. Se utilizar uma fila para mediar entre um cliente e um serviço, o mesmo padrão de solicitação-resposta necessita de enviar um pedido para a fila, seguida de uma dequeue/entrega da fila para o serviço. É seguida por um resposta de envio para outra fila e uma entrega/dequeue aquela fila para o cliente. Utilizar as mesmo suposições de tamanho ao longo (até 64 KB), o padrão de fila mediada resulta em 4 mensagens a cobrar. Serão faturados para duas vezes o número de mensagens para implementar o mesmo padrão que realizar utilizando o reencaminhamento. É claro, há benefícios para a utilização de filas para atingir esse padrão, como a durabilidade e nivelamento de carga. Esses benefícios podem justificar o custo adicional.

Reencaminhamentos abertos ao utilizar o **netTCPRelay** associação WCF tratar as mensagens não como mensagens individuais, mas como um fluxo de dados a fluir pelo sistema. Quando utiliza este enlace, apenas o remetente e o serviço de escuta tem visibilidade para o enquadramento das mensagens individuais enviados e recebidos. Para reencaminhamentos que utilizam o **netTCPRelay** todos os dados de ligação, é tratado como um fluxo para calcular as mensagens a cobrar. Neste caso, o Service Bus calcula a quantidade total de dados enviado ou recebido por meio de cada reencaminhamento individual numa base de 5 minutos. Em seguida, divide essa quantidade total de dados por 64 KB para determinar o número de mensagens a cobrar desse reencaminhamento durante esse período de tempo.

## <a name="quotas"></a>Quotas
| Nome da quota | Âmbito |  Notas | Valor |
| --- | --- | --- | --- |
| Serviços de escuta em simultâneo num reencaminhamento |Entidade |Pedidos subsequentes para ligações adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |25 |
| Ligações de reencaminhamento em simultâneo por todos os pontos finais de reencaminhamento num espaço de nomes de serviço |Espaço de nomes |- |5.000 |
| Pontos finais de reencaminhamento por espaço de nomes de serviço |Espaço de nomes |- |10,000 |
| Tamanho da mensagem [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) e [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) retransmite |Espaço de nomes |Mensagens de entrada que excederem estes quotas são rejeitadas e uma exceção é recebida pelo código de chamada. |64 KB |
| Tamanho da mensagem [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) e [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) retransmite |Espaço de nomes |Sem limite no tamanho da mensagem. |Ilimitado |

### <a name="does-relay-have-any-usage-quotas"></a>O reencaminhamento tem quaisquer quotas de utilização?
Por predefinição, para qualquer serviço cloud, a Microsoft define uma quota de utilização mensal agregado que é calculada em todas as subscrições de um cliente. Compreendemos que às vezes suas necessidades podem exceder estes limites. Pode contactar o suporte ao cliente em qualquer altura, para que possamos avaliar as suas necessidades e ajustar estes limites em conformidade. Para o Service Bus, as quotas de utilização de agregação são os seguintes:

* 5 mil milhões de mensagens
* horas de reencaminhamento de 2 milhões

Embora reservamo-no direito de desativar uma conta que exceda as suas quotas de utilização mensais, fornecemos a notificação por email e podemos tornar vários tenta contactar os clientes antes de efetuar qualquer ação. Os clientes que excederem estes quotas são ainda responsáveis por encargos em excesso.

### <a name="naming-restrictions"></a>Restrições de nomenclatura
Um nome de espaço de nomes do reencaminhamento tem de ter entre 6 e 50 carateres de comprimento.

## <a name="subscription-and-namespace-management"></a>Gestão de subscrição e o espaço de nomes
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um espaço de nomes para outra subscrição do Azure?

Para mover um espaço de nomes de uma subscrição do Azure para outra subscrição, pode utilizar o [portal do Azure](https://portal.azure.com) ou utilizar comandos do PowerShell. Para mover um espaço de nomes para outra subscrição, o espaço de nomes já deve estar ativo. O utilizador que executa os comandos tem de ser um utilizador de administrador em subscrições de origem e de destino.

#### <a name="azure-portal"></a>Portal do Azure

Para utilizar o portal do Azure para migrar espaços de nomes do reencaminhamento do Azure a partir de uma subscrição para outra subscrição, veja [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Para utilizar o PowerShell para mover um espaço de nomes de uma subscrição do Azure para outra subscrição, utilize a seguinte sequência de comandos. Para executar esta operação, o espaço de nomes já deve estar ativo e o utilizador que executa os comandos do PowerShell tem de ser um utilizador de administrador em subscrições de origem e de destino.

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
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Quais são algumas das exceções geradas pelas APIs de reencaminhamento do Azure e sugerido ações que pode realizar?
Para obter uma descrição de exceções comuns e ações sugeridas, que pode realizar, consulte [exceções de reencaminhamento][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>O que é uma assinatura de acesso partilhado e que idiomas posso utilizar para gerar uma assinatura?
Assinaturas de acesso partilhado (SAS) são um mecanismo de autenticação com base nos hashes de seguros de SHA-256 ou URIs. Para obter informações sobre como gerar seus próprios assinaturas no nó, PHP, Java, C e c#, veja [autenticação do Service Bus com assinaturas de acesso partilhado][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>É possível para pontos finais de reencaminhamento de lista de permissões?
Sim. O cliente de reencaminhamento faz ligações para o serviço de reencaminhamento do Azure, utilizando nomes de domínio completamente qualificado. Os clientes podem adicionar uma entrada para `*.servicebus.windows.net` nos firewalls que suportam listas de permissões de DNS.

## <a name="next-steps"></a>Passos Seguintes
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
