---
title: Implementar o carregador de log forwarder para ligar os dados do CEF ao Azure Sentinel [ Microsoft Docs
description: Aprenda a implementar o agente para ligar os dados do CEF ao Azure Sentinel.
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
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731647"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Passo 1: Implementar o avançado de registo


Neste passo, designará e configurará a máquina Linux que irá encaminhar os registos da sua solução de segurança para o seu espaço de trabalho Azure Sentinel. Esta máquina pode ser uma máquina física ou virtual no seu ambiente no local, um VM Azure ou um VM em outra nuvem. Utilizando o link fornecido, executará um script na máquina designada que executa as seguintes tarefas:
- Instala o agente Log Analytics para o Linux (também conhecido como agente OMS) e configura-o para os seguintes fins:
    - ouvir mensagens CEF do daemon Linux Syslog incorporado na porta TCP 25226
    - enviando as mensagens de forma segura sobre tLS para o seu espaço de trabalho Azure Sentinel, onde são analisadas e enriquecidas

- Configura o daemon Linux Syslog incorporado (rsyslog.d/syslog-ng) para os seguintes fins:
    - ouvir mensagens Syslog das suas soluções de segurança na porta TCP 514
    - reencaminhamento apenas das mensagens que identifica como CEF para o agente Log Analytics no local de acolhimento utilizando a porta TCP 25226
 
## <a name="prerequisites"></a>Pré-requisitos

- Deve ter permissões elevadas (sudo) na sua máquina Linux designada.
- Deve ter python instalado na máquina Linux.<br>Use `python -version` o comando para verificar.
- A máquina Linux não deve ser ligada a nenhum espaço de trabalho Azure antes de instalar o agente Log Analytics.

## <a name="run-the-deployment-script"></a>Executar o script de implementação
 
1. A partir do menu de navegação Azure Sentinel, clique em **Conectores de Dados**. A partir da lista de conectores, clique no formato de **evento comum (CEF)** e, em seguida, no botão de página do **conector Aberto** na parte inferior direita. 

1. Em **1.2 Instale o coletor CEF na máquina Linux,** copie o link fornecido sob **executar o seguinte script para instalar e aplicar o coletor CEF,** ou a partir do texto abaixo:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Enquanto o script estiver em execução, verifique se não obtém nenhum erro ou mensagens de aviso.

Continuar a [PASSO 2: Configure a sua solução de segurança para transmitir mensagens CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Script de implantação explicado

Segue-se uma descrição de comando por comando das ações do script de implantação.

Escolha um daemon syslog para ver a descrição apropriada.

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. **Descarregar e instalar o agente Log Analytics:**

    - Descarrega o script de instalação para o agente Linux Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Instala o agente Log Analytics<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configurar o daemon Syslog:**

    1. Abre a porta 514 para comunicação TCP `/etc/rsyslog.conf`utilizando o ficheiro de configuração do sislog .

    1. Configura o daemon para encaminhar mensagens CEF para o agente Log Analytics na porta `security-config-omsagent.conf` TCP 25226, `/etc/rsyslog.d/`inserindo um ficheiro de configuração especial no diretório da ememon syslog .

        Conteúdo do `security-config-omsagent.conf` ficheiro:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **Reiniciar o daemon Syslog**

    `service rsyslog restart`

1. **Definição da configuração do agente Log Analytics para ouvir na porta 25226 e reencaminhar mensagens CEF para O Sentinel**

    1. Descarrega a configuração do repositório gitHub do agente Delog Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Reinicia o agente Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. **Descarregar e instalar o agente Log Analytics:**

    - Descarrega o script de instalação para o agente Linux Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Instala o agente Log Analytics<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configurar o daemon Syslog:**

    1. Abre a porta 514 para comunicação TCP `/etc/syslog-ng/syslog-ng.conf`utilizando o ficheiro de configuração do sislog .

    1. Configura o daemon para encaminhar mensagens CEF para o agente Log Analytics na porta `security-config-omsagent.conf` TCP 25226, `/etc/syslog-ng/conf.d/`inserindo um ficheiro de configuração especial no diretório da ememon syslog .

        Conteúdo do `security-config-omsagent.conf` ficheiro:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **Reiniciar o daemon Syslog**

    `service syslog-ng restart`

1. **Definição da configuração do agente Log Analytics para ouvir na porta 25226 e reencaminhar mensagens CEF para O Sentinel**

    1. Descarrega a configuração do repositório gitHub do agente Delog Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Reinicia o agente Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a implantar o agente Log Analytics para ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)

