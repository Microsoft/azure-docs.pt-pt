---
title: Ligar dados Fortinet a pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Saiba como ligar a dados Fortinet a sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 6f061a99a526b3865081a7d19b1eecbdc158d6a1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442647"
---
# <a name="connect-your-fortinet-appliance"></a>Ligar a sua aplicação da Fortinet

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço. Não o recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode ligar sentinela do Azure para qualquer aplicação Fortinet ao guardar os ficheiros de registo como Syslog Common Event Format (CEF). Com a integração com o Azure sentinela, pode facilmente executar análises e consultas em dados de ficheiro de registo da Fortinet. Para obter mais informações sobre como o Azure sentinela ingere dados CEF, consulte [aparelhos de ligar o CEF](connect-common-event-format.md).

> [!NOTE]
> Dados são armazenados na localização geográfica da área de trabalho no qual executa sentinela do Azure.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Passo 1: Ligar a aplicação da Fortinet com um agente

Para ligar a sua aplicação da Fortinet a sentinela do Azure, implemente um agente num VM dedicada ou numa máquina no local para suportar a comunicação entre a aplicação e, em seguida, sentinela do Azure. Pode implementar o agente manualmente ou automaticamente. Implementação automática só está disponível se a sua máquina dedicada é uma nova VM que criar no Azure.

Também pode implementar o agente manualmente numa VM do Azure existente, numa VM noutra cloud ou numa máquina no local.

