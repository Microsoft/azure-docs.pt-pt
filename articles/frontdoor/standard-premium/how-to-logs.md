---
title: Azure Porta Frontal Standard/Premium (Pré-visualização) Registo
description: Este artigo explica como funciona o registo de madeira no Azure Front Door Standard/Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2020
ms.author: duau
ms.openlocfilehash: 6a1cf3112cd936ec842c755eb90b2c7b094aa781
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099411"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Azure Porta Frontal Standard/Premium (Pré-visualização) Registo

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

A Azure Front Door fornece diferentes registos para ajudá-lo a rastrear, monitorizar e depurar a porta da frente. 

* Os registos de acesso têm informações detalhadas sobre todos os pedidos que a AFD recebe e ajudam-no a analisar e monitorizar padrões de acesso e a depurar problemas. 
* Os registos de atividade dão visibilidade às operações es feitas nos recursos da Azure.  
* Os registos da Sonda de Saúde fornecem os registos de todas as sondas falhadas até à sua origem. 
* Os registos web application firewall (WAF) fornecem informações detalhadas sobre pedidos que são registados através do modo de deteção ou prevenção de um ponto final da Porta Frontal Azure. Um domínio personalizado que é configurado com WAF também pode ser visto através destes registos.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os registos de acesso, os registos de sondas de saúde e os registos WAF não são ativados por padrão. Utilize os passos abaixo para permitir a exploração madeireira. As entradas de registos de atividades são recolhidas por predefinição e pode visualizá-las no portal do Azure. Os registos podem ter atrasos até alguns minutos. 

Tem três opções para armazenar os registos: 

* **Conta de armazenamento:** As contas de armazenamento são melhor utilizadas para cenários quando os registos são armazenados por uma duração mais longa e revistos quando necessário. 
* **Centros de eventos:** Os centros de eventos são uma ótima opção para se integrar com outras ferramentas de informação de segurança e gestão de eventos (SIEM) ou lojas de dados externas. Por exemplo: Splunk/DataDog/Sumo. 
* **Azure Log Analytics:** O Azure Log Analytics em Azure Monitor é melhor utilizado para monitorização e análise em tempo real geral do desempenho da porta frontal do Azure.

## <a name="configure-logs"></a>Configure diários

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Procure por Azure Front Door Standard/Premium e selecione o perfil da porta frontal Azure.

1. No perfil, vá a **Monitoring**, selecione **Definição de Diagnóstico**. Selecione **Adicionar definição de diagnóstico**.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Screenshot da página de aterragem de definições de diagnóstico.":::

1. Nas **definições de Diagnóstico, insira** um nome para  **o nome de definições de diagnóstico**.

1. Selecione o  **registo** de **FrontDoorAccessLog,** **FrontDoorHealthProbeLog** e **FrontDoorWebApplicationFirewallLog**.

1. Selecione os **detalhes do Destino.** As opções de destino são: 

    * **Enviar para o Log Analytics**
        * Selecione o espaço de trabalho *subscrição* e *Log Analytics*.
    * **Arquivar para uma conta de armazenamento**
        * Selecione a *Subscrição* e a *Conta de Armazenamento.* e definir **Retenção (dias)**.
    * **Transmitir em fluxo para um hub de eventos**
        * Selecione o espaço de nome do centro de *eventos, o nome do hub do evento (opcional)* e *o nome da política do centro de eventos*. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Screenshot da página de definições de diagnóstico.":::

1. Clique em **Guardar**.

## <a name="access-log"></a>Registo de acesso

A Azure Front Door fornece atualmente pedidos individuais de API com cada entrada tendo o seguinte esquema e registado no formato JSON como mostrado abaixo. 

