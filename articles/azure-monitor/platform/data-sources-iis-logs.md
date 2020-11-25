---
title: Recolher registos IIS com agente Log Analytics no Azure Monitor
description: O Serviço de Informação da Internet (IIS) armazena a atividade do utilizador em ficheiros de registo que podem ser recolhidos pelo Azure Monitor.  Este artigo descreve como configurar a recolha de registos IIS e detalhes dos registos que criam no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2020
ms.openlocfilehash: a089631ab199b0fe997bba001561c6b027034e2c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993691"
---
# <a name="collect-iis-logs-with-log-analytics-agent-in-azure-monitor"></a>Recolher registos IIS com agente Log Analytics no Azure Monitor
O Serviço de Informação da Internet (IIS) armazena a atividade do utilizador em ficheiros de registo que podem ser recolhidos pelo agente Log Analytics e armazenados em [Registos do Monitor Azure](data-platform.md).

> [!IMPORTANT]
> Este artigo abrange a recolha de registos IIS com o [agente Log Analytics,](log-analytics-agent.md) que é um dos agentes utilizados pelo Azure Monitor. Outros agentes recolhem dados diferentes e são configurados de forma diferente. Consulte [a visão geral dos agentes do Azure Monitor](agents-overview.md) para obter uma lista dos agentes disponíveis e os dados que podem recolher.

![Registos do IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configurar registos IIS
O Azure Monitor recolhe entradas a partir de ficheiros de registo criados pelo IIS, pelo que deve [configurar o IIS para registar](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11)).

O Azure Monitor suporta apenas ficheiros de registo IIS armazenados em formato W3C e não suporta campos personalizados ou Registo Avançado IIS. Não recolhe registos em formato NCSA ou IIS.

Configurar registos IIS no Monitor Azure a partir do [menu Definições Avançadas](agent-data-sources.md#configuring-data-sources) para o agente Log Analytics.  Não é necessária outra configuração que não seja a seleção de **ficheiros de registo IIS do formato Collect W3C**.


## <a name="data-collection"></a>Recolha de dados
O Azure Monitor recolhe entradas de registo IIS de cada agente sempre que o selo de tempo de registo muda. O registo é lido a cada **5 minutos.** Se, por qualquer razão, o IIS não atualizar o relógio antes da hora de transição quando um novo ficheiro for criado, as entradas serão recolhidas após a criação do novo ficheiro. A frequência da nova criação de ficheiros é controlada pela definição de **Agenda de Rollover de Ficheiros de Registo** para o site IIS, que é uma vez por padrão por padrão. Se a regulação for **de hora em hora,** o Azure Monitor recolhe o registo a cada hora. Se a definição for **diária,** o Azure Monitor recolhe o registo a cada 24 horas.

> [!IMPORTANT]
> Recomenda-se definir o **calendário de capotamento** do ficheiro de registo para **hora a hora**. Se estiver definido para **o Daily,** poderá experimentar picos nos seus dados, uma vez que estes serão recolhidos apenas uma vez por dia.

## <a name="iis-log-record-properties"></a>Propriedades de registo de registo de registo de registo do IIS
Os registos do IIS têm um tipo de **W3CIISLog** e têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Nome do computador do evento de onde o evento foi recolhido. |
| cIP |Endereço IP do cliente. |
| csMethod |Método do pedido, como GET ou POST. |
| csReferer |Site que o utilizador seguiu um link do site atual. |
| csUserAgent |Tipo de navegador do cliente. |
| nome csUser |Nome do utilizador autenticado que acedeu ao servidor. Os utilizadores anónimos são indicados por um hífen. |
| csUriStem |Alvo do pedido, como uma página web. |
| csUriQuery |Pergunta, se houver, que o cliente estava a tentar realizar. |
| ManagementGroupName |Nome do grupo de gestão de agentes do Gestor de Operações.  Para outros agentes, este é o AOI...\<workspace ID\> |
| RemoteIPCountry |País/região do endereço IP do cliente. |
| RemoteIPLatitude |Latitude do endereço IP do cliente. |
| RemoteIPLongitude |Longitude do endereço IP do cliente. |
| scStatus |Código de estado HTTP. |
| scSubStatus |Código de erro do substatus. |
| scWin32Status |Código de estado do Windows. |
| sIP |Endereço IP do servidor web. |
| SourceSystem |OpsMgr |
| sPort |Ver no servidor ao que o cliente ligou. |
| sSiteName |Nome do site do IIS. |
| TimeGenerated |Data e hora a entrada foi registada. |
| TimeTaken |Duração do tempo para processar o pedido em milissegundos. |

## <a name="log-queries-with-iis-logs"></a>Registar consultas com registos IIS
A tabela seguinte fornece diferentes exemplos de consultas de registo que recuperam registos de registos IIS.

| Consulta | Descrição |
|:--- |:--- |
| W3CIISLog |Todos os registos do IIS. |
| W3CIISLog &#124; onde scStatus==500 |Todos os registos do IIS com um estado de devolução de 500. |
| W3CIISLog &#124; resumir a contagem() por cIP |Contagem de entradas de registo IIS por endereço IP do cliente. |
| W3CIISLog &#124; onde csHost=="www \. contoso.com" &#124; resumir a contagem() por csUriStem |Contagem de entradas de registo IIS por URL para o anfitrião www \. contoso.com. |
| W3CIISLog &#124; resumir a soma (csBytes) por computador &#124; take 500000 |Total de bytes recebidos por cada computador IIS. |

## <a name="next-steps"></a>Passos seguintes
* Configure O Monitor Azure para recolher [outras fontes de dados](agent-data-sources.md) para análise.
* Saiba mais [sobre consultas de registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções.
