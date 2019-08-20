---
title: Conectar dados de CEF à visualização do Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de CEF ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2019
ms.author: rkarlin
ms.openlocfilehash: 28def73926294a025d70844e535a0856153ae30a
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611929"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Conectar sua solução externa usando o formato de evento comum

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode conectar o Azure Sentinel a uma solução externa que permite salvar arquivos de log no syslog. Se seu dispositivo permitir que você salve logs como CEF (formato comum de evento) do syslog, a integração com o Azure Sentinel permite que você execute facilmente análises e consultas em todos os dados.

> [!NOTE] 
> Os dados são armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="how-it-works"></a>Como funciona

A conexão entre o Azure Sentinel e seu dispositivo CEF ocorre em três etapas:

1. No dispositivo, você precisa definir esses valores para que o dispositivo envie os logs necessários no formato necessário para o agente de syslog do Azure Sentinel, com base na Microsoft Monitoring Agent. Você pode modificar esses parâmetros em seu dispositivo, contanto que também os modifique no daemon do syslog no agente do Azure Sentinel.
    - Protocolo = UDP
    - Porta = 514
    - Instalação = Local4
    - Formato = CEF
2. O agente de syslog coleta os dados e os envia com segurança para Log Analytics, onde são analisados e aprimorados.
3. O agente armazena os dados em um espaço de trabalho Log Analytics para que possa ser consultado conforme necessário, usando análise, regras de correlação e painéis.

> [!NOTE]
> O agente pode coletar logs de várias fontes, mas deve ser instalado no computador dedicado.


 ![CEF no Azure](./media/connect-cef/cef-syslog-azure.png)

