---
title: Registos de diagnóstico para serviço de sinalização Azure
description: Aprenda a configurar registos de diagnóstico para o Serviço De Sinalização Azure e como utilizá-lo para auto-resolução de problemas.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 72f57ba4bbbbde07f6d26edc88c158f301ebe2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536739"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Registos de diagnóstico para serviço de sinalização Azure

Este tutorial cobre o que são registos de diagnóstico para o Serviço de Sinalização Azure e como configurar registos de diagnóstico e como resolver problemas com registos de diagnóstico.

## <a name="prerequisites"></a>Pré-requisitos
Para ativar os registos de diagnóstico, necessitará de um lugar para armazenar os seus dados de registo. Este tutorial utiliza o Azure Storage e o Log Analytics.

* [Armazenamento azure](../azure-monitor/platform/resource-logs-collect-storage.md) - Retém registos de diagnóstico para auditoria de políticas, análise estática ou backup.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) - Uma ferramenta flexível de pesquisa de registos e análise que permite a análise de troncos brutos gerados por um recurso Azure.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Configurar registos de diagnóstico para um serviço de sinalização Azure

Pode ver registos de diagnóstico para o Serviço De Sinalização Azure. Estes registos proporcionam uma visão mais rica da conectividade com a sua instância de Serviço De Sinalização Azure. Os registos de diagnóstico fornecem informações detalhadas de todas as ligações. Por exemplo, informações básicas (ID do utilizador, id de ligação e tipo de transporte e assim por diante) e informações do evento (conectar, desligar e abortar evento e assim por diante) da ligação. Os registos de diagnóstico podem ser utilizados para identificação de problemas, rastreio de ligação e análise.

### <a name="enable-diagnostic-logs"></a>Ativar registos de diagnóstico

