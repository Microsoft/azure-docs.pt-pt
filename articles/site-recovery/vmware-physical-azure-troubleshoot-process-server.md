---
title: Solucionar problemas do servidor de processo de Azure Site Recovery
description: Este artigo descreve como solucionar problemas com o servidor de processo de Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813420"
---
# <a name="troubleshoot-the-process-server"></a>Solucionar problemas do servidor de processo

O servidor de processo de [site Recovery](site-recovery-overview.md) é usado quando você configura a recuperação de desastres para o Azure para VMs VMware locais e servidores físicos. Este artigo descreve como solucionar problemas com o servidor de processo, incluindo problemas de replicação e conectividade.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre o servidor de processo.

## <a name="before-you-start"></a>Antes de começar

Antes de iniciar a solução de problemas:

1. Certifique-se de entender como [monitorar servidores de processo](vmware-physical-azure-monitor-process-server.md).
2. Examine as práticas recomendadas abaixo.
3. Certifique-se de seguir as [considerações de capacidade](site-recovery-plan-capacity-vmware.md#capacity-considerations)e use a orientação de dimensionamento para o [servidor de configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) ou para os [servidores de processo autônomos](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Práticas recomendadas para a implantação do servidor de processo

Para obter um desempenho ideal dos servidores de processo, resumimos várias práticas recomendadas gerais.

**Prática recomendada** | **Detalhes**
--- |---
**Utilização** | Verifique se o servidor de configuração/servidor de processo autônomo só é usado para a finalidade pretendida. Não execute nada mais no computador.
**Endereço IP** | Verifique se o servidor de processo tem um endereço IPv4 estático e se o NAT não está configurado.
**Controlar a memória/uso da CPU** |Mantenha o uso de CPU e memória em 70%.
**Garantir espaço livre** | Espaço livre refere-se ao espaço em disco do cache no servidor de processo. Os dados de replicação são armazenados no cache antes de serem carregados no Azure.<br/><br/> Mantenha o espaço livre acima de 25%. Se ficar abaixo de 20%, a replicação será limitada para os computadores replicados associados ao servidor de processo.

## <a name="check-process-server-health"></a>Verificar a integridade do servidor de processo

A primeira etapa da solução de problemas é verificar a integridade e o status do servidor de processo. Para fazer isso, examine todos os alertas, verifique se os serviços necessários estão em execução e verifique se há uma pulsação do servidor de processo. Essas etapas são resumidas no gráfico a seguir, seguido por procedimentos para ajudá-lo a executar as etapas.

![Solucionar problemas de integridade do servidor de processo](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Passo 1: Solucionar problemas de alertas de integridade do servidor de processo

O servidor de processo gera vários alertas de integridade. Esses alertas e as ações recomendadas são resumidos na tabela a seguir.

**Tipo de alerta** | **Ao** | **Resolução de problemas**
--- | --- | --- 
![Bom estado de funcionamento][green] | Nenhum  | O servidor de processo está conectado e íntegro.
![Aviso][yellow] | Os serviços especificados não estão em execução. | 1. Verifique se os serviços estão em execução.<br/> 2. Se os serviços estiverem sendo executados conforme o esperado, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Aviso][yellow]  | A utilização da CPU > 80% nos últimos 15 minutos. | 1. Não adicione novos computadores.<br/>2. Verifique se o número de VMs que usam o servidor de processo está alinhado aos [limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  A utilização da CPU > 95% nos últimos 15 minutos. | 1. Não adicione novos computadores.<br/>2. Verifique se o número de VMs que usam o servidor de processo está alinhado aos [limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [planejador de implantações](https://aka.ms/asr-v2a-deployment-planner) para replicação de servidor VMware/físico.
![Aviso][yellow] | Uso de memória > 80% nos últimos 15 minutos. |  1. Não adicione novos computadores.<br/>2. Verifique se o número de VMs que usam o servidor de processo está alinhado aos [limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções associadas ao aviso.<br/> 4. Se o problema persistir, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] | Uso de memória > 95% nos últimos 15 minutos. | 1. Não adicione novos computadores e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/> 2. Siga as instruções associadas ao aviso.<br/> 3. 4. Se o problema continuar, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [planejador de implantações](https://aka.ms/asr-v2a-deployment-planner) para problemas de replicação de servidor VMware/físico.
![Aviso][yellow] | Espaço livre na pasta de cache < 30% nos últimos 15 minutos. | 1. Não adicione novos computadores e considere configurar um servidor de [processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>2. Verifique se o número de VMs que usam o servidor de processo está alinhado às [diretrizes](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  Espaço livre < 25% nos últimos 15 minutos | 1. Siga as instruções associadas ao aviso para esse problema.<br/> 2. 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 3. Se o problema persistir, execute o [planejador de implantações](https://aka.ms/asr-v2a-deployment-planner) para replicação de servidor VMware/físico.
![Crítico][red] | Não há pulsação do servidor de processo por 15 minutos ou mais. O serviço tmansvs não está se comunicando com o servidor de configuração. | 1) Verifique se o servidor de processo está em execução.<br/> 2. Verifique se o tmassvc está em execução no servidor de processo.<br/> 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).


![Chave de tabela](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Passo 2: Verificar serviços do servidor de processo

Os serviços que devem estar em execução no servidor de processo são resumidos na tabela a seguir. Há pequenas diferenças nos serviços, dependendo de como o servidor de processo é implantado. 

Para todos os serviços, exceto o obengine (agente de Serviços de Recuperação do Microsoft Azure), verifique se o StartType está definido como **automático** ou **automático (início atrasado)** .
 
**Implementação** | **Serviços em execução**
--- | ---
**Servidor de processo no servidor de configuração** | ProcessServer ProcessServerMonitor; cxprocessserver InMage PushInstall; Serviço de carregamento de log (LogUpload); Serviço de aplicativo InMage Scout; Agente de Serviços de Recuperação do Microsoft Azure (obengine); Agente do InMage Scout VX-sentinela/posto avançado (svagents); tmansvc W3SVC (serviço de publicação de World Wide Web); MySQL Serviço de Site Recovery de Microsoft Azure (Dra)
**Servidor de processo em execução como um servidor autônomo** | ProcessServer ProcessServerMonitor; cxprocessserver InMage PushInstall; Serviço de carregamento de log (LogUpload); Serviço de aplicativo InMage Scout; Agente de Serviços de Recuperação do Microsoft Azure (obengine); Agente do InMage Scout VX-sentinela/posto avançado (svagents); tmansvc.
**Servidor de processo implantado no Azure para failback** | ProcessServer ProcessServerMonitor; cxprocessserver InMage PushInstall; Serviço de carregamento de log (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Passo 3: Verificar a pulsação do servidor de processo

Se não houver pulsação do servidor de processo (código de erro 806), faça o seguinte:

1. Verifique se a VM do servidor de processo está em execução.
2. Verifique esses logs em busca de erros.

    C:\ProgramData\ASR\home\svsystems\eventmanager *. log C\ProgramData\ASR\home\svsystems\monitor_protection*. log

## <a name="check-connectivity-and-replication"></a>Verificar a conectividade e a replicação

 As falhas de replicação inicial e contínua geralmente são causadas por problemas de conectividade entre computadores de origem e o servidor de processo, ou entre o servidor de processo e o Azure. Essas etapas são resumidas no gráfico a seguir, seguido por procedimentos para ajudá-lo a executar as etapas.

![Solucionar problemas de conectividade e replicação](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Passo 4: Verificar a sincronização de horário no computador de origem

Verifique se a data/hora do sistema do computador replicado está em sincronia. [Saiba mais](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Passo 5: Verificar o software antivírus no computador de origem

Verifique se nenhum software antivírus no computador replicado está bloqueando Site Recovery. Se você precisar excluir Site Recovery de programas antivírus, leia [Este artigo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Passo 6: Verificar a conectividade do computador de origem


1. Instale o [cliente Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) no computador de origem, se necessário. Não use Ping.
2. No computador de origem, execute ping no servidor de processo na porta HTTPS com Telnet. Por padrão, 9443 é a porta HTTPS para o tráfego de replicação.

    `telnet <process server IP address> <port>`

3. Verifique se a conexão foi bem-sucedida.


**Conetividade** | **Detalhes** | **ação**
--- | --- | ---
**Bem-sucedida** | O Telnet mostra uma tela em branco e o servidor de processo pode ser acessado. | Nenhuma ação adicional é necessária.
**Bem-sucedida** | Você não pode se conectar | Verifique se a porta de entrada 9443 é permitida no servidor de processo. Por exemplo, se você tiver uma rede de perímetro ou uma sub-rede filtrada. Verifique a conectividade novamente.
**Parcialmente bem-sucedido** | Você pode se conectar, mas o computador de origem relata que o servidor de processo não pode ser acessado. | Continue com o próximo procedimento de solução de problemas.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Passo 7: Solucionar problemas de um servidor de processo inacessível

Se o servidor de processo não estiver acessível a partir do computador de origem, o erro 78186 será exibido. Se não for resolvido, esse problema fará com que os pontos de recuperação consistentes com o aplicativo e com falhas não sejam gerados conforme o esperado.

Solucionar problemas verificando se o computador de origem pode alcançar o endereço IP do servidor de processo e executar a ferramenta cxpsclient no computador de origem, para verificar a conexão de ponta a ponta.


### <a name="check-the-ip-connection-on-the-process-server"></a>Verificar a conexão IP no servidor de processo

Se o Telnet for bem-sucedido, mas o computador de origem relatar que o servidor de processo não pode ser acessado, verifique se você pode acessar o endereço IP do servidor de processo.

1. Em um navegador da Web, tente acessar o endereço IP HTTPS://< PS_IP >: < PS_Data_Port >/.
2. Se essa verificação mostrar um erro de certificado HTTPS, isso é normal. Se você ignorar o erro, verá uma solicitação inadequada 400. Isso significa que o servidor não pode atender à solicitação do navegador e que a conexão HTTPS padrão está correta.
3. Se essa verificação não funcionar, anote a mensagem de erro do navegador. Por exemplo, um erro 407 indicará um problema com a autenticação de proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Verifique a conexão com cxpsclient

Além disso, você pode executar a ferramenta cxpsclient para verificar a conexão de ponta a ponta.

1. Execute a ferramenta da seguinte maneira:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. No servidor de processo, verifique os logs gerados nessas pastas:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Verificar os logs de VM de origem para falhas de carregamento (erro 78028)

O problema com carregamentos de dados bloqueados de computadores de origem para o serviço de processo pode resultar em pontos de recuperação consistentes com falhas e consistentes com o aplicativo não sendo gerados. 

1. Para solucionar problemas de falhas de carregamento de rede, você pode procurar erros neste log:

    C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\svagents *. log 

2. Usar o restante dos procedimentos neste artigo pode ajudá-lo a resolver problemas de carregamento de dados.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Passo 8: Verificar se o servidor de processo está enviando dados por push

Verifique se o servidor de processo está enviando dados ativamente para o Azure.

  1. No servidor de processo, abra o Gerenciador de tarefas (pressione CTRL + SHIFT + ESC).
  2. Selecione a guia **desempenho** > **abrir monitor de recursos**.
  3. Na página **Monitor de recursos** , selecione a guia **rede** . Em **processos com a atividade de rede**, verifique se cbengine. exe está enviando ativamente um grande volume de dados.

       ![Volumes sob processos com atividade de rede](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se o cbengine. exe não estiver enviando um grande volume de dados, conclua as etapas nas seções a seguir.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Passo 9: Verificar a conexão do servidor de processo com o armazenamento de BLOBs do Azure

1. Em Monitor de Recursos, selecione **cbengine. exe**.
2. Em **conexões TCP**, verifique se há conectividade do servidor de processo com o armazenamento do Azure.

  ![Conectividade entre o cbengine. exe e a URL do armazenamento de BLOBs do Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Verificar serviços

Se não houver conectividade do servidor de processo com a URL de armazenamento de BLOBs do Azure, verifique se os serviços estão em execução.

1. No painel de controle, selecione **Serviços**.
2. Verifique se os seguintes serviços estão em execução:

    - cxprocessserver
    - Agente do InMage Scout VX – sentinela/posto avançado
    - Agente dos Serviços de Recuperação do Microsoft Azure
    - Serviço Microsoft Azure Site Recovery
    - tmansvc

3. Inicie ou reinicie qualquer serviço que não esteja em execução.
4. Verifique se o servidor de processo está conectado e acessível. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Etapa 10: verificar a conexão do servidor de processo com o endereço IP público do Azure

1. No servidor de processo, em **%ProgramFiles%\Microsoft serviços de recuperação do Azure Agent\Temp**, abra o arquivo CBEngineCurr. errlog mais recente.
2. No arquivo, procure **443**, ou para a **tentativa de conexão**da cadeia de caracteres falhou.

  ![Logs de erros na pasta Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Se você encontrar problemas, encontre o endereço IP público do Azure no arquivo CBEngineCurr. currLog usando a porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Na linha de comando no servidor de processo, use o Telnet para executar ping no seu endereço IP público do Azure.
6. Se você não conseguir se conectar, siga o procedimento a seguir.

## <a name="step-11-check-process-server-firewall-settings"></a>Etapa 11: Verifique as configurações de firewall do servidor de processo. 

Verifique se o firewall baseado em endereço IP no servidor de processo está bloqueando o acesso.

1. Para regras de firewall baseadas em endereço IP:

    a) Baixe a lista completa de [intervalos de IP do datacenter Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) adicione os intervalos de endereços IP à sua configuração de firewall para garantir que o firewall permita a comunicação com o Azure (e para a porta HTTPS padrão, 443).

    c) permitir intervalos de endereços IP para a região do Azure da sua assinatura e para a região oeste dos EUA do Azure (usada para controle de acesso e gerenciamento de identidade).

2. Para firewalls baseados em URL, adicione as URLs listadas na tabela a seguir à configuração do firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Etapa 12: Verificar as configurações de proxy do servidor de processo 

1. Se você usar um servidor proxy, certifique-se de que o nome do servidor proxy seja resolvido pelo servidor DNS. Verifique o valor que você forneceu ao configurar o servidor de configuração na chave do registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure site Recovery\ProxySettings**.
2. Verifique se as mesmas configurações são usadas pelo agente de Azure Site Recovery para enviar dados.

    a) procure **backup do Microsoft Azure**.

    b) abra **backup do Microsoft Azure**e selecione **ação** > **alterar propriedades**.

    c) na guia **configuração de proxy** , o endereço de proxy deve ser o mesmo que o endereço de proxy mostrado nas configurações do registro. Caso contrário, altere-o para o mesmo endereço.

## <a name="step-13-check-bandwidth"></a>Etapa 13: Verificar largura de banda

Aumente a largura de banda entre o servidor de processo e o Azure e verifique se o problema ainda ocorre.


## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda, poste sua pergunta no [Fórum de Azure site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