| Propriedade | Descrição |
|----------|-------------| 
| TrackingReference | A cadeia de referência única que identifica um pedido servido pela AFD, também enviada como cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos registos de acesso para um pedido específico. |
| Hora | A data e a hora em que a borda AFD entregou conteúdo solicitado ao cliente (na UTC). |
| HttpMethod | MÉTODO HTTP utilizado pelo pedido: DELETE, GET, HEAD, OPTIONS, PATCH, POST ou PUT. |
| HttpVersion | A versão HTTP que o espectador especificou no pedido. |
| RequestUri | URI do pedido recebido. Este campo é um esquema completo, porta, domínio, caminho e cadeia de consulta |
| Nome do Anfitrião | O nome do anfitrião no pedido do cliente. Se ativar domínios personalizados e tiver domínio wildcard (*.contoso.com), o nome de anfitrião é a.contoso.com. se utilizar o domínio da porta frontal Azure (contoso.azurefd.net), o nome de anfitrião é contoso.azurefd.net. |
| PedidoBytes | O tamanho da mensagem de pedido HTTP em bytes, incluindo os cabeçalhos de pedido e o corpo de pedido. O número de bytes de dados que o espectador incluiu no pedido, incluindo cabeçalhos. |
| RespostaBytes | Bytes enviados pelo servidor backend como resposta. |
| UserAgent | O tipo de navegador que o cliente usou. |
| ClientIp | O endereço IP do cliente que fez o pedido original. Se houve um cabeçalho X-Forwarded-For no pedido, então o IP do Cliente é escolhido do mesmo. |
| SocketIp | O endereço IP da ligação direta à borda AFD. Se o cliente usou um representante HTTP ou um equilibrador de carga para enviar o pedido, o valor do SocketIp é o endereço IP do proxy ou do equilibrador de carga. |
| Latência | O tempo a partir do momento em que o servidor de borda AFD recebe o pedido de um cliente até ao momento em que a AFD envia o último byte de resposta ao cliente, em milissegundos. Este campo não tem em conta a latência da rede e o tampão TCP. |
| PedidoProtocol | O protocolo que o cliente especificou no pedido: HTTP, HTTPS. |
| SecurityProtocol | A versão do protocolo TLS/SSL utilizada pelo pedido ou nula se não houver encriptação. Os valores possíveis incluem: SSLv3, TLSv1, TLSv1.1, TLSv1.2 |
| SegurançaCifra | Quando o valor para Protocolo de Pedido é HTTPS, este campo indica a cifra TLS/SSL negociada pelo cliente e AFD para encriptação. |
| Ponto final | O nome de domínio do ponto final da AFD, por exemplo, contoso.z01.azurefd.net |
| HttpStatusCode | O código de estado HTTP devolvido da AFD. |
| Pop | O pop de borda, que respondeu ao pedido do utilizador.  |
| Estado da Cache | Fornece o código de estado de como o pedido é tratado pelo serviço CDN no que diz respeito ao caching. Os valores possíveis são HIT: O pedido HTTP foi servido a partir da cache POP de borda AFD. <br> **MISS**: O pedido HTTP foi servido de origem. <br/> **PARTIAL_HIT**: Alguns dos bytes de um pedido foram servidos a partir de cache POP de borda AFD, enquanto alguns dos bytes foram servidos de origem para cenários de chunking de objetos. <br> **CACHE_NOCONFIG**: Encaminhamento de pedidos sem configurações de caching, incluindo cenário de bypass. <br/> **PRIVATE_NOSTORE**: Não há cache configurado nas definições de cache pelos clientes. <br> **REMOTE_HIT**: O pedido foi servido por cache de nó de pai. <br/> **N/A**:** Pedido que foi negado por URL assinado e Regras Definidas. |
| Nome matchedRulesSetName | Os nomes das regras que foram processadas. |
| Nome da Rota | O nome da rota que o pedido correspondia. |
| ClientPort | A porta IP do cliente que fez o pedido. |
| Remetente | O URL do site que originou o pedido. |
| TimetoFirstByte | O tempo de tempo em milissegundos da AFD recebe o pedido até ao momento em que o primeiro byte é enviado ao cliente, conforme medido na Porta frontal Azure. Esta propriedade não mede os dados do cliente. |
| ErrorInfo | Este campo fornece informações detalhadas sobre o sinal de erro para cada resposta. <br> **NoError**: Indica que não foi encontrado nenhum erro. <br> **CertificadoError**: Erro genérico do certificado SSL. <br> **CertificadoNameCheckFailed**: O nome de anfitrião no certificado SSL é inválido ou não corresponde. <br> **ClientDisconnected**: Solicitar falha por causa da ligação à rede do cliente. <br> **ClientGeoBlocked**: O cliente foi bloqueado devido à localização geográfica do IP. <br> **Não especificadoClientError**: Erro genérico do cliente. <br> **InvalidRequest**: Pedido inválido. Pode ocorrer por causa de cabeçalho, corpo e URL mal formados. <br> **DNSFailure**: Falha do DNS. <br> **DNSTimeout**: A consulta DNS para resolver o tempo de backend. <br> **DNSNameNotResolved**: O nome ou endereço do servidor não pôde ser resolvido. <br> **OriginConnectionAborted**: A ligação com a origem foi desligada anormalmente. <br> **OriginConnectionError**: Erro de ligação de origem genérica. <br> **OriginConnectionRefused**: A ligação com a origem não foi estabelecida. <br> **OriginError**: Erro de origem genérica. <br> **OriginInvalidRequest**: Foi enviado um pedido inválido à origem. <br> **ResponseHeaderTooBig**: A origem devolveu um cabeçalho de resposta demasiado grande. <br> **OriginInvalidResponse**:** Origin devolveu uma resposta inválida ou não reconhecida. <br> **OriginTimeout**: O prazo de validade do pedido de origem expirou. <br> **ResponseHeaderTooBig**: A origem devolveu um cabeçalho de resposta demasiado grande. <br> **Restrição:** O pedido foi bloqueado devido a um período de inquérito restrito. <br> **SSLHandshakeError**: Incapaz de estabelecer ligação com a origem devido à falha do aperto de mão SSL. <br> **SSLInvalidRootCA**: O RootCA foi inválido. <br> **SSLInvalidCipher**: A cifra foi inválida para a qual foi estabelecida a ligação HTTPS. <br> **OriginConnectionAborted**: A ligação com a origem foi desligada anormalmente. <br> **OriginConnectionRefused**: A ligação com a origem não foi estabelecida. <br> **Não especificadoError**: Ocorreu um erro que não se enquadrava em nenhum dos erros da tabela. |
| OriginURL | O URL completo da origem para onde os pedidos são enviados. Composto pelo esquema, cabeçalho de anfitrião, porta, caminho e cadeia de consulta. <br> **Reescrita de URL**: Se houver uma regra de reescrita de URL no Conjunto de Regras, o caminho refere-se ao caminho reescrito. <br> **Cache na borda POP** Se for um sucesso de cache no pop de borda, a origem é N/A. <br> **Grande pedido** Se o conteúdo solicitado for grande, com vários pedidos de origem, este campo corresponderá ao primeiro pedido à origem. Para mais informações, consulte Object Chunking para obter mais detalhes. |
| OriginIP | O IP de origem que serviu o pedido. <br> **Cache bateu no pop de borda** Se for um sucesso de cache no pop de borda, a origem é N/A. <br> **Grande pedido** Se o conteúdo solicitado for grande, com vários pedidos de origem, este campo corresponderá ao primeiro pedido à origem. Para mais informações, consulte Object Chunking para obter mais detalhes. |
| Nome de Origem| O nome DNS completo (nome de anfitrião na origem URL) até à origem. <br> **Cache bateu no pop de borda** Se for um sucesso de cache no pop de borda, a origem é N/A. <br> **Grande pedido** Se o conteúdo solicitado for grande, com vários pedidos de origem, este campo corresponderá ao primeiro pedido à origem. Para mais informações, consulte Object Chunking para obter mais detalhes. |

