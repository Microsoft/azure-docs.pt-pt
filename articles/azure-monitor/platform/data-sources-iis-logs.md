---
title: Logs do IIS no Azure Monitor | Microsoft Docs
description: O Serviços de Informações da Internet (IIS) armazena a atividade do usuário em arquivos de log que podem ser coletados pelo Azure Monitor.  Este artigo descreve como configurar a coleta de logs do IIS e detalhes dos registros que eles criam no Azure Monitor.
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
ms.openlocfilehash: 05d9dc8f676589dcb301c19b0a2e80e9fd4c1fa0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249747"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Coletar logs do IIS no Azure Monitor
O Serviços de Informações da Internet (IIS) armazena a atividade do usuário em arquivos de log que podem ser coletados pelo Azure Monitor e armazenados como [dados de log](data-platform.md).

![Registos do IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configurando logs do IIS
Azure Monitor coleta entradas de arquivos de log criados pelo IIS, portanto, você deve [Configurar o IIS para registro em log](https://technet.microsoft.com/library/hh831775.aspx).

Azure Monitor só dá suporte a arquivos de log do IIS armazenados no formato W3C e não oferece suporte a campos personalizados ou logs avançados do IIS. Ele não coleta logs em formato nativo NCSA ou IIS.

Configure os logs do IIS no Azure Monitor no [menu configurações avançadas](agent-data-sources.md#configuring-data-sources).  Não há nenhuma configuração necessária além de selecionar **coletar arquivos de log do IIS no formato W3C**.


## <a name="data-collection"></a>Recolha de dados
Azure Monitor coleta entradas de log do IIS de cada agente sempre que o carimbo de data/hora do log é alterado ou um novo arquivo é criado. O log é lido a cada 5 minutos. A frequência da criação do novo arquivo é controlada pela configuração **agenda de substituição do arquivo de log** para o site do IIS, que é uma vez por dia por padrão. Se, por algum motivo, o IIS não atualizar o carimbo de data/hora antes do horário de substituição, se a configuração for por **hora**, Azure monitor coletará o log a cada hora. Se a configuração for **diária**, Azure monitor coletará o log a cada 24 horas.


## <a name="iis-log-record-properties"></a>Propriedades de registro de log do IIS
Os registros de log do IIS têm um tipo de **W3CIISLog** e têm as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| Computer |Nome do computador do qual o evento foi coletado. |
| cIP |Endereço IP do cliente. |
| csMethod |Método da solicitação, como GET ou POST. |
| csReferer |Site do qual o usuário seguiu um link para o site atual. |
| csUserAgent |Tipo de navegador do cliente. |
| csUserName |Nome do usuário autenticado que acessou o servidor. Os usuários anônimos são indicados por um hífen. |
| csUriStem |Destino da solicitação, como uma página da Web. |
| csUriQuery |Consulta, se houver, que o cliente estava tentando executar. |
| ManagementGroupName |Nome do grupo de gerenciamento para agentes de Operations Manager.  Para outros agentes, é AOI -\<ID da área de trabalho\> |
| RemoteIPCountry |País/região do endereço IP do cliente. |
| RemoteIPLatitude |Latitude do endereço IP do cliente. |
| RemoteIPLongitude |Longitude do endereço IP do cliente. |
| scStatus |Código de status HTTP. |
| scSubStatus |Código de erro de substatus. |
| scWin32Status |Código de status do Windows. |
| sIP |Endereço IP do servidor Web. |
| SourceSystem |OpsMgr |
| Esporte |Porta no servidor ao qual o cliente se conectou. |
| sSiteName |Nome do site do IIS. |
| TimeGenerated |Data e hora em que a entrada foi registrada. |
| TimeTaken |Período de tempo para processar a solicitação em milissegundos. |

## <a name="log-queries-with-iis-logs"></a>Consultas de log com logs do IIS
A tabela a seguir fornece diferentes exemplos de consultas de log que recuperam registros de log do IIS.

| Consulta | Descrição |
|:--- |:--- |
| W3CIISLog |Todos os registros de log do IIS. |
| W3CIISLog &#124; em que scStatus = = 500 |Todos os registros de log do IIS com um status de retorno de 500. |
| Contagem &#124; de Resumo de W3CIISLog () por cIP |Contagem de entradas de log do IIS por endereço IP do cliente. |
| W3CIISLog &#124; where csHost=="www\.contoso.com" &#124; summarize count() by csUriStem |Contagem de entradas de log do IIS por URL para o\.host www contoso.com. |
| W3CIISLog &#124; resumite Sum (csBytes) por &#124; computador, pegue 500000 |Total de bytes recebidos por cada computador do IIS. |

## <a name="next-steps"></a>Passos Seguintes
* Configure Azure Monitor para coletar outras [fontes de dados](agent-data-sources.md) para análise.
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.
