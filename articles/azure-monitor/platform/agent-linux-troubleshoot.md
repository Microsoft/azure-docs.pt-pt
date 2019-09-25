---
title: Resolver problemas de agente do Linux de análise de registos do Azure | Documentos da Microsoft
description: Descreva os sintomas, as causas e a resolução dos problemas mais comuns com o agente de Log Analytics para Linux no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 2922bbd2e85a671cd17b6042c0b544d3936de884
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262113"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Como resolver problemas com o agente do Log Analytics para Linux 

Este artigo fornece ajuda para solucionar erros que podem ocorrer com o Log Analytics Agent para Linux no Azure Monitor e sugere possíveis soluções para resolvê-los.

Se nenhum destes passos resolver, também estão disponíveis os seguintes canais de suporte:

* Clientes com suporte Premier benefícios pode abrir um pedido de suporte com [Premier](https://premier.microsoft.com/).
* Os clientes com contratos de suporte do Azure, podem abrir um pedido de suporte [no portal do Azure](https://manage.windowsazure.com/?getsupport=true).
* Diagnosticar problemas de OMI com o [guia de resolução de problemas da OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Ficheiro de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Visite a página de comentários do Log Analytics para rever as ideias submetidas e bugs [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) ou um novo de ficheiros.  

## <a name="important-log-locations-and-log-collector-tool"></a>Localizações de registo importante e a ferramenta de Recoletor de registos

 Ficheiro | Caminho
 ---- | -----
 Agente de análise de registo para o ficheiro de registo do Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Ficheiro de registo do configuração de agente do log Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Recomendamos que utilize a nossa ferramenta de recoletor de registo para obter os registos importantes para resolução de problemas ou antes de submeter um problema do GitHub. Pode ler mais sobre a ferramenta e como executá-lo [aqui](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Ficheiros de configuração importantes

 Categoria | Localização do Ficheiro
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` ou `/etc/rsyslog.conf` ou `/etc/rsyslog.d/95-omsagent.conf`
 Desempenho, Nagios, Zabbix, saída do Log Analytics e o agente geral | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Configurações adicionais | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Editar ficheiros de configuração para contadores de desempenho e Syslog é substituído se a coleção estiver configurada a partir da [menu Definições avançadas do registo de análise de dados](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) no portal do Azure para a área de trabalho. Para desativar a configuração para todos os agentes, desativar a recolha do Log Analytics **definições avançadas** ou para um único agente, execute o seguinte:  
> `sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable`

## <a name="installation-error-codes"></a>Códigos de erro de instalação

| Código de Erro | Significado |
| --- | --- |
| NOT_DEFINED | Uma vez que as dependências necessárias não estão instaladas, não será instalado o plug-in do auoms auditd | Instalação de auoms falhou, instalar o pacote auditd. |
| 2 | Opção inválido fornecida para o pacote de shell. Executar `sudo sh ./omsagent-*.universal*.sh --help` para utilização |
| 3 | Nenhuma opção fornecida para o pacote de shell. Executar `sudo sh ./omsagent-*.universal*.sh --help` para utilização. |
| 4 | Definições de proxy inválidas ou; tipo de pacote inválido omsagent -*rpm*pacotes. SH só podem ser instalados em sistemas baseados em RPM e omsagent -*deb*. SH pacotes só podem ser instalados em sistemas baseados no Debian. Recomendamos que utilize o instalador universal de é o [versão mais recente](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Examine também para verificar as configurações de proxy. |
| 5 | O pacote de shell tem de ser executado como raiz ou ocorreu o erro 403 devolvido durante a integração. Execute o comando com `sudo`. |
| 6 | Inválido empacotar a arquitetura ou ocorreu o erro de erro 200 devolvido durante a integração; omsagent -*x64.sh pacotes só podem ser instalados em sistemas de 64 bits e omsagent -* x86.sh pacotes só podem ser instalados em sistemas de 32 bits. Transferir o pacote correto para a sua arquitetura do [versão mais recente](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Falha na instalação do pacote do OMS. Examine a saída do comando da falha de raiz. |
| 19 | Falha na instalação do pacote da OMI. Examine a saída do comando da falha de raiz. |
| 20 | Falha na instalação do pacote SCX. Examine a saída do comando da falha de raiz. |
| 21 | Falha ao instalar os kits de fornecedor. Examine a saída do comando da falha de raiz. |
| 22 | Falha na instalação do pacote agrupado. Examinar a saída do comando da falha de raiz |
| 23 | Pacote SCX ou OMI já instalado. Uso `--upgrade` em vez de `--install` para instalar o pacote de shell. |
| 30 | Erro interno do pacote. Ficheiro de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com os detalhes da saída. |
| 55 | Versão sem suporte do OpenSSL ou não é possível conectar-se a Azure Monitor ou dpkg está bloqueado ou faltando programa de rotação. |
| 61 | Biblioteca de ctypes de Python em falta. Instale a biblioteca de ctypes Python ou o pacote (python ctypes). |
| 62 | Programa tar em falta, tar de instalação. |
| 63 | Programa po em falta, sed. de instalação |
| 64 | Programa curl em falta, curl de instalação. |
| 65 | Programa gpg em falta, gpg da instalação. |

## <a name="onboarding-error-codes"></a>Códigos de erro de integração

| Código de Erro | Significado |
| --- | --- |
| 2 | Opção inválido fornecida para o script de omsadmin. Executar `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para utilização. |
| 3 | Configuração inválida fornecida para o script de omsadmin. Executar `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para utilização. |
| 4 | Proxy inválido fornecido para o script de omsadmin. Verifique se o proxy e consulte nossos [documentação para utilizar um proxy de HTTP](log-analytics-agent.md#network-firewall-requirements). |
| 5 | 403 erro HTTP recebido de Azure Monitor. Ver o resultado completo do script omsadmin para obter detalhes. |
| 6 | Erro de HTTP não 200 recebido de Azure Monitor. Ver o resultado completo do script omsadmin para obter detalhes. |
| 7 | Não é possível conectar-se ao Azure Monitor. Ver o resultado completo do script omsadmin para obter detalhes. |
| 8 | Integração de erro à área de trabalho do Log Analytics. Ver o resultado completo do script omsadmin para obter detalhes. |
| 30 | Erro de script interno. Ficheiro de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com os detalhes da saída. |
| 31 | ID do agente de geração de erro. Ficheiro de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com os detalhes da saída. |
| 32 | Erro ao gerar certificados. Ver o resultado completo do script omsadmin para obter detalhes. |
| 33 | Erro ao gerar o metaconfiguration para omsconfig. Ficheiro de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com os detalhes da saída. |
| 34 | Script de geração de Metaconfiguration não está presente. Repita a integração com `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Ativar o registo de depuração
### <a name="oms-output-plugin-debug"></a>Depuração de plug-in de saída do OMS
 FluentD permite que os níveis de registo específico de plug-in que lhe permite especificar níveis de registo diferentes para entradas e saídas. Para especificar um nível de registo diferente para a saída do OMS, edite a configuração do agente geral em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 O plugin de saída do OMS, antes do final do ficheiro de configuração, alterar os `log_level` propriedade a partir `info` para `debug`:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

O log de depuração permite que você veja os carregamentos em lote para Azure Monitor separados por tipo, número de itens de dados e tempo gasto para enviar:

*Registo de depuração ativado de exemplo:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Saída verbosa ideal
Em vez de utilizar o plug-in de saída do OMS o utilizador pode também apresentar os itens de dados diretamente para `stdout`, que é visível no agente do Log Analytics para o ficheiro de registo do Linux.

No ficheiro de configuração do agente geral do Log Analytics na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, comente o OMS Plug-in de saída através da adição de um `#` à frente de cada linha:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Abaixo o plug-in de saída, anule os comentários a seção a seguir, removendo o `#` à frente de cada linha:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problema:  Não é possível se conectar por meio de proxy para Azure Monitor

### <a name="probable-causes"></a>Causas prováveis
* O proxy especificado durante a integração estava incorreto
* Os pontos de extremidade do Azure Monitor e do serviço de automação do Azure não estão na lista de permissões no seu datacenter 

### <a name="resolution"></a>Resolução
1. Reintegre para Azure monitor com o agente de log Analytics para Linux usando o comando a seguir com a `-v` opção habilitada. Ele permite a saída detalhada do agente que se conecta por meio do proxy para Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Reveja a secção [atualizar as definições de proxy](agent-manage.md#update-proxy-settings) para verificar se configurou corretamente o agente para comunicar através de um servidor proxy.    
* Verifique se os seguintes Azure Monitor pontos de extremidade estão na lista de permissões:

    |Recursos do Agente| Portas | Direção |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Porta 443| Entrada e saída |  
    |*.oms.opinsights.azure.com | Porta 443| Entrada e saída |  
    |*.blob.core.windows.net | Porta 443| Entrada e saída |  
    |*.azure-automation.net | Porta 443| Entrada e saída | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: Você recebe um erro 403 ao tentar carregar

### <a name="probable-causes"></a>Causas prováveis
* Data e hora é incorreta no servidor Linux 
* ID de área de trabalho e a chave de área de trabalho utilizado não estiverem corretos

### <a name="resolution"></a>Resolução

1. Verifique a hora no seu servidor do Linux com a data de comando. Se o tempo é + /-15 minutos da hora atual, em seguida, integração falha. Para correto isso atualizar a data e/ou do fuso horário do seu servidor do Linux. 
2. Certifique-se de que instalou a versão mais recente do agente do Log Analytics para Linux.  A versão mais recente agora notifica que se desfasamento de tempo está a causar a falha de inclusão.
3. Reonboard com o ID de área de trabalho correto e a chave de área de trabalho seguindo as instruções de instalação anteriormente no artigo.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Você verá um erro 500 e 404 no arquivo de log logo após a integração
Este é um problema conhecido que ocorre no primeiro carregamento de dados do Linux para uma área de trabalho do Log Analytics. Isso não afeta os dados que está a ser enviada ou serviço experiência.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problema: Você vê omiagent usando a CPU de 100%

### <a name="probable-causes"></a>Causas prováveis
Uma regressão no pacote do NSS-PEM [v 1.0.3 -5. EL7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) causou um problema de desempenho grave, que temos visto muito nas distribuições RedHat/CentOS 7. x. Para saber mais sobre esse problema, verifique a seguinte documentação: Bug [1667121 regressão de desempenho em libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Os bugs relacionados ao desempenho não acontecem o tempo todo e são muito difíceis de reproduzir. Se você tiver esse problema com o omiagent, deverá usar o script omiHighCPUDiagnostics.sh, que coletará o rastreamento de pilha do omiagent ao exceder um certo limite.

1. Baixar o script <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Execute o diagnóstico por 24 horas com um limite de CPU de 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. A pilha de chamadas será despejada no arquivo omiagent_trace, se você notar muitas chamadas de função de rotação e NSS, siga as etapas de resolução abaixo.

### <a name="resolution-step-by-step"></a>Resolução (passo a passo)

1. Atualize o pacote NSS-PEM para [v 1.0.3 -5. EL7 _ 6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Se o NSS-PEM não estiver disponível para atualização (na maioria das vezes, ocorre no CentOS), faça o downgrade da ondulação para 7.29.0-46. Se, por engano, você executar "atualização yum", a ondulação será atualizada para 7.29.0-51 e o problema ocorrerá novamente. <br/>
`sudo yum downgrade curl libcurl`

3. Reiniciar OMI: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problema: Você não está vendo nenhum dado no portal do Azure

### <a name="probable-causes"></a>Causas prováveis

- Falha na integração ao Azure Monitor
- A conexão com o Azure Monitor está bloqueada
- Agente de análise do registo de dados do Linux é uma cópia de segurança

### <a name="resolution"></a>Resolução
1. Verifique se a integração de Azure Monitor foi bem-sucedida verificando se o seguinte arquivo existe:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard usando o `omsadmin.sh` instruções da linha de comandos
3. Se utilizar um proxy, consulte os passos de resolução de proxy fornecidos anteriormente.
4. Em alguns casos, quando o agente do Log Analytics para Linux não consegue comunicar com o serviço, dados no agente é colocado em fila para o tamanho do buffer cheio, o que é a 50 MB. O agente deve ser reiniciado executando o seguinte comando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Este problema ser corrigido no 1.1.0-28 de versão do agente e versões posteriores.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problema: Você não está vendo mensagens de syslog encaminhadas 

### <a name="probable-causes"></a>Causas prováveis
* A configuração aplicada ao servidor Linux não permite a coleção das instalações enviadas e/ou níveis de registo
* Syslog não está a ser reencaminhado corretamente para o servidor Linux
* O número de mensagens sejam encaminhados por segundo é demasiado grande para a configuração base do agente do Log Analytics para Linux lidar com

### <a name="resolution"></a>Resolução
* Certifique-se de que a configuração na área de trabalho do Log Analytics para Syslog tem todas as instalações e os níveis de registo correto. Revisão [configurar a recolha de Syslog no portal do Azure](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Verifique se o syslog nativo mensagens daemons (`rsyslog`, `syslog-ng`) são capazes de receber as mensagens encaminhadas
* Verifique as definições da firewall no servidor do Syslog para garantir que as mensagens não estão a ser bloqueadas
* Simular uma mensagem do Syslog para utilizar o Log Analytics `logger` comando
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problema: Você está recebendo o endereço errno já em uso no arquivo de log omsagent
Se vir `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` no omsagent.log.

### <a name="probable-causes"></a>Causas prováveis
Este erro indica que a extensão de diagnóstico do Linux (LAD) é instalada lado a lado com a extensão de VM do Linux do Log Analytics e mesma porta está a utilizar para a recolha de dados do syslog como omsagent.

### <a name="resolution"></a>Resolução
1. Como raiz, execute os seguintes comandos (Observe que 25224 é um exemplo e é possível que no seu ambiente vê um número de porta diferentes utilizado pelo LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Em seguida, terá de editar o correto `rsyslogd` ou `syslog_ng` config de ficheiros e alterar a configuração relacionada com LAD escrever para a porta 25229.

2. Se a VM está em execução `rsyslogd`, o ficheiro deve ser modificada é: `/etc/rsyslog.d/95-omsagent.conf` (se existir, caso contrário `/etc/rsyslog`). Se a VM está em execução `syslog_ng`, o ficheiro deve ser modificada é: `/etc/syslog-ng/syslog-ng.conf`.
3. Reiniciar omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Reinicie o serviço syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problema: Não é possível desinstalar o omsagent usando a opção de limpeza

### <a name="probable-causes"></a>Causas prováveis

* Extensão de diagnóstico do Linux está instalado
* Extensão de diagnóstico do Linux foi instalado e desinstalado, mas ainda verá um erro sobre omsagent a ser utilizado por mdsd e não pode ser removido.

### <a name="resolution"></a>Resolução
1. Desinstale a extensão de diagnóstico do Linux (LAD).
2. Remova os ficheiros de extensão de diagnóstico do Linux da máquina, se estiverem presentes na seguinte localização: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` e `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problema: Você não pode ver dados de nenhum dado Nagios 

### <a name="probable-causes"></a>Causas prováveis
* Omsagent. o utilizador não tem permissões para ler a partir do ficheiro de registo da Nagios
* Origem do Nagios e do filtro não tenham sido uncommented do ficheiro de omsagent. Conf

### <a name="resolution"></a>Resolução
1. Adicionar utilizador omsagent para ler a partir do ficheiro Nagios através destas [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. No agente do Log Analytics para o ficheiro de configuração geral do Linux no `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, certifique-se de que **ambos** Nagios origem e de filtro são uncommented.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problema: Você não está vendo nenhum dado do Linux 

### <a name="probable-causes"></a>Causas prováveis
* Falha na integração ao Azure Monitor
* A conexão com o Azure Monitor está bloqueada
* Máquina virtual foi reiniciada
* Pacote OMI manualmente tiver sido atualizado para uma versão mais recente em comparação comparada o que tenha sido instalado pelo agente do Log Analytics para o pacote do Linux
* Registos de recursos de DSC *classe não encontrado* erro na `omsconfig.log` ficheiro de registo
* Agente de análise de registo para dados de cópia de segurança
* Registos de DSC *configuração atual não existe. Execute o comando de início-dscconfiguration para com - parâmetro de caminho para especificar um ficheiro de configuração e criar primeiro uma configuração atual.* na `omsconfig.log` ficheiro de registo, mas nenhuma mensagem de registo existe sobre `PerformRequiredConfigurationChecks` operações.

### <a name="resolution"></a>Resolução
1. Instale todas as dependências como auditd pacote.
2. Verifique se a integração com Azure Monitor foi bem-sucedida verificando se o seguinte arquivo existe: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Se não, era reonboard utilizando a linha de comando omsadmin.sh [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
4. Se utilizar um proxy, verifique os passos acima de resolução de problemas do proxy.
5. Em alguns sistemas de distribuição do Azure, o daemon de servidor do OMI omid não inicia depois da máquina virtual é reiniciada. Isto resultará na não são apresentados dados relacionados com a solução de auditoria, ChangeTracking ou UpdateManagement. A solução é para iniciar manualmente o servidor omi executando `sudo /opt/omi/bin/service_control restart`.
6. Depois do pacote do OMI manualmente é atualizado para uma versão mais recente, tem de ser reiniciado manualmente para o agente do Log Analytics continuar a funcionar. Este passo é necessário para algumas distribuições em que servidor OMI não for iniciado automaticamente após ser atualizada. Executar `sudo /opt/omi/bin/service_control restart` para reiniciar o OMI.
7. Se vir que os recursos de DSC *classe não encontrado* erro na omsconfig.log, executar `sudo /opt/omi/bin/service_control restart`.
8. Em alguns casos, quando o agente de Log Analytics para Linux não pode se comunicar com Azure Monitor, é feito backup dos dados no agente em todo o tamanho do buffer: 50 MB. O agente deve ser reiniciado executando o seguinte comando `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Este problema ser corrigido no 1.1.0-28 de versão do agente ou posterior
    >

* Se `omsconfig.log` arquivo de log não indica que `PerformRequiredConfigurationChecks` operations está a executar periodicamente no sistema, poderá haver um problema com o tarefa de cron/serviço. Certifique-se de tarefa cron existe em `/etc/cron.d/OMSConsistencyInvoker`. Se necessário, execute os seguintes comandos para criar a tarefa de cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Além disso, certifique-se de que o serviço de cron está em execução. Pode usar `service cron status` com o Debian, Ubuntu, SUSE, ou `service crond status` com RHEL, CentOS, Oracle Linux para verificar o estado deste serviço. Se o serviço não existir, pode instalar os binários e iniciar o serviço utilizando o seguinte:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problema: Ao configurar a coleta do portal para os contadores de desempenho do syslog ou do Linux, as configurações não são aplicadas

### <a name="probable-causes"></a>Causas prováveis
* O agente do Log Analytics para Linux não tem aplicadas a configuração mais recente
* Não foram aplicadas as definições alteradas no portal

### <a name="resolution"></a>Resolução
**Em segundo plano:** `omsconfig` é o agente do Log Analytics para o agente de configuração do Linux que procura a nova configuração do lado do portal a cada cinco minutos. Esta configuração é aplicada para o agente do Log Analytics para ficheiros de configuração do Linux localizado em /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* Em alguns casos, o agente do Log Analytics para o agente de configuração do Linux poderá não conseguir comunicar com o serviço de configuração do portal, resultando em configuração mais recente, não sendo aplicada.
  1. Verifique se o `omsconfig` agente está instalado ao executar `dpkg --list omsconfig` ou `rpm -qi omsconfig`.  Se não estiver instalado, reinstale a versão mais recente do agente do Log Analytics para Linux.

  2. Verifique se o `omsconfig` agente pode se comunicar com Azure monitor executando o comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`a seguir. Este comando devolve a configuração que o agente receber do serviço, incluindo definições de Syslog, contadores de desempenho do Linux e registos personalizados. Se este comando falhar, execute o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Esse comando força o agente programa omsconfig a se comunicar com Azure Monitor e recuperar a configuração mais recente.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problema: Você não está vendo nenhum dado de log personalizado 

### <a name="probable-causes"></a>Causas prováveis
* Falha na integração ao Azure Monitor.
* A definição **aplicam-se a seguinte configuração para meus servidores Linux** não tiver sido selecionada.
* omsconfig não tenha capturado a configuração de registo personalizado mais recente do serviço.
* Agente de análise do registo de utilizador do Linux `omsagent` não conseguir aceder ao registo personalizado devido a permissões ou não foi encontrada.  Pode ver os seguintes erros:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Problema com a condição de corrida corrigidos no agente do Log Analytics para Linux versão 1.1.0-217 conhecido

### <a name="resolution"></a>Resolução
1. Verifique se a integração com Azure Monitor foi bem-sucedida verificando se o seguinte arquivo existe: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Se não, qualquer um de:  

  1. Reonboard utilizando a linha de comando omsadmin.sh [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
  2. Sob **definições avançadas** no portal do Azure, certifique-se de que a definição **aplicam-se a seguinte configuração para meus servidores Linux** está ativada.  

2. Verifique se o `omsconfig` agente pode se comunicar com Azure monitor executando o comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`a seguir.  Este comando devolve a configuração que o agente receber do serviço, incluindo definições de Syslog, contadores de desempenho do Linux e registos personalizados. Se este comando falhar, execute o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Esse comando força o agente programa omsconfig a se comunicar com Azure Monitor e recuperar a configuração mais recente.

**Seguindo** Em vez do agente de log Analytics para Linux em execução como um usuário `root`com privilégios, o agente é `omsagent` executado como o usuário. Na maioria dos casos, a permissão explícita deve ser concedido a este utilizador para que determinados arquivos a serem lidos. Para conceder permissão para `omsagent` utilizador, execute os seguintes comandos:

1. Adicionar o `omsagent` utilizador ao grupo específico `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Conceder acesso de leitura universal para o ficheiro necessário `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Existe um problema conhecido com uma condição de corrida com o agente do Log Analytics para versão anterior ao 1.1.0-217 do Linux. Depois de atualizar para o agente mais recente, execute o seguinte comando para obter a versão mais recente do que o plug-in de saída `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problema: Você está tentando reintegre para um novo espaço de trabalho
Quando tentar reonboard um agente para uma nova área de trabalho, a configuração do agente do Log Analytics tem de ser limpos antes reonboarding. Para limpar a configuração antiga do agente, execute o pacote de shell com `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Ou

```
sudo sh ./onboard_agent.sh --purge
```

Pode continuar reonboard depois de utilizar o `--purge` opção

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Log Analytics extensão do agente na portal do Azure está marcada com um estado de falha: O aprovisionamento falhou

### <a name="probable-causes"></a>Causas prováveis
* Agente do log Analytics foi removido do sistema operativo
* Serviço de agente do log Analytics está inativo, desativado ou não configurado

### <a name="resolution"></a>Resolução 
Execute os seguintes passos para corrigir o problema.
1. Remova a extensão do portal do Azure.
2. Instalar o agente ao seguir a [instruções](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Reinicie o agente ao executar o seguinte comando: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Aguarde alguns minutos e o estado de aprovisionamento é alterado para **aprovisionamento foi concluída com êxito**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problema: A atualização do agente de Log Analytics sob demanda

### <a name="probable-causes"></a>Causas prováveis

Os pacotes de agente do Log Analytics no anfitrião estão desatualizados.

### <a name="resolution"></a>Resolução 
Execute os seguintes passos para corrigir o problema.

1. Verificar a versão mais recente num [página](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Transferir o script de instalação (1.4.2-124 como versão de exemplo):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Pacotes de atualização executando `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
