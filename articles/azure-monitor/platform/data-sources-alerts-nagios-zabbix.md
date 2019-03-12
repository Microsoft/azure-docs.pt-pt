---
title: Recolher alertas do Nagios e do Zabbix no Azure Monitor | Documentos da Microsoft
description: Nagios e do Zabbix são de código aberto, ferramentas de monitorização. Pode recolher alertas dessas ferramentas no Azure Monitor para analisá-los junto com alertas a partir de outras origens.  Este artigo descreve como configurar o agente do Log Analytics para Linux recolher alertas a partir desses sistemas.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: f0f156568eed5a1e8f3296ff7c37df7f050dbc33
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540050"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-azure-monitor-from-log-analytics-agent-for-linux"></a>Recolher alertas do Nagios e do Zabbix no Azure Monitor do agente do Log Analytics para Linux 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

[Nagios](https://www.nagios.org/) e [Zabbix](https://www.zabbix.com/) são de código aberto, ferramentas de monitorização. Pode recolher alertas dessas ferramentas no Azure Monitor para analisá-los com dados de registo de outras origens.  Este artigo descreve como configurar o agente do Log Analytics para Linux recolher alertas a partir desses sistemas.


> [!NOTE]
> [Alertas criados pelo Azure Monitor](alerts-overview.md) são armazenados separadamente a partir dos dados de registo e não está acessível a partir de consultas de registo.

 
## <a name="prerequisites"></a>Pré-requisitos
O agente do Log Analytics para Linux suporta recolher alertas do Nagios até versão 4.2.x e do Zabbix até versão 2.x.

## <a name="configure-alert-collection"></a>Configurar a recolha de alertas

### <a name="configuring-nagios-alert-collection"></a>Configurar a recolha de alertas do Nagios
Recolher alertas, execute os seguintes passos no servidor Nagios.

1. Conceder ao utilizador **omsagent** acesso de leitura para o ficheiro de registo Nagios `/var/log/nagios/nagios.log`. Supondo que o ficheiro de nagios.log é pertencentes ao grupo `nagios`, pode adicionar o usuário **omsagent** para o **nagios** grupo. 

    sudo usermod -a -G nagios omsagent

2.  Modificar o ficheiro de configuração em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Certifique-se de que as entradas a seguir estão presentes e não comentado horizontalmente:  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. Reinicie o daemon de omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Configurar a recolha de alertas da Zabbix
Recolher alertas de um servidor da Zabbix, tem de especificar um utilizador e palavra-passe na *limpar texto*.  Embora não seja ideal, recomendamos que crie um utilizador Zabbix com permissões só de leitura para capturar alarmes relevantes.

Recolher alertas no servidor Nagios, execute os seguintes passos.

1. Modificar o ficheiro de configuração em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Certifique-se de que as entradas a seguir estão presentes e não comentado horizontalmente.  Altere o nome de utilizador e palavra-passe para os valores para o seu ambiente do Zabbix.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Reinicie o daemon de omsagent

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Registos de alerta
Pode obter registos de alertas do Nagios e do Zabbix usando [registar as consultas](../log-query/log-query-overview.md) no Azure Monitor.

### <a name="nagios-alert-records"></a>Registos de alerta de Nagios

Alertas de registos recolhidos pelo Nagios têm uma **tipo** de **alerta** e um **SourceSystem** de **Nagios**.  Eles têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alertar* |
| SourceSystem |*Nagios* |
| AlertName |Nome do alerta. |
| AlertDescription | Descrição do alerta. |
| AlertState | Estado do serviço ou do anfitrião.<br><br>OK<br>AVISO<br>CÓPIA DE SEGURANÇA<br>PARA BAIXO |
| nome de anfitrião | Nome do anfitrião que criou o alerta. |
| PriorityNumber | Nível de prioridade do alerta. |
| StateType | O tipo de estado do alerta.<br><br>Configuração SOFT - problema que não tenha sido verificados-novamente.<br>RÍGIDO - problema que tem sido verificados novamente um número de vezes especificado.  |
| TimeGenerated |Data e hora que o alerta foi criado. |


### <a name="zabbix-alert-records"></a>Registos de alerta do Zabbix
Alertas de registos recolhidos pelo Zabbix têm uma **tipo** de **alerta** e um **SourceSystem** de **Zabbix**.  Eles têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alertar* |
| SourceSystem |*Zabbix* |
| AlertName | Nome do alerta. |
| AlertPriority | Gravidade do alerta.<br><br>não classificado<br>informações<br>aviso<br>média<br>Alta<br>após desastre  |
| AlertState | Estado do alerta.<br><br>0 - estado está atualizado.<br>1 - estado é desconhecido.  |
| AlertTypeNumber | Especifica se o alerta pode gerar vários eventos de problema.<br><br>0 - estado está atualizado.<br>1 - estado é desconhecido.    |
| Comentários | Comentários adicionais para o alerta. |
| nome de anfitrião | Nome do anfitrião que criou o alerta. |
| PriorityNumber | Valor que indica a gravidade do alerta.<br><br>0 - não classificado<br>1 - informações<br>2 - aviso<br>3 - médio<br>4 - elevada<br>5 - após desastre |
| TimeGenerated |Data e hora que o alerta foi criado. |
| TimeLastModified |Data e hora, que o estado do alerta foi alterado pela última vez. |


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [alertas](alerts-overview.md) no Azure Monitor.
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções. 