Os registos de diagnóstico são desativados por defeito. Para ativar os registos de diagnóstico, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)sob **monitorização,** clique em **definições de diagnóstico.**

    ![Pane navegação para configurações de diagnóstico](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Em seguida, clique em **Adicionar definição de diagnóstico**.

    ![Adicione registos de diagnóstico](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Detete o alvo de arquivo que quiser. Atualmente, apoiamos o **Archive numa conta** de armazenamento e **enviamos para Log Analytics.**

1. Selecione os registos que pretende arquivar.

    ![Painel de definições de diagnóstico](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Guarde as novas definições de diagnóstico.

As novas definições têm efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo de arquivo configurado, no painel de registos de **diagnóstico.**

Para obter mais informações sobre a configuração dos diagnósticos, consulte a [visão geral dos registos de diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="diagnostic-logs-categories"></a>Categorias de registos de diagnóstico

O Serviço De Sinalização Azure captura registos de diagnóstico numa categoria:

* **Todos os Registos**: Rastreie as ligações que ligam ao Serviço De Sinalização Azure. Os registos fornecem informações sobre a ligação/desconexão, autenticação e estrangulamento. Para mais informações, consulte a secção seguinte.

### <a name="archive-to-a-storage-account"></a>Arquivar numa conta de armazenamento

Os registos são armazenados na conta de armazenamento que configura do painel de registos de **diagnóstico.** Um recipiente `insights-logs-alllogs` nomeado é criado automaticamente para armazenar registos de diagnóstico. No interior do recipiente, os registos são armazenados no ficheiro `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`. Basicamente, o caminho `resource ID` `Date Time`é combinado por e . Os ficheiros de `hour`registo são divididos por . Portanto, os minutos `m=00`sempre são.

Todos os registos são armazenados no formato JavaScript Object Notation (JSON). Cada entrada tem campos de cordas que utilizam o formato descrito nas seguintes secções.

As cordas JSON de registo de arquivo incluem elementos listados nas seguintes tabelas:

**Formato**

Nome | Descrição
------- | -------
hora | Hora do evento de log
nível | Nível de evento de log
resourceId | Id de recurso do seu serviço De Sinalização Azure
localização | Localização do seu Serviço De Sinalização Azure
categoria | Categoria do evento de registo
operationName | Nome de operação do evento
callerIpAddress | Endereço IP do seu servidor/cliente
propriedades | Propriedades detalhadas relacionadas com este evento de log. Para mais detalhes, consulte a tabela de propriedades abaixo

**Tabela de Propriedades**

Nome | Descrição
------- | -------
tipo | Tipo de evento de registo. Atualmente, fornecemos informações sobre conectividade com o Serviço De Sinalização Azure. Só `ConnectivityLogs` o tipo está disponível
coleção | Coleção do evento de log. Os valores `Connection` `Authorization` permitidos são: , e`Throttling`
conexãoId | Identidade da ligação
transporteTipo | Tipo de transporte da ligação. Os valores `Websockets` \| `ServerSentEvents` \| permitidos são:`LongPolling`
conexãoTipo | Tipo de ligação. Os valores `Server` \| `Client`permitidos são: . `Server`: ligação do lado do servidor; `Client`: ligação do lado do cliente
userId | Identidade do utilizador
message | Mensagem detalhada do evento de log

O seguinte código é um exemplo de uma cadeia JSON de registo de arquivo:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Arquivar logs schema para Log Analytics

Para ver os registos de diagnóstico, siga estes passos:

1. Clique `Logs` no seu alvo Log Analytics.

    ![Item do menu Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Introduza e selecione `SignalRServiceDiagnosticLogs` intervalo de tempo para consultar registos de diagnóstico. Para consultas avançadas, consulte [Iniciar com Log Analytics no Monitor Azure](../azure-monitor/log-query/get-started-portal.md)

    ![Consulta log in Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

As colunas de registo de arquivo incluem elementos listados na tabela seguinte:

Nome | Descrição
------- | ------- 
TimeGenerated | Hora do evento de log
Coleção | Coleção do evento de log. Os valores `Connection` `Authorization` permitidos são: , e`Throttling`
OperationName | Nome de operação do evento
Localização | Localização do seu Serviço De Sinalização Azure
Nível | Nível de evento de log
CallerIpAddress | Endereço IP do seu servidor/cliente
Mensagem | Mensagem detalhada do evento de log
IDUtilizador | Identidade do utilizador
Ligação | Identidade da ligação
Tipo de Ligação | Tipo de ligação. Os valores `Server` \| `Client`permitidos são: . `Server`: ligação do lado do servidor; `Client`: ligação do lado do cliente
Tipo de transporte | Tipo de transporte da ligação. Os valores `Websockets` \| `ServerSentEvents` \| permitidos são:`LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Resolução de problemas com registos de diagnóstico

Para resolver problemas para o Serviço De Sinalização Azure, pode ativar registos laterais do servidor/cliente para capturar falhas. Atualmente, o Serviço De Sinalização Azure expõe registos de diagnóstico, podendo também ativar registos para o lado do serviço.

Ao encontrar uma ligação inesperada de crescimento ou queda de situação, pode aproveitar os registos de diagnóstico para resolver problemas.

As questões típicas são muitas vezes sobre as alterações inesperadas da quantidade de ligações, as ligações atingem limites de ligação e falha de autorização. Veja as próximas secções sobre como resolver problemas.

#### <a name="unexpected-connection-number-changes"></a>Alterações inesperadas do número de ligação

##### <a name="unexpected-connection-dropping"></a>Queda inesperada da ligação

Se encontrar ligações inesperadas, primeiro ative os registos nos lados do serviço, do servidor e do cliente.

Se uma ligação se desligar, os registos de diagnóstico `ConnectionAborted` `ConnectionEnded` gravarão este evento de desconexão, verá ou entrará `operationName`.

A diferença `ConnectionAborted` `ConnectionEnded` entre `ConnectionEnded` e é que é uma desconexão esperada que é desencadeada pelo lado do cliente ou do servidor. Embora `ConnectionAborted` seja geralmente um evento inesperado de queda `message`de ligação, e a razão de abortar será fornecida em .

As razões de aborto estão listadas na seguinte tabela:

Razão | Descrição
------- | ------- 
Contagem de ligação atinge limite | A contagem de ligação atinge o limite do seu nível de preço atual. Considere a unidade de serviço de escala
Servidor de aplicações fechou a ligação | O servidor de aplicações despoleta o aborto. Pode ser considerado como um aborto esperado
Intervalo de ping de ligação | Normalmente é causado por problemas de rede. Considere verificar a disponibilidade do seu servidor de aplicações a partir da internet
Recarregamento de serviço, religação | O Serviço de Sinalização Azure está a recarregar. O Azure SignalR suporta a religação automática, pode esperar até voltar a ligar ou voltar a ligar manualmente ao Serviço de Sinalização Azure
Erro transitório do servidor interno | Erro transitório ocorre no Serviço De Sinalização Azure, deve ser recuperado automaticamente
Ligação do servidor caiu | A ligação do servidor cai com erro desconhecido, considere a auto-resolução de problemas com o registo lateral do serviço/servidor/cliente primeiro. Tente excluir questões básicas (por exemplo, problema de rede, problema lateral do servidor de aplicações e assim por diante). Se o problema não for resolvido, contacte-nos para mais ajuda. Para mais informações, consulte a secção [de ajuda.](#get-help) 

##### <a name="unexpected-connection-growing"></a>Crescimento inesperado da ligação

Para resolver problemas sobre o crescimento inesperado da ligação, a primeira coisa que precisa de fazer é filtrar as ligações extra. Pode adicionar identificação única do utilizador de teste à sua ligação ao cliente de teste. Em seguida, verifique-o com registos de diagnóstico, se você vê mais de um cliente conexões têm o mesmo ID ou IP do utilizador de teste, então é provável que o lado do cliente crie e estabeleça mais ligações do que o esperado. Verifique o lado do seu cliente.

#### <a name="authorization-failure"></a>Falha na autorização

Se receber 401 não autorizados devolvidos para pedidos de clientes, verifique os seus registos de diagnóstico. Se encontrar, `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`significa que todos os espectadores no seu sinal de acesso são inválidos. Tente utilizar o público válido sugerido no registo.


#### <a name="throttling"></a>Limitação

Se verificar que não pode estabelecer ligações ao cliente SignalR ao Serviço De Sinalização Azure, verifique os seus registos de diagnóstico. Se encontrar `Connection count reaches limit` no registo de diagnóstico, estabelece demasiadas ligações ao SignalR Service, que atingem o limite de contagem de ligação. Considere escalonar o seu Serviço de Sinalização. Se encontrar `Message count reaches limit` no registo de diagnóstico, significa que utiliza o nível livre e utiliza a quota de mensagens. Se quiser enviar mais mensagens, considere mudar o seu Serviço SignalR para um nível padrão para enviar mensagens adicionais. Para mais informações, consulte [o Preço do Serviço De Sinalização Azure](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Obter ajuda

Recomendamos que se desloque os problemas sozinho primeiro. A maioria dos problemas são causados por problemas de servidor de aplicações ou de rede. Siga o guia de resolução de [problemas com registo de diagnóstico](#troubleshooting-with-diagnostic-logs) e guia básico de tiro de [problemas](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) para encontrar a causa principal.
Se o problema ainda não puder ser resolvido, considere abrir um problema no GitHub ou criar bilhete no Portal Azure.
Fornecer:
1. Intervalo de tempo cerca de 30 minutos quando o problema ocorre
2. ID de recurso do Serviço De Sinalização Azure
3. Emitem detalhes, o mais específicopossível: Por exemplo, o servidor de aplicações não envia mensagens, quedas de ligação ao cliente e assim por diante
4. Registos recolhidos do lado do servidor/cliente, e outros materiais que possam ser úteis
5. [Opcional] Código repro

> [!NOTE]
> Se abrir um problema no GitHub, mantenha as suas informações sensíveis (por exemplo, ID de recursos, registos de servidor/cliente) privadas, apenas envie para membros da organização da Microsoft em privado.