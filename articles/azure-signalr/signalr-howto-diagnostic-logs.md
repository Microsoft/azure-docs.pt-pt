---
title: Registos de recursos para o serviço Azure SignalR
description: Saiba como configurar registos de recursos para o Serviço Azure SignalR e como utilizá-lo para resolver problemas próprios.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 5650ff0e039d1e9211b8d0013726e101efdfab78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572262"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Registos de recursos para o Serviço Azure SignalR

Este tutorial discute quais são os registos de recursos do Serviço Azure SignalR, como os configurar e como resolver problemas com eles. 

## <a name="prerequisites"></a>Pré-requisitos
Para ativar registos de recursos, precisará de um lugar para armazenar os seus dados de registo. Este tutorial utiliza a Azure Storage e Log Analytics.

* [Armazenamento Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) - Retém registos de recursos para auditoria de política, análise estática ou backup.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) - Uma ferramenta flexível de pesquisa de registos e análise que permite a análise de troncos crus gerados por um recurso Azure.

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>Configurar registos de recursos para um serviço Azure SignalR

Pode visualizar registos de recursos para o Serviço Azure SignalR. Estes registos proporcionam uma visão mais rica da conectividade à sua instância do Serviço Azure SignalR. Os registos de recursos fornecem informações detalhadas de todas as ligações. Por exemplo, informações básicas (ID do utilizador, iD de ligação e tipo de transporte e assim por diante) e informações sobre eventos (ligar, desligar e abortar evento e assim por diante) da ligação. registos de recursos podem ser usados para identificação de problemas, rastreio de ligação e análise.

### <a name="enable-resource-logs"></a>Ativar registos de recursos

