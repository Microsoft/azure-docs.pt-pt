---
title: Coletar e analisar mensagens de syslog no Azure Monitor | Microsoft Docs
description: O syslog é um protocolo de log de eventos comum ao Linux. Este artigo descreve como configurar a coleta de mensagens de syslog no Log Analytics e os detalhes dos registros que eles criam.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 03/22/2019
ms.openlocfilehash: 5daa9e99ccf71da680dad00b06c4e53f6c8b4e81
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932421"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Fontes de dados syslog no Azure Monitor
O syslog é um protocolo de log de eventos comum ao Linux. Os aplicativos enviarão mensagens que podem ser armazenadas no computador local ou entregues a um coletor de syslog. Quando o agente de Log Analytics para Linux é instalado, ele configura o daemon do syslog local para encaminhar mensagens para o agente. Em seguida, o agente envia a mensagem para Azure Monitor onde um registro correspondente é criado.  

> [!NOTE]
> Azure Monitor dá suporte à coleta de mensagens enviadas por rsyslog ou syslog-ng, em que rsyslog é o daemon padrão. O daemon de syslog padrão na versão 5 de Red Hat Enterprise Linux, CentOS e versão de Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog desta versão dessas distribuições, o [daemon rsyslog](http://rsyslog.com) deve ser instalado e configurado para substituir o sysklog.
>
>

![Coleção de syslog](media/data-sources-syslog/overview.png)

Os seguintes recursos têm suporte com o coletor de syslog:

* Kern
* Usuário
* Mescla
* demonstração
* propósito
* syslog
* LPR
* Notícias
* uucp
* cron
* authpriv
* FTP
* local0-local7

Para qualquer outro recurso, [Configure uma fonte de dados de logs personalizados](data-sources-custom-logs.md) no Azure monitor.
 
## <a name="configuring-syslog"></a>Configurando o syslog
O agente Log Analytics para Linux coletará apenas eventos com as instalações e severidades especificadas em sua configuração. Você pode configurar o syslog por meio do portal do Azure ou gerenciando arquivos de configuração em seus agentes do Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Configurar o syslog no portal do Azure
Configure o syslog no [menu dados em configurações avançadas](agent-data-sources.md#configuring-data-sources). Essa configuração é entregue ao arquivo de configuração em cada agente do Linux.

Você pode adicionar um novo recurso digitando seu nome e clicando em **+** . Para cada recurso, somente as mensagens com as severidades selecionadas serão coletadas.  Verifique as severidades para a instalação específica que você deseja coletar. Você não pode fornecer nenhum critério adicional para filtrar mensagens.

![Configurar syslog](media/data-sources-syslog/configure.png)

Por padrão, todas as alterações de configuração são automaticamente enviadas para todos os agentes. Se você quiser configurar o syslog manualmente em cada agente do Linux, desmarque a caixa *aplicar configuração abaixo a meus computadores*.

### <a name="configure-syslog-on-linux-agent"></a>Configurar syslog no agente do Linux
Quando o [agente de log Analytics é instalado em um cliente Linux](../../azure-monitor/learn/quick-collect-linux-computer.md), ele instala um arquivo de configuração de syslog padrão que define a instalação e a gravidade das mensagens que são coletadas. Você pode modificar esse arquivo para alterar a configuração. O arquivo de configuração é diferente dependendo do daemon do syslog que o cliente instalou.

> [!NOTE]
> Se você editar a configuração do syslog, deverá reiniciar o daemon do syslog para que as alterações entrem em vigor.
>
>

#### <a name="rsyslog"></a>rsyslog
O arquivo de configuração para rsyslog está localizado em **/etc/rsyslog.d/95-omsagent.conf**. Seu conteúdo padrão é mostrado abaixo. Isso coleta mensagens de syslog enviadas do agente local para todos os recursos com um nível de aviso ou superior.

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

Você pode remover um recurso removendo sua seção do arquivo de configuração. Você pode limitar as severidades que são coletadas para uma determinada instalação, modificando a entrada do recurso. Por exemplo, para limitar o recurso de usuário a mensagens com uma severidade de erro ou mais alto, você modificaria essa linha do arquivo de configuração para o seguinte:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
O arquivo de configuração para syslog-ng é o local em **/etc/syslog-ng/syslog-ng.conf**.  Seu conteúdo padrão é mostrado abaixo. Isso coleta mensagens de syslog enviadas do agente local para todas as instalações e todas as severidades.   

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

Você pode remover um recurso removendo sua seção do arquivo de configuração. Você pode limitar as severidades que são coletadas para um determinado recurso removendo-as de sua lista.  Por exemplo, para limitar o recurso do usuário apenas a alertas e mensagens críticas, você modificaria essa seção do arquivo de configuração para o seguinte:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Coletando dados de portas de syslog adicionais
O agente de Log Analytics escuta mensagens de syslog no cliente local na porta 25224.  Quando o agente é instalado, uma configuração de syslog padrão é aplicada e encontrada no seguinte local:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Você pode alterar o número da porta criando dois arquivos de configuração: um arquivo de configuração Fluentd e um arquivo rsyslog-ou-syslog-ng, dependendo do daemon syslog que você instalou.  

* O arquivo de configuração Fluentd deve ser um novo arquivo localizado em: `/etc/opt/microsoft/omsagent/conf/omsagent.d` e substituir o valor na entrada de **porta** pelo número da porta personalizada.

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

* Para rsyslog, você deve criar um novo arquivo de configuração localizado em: `/etc/rsyslog.d/` e substituir o valor% SYSLOG_PORT% pelo seu número de porta personalizado.  

    > [!NOTE]
    > Se você modificar esse valor no arquivo de configuração `95-omsagent.conf`, ele será substituído quando o agente aplicar uma configuração padrão.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* A configuração do syslog-ng deve ser modificada copiando-se a definição de exemplo mostrada abaixo e adicionando as configurações personalizadas modificadas ao final do arquivo de configuração syslog-ng. conf localizado em `/etc/syslog-ng/`. **Não** use o rótulo padrão **% WORKSPACE_ID% _Oms** ou **% WORKSPACE_ID_OMS**, defina um rótulo personalizado para ajudar a distinguir suas alterações.  

    > [!NOTE]
    > Se você modificar os valores padrão no arquivo de configuração, eles serão substituídos quando o agente aplicar uma configuração padrão.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Depois de concluir as alterações, o syslog e o serviço do agente de Log Analytics precisam ser reiniciados para garantir que as alterações de configuração tenham efeito.   

## <a name="syslog-record-properties"></a>Propriedades do registro do syslog
Os registros de syslog têm um tipo de **syslog** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador do qual o evento foi coletado. |
| Saldo |Define a parte do sistema que gerou a mensagem. |
| HostIP |Endereço IP do sistema que está enviando a mensagem. |
| Nome do host |Nome do sistema que está enviando a mensagem. |
| Nível |Nível de severidade do evento. |
| SyslogMessage |Texto da mensagem. |
| ProcessID |ID do processo que gerou a mensagem. |
| eventTime |Data e hora em que o evento foi gerado. |

## <a name="log-queries-with-syslog-records"></a>Consultas de log com registros de syslog
A tabela a seguir fornece exemplos diferentes de consultas de log que recuperam registros syslog.

| Consulta | Descrição |
|:--- |:--- |
| Syslog |Todos os syslogs. |
| Syslog &#124; onde nível = = "Error" |Todos os registros de syslog com severidade de erro. |
| Syslog &#124; resume AggregatedValue = Count () por computador |Contagem de registros de syslog por computador. |
| Syslog &#124; resume AggregatedValue = Count () por recurso |Contagem de registros de syslog por recurso. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [consultas de log](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções.
* Use [campos personalizados](../../azure-monitor/platform/custom-fields.md) para analisar dados de registros de syslog em campos individuais.
* [Configure agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) para coletar outros tipos de dados.
