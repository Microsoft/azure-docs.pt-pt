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
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 07a6b84569fe0356267440e38b31ac738b2659d6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260836"
---
# <a name="step-3-validate-connectivity"></a>PASSO 3: Validar a conectividade

Depois de ter implantado o seu reencaminhador de registo (no Passo 1) e configurado a sua solução de segurança para lhe enviar mensagens CEF (no passo 2), siga estas instruções para verificar a conectividade entre a sua solução de segurança e o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter permissões elevadas (sudo) na sua máquina de reencaminhamento de registo.

- Deve ter python instalado na sua máquina de reencaminhar de bordo.<br>
Use o `python –version` comando para verificar.

## <a name="how-to-validate-connectivity"></a>Como validar a conectividade

1. A partir do menu de navegação Azure Sentinel, abra **registos.** Faça uma consulta utilizando o esquema **CommonSecurityLog** para ver se está a receber registos da sua solução de segurança.<br>
Tenha em atenção que pode demorar cerca de 20 minutos até que os seus registos comecem a aparecer no **Log Analytics**. 

1. Se não vir nenhum resultado da consulta, verifique se os eventos estão a ser gerados a partir da sua solução de segurança, ou tente gerar alguns, e verifique se estão a ser encaminhados para a máquina de reencaminhamento Syslog que designou. 

1. Execute o seguinte script no reencaminhador de registo para verificar a conectividade entre a sua solução de segurança, o reencaminhador de registos e o Azure Sentinel. Este script verifica se o daemon está a ouvir nas portas corretas, que o reencaminhamento está devidamente configurado, e que nada está a bloquear a comunicação entre o daemon e o agente Log Analytics. Também envia mensagens falsas 'TestCommonEventFormat' para verificar a conectividade de ponta a ponta. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Roteiro de validação explicado

O script de validação executa as seguintes verificações:

# <a name="rsyslog-daemon"></a>[daemon rsyslog](#tab/rsyslog)

1. Verifica que o ficheiro<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe e é válido.

1. Verifica se o ficheiro inclui o seguinte texto:

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

1. Verifique se existem melhorias de segurança na máquina que possam estar a bloquear o tráfego da rede (como uma firewall de hospedeiro).

1. Verifica se o syslog daemon (rsyslog) está devidamente configurado para enviar mensagens que identifica como CEF (usando um regex) para o agente Log Analytics na porta TCP 25226:

    - Ficheiro de configuração:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF"|"ASA"
            *.* @@127.0.0.1:25226

1. Verifica se o syslog Daemon está a receber dados sobre o porto 514

1. Verifica se estão estabelecidas as ligações necessárias: tcp 514 para receção de dados, tcp 25226 para comunicação interna entre o syslog daemon e o agente Log Analytics

1. Envia dados MOCK para o porto 514 na localidade local. Estes dados devem ser observáveis no espaço de trabalho Azure Sentinel, executando a seguinte consulta:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. Verifica que o ficheiro<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe e é válido.

1. Verifica se o ficheiro inclui o seguinte texto:

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

1. Verifique se existem melhorias de segurança na máquina que possam estar a bloquear o tráfego da rede (como uma firewall de hospedeiro).

1. Verifica se o syslog daemon (syslog-ng) está devidamente configurado para enviar mensagens que identifica como CEF (usando um regex) para o agente Log Analytics na porta TCP 25226:

    - Ficheiro de configuração:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Verifica se o syslog Daemon está a receber dados sobre o porto 514

1. Verifica se estão estabelecidas as ligações necessárias: tcp 514 para receção de dados, tcp 25226 para comunicação interna entre o syslog daemon e o agente Log Analytics

1. Envia dados MOCK para o porto 514 na localidade local. Estes dados devem ser observáveis no espaço de trabalho Azure Sentinel, executando a seguinte consulta:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.