Os registos de recursos são desativadas por predefinição. Para ativar registos de recursos, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)em **Monitorização,** clique nas **definições de Diagnóstico**.

    ![Navegação de painel para configurações de diagnóstico](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Em seguida, clique **em Adicionar a definição de diagnóstico**.

    ![Adicionar registos de recursos](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Desaponte o alvo de arquivo que deseja. Atualmente, apoiamos **o Arquivo numa conta de armazenamento** e **enviamos para registar analíticos.**

1. Selecione os registos que pretende arquivar.

    ![Painel de definições de diagnóstico](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Guarde as novas definições de diagnóstico.

As novas definições fazem efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo de arquivo configurado, no painel de **registos de diagnóstico.**

Para obter mais informações sobre a configuração dos diagnósticos, consulte a [visão geral dos registos de recursos do Azure](../azure-monitor/essentials/platform-logs-overview.md).

### <a name="resource-logs-categories"></a>Categorias de registos de recursos

O Serviço Azure SignalR captura registos de recursos numa única categoria:

* **Todos os Registos**: Ligações de rastreador que se ligam ao Serviço Azure SignalR. Os registos fornecem informações sobre a ligação/desconexão, autenticação e estrangulamento. Para mais informações, consulte a secção seguinte.

### <a name="archive-to-a-storage-account"></a>Arquivar numa conta de armazenamento

Os registos são armazenados na conta de armazenamento que configura no painel **de registos de diagnóstico.** Um contentor nomeado `insights-logs-alllogs` é criado automaticamente para armazenar registos de recursos. Dentro do recipiente, os troncos são armazenados no ficheiro `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` . Basicamente, o caminho é combinado por `resource ID` e `Date Time` . Os ficheiros de registo são divididos por `hour` . Portanto, os minutos são `m=00` sempre.

Todos os registos são armazenados no formato JavaScript Object Notation (JSON). Cada entrada tem campos de cordas que utilizam o formato descrito nas seguintes secções.

As cordas JSON do log de arquivo incluem elementos listados nas seguintes tabelas:

**Formato**

Nome | Descrição
------- | -------
hora | Hora do evento de registo
nível | Nível de evento de registo
resourceId | ID de recursos do seu Serviço Azure SignalR
localização | Localização do seu Serviço Azure SignalR
categoria | Categoria do evento de log
operationName | Nome da operação do evento
callerIpAddress | Endereço IP do seu servidor/cliente
propriedades | Propriedades detalhadas relacionadas com este evento de registo. Para mais detalhes, consulte a tabela de propriedades abaixo

**Tabela de Imóveis**

Nome | Descrição
------- | -------
tipo | Tipo do evento de registo. Atualmente, fornecemos informações sobre conectividade ao Serviço Azure SignalR. O `ConnectivityLogs` único tipo está disponível
coleção | Coleção do evento de registo. Os valores permitidos são: `Connection` e `Authorization``Throttling`
connectionId | Identidade da ligação
Type de transporte | Tipo de transporte da ligação. Os valores permitidos são: `Websockets` \| `ServerSentEvents` \|`LongPolling`
tipo de conexão | Tipo de ligação. Os valores permitidos são: `Server` \| `Client` . `Server`: ligação do lado do servidor; `Client`: ligação do lado do cliente
userId | Identidade do utilizador
message | Mensagem detalhada do evento de log

O seguinte código é um exemplo de uma cadeia JSON de log de arquivo:

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

### <a name="archive-logs-schema-for-log-analytics"></a>Esquema de registos de arquivo para Log Analytics

Para ver registos de recursos, siga estes passos:

1. Clique `Logs` no seu target Log Analytics.

    ![Item do menu Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Introduza `SignalRServiceDiagnosticLogs` e selecione o intervalo de tempo para consultar registos de recursos. Para consultas avançadas, consulte [Começar com Log Analytics no Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

    ![Início de consulta no Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

As colunas de registo de arquivo incluem elementos listados na tabela seguinte:

Nome | Descrição
------- | ------- 
TimeGenerated | Hora do evento de registo
Coleção | Coleção do evento de registo. Os valores permitidos são: `Connection` e `Authorization``Throttling`
OperationName | Nome da operação do evento
Localização | Localização do seu Serviço Azure SignalR
Level | Nível de evento de registo
CallerIpAddress | Endereço IP do seu servidor/cliente
Mensagem | Mensagem detalhada do evento de log
IDUtilizador | Identidade do utilizador
ConnectionId | Identidade da ligação
ConexoType | Tipo de ligação. Os valores permitidos são: `Server` \| `Client` . `Server`: ligação do lado do servidor; `Client`: ligação do lado do cliente
Type de transporte | Tipo de transporte da ligação. Os valores permitidos são: `Websockets` \| `ServerSentEvents` \|`LongPolling`

### <a name="troubleshooting-with-resource-logs"></a>Resolução de problemas com registos de recursos

Para resolver problemas para o Serviço Azure SignalR, pode ativar registos laterais do servidor/cliente para capturar falhas. Atualmente, o Serviço Azure SignalR expõe registos de recursos, podendo também ativar registos para o lado do serviço.

Ao encontrar uma situação inesperada de crescimento ou queda de ligação, pode aproveitar os registos de recursos para resolver problemas.

As questões típicas são muitas vezes sobre as mudanças inesperadas de quantidade das ligações, as ligações atingem os limites de ligação e a falha de autorização. Veja as próximas secções sobre como resolver problemas.

#### <a name="unexpected-connection-number-changes"></a>Alterações inesperadas do número de ligação

##### <a name="unexpected-connection-dropping"></a>Queda inesperada da ligação

Se encontrar ligações inesperadas, em primeiro lugar, ative os registos no serviço, servidor e lado do cliente.

Se uma ligação se desligar, os registos de recursos gravarão este evento de desconexão, verá `ConnectionAborted` ou `ConnectionEnded` entrará `operationName` .

A diferença entre `ConnectionAborted` e é que é uma `ConnectionEnded` `ConnectionEnded` desconexão esperada que é desencadeada pelo lado do cliente ou do servidor. Embora `ConnectionAborted` seja geralmente um evento inesperado de queda de ligação, e a razão de abortar será fornecida em `message` .

As razões do aborto constam da seguinte tabela:

Razão | Description
------- | ------- 
Contagem de ligação atinge limite | A contagem de ligação atinge o limite do seu nível de preço atual. Considere aumentar a unidade de serviço
O servidor de aplicações fechou a ligação | O servidor de aplicações desencadeia o aborto. Pode ser considerado como um aborto esperado
Tempo limite de ping de ligação | Normalmente é causado por problema de rede. Considere verificar a disponibilidade do seu servidor de aplicações a partir da internet
Recarga de serviço, reconectar | O Serviço Azure SignalR está a recarregar. O Azure SignalR suporta a reconexão automática, pode esperar até voltar a ligar ou voltar a ligar manualmente ao Serviço Azure SignalR
Erro transitório do servidor interno | Erro transitório ocorre no Serviço Azure SignalR, deve ser recuperado automaticamente
A ligação do servidor caiu | A ligação do servidor cai com erro desconhecido, considere a resolução de problemas com o registo lateral de serviço/servidor/cliente primeiro. Tente excluir questões básicas (por exemplo, problema de rede, problema lateral do servidor de aplicações e assim por diante). Se o problema não for resolvido, contacte-nos para mais ajuda. Para mais informações, consulte a secção [de ajuda.](#get-help) 

##### <a name="unexpected-connection-growing"></a>Crescimento inesperado da conexão

Para resolver problemas sobre o crescimento inesperado da ligação, a primeira coisa que precisa fazer é filtrar as ligações extra. Pode adicionar iD exclusivo do utilizador de teste à sua ligação ao cliente de teste. Em seguida, verifique-o com registos de recursos, se vir mais de um cliente ter o mesmo ID ou IP do utilizador de teste, então é provável que o lado do cliente crie e estabeleça mais ligações do que o esperado. Verifique o lado do seu cliente.

#### <a name="authorization-failure"></a>Falha na autorização

Se receber 401 devolvidos não autorizados para pedidos de clientes, verifique os seus registos de recursos. Se `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` encontrar, significa que todos os públicos no seu token de acesso são inválidos. Tente usar o público válido sugerido no registo.


#### <a name="throttling"></a>Limitação

Se verificar que não consegue estabelecer ligações do cliente SignalR ao Serviço Azure SignalR, verifique os seus registos de recursos. Se encontrar `Connection count reaches limit` no registo de recursos, estabelece demasiadas ligações ao Serviço SignalR, que atingem o limite da contagem de ligação. Considere aumentar o seu serviço SignalR. Se encontrar `Message count reaches limit` em registo de recursos, significa que usa o nível livre e utiliza a quota de mensagens. Se pretender enviar mais mensagens, considere alterar o serviço SignalR para o nível normal para enviar mensagens adicionais. Para mais informações, consulte [o preço do serviço Azure SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Obter ajuda

Recomendamos que resolva os problemas sozinho primeiro. A maioria dos problemas são causados por problemas de servidor de aplicações ou de rede. Siga o [guia de resolução de problemas com registo de recursos](#troubleshooting-with-resource-logs) e guia básico de tiro a [problemas](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) para encontrar a causa principal.
Se o problema ainda não puder ser resolvido, então considere abrir um problema no GitHub ou criar bilhete no Portal Azure.
Fornecer:
1. Intervalo de tempo cerca de 30 minutos quando o problema ocorre
2. ID de recursos do Serviço Azure SignalR
3. Detalhes de emissão, o mais específico possível: Por exemplo, appserver não envia mensagens, ligação ao cliente cai e assim por diante
4. Registos recolhidos do lado do servidor/cliente, e outros materiais que podem ser úteis
5. [Opcional] Código Repro

> [!NOTE]
> Se abrir o problema no GitHub, mantenha as suas informações sensíveis (por exemplo, ID de recursos, registos de servidores/clientes) privadas, apenas envie para membros da organização da Microsoft em privado.