Para ver um diagrama de rede de ambas as opções, consulte [ligar a origens de dados](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent-in-azure"></a>Implementar o agente no Azure

1. No portal do Azure sentinela, selecione **conectores de dados** e selecione o tipo de aplicação.

1. Sob **configuração do agente Linux Syslog**:
   - Escolher **implementação automática** se pretender criar uma nova máquina que está pré-instalado com o agente do Azure sentinela e inclui toda a configuração necessária, conforme descrita anteriormente. Selecione **implementação automática** > **implementação automática do agente**. É apresentada a página de compra para uma VM dedicada, que é conectada automaticamente à sua área de trabalho. A VM é um **padrão D2s v3 (2 vCPUs, 8 GB de memória)** e tem um endereço IP público.
      1. Sobre o **implementação personalizada** página, forneça os detalhes da sua, introduza um nome de utilizador e uma palavra-passe e se concordar com os termos e condições, comprar a VM.
      1. Configure a aplicação para enviar registos ao utilizar as definições listadas na página de ligação. Para o conector de formato de evento comum genérica, utilize estas definições:
         - Protocolo = UDP
         - Porta = 514
         - Recurso = Local 4
         - Formato = CEF
   - Escolher **implementação Manual** se pretender utilizar uma VM existente como a máquina Linux dedicada em que o agente do Azure sentinela é instalado. 
      1. Sob **transfira e instale o agente de Syslog**, selecione **máquina virtual do Linux do Azure**. 
      1. Sobre o **máquinas virtuais** ecrã, selecione a máquina que pretende utilizar e selecione **Connect**.
      1. No ecrã de conector, sob **configurar e reencaminhar Syslog**, defina se o daemon de Syslog está **rsyslog.d** ou **syslog-ng**. 
      1. Copie estes comandos e executá-los na sua aplicação:
          - Se tiver selecionado rsyslog.d:
              
            1. Informe o Syslog daemon para escutar local_4 de instalações e para enviar as mensagens Syslog para o agente do Azure sentinela através da porta 25226. Use este comando: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Transferir e instalar o [o ficheiro de configuração de security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. Utilize este comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` onde {0} deve ser substituído pela sua área de trabalho GUID.
            1. Reinicie o syslog daemon, utilizando este comando: `sudo service rsyslog restart`
             
          - Se selecionou syslog-ng:

              1. Informe o Syslog daemon para escutar local_4 de instalações e para enviar as mensagens Syslog para o agente do Azure sentinela através da porta 25226. Use este comando: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              1. Transferir e instalar o [o ficheiro de configuração de security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. Utilize este comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` onde {0} deve ser substituído pela sua área de trabalho GUID.
              1. Reinicie o syslog daemon, utilizando este comando: `sudo service syslog-ng restart`
      1. Reinicie o agente de Syslog, utilizando este comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirme que não existem erros no registo do agente ao executar este comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Implementar o agente num servidor Linux no local

Se não estiver a utilizar o Azure, implemente manualmente o agente de Azure sentinela de mensagens em fila para ser executado num servidor Linux dedicado.

1. No portal do Azure sentinela, selecione **conectores de dados** e selecione o tipo de aplicação.
1. Para criar uma VM do Linux dedicado, em **configuração do agente Linux Syslog** selecionar **implementação Manual**.

    1. Sob **transfira e instale o agente de Syslog**, selecione **máquina Linux não Azure**.
    1. Na **agente direto** ecrã que se abre, selecione **agente para Linux** para transferir o agente ou execute este comando para baixá-lo no seu computador Linux: `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`

       1. No ecrã de conector, sob **configurar e reencaminhar Syslog**, defina se o daemon de Syslog está **rsyslog.d** ou **syslog-ng**.
       1. Copie estes comandos e executá-los na sua aplicação:

          - Se tiver selecionado rsyslog:

            1. Informe o Syslog daemon para escutar local_4 de instalações e para enviar as mensagens Syslog para o agente do Azure sentinela através da porta 25226. Use este comando: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Transferir e instalar o [o ficheiro de configuração de security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. Utilize este comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` onde {0} deve ser substituído pela sua área de trabalho GUID.
            1. Reinicie o syslog daemon, utilizando este comando: `sudo service rsyslog restart`

          - Se selecionou syslog-ng:

            1. Informe o Syslog daemon para escutar local_4 de instalações e para enviar as mensagens Syslog para o agente do Azure sentinela através da porta 25226. Use este comando: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            1. Transferir e instalar o [o ficheiro de configuração de security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. Utilize este comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` onde {0} deve ser substituído pela sua área de trabalho GUID.
            1. Reinicie o syslog daemon, utilizando este comando: `sudo service syslog-ng restart`

      1. Reinicie o agente de Syslog, utilizando este comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirme que não existem erros no registo do agente ao executar este comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Passo 2: Reencaminhar registos Fortinet para o agente do Syslog

Configure Fortinet para encaminhar mensagens do Syslog no formato CEF para sua área de trabalho do Azure por meio do agente do Syslog.

1. Abra a CLI na sua aplicação Fortinet e execute os seguintes comandos:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Substitua o servidor **endereço ip** com o endereço IP do agente.
    - Definir **facility_name** para usar o recurso que configurou no agente. Por predefinição, o agente define isto para local4.
    - Definir o **porta de syslog** ao **514** ou a porta definido no agente.
    - Para ativar o formato CEF em versões anteriores do FortiOS, poderá ter de executar o conjunto de comandos **csv desativar**.
 
   > [!NOTE] 
   > Para obter mais informações, vá para o [biblioteca de documentos Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Selecione sua versão e utilize o **'s Handbook** e **referência de mensagem do registo**.

 Para utilizar o esquema relevante no Log Analytics do Azure Monitor para os eventos da Fortinet, procure `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Passo 3: Validar a conectividade

Poderá demorar até 20 minutos até que os seus registos começam a aparecer no Log Analytics. 

1. Certifique-se de que utiliza o recurso correto. O recurso tem de ser o mesmo na sua aplicação e no Azure sentinela. Pode verificar o ficheiro de instalação estiver a utilizar no Azure sentinela e modificá-lo no arquivo `security-config-omsagent.conf`. 

2. Certifique-se de que os registos estão a obter para a porta correta do agente do Syslog. Execute este comando no computador de agente Syslog: `tcpdump -A -ni any  port 514 -vv`. Este comando mostra-lhe os registos que transmita em fluxo do dispositivo para a máquina do Syslog. Certifique-se de que os registos estão a ser recebidos a partir da aplicação de origem a porta à direita e o recurso correto.

3. Certifique-se de que estão em conformidade com os registos enviados [RFC 5424](https://tools.ietf.org/html/rfc542).

4. No computador que executa o agente de Syslog, certifique-se das portas 514 e 25226 são abertos e escuta com o comando `netstat -a -n:`. Para obter mais informações sobre como utilizar este comando, consulte [netstat(8) - página de homem de Linux](https://linux.die.net/man/8/netstat). Se estiver a escutar corretamente, consulte:

   ![Portas de Sentinel do Azure](./media/connect-cef/ports.png) 

5. Certifique-se de que o daemon está definido para escutar na porta 514, no qual está a enviar os registos.
    - Para rsyslog:<br>Certifique-se de que o ficheiro `/etc/rsyslog.conf` inclui esta configuração:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Para obter mais informações, consulte [imudp: Módulo Syslog de UDP de entrada](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) e [imtcp: Módulo de entrada de TCP Syslog](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - Para syslog-ng:<br>Certifique-se de que o ficheiro `/etc/syslog-ng/syslog-ng.conf` inclui esta configuração:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Para obter mais informações, consulte [imudp: Módulo Syslog de UDP de entrada](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) e [edição de código-fonte aberto de syslog-ng 3.16 - guia de administração](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Verifique que existe comunicação entre o Syslog daemon e o agente. Execute este comando no computador de agente Syslog: `tcpdump -A -ni any  port 25226 -vv`. Este comando mostra-lhe os registos que transmita em fluxo do dispositivo para a máquina do Syslog. Certifique-se de que os registos também estão a ser recebidos no agente.

6. Se ambos esses comandos fornecidos resultados com êxito, verifique o Log Analytics para ver se os registos estão a chegar. Todos os eventos transmitidos em fluxo a partir destas aplicações são apresentados no formato não processado no Log Analytics em `CommonSecurityLog` tipo.

7. Para verificar se existem erros ou se os registos não estão a chegar, consulte `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Se disser existem erros de falta de correspondência do formato de registo, aceda a `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` e examinar o arquivo `security_events.conf`. Certifique-se de que os seus registos corresponde ao formato regex que ver neste ficheiro.

8. Certifique-se de que o tamanho de padrão de mensagem do Syslog é limitado a 2048 bytes (2 KB). Se os registos são demasiado longos, atualize security_events utilizando este comando: `message_length_limit 4096`

10. Se os seus registos de Fortinet não estão a ser recebidos pelo agente, execute este comando, dependendo do tipo de daemon de Syslog está a utilizar, para definir o recurso e defina os registos para procurar a palavra Fortinet nos registos:
       - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Reinicie o Syslog daemon, utilizando este comando: `sudo service rsyslog restart`
       - syslog-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Reinicie o Syslog daemon, utilizando este comando: `sudo service syslog-ng restart`

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a ligar aparelhos de Fortinet a sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

