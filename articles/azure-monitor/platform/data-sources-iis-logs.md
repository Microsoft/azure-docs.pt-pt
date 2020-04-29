---
title: IIS regista no Monitor Azure [ Monitor De Acesso) Microsoft Docs
description: O Internet Information Services (IIS) armazena a atividade do utilizador em ficheiros de registo que podem ser recolhidos pelo Monitor Azure.  Este artigo descreve como configurar a recolha de registos IIS e detalhes dos registos que criam no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 1b3ae6295a639c3d59643b106b920cb606572e0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670581"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Recolher registos IIS no Monitor Azure
O Serviço de Informação de Internet (IIS) armazena a atividade do utilizador em ficheiros de registo que podem ser recolhidos pelo Monitor Azure e armazenados como dados de [registo](data-platform.md).

![Registos do IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configurar registos IIS
O Monitor Azure recolhe entradas de ficheiros de registo criados pelo IIS, pelo que tem de [configurar o IIS para a exploração de madeira](https://technet.microsoft.com/library/hh831775.aspx).

O Azure Monitor apenas suporta ficheiros de registo IIS armazenados em formato W3C e não suporta campos personalizados ou IIS Advanced Logging. Não recolhe registos em formato nativo NCSA ou IIS.

Configure os registos IIS no Monitor Azure a partir do [menu Definições Avançadas](agent-data-sources.md#configuring-data-sources).  Não é necessária qualquer configuração que não seja selecionar ficheiros de **registo Do formato W3C IIS**.


## <a name="data-collection"></a>Recolha de dados
O Monitor Azure recolhe entradas de registo IIS de cada agente sempre que o carimbo de tempo de registo muda. O registo é lido a cada **5 minutos**. Se, por alguma razão, o IIS não atualizar o carimbo de tempo antes do tempo de reversão quando um novo ficheiro for criado, as entradas serão recolhidas após a criação do novo ficheiro. A frequência da criação de novos ficheiros é controlada pela definição de **'Rollover Schedule'** de registo para o site IIS, que é uma vez por dia por padrão. Se a definição for **horada,** o Monitor Azure recolhe o registo a cada hora. Se a definição for **Diária,** o Azure Monitor recolhe o registo a cada 24 horas.


## <a name="iis-log-record-properties"></a>Propriedades de registo de registo iIS
Os registos de registo iIS têm um tipo de **W3CIISLog** e têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Nome do computador de que o evento foi recolhido. |
| cIP |Endereço IP do cliente. |
| csMétodo |Método do pedido, como GET ou POST. |
| csReferenciador |Site que o utilizador seguiu um link de para o site atual. |
| csUserAgent |Tipo de navegador do cliente. |
| csUserName |Nome do utilizador autenticado que acedeu ao servidor. Os utilizadores anónimos são indicados por um hífen. |
| csUriStem |Alvo do pedido, como uma página web. |
| csUriQuery |Pergunta, se houver, que o cliente estava a tentar atuar. |
| ManagementGroupName |Nome do grupo de gestão dos agentes do Gestor de Operações.  Para outros agentes, este\<é O II- espaço de trabalho\> |
| RemoteIPCountry |País/região do endereço IP do cliente. |
| RemoteIPLatitude |Latitude do endereço IP do cliente. |
| RemoteIPLongitude |Longitude do endereço IP do cliente. |
| scStatus |Código de estado HTTP. |
| scSubStatus |Código de erro de subestatuto. |
| scWin32Status |Código de estado do Windows. |
| SIP |Endereço IP do servidor web. |
| SourceSystem |OpsMgr |
| sPort |Porta no servidor ao qual o cliente ligou. |
| sSiteName |Nome do site do IIS. |
| TimeGenerated |Data e hora da entrada foi registada. |
| Tempo tomado |Duração do tempo para processar o pedido em milissegundos. |

## <a name="log-queries-with-iis-logs"></a>Consultas de log com registos IIS
A tabela seguinte fornece diferentes exemplos de consultas de registo que recuperam registos de registos IIS.

| Consulta | Descrição |
|:--- |:--- |
| W3CIISLog |Todos os registos de registos do IIS. |
| W3CIISLog &#124; onde scStatus==500 |Todos os registos de registo sinuoso iIS com um estado de devolução de 500. |
| W3CIISLog &#124; resumir contagem() por cIP |Contagem das entradas de log IIS pelo endereço IP do cliente. |
| W3CIISLog &#124; onde csHost="www\.contoso.com" &#124; resumir a contagem() por csUriStem |Contagem das entradas de log IIS\.por URL para o anfitrião www contoso.com. |
| W3CIISLog &#124; resumir sumo (csBytes) por computador &#124; tomar 500000 |Bytes totais recebidos por cada computador IIS. |

## <a name="next-steps"></a>Passos seguintes
* Configure o Monitor Azure para recolher [outras fontes de dados](agent-data-sources.md) para análise.
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados.
