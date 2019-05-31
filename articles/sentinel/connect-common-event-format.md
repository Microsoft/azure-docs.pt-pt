---
title: Ligar dados CEF a pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Saiba como ligar a dados CEF ao sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: rkarlin
ms.openlocfilehash: 8e711c0586ce63d4293e2fb0914bbe884b55971f
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389955"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Ligue a sua solução externa com o formato de evento comum

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode ligar o Azure sentinela com uma solução externa que permite-lhe guardar os ficheiros de registo do Syslog. Se a aplicação permite-lhe guardar os registos como Syslog Common Event Format (CEF), a integração com o Azure sentinela permite-lhe executar facilmente análises e consultas em todos os dados.

> [!NOTE] 
> Dados são armazenados na localização geográfica da área de trabalho no qual está a executar sentinela do Azure.

## <a name="how-it-works"></a>Como funciona

A ligação entre sentinela do Azure e a sua aplicação de CEF acontece em três passos:

1. Na aplicação da terá de definir esses valores para que a aplicação envia as informações necessárias registos no formato necessário para o agente de Syslog sentinela do Azure. Pode modificar estes parâmetros na sua aplicação, desde que modificar também no daemon de Syslog no agente sentinela do Azure.
    - Protocolo = UDP
    - Porta = 514
    - Recurso = Local 4
    - Formato = CEF
2. O agente de Syslog recolhe os dados e envia-os em segurança para o Log Analytics, onde é analisado e enriquecida.
3. O agente armazena os dados numa área de trabalho do Log Analytics, para que possa ser consultada conforme necessário, com a análise, regras de correlação e dashboards.

> [!NOTE]
> O agente pode recolher registos de várias origens, mas tem de estar instalado no computador de proxy dedicada.

## <a name="step-1-connect-to-your-cef-appliance-via-dedicated-azure-vm"></a>Passo 1: Ligar à sua aplicação de CEF por meio de VM do Azure dedicada

Terá de implementar um agente num computador Linux dedicado (VM ou no local) para suportar a comunicação entre a aplicação e, em seguida, sentinela do Azure. Pode implementar o agente manualmente ou automaticamente. Implementação automática é baseada em modelos do Resource Manager e pode ser utilizada apenas se a sua máquina Linux dedicada é uma nova VM que está a criar no Azure.

 ![CEF no Azure](./media/connect-cef/cef-syslog-azure.png)

Em alternativa, pode implementar o agente manualmente numa VM do Azure existente, numa VM noutra cloud ou numa máquina no local. 

 ![CEF no local](./media/connect-cef/cef-syslog-onprem.png)

### <a name="deploy-the-agent-in-azure"></a>Implementar o agente no Azure


1. No portal do Azure sentinela, clique em **conectores de dados** e selecione o tipo de aplicação. 

1. Sob **configuração do agente Linux Syslog**:
   - Escolher **implementação automática** se pretender criar uma nova máquina que está pré-instalado com o agente do Azure sentinela e inclui todo o necessário de configuração, conforme descrito acima. Selecione **implementação automática** e clique em **implementação automática do agente**. Isto leva-o para a página de compra para uma VM do Linux dedicado que é conectado automaticamente à sua área de trabalho. A VM é um **padrão D2s v3 (2 vCPUs, 8 GB de memória)** e tem um endereço IP público.
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
            
            1. Reinicie o syslog daemon `sudo service rsyslog restart`<br> Para obter mais informações, consulte o [rsyslog documentação](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
          - Se selecionou syslog-ng:

              1. Informe o Syslog daemon para escutar local_4 de instalações e para enviar as mensagens Syslog para o agente do Azure sentinela através da porta 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Transferir e instalar o [o ficheiro de configuração de security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Onde {0} deve ser substituído pela sua área de trabalho GUID.

              3. Reinicie o syslog daemon `sudo service syslog-ng restart` <br>Para obter mais informações, consulte o [documentação de syslog-ng](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
      2. Reinicie o agente de Syslog utilizando este comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirme que não existem erros no registo do agente ao executar este comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

 Para utilizar o esquema relevante no Log Analytics para os eventos CEF, procure `CommonSecurityLog`.


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
  
 Para utilizar o esquema relevante no Log Analytics para os eventos CEF, procure `CommonSecurityLog`.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Passo 2: Reencaminhar registos Common Event Format (CEF) para o agente do Syslog

Defina a sua solução de segurança para enviar mensagens do Syslog no formato CEF para o agente do Syslog. Certifique-se de que utilizar os mesmos parâmetros que são apresentados na sua configuração de agente. Normalmente, são:

- Porta 514
- Recurso local4

## <a name="step-3-validate-connectivity"></a>Passo 3: Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos começam a aparecer no Log Analytics. 

1. Certifique-se de que utiliza o recurso correto. O recurso tem de ser o mesmo na sua aplicação e no Azure sentinela. Pode verificar o ficheiro de instalação estiver a utilizar no Azure sentinela e modificá-lo no arquivo `security-config-omsagent.conf`. 

2. Certifique-se de que os registos estão a obter para a porta correta do agente do Syslog. Execute este comando no computador de agente Syslog: `tcpdump -A -ni any  port 514 -vv` Este comando mostra-lhe os registos que transmite do dispositivo para a máquina do Syslog. Certifique-se de que os registos estão a ser recebidos do dispositivo de origem na porta correta e recurso correto.

3. Certifique-se de que estão em conformidade com os registos enviados [RFC 5424](https://tools.ietf.org/html/rfc542).

4. No computador que executa o agente de Syslog, certifique-se de que estas portas 514, 25226 são abertos e está a escutar, utilizando o comando `netstat -a -n:`. Para obter mais informações sobre como utilizar este comando, consulte [netstat(8) - página de homem de Linux](https://linux.die.net/man/8/netstat). Se estiver a escutar corretamente, verá isto:

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


## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar aparelhos de CEF ao sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

