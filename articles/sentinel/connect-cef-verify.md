---
title: Validar conectividade ao Azure Sentinel ! Microsoft Docs
description: Valide a conectividade da sua solução de segurança para garantir que as mensagens CEF estão a ser reencaminhadas para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 8bdc9d588e89f498b973db7d1de299b0b26a3d02
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655736"
---
# <a name="step-3-validate-connectivity"></a>PASSO 3: Validar a conectividade

Depois de ter implantado o seu reencaminhador de registo (no Passo 1) e configurado a sua solução de segurança para lhe enviar mensagens CEF (no passo 2), siga estas instruções para verificar a conectividade entre a sua solução de segurança e o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter permissões elevadas (sudo) na sua máquina de reencaminhamento de registo.

- Deve ter **a pitão 2.7** ou **3** instalada na sua máquina de reencaminhador de troncos.<br>
Use o `python –version` comando para verificar.

- Você pode precisar do ID do espaço de trabalho e chave primária do espaço de trabalho em algum momento deste processo. Pode encontrá-los no recurso do espaço de trabalho, sob **gestão de Agentes.**

## <a name="how-to-validate-connectivity"></a>Como validar a conectividade

1. A partir do menu de navegação Azure Sentinel, abra **registos.** Faça uma consulta utilizando o esquema **CommonSecurityLog** para ver se está a receber registos da sua solução de segurança.<br>
Tenha em atenção que pode demorar cerca de 20 minutos até que os seus registos comecem a aparecer no **Log Analytics**. 

1. Se não vir nenhum resultado da consulta, verifique se os eventos estão a ser gerados a partir da sua solução de segurança, ou tente gerar alguns, e verifique se estão a ser encaminhados para a máquina de reencaminhamento Syslog que designou. 

1. Execute o seguinte script no reencaminhador de registo (aplicando o ID do espaço de trabalho no lugar do espaço reservado) para verificar a conectividade entre a sua solução de segurança, o reencaminhador de registos e o Azure Sentinel. Este script verifica se o daemon está a ouvir nas portas corretas, que o reencaminhamento está devidamente configurado, e que nada está a bloquear a comunicação entre o daemon e o agente Log Analytics. Também envia mensagens falsas 'TestCommonEventFormat' para verificar a conectividade de ponta a ponta. <br>

    ```bash
    sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]` 
    ```

   - Pode receber uma mensagem que o direcione a executar um comando para corrigir um problema com o **mapeamento do campo *Computador***. Consulte a [explicação no roteiro de validação](#mapping-command) para mais detalhes.

    - Pode obter uma mensagem que o direcione a executar um comando para corrigir um problema com a **análise dos registos de firewall cisco ASA**. Consulte a [explicação no roteiro de validação](#parsing-command) para mais detalhes.

## <a name="validation-script-explained"></a>Roteiro de validação explicado

O script de validação executa as seguintes verificações:

# <a name="rsyslog-daemon"></a>[daemon rsyslog](#tab/rsyslog)

1. Verifica que o ficheiro<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe e é válido.

1. Verifica se o ficheiro inclui o seguinte texto:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Verifica se a análise dos eventos cisco ASA Firewall está configurada como esperado, utilizando o seguinte comando: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Se houver algum problema com a análise, o script produzirá uma mensagem de erro direcionando-o a **executar manualmente o seguinte comando** (aplicando o ID do espaço de trabalho no lugar do espaço reservado). O comando assegurará a análise correta e reiniciará o agente.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Verifique se o campo *de Computador* na fonte de syslog está corretamente mapeado no agente Log Analytics, utilizando o seguinte comando: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Se houver algum problema com o mapeamento, o script produzirá uma mensagem de erro direcionando-o a **executar manualmente o seguinte comando** (aplicando o ID do espaço de trabalho no lugar do espaço reservado). O comando assegurará o mapeamento correto e reiniciará o agente.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Verifique se existem melhorias de segurança na máquina que possam estar a bloquear o tráfego da rede (como uma firewall de hospedeiro).

1. Verifica se o syslog daemon (rsyslog) está devidamente configurado para enviar mensagens (que identifica como CEF) para o agente Log Analytics na porta TCP 25226:

    - Ficheiro de configuração: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. Reinicia o syslog daemon e o agente Log Analytics:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Verificação de que estão estabelecidas as ligações necessárias: tcp 514 para receção de dados, tcp 25226 para comunicação interna entre o syslog daemon e o agente Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Verifica se o syslog Daemon está a receber dados sobre o porto 514, e que o agente está a receber dados sobre a porta 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Envia dados MOCK para o porto 514 na localidade local. Estes dados devem ser observáveis no espaço de trabalho Azure Sentinel, executando a seguinte consulta:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. Verifica que o ficheiro<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe e é válido.

1. Verifica se o ficheiro inclui o seguinte texto:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Verifica se a análise dos eventos cisco ASA Firewall está configurada como esperado, utilizando o seguinte comando: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Se houver algum problema com a análise, o script produzirá uma mensagem de erro direcionando-o a **executar manualmente o seguinte comando** (aplicando o ID do espaço de trabalho no lugar do espaço reservado). O comando assegurará a análise correta e reiniciará o agente.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Verifique se o campo *de Computador* na fonte de syslog está corretamente mapeado no agente Log Analytics, utilizando o seguinte comando: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Se houver algum problema com o mapeamento, o script produzirá uma mensagem de erro direcionando-o a **executar manualmente o seguinte comando** (aplicando o ID do espaço de trabalho no lugar do espaço reservado). O comando assegurará o mapeamento correto e reiniciará o agente.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Verifique se existem melhorias de segurança na máquina que possam estar a bloquear o tráfego da rede (como uma firewall de hospedeiro).

1. Verifica se o syslog daemon (syslog-ng) está devidamente configurado para enviar mensagens que identifica como CEF (usando um regex) para o agente Log Analytics na porta TCP 25226:

    - Ficheiro de configuração: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Reinicia o syslog daemon e o agente Log Analytics:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Verificação de que estão estabelecidas as ligações necessárias: tcp 514 para receção de dados, tcp 25226 para comunicação interna entre o syslog daemon e o agente Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Verifica se o syslog Daemon está a receber dados sobre o porto 514, e que o agente está a receber dados sobre a porta 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Envia dados MOCK para o porto 514 na localidade local. Estes dados devem ser observáveis no espaço de trabalho Azure Sentinel, executando a seguinte consulta:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](./tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.