Como alternativa, você pode implantar o agente manualmente em uma VM do Azure existente, em uma VM em outra nuvem ou em um computador local. 

 ![CEF local](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Considerações de segurança

Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, você pode configurar sua rede para se alinhar com sua política de segurança de rede corporativa e alterar as portas e protocolos no daemon para se alinhar com seus requisitos. Você pode usar as seguintes instruções para melhorar sua configuração de segurança de computador:  [VM segura no Azure](../virtual-machines/linux/security-policy.md), [práticas recomendadas para segurança de rede](../security/fundamentals/network-best-practices.md).

Para usar a comunicação TLS entre a solução de segurança e a máquina syslog, você precisará configurar o daemon syslog (rsyslog ou syslog-ng) para se comunicar em TLS: [Criptografar o tráfego de syslog com TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [criptografando mensagens de log com TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).


## <a name="step-1-configure-your-syslog-vm"></a>Passo 1: Configurar sua VM do syslog

Você precisa implantar um agente em um computador Linux dedicado (VM ou local) para dar suporte à comunicação entre o dispositivo e o Azure Sentinel. 

> [!NOTE]
> Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, você pode configurar sua rede para se alinhar com sua política de segurança de rede corporativa e alterar as portas e protocolos no daemon para se alinhar com seus requisitos. 


1. No portal do Azure Sentinel, clique em conectores de **dados** e selecione o **formato de evento comum (CEF)** e, em seguida, **abra a página conector**. 

1. Em **baixar e instalar o agente de syslog**, selecione o tipo de computador, o Azure ou o local. 
1. Na tela **máquinas virtuais** que é aberta, selecione o computador que você deseja usar e clique em **conectar**.
1. Se você escolher **baixar e instalar o agente para máquinas virtuais Linux do Azure**, selecione o computador e clique em **conectar**. Se você escolheu **baixar e instalar o agente para máquinas virtuais Linux que não são do Azure**, na tela **agente direto** , execute o script em **baixar e integrar agente para Linux**.
1. Na tela do conector CEF, em **configurar e encaminhar syslog**, defina se o daemon do syslog é **rsyslog. d** ou **syslog-ng**. 
1. Copie esses comandos e execute-os em seu dispositivo:
    - Se você selecionou rsyslog. d:
              
       1. Diga ao daemon do syslog para escutar no recurso local_4 e enviar as mensagens do syslog para o agente do Azure Sentinel usando a porta 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Baixe e instale o [arquivo de configuração security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente syslog para escutar na porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Onde {0} deve ser substituído pelo GUID do espaço de trabalho.
            
       1. Reiniciar o daemon do syslog`sudo service rsyslog restart`<br> Para obter mais informações, consulte a [documentação do rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
    - Se você selecionou syslog-ng:
       1. Diga ao daemon do syslog para escutar no recurso local_4 e enviar as mensagens do syslog para o agente do Azure Sentinel usando a porta 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
       2. Baixe e instale o [arquivo de configuração security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente syslog para escutar na porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Onde {0} deve ser substituído pelo GUID do espaço de trabalho.

        3. Reiniciar o daemon do syslog`sudo service syslog-ng restart` <br>Para obter mais informações, consulte a [documentação do syslog-ng](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
1. Reinicie o agente de syslog usando este comando:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Confirme se não há erros no log do agente executando este comando:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

Para usar o esquema relevante em Log Analytics para os eventos CEF, procure `CommonSecurityLog`.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Passo 2: Encaminhar logs de CEF (formato de evento comum) para o agente de syslog

Defina sua solução de segurança para enviar mensagens de syslog no formato CEF para o agente de syslog. Certifique-se de usar os mesmos parâmetros que aparecem na configuração do agente. Normalmente, eles são:

- Porta 514
- Local4 de instalação

## <a name="step-3-validate-connectivity"></a>Passo 3: Validar conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

1. Certifique-se de usar a instalação correta. O recurso deve ser o mesmo em seu dispositivo e no Azure Sentinel. Você pode verificar qual arquivo de recurso está usando no Azure Sentinel e modificá-lo no arquivo `security-config-omsagent.conf`. 

2. Verifique se os logs estão chegando à porta certa no agente de syslog. Execute este comando no computador do agente de syslog: `tcpdump -A -ni any  port 514 -vv`Esse comando mostra os logs que o transmite do dispositivo para o computador syslog. Verifique se os logs estão sendo recebidos do dispositivo de origem na porta correta e no recurso certo.

3. Certifique-se de que os logs enviados estão em conformidade com a [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. No computador que executa o agente de syslog, verifique se essas portas 514, 25226 estão abertas e ouvindo, usando o `netstat -a -n:`comando. Para obter mais informações sobre como usar esse comando, consulte [netstat (8)-página do manual do Linux](https://linux.die.net/man/8/netstat). Se ele estiver escutando corretamente, você verá:

   ![Portas do Azure Sentinel](./media/connect-cef/ports.png) 

5. Verifique se o daemon está definido para escutar na porta 514, na qual você está enviando os logs.
    - Para rsyslog:<br>Certifique-se de que `/etc/rsyslog.conf` o arquivo inclui essa configuração:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Para obter mais informações, [consulte imudp: Módulo](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) de entrada do syslog [UDP e imtcp: Módulo de entrada do syslog TCP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Para syslog-ng:<br>Certifique-se de que `/etc/syslog-ng/syslog-ng.conf` o arquivo inclui essa configuração:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Para obter mais informações, consulte [syslog-ng Open Source Edition 3,16-guia de administração](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Verifique se há comunicação entre o daemon do syslog e o agente. Execute este comando no computador do agente de syslog: `tcpdump -A -ni any  port 25226 -vv`Esse comando mostra os logs que o transmite do dispositivo para o computador syslog. Verifique se os logs também estão sendo recebidos no agente.

6. Se ambos os comandos forneceram resultados bem-sucedidos, verifique Log Analytics para ver se os logs estão chegando. Todos os eventos transmitidos por meio desses dispositivos aparecem em formato bruto no `CommonSecurityLog` log Analytics em tipo.

7. Para verificar se há erros ou se os logs não chegam, examine `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Se houver erros de incompatibilidade de formato de log, vá `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` para e examine o arquivo `security_events.conf`e verifique se os logs correspondem ao formato Regex que você vê neste arquivo.

8. Verifique se o tamanho padrão da mensagem syslog é limitado a 2048 bytes (2 KB). Se os logs forem muito longos, atualize o security_events. conf usando este comando:`message_length_limit 4096`


## <a name="next-steps"></a>Passos Seguintes
Neste documento, você aprendeu a conectar os dispositivos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

