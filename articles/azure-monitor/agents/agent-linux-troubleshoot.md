---
title: Resolução de problemas Azure Log Analytics Linux Agente | Microsoft Docs
description: Descreva os sintomas, as causas e a resolução para as questões mais comuns com o agente Log Analytics para o Linux no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 136e063f6d272589c609bad93532df025a15a68d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723627"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>How to troubleshoot issues with the Log Analytics agent for Linux (Como resolver problemas com o agente do Log Analytics para Linux) 

Este artigo fornece falhas de resolução de problemas que pode experimentar com o agente Log Analytics para o Linux no Azure Monitor e sugere possíveis soluções para os resolver.

Se nenhum destes passos funcionar para si, estão também disponíveis os seguintes canais de suporte:

* Os clientes com benefícios de suporte Premier podem abrir um pedido de apoio com [o Premier.](https://premier.microsoft.com/)
* Os clientes com contratos de apoio Azure podem abrir um pedido de apoio [no portal Azure.](https://manage.windowsazure.com/?getsupport=true)
* Diagnosticar problemas de OMI com o [guia de resolução de problemas do OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Arquivar uma [edição do GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Visite a página de Feedback do Log Analytics para rever as ideias e bugs submetidos [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) ou arquivar uma nova. 

## <a name="log-analytics-troubleshooting-tool"></a>Ferramenta de resolução de problemas de log analytics

A Ferramenta de resolução de problemas do Agente Desanal do Registo Linux é um script concebido para ajudar a encontrar e diagnosticar problemas com o Agente de Análise de Registo. É automaticamente incluído com o agente aquando da instalação. Executar a ferramenta deve ser o primeiro passo para diagnosticar um problema.

### <a name="how-to-use"></a>Como usar
A Ferramenta de resolução de problemas pode ser executada colando o seguinte comando numa janela de terminal numa máquina com o agente Log Analytics: `sudo /opt/microsoft/omsagent/bin/troubleshooter`

### <a name="manual-installation"></a>Instalação Manual
A Ferramenta de Resolução de Problemas é automaticamente incluída na instalação do Agente De Análise de Registo. No entanto, se a instalação falhar de alguma forma, também pode ser instalada manualmente seguindo os passos abaixo.

1. Copie o pacote de resolução de problemas na sua máquina: `wget https://raw.github.com/microsoft/OMS-Agent-for-Linux/master/source/code/troubleshooter/omsagent_tst.tar.gz`
2. Desempacotar o pacote: `tar -xzvf omsagent_tst.tar.gz`
3. Executar a instalação manual: `sudo ./install_tst`

### <a name="scenarios-covered"></a>Cenários Cobertos
Abaixo está uma lista de cenários verificados pela Ferramenta de Resolução de Problemas:

1. Agente não é saudável, batimentos cardíacos não funcionam corretamente.
2. O agente não começa, não consegue ligar-se aos Serviços Analíticos de Registo
3. O agente Syslog não está a funcionar.
4. O agente tem um alto uso de CPU/ memória
5. Agente com problemas de instalação
6. Os registos personalizados do agente não estão a funcionar.
7. Recolher registos de agentes

Para mais detalhes, consulte a nossa [documentação do Github.](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting-Tool.md)

 >[!NOTE]
 >Por favor, execute a ferramenta Log Collector quando tiver um problema. Ter os registos inicialmente ajudará muito a nossa equipa de suporte a resolver o seu problema mais rapidamente.

## <a name="purge-and-re-install-the-linux-agent"></a>Purgar e Re-Install o Agente Linux

Vimos que uma reinstalação limpa do agente resolverá a maioria dos problemas. Na verdade, esta pode ser a primeira sugestão do Apoio para colocar o Agente num estado não acorrido da nossa equipa de apoio. Executar o resolução de problemas, registar recolha e tentar uma reinstalação limpa ajudará a resolver os problemas mais rapidamente.

1. Descarregue o script de purga:
- `$ wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/purge_omsagent.sh`
2. Executar o script de purga (com permissões de sudo):
- `$ sudo sh purge_omsagent.sh`

## <a name="important-log-locations-and-log-collector-tool"></a>Localizações importantes do registo e ferramenta de colecionador de registos

 Ficheiro | Caminho
 ---- | -----
 Log Analytics agente para ficheiro de registo Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Registar ficheiro de registo de configuração de agente do Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Recomendamos que utilize a nossa ferramenta de colecionador de registos para recuperar registos importantes para resolução de problemas ou antes de enviar um problema do GitHub. Pode ler mais sobre a ferramenta e como executá-la [aqui.](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)

## <a name="important-configuration-files"></a>Ficheiros de configuração importantes

 Categoria | Localização do Ficheiro
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` ou `/etc/rsyslog.conf` ou `/etc/rsyslog.d/95-omsagent.conf`
 Desempenho, Nagios, Zabbix, Log Analytics e agente geral | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Configurações adicionais | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >A edição de ficheiros de configuração para contadores de desempenho e Syslog é substituída se a recolha for configurada a partir do menu de [dados Log Analytics Advanced Settings](../agents/agent-data-sources.md#configuring-data-sources) no portal Azure para o seu espaço de trabalho. Para desativar a configuração para todos os agentes, desative a recolha das **Definições Avançadas** de Log Analytics ou para um único agente executar o seguinte:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Códigos de erro de instalação

| Código de Erro | Significado |
| --- | --- |
| NOT_DEFINED | Como as dependências necessárias não estão instaladas, o plugin auditado auoms não será instalado | A instalação de auoms falhou, instalou pacote auditado. |
| 2 | Opção inválida fornecida ao pacote de conchas. Correr `sudo sh ./omsagent-*.universal*.sh --help` para uso |
| 3 | Nenhuma opção fornecida ao pacote de conchas. Corra `sudo sh ./omsagent-*.universal*.sh --help` para uso. |
| 4 | Configurações de procuração inválidas do tipo de pacote INVÁLidas ou inválidas; Os pacotes de .sh omsagent-*rpm* só podem ser instalados em sistemas baseados em RPM, e pacotes omsagent-deb .sh pacotes só podem ser instalados em sistemas baseados em Debian. Recomenda-se que utilize o instalador universal a partir da [versão mais recente](../vm/quick-collect-linux-computer.md#install-the-agent-for-linux). Reveja também para verificar as definições de procuração. |
| 5 | O feixe de concha deve ser executado como raiz OU houve 403 erros devolvidos durante o embarque. Executar o seu comando utilizando `sudo` . |
| 6 | Arquitetura de pacote inválida OU houve erro 200 devolvido durante o embarque; Os *pacotes omsagent-x64.sh só podem ser instalados em sistemas de 64 bits, e* os pacotes x86.sh só podem ser instalados em sistemas de 32 bits. Descarregue o pacote correto para a sua arquitetura a partir do [último lançamento.](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest) |
| 17 | A instalação do pacote OMS falhou. Procurem através da saída de comando para a falha da raiz. |
| 18 | A instalação do pacote OMSConfig falhou. Procurem através da saída de comando para a falha da raiz. |
| 19 | A instalação do pacote OMI falhou. Procurem através da saída de comando para a falha da raiz. |
| 20 | A instalação do pacote SCX falhou. Procurem através da saída de comando para a falha da raiz. |
| 21 | A instalação de kits provider falhou. Procurem através da saída de comando para a falha da raiz. |
| 22 | A instalação do pacote agregado falhou. Procure através da saída de comando para a falha da raiz |
| 23 | Pacote SCX ou OMI já instalado. Utilize `--upgrade` em vez de instalar o pacote de `--install` conchas. |
| 30 | Erro interno do pacote. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 55 | Versão de abertura não suportada OR Não pode ligar-se ao Azure Monitor OU dpkg está bloqueado ou em falta do programa curl. |
| 61 | Biblioteca de cótipos Python desaparecidos. Instale a biblioteca ou embalagem de cótipos Python (python-ctypes). |
| 62 | Programa de alcatrão desaparecido, instale alcatrão. |
| 63 | Programa de sed falta, instalar sed. |
| 64 | Programa de caracóis em falta, instale caracóis. |
| 65 | Programa gpg em falta, instale o GPG. |

## <a name="onboarding-error-codes"></a>Códigos de erro de embarque

| Código de Erro | Significado |
| --- | --- |
| 2 | Opção inválida fornecida ao script omsadmin. Corra `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para uso. |
| 3 | Configuração inválida fornecida ao script omsadmin. Corra `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` para uso. |
| 4 | Procuração inválida fornecida ao script omsadmin. Verifique o representante e consulte a nossa [documentação para a utilização de um representante HTTP](./log-analytics-agent.md#firewall-requirements). |
| 5 | 403 ERRO HTTP recebido do Azure Monitor. Consulte a saída completa do script omsadmin para obter detalhes. |
| 6 | Erro http não-200 recebido do Azure Monitor. Consulte a saída completa do script omsadmin para obter detalhes. |
| 7 | Não é possível ligar-se ao Monitor Azure. Consulte a saída completa do script omsadmin para obter detalhes. |
| 8 | Erro no embarque para log analytics espaço de trabalho. Consulte a saída completa do script omsadmin para obter detalhes. |
| 30 | Erro de script interno. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 31 | Identificação do agente gerador de erros. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 32 | Certificados geradores de erro. Consulte a saída completa do script omsadmin para obter detalhes. |
| 33 | Erro gerador de metaconfigução para omsconfig. Arquivar um [Problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) com detalhes da saída. |
| 34 | O roteiro da geração de metaconfigução não está presente. Reagem ao embarque com `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>` . |

## <a name="enable-debug-logging"></a>Ativar a exploração madeireira de depurar
### <a name="oms-output-plugin-debug"></a>Plugin de saída OMS depura
 FluentD permite níveis de registo específicos de plugin, permitindo especificar diferentes níveis de registo para entradas e saídas. Para especificar um nível de registo diferente para a saída OMS, edite a configuração do agente geral em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .  

 No plugin de saída OMS, antes do fim do ficheiro de configuração, altere a `log_level` propriedade `info` `debug` de:

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

A depuração permite-lhe ver uploads em lote para O Azure Monitor separados por tipo, número de dados e tempo de envio:

*Registo habilitado para depurar exemplo:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Saída verbose
Em vez de utilizar o plugin de saída OMS, também pode desauscar itens de dados diretamente para `stdout` , o que é visível no agente Log Analytics para ficheiro de registo Linux.

No ficheiro de configuração do agente geral Do Log `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` Analytics, comente o plugin de saída OMS adicionando um `#` à frente de cada linha:

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

Abaixo do plugin de saída, descompromesse a seguinte secção removendo a `#` frente de cada linha:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problema: Incapaz de ligar através de procuração ao Azure Monitor

### <a name="probable-causes"></a>Causas prováveis
* O representante especificado durante o embarque foi incorreto
* Os pontos finais do Serviço de Automação Azure Monitor e Azure Não estão incluídos na lista aprovada no seu datacenter 

### <a name="resolution"></a>Resolução
1. Reonboard para Azure Monitor com o agente Log Analytics para Linux, utilizando o seguinte comando com a opção `-v` ativada. Permite a saída verbosa do agente que se liga através do proxy ao Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Reveja as [definições de procuração](agent-manage.md#update-proxy-settings) de atualização da secção para verificar se configura o agente corretamente para comunicar através de um servidor de procuração.    

3. Verifique duas vezes se os pontos finais descritos na lista [de requisitos](./log-analytics-agent.md#firewall-requirements) de firewall da rede Azure Monitor são adicionados corretamente a uma lista de autorizações. Se utilizar a Azure Automation, os passos de configuração de rede necessários também estão ligados acima.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: Recebe um erro de 403 ao tentar embarcar

### <a name="probable-causes"></a>Causas prováveis
* Data e hora estão incorretas no Servidor Linux 
* ID do espaço de trabalho e chave do espaço de trabalho utilizada não estão corretas

### <a name="resolution"></a>Resolução

1. Verifique a hora no seu servidor Linux com a data de comando. Se o tempo for de +/- 15 minutos da hora atual, então o embarque falha. Para corrigir esta atualização, a data e/ou o tempo de intervalo do seu servidor Linux. 
2. Verifique se instalou a versão mais recente do agente Log Analytics para o Linux.  A versão mais recente agora notifica-o se o distorcer do tempo está a causar a falha de embarque.
3. Reonboard utilizando a chave de ID e espaço de trabalho corretas seguindo as instruções de instalação anteriormente neste artigo.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Vê-se um erro de 500 e 404 no ficheiro de registo logo após o embarque
Este é um problema conhecido que ocorre no primeiro upload de dados linux para um espaço de trabalho Log Analytics. Isto não afeta o envio de dados ou a experiência de serviço.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problema: Você vê omiagent usando 100% CPU

### <a name="probable-causes"></a>Causas prováveis
Uma regressão no pacote nss-pem [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) causou um grave problema de desempenho, que temos visto surgir muito nas distribuições de Redhat/Centos 7.x. Para saber mais sobre este assunto, consulte a seguinte documentação: Bug [1667121 Regressão de desempenho em libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Os bugs relacionados com o desempenho não acontecem a toda a hora, e são muito difíceis de reproduzir. Se sentir tal problema com o omiagent, deve utilizar o script omiHighCPUDiagnostics.sh que recolherá o traço de pilha do omiagent quando exceder um determinado limiar.

1. Descarregue o script <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Executar diagnósticos durante 24 horas com limiar de 30% de CPU <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Callstack será despejado em omiagent_trace ficheiro, se notar muitas chamadas de funções Curl e NSS, siga os passos de resolução abaixo.

### <a name="resolution-step-by-step"></a>Resolução (passo a passo)

1. Atualize o pacote nss-pem para [v1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Se o nss-pem não estiver disponível para upgrade (a maioria acontece em Centos), então desclasse o caracol para 7.29.0-46. Se por engano executar "yum update", então o curl será atualizado para 7.29.0-51 e o problema voltará a acontecer. <br/>
`sudo yum downgrade curl libcurl`

3. Reiniciar o OMI: <br/>
`sudo scxadmin -restart`


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problema: Não está a ver mensagens Syslog reencaminhadas 

### <a name="probable-causes"></a>Causas prováveis
* A configuração aplicada ao servidor Linux não permite a recolha das instalações enviadas e/ou níveis de registo
* O Syslog não está a ser encaminhado corretamente para o servidor Linux
* O número de mensagens a ser reencaminhadas por segundo é demasiado grande para a configuração base do agente Log Analytics para o Linux lidar

### <a name="resolution"></a>Resolução
* Verifique se a configuração no espaço de trabalho do Log Analytics para o Syslog tem todas as instalações e os níveis de registo corretos. [Reveja a coleção Syslog no portal Azure](data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Verifique se os daemons de mensagens syslog nativos são `rsyslog` `syslog-ng` capazes de receber as mensagens reencaminhadas
* Verifique as definições de firewall no servidor Syslog para garantir que as mensagens não estão a ser bloqueadas
* Simular uma mensagem Syslog para Registar Analytics usando `logger` o comando
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problema: Está a receber o endereço Errno já em uso no ficheiro de registo omsagent
Se vir `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` em omsagent.log.

### <a name="probable-causes"></a>Causas prováveis
Este erro indica que a extensão de Diagnóstico Linux (LAD) está instalada lado a lado com a extensão VM do Log Analytics Linux, e está a utilizar a mesma porta para a recolha de dados syslog como omsagent.

### <a name="resolution"></a>Resolução
1. Como raiz, execute os seguintes comandos (note que 25224 é um exemplo e é possível que no seu ambiente você veja um número de porta diferente usado por LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Em seguida, é necessário editar o ficheiro correto `rsyslogd` ou `syslog_ng` config e alterar a configuração relacionada com o LAD para escrever para a porta 25229.

2. Se o VM estiver em `rsyslogd` execução, o ficheiro a ser modificado é: `/etc/rsyslog.d/95-omsagent.conf` (se existir, caso exista), caso `/etc/rsyslog` contrário). Se o VM estiver em `syslog_ng` funcionamento, o ficheiro a modificar é: `/etc/syslog-ng/syslog-ng.conf` .
3. Reiniciar omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart` .
4. Reinicie o serviço de syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problema: Não é capaz de desinstalar o omsagent usando a opção de purga

### <a name="probable-causes"></a>Causas prováveis

* Extensão de diagnóstico Linux está instalada
* A extensão de diagnóstico do Linux foi instalada e desinstalada, mas ainda se vê um erro sobre o omsagent ser usado pela mdsd e não pode ser removido.

### <a name="resolution"></a>Resolução
1. Desinstalar a extensão de diagnóstico Linux (LAD).
2. Remova os ficheiros de extensão de diagnóstico Linux da máquina se estiverem presentes no seguinte local: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` e `/var/opt/microsoft/omsagent/LAD/` .

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problema: Não é possível ver dados de quaisquer dados de Nagios 

### <a name="probable-causes"></a>Causas prováveis
* Utilizador da Omsagent não tem permissões para ler no ficheiro de registo de Nagios
* A fonte e o filtro de Nagios não foram descomprimidos a partir do ficheiro omsagent.conf

### <a name="resolution"></a>Resolução
1. Adicione o utilizador omsagent para ler no ficheiro Nagios seguindo estas [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. No agente Log Analytics para o ficheiro de configuração geral do `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` Linux, certifique-se de que **tanto** a fonte de Nagios como o filtro não estão comentados.

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
* A bordo do Monitor Azure falhou
* A ligação ao Monitor Azure está bloqueada
* Máquina virtual foi reiniciada
* O pacote OMI foi atualizado manualmente para uma versão mais recente em comparação com o que foi instalado pelo agente Log Analytics para o pacote Linux
* OMI está congelado, bloqueando o agente OMS
* Classe de registos de recursos DSC *não encontrou* erro no ficheiro `omsconfig.log` de registo
* O agente do Log Analytics para os dados é apoiado
* Registos DSC *A configuração atual não existe. Execute Start-DscConfiguration comando com parâmetro -Path para especificar um ficheiro de configuração e criar uma configuração atual primeiro.* no `omsconfig.log` ficheiro de registo, mas não existe nenhuma mensagem de registo sobre `PerformRequiredConfigurationChecks` operações.

### <a name="resolution"></a>Resolução
1. Instale todas as dependências como pacote auditado.
2. Verifique se o embarque no Azure Monitor foi bem sucedido verificando se existe o seguinte ficheiro: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` .  Caso contrário, reencaia as [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)da linha de comando omsadmin.sh .
4. Se utilizar um representante, verifique os passos de resolução de problemas por procuração acima.
5. Em alguns sistemas de distribuição Azure, o omid OMI server daemon não começa após o reboot da máquina virtual. Isto resultará em não ver dados relacionados com auditoria, ChangeTracking ou UpdateManagement. A solução é iniciar manualmente o servidor omi executando `sudo /opt/omi/bin/service_control restart` .
6. Depois de o pacote OMI ser atualizado manualmente para uma versão mais recente, tem de ser reiniciado manualmente para que o agente Log Analytics continue a funcionar. Este passo é necessário para alguns distros onde o servidor OMI não começa automaticamente depois de ser atualizado. Corra `sudo /opt/omi/bin/service_control restart` para reiniciar o OMI.
* Em algumas situações, o OMI pode ficar congelado. O agente da OMS pode entrar num estado bloqueado à espera de OMI, bloqueando toda a recolha de dados. O processo de agente da OMS estará em curso, mas não haverá atividade, evidenciado por nenhuma nova linha de registo (como batimentos cardíacos) presentes em `omsagent.log` . Reinicie o OMI `sudo /opt/omi/bin/service_control restart` para recuperar o agente.
7. Se vir a classe de recursos DSC *não encontrada* erro em omsconfig.log, corra `sudo /opt/omi/bin/service_control restart` .
8. Em alguns casos, quando o agente Log Analytics do Linux não pode falar com o Azure Monitor, os dados sobre o agente são apoiados até ao tamanho total do tampão: 50 MB. O agente deve ser reiniciado executando o seguinte comando `/opt/microsoft/omsagent/bin/service_control restart` .

    >[!NOTE]
    >Esta emissão é corrigida na versão 1.1.0-28 do Agente ou posterior
    >

* Se `omsconfig.log` o ficheiro de registo não indicar que as `PerformRequiredConfigurationChecks` operações estão a funcionar periodicamente no sistema, pode haver um problema com o cron job/service. Certifique-se de que o trabalho de cron existe sob `/etc/cron.d/OMSConsistencyInvoker` . Se necessário executar os seguintes comandos para criar o cron job:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Além disso, certifique-se de que o serviço de cron está a funcionar. Pode utilizar `service cron status` com a Debian, Ubuntu, SUSE ou `service crond status` com RHEL, CentOS, Oracle Linux para verificar o estado deste serviço. Se o serviço não existir, pode instalar as binários e iniciar o serviço utilizando o seguinte:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problema: Ao configurar a recolha a partir do portal para contadores de desempenho Syslog ou Linux, as definições não são aplicadas

### <a name="probable-causes"></a>Causas prováveis
* O agente Log Analytics do Linux não captou a mais recente configuração
* As definições alteradas no portal não foram aplicadas

### <a name="resolution"></a>Resolução
**Antecedentes:** `omsconfig` é o agente Log Analytics para o agente de configuração Linux que procura uma nova configuração do lado do portal a cada cinco minutos. Esta configuração é então aplicada ao agente Log Analytics para ficheiros de configuração Linux localizados em /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* Em alguns casos, o agente Log Analytics para o agente de configuração Linux pode não conseguir comunicar com o serviço de configuração do portal, resultando na não aplicação da configuração mais recente.
  1. Verifique se o `omsconfig` agente está instalado em funcionamento ou `dpkg --list omsconfig` `rpm -qi omsconfig` .  Se não for instalado, volte a instalar a versão mais recente do agente Log Analytics para o Linux.

  2. Verifique se o agente pode comunicar com o `omsconfig` Azure Monitor executando o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` . Este comando devolve a configuração que o agente recebe do serviço, incluindo as definições de Syslog, contadores de desempenho Linux e registos personalizados. Se este comando falhar, executar o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . Este comando força o agente omsconfig a falar com o Azure Monitor e a recuperar a configuração mais recente.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problema: Não está a ver quaisquer dados de registo personalizados 

### <a name="probable-causes"></a>Causas prováveis
* O embarque para o Monitor Azure falhou.
* A definição **Aplicar a seguinte configuração nos meus Servidores Linux** não foi selecionada.
* Omsconfig não captou a mais recente configuração de registo personalizado do serviço.
* O agente Log Analytics para o utilizador Linux `omsagent` não consegue aceder ao registo personalizado devido a permissões ou não ser encontrado.  Pode ver os seguintes erros:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Problema conhecido com condição de raça fixado no agente Log Analytics para a versão Linux 1.1.0-217

### <a name="resolution"></a>Resolução
1. Verifique se o bordo do Azure Monitor foi bem sucedido, verificando se existe o seguinte ficheiro: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` . Se não, também:  

  1. Reonboard utilizando as [instruções](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)da linha de comando omsadmin.sh .
  2. Em **Definições Avançadas** no portal Azure, certifique-se de que a definição **Aplicar a seguinte configuração nos meus Servidores Linux** está ativada.  

2. Verifique se o agente pode comunicar com o `omsconfig` Azure Monitor executando o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` .  Este comando devolve a configuração que o agente recebe do serviço, incluindo as definições de Syslog, contadores de desempenho Linux e registos personalizados. Se este comando falhar, executar o seguinte comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . Este comando força o agente omsconfig a falar com o Azure Monitor e a recuperar a configuração mais recente.

**Antecedentes:** Em vez do agente Log Analytics para o Linux funcionar como um utilizador privilegiado - `root` , o agente funciona como `omsagent` utilizador. Na maioria dos casos, deve ser concedida uma autorização explícita a este utilizador para que certos ficheiros sejam lidos. Para conceder permissão ao `omsagent` utilizador, executar os seguintes comandos:

1. Adicione o `omsagent` utilizador a um grupo específico `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Conceder acesso universal de leitura ao ficheiro necessário `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Há um problema conhecido com uma condição de corrida com o agente Log Analytics para a versão Linux mais cedo que 1.1.0-217. Depois de atualizar para o mais recente agente, executar o seguinte comando para obter a versão mais recente do plugin de saída `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problema: Está a tentar reecaçar para um novo espaço de trabalho
Quando tenta reencaçar um agente para um novo espaço de trabalho, a configuração do agente Log Analytics precisa de ser limpa antes de voltar a fazer o ensaio. Para limpar a configuração antiga do agente, executar o pacote de conchas com `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Ou

```
sudo sh ./onboard_agent.sh --purge
```

Pode continuar a fazer reonboard depois de utilizar a `--purge` opção

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>A extensão do agente Log Analytics no portal Azure está marcada com um estado falhado: Provisioning falhou

### <a name="probable-causes"></a>Causas prováveis
* O agente Log Analytics foi removido do sistema operativo
* O serviço de agente do Log Analytics está em baixo, desativado ou não configurado

### <a name="resolution"></a>Resolução 
Execute os seguintes passos para corrigir o problema.
1. Remova a extensão do portal Azure.
2. Instale o agente seguindo as [instruções](../vm/quick-collect-linux-computer.md).
3. Reiniciar o agente executando o seguinte comando: `sudo /opt/microsoft/omsagent/bin/service_control restart` .
* Aguarde vários minutos e as alterações estatais de provisionamento ao **Provisioning foram bem sucedidas.**


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problema: O agente log analytics upgrade on-demand

### <a name="probable-causes"></a>Causas prováveis

Os pacotes de agente do Log Analytics no hospedeiro estão desatualizados.

### <a name="resolution"></a>Resolução 
Execute os seguintes passos para corrigir o problema.

1. Verifique o último lançamento na [página](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Descarregue o script de instalação (1.4.2-124 como versão exemplo):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Atualizar pacotes executando `sudo sh ./omsagent-*.universal.x64.sh --upgrade` .