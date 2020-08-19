---
title: Implemente o reencaminhador de registos para ligar os dados do CEF ao Azure Sentinel ! Microsoft Docs
description: Saiba como implantar o agente para ligar os dados do CEF ao Azure Sentinel.
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
ms.openlocfilehash: a7d7c7b7236841835866ccb7786e7e4eab767c1f
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565592"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Passo 1: Implementar o reencaminhador de registo


Neste passo, designará e configurará a máquina Linux que irá encaminhar os registos da sua solução de segurança para o seu espaço de trabalho Azure Sentinel. Esta máquina pode ser uma máquina física ou virtual no seu ambiente no local, um VM Azure ou um VM em outra nuvem. Utilizando o link fornecido, irá executar um script na máquina designada que executa as seguintes tarefas:
- Instala o agente Log Analytics para o Linux (também conhecido como agente OMS) e configura-o para as seguintes finalidades:
    - Escutando as mensagens CEF do daemon linux syslog incorporado na porta TCP 25226
    - enviando as mensagens de forma segura sobre o TLS para o seu espaço de trabalho Azure Sentinel, onde são analisadas e enriquecidas

- Configura o daemon Linux Syslog incorporado (rsyslog.d/syslog-ng) para os seguintes fins:
    - Ouvir mensagens Syslog das suas soluções de segurança na porta TCP 514
    - reencaminhamento apenas das mensagens que identifica como CEF para o agente Log Analytics na localidade local usando a porta TCP 25226
 
## <a name="prerequisites"></a>Pré-requisitos

- Deve ter permissões elevadas (sudo) na sua máquina Linux designada.
- Deve ter pitão instalado na máquina Linux.<br>Use o `python -version` comando para verificar.
- A máquina Linux não deve ser ligada a nenhum espaço de trabalho da Azure antes de instalar o agente Log Analytics.

## <a name="run-the-deployment-script"></a>Executar o script de implementação
 
1. A partir do menu de navegação Azure Sentinel, clique nos **conectores de dados.** A partir da lista de conectores, clique no **azulejo do Formato de Evento Comum (CEF)** e, em seguida, no botão de página do **conector Aberto** no direito inferior. 

1. Abaixo de **1.2 Instale o coletor CEF na máquina Linux,** copie o link fornecido sob **executar o seguinte script para instalar e aplicar o coletor CEF**, ou a partir do texto abaixo:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Enquanto o script está em execução, verifique se não recebe nenhum erro ou mensagens de aviso.

> [!NOTE]
> **Usando a mesma máquina para encaminhar mensagens simples de Syslog *e* CEF**
>
> Se planeia utilizar esta máquina de encaminhar o reencaminhador de registos para encaminhar [mensagens Syslog,](connect-syslog.md) bem como CEF, então, para evitar a duplicação de eventos nas tabelas Syslog e CommonSecurityLog:
>
> 1. Em cada máquina de origem que envia registos para o reencaminhador no formato CEF, tem de editar o ficheiro de configuração Syslog para remover as instalações que estão a ser utilizadas para enviar mensagens CEF. Desta forma, as instalações que são enviadas no CEF também não serão enviadas em Syslog. Consulte [o Configure Syslog no agente Linux](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) para obter instruções detalhadas sobre como fazê-lo.
>
> 1. Tem de executar o seguinte comando nessas máquinas para desativar a sincronização do agente com a configuração Syslog em Azure Sentinel. Isto garante que a alteração de configuração efetuada no passo anterior não seja substituída.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

Continue a [passo 2: Configure a sua solução de segurança para encaminhar mensagens CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Script de implementação explicado

Segue-se uma descrição de comando por comando das ações do script de implantação.

Escolha um daemon syslog para ver a descrição apropriada.

# <a name="rsyslog-daemon"></a>[daemon rsyslog](#tab/rsyslog)

1. **Descarregar e instalar o agente Log Analytics:**

    - Descarrega o script de instalação para o agente Linux Do Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Instala o agente Log Analytics<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configurar o daemon Syslog:**

    1. Abre a porta 514 para a comunicação TCP utilizando o ficheiro de configuração do syslog `/etc/rsyslog.conf` .

    1. Configura o daemon para encaminhar mensagens CEF para o agente Log Analytics na porta TCP 25226, inserindo um ficheiro de configuração especial `security-config-omsagent.conf` no diretório de daemon syslog `/etc/rsyslog.d/` .

        Conteúdo do `security-config-omsagent.conf` ficheiro:

        ```console
        :rawmsg, regex, "CEF"|"ASA"
        *.* @@127.0.0.1:25226
        ```

1. **Reiniciar o daemon Syslog**

    `service rsyslog restart`

1. **Definição da configuração do agente Log Analytics para ouvir na porta 25226 e encaminhar mensagens CEF para Azure Sentinel**

    1. Descarrega a configuração do repositório gitHub do agente log analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Reinicia o agente Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. **Descarregar e instalar o agente Log Analytics:**

    - Descarrega o script de instalação para o agente Linux Do Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Instala o agente Log Analytics<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configurar o daemon Syslog:**

    1. Abre a porta 514 para a comunicação TCP utilizando o ficheiro de configuração do syslog `/etc/syslog-ng/syslog-ng.conf` .

    1. Configura o daemon para encaminhar mensagens CEF para o agente Log Analytics na porta TCP 25226, inserindo um ficheiro de configuração especial `security-config-omsagent.conf` no diretório de daemon syslog `/etc/syslog-ng/conf.d/` .

        Conteúdo do `security-config-omsagent.conf` ficheiro:

        ```console
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Reiniciar o daemon Syslog**

    `service syslog-ng restart`

1. **Definição da configuração do agente Log Analytics para ouvir na porta 25226 e encaminhar mensagens CEF para Azure Sentinel**

    1. Descarrega a configuração do repositório gitHub do agente log analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Reinicia o agente Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a implantar o agente Log Analytics para ligar aparelhos CEF ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)

