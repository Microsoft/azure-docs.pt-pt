---
title: Resolver problemas relacionados com o servidor de processos do Azure Site Recovery
description: Este artigo descreve como resolver problemas com o servidor de processos do Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.author: raynew
ms.openlocfilehash: 6e31308800f72d60381f1e4ecd540482ba263851
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969363"
---
# <a name="troubleshoot-the-process-server"></a>Resolver problemas relacionados com o servidor de processos

O [recuperação de Site](site-recovery-overview.md) servidor de processos é utilizado quando configurar a recuperação após desastre para o Azure para VMs de VMware no local e servidores físicos. Este artigo descreve como resolver problemas com o servidor de processos, incluindo problemas de conectividade e replicação.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre o servidor de processos.

## <a name="before-you-start"></a>Antes de começar

Antes de iniciar a resolução de problemas:

1. Certifique-se de que compreende como [monitorizar os servidores de processo](vmware-physical-azure-monitor-process-server.md).
2. Rever as melhores práticas abaixo.
3. Certifique-se de que siga [considerações sobre a capacidade](site-recovery-plan-capacity-vmware.md#capacity-considerations)e utilizar a orientação de dimensionamento para o [servidor de configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) ou [servidores autónomos do processo](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Melhores práticas para a implementação de servidor de processo

Para um desempenho ideal de servidores de processos, podemos resumi um número de melhores práticas gerais.

**Melhor prática** | **Detalhes**
--- |---
**Utilização** | Certifique-se de que o servidor de processos do servidor/autônomos de configuração só são utilizadas para o objetivo pretendido. Não execute qualquer outra coisa na máquina.
**Endereço IP** | Certifique-se de que o servidor de processos tem um endereço IPv4 estático e não tem o NAT configurado.
**Utilização de memória/CPU do controlo** |Mantenha a utilização da CPU e memória em 70%.
**Certifique-se de espaço livre** | Espaço livre refere-se para o espaço em disco de cache no servidor de processos. Dados de replicação são armazenados na cache antes de ser carregado para o Azure.<br/><br/> Manter o espaço livre acima de 25%. Se ficar abaixo de 20%, a replicação está limitada para máquinas replicadas que estão associadas com o servidor de processos.

## <a name="check-process-server-health"></a>Verifique o estado de funcionamento de servidor de processos

O primeiro passo na resolução de problemas é verificar o estado de funcionamento e o estado do servidor de processos. Para fazer isso, reveja todos os alertas, verifique se os serviços necessários estão em execução e verifique se existe um heartbeat do servidor de processos. Estes passos estão resumidos na seguinte gráfico, seguida dos procedimentos para ajudar a efetuar os passos.

![Resolver problemas de estado de funcionamento de servidor de processos](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Passo 1: Resolver problemas relacionados com alertas de estado de funcionamento do servidor de processo

O servidor de processos gera um número de alertas de estado de funcionamento. Estes alertas e ações recomendadas, estão resumidas na tabela seguinte.

**Tipo de alerta** | **Error** | **Resolução de problemas**
--- | --- | --- 
![Bom estado de funcionamento][green] | Nenhuma  | Servidor de processos está ligado e bom estado de funcionamento.
![Aviso][yellow] | Serviços especificados não estão em execução. | 1. Verifique que serviços estão em execução.<br/> 2. Se os serviços estão em execução conforme esperado, siga as instruções abaixo para [resolver problemas de conectividade e replicação](#check-connectivity-and-replication).
![Aviso][yellow]  | Utilização de CPU > 80% para os últimos 15 minutos. | 1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs com o servidor de processos ajustado a [definidos limites](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere criar um [servidor de processos adicionais](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [resolver problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  Utilização de CPU > 95% para os últimos 15 minutos. | 1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs com o servidor de processos ajustado a [definidos limites](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere criar um [servidor de processos adicionais](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [resolver problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [Planeador de implementações](https://aka.ms/asr-v2a-deployment-planner) para a replicação de servidor do VMware/físico.
![Aviso][yellow] | Utilização de memória > 80% para os últimos 15 minutos. |  1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs com o servidor de processos ajustado a [definidos limites](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere criar um [servidor de processos adicionais](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga qualquer instruções associadas com o aviso.<br/> 4. Se o problema persistir, siga as instruções abaixo para [resolver problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] | Utilização de memória > 95% para os últimos 15 minutos. | 1. Não adicione novas máquinas e considerar como configurar uma [servidor de processos adicionais](vmware-azure-set-up-process-server-scale.md).<br/> 2. Siga qualquer instruções associadas com o aviso.<br/> 3. 4. Se o problema persistir, siga as instruções abaixo para [resolver problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [Planeador de implementações](https://aka.ms/asr-v2a-deployment-planner) para problemas de replicação de servidor do VMware/físico.
![Aviso][yellow] | Pasta de cache de espaço livre < 30% nos últimos 15 minutos. | 1. Não adicione novas máquinas e considere criar um [servidor de processos adicionais](vmware-azure-set-up-process-server-scale.md).<br/>2. Verifique se o número de VMs com o servidor de processos ajustado a [diretrizes](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Siga as instruções abaixo para [resolver problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  Espaço livre < 25% para os últimos 15 minutos | 1. Siga as instruções associadas com o aviso para este problema.<br/> 2. 3. Siga as instruções abaixo para [resolver problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 3. Se o problema persistir, execute o [Planeador de implementações](https://aka.ms/asr-v2a-deployment-planner) para a replicação de servidor do VMware/físico.
![Crítico][red] | Nenhum heartbeat do servidor de processos para 15 minutos ou mais. O serviço de tmansvs não está a comunicar com o servidor de configuração. | 1) verifique se o servidor de processos está em execução.<br/> 2. Verifique se o tmassvc está em execução no servidor de processos.<br/> 3. Siga as instruções abaixo para [resolver problemas de conectividade e replicação](#check-connectivity-and-replication).


![chave da tabela](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Passo 2: Verificar serviços do servidor de processos

Serviços que devem estar em execução no servidor de processos estão resumidos na tabela seguinte. Existem pequenas diferenças em serviços, dependendo de como o servidor de processos é implementado. 

Para todos os serviços, exceto o agente de serviços de recuperação do Microsoft Azure (obengine), verifique se o StartType está definido como **automática** ou **automático (início atrasado)** .
 
**Implementação** | **Serviços em execução**
--- | ---
**Servidor de processos no servidor de configuração** | ProcessServer; ProcessServerMonitor; cxprocessserver; Serviço InMage PushInstall; Serviço de carregamento de registo (LogUpload); Serviço de aplicações InMage Scout; Agente de serviços de recuperação do Microsoft Azure (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; World Wide Web (W3SVC); do serviço de publicação MySQL; Serviço do Microsoft Azure Site Recovery (dra)
**Servidor de processos em execução como um servidor autónomo** | ProcessServer; ProcessServerMonitor; cxprocessserver; Serviço InMage PushInstall; Serviço de carregamento de registo (LogUpload); Serviço de aplicações InMage Scout; Agente de serviços de recuperação do Microsoft Azure (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc.
**Servidor de processos implementado no Azure para reativação pós-falha** | ProcessServer; ProcessServerMonitor; cxprocessserver; Serviço InMage PushInstall; Serviço de carregamento de registo (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Passo 3: Verifique o heartbeat do servidor de processo

Se não houver nenhum heartbeat do servidor de processos (código de erro 806), faça o seguinte:

1. Certifique-se de que a VM do servidor de processo está em execução.
2. Verifique estes registos de erros.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Verifique a conectividade e replicação

 Falhas de replicação iniciais e contínuas geralmente são causadas por problemas de conectividade entre máquinas de origem e o servidor de processos ou entre o servidor de processos e o Azure. Estes passos estão resumidos na seguinte gráfico, seguida dos procedimentos para ajudar a efetuar os passos.

![Resolver problemas de conectividade e replicação](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Passo 4: Certifique-se a sincronização de hora na máquina de origem

Certifique-se de que a data/hora do sistema para a máquina replicada está em sincronização. [Saiba mais](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Passo 5: Verifique o software de antivírus no computador de origem

Verifique que nenhum software de antivírus na máquina replicada está a bloquear o Site Recovery. Se precisar de excluir a recuperação de Site a partir de programas de software antivírus, reveja [este artigo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Passo 6: Verifique a conectividade entre a máquina de origem


1. Instalar o [cliente Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) na máquina de origem, se for necessário. Não utilize o Ping.
2. Da máquina de origem, enviar pings para o servidor de processos na porta HTTPS Telnet. Por predefinição 9443 é a porta HTTPS para o tráfego de replicação.

    `telnet <process server IP address> <port>`

3. Certifique-se de que se a ligação é efetuada com êxito.


**Conetividade** | **Detalhes** | **ação**
--- | --- | ---
**Com êxito** | Telnet mostra uma tela em branco e o servidor de processos está acessível. | Nenhuma ação necessária.
**Unsuccessful** | Não é possível ligar | Certifique-se de que a porta 9443 de entrada é permitida no servidor de processos. Por exemplo, se tiver uma rede de perímetro ou uma sub-rede filtrada. Verifique a conectividade novamente.
**Parcialmente bem sucedida** | Pode se conectar, mas a máquina de origem relatórios que não é possível alcançar o servidor de processos. | Continue com o seguinte procedimento de resolução de problemas.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Passo 7: Resolver problemas de um servidor de processos inacessível

Se o servidor de processos não estiver acessível a partir do computador de origem, será apresentado o erro 78186. Se não resolvidos, ambos consistentes com a aplicação irá causar esse problema e não a ser gerados como esperado de pontos de recuperação consistentes com falhas.

Solucione problemas a verificar se a máquina de origem pode alcançar o endereço IP do servidor de processos e execute a ferramenta de cxpsclient na máquina de origem, para verificar a ligação de ponto-a-ponto.


### <a name="check-the-ip-connection-on-the-process-server"></a>Verifique a ligação de IP no servidor de processos

Se o telnet é efetuada com êxito, mas a máquina de origem relatórios que não é possível alcançar o servidor de processos, verifique se pode alcançar o endereço IP do servidor de processos.

1. Num navegador da web, tentam entrar no IP endereço https://<PS_IP>:<PS_Data_Port>/.
2. Se esta verificação mostra um erro de certificado HTTPS, que é normal. Se ignorar o erro, deverá ver um 400 - pedido inválido. Isso significa que o servidor não é possível atender à solicitação de navegador e que serve a ligação HTTPS standard.
3. Se esta verificação não funcionar, em seguida, tenha em atenção a mensagem de erro do browser. Por exemplo, um erro de 407 indica um problema com a autenticação de proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Verifique a ligação com cxpsclient

Além disso, pode executar a ferramenta de cxpsclient para verificar a ligação de ponto-a-ponto.

1. Execute a ferramenta do seguinte modo:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. No servidor de processos, verifique os registos gerados nessas pastas:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Verificar o carregamento de registos VM de origem para falhas (erro 78028)

Problema com impedidos de máquinas de origem para o serviço de processos de carregamentos de dados pode resultar em ambos os pontos de recuperação consistentes com falhas e consistente com a aplicação não a ser gerados. 

1. Para resolver problemas de falhas de carregamento de rede, pode procurar erros neste registo:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. Utilize o resto dos procedimentos neste artigo pode ajudar a resolver problemas de carregamento de dados.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Passo 8: Verifique se o servidor de processos está a enviar dados

Verifique se o servidor de processos está ativamente enviar dados por push para o Azure.

  1. No servidor de processos, abra o Gestor de tarefas (prima Ctrl + Shift + Esc).
  2. Selecione o **desempenho** separador > **abra o Monitor de recursos**.
  3. Na **Monitor de recursos** página, selecione a **rede** separador. Sob **processos com a atividade de rede**, verifique se o cbengine.exe está ativamente a enviar um vNotolume grandes de dados.

       ![Volumes em processos com a atividade de rede](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se cbengine.exe não está a enviar um grande volume de dados, conclua os passos nas secções seguintes.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Passo 9: Verifique a ligação de servidor de processo para o armazenamento de Blobs do Azure

1. No Monitor de recursos, selecione **cbengine.exe**.
2. Sob **conexões TCP**, verifique se existe conectividade entre o servidor de processos e o armazenamento do Azure.

  ![Conectividade entre cbengine.exe e o URL de armazenamento de Blobs do Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Verificação de serviços

Se não houver nenhuma conectividade do servidor de processos para o URL de armazenamento de Blobs do Azure, verifique que serviços estão em execução.

1. No painel de controlo, selecione **serviços**.
2. Certifique-se de que os seguintes serviços estão em execução:

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Agente dos Serviços de Recuperação do Microsoft Azure
    - Serviço Microsoft Azure Site Recovery
    - tmansvc

3. Iniciar ou reiniciar qualquer serviço que não está em execução.
4. Certifique-se de que o servidor de processos está ligado e acessível. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Passo 10: verificar a ligação do servidor de processo para o endereço IP público do Azure

1. No servidor de processos, no **%programfiles%\Microsoft Agent\Temp de serviços de recuperação do Azure**, abra o ficheiro de CBEngineCurr.errlog mais recente.
2. No ficheiro, procure **443**, ou para a cadeia de caracteres **tentativa de ligação falhada**.

  ![Na pasta Temp nos registos de erros](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Se é possível ver problemas, localizado seu endereço IP público do Azure no ficheiro CBEngineCurr.currLog utilizando a porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Na linha de comandos no servidor de processos, utilize o Telnet para enviar um ping de seu endereço IP público do Azure.
6. Se não conseguir ligar, siga o procedimento seguinte.

## <a name="step-11-check-process-server-firewall-settings"></a>Passo 11: Verifique as definições de firewall do servidor de processo. 

Verifique se o firewall baseadas no endereço IP no servidor de processos está a bloquear o acesso.

1. Para regras de firewall baseadas no endereço IP:

    a) Transferir uma lista completa das [intervalos IP de datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) adicione os intervalos de endereços IP à sua configuração de firewall, para garantir que a firewall permite a comunicação para o Azure (e a porta HTTPS predefinida, 443).

    c) permitir intervalos de endereços IP para a região do Azure da sua subscrição e para a região do Azure-oeste (utilizada para gestão de identidades e de controlo de acesso).

2. Para obter firewalls de baseado em URL, adicione os URLs indicados na tabela a seguir para a configuração de firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Passo 12: Verifique se as definições de proxy de servidor de processo 

1. Se utilizar um servidor proxy, certifique-se de que o nome do servidor proxy é resolvido pelo servidor DNS. Verifique o valor que forneceu quando configurou o servidor de configuração na chave do Registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Certifique-se de que as mesmas definições são utilizadas pelo agente de recuperação de sites do Azure para enviar dados.

    a) procure **do Microsoft Azure Backup**.

    b) abra **Microsoft Azure Backup**e selecione **ação** > **alterar propriedades**.

    c) na **configuração do Proxy** guia, o endereço de proxy deve ser a mesmo que o endereço de proxy que é mostrado nas definições de registo. Caso contrário, alterá-lo para o mesmo endereço.

## <a name="step-13-check-bandwidth"></a>Passo 13: Verificação de largura de banda

Aumentar a largura de banda entre o servidor de processos e o Azure e, em seguida, verifique se o problema ainda ocorre.


## <a name="next-steps"></a>Passos Seguintes

Se precisar de mais ajuda, publique sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png