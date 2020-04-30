---
title: Validar a conectividade com o Azure Sentinel Microsoft Docs
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
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731822"
---
# <a name="step-3-validate-connectivity"></a>PASSO 3: Validar a conectividade

Depois de ter implantado o seu avançado de registo (no Passo 1) e configurado a sua solução de segurança para enviar mensagens CEF (no Passo 2), siga estas instruções para verificar a conectividade entre a sua solução de segurança e o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter permissões elevadas (sudo) na sua máquina de encaminhamento.

- Deve ter python instalado na sua máquina de encaminhador.<br>
Use `python –version` o comando para verificar.

## <a name="how-to-validate-connectivity"></a>Como validar a conectividade

1. Do menu de navegação Azure Sentinel, open **Logs**. Faça uma consulta utilizando o esquema **CommonSecurityLog** para ver se está a receber registos da sua solução de segurança.<br>
Esteja ciente de que pode demorar cerca de 20 minutos até que os seus registos comecem a aparecer no **Log Analytics**. 

1. Se não vir quaisquer resultados da consulta, verifique se os eventos estão a ser gerados a partir da sua solução de segurança, ou tente gerar alguns, e verifique se estão a ser encaminhados para a máquina de reencaminhamento Syslog que designou. 

1. Execute o seguinte script no carregador de log para verificar a conectividade entre a sua solução de segurança, o avançado de registo e o Azure Sentinel. Este guião verifica se o daemon está a ouvir as portas corretas, que o reencaminhamento está devidamente configurado, e que nada está a bloquear a comunicação entre o daemon e o agente Log Analytics. Também envia mensagens falsas 'TestCommonEventFormat' para verificar a conectividade de ponta a ponta. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Script de validação explicado

O script de validação executa as seguintes verificações:

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

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

1. Verifica se existem melhorias de segurança na máquina que possam estar a bloquear o tráfego da rede (como uma firewall hospedeira).

1. Verifica se o daemon syslog (rsyslog) está devidamente configurado para enviar mensagens que identifica como CEF (utilizando um regex) ao agente Log Analytics na porta TCP 25226:

    - Ficheiro de configuração:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Verifica se o syslog daemon está a receber dados na porta 514

1. Verifica se as ligações necessárias estão estabelecidas: tCP 514 para receber dados, tCP 25226 para comunicação interna entre o daemon syslog e o agente Log Analytics

1. Envia dados MOCK para a porta 514 no local de acolhimento. Estes dados devem ser observáveis no espaço de trabalho Do Sentinel Ad.

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

1. Verifica se existem melhorias de segurança na máquina que possam estar a bloquear o tráfego da rede (como uma firewall hospedeira).

1. Verifica se o syslog daemon (syslog-ng) está devidamente configurado para enviar mensagens que identifica como CEF (utilizando um regex) ao agente Log Analytics na porta TCP 25226:

    - Ficheiro de configuração:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Verifica se o syslog daemon está a receber dados na porta 514

1. Verifica se as ligações necessárias estão estabelecidas: tCP 514 para receber dados, tCP 25226 para comunicação interna entre o daemon syslog e o agente Log Analytics

1. Envia dados MOCK para a porta 514 no local de acolhimento. Estes dados devem ser observáveis no espaço de trabalho Do Sentinel Ad.

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.

