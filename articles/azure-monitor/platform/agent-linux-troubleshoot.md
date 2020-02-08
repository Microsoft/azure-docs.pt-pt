---
title: Resolver problemas de agente do Linux de análise de registos do Azure | Documentos da Microsoft
description: Descreva os sintomas, as causas e a resolução dos problemas mais comuns com o agente de Log Analytics para Linux no Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: b7036b305b4b1041fced3be68024be29d49a4990
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086862"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Como resolver problemas com o agente do Log Analytics para Linux 

Este artigo fornece ajuda para solucionar erros que podem ocorrer com o Log Analytics Agent para Linux no Azure Monitor e sugere possíveis soluções para resolvê-los.

Se nenhum destes passos resolver, também estão disponíveis os seguintes canais de suporte:

* Os clientes com benefícios de suporte Premier podem abrir um pedido de apoio com a [Premier.](https://premier.microsoft.com/)
* Os clientes com contratos de apoio Azure podem abrir um pedido de apoio [no portal Azure.](https://manage.windowsazure.com/?getsupport=true)
* Diagnosticar Problemas de OMI com o guia de resolução de [problemas oMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Arquivar um [problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Visite a página de Feedback do Log Analytics para rever as ideias e bugs enviados [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) ou arquivar uma nova.  

## <a name="important-log-locations-and-log-collector-tool"></a>Localizações de registo importante e a ferramenta de Recoletor de registos

 Ficheiro | Caminho
 ---- | -----
 Agente de análise de registo para o ficheiro de registo do Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Ficheiro de registo do configuração de agente do log Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Recomendamos que utilize a nossa ferramenta de recoletor de registo para obter os registos importantes para resolução de problemas ou antes de submeter um problema do GitHub. Pode ler mais sobre a ferramenta e como executá-la [aqui.](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)

## <a name="important-configuration-files"></a>Ficheiros de configuração importantes

 Categoria | Localização do Ficheiro
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` ou `/etc/rsyslog.conf` ou `/etc/rsyslog.d/95-omsagent.conf`
 Desempenho, Nagios, Zabbix, saída do Log Analytics e o agente geral | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Configurações adicionais | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >A edição de ficheiros de configuração para contadores de desempenho e Syslog é substituída se a recolha estiver configurada a partir do menu de [dados Log Analytics Advanced Settings](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) no portal Azure para o seu espaço de trabalho. Para desativar a configuração de todos os agentes, desative a recolha a partir de **Definições Avançadas** de Log Analytics ou para um único agente executar o seguinte:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Códigos de erro de instalação

| Código de Erro | Significado |
| --- | --- |
| NOT_DEFINED | Uma vez que as dependências necessárias não estão instaladas, não será instalado o plug-in do auoms auditd | Instalação de auoms falhou, instalar o pacote auditd. |
| 2 | Opção inválido fornecida para o pacote de shell. Executar `sudo sh ./omsagent-*.universal*.sh --help` para uso |
| 3 | Nenhuma opção fornecida para o pacote de shell. Executar `sudo sh ./omsagent-*.universal*.sh --help` para uso. |
| 4 | Configurações de procuração inválidas tipo de pacote OU ou inválidas; Os pacotes omsagent-*rpm*.sh só podem ser instalados em sistemas baseados em RPM, e os pacotes omsagent-deb .sh só podem ser instalados em sistemas baseados em Debian. Recomenda-se que utilize o instalador universal a partir do [lançamento mais recente](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Examine também para verificar as configurações de proxy. |
| 5 | O pacote de shell tem de ser executado como raiz ou ocorreu o erro 403 devolvido durante a integração. Execute o seu comando usando `sudo`. |
| 6 | Arquitetura de pacote inválido OU houve erro 200 devolvido durante o embarque; Os pacotes omsagent-x64.sh*só podem ser instalados em sistemas de 64 bits, e os pacotes x86.sh omsagent*só podem ser instalados em sistemas de 32 bits. Descarregue o pacote correto para a sua arquitetura a partir do [lançamento mais recente.](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest) |
| 17 | Falha na instalação do pacote do OMS. Examine a saída do comando da falha de raiz. |
| 19 | Falha na instalação do pacote da OMI. Examine a saída do comando da falha de raiz. |
| 20 | Falha na instalação do pacote SCX. Examine a saída do comando da falha de raiz. |
| 21 | Falha ao instalar os kits de fornecedor. Examine a saída do comando da falha de raiz. |
| 22 | Falha na instalação do pacote agrupado. Examinar a saída do comando da falha de raiz |
| 23 | Pacote SCX ou OMI já instalado. Utilize `--upgrade` em vez de `--install` para instalar o pacote de conchas. |
| 30 | Erro interno do pacote. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 55 | Versão sem suporte do OpenSSL ou não é possível conectar-se a Azure Monitor ou dpkg está bloqueado ou faltando programa de rotação. |
| 61 | Biblioteca de ctypes de Python em falta. Instale a biblioteca de ctypes Python ou o pacote (python ctypes). |
| 62 | Programa tar em falta, tar de instalação. |
| 63 | Programa po em falta, sed. de instalação |
| 64 | Programa curl em falta, curl de instalação. |
| 65 | Programa gpg em falta, gpg da instalação. |

## <a name="onboarding-error-codes"></a>Códigos de erro de integração

| Código de Erro | Significado |
| --- | --- |
| 2 | Opção inválido fornecida para o script de omsadmin. Executar `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para uso. |
| 3 | Configuração inválida fornecida para o script de omsadmin. Executar `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para uso. |
| 4 | Proxy inválido fornecido para o script de omsadmin. Verifique o representante e consulte a nossa [documentação para utilizar um proxy HTTP](log-analytics-agent.md#network-firewall-requirements). |
| 5 | 403 erro HTTP recebido de Azure Monitor. Ver o resultado completo do script omsadmin para obter detalhes. |
| 6 | Erro de HTTP não 200 recebido de Azure Monitor. Ver o resultado completo do script omsadmin para obter detalhes. |
| 7 | Não é possível conectar-se ao Azure Monitor. Ver o resultado completo do script omsadmin para obter detalhes. |
| 8 | Integração de erro à área de trabalho do Log Analytics. Ver o resultado completo do script omsadmin para obter detalhes. |
| 30 | Erro de script interno. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 31 | ID do agente de geração de erro. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 32 | Erro ao gerar certificados. Ver o resultado completo do script omsadmin para obter detalhes. |
| 33 | Erro ao gerar o metaconfiguration para omsconfig. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 34 | Script de geração de Metaconfiguration não está presente. Voltar a embarcar com `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Ativar o registo de depuração
### <a name="oms-output-plugin-debug"></a>Depuração de plug-in de saída do OMS
 FluentD permite que os níveis de registo específico de plug-in que lhe permite especificar níveis de registo diferentes para entradas e saídas. Para especificar um nível de registo diferente para a saída de OMS, edite a configuração do agente geral em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 No plugin de saída OMS, antes do fim do ficheiro de configuração, altere a propriedade `log_level` de `info` para `debug`:

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

*Registo ativado por depuração de exemplo:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Saída verbosa ideal
Em vez de utilizar o plugin de saída OMS, também pode ser de sobrar itens de dados diretamente para `stdout`, que é visível no agente Log Analytics para ficheiro de registo Linux.

No ficheiro de configuração do agente geral log Analytics em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, comente o plugin de saída OMS adicionando um `#` em frente a cada linha:

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

Abaixo do plugin de saída, descomente a seguinte secção removendo o `#` em frente a cada linha:

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
1. Reonboard para O Monitor Azure com o agente Log Analytics para linux utilizando o seguinte comando com a opção `-v` ativado. Ele permite a saída detalhada do agente que se conecta por meio do proxy para Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Reveja as definições de proxy da secção [Atualização](agent-manage.md#update-proxy-settings) para verificar se configura corretamente o agente para comunicar através de um servidor proxy.    
* Verifique se os seguintes Azure Monitor pontos de extremidade estão na lista de permissões:

    |Recursos do Agente| Portas | Direção |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Porta 443| Entrada e saída |  
    |*.oms.opinsights.azure.com | Porta 443| Entrada e saída |  
    |*.blob.core.windows.net | Porta 443| Entrada e saída |  

    Se planeia utilizar o Trabalhador do Livro Híbrido Azure Automation para se ligar e registar-se no serviço automationpara utilizar livros de execução ou soluções de gestão no seu ambiente, deve ter acesso ao número de porta e aos URLs descritos na Configuração da sua rede para o Trabalhador do [Livro de Execução Híbrido.](../../automation/automation-hybrid-runbook-worker.md#network-planning) 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: Recebe um erro 403 quando tentar carregar

### <a name="probable-causes"></a>Causas prováveis
* Data e hora é incorreta no servidor Linux 
* ID de área de trabalho e a chave de área de trabalho utilizado não estiverem corretos

### <a name="resolution"></a>Resolução

1. Verifique a hora no seu servidor do Linux com a data de comando. Se o tempo é + /-15 minutos da hora atual, em seguida, integração falha. Para correto isso atualizar a data e/ou do fuso horário do seu servidor do Linux. 
2. Certifique-se de que instalou a versão mais recente do agente do Log Analytics para Linux.  A versão mais recente agora notifica que se desfasamento de tempo está a causar a falha de inclusão.
3. Reonboard com o ID de área de trabalho correto e a chave de área de trabalho seguindo as instruções de instalação anteriormente no artigo.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Vê um erro 404 e 500 no ficheiro de registo logo após a integração
Este é um problema conhecido que ocorre no primeiro carregamento de dados do Linux para uma área de trabalho do Log Analytics. Isso não afeta os dados que está a ser enviada ou serviço experiência.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Edição: Vê-se o miagent a usar CPU 100%

### <a name="probable-causes"></a>Causas prováveis
Uma regressão no pacote NSS-pem [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) causou um grave problema de desempenho, que temos visto surgir muito nas distribuições de Redhat/Centos 7.x. Para saber mais sobre este assunto, consulte a seguinte documentação: Bug [1667121 Regressão](https://bugzilla.redhat.com/show_bug.cgi?id=1667121)de desempenho em libcurl .

Os bugs relacionados ao desempenho não acontecem o tempo todo e são muito difíceis de reproduzir. Se você tiver esse problema com o omiagent, deverá usar o script omiHighCPUDiagnostics.sh, que coletará o rastreamento de pilha do omiagent ao exceder um certo limite.

1. Baixar o script <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Executar diagnósticos durante 24 horas com limiar de CPU de 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. A Callstack será despejada em omiagent_trace ficheiro, se notar muitas chamadas de função Curl e NSS, siga os passos de resolução abaixo.

### <a name="resolution-step-by-step"></a>Resolução (passo a passo)

1. Atualize o pacote NSS-pem para [v1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Se o NSS-PEM não estiver disponível para atualização (na maioria das vezes, ocorre no CentOS), faça o downgrade da ondulação para 7.29.0-46. Se, por engano, você executar "atualização yum", a ondulação será atualizada para 7.29.0-51 e o problema ocorrerá novamente. <br/>
`sudo yum downgrade curl libcurl`

3. Reiniciar OMI: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problema: Não está a ver todos os dados no portal do Azure

### <a name="probable-causes"></a>Causas prováveis

- Falha na integração ao Azure Monitor
- A conexão com o Azure Monitor está bloqueada
- Agente de análise do registo de dados do Linux é uma cópia de segurança

### <a name="resolution"></a>Resolução
1. Verifique se o monitor azure de embarque foi bem sucedido verificando se o seguinte ficheiro existe: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard utilizando as instruções de linha de comando `omsadmin.sh`
3. Se utilizar um proxy, consulte os passos de resolução de proxy fornecidos anteriormente.
4. Em alguns casos, quando o agente do Log Analytics para Linux não consegue comunicar com o serviço, dados no agente é colocado em fila para o tamanho do buffer cheio, o que é a 50 MB. O agente deve ser reiniciado executando o seguinte comando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Este problema ser corrigido no 1.1.0-28 de versão do agente e versões posteriores.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problema: Não está a ver as mensagens encaminhadas do Syslog 

### <a name="probable-causes"></a>Causas prováveis
* A configuração aplicada ao servidor Linux não permite a coleção das instalações enviadas e/ou níveis de registo
* Syslog não está a ser reencaminhado corretamente para o servidor Linux
* O número de mensagens sejam encaminhados por segundo é demasiado grande para a configuração base do agente do Log Analytics para Linux lidar com

### <a name="resolution"></a>Resolução
* Certifique-se de que a configuração na área de trabalho do Log Analytics para Syslog tem todas as instalações e os níveis de registo correto. Reveja a [recolha de Syslog no portal Azure](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Verifique se os daemons de mensagens syslog nativos (`rsyslog`, `syslog-ng`) são capazes de receber as mensagens reencaminhadas
* Verifique as definições da firewall no servidor do Syslog para garantir que as mensagens não estão a ser bloqueadas
* Simular uma mensagem Syslog para Log Analytics usando `logger` comando
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problema: Está recebendo Errno endereço já em utilização no ficheiro de registo de omsagent
Se vir `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` em omsagent.log.

### <a name="probable-causes"></a>Causas prováveis
Este erro indica que a extensão de diagnóstico do Linux (LAD) é instalada lado a lado com a extensão de VM do Linux do Log Analytics e mesma porta está a utilizar para a recolha de dados do syslog como omsagent.

### <a name="resolution"></a>Resolução
1. Como raiz, execute os seguintes comandos (Observe que 25224 é um exemplo e é possível que no seu ambiente vê um número de porta diferentes utilizado pelo LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Em seguida, é necessário editar o ficheiro `rsyslogd` ou `syslog_ng` config correto e alterar a configuração relacionada com o LAD para escrever para a porta 25229.

2. Se o VM estiver a funcionar `rsyslogd`, o ficheiro a modificar é: `/etc/rsyslog.d/95-omsagent.conf` (se existir, caso contrário `/etc/rsyslog`). Se o VM estiver a funcionar `syslog_ng`, o ficheiro a modificar é: `/etc/syslog-ng/syslog-ng.conf`.
3. Reiniciar o agente `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Reinicie o serviço syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problema: Não for possível desinstalar omsagent usando a opção de remoção

### <a name="probable-causes"></a>Causas prováveis

* Extensão de diagnóstico do Linux está instalado
* Extensão de diagnóstico do Linux foi instalado e desinstalado, mas ainda verá um erro sobre omsagent a ser utilizado por mdsd e não pode ser removido.

### <a name="resolution"></a>Resolução
1. Desinstale a extensão de diagnóstico do Linux (LAD).
2. Retire os ficheiros de extensão de diagnóstico do Linux da máquina se estiverem presentes no seguinte local: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` e `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problema: Não é possível vê os dados quaisquer dados Nagios 

### <a name="probable-causes"></a>Causas prováveis
* Omsagent. o utilizador não tem permissões para ler a partir do ficheiro de registo da Nagios
* Origem do Nagios e do filtro não tenham sido uncommented do ficheiro de omsagent. Conf

### <a name="resolution"></a>Resolução
1. Adicione o utilizador omsagent para ler a partir do ficheiro Nagios seguindo estas [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. No agente Log Analytics para o ficheiro de configuração geral do Linux em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, certifique-se de que tanto a fonte nagios **como** o filtro não são comentados.

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

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problema: Não vir quaisquer dados do Linux 

### <a name="probable-causes"></a>Causas prováveis
* Falha na integração ao Azure Monitor
* A conexão com o Azure Monitor está bloqueada
* Máquina virtual foi reiniciada
* Pacote OMI manualmente tiver sido atualizado para uma versão mais recente em comparação comparada o que tenha sido instalado pelo agente do Log Analytics para o pacote do Linux
* Classe de registos de recursos DSC *não encontrou* erro no ficheiro de registo `omsconfig.log`
* Agente de análise de registo para dados de cópia de segurança
* Registos DSC A configuração atual *não existe. Execute o comando Start-DscConfiguration com o parâmetro -Path para especificar um ficheiro de configuração e criar uma configuração de corrente primeiro.* no ficheiro de registo `omsconfig.log`, mas não existe nenhuma mensagem de registo sobre `PerformRequiredConfigurationChecks` operações.

### <a name="resolution"></a>Resolução
1. Instale todas as dependências como auditd pacote.
2. Verifique se o embarque no Azure Monitor foi bem sucedido verificando se o seguinte ficheiro existe: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Se não fosse, retoque utilizando as [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)da linha de comando omsadmin.sh .
4. Se utilizar um proxy, verifique os passos acima de resolução de problemas do proxy.
5. Em alguns sistemas de distribuição do Azure, o daemon de servidor do OMI omid não inicia depois da máquina virtual é reiniciada. Isto resultará na não são apresentados dados relacionados com a solução de auditoria, ChangeTracking ou UpdateManagement. A suposição é iniciar manualmente o servidor omi executando `sudo /opt/omi/bin/service_control restart`.
6. Depois do pacote do OMI manualmente é atualizado para uma versão mais recente, tem de ser reiniciado manualmente para o agente do Log Analytics continuar a funcionar. Este passo é necessário para algumas distribuições em que servidor OMI não for iniciado automaticamente após ser atualizada. Executar `sudo /opt/omi/bin/service_control restart` para reiniciar o OMI.
7. Se vir que a classe de recursos DSC *não encontrou* erro em omsconfig.log, faça `sudo /opt/omi/bin/service_control restart`.
8. Em alguns casos, quando o agente de Log Analytics para Linux não pode se comunicar com Azure Monitor, é feito backup dos dados no agente em todo o tamanho do buffer: 50 MB. O agente deve ser reiniciado executando o seguinte comando `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Este problema ser corrigido no 1.1.0-28 de versão do agente ou posterior
    >

* Se `omsconfig.log` ficheiro de registo não indicar que as operações `PerformRequiredConfigurationChecks` estão a decorrer periodicamente no sistema, pode haver um problema com o trabalho/serviço cron. Certifique-se de que o trabalho de compadrio existe sob `/etc/cron.d/OMSConsistencyInvoker`. Se necessário, execute os seguintes comandos para criar a tarefa de cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Além disso, certifique-se de que o serviço de cron está em execução. Pode utilizar `service cron status` com Debian, Ubuntu, SUSE ou `service crond status` com RHEL, CentOS, Oracle Linux para verificar o estado deste serviço. Se o serviço não existir, pode instalar os binários e iniciar o serviço utilizando o seguinte:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problema: Ao configurar a coleção a partir do portal para contadores de desempenho de Syslog ou Linux, as definições não são aplicadas

### <a name="probable-causes"></a>Causas prováveis
* O agente do Log Analytics para Linux não tem aplicadas a configuração mais recente
* Não foram aplicadas as definições alteradas no portal

### <a name="resolution"></a>Resolução
**Fundo:** `omsconfig` é o agente Log Analytics para o agente de configuração Linux que procura uma nova configuração do lado do portal a cada cinco minutos. Esta configuração é aplicada para o agente do Log Analytics para ficheiros de configuração do Linux localizado em /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* Em alguns casos, o agente do Log Analytics para o agente de configuração do Linux poderá não conseguir comunicar com o serviço de configuração do portal, resultando em configuração mais recente, não sendo aplicada.
  1. Verifique se o agente `omsconfig` está instalado executando `dpkg --list omsconfig` ou `rpm -qi omsconfig`.  Se não estiver instalado, reinstale a versão mais recente do agente do Log Analytics para Linux.

  2. Verifique se o agente `omsconfig` pode comunicar com o Monitor Azure executando as seguintes `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`de comando . Este comando devolve a configuração que o agente receber do serviço, incluindo definições de Syslog, contadores de desempenho do Linux e registos personalizados. Se este comando falhar, execute o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Esse comando força o agente programa omsconfig a se comunicar com Azure Monitor e recuperar a configuração mais recente.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problema: Não vir quaisquer dados de registo personalizado 

### <a name="probable-causes"></a>Causas prováveis
* Falha na integração ao Azure Monitor.
* A **definição Aplicar a seguinte configuração aos meus Servidores Linux** não foi selecionada.
* omsconfig não tenha capturado a configuração de registo personalizado mais recente do serviço.
* O agente de Log Analytics para o utilizador do Linux `omsagent` não consegue aceder ao registo personalizado devido a permissões ou não ter sido encontrado.  Pode ver os seguintes erros:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Problema com a condição de corrida corrigidos no agente do Log Analytics para Linux versão 1.1.0-217 conhecido

### <a name="resolution"></a>Resolução
1. A verificação do embarque no Azure Monitor foi bem sucedida verificando se existe o seguinte ficheiro: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Se não, qualquer um de:  

  1. Reonboard utilizando as [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)da linha de comando omsadmin.sh .
  2. Em **Definições Avançadas** no portal Azure, certifique-se de que a definição **Aplique a seguinte configuração nos meus Servidores Linux.**  

2. Verifique se o agente `omsconfig` pode comunicar com o Monitor Azure executando as seguintes `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`de comando .  Este comando devolve a configuração que o agente receber do serviço, incluindo definições de Syslog, contadores de desempenho do Linux e registos personalizados. Se este comando falhar, execute o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Esse comando força o agente programa omsconfig a se comunicar com Azure Monitor e recuperar a configuração mais recente.

**Antecedentes:** Em vez do agente Log Analytics para o Linux a funcionar como um utilizador privilegiado - `root`, o agente funciona como utilizador `omsagent`. Na maioria dos casos, a permissão explícita deve ser concedido a este utilizador para que determinados arquivos a serem lidos. Para conceder permissão a `omsagent` utilizador, execute os seguintes comandos:

1. Adicione o utilizador `omsagent` a `sudo usermod -a -G <GROUPNAME> <USERNAME>` de grupo específico
2. Conceder acesso universal de leitura ao arquivo necessário `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Existe um problema conhecido com uma condição de corrida com o agente do Log Analytics para versão anterior ao 1.1.0-217 do Linux. Depois de atualizar para o agente mais recente, execute o seguinte comando para obter a versão mais recente do plugin de saída `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problema: Está tentando reonboard para uma nova área de trabalho
Quando tentar reonboard um agente para uma nova área de trabalho, a configuração do agente do Log Analytics tem de ser limpos antes reonboarding. Para limpar a configuração antiga do agente, execute o pacote de conchas com `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Ou

```
sudo sh ./onboard_agent.sh --purge
```

Pode continuar a reonboard depois de utilizar a opção `--purge`

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Extensão de agente do log Analytics no portal do Azure está marcado com estado de falha: Falha ao aprovisionar

### <a name="probable-causes"></a>Causas prováveis
* Agente do log Analytics foi removido do sistema operativo
* Serviço de agente do log Analytics está inativo, desativado ou não configurado

### <a name="resolution"></a>Resolução 
Execute os seguintes passos para corrigir o problema.
1. Remova a extensão do portal do Azure.
2. Instale o agente seguindo as [instruções](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Reiniciar o agente executando o seguinte comando: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Aguarde vários minutos e as alterações do Estado de provisionamento ao **Provisioning foram bem sucedidas.**


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problema: Sob demanda de atualizar o agente Log Analytics

### <a name="probable-causes"></a>Causas prováveis

Os pacotes de agente do Log Analytics no anfitrião estão desatualizados.

### <a name="resolution"></a>Resolução 
Execute os seguintes passos para corrigir o problema.

1. Verifique o mais recente lançamento na [página](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Transferir o script de instalação (1.4.2-124 como versão de exemplo):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Atualize os pacotes executando `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
