---
title: Recolher e analisar as mensagens do Syslog no Azure Monitor | Documentos da Microsoft
description: Syslog é um protocolo de registo de eventos que é comum para Linux. Este artigo descreve como configurar a recolha de mensagens do Syslog no Log Analytics e detalhes dos registos que criaram.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: magoedte
ms.openlocfilehash: 41ea6222689516f224fc23ce6a658d17f7f81866
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372306"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Origens de dados do syslog no Azure Monitor
Syslog é um protocolo de registo de eventos que é comum para Linux. Aplicativos enviará as mensagens que podem ser armazenadas no computador local ou entregues a um recoletor de Syslog. Quando é instalado o agente do Log Analytics para Linux, este configura o daemon de Syslog local para encaminhar mensagens para o agente. O agente, em seguida, envia a mensagem para o Azure Monitor, onde é criado um registo correspondente.  

> [!NOTE]
> Monitor do Azure suporta a recolha de mensagens enviadas por rsyslog ou syslog-ng, onde rsyslog é o daemon de predefinição. O daemon de syslog padrão na versão 5 da versão do Oracle Linux, CentOS e Red Hat Enterprise Linux (sysklog) não é suportado para a recolha de eventos do syslog. Para recolher dados de syslog nesta versão nessas distribuições, o [rsyslog daemon](http://rsyslog.com) deve ser instalado e configurado para substituir sysklog.
>
>

![Recolha de syslog](media/data-sources-syslog/overview.png)

Os recursos seguintes são suportados com o coletor de Syslog:

* kern
* Utilizador
* mail
* Daemon
* autenticação
* syslog
* lpr
* Notícias
* uucp
* cron
* authpriv
* ftp
* local0-local7

Para quaisquer outras instalações [configurar uma origem de dados de registos personalizado](data-sources-custom-logs.md) no Azure Monitor.
 
## <a name="configuring-syslog"></a>Configurar Syslog
O agente do Log Analytics para Linux apenas à recolha de eventos com as instalações e gravidades especificadas na respetiva configuração. Pode configurar Syslog através do portal do Azure ou através da gestão de ficheiros de configuração nos agentes do Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Configurar o Syslog no portal do Azure
Configurar o Syslog do [menu de dados nas definições avançadas](agent-data-sources.md#configuring-data-sources). Esta configuração é entregue ao arquivo de configuração em cada agente do Linux.

Pode adicionar um novo recurso, escrevendo em seu nome e clicar **+**. Para cada recurso, apenas as mensagens com as gravidades selecionadas serão recolhidas.  Verifique as gravidades do recurso específico que pretende recolher. Não é possível fornecer quaisquer critérios adicionais para filtrar mensagens.

![Configurar o Syslog](media/data-sources-syslog/configure.png)

Por predefinição, todas as alterações de configuração são automaticamente enviados por push para todos os agentes. Se quiser configurar o Syslog manualmente em cada agente do Linux, em seguida, desmarque a caixa *aplicar configuração abaixo aos meus computadores Linux*.

### <a name="configure-syslog-on-linux-agent"></a>Configurar o Syslog no agente do Linux
Quando o [agente do Log Analytics está instalado num cliente Linux](../../azure-monitor/learn/quick-collect-linux-computer.md), ele instala um ficheiro de configuração do syslog de predefinição que define o recurso e a gravidade das mensagens que são recolhidos. Pode modificar esse arquivo para alterar a configuração. O ficheiro de configuração é diferente consoante o daemon de Syslog que tem instalado o cliente.

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
O agente Log Analytics escuta de mensagens do Syslog no cliente local na porta 25224.  Quando o agente está instalado, uma configuração de syslog predefinida é aplicada e foi encontrada na seguinte localização:

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

Depois de concluir as alterações, o Syslog e o serviço de agente tem de ser reiniciada para garantir que as alterações de configuração do Log Analytics em vigor.   

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

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [registar as consultas](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.
* Uso [campos personalizados](../../azure-monitor/platform/custom-fields.md) para analisar dados de registos de syslog em campos individuais.
* [Configurar os agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) para recolher outros tipos de dados.
