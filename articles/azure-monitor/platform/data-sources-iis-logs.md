---
title: Registo do IIS no Azure Monitor | Documentos da Microsoft
description: Serviços de informação Internet (IIS) armazena a atividade do utilizador nos ficheiros de registo que podem ser coletados pelo Azure Monitor.  Este artigo descreve como configurar a coleção de registos do IIS e os detalhes dos registos que criaram no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: 94bdc0670fb63b1b4c306a5af3357f437784338c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007927"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Recolher registos do IIS no Azure Monitor
Serviços de informação Internet (IIS) armazena a atividade do utilizador nos ficheiros de registo que podem ser recolhidos pelo Azure Monitor e armazenados como [registos de dados](data-collection.md).

![Registos do IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Registos de configuração do IIS
Monitor do Azure recolhe as entradas de ficheiros de registo criados pelo IIS, assim, tem [configurar o IIS para o registo de](https://technet.microsoft.com/library/hh831775.aspx).

O Azure Monitor só suporta ficheiros de registo do IIS armazenados no formato W3C e não suporta campos personalizados ou avançados o registo do IIS. Não recolhe registos no formato nativo NCSA ou IIS.

Configurar registos de IIS no Monitor e do Azure a partir da [menu de definições avançadas](agent-data-sources.md#configuring-data-sources).  É necessária nenhuma configuração que selecionar **ficheiros de registo IIS de formato W3C</a&gt recolher**.


## <a name="data-collection"></a>Recolha de dados
Monitor do Azure recolhe entradas de registo IIS de cada agente que é criada sempre que o log esteja fechado e um novo. Esta frequência é controlada pelos **agenda de Rollover de ficheiro de registo** definição para o site do IIS que é uma vez por dia por predefinição. Por exemplo, se as definições são **por hora**, em seguida, o Azure Monitor irá recolher o registo de cada hora.  Se a definição for **diária**, em seguida, o Azure Monitor irá recolher o registo a cada 24 horas.


## <a name="iis-log-record-properties"></a>Propriedades de registo do registo IIS
Registros de log do IIS têm um tipo de **W3CIISLog** e ter as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Nome do computador que o evento foi recolhido a partir de. |
| cIP |Endereço IP do cliente. |
| csMethod |Método do pedido, como GET ou POST. |
| csReferer |Site que o utilizador seguida de uma ligação de para o site atual. |
| csUserAgent |Tipo de navegador do cliente. |
| csUserName |Nome do utilizador autenticado que acedeu ao servidor. Utilizadores anónimos são indicados por um hífen. |
| csUriStem |Destino do pedido, como uma página da web. |
| csUriQuery |Consulte, se houver, o cliente estava a tentar realizar. |
| ManagementGroupName |Nome do grupo de gestão para agentes do Operations Manager.  Para outros agentes, é AOI -\<ID da área de trabalho\> |
| RemoteIPCountry |País do endereço IP do cliente. |
| RemoteIPLatitude |Latitude do endereço IP de cliente. |
| RemoteIPLongitude |Longitude do endereço IP de cliente. |
| scStatus |Código de estado HTTP. |
| scSubStatus |Código de erro de subestado. |
| scWin32Status |Código de estado do Windows. |
| sIP |Endereço IP do servidor web. |
| SourceSystem |OpsMgr |
| sPort |Porta no cliente ligado ao servidor. |
| sSiteName |Nome do site do IIS. |
| TimeGenerated |Data e hora que a entrada foi registada. |
| TimeTaken |Período de tempo para processar o pedido em milissegundos. |

## <a name="log-queries-with-iis-logs"></a>Consultas de registo com logs do IIS
A tabela seguinte fornece exemplos diferentes de consultas de registo que obter registros de log do IIS.

| Consulta | Descrição |
|:--- |:--- |
| W3CIISLog |Todos os registros de log do IIS. |
| W3CIISLog &#124; onde scStatus = = 500 |Todos os registos de registo IIS com um status de retorno de 500. |
| W3CIISLog &#124; summarize count() by cIP |Contagem dos IIS entradas de registo por endereço IP do cliente. |
| W3CIISLog &#124; where csHost=="www\.contoso.com" &#124; summarize count() by csUriStem |Entradas de registo de contagem do IIS por URL para o host www\.contoso.com. |
| W3CIISLog &#124; resumir sum(csBytes) por computador &#124; tirar 500000 |Total de bytes recebidos por cada computador do IIS. |

## <a name="next-steps"></a>Passos Seguintes
* Configure o Azure Monitor para recolher outros [origens de dados](agent-data-sources.md) para análise.
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.