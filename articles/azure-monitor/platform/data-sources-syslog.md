---
title: Coletar e analisar mensagens de syslog no Azure Monitor | Microsoft Docs
description: Syslog é um protocolo de registo de eventos que é comum para Linux. Este artigo descreve como configurar a coleta de mensagens de syslog no Log Analytics e os detalhes dos registros que eles criam.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/22/2019
ms.openlocfilehash: ffc6c48a6b49edded97570fd1ac421933b5f6b72
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450641"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Fontes de dados syslog no Azure Monitor
Syslog é um protocolo de registo de eventos que é comum para Linux. Aplicativos enviará as mensagens que podem ser armazenadas no computador local ou entregues a um recoletor de Syslog. Quando o agente de Log Analytics para Linux é instalado, ele configura o daemon do syslog local para encaminhar mensagens para o agente. Em seguida, o agente envia a mensagem para Azure Monitor onde um registro correspondente é criado.  

> [!NOTE]
> Azure Monitor dá suporte à coleta de mensagens enviadas por rsyslog ou syslog-ng, em que rsyslog é o daemon padrão. O daemon de syslog padrão na versão 5 da versão do Oracle Linux, CentOS e Red Hat Enterprise Linux (sysklog) não é suportado para a recolha de eventos do syslog. Para recolher dados de syslog nesta versão nessas distribuições, o [rsyslog daemon](http://rsyslog.com) deve ser instalado e configurado para substituir sysklog.
>
>

![Recolha de syslog](media/data-sources-syslog/overview.png)

Os seguintes recursos têm suporte com o coletor de syslog:

* Kern
* Utilizador
* correio
* Demonstração
* propósito
* syslog
* LPR
* news
* uucp
* cron
* authpriv
* ftp
* local0-local7

Para qualquer outro recurso, [Configure uma fonte de dados de logs personalizados](data-sources-custom-logs.md) no Azure monitor.
 
## <a name="configuring-syslog"></a>Configurar Syslog
O agente Log Analytics para Linux coletará apenas eventos com as instalações e severidades especificadas em sua configuração. Pode configurar Syslog através do portal do Azure ou através da gestão de ficheiros de configuração nos agentes do Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Configurar o Syslog no portal do Azure
Configure o syslog no [menu dados em configurações avançadas](agent-data-sources.md#configuring-data-sources). Esta configuração é entregue ao arquivo de configuração em cada agente do Linux.

Você pode adicionar um novo recurso selecionando primeiro a opção **aplicar abaixo a configuração em meus computadores** e, em seguida, digitando seu nome e clicando em **+** . Para cada recurso, apenas as mensagens com as gravidades selecionadas serão recolhidas.  Verifique as gravidades do recurso específico que pretende recolher. Não é possível fornecer quaisquer critérios adicionais para filtrar mensagens.

![Configurar o Syslog](media/data-sources-syslog/configure.png)

Por predefinição, todas as alterações de configuração são automaticamente enviados por push para todos os agentes. Se você quiser configurar o syslog manualmente em cada agente do Linux, desmarque a caixa *aplicar configuração abaixo a meus computadores*.

### <a name="configure-syslog-on-linux-agent"></a>Configurar o Syslog no agente do Linux
Quando o [agente de log Analytics é instalado em um cliente Linux](../../azure-monitor/learn/quick-collect-linux-computer.md), ele instala um arquivo de configuração de syslog padrão que define a instalação e a gravidade das mensagens que são coletadas. Pode modificar esse arquivo para alterar a configuração. O ficheiro de configuração é diferente consoante o daemon de Syslog que tem instalado o cliente.

> [!NOTE]
> Se editar a configuração do syslog, tem de reiniciar o syslog daemon para que as alterações entrem em vigor.
>
>

#### <a name="rsyslog"></a>rsyslog
O ficheiro de configuração para rsyslog está localizado em **/etc/rsyslog.d/95-omsagent.conf**. Seu conteúdo padrão é mostrado abaixo. Esta secção reúne as mensagens do syslog enviadas a partir do agente local para todas as instalações com um nível de aviso ou superior.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

Pode remover um recurso ao remover a secção do ficheiro de configuração. Pode limitar as gravidades que são recolhidas para um recurso específico ao modificar a entrada desse recurso. Por exemplo, para limitar as instalações de utilizador para as mensagens com uma gravidade de erro ou superior seria modificar essa linha do ficheiro de configuração para o seguinte:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
O ficheiro de configuração para syslog-ng é o local em **/etc/syslog-ng/syslog-ng.conf**.  Seu conteúdo padrão é mostrado abaixo. Esta secção reúne as mensagens do syslog enviadas a partir do agente local para todas as instalações e todas as gravidades.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Pode remover um recurso ao remover a secção do ficheiro de configuração. Pode limitar as gravidades que são recolhidas para um recurso específico, removendo-os partir da respetiva lista.  Por exemplo, para limitar as instalações de utilizador para mensagens basta alerta e críticas, deverá modificar essa secção do ficheiro de configuração para o seguinte:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Recolha de dados de portas adicionais do Syslog
O agente de Log Analytics escuta mensagens de syslog no cliente local na porta 25224.  Quando o agente está instalado, uma configuração de syslog predefinida é aplicada e foi encontrada na seguinte localização:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Pode alterar o número da porta através da criação de dois ficheiros de configuração: um ficheiro de configuração FluentD e um arquivo de rsyslog-ou-syslog-ng consoante o daemon de Syslog que instalou.  

* O ficheiro de configuração FluentD deve ser um novo ficheiro, localizado em: `/etc/opt/microsoft/omsagent/conf/omsagent.d` e substitua o valor no **porta** entrada com o seu número de porta personalizado.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* Para rsyslog, deve criar um novo ficheiro de configuração localizado em: `/etc/rsyslog.d/` e substitua o valor % SYSLOG_PORT % com o seu número de porta personalizado.  

    > [!NOTE]
    > Se modificar este valor no ficheiro de configuração `95-omsagent.conf`, esta será substituída quando o agente aplica-se uma configuração predefinida.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* A configuração do syslog-ng deve ser modificada ao copiar a configuração de exemplo mostrada abaixo e adicionar as definições de modificação personalizadas ao fim do ficheiro de configuração do syslog ng.conf localizado no `/etc/syslog-ng/`. Fazer **não** utilize a etiqueta predefinida **% WORKSPACE_ID % _oms** ou **% WORKSPACE_ID_OMS**, definir uma etiqueta personalizada para o ajudar a distinguir as suas alterações.  

    > [!NOTE]
    > Se modificar os valores predefinidos no ficheiro de configuração, estas serão substituídas quando o agente aplica-se uma configuração predefinida.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Depois de concluir as alterações, o syslog e o serviço do agente de Log Analytics precisam ser reiniciados para garantir que as alterações de configuração tenham efeito.   

## <a name="syslog-record-properties"></a>Propriedades de registo do syslog
Registos de syslog têm um tipo de **Syslog** e ter as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador que o evento foi recolhido a partir de. |
| Recurso |Define a parte do sistema que gerou a mensagem. |
| HostIP |Endereço IP do sistema enviar a mensagem. |
| nome de anfitrião |Nome do sistema enviar a mensagem. |
| SeverityLevel |Nível de gravidade do evento. |
| SyslogMessage |Texto da mensagem. |
| ProcessID |ID do processo que gerou a mensagem. |
| eventTime |Data e hora em que o evento foi gerado. |

## <a name="log-queries-with-syslog-records"></a>Consultas de registo com registos de Syslog
A tabela seguinte fornece exemplos diferentes de consultas de registo que obter registos de Syslog.

| Consulta | Descrição |
|:--- |:--- |
| Syslog |Todos os Syslogs. |
| Syslog &#124; onde SeverityLevel = = "erro" |Todos os registos de Syslog com gravidade do erro. |
| Syslog &#124; resumir AggregatedValue = count () por computador |Contagem de Syslog registos por computador. |
| Syslog &#124; resumir AggregatedValue = count () por instalação |Registos de contagem de Syslog por instalação. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [registar as consultas](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.
* Uso [campos personalizados](../../azure-monitor/platform/custom-fields.md) para analisar dados de registos de syslog em campos individuais.
* [Configurar os agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) para recolher outros tipos de dados.
