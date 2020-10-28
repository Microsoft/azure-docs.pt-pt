---
title: Problemas de conectividade da rede de resolução de problemas - Azure Event Grid Microsoft Docs
description: Este artigo fornece informações sobre problemas de conectividade da rede de resolução de problemas com a Azure Event Grid.
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: 7b93d7a110889192bb5be6fffa56a73758d6faa2
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892320"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Problemas de conectividade resolução de problemas - Azure Event Grid

Existem várias razões para as aplicações do cliente não conseguirem ligar-se a um tópico/domínio da Grade de Eventos. Os problemas de conectividade que experimenta podem ser permanentes ou transitórios. Se o problema acontecer a toda a hora (permanente), é melhor verificar as definições de firewall da sua organização, definições de firewall IP, tags de serviço, pontos finais privados e muito mais. Para problemas transitórios, executar comandos para verificar pacotes abandonados, e obter vestígios de rede pode ajudar a resolver problemas.

Este artigo fornece dicas para resolver problemas de conectividade com a Azure Event Grid.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Resolver problemas de conectividade permanente

Se a aplicação não for capaz de ligar à grelha de eventos, siga os passos desta secção para resolver o problema.

### <a name="check-if-theres-a-service-outage"></a>Verifique se há uma paragem de serviço

Verifique a falha de serviço da Grelha de Eventos Azure no [site de estado do serviço Azure](https://azure.microsoft.com/status/).

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Verifique se as portas necessárias para comunicar com a Grade de Eventos não estão bloqueadas pela firewall da organização

Verifique se as portas utilizadas na comunicação com a Azure Event Grid não estão bloqueadas na firewall da sua organização. Consulte a tabela seguinte para as portas de saída que precisa de abrir para comunicar com a Azure Event Grid.

| Protocolo | Portas |
| -------- | ----- |
| HTTPS    | 443   |

Aqui está um comando de amostra que verifica se a porta 443 está bloqueada.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Em Linux:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Verifique se os endereços IP são permitidos na sua firewall corporativa

Quando está a trabalhar com o Azure, por vezes tem de permitir que intervalos de endereços IP específicos ou URLs na sua firewall corporativa ou procuração acedam a todos os serviços Azure que está a usar ou a tentar utilizar. Verifique se o tráfego é permitido nos endereços IP utilizados pela Grade de Eventos. Para endereços IP utilizados pela Azure Event Grid: ver [Gamas IP Azure e Tags de Serviço - Cloud e](https://www.microsoft.com/download/details.aspx?id=56519) Service tag público - [AzureEventGrid](network-security.md#service-tags).

O [Azure IP Ranges and Service Tags - Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) document também lista endereços IP por **região** . Você pode permitir intervalos de endereços para a **região do tópico** e a **região emparelhada** na sua firewall corporativa ou proxy. Para uma região emparelhada para uma região, consulte [a continuidade do Negócio e a recuperação de desastres (BCDR): Regiões Emparelhadas Azure](/azure/best-practices-availability-paired-regions). 

> [!NOTE]
> Novos endereços IP podem ser adicionados à etiqueta de serviço AzureEventGrid, embora não seja habitual. Então é bom fazer uma verificação semanal nas etiquetas de serviço.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Verifique se a etiqueta de serviço AzureEventGrid é permitida nos seus grupos de segurança de rede

Se a sua aplicação estiver a decorrer dentro de uma sub-rede e se houver um grupo de segurança de rede associado, confirme se uma saída de internet é permitida ou se é permitida a etiqueta de serviço AzureEventGrid. Ver [Etiquetas de Serviço](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>Consulte as definições de Firewall IP para o seu Tópico/Domínio

Verifique se o endereço IP público da máquina em que a aplicação está a ser executada não está bloqueado pela firewall IP tópico/domínio do EventGrid.

Por predefinição, os tópicos/domínios da Grelha de Eventos estão acessíveis a partir da Internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Encaminhamento).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

As regras de firewall IP são aplicadas ao nível tópico/domínio da Grade de Eventos. Por isso, as regras aplicam-se a todas as ligações dos clientes utilizando qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra de IP permitida no tópico/domínio da Grelha de Eventos é rejeitada como proibida. A resposta não menciona a regra do IP.

Para obter mais informações, consulte [as regras de firewall IP configurar para um tópico/domínio da Grelha de Eventos Azure](configure-firewall.md).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Encontre os endereços IP bloqueados por IP Firewall

Ativar registos de diagnóstico para tópico/domínio de Grelha de [Eventos Ativar os registos de diagnóstico](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic). Verá o endereço IP para a ligação que é negada.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Verifique se o tópico/domínio EventGrid pode ser acedido usando apenas um ponto final privado

Se o tópico/domínio da Grade de Eventos estiver configurado para ser acessível apenas através de um ponto final privado, confirme que a aplicação do cliente está a aceder ao tópico/domínio sobre o ponto final privado. Para o confirmar, verifique se a aplicação do cliente está a decorrer dentro de uma sub-rede e existe um ponto final privado para o tópico/domínio da Grade de Eventos nessa sub-rede.

[O serviço Azure Private Link](../private-link/private-link-overview.md) permite-lhe aceder à Azure Event Grid sobre um **ponto final privado** na sua rede virtual. Um ponto final privado é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.

Para obter mais informações, consulte [os pontos finais privados Configure](configure-private-endpoints.md).

## <a name="troubleshoot-transient-connectivity-issues"></a>Problemas de conectividade transitória de resolução de problemas

Se estiver a ter problemas de conectividade intermitentes, aceda às seguintes secções para obter dicas de resolução de problemas.

### <a name="run-the-command-to-check-dropped-packets"></a>Executar o comando para verificar pacotes caídos

Quando houver problemas de conectividade intermitentes, verifique o seguinte comando para verificar se existem pacotes abandonados. Este comando tentará estabelecer 25 ligações TCP diferentes a cada 1 segundo com o serviço. Em seguida, pode verificar quantos deles conseguiram/falharam e também ver a latência da ligação TCP. Pode descarregar a ferramenta a `psping` partir [daqui.](/sysinternals/downloads/psping)

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Pode utilizar comandos equivalentes se estiver a utilizar outras ferramentas, como `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php).

Obtenha um rastreio de rede se os passos anteriores não ajudarem e analisá-lo usando ferramentas como [o Wireshark](https://www.wireshark.org/). Contacte [o Microsoft Support](https://support.microsoft.com/) se necessário.

### <a name="service-upgradesrestarts"></a>Atualizações/reinícios de serviços

Problemas de conectividade transitórios podem ocorrer devido a atualizações e reiniciam as atualizações e reiniciam o serviço de backend. Quando ocorrerem, poderá ver os seguintes sintomas:

- Pode haver uma queda nas mensagens/pedidos de entrada.
- O ficheiro de registo pode conter mensagens de erro.
- As aplicações podem ser desligadas do serviço durante alguns segundos.
- Os pedidos podem ser momentaneamente estrangulados.

Apanhar estes erros transitórios, recuar e, em seguida, voltar a tentar a chamada, garantirá que o seu código é resistente a estas questões transitórias.

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda, publique o seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [apoio.](https://azure.microsoft.com/support/options/)