## <a name="health-probe-log"></a>Registo de sonda de saúde

Os registos de sondas de saúde fornecem registos de todas as sondas falhadas para ajudá-lo a diagnosticar a sua origem.Os registos fornecer-lhe-ão informações que pode usar para trazer a origem de volta ao serviço. Alguns cenários para os seguintes são úteis para os seguintes:

* Reparaste que o tráfego da Porta frontal Azure foi enviado para algumas das origens. Por exemplo, apenas três em cada quatro origens recebem tráfego. Quer saber se as origens estão a receber sondas e se não a razão da falha.  

* Notou que a origem da saúde % é inferior ao esperado e quer saber qual a origem que falhou e a razão da falha.

### <a name="health-probe-log-properties"></a>Propriedades do registo de sonda de saúde

Cada registo de sonda saúde tem o seguinte esquema.

| Propriedade | Descrição |
| --- | --- |
| HealthProbeId  | Uma identificação única para identificar o pedido. |
| Hora | Tempo completo da sonda |
| HttpMethod | Método HTTP utilizado pelo pedido da sonda de saúde. Os valores incluem GET e HEAD, com base nas configurações da sonda de saúde. |
| Resultado | Estado da sonda de saúde até origem, valor inclui sucesso e outro texto de erro. |
| HttpStatusCode  | O código de estado HTTP devolvido da origem. |
| ProbeURL (alvo) | O URL completo da origem para onde os pedidos são enviados. Composto pelo esquema, cabeçalho de anfitrião, caminho e cadeia de consulta. |
| Nome de Origem  | A origem para o envio de pedidos. Este campo ajuda a localizar as origens de interesse se a origem estiver configurada para FDQN. |
| POP | O pop da borda, que enviou o pedido da sonda. |
| IP de origem | Origem do alvo IP. Este campo é útil para localizar origens de interesse se configurar a origem usando fDQN. |
| Totolalatency | O tempo a partir da borda AFDX envia o pedido de origem para a origem do tempo envia a última resposta para a borda AFDX. |
| Conexão| Duração do tempo gasto na configuração da ligação TCP para enviar o pedido de sonda HTTP à origem. | 
| Latência de DNSResolution | Duração gasta na resolução DNS se a origem estiver configurada para ser um FDQN em vez de IP. N/A se a origem estiver configurada para IP. |

### <a name="health-probe-log-sample-in-json"></a>Amostra de registo de sonda de saúde em JSON

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Registos de Atividade

Os registos de atividade fornecem informações sobre as operações e feitas no Azure Front Door Standard/Premium. Os registos incluem detalhes sobre o que, quem e quando uma operação de escrita foi feita na Porta frontal de Azure. 

> [!NOTE]
> Os registos de atividade não incluem operações GET. Também não incluem operações que executa utilizando o portal Azure ou a API de Gestão original. 

Aceda aos registos de atividades na porta da frente ou em todos os registos dos seus recursos Azure no Azure Monitor.

Para ver registos de atividades:

1. Selecione o seu perfil da porta da frente.

1. Selecione **registo de atividade.**

1. Escolha um âmbito de filtragem e, em seguida, **selecione Aplicar**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais [relatórios Azure Front Door Standard/Premium (Preview).](how-to-reports.md)
- Saiba mais sobre [as métricas de monitorização em tempo real do Azure Front Door Standard/Premium (Preview).](how-to-monitor-metrics.md)
