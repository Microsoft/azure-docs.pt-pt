---
title: Recolher fontes de dados do Syslog com o agente Log Analytics no Azure Monitor
description: Syslog é um protocolo de registo de eventos que é comum ao Linux. Este artigo descreve como configurar a recolha de mensagens Syslog no Log Analytics e detalhes dos registos que criam.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 2d86983c8ed6c738e4b4e96d8d291dee4dc4d87d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440625"
---
# <a name="collect-syslog-data-sources-with-log-analytics-agent"></a>Recolher fontes de dados Syslog com o agente Log Analytics
Syslog é um protocolo de registo de eventos que é comum ao Linux. As aplicações enviarão mensagens que podem ser armazenadas na máquina local ou entregues a um colecionador Syslog. Quando o agente Log Analytics do Linux é instalado, configura o daemon Syslog local para transmitir mensagens ao agente. Em seguida, o agente envia a mensagem para o Azure Monitor, onde é criado um registo correspondente.  

> [!IMPORTANT]
> Este artigo abrange a recolha de eventos Syslog com o [agente Log Analytics,](log-analytics-agent.md) que é um dos agentes utilizados pelo Azure Monitor. Outros agentes recolhem dados diferentes e são configurados de forma diferente. Consulte [a visão geral dos agentes do Azure Monitor](agents-overview.md) para obter uma lista dos agentes disponíveis e os dados que podem recolher.

