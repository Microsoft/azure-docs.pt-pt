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
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 72306132f88f211180c99cd30845781667605204
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921875"
---
# <a name="connect-your-fortinet-appliance"></a>Ligar a sua aplicação da Fortinet 

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode ligar sentinela do Azure para qualquer aplicação Fortinet ao guardar os ficheiros de registo como Syslog CEF. A integração com o Azure sentinela permite-lhe executar facilmente análises e consultas em dados de ficheiro de registo da Fortinet. Para obter mais informações sobre como o Azure sentinela ingere dados CEF, consulte [aparelhos de ligar o CEF](connect-common-event-format.md).

> [!NOTE]
> - Os dados serão armazenados na localização geográfica da área de trabalho no qual está a executar sentinela do Azure.

## <a name="step-1-connect-your-fortinet-appliance-using-an-agent"></a>Passo 1: Ligar a sua aplicação Fortinet da utilização de um agente

Para ligar a aplicação da Fortinet para sentinela do Azure, terá de implementar um agente numa máquina dedicada (VM ou no local) para suportar a comunicação entre a aplicação e, em seguida, sentinela do Azure. Pode implementar o agente manualmente ou automaticamente. Implementação automática só está disponível se a sua máquina dedicada é uma nova VM que está a criar no Azure. 

Em alternativa, pode implementar o agente manualmente numa VM do Azure existente, numa VM noutra cloud ou numa máquina no local.

