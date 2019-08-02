---
title: Conectar a Fortinet data à visualização do Azure Sentinel | Microsoft Docs
description: Saiba como conectar o Fortinet data ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 339b8c1b59720989016f68fdb94fae30c26b42f0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679272"
---
# <a name="connect-your-fortinet-appliance"></a>Conecte seu dispositivo Fortinet

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço. Não é recomendável para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode conectar o Azure Sentinel a qualquer dispositivo da Fortinet salvando os arquivos de log como CEF (formato comum de evento) do syslog. Com a integração com o Azure Sentinel, você pode facilmente executar análises e consultas nos dados do arquivo de log da Fortinet. Para obter mais informações sobre como o Azure Sentinel ingeri dados de CEF, consulte [conectar dispositivos CEF](connect-common-event-format.md).

> [!NOTE]
> Os dados são armazenados na localização geográfica do espaço de trabalho no qual você executa o Azure Sentinel.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Passo 1: Conectar seu dispositivo Fortinet usando um agente

Para conectar seu dispositivo Fortinet ao Azure Sentinel, implante um agente em uma VM dedicada ou em um computador local para dar suporte à comunicação entre o dispositivo e o Azure Sentinel. 

Você também pode implantar o agente manualmente em uma VM do Azure existente, em uma VM em outra nuvem ou em um computador local.

> [!NOTE]
> Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, você pode configurar sua rede para se alinhar com sua política de segurança de rede corporativa e alterar as portas e protocolos no daemon para se alinhar com seus requisitos. 