> [!NOTE]
> O Azure Monitor suporta a recolha de mensagens enviadas por rsyslog ou syslog-ng, onde o rsyslog é o daemon predefinido. O syslog daemon padrão na versão 5 da red hat Enterprise Linux, CentOS e oracle Linux versão (sysklog) não é suportado para a coleção de eventos syslog. Para recolher dados syslog desta versão destas distribuições, o [anão de rsyslog](http://rsyslog.com) deve ser instalado e configurado para substituir o sysklog.
>
>

![Coleção Syslog](media/data-sources-syslog/overview.png)

As seguintes instalações são apoiadas com o colecionador Syslog:

* kern
* utilizador
* correio
* Daemon
* auth
* syslog
* lpr
* news
* uucp
* cron
* authpriv
* ftp
* local0-local7

Para qualquer outra instalação, [configuure uma fonte de dados de registos personalizados](data-sources-custom-logs.md) no Azure Monitor.
 
## <a name="configuring-syslog"></a>Syslog configurante
O agente Log Analytics do Linux apenas recolherá eventos com as instalações e severidades que são especificadas na sua configuração. Pode configurar o Syslog através do portal Azure ou gerindo ficheiros de configuração nos seus agentes Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Configurar syslog no portal Azure
Configure o Syslog do [menu Dados em Definições Avançadas](agent-data-sources.md#configuring-data-sources) para o espaço de trabalho Do Log Analytics. Esta configuração é entregue no ficheiro de configuração de cada agente Linux.

Pode adicionar uma nova instalação selecionando primeiro a opção **Aplicar abaixo a configuração às minhas máquinas** e, em seguida, digitar em seu nome e clicar **+** . Para cada instalação, apenas serão recolhidas mensagens com as severidades selecionadas.  Verifique as gravidades das instalações específicas que pretende recolher. Não é possível fornecer critérios adicionais para filtrar mensagens.

![Configurar syslog](media/data-sources-syslog/configure.png)

Por predefinição, todas as alterações de configuração são automaticamente empurradas para todos os agentes. Se pretender configurar o Syslog manualmente em cada agente Linux, desmarque a caixa *Aplique abaixo a configuração das minhas máquinas*.

### <a name="configure-syslog-on-linux-agent"></a>Configurar syslog sobre o agente Linux
Quando o [agente Log Analytics é instalado num cliente Linux,](../learn/quick-collect-linux-computer.md)instala um ficheiro de configuração de syslog predefinido que define a instalação e a gravidade das mensagens recolhidas. Pode modificar este ficheiro para alterar a configuração. O ficheiro de configuração é diferente dependendo do daemon Syslog que o cliente instalou.

> [!NOTE]
> Se editar a configuração do syslog, deve reiniciar o syslog daemon para que as alterações produzam efeitos.
>
>

#### <a name="rsyslog"></a>rsyslog
O ficheiro de configuração para rsyslog está localizado em **/etc/rsyslog.d/95-omsagent.conf**. Os seus conteúdos predefinidos são apresentados abaixo. Isto recolhe mensagens syslog enviadas do agente local para todas as instalações com um nível de aviso ou superior.

```config
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
```

Pode remover uma instalação removendo a sua secção do ficheiro de configuração. Pode limitar as severidades recolhidas para uma determinada instalação modificando a entrada dessa instalação. Por exemplo, para limitar a facilidade do utilizador a mensagens com uma gravidade de erro ou superior modificaria essa linha do ficheiro de configuração para o seguinte:

```config
user.error    @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>syslog-ng
O ficheiro de configuração para syslog-ng é localização em **/etc/syslog-ng/syslog-ng.conf**.  Os seus conteúdos predefinidos são apresentados abaixo. Isto recolhe mensagens syslog enviadas do agente local para todas as instalações e todas as severidades.   

```config
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
```

Pode remover uma instalação removendo a sua secção do ficheiro de configuração. Pode limitar as severidades recolhidas para uma determinada instalação, retirando-as da sua lista.  Por exemplo, para limitar a facilidade do utilizador a apenas alertar e mensagens críticas, modificaria essa secção do ficheiro de configuração para o seguinte:

```config
#OMS_facility = user
filter f_user_oms { level(alert,crit) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

### <a name="collecting-data-from-additional-syslog-ports"></a>Recolha de dados a partir de portas Syslog adicionais
O agente do Log Analytics ouve mensagens Syslog no cliente local na porta 25224.  Quando o agente é instalado, uma configuração de sísloge predefinida é aplicada e encontrada no seguinte local:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Pode alterar o número de porta criando dois ficheiros de configuração: um ficheiro config FluentD e um ficheiro rsyslog-or-syslog-ng dependendo do daemon Syslog que instalou.  

* O ficheiro config FluentD deve ser um novo ficheiro localizado em: `/etc/opt/microsoft/omsagent/conf/omsagent.d` e substituir o valor na entrada da **porta** pelo seu número de porta personalizado.

    ```xml
    <source>
        type syslog
        port %SYSLOG_PORT%
        bind 127.0.0.1
        protocol_type udp
        tag oms.syslog
    </source>
    <filter oms.syslog.**>
        type filter_syslog
    ```

* Para o rsyslog, deverá criar um novo ficheiro de configuração localizado em: `/etc/rsyslog.d/` e substituir o valor %SYSLOG_PORT% pelo número de porta personalizado.  

    > [!NOTE]
    > Se modificar este valor no ficheiro de `95-omsagent.conf` configuração, será substituído quando o agente aplicar uma configuração predefinida.
    >

    ```config
    # OMS Syslog collection for workspace %WORKSPACE_ID%
    kern.warning              @127.0.0.1:%SYSLOG_PORT%
    user.warning              @127.0.0.1:%SYSLOG_PORT%
    daemon.warning            @127.0.0.1:%SYSLOG_PORT%
    auth.warning              @127.0.0.1:%SYSLOG_PORT%
    ```

* O syslog-ng config deve ser modificado copiando a configuração de exemplo apresentada abaixo e adicionando as definições modificadas personalizadas ao fim do ficheiro de configuração syslog-ng.conf localizado em `/etc/syslog-ng/` . **Não** utilize a etiqueta por defeito **%WORKSPACE_ID%_oms** ou **%WORKSPACE_ID_OMS**, defina uma etiqueta personalizada para ajudar a distinguir as suas alterações.  

    > [!NOTE]
    > Se modificar os valores predefinidos no ficheiro de configuração, serão substituídos quando o agente aplicar uma configuração predefinida.
    >

    ```config
    filter f_custom_filter { level(warning) and facility(auth; };
    destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
    log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };
    ```

Após completar as alterações, o serviço Syslog e o serviço de agente Log Analytics precisam de ser reiniciados para garantir que as alterações de configuração entram em vigor.   

## <a name="syslog-record-properties"></a>Propriedades de registo Syslog
Os registos Syslog têm um tipo de **Syslog** e têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador de onde o evento foi recolhido. |
| Instalação |Define a parte do sistema que gerou a mensagem. |
| HostIP |Endereço IP do sistema que envia a mensagem. |
| Nome do Anfitrião |O nome do sistema envia a mensagem. |
| SeveridadeLevel |Nível de gravidade do evento. |
| SyslogMessage |Texto da mensagem. |
| ProcessID |Identificação do processo que gerou a mensagem. |
| Hora do Evento |Data e hora que o evento foi gerado. |

## <a name="log-queries-with-syslog-records"></a>Consultas de registo com registos Syslog
A tabela seguinte fornece diferentes exemplos de consultas de log que recuperam registos Syslog.

| Consulta | Descrição |
|:--- |:--- |
| Syslog |Todos os Syslogs. |
| Syslog &#124; onde SeverityLevel == "erro" |Todos os registos do Syslog com gravidade de erro. |
| Syslog &#124; resumir AggregatedValue = count() por Computador |Contagem de registos de Syslog por computador. |
| Syslog &#124; resumir AggregatedValue = count() by Facility |Contagem de registos de Syslog por instalações. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais [sobre consultas de registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções.
* Utilize [campos personalizados](./custom-fields.md) para analisar dados de syslog records em campos individuais.
* [Configure os agentes Linux](../learn/quick-collect-linux-computer.md) para recolher outros tipos de dados.

