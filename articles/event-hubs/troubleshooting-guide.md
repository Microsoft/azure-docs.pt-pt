---
title: Problemas de conectividade de resolução de problemas - Azure Event Hubs Microsoft Docs
description: Este artigo fornece informações sobre problemas de conectividade com os Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b85c0895d1c8f165f494d29013adea014187dd23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87039332"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>Problemas de conectividade de resolução de problemas - Azure Event Hubs
Existem várias razões para as aplicações do cliente não conseguirem ligar-se a um centro de eventos. Os problemas de conectividade que experimenta podem ser permanentes ou transitórios. Se o problema acontecer a toda a hora (permanente), pode querer verificar a cadeia de ligação, as definições de firewall da sua organização, definições de firewall IP, definições de segurança da rede (pontos finais de serviço, pontos finais privados, etc.) e muito mais. Para questões transitórias, o upgrade para a versão mais recente do SDK, executar comandos para verificar pacotes abandonados, e obter vestígios de rede pode ajudar a resolver problemas. 

Este artigo fornece dicas para resolver problemas de conectividade com os Azure Event Hubs. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>Resolver problemas de conectividade permanente
Se a aplicação não for capaz de ligar ao centro de eventos, siga os passos desta secção para resolver o problema. 

### <a name="check-if-there-is-a-service-outage"></a>Verifique se há uma paragem de serviço
Verifique a paragem de serviço do Azure Event Hubs no [site de estado do serviço Azure](https://azure.microsoft.com/status/).

### <a name="verify-the-connection-string"></a>Verifique a cadeia de ligação 
Verifique se o fio de ligação que está a utilizar está correto. Consulte [o fio de ligação](event-hubs-get-connection-string.md) para obter a cadeia de ligação utilizando o portal Azure, CLI ou PowerShell. 

Para os clientes Kafka, verifique se os ficheiros producer.config ou consumer.config estão configurados corretamente. Para mais informações, consulte [Enviar e receber mensagens com Kafka em Event Hubs](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs).

### <a name="check-if-the-ports-required-to-communicate-with-event-hubs-are-blocked-by-organizations-firewall"></a>Verifique se as portas necessárias para comunicar com os Centros de Eventos estão bloqueadas pela firewall da organização
Verifique se as portas utilizadas na comunicação com os Azure Event Hubs não estão bloqueadas na firewall da sua organização. Consulte a tabela seguinte para as portas de saída que precisa de abrir para comunicar com os Azure Event Hubs. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte o [guia de protocolo amQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Ver [Use Event Hubs a partir de aplicações kafka](event-hubs-for-kafka-ecosystem-overview.md)

Aqui está um comando de amostra que verifica se a porta 5671 está bloqueada.

```powershell
tnc <yournamespacename>.servicebus.windows.net -port 5671
```

Em Linux:

```shell
telnet <yournamespacename>.servicebus.windows.net 5671
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Verifique se os endereços IP são permitidos na sua firewall corporativa
Quando está a trabalhar com o Azure, por vezes tem de permitir que intervalos de endereços IP específicos ou URLs na sua firewall corporativa ou procuração acedam a todos os serviços Azure que está a usar ou a tentar utilizar. Verifique se o tráfego é permitido nos endereços IP utilizados pelos Centros de Eventos. Para endereços IP utilizados pelos Azure Event Hubs: consulte [gamas IP Azure e Tags de Serviço - Nuvem Pública](https://www.microsoft.com/download/details.aspx?id=56519).

Além disso, verifique se o endereço IP para o seu espaço de nome é permitido. Para encontrar os endereços IP certos para permitir as suas ligações, siga estes passos:

1. Executar o seguinte comando a partir de um pedido de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Note o endereço IP devolvido em `Non-authoritative answer` . A única altura em que mudaria é se restaurares o espaço de nomes num aglomerado diferente.

Se utilizar a redundância da zona para o seu espaço de nome, tem de fazer alguns passos adicionais: 

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
3. Execute nslookup para cada um com sufixos s1, s2 e s3 para obter os endereços IP dos três casos em execução em três zonas de disponibilidade. 

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Verifique se a etiqueta de serviço AzureEventGrid é permitida nos seus grupos de segurança de rede
Se a sua aplicação estiver a decorrer dentro de uma sub-rede e houver um grupo de segurança de rede associado, confirme se a saída da Internet é permitida ou se é permitida a etiqueta de serviço AzureEventGrid. Consulte [as etiquetas de serviço de rede virtual](../virtual-network/service-tags-overview.md) e `EventHub` procure.

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>Verifique se a aplicação precisa de estar a funcionar numa sub-rede específica de uma rede
Confirme que a sua aplicação está a ser executada numa sub-rede de rede virtual que tem acesso ao espaço de nomes. Se não for, execute a aplicação na sub-rede que tenha acesso ao espaço de nome ou adicione o endereço IP da máquina em que a aplicação está a correr para a [firewall IP](event-hubs-ip-filtering.md). 

Quando cria um ponto final de serviço de rede virtual para um espaço de nome de centro de eventos, o espaço de nomes aceita o tráfego apenas a partir da sub-rede que está ligada ao ponto final de serviço. Há uma exceção a este comportamento. Pode adicionar endereços IP específicos na firewall IP para permitir o acesso ao ponto final público do Event Hub. Para obter mais informações, consulte [os pontos finais do serviço de rede](event-hubs-service-endpoints.md).

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>Verifique as definições ip firewall para o seu espaço de nome
Verifique se o endereço IP público da máquina em que a aplicação está a funcionar não está bloqueado pela firewall IP.  

Por predefinição, os espaços de nomes do Event Hubs estão acessíveis a partir da Internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Encaminhamento).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

As regras de firewall IP são aplicadas ao nível do espaço de nomes do Event Hubs. Por isso, as regras aplicam-se a todas as ligações dos clientes utilizando qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra IP permitida no espaço de nomes Do Event Hubs é rejeitada como não autorizada. A resposta não menciona a regra do IP. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a ação de aceitação ou rejeição.

Para obter mais informações, consulte [as regras de firewall IP configurar para um espaço de nomes Azure Event Hubs](event-hubs-ip-filtering.md). Para verificar se tem problemas de filtragem IP, rede virtual ou cadeia de certificados, consulte [problemas relacionados com a rede de resolução de problemas](#troubleshoot-network-related-issues).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Encontre os endereços IP bloqueados por IP Firewall
Ativar registos de diagnóstico para [eventos de ligação de rede virtual do Event Hubs](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) seguindo instruções nos [registos de diagnóstico Ativar](event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Verá o endereço IP para a ligação que é negada.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>Verifique se o espaço de nome pode ser acedido usando apenas um ponto final privado
Se o espaço de nomes do Event Hubs estiver configurado para ser acessível apenas através de um ponto final privado, confirme que a aplicação do cliente está a aceder ao espaço de nomes sobre o ponto final privado. 

[O serviço Azure Private Link](../private-link/private-link-overview.md) permite-lhe aceder aos Azure Event Hubs sobre um **ponto final privado** na sua rede virtual. Um ponto final privado é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir da sua rede virtual, efetivamente trazendo o serviço para a sua rede virtual. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.

Para obter mais informações, consulte [os pontos finais privados Configure](private-link-service.md). Consulte a Validação de que a secção **de ligação de ponto final privado funciona** para confirmar a utilização de um ponto final privado. 

### <a name="troubleshoot-network-related-issues"></a>Problemas relacionados com a rede de resolução de problemas
Para resolver problemas relacionados com a rede com os Centros de Eventos, siga estes passos: 

Navegue para ou [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Ajuda a verificar se tem problemas de filtragem IP ou rede virtual ou cadeia de certificados (o mais comum na utilização de Java SDK).

Um exemplo de **mensagem bem sucedida:**

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

Um exemplo de mensagem de erro de **falha:**

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>Problemas de conectividade transitória de resolução de problemas
Se estiver a ter problemas de conectividade intermitentes, aceda às seguintes secções para obter dicas de resolução de problemas. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>Use a versão mais recente do cliente SDK
Alguns dos problemas de conectividade transitórios podem ter sido corrigidos nas versões posteriores do SDK do que o que está a usar. Certifique-se de que está a utilizar a versão mais recente dos SDKs clientes nas suas aplicações. Os SDKs são continuamente melhorados com funcionalidades novas/atualizadas e correções de bugs, por isso teste sempre com o pacote mais recente. Verifique as notas de lançamento para obter problemas que sejam corrigidos e funcionalidades adicionadas/atualizadas. 

Para obter informações sobre os SDKs do cliente, consulte o artigo [Azure Event Hubs - Client SDKs.](sdks.md) 

### <a name="run-the-command-to-check-dropped-packets"></a>Executar o comando para verificar pacotes caídos
Quando houver problemas de conectividade intermitentes, verifique o seguinte comando para verificar se existem pacotes abandonados. Este comando tentará estabelecer 25 ligações TCP diferentes a cada 1 segundo com o serviço. Em seguida, pode verificar quantos deles conseguiram/falharam e também ver a latência da ligação TCP. Pode descarregar a ferramenta a `psping` partir [daqui.](/sysinternals/downloads/psping)

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
Pode utilizar comandos equivalentes se estiver a utilizar outras ferramentas `tnc` `ping` como, e assim por diante. 

Obtenha um rastreio de rede se os passos anteriores não ajudarem e analisá-lo usando ferramentas como [o Wireshark](https://www.wireshark.org/). Contacte [o Microsoft Support](https://support.microsoft.com/) se necessário. 

### <a name="service-upgradesrestarts"></a>Atualizações/reinícios de serviços
Problemas de conectividade transitórios podem ocorrer devido a atualizações e reiniciam as atualizações e reiniciam o serviço de backend. Quando ocorrerem, poderá ver os seguintes sintomas: 

- Pode haver uma queda nas mensagens/pedidos de entrada.
- O ficheiro de registo pode conter mensagens de erro.
- As aplicações podem ser desligadas do serviço durante alguns segundos.
- Os pedidos podem ser momentaneamente estrangulados.

Se o código de aplicação utilizar o SDK, a política de retíria já está incorporada e ativa. A aplicação reconectará-se sem impacto significativo na aplicação/fluxo de trabalho. Apanhar estes erros transitórios, recuar e, em seguida, voltar a tentar a chamada, garantirá que o seu código é resistente a estas questões transitórias.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* [Resolver problemas de autenticação e autorização](troubleshoot-authentication-authorization.md)