Para ver um diagrama de rede de ambas as opções, consulte [conectar fontes de dados](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent"></a>Implantar o agente

1. No portal do Azure Sentinel, clique em conectores de **dados** e selecione **Fortinet** e, em seguida, **abra a página conector**. 

1. Em **baixar e instalar o agente de syslog**, selecione o tipo de computador, o Azure ou o local. 
1. Na tela **máquinas virtuais** que é aberta, selecione o computador que você deseja usar e clique em **conectar**.
1. Se você escolher **baixar e instalar o agente para máquinas virtuais Linux do Azure**, selecione o computador e clique em **conectar**. Se você escolheu **baixar e instalar o agente para máquinas virtuais Linux que não são do Azure**, na tela **agente direto** , execute o script em **baixar e integrar agente para Linux**.
1. Na tela do conector, em **configurar e encaminhar syslog**, defina se o daemon do syslog é **rsyslog. d** ou **syslog-ng**. 
1. Copie esses comandos e execute-os em seu dispositivo:
   - Se você selecionou rsyslog. d:
            
     1. Diga ao daemon do syslog para escutar no recurso local_4 e enviar as mensagens do syslog para o agente do Azure Sentinel usando a porta 25226. Use este comando:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
     1. Baixe e instale o [arquivo de configuração security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente syslog para escutar na porta 25226. Use este comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` onde {0} deve ser substituído pelo GUID do espaço de trabalho.
     1. Reinicie o daemon do syslog usando este comando:`sudo service rsyslog restart`
            
   - Se você selecionou syslog-ng:

      1. Diga ao daemon do syslog para escutar no recurso local_4 e enviar as mensagens do syslog para o agente do Azure Sentinel usando a porta 25226. Use este comando:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      1. Baixe e instale o [arquivo de configuração security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente syslog para escutar na porta 25226. Use este comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` onde {0} deve ser substituído pelo GUID do espaço de trabalho.
      1. Reinicie o daemon do syslog usando este comando:`sudo service syslog-ng restart`
1. Reinicie o agente de syslog usando este comando:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Confirme se não há erros no log do agente executando este comando:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Passo 2: Encaminhe os logs da Fortinet para o agente de syslog

Configure a Fortinet para encaminhar mensagens de syslog no formato CEF para seu espaço de trabalho do Azure por meio do agente de syslog.

1. Abra a CLI no seu dispositivo Fortinet e execute os seguintes comandos:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Substitua o **endereço IP** do servidor pelo endereço IP do agente.
    - Defina **facility_name** para usar o recurso configurado no agente. Por padrão, o agente define isso como local4.
    - Defina a **porta do syslog** como **514** ou a porta definida no agente.
    - Para habilitar o formato CEF nas versões iniciais do FortiOS, talvez seja necessário executar o comando Set **CSV Disable**.
 
   > [!NOTE] 
   > Para obter mais informações, vá para a [biblioteca de documentos do Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Selecione sua versão e use o **manual** e a **referência de mensagem de log**.

 Para usar o esquema relevante no Azure Monitor Log Analytics para os eventos da Fortinet, procure `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Passo 3: Validar conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

1. Certifique-se de usar a instalação correta. O recurso deve ser o mesmo em seu dispositivo e no Azure Sentinel. Você pode verificar qual arquivo de recurso está usando no Azure Sentinel e modificá-lo no arquivo `security-config-omsagent.conf`. 

2. Verifique se os logs estão chegando à porta certa no agente de syslog. Execute este comando no computador do agente de syslog `tcpdump -A -ni any  port 514 -vv`:. Esse comando mostra os logs que transmitem do dispositivo para o computador syslog. Verifique se os logs estão sendo recebidos do dispositivo de origem na porta correta e no recurso certo.

3. Certifique-se de que os logs enviados estão em conformidade com a [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. No computador que executa o agente de syslog, verifique se as portas 514 e 25226 estão abertas e ouvindo usando o comando `netstat -a -n:`. Para obter mais informações sobre como usar esse comando, consulte [netstat (8)-página do manual do Linux](https://linux.die.net/man/8/netstat). Se ele estiver escutando corretamente, você verá:

   ![Portas do Azure Sentinel](./media/connect-cef/ports.png) 

5. Verifique se o daemon está definido para escutar na porta 514, na qual você está enviando os logs.
    - Para rsyslog:<br>Certifique-se de que `/etc/rsyslog.conf` o arquivo inclui essa configuração:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Para obter mais informações, [consulte imudp: Módulo](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) de entrada do syslog [UDP e imtcp: Módulo](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)de entrada do syslog TCP.

   - Para syslog-ng:<br>Certifique-se de que `/etc/syslog-ng/syslog-ng.conf` o arquivo inclui essa configuração:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Para obter mais informações, [consulte imudp: Módulo](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) de entrada do syslog UDP e [syslog-ng Open Source Edition 3,16-guia de administração](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Verifique se há comunicação entre o daemon do syslog e o agente. Execute este comando no computador do agente de syslog `tcpdump -A -ni any  port 25226 -vv`:. Esse comando mostra os logs que transmitem do dispositivo para o computador syslog. Verifique se os logs também estão sendo recebidos no agente.

6. Se ambos os comandos forneceram resultados bem-sucedidos, verifique Log Analytics para ver se os logs estão chegando. Todos os eventos transmitidos por meio desses dispositivos aparecem em formato bruto no `CommonSecurityLog` log Analytics em tipo.

7. Para verificar se há erros ou se os logs não chegam, examine `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Se ele informar que há erros de incompatibilidade de formato de `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` log, vá para e `security_events.conf`examine o arquivo. Verifique se os logs correspondem ao formato Regex que você vê neste arquivo.

8. Verifique se o tamanho padrão da mensagem syslog é limitado a 2048 bytes (2 KB). Se os logs forem muito longos, atualize security_events. conf usando este comando:`message_length_limit 4096`

10. Se os logs da Fortinet não estiverem sendo recebidos pelo agente, execute este comando, dependendo do tipo de daemon de syslog que você está usando, para definir o recurso e definir os logs para pesquisar a palavra Fortinet nos logs:
       - rsyslog. d:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Reinicie o daemon do syslog usando este comando:`sudo service rsyslog restart`
       - syslog-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Reinicie o daemon do syslog usando este comando:`sudo service syslog-ng restart`

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, você aprendeu a conectar o Fortinet appliances ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

