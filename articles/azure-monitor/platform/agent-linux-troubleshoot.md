---
title: Solucionar problemas do agente Linux Log Analytics do Azure | Microsoft Docs
description: Descreva os sintomas, as causas e a resolução dos problemas mais comuns com o agente de Log Analytics para Linux no Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/13/2018
ms.openlocfilehash: 5b828f62d5a8c7c518f3d28e92f52aac3b77f65c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932819"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Como solucionar problemas com o agente de Log Analytics para Linux 

Este artigo fornece ajuda para solucionar erros que podem ocorrer com o Log Analytics Agent para Linux no Azure Monitor e sugere possíveis soluções para resolvê-los.

Se nenhuma dessas etapas funcionar para você, os seguintes canais de suporte também estarão disponíveis:

* Os clientes com benefícios de suporte Premier podem abrir uma solicitação de suporte com o [Premier](https://premier.microsoft.com/).
* Os clientes com contratos de suporte do Azure podem abrir uma solicitação de suporte [no portal do Azure](https://manage.windowsazure.com/?getsupport=true).
* Diagnostique problemas de OMI com o [Guia de solução de problemas do OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Arquivo de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Visite a página de comentários Log Analytics para revisar ideias e bugs enviados [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) ou arquivo um novo.  

## <a name="important-log-locations-and-log-collector-tool"></a>Locais de log importantes e ferramenta coletor de logs

 Ficheiros | Caminho
 ---- | -----
 Log Analytics agente para arquivo de log do Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Arquivo de log de configuração do agente Log Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Recomendamos que você use nossa ferramenta de coletor de logs para recuperar logs importantes para solução de problemas ou antes de enviar um problema do GitHub. Você pode ler mais sobre a ferramenta e como executá-la [aqui](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Arquivos de configuração importantes

 Categoria | Local do arquivo
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` ou `/etc/rsyslog.conf` ou `/etc/rsyslog.d/95-omsagent.conf`
 Desempenho, Nagios, Zabbix, saída de Log Analytics e agente geral | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Configurações adicionais | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >A edição de arquivos de configuração para contadores de desempenho e syslog será substituída se a coleção estiver configurada no [menu dados log Analytics configurações avançadas](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) no portal do Azure para seu espaço de trabalho. Para desabilitar a configuração de todos os agentes, desabilite a coleta de Log Analytics **Configurações avançadas** ou para um único agente execute o seguinte:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Códigos de erro de instalação

| Código de erro | Significado |
| --- | --- |
| NOT_DEFINED | Como as dependências necessárias não são instaladas, o plug-in auoms audited não será instalado | Falha na instalação do auoms, instale o pacote de auditoria. |
| 2 | Opção inválida fornecida para o pacote de Shell. Executar `sudo sh ./omsagent-*.universal*.sh --help` para uso |
| 3 | Nenhuma opção fornecida para o pacote de Shell. Execute `sudo sh ./omsagent-*.universal*.sh --help` para uso. |
| 4 | Tipo de pacote inválido ou configurações de proxy inválidas; os pacotes omsagent-*rpm*. sh só podem ser instalados em sistemas baseados em RPM e os pacotes omsagent-*Deb*. sh só podem ser instalados em sistemas baseados em Debian. É recomendável que você use o Universal Installer a partir da [versão mais recente](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Examine também para verificar as configurações de proxy. |
| 5 | O pacote de Shell deve ser executado como raiz ou houve um erro 403 retornado durante a integração. Execute o comando usando `sudo`. |
| 6 | Arquitetura de pacote inválida ou erro de erro 200 retornado durante a integração; os pacotes omsagent-*x64.sh só podem ser instalados em sistemas de 64 bits e os pacotes omsagent-* x86.sh só podem ser instalados em sistemas de 32 bits. Baixe o pacote correto para sua arquitetura a partir da [versão mais recente](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Falha na instalação do pacote do OMS. Examine a saída do comando para obter a falha raiz. |
| 19 | Falha na instalação do pacote OMI. Examine a saída do comando para obter a falha raiz. |
| 20 | Falha na instalação do pacote SCX. Examine a saída do comando para obter a falha raiz. |
| 21 | Falha na instalação dos kits de provedor. Examine a saída do comando para obter a falha raiz. |
| 22 | Falha na instalação do pacote agrupado. Examinar a saída do comando para a falha raiz |
| 23 | Pacote SCX ou OMI já instalado. Use `--upgrade` em vez de `--install` para instalar o pacote de Shell. |
| 30 | Erro de pacote interno. Arquivo de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 55 | Versão sem suporte do OpenSSL ou não é possível conectar-se a Azure Monitor ou dpkg está bloqueado ou faltando programa de rotação. |
| 61 | Biblioteca de ctypes do Python ausente. Instale a biblioteca ou pacote de ctypes do Python (Python-ctypes). |
| 62 | Programa tar ausente, instale o tar. |
| 63 | Programa sed ausente, instale o sed. |
| 64 | Programa de rotação ausente, rotação de instalação. |
| 65 | Programa GPG ausente, instale o GPG. |

## <a name="onboarding-error-codes"></a>Códigos de erro de integração

| Código de erro | Significado |
| --- | --- |
| 2 | Opção inválida fornecida para o script omsadmin. Execute `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para uso. |
| 3 | Configuração inválida fornecida para o script omsadmin. Execute `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para uso. |
| 4 | Proxy inválido fornecido para o script omsadmin. Verifique o proxy e veja nossa [documentação para usar um proxy http](log-analytics-agent.md#network-firewall-requirements). |
| 5 | 403 erro HTTP recebido de Azure Monitor. Consulte a saída completa do script omsadmin para obter detalhes. |
| 6 | Erro de HTTP não 200 recebido de Azure Monitor. Consulte a saída completa do script omsadmin para obter detalhes. |
| 7 | Não é possível conectar-se ao Azure Monitor. Consulte a saída completa do script omsadmin para obter detalhes. |
| 8 | Erro ao realizar a integração com o espaço de trabalho Log Analytics. Consulte a saída completa do script omsadmin para obter detalhes. |
| 30 | Erro de script interno. Arquivo de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 31 | Erro ao gerar a ID do agente. Arquivo de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 32 | Erro ao gerar certificados. Consulte a saída completa do script omsadmin para obter detalhes. |
| 33 | Erro ao gerar metaconfiguração para programa omsconfig. Arquivo de um [problema do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 34 | O script de geração de metaconfiguração não está presente. Repita a integração com `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Habilitar log de depuração
### <a name="oms-output-plugin-debug"></a>Depuração do plug-in de saída do OMS
 O fluentd permite níveis de log específicos do plug-in, permitindo que você especifique diferentes níveis de log para entradas e saídas. Para especificar um nível de log diferente para a saída do OMS, edite a configuração geral do agente em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 No plug-in de saída do OMS, antes do final do arquivo de configuração, altere a propriedade `log_level` de `info` para `debug`:

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

*Exemplo de log habilitado para depuração:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Saída detalhada
Em vez de usar o plug-in de saída do OMS, você também pode gerar itens de dados diretamente para `stdout`, que é visível no arquivo de log do Log Analytics Agent para Linux.

No Log Analytics arquivo de configuração geral do agente em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, comente o plug-in de saída do OMS adicionando uma `#` na frente de cada linha:

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

Abaixo do plug-in de saída, remova a marca de comentário da seção a seguir removendo o `#` na frente de cada linha:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problema: não é possível se conectar por meio de proxy para Azure Monitor

### <a name="probable-causes"></a>Causas prováveis
* O proxy especificado durante a integração estava incorreto
* Os pontos de extremidade do Azure Monitor e do serviço de automação do Azure não estão na lista de permissões no seu datacenter 

### <a name="resolution"></a>Resolução
1. Reintegre para Azure Monitor com o agente do Log Analytics para Linux usando o comando a seguir com a opção `-v` habilitada. Ele permite a saída detalhada do agente que se conecta por meio do proxy para Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Examine a seção [Atualizar configurações de proxy](agent-manage.md#update-proxy-settings) para verificar se você configurou corretamente o agente para se comunicar por meio de um servidor proxy.    
* Verifique se os seguintes Azure Monitor pontos de extremidade estão na lista de permissões:

    |Recursos do Agente| Portas | Direção |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Porta 443| Entrada e saída |  
    |*.oms.opinsights.azure.com | Porta 443| Entrada e saída |  
    |*.blob.core.windows.net | Porta 443| Entrada e saída |  
    |*.azure-automation.net | Porta 443| Entrada e saída | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: você recebe um erro 403 ao tentar carregar

### <a name="probable-causes"></a>Causas prováveis
* A data e a hora estão incorretas no servidor Linux 
* A ID do espaço de trabalho e a chave do espaço de trabalho usados não estão

### <a name="resolution"></a>Resolução

1. Verifique a hora no servidor Linux com a data de comando. Se o tempo for +/-15 minutos a partir da hora atual, a integração falhará. Para corrigir essa atualização, a data e/ou o fuso horário do servidor Linux. 
2. Verifique se você instalou a versão mais recente do agente de Log Analytics para Linux.  A versão mais recente agora notifica se a distorção de tempo está causando a falha de integração.
3. Reintegre usando a ID e a chave do espaço de trabalho corretas seguindo as instruções de instalação anteriores neste artigo.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: você verá um erro 500 e 404 no arquivo de log logo após a integração
Esse é um problema conhecido que ocorre no primeiro carregamento de dados do Linux em um espaço de trabalho Log Analytics. Isso não afeta os dados que estão sendo enviados ou a experiência de serviço.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problema: você vê omiagent usando a CPU de 100%

### <a name="probable-causes"></a>Causas prováveis
Uma regressão no pacote do NSS-PEM [v 1.0.3 -5. EL7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) causou um problema de desempenho grave, que temos visto muito nas distribuições RedHat/CentOS 7. x. Para saber mais sobre esse problema, verifique a seguinte documentação: bug [1667121 regressão de desempenho em libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

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

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problema: você não está vendo nenhum dado no portal do Azure

### <a name="probable-causes"></a>Causas prováveis

- Falha na integração ao Azure Monitor
- A conexão com o Azure Monitor está bloqueada
- Backup do Log Analytics Agent para dados do Linux

### <a name="resolution"></a>Resolução
1. Verifique se a integração de Azure Monitor foi bem-sucedida verificando se o seguinte arquivo existe: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reintegre usando as instruções de linha de comando `omsadmin.sh`
3. Se estiver usando um proxy, consulte as etapas de resolução de proxy fornecidas anteriormente.
4. Em alguns casos, quando o agente de Log Analytics para Linux não pode se comunicar com o serviço, os dados no agente são enfileirados para o tamanho de buffer completo, que é 50 MB. O agente deve ser reiniciado executando o seguinte comando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Esse problema é corrigido no Agent versão 1.1.0-28 e posterior.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problema: você não está vendo mensagens de syslog encaminhadas 

### <a name="probable-causes"></a>Causas prováveis
* A configuração aplicada ao servidor Linux não permite a coleta dos recursos enviados e/ou dos níveis de log
* O syslog não está sendo encaminhado corretamente para o servidor Linux
* O número de mensagens encaminhadas por segundo é muito grande para a configuração básica do agente de Log Analytics para o Linux manipular

### <a name="resolution"></a>Resolução
* Verifique se a configuração no espaço de trabalho Log Analytics para syslog tem todas as instalações e os níveis de log corretos. Examine [Configurar coleção de syslog no portal do Azure](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Verifique se os daemons de mensagens do syslog nativo (`rsyslog``syslog-ng`) são capazes de receber as mensagens encaminhadas
* Verifique as configurações de firewall no servidor syslog para garantir que as mensagens não estejam sendo bloqueadas
* Simular uma mensagem syslog para Log Analytics usando `logger` comando
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problema: você está recebendo o endereço errno já em uso no arquivo de log omsagent
Se você vir `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` em omsagent. log.

### <a name="probable-causes"></a>Causas prováveis
Esse erro indica que a extensão de diagnóstico do Linux (LAD) está instalada lado a lado com a Log Analytics extensão de VM do Linux e está usando a mesma porta para coleta de dados de syslog como omsagent.

### <a name="resolution"></a>Resolução
1. Como raiz, execute os seguintes comandos (Observe que 25224 é um exemplo e é possível que em seu ambiente você veja um número de porta diferente usado por LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Em seguida, você precisa editar o arquivo de configuração `rsyslogd` ou `syslog_ng` correto e alterar a configuração relacionada ao LAD para gravar na porta 25229.

2. Se a VM estiver em execução `rsyslogd`, o arquivo a ser modificado será: `/etc/rsyslog.d/95-omsagent.conf` (se existir, mais `/etc/rsyslog`). Se a VM estiver em execução `syslog_ng`, o arquivo a ser modificado será: `/etc/syslog-ng/syslog-ng.conf`.
3. Reinicie o omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Reinicie o serviço syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problema: não é possível desinstalar o omsagent usando a opção de limpeza

### <a name="probable-causes"></a>Causas prováveis

* A extensão de diagnóstico do Linux está instalada
* A extensão de diagnóstico do Linux foi instalada e desinstalada, mas você ainda verá um erro sobre omsagent que está sendo usado pelo MDSD e não pode ser removido.

### <a name="resolution"></a>Resolução
1. Desinstale a extensão de diagnóstico do Linux (LAD).
2. Remova os arquivos de extensão de diagnóstico do Linux do computador se eles estiverem presentes no seguinte local: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` e `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problema: você não pode ver dados de qualquer Nagios de dados 

### <a name="probable-causes"></a>Causas prováveis
* O usuário Omsagent não tem permissões para ler do arquivo de log Nagios
* A origem e o filtro do Nagios não foram recomentados do arquivo omsagent. conf

### <a name="resolution"></a>Resolução
1. Adicione o usuário omsagent para ler o arquivo Nagios seguindo estas [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. No arquivo de configuração geral do agente de Log Analytics para Linux em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, verifique **se a origem** e o filtro Nagios estão sem comentários.

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

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problema: você não está vendo nenhum dado do Linux 

### <a name="probable-causes"></a>Causas prováveis
* Falha na integração ao Azure Monitor
* A conexão com o Azure Monitor está bloqueada
* A máquina virtual foi reinicializada
* O pacote OMI foi atualizado manualmente para uma versão mais recente em comparação com o que foi instalado pelo agente Log Analytics para o pacote do Linux
* Erro de classe de logs de recursos DSC *não encontrado* no arquivo de log `omsconfig.log`
* O backup do agente de Log Analytics para dados é feito
* A configuração atual dos logs de DSC *não existe. Execute o comando Start-DscConfiguration com o parâmetro-Path para especificar um arquivo de configuração e criar uma configuração atual primeiro.* no `omsconfig.log` arquivo de log, mas não existe nenhuma mensagem de log sobre `PerformRequiredConfigurationChecks` operações.

### <a name="resolution"></a>Resolução
1. Instale todas as dependências, como o pacote auditado.
2. Verifique se a integração com Azure Monitor foi bem-sucedida verificando se o seguinte arquivo existe: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Se não foi, reintegre usando as [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)de linha de comando do omsadmin.sh.
4. Se estiver usando um proxy, verifique as etapas de solução de problemas de proxy acima.
5. Em alguns sistemas de distribuição do Azure, o daemon do Omid OMI Server não é iniciado depois que a máquina virtual é reinicializada. Isso fará com que não veja dados relacionados à solução de auditoria, ChangeTracking ou UpdateManagement. A solução alternativa é iniciar manualmente o OMI Server executando `sudo /opt/omi/bin/service_control restart`.
6. Depois que o pacote OMI é atualizado manualmente para uma versão mais recente, ele precisa ser reiniciado manualmente para que Log Analytics Agent continue funcionando. Essa etapa é necessária para algumas distribuições em que o servidor OMI não é iniciado automaticamente depois de ser atualizado. Execute `sudo /opt/omi/bin/service_control restart` para reiniciar o OMI.
7. Se você vir o erro classe de recurso DSC *não encontrado* em programa omsconfig. log, execute `sudo /opt/omi/bin/service_control restart`.
8. Em alguns casos, quando o agente de Log Analytics para Linux não pode se comunicar com Azure Monitor, é feito backup dos dados no agente em todo o tamanho do buffer: 50 MB. O agente deve ser reiniciado executando o seguinte comando `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Esse problema foi corrigido na versão 1.1.0 do agente-28 ou posterior
    >

* Se `omsconfig.log` arquivo de log não indicar que as operações de `PerformRequiredConfigurationChecks` estão sendo executadas periodicamente no sistema, pode haver um problema com o trabalho/serviço cron. Verifique se o trabalho cron existe em `/etc/cron.d/OMSConsistencyInvoker`. Se necessário, execute os seguintes comandos para criar o trabalho cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Além disso, verifique se o serviço cron está em execução. Você pode usar `service cron status` com Debian, Ubuntu, SUSE ou `service crond status` com RHEL, CentOS Oracle Linux para verificar o status desse serviço. Se o serviço não existir, você poderá instalar os binários e iniciar o serviço usando o seguinte:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problema: ao configurar a coleta do portal para os contadores de desempenho do syslog ou do Linux, as configurações não são aplicadas

### <a name="probable-causes"></a>Causas prováveis
* O agente de Log Analytics para Linux não pegou a configuração mais recente
* As configurações alteradas no portal não foram aplicadas

### <a name="resolution"></a>Resolução
**Plano de fundo:** `omsconfig` é o agente de configuração do log Analytics Agent para Linux que procura pela nova configuração no lado do portal a cada cinco minutos. Essa configuração é aplicada ao agente de Log Analytics para arquivos de configuração do Linux localizado em/etc/opt/Microsoft/omsagent/conf/omsagent.conf.

* Em alguns casos, o agente de configuração do Log Analytics Agent para Linux pode não ser capaz de se comunicar com o serviço de configuração do portal, resultando na última configuração não ser aplicada.
  1. Verifique se o agente de `omsconfig` está instalado executando `dpkg --list omsconfig` ou `rpm -qi omsconfig`.  Se não estiver instalado, reinstale a versão mais recente do agente de Log Analytics para Linux.

  2. Verifique se o agente de `omsconfig` pode se comunicar com Azure Monitor executando o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`. Esse comando retorna a configuração que o agente recebe do serviço, incluindo configurações de syslog, contadores de desempenho do Linux e logs personalizados. Se esse comando falhar, execute o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Esse comando força o agente programa omsconfig a se comunicar com Azure Monitor e recuperar a configuração mais recente.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problema: você não está vendo nenhum dado de log personalizado 

### <a name="probable-causes"></a>Causas prováveis
* Falha na integração ao Azure Monitor.
* A configuração **aplicar a configuração a seguir aos meus servidores Linux** não foi selecionada.
* o programa omsconfig não selecionou a configuração de log personalizado mais recente do serviço.
* O agente de Log Analytics para `omsagent` de usuário do Linux não pode acessar o log personalizado devido a permissões ou não encontrado.  Você pode ver os seguintes erros:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Problema conhecido com a condição de corrida corrigida no agente Log Analytics para Linux versão 1.1.0-217

### <a name="resolution"></a>Resolução
1. Verifique se a integração com Azure Monitor foi bem-sucedida verificando se o seguinte arquivo existe: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Caso contrário, seja:  

  1. Reintegre usando as [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)de linha de comando do omsadmin.sh.
  2. Em **Configurações avançadas** no portal do Azure, verifique se a configuração **aplicar a configuração a seguir aos meus servidores Linux** está habilitada.  

2. Verifique se o agente de `omsconfig` pode se comunicar com Azure Monitor executando o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.  Esse comando retorna a configuração que o agente recebe do serviço, incluindo configurações de syslog, contadores de desempenho do Linux e logs personalizados. Se esse comando falhar, execute o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Esse comando força o agente programa omsconfig a se comunicar com Azure Monitor e recuperar a configuração mais recente.

**Plano de fundo:** Em vez do agente de Log Analytics para Linux em execução como um usuário `root`com privilégios, o agente é executado como o usuário `omsagent`. Na maioria dos casos, a permissão explícita deve ser concedida a esse usuário para que determinados arquivos sejam lidos. Para conceder permissão para `omsagent` usuário, execute os seguintes comandos:

1. Adicionar o usuário do `omsagent` a um grupo específico `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Conceda acesso de leitura universal ao arquivo necessário `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Há um problema conhecido com uma condição de corrida com a versão do agente Log Analytics para Linux anterior ao 1.1.0-217. Depois de atualizar para o agente mais recente, execute o comando a seguir para obter a versão mais recente do plug-in de saída `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problema: você está tentando reintegre para um novo espaço de trabalho
Quando você tenta reintegre um agente em um novo espaço de trabalho, a configuração do agente de Log Analytics precisa ser limpa antes de reonboarding. Para limpar a configuração antiga do agente, execute o pacote de shell com `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Ou

```
sudo sh ./onboard_agent.sh --purge
```

Você pode continuar reintegre depois de usar a opção `--purge`

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Log Analytics extensão do agente na portal do Azure está marcada com um estado de falha: falha no provisionamento

### <a name="probable-causes"></a>Causas prováveis
* O agente de Log Analytics foi removido do sistema operacional
* Log Analytics serviço do agente está inoperante, desabilitado ou não configurado

### <a name="resolution"></a>Resolução 
Execute as etapas a seguir para corrigir o problema.
1. Remova a extensão do portal do Azure.
2. Instale o agente seguindo as [instruções](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Reinicie o agente executando o seguinte comando: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Aguarde alguns minutos e o estado de provisionamento muda para o **provisionamento bem-sucedido**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problema: a atualização do agente de Log Analytics sob demanda

### <a name="probable-causes"></a>Causas prováveis

Os pacotes do agente de Log Analytics no host estão desatualizados.

### <a name="resolution"></a>Resolução 
Execute as etapas a seguir para corrigir o problema.

1. Verifique a versão mais recente na [página](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Baixar o script de instalação (1.4.2-124 como versão de exemplo):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Atualize os pacotes executando `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
