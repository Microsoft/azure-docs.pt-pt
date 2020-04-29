---
title: Troubleshoot Azure Log Analytics Linux Agent [ Microsoft Docs
description: Descreva os sintomas, causas e resolução para os problemas mais comuns com o agente Log Analytics para linux no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 2343de97d06abdefed2c2977a7341aa411429319
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80520747"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>How to troubleshoot issues with the Log Analytics agent for Linux (Como resolver problemas com o agente do Log Analytics para Linux) 

Este artigo fornece erros de resolução de problemas que poderá experimentar com o agente Log Analytics para o Linux no Azure Monitor e sugere possíveis soluções para os resolver.

Se nenhuma destas etapas funcionar para si, também estão disponíveis os seguintes canais de apoio:

* Os clientes com benefícios de suporte Premier podem abrir um pedido de apoio com a [Premier.](https://premier.microsoft.com/)
* Os clientes com contratos de apoio Azure podem abrir um pedido de apoio [no portal Azure.](https://manage.windowsazure.com/?getsupport=true)
* Diagnosticar Problemas de OMI com o guia de resolução de [problemas oMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Arquivar um [problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Visite a página de Feedback do Log [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) Analytics para rever as ideias e bugs enviados ou arquivar um novo.  

## <a name="important-log-locations-and-log-collector-tool"></a>Localizações importantes de log e ferramenta de colecionador de log

 Ficheiro | Caminho
 ---- | -----
 Agente de Log Analytics para ficheiro de log Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Ficheiro de registo de configuração do agente Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Recomendamos que utilize a nossa ferramenta de colecionador de registos para recuperar registos importantes para resolução de problemas ou antes de enviar um problema gitHub. Pode ler mais sobre a ferramenta e como executá-la [aqui.](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)

## <a name="important-configuration-files"></a>Ficheiros de configuração importantes

 Categoria | Localização do Ficheiro
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf`ou `/etc/rsyslog.conf` ou ou`/etc/rsyslog.d/95-omsagent.conf`
 Desempenho, Nagios, Zabbix, saída de Log Analytics e agente geral | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Configurações adicionais | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >A edição de ficheiros de configuração para contadores de desempenho e Syslog é substituída se a recolha estiver configurada a partir do menu de [dados Log Analytics Advanced Settings](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) no portal Azure para o seu espaço de trabalho. Para desativar a configuração de todos os agentes, desative a recolha a partir de **Definições Avançadas** de Log Analytics ou para um único agente executar o seguinte:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Códigos de erro de instalação

| Código de Erro | Significado |
| --- | --- |
| NOT_DEFINED | Como as dependências necessárias não são instaladas, o plugin auditado auoms não será instalado | Instalação de auomas falhou, instale pacote auditado. |
| 2 | Opção inválida fornecida ao pacote de conchas. Correr `sudo sh ./omsagent-*.universal*.sh --help` para uso |
| 3 | Nenhuma opção fornecida para o pacote de conchas. Corra `sudo sh ./omsagent-*.universal*.sh --help` para o uso. |
| 4 | Configurações de procuração inválidas tipo de pacote OU ou inválidas; Os pacotes omsagent-*rpm*.sh só podem ser instalados em sistemas baseados em RPM, e os pacotes omsagent-deb .sh só podem ser instalados em sistemas baseados em Debian.*deb* Recomenda-se que utilize o instalador universal a partir do [lançamento mais recente](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Consulte também para verificar as definições de procuração. |
| 5 | O feixe deve ser executado como raiz OU houve 403 erros devolvidos durante o embarque. Executar o `sudo`seu comando usando . |
| 6 | Arquitetura de pacote inválido OU houve erro 200 devolvido durante o embarque; Os pacotes omsagent-x64.sh*só podem ser instalados em sistemas de 64 bits, e os pacotes x86.sh omsagent*só podem ser instalados em sistemas de 32 bits. Descarregue o pacote correto para a sua arquitetura a partir do [lançamento mais recente.](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest) |
| 17 | A instalação do pacote OMS falhou. Olhe através da saída de comando para a falha de raiz. |
| 19 | A instalação do pacote OMI falhou. Olhe através da saída de comando para a falha de raiz. |
| 20 | A instalação do pacote SCX falhou. Olhe através da saída de comando para a falha de raiz. |
| 21 | A instalação de kits de fornecedor falhou. Olhe através da saída de comando para a falha de raiz. |
| 22 | A instalação de pacote agregado falhou. Olhe através da saída de comando para a falha de raiz |
| 23 | Pacote SCX ou OMI já instalado. Utilize `--upgrade` em `--install` vez de instalar o pacote de conchas. |
| 30 | Erro de pacote interno. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 55 | Versão aberta não suportada OU Não pode ligar-se ao Azure Monitor OU dpkg está bloqueado ou programa de caracóis em falta. |
| 61 | Biblioteca de ctypes python desaparecido. Instale a biblioteca ou embalagem python ctypes (python-ctypes). |
| 62 | Programa de alcatrão desaparecido, instale alcatrão. |
| 63 | Falta ndo programa de sed, instale sed. |
| 64 | Programa de caracóis em falta, instale caracóis. |
| 65 | Programa GPG em falta, instala o GPG. |

## <a name="onboarding-error-codes"></a>Códigos de erro de embarque

| Código de Erro | Significado |
| --- | --- |
| 2 | Opção inválida fornecida ao script omsadmin. Corra `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para o uso. |
| 3 | Configuração inválida fornecida ao script omsadmin. Corra `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para o uso. |
| 4 | Procuração inválida fornecida ao guião omsadmin. Verifique o representante e consulte a nossa [documentação para utilizar um proxy HTTP](log-analytics-agent.md#firewall-requirements). |
| 5 | Erro HTTP 403 recebido do Monitor Azure. Consulte a saída completa do guião omsadmina para mais detalhes. |
| 6 | Erro NÃO-200 HTTP recebido do Monitor Azure. Consulte a saída completa do guião omsadmina para mais detalhes. |
| 7 | Incapaz de ligar ao Monitor Azure. Consulte a saída completa do guião omsadmina para mais detalhes. |
| 8 | Erro no embarque para log Analytics espaço de trabalho. Consulte a saída completa do guião omsadmina para mais detalhes. |
| 30 | Erro interno do guião. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 31 | Identificação do agente gerador de erros. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 32 | Certificados geradores de erros. Consulte a saída completa do guião omsadmina para mais detalhes. |
| 33 | Erro gerador de configuração para omsconfig. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 34 | Roteiro de geração de configuração não está presente. Voltar a embarcar `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`com. |

## <a name="enable-debug-logging"></a>Ativar a exploração de depuração
### <a name="oms-output-plugin-debug"></a>Depuração de plugin de saída OMS
 FluentD permite níveis de registo específicos de plugin, permitindo especificar diferentes níveis de registo para entradas e saídas. Para especificar um nível de registo diferente para a `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`saída de OMS, edite a configuração do agente geral em .  

 No plugin de saída OMS, antes do fim `log_level` do `info` `debug`ficheiro de configuração, altere a propriedade de:

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

O registo de depuração permite-lhe ver uploads emlotado para o Monitor Azure separados por tipo, número de itens de dados e tempo de envio:

*Registo ativado por depuração de exemplo:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Saída verbosa
Em vez de utilizar o plugin de saída OMS, `stdout`também pode ser de saída de itens de dados diretamente para , que é visível no agente Log Analytics para ficheiro de logux.

No ficheiro de configuração `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`do agente geral log Analytics em , `#` comente o plugin de saída OMS adicionando um em frente a cada linha:

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

Abaixo do plugin de saída, descomente a seguinte secção removendo a `#` frente de cada linha:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Emissão: Incapaz de ligar através de procuração ao Monitor Azure

### <a name="probable-causes"></a>Causas prováveis
* O representante especificado durante o embarque foi incorreto
* Os pontos finais do Serviço de Automação Azure Monitor e Azure não estão listados em branco no seu datacenter 

### <a name="resolution"></a>Resolução
1. Reonboard para O Monitor Azure com o agente Log Analytics `-v` para linux utilizando o seguinte comando com a opção ativada. Permite a saída verbosa do agente que liga através do proxy ao Monitor Azure. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Reveja as definições de proxy da secção [Atualização](agent-manage.md#update-proxy-settings) para verificar se configura corretamente o agente para comunicar através de um servidor proxy.    

3. Verifique duas vezes se os pontos finais descritos na lista de [requisitos](log-analytics-agent.md#firewall-requirements) de firewall da rede Azure Monitor são adicionados corretamente a uma lista de autorizações. Se utilizar a Automatização Azure, os passos de configuração de rede necessários também estão ligados acima.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Emissão: Recebe um erro de 403 ao tentar embarcar

### <a name="probable-causes"></a>Causas prováveis
* Data e Hora estão incorretos no Servidor Linux 
* Id do espaço de trabalho e chave do espaço de trabalho utilizados não estão corretos

### <a name="resolution"></a>Resolução

1. Verifique a hora no seu servidor Linux com a data de comando. Se o tempo for de +/- 15 minutos a partir do tempo atual, então o embarque falha. Para corrigir esta atualização, a data e/ou fuso horário do seu servidor Linux. 
2. Verifique se instalou a versão mais recente do agente Log Analytics para o Linux.  A versão mais recente agora notifica-o se o tempo estiver a causar a falha no embarque.
3. Reonboard utilizando id de espaço de trabalho correto e chave workspace seguindo as instruções de instalação anteriormente neste artigo.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Emissão: Vê um erro de 500 e 404 no ficheiro de registo logo após o embarque
Este é um problema conhecido que ocorre no primeiro upload de dados do Linux para um espaço de trabalho de Log Analytics. Isto não afeta o envio de dados ou a experiência de serviço.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Edição: Vê-se o miagent a usar CPU 100%

### <a name="probable-causes"></a>Causas prováveis
Uma regressão no pacote NSS-pem [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) causou um grave problema de desempenho, que temos visto surgir muito nas distribuições de Redhat/Centos 7.x. Para saber mais sobre este assunto, consulte a seguinte documentação: Bug [1667121 Regressão](https://bugzilla.redhat.com/show_bug.cgi?id=1667121)de desempenho em libcurl .

Bugs relacionados com o desempenho não acontecem a toda a hora, e são muito difíceis de reproduzir. Se tiver esse problema com o omiagent, deve utilizar o guião omiHighCPUDiagnostics.sh que recolherá o traço da pilha do omiagent quando exceder um determinado limiar.

1. Descarregue o script <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Executar diagnósticos durante 24 horas com limiar de CPU de 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. A Callstack será despejada em omiagent_trace ficheiro, se notar muitas chamadas de função Curl e NSS, siga os passos de resolução abaixo.

### <a name="resolution-step-by-step"></a>Resolução (passo a passo)

1. Atualize o pacote NSS-pem para [v1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Se o NSS-pem não estiver disponível para atualização (a maioria acontece em Centos), então reduza o caracol para 7.29.0-46. Se por engano executar "yum update", então o curl será atualizado para 7.29.0-51 e o problema voltará a acontecer. <br/>
`sudo yum downgrade curl libcurl`

3. Reiniciar o OMI: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problema: Não está a ver nenhum dado no portal Azure

### <a name="probable-causes"></a>Causas prováveis

- O embarque no Monitor Azure falhou
- A ligação ao Monitor Azure está bloqueada
- O agente de Log Analytics para dados linux é apoiado

### <a name="resolution"></a>Resolução
1. Verifique se o monitor Azure de embarque foi bem sucedido verificando se o seguinte ficheiro existe:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard utilizando `omsadmin.sh` as instruções da linha de comando
3. Se utilizar um representante, consulte as medidas de resolução de procuração fornecidas anteriormente.
4. Em alguns casos, quando o agente Log Analytics do Linux não consegue comunicar com o serviço, os dados do agente são em fila para o tamanho total do tampão, que é de 50 MB. O agente deve ser reiniciado executando o seguinte comando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Esta emissão é corrigida na versão 1.1.0-28 do agente e posteriormente.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problema: Não está a ver mensagens syslog reencaminhadas 

### <a name="probable-causes"></a>Causas prováveis
* A configuração aplicada ao servidor Linux não permite a recolha das instalações enviadas e/ou níveis de registo
* O Syslog não está a ser encaminhado corretamente para o servidor Linux.
* O número de mensagens que estão a ser reencaminhados por segundo são demasiado grandes para a configuração base do agente Log Analytics para o Linux lidar

### <a name="resolution"></a>Resolução
* Verifique se a configuração no espaço de trabalho do Log Analytics para o Syslog tem todas as instalações e os níveis de registo corretos. Reveja a [recolha de Syslog no portal Azure](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Verifique se os daemons de mensagens syslog nativos são capazes`rsyslog`de `syslog-ng`receber as mensagens reencaminhadas
* Verifique as definições de firewall no servidor Syslog para garantir que as mensagens não estão a ser bloqueadas
* Simular uma mensagem Syslog `logger` para Log Analytics usando o comando
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problema: Está a receber endereço Errno já em uso no ficheiro de registo omsagent
Se vir `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` em omsagent.log.

### <a name="probable-causes"></a>Causas prováveis
Este erro indica que a extensão de diagnóstico linux (LAD) está instalada lado a lado com a extensão Log Analytics Linux VM, e está a utilizar a mesma porta para a recolha de dados syslog como omsagent.

### <a name="resolution"></a>Resolução
1. Como raiz, execute os seguintes comandos (note que 25224 é um exemplo e é possível que no seu ambiente você veja um número de porta diferente usado pelo LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Em seguida, é `rsyslogd` necessário `syslog_ng` editar o ficheiro correto ou config e alterar a configuração relacionada com o LAD para escrever para a porta 25229.

2. Se o VM `rsyslogd`estiver em execução, `/etc/rsyslog.d/95-omsagent.conf` o ficheiro a `/etc/rsyslog`modificar é: (se existir, caso contrário). Se o VM `syslog_ng`estiver em execução, `/etc/syslog-ng/syslog-ng.conf`o ficheiro a modificar é: .
3. Reiniciar o `sudo /opt/microsoft/omsagent/bin/service_control restart`agente.
4. Reiniciar o serviço de syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problema: Não é possível desinstalar omsagent utilizando a opção de purga

### <a name="probable-causes"></a>Causas prováveis

* Extensão de diagnóstico linux está instalada
* A extensão de diagnóstico do Linux foi instalada e desinstalada, mas ainda se vê um erro sobre o agente omsagent ser usado por mdsd e não pode ser removido.

### <a name="resolution"></a>Resolução
1. Desinstale a extensão de diagnóstico linux (LAD).
2. Retire os ficheiros de extensão de diagnóstico do `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` Linux da máquina se estiverem presentes no seguinte local: e `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problema: Não pode ver dados de dados nagios 

### <a name="probable-causes"></a>Causas prováveis
* Utilizador da Omsagent não tem permissões para ler a partir do ficheiro de log nagios
* Nagios fonte e filtro não foram não comentados do ficheiro omsagent.conf

### <a name="resolution"></a>Resolução
1. Adicione o utilizador omsagent para ler a partir do ficheiro Nagios seguindo estas [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. No agente Log Analytics para o `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`ficheiro de configuração geral do Linux, certifique-se de que **tanto** a fonte nagios como o filtro não são comentados.

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

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problema: Não está a ver nenhum dado linux 

### <a name="probable-causes"></a>Causas prováveis
* O embarque no Monitor Azure falhou
* A ligação ao Monitor Azure está bloqueada
* A máquina virtual foi reiniciada
* O pacote OMI foi atualizado manualmente para uma versão mais recente em comparação com o que foi instalado pelo agente Log Analytics para o pacote Linux
* Classe de registos de recursos `omsconfig.log` DSC não *encontrou* erro no ficheiro de registo
* O agente de Log Analytics para dados é apoiado
* Registos DSC A configuração atual *não existe. Execute o comando Start-DscConfiguration com o parâmetro -Path para especificar um ficheiro de configuração e criar uma configuração de corrente primeiro.* no `omsconfig.log` ficheiro de registo, mas `PerformRequiredConfigurationChecks` não existe nenhuma mensagem de registo sobre operações.

### <a name="resolution"></a>Resolução
1. Instale todas as dependências, como pacote auditado.
2. Verifique se o embarque no Azure Monitor foi bem `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`sucedido verificando se o seguinte ficheiro existe: .  Se não fosse, retoque utilizando as [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)da linha de comando omsadmin.sh .
4. Se utilizar um representante, verifique os passos de resolução de problemas por procuração acima.
5. Em alguns sistemas de distribuição Azure, o daemon do servidor Omid OMI não começa depois de a máquina virtual ser reiniciada. Isto resultará em não ver dados relacionados com auditoria, ChangeTracking ou UpdateManagement. A suposição é iniciar manualmente `sudo /opt/omi/bin/service_control restart`o servidor omi executando .
6. Depois de o pacote OMI ser atualizado manualmente para uma versão mais recente, tem de ser reiniciado manualmente para que o agente Log Analytics continue a funcionar. Este passo é necessário para alguns distros onde o servidor OMI não começa automaticamente depois de atualizado. Corra `sudo /opt/omi/bin/service_control restart` para reiniciar o OMI.
7. Se vir a classe de recursos DSC *não encontrar* erro `sudo /opt/omi/bin/service_control restart`em omsconfig.log, corra .
8. Em alguns casos, quando o agente Log Analytics do Linux não pode falar com o Azure Monitor, os dados sobre o agente são apoiados até ao tamanho total do tampão: 50 MB. O agente deve ser reiniciado `/opt/microsoft/omsagent/bin/service_control restart`executando o seguinte comando .

    >[!NOTE]
    >Esta edição é corrigida na versão 1.1.0-28 ou posterior
    >

* Se `omsconfig.log` o ficheiro de `PerformRequiredConfigurationChecks` registo não indicar que as operações estão a decorrer periodicamente no sistema, pode haver um problema com o trabalho/serviço cron. Certifique-se de `/etc/cron.d/OMSConsistencyInvoker`que o trabalho de compadrio existe em . Se necessário executar os seguintes comandos para criar o trabalho de compadrio:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Além disso, certifique-se de que o serviço de cron está funcionando. Pode utilizar `service cron status` com Debian, Ubuntu, `service crond status` SUSE ou com RHEL, CentOS, Oracle Linux para verificar o estado deste serviço. Se o serviço não existir, pode instalar os binários e iniciar o serviço utilizando o seguinte:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problema: Ao configurar a recolha do portal para balcões de desempenho Syslog ou Linux, as definições não são aplicadas

### <a name="probable-causes"></a>Causas prováveis
* O agente Log Analytics do Linux não captou a configuração mais recente
* As alterações alteradas no portal não foram aplicadas

### <a name="resolution"></a>Resolução
**Fundo:** `omsconfig` é o agente Log Analytics para o agente de configuração Linux que procura uma nova configuração do lado do portal a cada cinco minutos. Esta configuração é então aplicada ao agente Log Analytics para ficheiros de configuração Linux localizados em /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* Em alguns casos, o agente Log Analytics para o agente de configuração Linux pode não ser capaz de comunicar com o serviço de configuração do portal, resultando na não aplicação da configuração mais recente.
  1. Verifique se `omsconfig` o agente está `dpkg --list omsconfig` `rpm -qi omsconfig`instalado em execução ou .  Se não estiver instalado, reinstale a versão mais recente do agente Log Analytics para o Linux.

  2. Verifique se `omsconfig` o agente pode comunicar com o `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`Monitor Azure executando o seguinte comando . Este comando devolve a configuração que o agente recebe do serviço, incluindo definições de Syslog, contadores de desempenho Linux e registos personalizados. Se este comando falhar, execute o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Este comando obriga o agente omsconfig a falar com o Monitor Azure e recuperar a configuração mais recente.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problema: Não está a ver quaisquer dados de registo personalizados 

### <a name="probable-causes"></a>Causas prováveis
* O embarque no Monitor Azure falhou.
* A **definição Aplicar a seguinte configuração aos meus Servidores Linux** não foi selecionada.
* A omsconfig não captou a mais recente configuração de registo personalizado do serviço.
* O agente Desinformação de log para o utilizador `omsagent` do Linux não consegue aceder ao registo personalizado devido a permissões ou não ser encontrado.  Pode ver os seguintes erros:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Problema conhecido com condição de corrida fixado no agente Log Analytics para a versão Linux 1.1.0-217

### <a name="resolution"></a>Resolução
1. A verificação do embarque no Azure Monitor foi `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`bem sucedida verificando se existe o seguinte ficheiro: . Se não, também:  

  1. Reonboard utilizando as [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)da linha de comando omsadmin.sh .
  2. Em **Definições Avançadas** no portal Azure, certifique-se de que a definição **Aplique a seguinte configuração nos meus Servidores Linux.**  

2. Verifique se `omsconfig` o agente pode comunicar com o `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`Monitor Azure executando o seguinte comando .  Este comando devolve a configuração que o agente recebe do serviço, incluindo definições de Syslog, contadores de desempenho Linux e registos personalizados. Se este comando falhar, execute o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Este comando obriga o agente omsconfig a falar com o Monitor Azure e recuperar a configuração mais recente.

**Antecedentes:** Em vez do agente Log Analytics para o `root`Linux a funcionar `omsagent` como um utilizador privilegiado - o agente funciona como utilizador. Na maioria dos casos, deve ser concedida permissão explícita a este utilizador para que certos ficheiros sejam lidos. Para conceder `omsagent` permissão ao utilizador, executar os seguintes comandos:

1. Adicione `omsagent` o utilizador a um grupo específico`sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Conceder acesso universal de leitura ao ficheiro necessário`sudo chmod -R ugo+rx <FILE DIRECTORY>`

Há um problema conhecido com uma condição de corrida com o agente Log Analytics para a versão Linux mais cedo do que 1.1.0-217. Depois de atualizar para o agente mais recente, execute o `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`seguinte comando para obter a versão mais recente do plugin de saída .

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Questão: Está a tentar reembarcar para um novo espaço de trabalho
Quando tenta reembarcar um agente para um novo espaço de trabalho, a configuração do agente Log Analytics precisa de ser limpa antes de reembarcar. Para limpar a configuração antiga do agente, executar o pacote de concha com`--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Ou

```
sudo sh ./onboard_agent.sh --purge
```

Pode continuar a reonboard `--purge` depois de usar a opção

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>A extensão do agente Log Analytics no portal Azure é marcada com um estado falhado: O provisionamento falhou

### <a name="probable-causes"></a>Causas prováveis
* O agente Log Analytics foi removido do sistema operativo
* O serviço de agente Log Analytics está para baixo, desativado ou não configurado

### <a name="resolution"></a>Resolução 
Execute os seguintes passos para corrigir o problema.
1. Remova a extensão do portal Azure.
2. Instale o agente seguindo as [instruções](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Reiniciar o agente executando `sudo /opt/microsoft/omsagent/bin/service_control restart`o seguinte comando: .
* Aguarde vários minutos e as alterações do Estado de provisionamento ao **Provisioning foram bem sucedidas.**


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Emissão: O upgrade do agente Log Analytics a pedido

### <a name="probable-causes"></a>Causas prováveis

Os pacotes de agentes Log Analytics no hospedeiro estão desatualizados.

### <a name="resolution"></a>Resolução 
Execute os seguintes passos para corrigir o problema.

1. Verifique o mais recente lançamento na [página](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Descarregue o script de instalação (1.4.2-124 como versão exemplo):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Atualizar pacotes executando `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