Para ver um diagrama de rede de ambas as opções, consulte [ligar a origens de dados](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent-in-azure"></a>Implementar o agente no Azure

1. No portal do Azure sentinela, clique em **conectores de dados** e selecione o tipo de aplicação. 

1. Sob **configuração do agente Linux Syslog**:
   - Escolher **implementação automática** se pretender criar uma nova máquina que está pré-instalado com o agente do Azure sentinela e inclui todo o necessário de configuração, conforme descrito acima. Selecione **implementação automática** e clique em **implementação automática do agente**. Isto leva-o para a página de compra para uma VM dedicada, que é conectado automaticamente à sua área de trabalho, é. A VM é um **padrão D2s v3 (2 vCPUs, 8 GB de memória)** e tem um endereço IP público.
      1. Na **implementação personalizada** página, forneça os detalhes da sua e escolha um nome de utilizador e uma palavra-passe e se concordar com os termos e condições, comprar a VM.
      1. Configure a aplicação para enviar registos utilizando as definições indicadas na página de ligação. Para o conector de formato de evento comum genérica, utilize estas definições:
         - Protocolo = UDP
         - Porta = 514
         - Recurso = Local 4
         - Formato = CEF
   - Escolher **implementação Manual** se pretender utilizar uma VM existente como a máquina Linux dedicada em que o agente do Azure sentinela deve ser instalado. 
      1. Sob **transfira e instale o agente de Syslog**, selecione **máquina virtual do Linux do Azure**. 
      1. Na **máquinas virtuais** ecrã que se abre, selecione a máquina que pretende utilizar e clique em **Connect**.
      1. No ecrã de conector, sob **configurar e reencaminhar Syslog**, defina se o daemon de Syslog está **rsyslog.d** ou **syslog-ng**. 
      1. Copie estes comandos e executá-los na sua aplicação:
          - Se tiver selecionado rsyslog.d:
              
            1. Informe o Syslog daemon para escutar local_4 de instalações e para enviar as mensagens Syslog para o agente do Azure sentinela através da porta 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Transferir e instalar o [o ficheiro de configuração de security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Onde {0} deve ser substituído pela sua área de trabalho GUID.
            
            1. Reinicie o syslog daemon `sudo service rsyslog restart`
             
          - Se selecionou syslog-ng:

              1. Informe o Syslog daemon para escutar local_4 de instalações e para enviar as mensagens Syslog para o agente do Azure sentinela através da porta 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Transferir e instalar o [o ficheiro de configuração de security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Onde {0} deve ser substituído pela sua área de trabalho GUID.

              3. Reinicie o syslog daemon `sudo service syslog-ng restart`
      2. Reinicie o agente de Syslog utilizando este comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirme que não existem erros no registo do agente ao executar este comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Implementar o agente num servidor Linux no local

Se não estiver a utilizar o Azure, implemente manualmente o agente de Azure sentinela de mensagens em fila para ser executado num servidor Linux dedicado.


1. No portal do Azure sentinela, clique em **conectores de dados** e selecione o tipo de aplicação.
1. Para criar uma VM do Linux dedicado, em **configuração do agente Linux Syslog** escolha **implementação Manual**.
   1. Sob **transfira e instale o agente de Syslog**, selecione **máquina Linux não Azure**. 
   1. Na **agente direto** ecrã que se abre, selecione **agente para Linux** para transferir o agente ou execute este comando para baixá-lo no seu computador Linux:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. No ecrã de conector, sob **configurar e reencaminhar Syslog**, defina se o daemon de Syslog está **rsyslog.d** ou **syslog-ng**. 
      1. Copie estes comandos e executá-los na sua aplicação:
         - Se tiver selecionado rsyslog:
           1. Informe o Syslog daemon para escutar local_4 de instalações e para enviar as mensagens Syslog para o agente do Azure sentinela através da porta 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Transferir e instalar o [o ficheiro de configuração de security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Onde {0} deve ser substituído pela sua área de trabalho GUID.
           3. Reinicie o syslog daemon `sudo service rsyslog restart`
         - Se selecionou syslog-ng:
            1. Informe o Syslog daemon para escutar local_4 de instalações e para enviar as mensagens Syslog para o agente do Azure sentinela através da porta 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Transferir e instalar o [o ficheiro de configuração de security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Onde {0} deve ser substituído pela sua área de trabalho GUID.
            3. Reinicie o syslog daemon `sudo service syslog-ng restart`
      1. Reinicie o agente de Syslog utilizando este comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirme que não existem erros no registo do agente ao executar este comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Passo 2: Reencaminhar registos Fortinet para o agente do Syslog

Configure Fortinet para encaminhar mensagens do Syslog no formato CEF para sua área de trabalho do Azure por meio do agente de Syslog:

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
    - Definir o **facility_name** para usar o recurso que configurou no agente. Por predefinição, o agente define isto para local4.
    - Definir o **porta de syslog** ao **514**, ou a porta definido no agente.
    - Para ativar o formato CEF em versões anteriores do FortiOS, poderá ter de executar o conjunto de comandos **csv desativar**.
 
   > [!NOTE] 
   > Para obter mais informações, vá para o [biblioteca de documentos Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Escolha a sua versão e utilizar o **'s Handbook** e **referência de mensagem do registo**.

 Para utilizar o esquema relevante no Log Analytics para os eventos da Fortinet, procure `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Passo 3: Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos começam a aparecer no Log Analytics. 

1. Certifique-se de que utiliza o recurso correto. O recurso tem de ser o mesmo na sua aplicação e no Azure sentinela. Pode verificar o ficheiro de instalação estiver a utilizar no Azure sentinela e modificá-lo no arquivo `security-config-omsagent.conf`. 

2. Certifique-se de que os registos estão a obter para a porta correta do agente do Syslog. Execute este comando no computador de agente Syslog: `tcpdump -A -ni any  port 514 -vv` Este comando mostra-lhe os registos que transmite do dispositivo para a máquina do Syslog. Certifique-se de que os registos estão a ser recebidos do dispositivo de origem na porta correta e recurso correto.

3. Certifique-se de que estão em conformidade com os registos enviados [RFC 5424](https://tools.ietf.org/html/rfc542).

4. No computador que executa o agente de Syslog, certifique-se de que estas portas 514, 25226 são abertos e está a escutar, utilizando o comando `netstat -a -n:`. Para obter mais informações sobre como utilizar este comando, consulte [netstat(8) - página de homem de Linux](https://linux.die.netman/8/netstat). Se estiver a escutar corretamente, verá isto:

   ![Portas de Sentinel do Azure](./media/connect-cef/ports.png) 

5. Certifique-se de que o daemon está definido para escutar na porta 514, no qual está a enviar os registos.
    - Para rsyslog:<br>Certifique-se de que o ficheiro `/etc/rsyslog.conf` inclui esta configuração:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Para obter mais informações, consulte [imudp: Módulo de entrada de Syslog do UDP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) e [imtcp: Módulo Syslog entrada TCP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Para syslog-ng:<br>Certifique-se de que o ficheiro `/etc/syslog-ng/syslog-ng.conf` inclui esta configuração:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Para obter mais informações, consulte [imudp: Módulo de entrada de Syslog do UDP] (para obter mais informações, consulte o [3.16 de edição de fonte aberto do syslog-ng - guia de administração do](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Verifique que existe comunicação entre o Syslog daemon e o agente. Execute este comando no computador de agente Syslog: `tcpdump -A -ni any  port 25226 -vv` Este comando mostra-lhe os registos que transmite do dispositivo para a máquina do Syslog. Certifique-se de que os registos também estão a ser recebidos no agente.

6. Se ambos esses comandos fornecidos resultados com êxito, verifique o Log Analytics para ver se os registos estão a chegar. Todos os eventos transmitidos em fluxo a partir destas aplicações são apresentados no formato não processado no Log Analytics em `CommonSecurityLog` tipo.

7. Para verificar se existem erros ou se os registos não estão a chegar, consulte `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Se disser existem erros de falta de correspondência do formato de registo, aceda a `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` e examinar o arquivo `security_events.conf`e certifique-se de que os seus registos corresponde ao formato regex ver neste ficheiro.

8. Certifique-se de que o tamanho de padrão de mensagem do Syslog é limitado a 2048 bytes (2KB). Se os registos são demasiado longos, atualize o security_events utilizando este comando: `message_length_limit 4096`

1. Se os seus registos de Fortinet não estão a ser recebidos pelo agente, execute este comando, dependendo do tipo de daemon de Syslog está a utilizar, para definir o recurso e defina os registos para procurar a palavra Fortinet nos registos:
   - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Reinicie o Syslog daemon: `sudo service rsyslog restart`
   - syslog-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Reinicie o Syslog daemon: `sudo service syslog-ng restart`

## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar aparelhos de Fortinet a sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

