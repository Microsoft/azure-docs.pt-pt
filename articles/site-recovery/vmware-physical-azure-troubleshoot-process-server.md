---
title: Problemas de resolução do servidor de processo de recuperação do site azure
description: Este artigo descreve como resolver problemas com o servidor do processo de recuperação do site Azure
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 7657d614645bb00235db2701773bc15fa260b70d
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835806"
---
# <a name="troubleshoot-the-process-server"></a>Resolução de problemas no servidor de processos

O servidor do processo de [recuperação](site-recovery-overview.md) do site é utilizado quando configura a recuperação de desastres para o Azure para VMs e servidores físicos no local. Este artigo descreve como resolver problemas com o servidor de processos, incluindo problemas de replicação e conectividade.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre o servidor de processos.

## <a name="before-you-start"></a>Antes de começar

Antes de começar a resolver problemas:

1. Certifique-se de que compreende como monitorizar os servidores de [processos](vmware-physical-azure-monitor-process-server.md).
2. Reveja as melhores práticas abaixo.
3. Certifique-se de que segue [as considerações](site-recovery-plan-capacity-vmware.md#capacity-considerations)de capacidade e utilize orientações de dimensionamento para o servidor de [configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) ou [servidores de processo autónomos](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>As melhores práticas para a implementação do servidor de processos

Para um ótimo desempenho dos servidores de processos, resumimos uma série de boas práticas gerais.

**Boas práticas** | **Detalhes**
--- |---
**Utilização** | Certifique-se de que o servidor de configuração/servidor de processo autónomo só é utilizado para o fim a que se destina. Não faça mais nada na máquina.
**Endereço IP** | Certifique-se de que o servidor de processo tem um endereço IPv4 estático e não tem NAT configurado.
**Controlar a utilização da memória/CPU** |Mantenha o CPU e o uso da memória abaixo dos 70%.
**Garantir espaço gratuito** | O espaço livre refere-se ao espaço do disco cache no servidor de processos. Os dados de replicação são armazenados na cache antes de ser enviado para o Azure.<br/><br/> Mantenha o espaço livre acima dos 25%. Se for abaixo dos 20%, a replicação é acelerada para máquinas replicadas que estão associadas ao servidor de processos.

## <a name="check-process-server-health"></a>Verifique a saúde do servidor de processos

O primeiro passo na resolução de problemas é verificar a saúde e o estado do servidor de processos. Para isso, reveja todos os alertas, verifique se os serviços necessários estão em execução e verifique se há um batimento cardíaco do servidor de processos. Estes passos são resumidos no gráfico seguinte, seguidos de procedimentos para ajudá-lo a executar os passos.

![Saúde do servidor de processo de resolução de problemas](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Passo 1: Alertas de saúde do servidor do processo de resolução de problemas

O servidor de processogera uma série de alertas de saúde. Estes alertas, e as ações recomendadas, são resumidos na tabela seguinte.

**Tipo de alerta** | **Erro** | **Resolução de problemas**
--- | --- | --- 
![Bom estado de funcionamento][green] | Nenhum  | O servidor de processos está conectado e saudável.
![Aviso][yellow] | Os serviços especificados não estão a funcionar. | 1. Verifique se os serviços estão em funcionamento.<br/> 2. Se os serviços estiverem a funcionar como esperado, siga as instruções abaixo para resolver problemas de [conectividade e replicação.](#check-connectivity-and-replication)
![Aviso][yellow]  | A utilização do CPU > 80% nos últimos 15 minutos. | 1. Não adicione máquinas novas.<br/>2. Verifique se o número de VMs que usam o servidor de processo saca-se aos [limites definidos,](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a criação de um servidor de [processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para resolver problemas de [conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  A utilização do CPU > 95% nos últimos 15 minutos. | 1. Não adicione máquinas novas.<br/>2. Verifique se o número de VMs que usam o servidor de processo saca-se aos [limites definidos,](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a criação de um servidor de [processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para resolver problemas de [conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [Planificador](https://aka.ms/asr-v2a-deployment-planner) de Implementação para a replicação vMware/servidor físico.
![Aviso][yellow] | O uso da memória > 80% nos últimos 15 minutos. |  1. Não adicione máquinas novas.<br/>2. Verifique se o número de VMs que usam o servidor de processo saca-se aos [limites definidos,](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a criação de um servidor de [processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga todas as instruções associadas ao aviso.<br/> 4. Se o problema persistir, siga as instruções abaixo para resolver problemas de [conectividade e replicação.](#check-connectivity-and-replication)
![Crítico][red] | O uso da memória > 95% nos últimos 15 minutos. | 1. Não adicione novas máquinas e considere a criação de um servidor de [processo adicional](vmware-azure-set-up-process-server-scale.md).<br/> 2. Siga todas as instruções associadas ao aviso.<br/> 3. 4. Se o problema continuar, siga as instruções abaixo para resolver problemas de [conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [Planificador](https://aka.ms/asr-v2a-deployment-planner) de Implementação para problemas de replicação vMware/servidor físico.
![Aviso][yellow] | Espaço livre de pasta cache < 30% nos últimos 15 minutos. | 1. Não adicione novas máquinas e considere a criação de um servidor de [processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>2. Verifique se o número de VMs que utilizam o servidor de processo sais às [diretrizes](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Siga as instruções abaixo para resolver problemas de [conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  Espaço livre < 25% nos últimos 15 minutos | 1. Siga as instruções associadas ao aviso para este problema.<br/> 2. 3. Siga as instruções abaixo para resolver problemas de [conectividade e problemas](#check-connectivity-and-replication)de replicação .<br/> 3. Se o problema persistir, execute o [Planificador](https://aka.ms/asr-v2a-deployment-planner) de Implementação para a replicação vMware/servidor físico.
![Crítico][red] | Nenhum batimento cardíaco do servidor de processos por 15 minutos ou mais. O serviço tmansvs não está a comunicar com o servidor de configuração. | 1) Verifique se o servidor de processo está a funcionar.<br/> 2. Verifique se o tmassvc está a funcionar no servidor de processos.<br/> 3. Siga as instruções abaixo para resolver problemas de [conectividade e replicação](#check-connectivity-and-replication).


![Chave de mesa](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Passo 2: Verificar os serviços de servidor de processos

Os serviços que devem estar a funcionar no servidor de processos são resumidos na tabela seguinte. Existem ligeiras diferenças nos serviços, dependendo da forma como o servidor de processo é implementado. 

Para todos os serviços, com exceção do Agente de Serviços de Recuperação do Microsoft Azure (obengine), verifique se o StartType está definido para **Automático** ou **Automático (Arranque Atrasado)**.
 
**Implementação** | **Serviços de funcionamento**
--- | ---
**Servidor de processos no servidor de configuração** | ProcessServer; Monitor de servidor esquelésdo; cxprocessserver; InMage PushInstall; Serviço de upload de registo (LogUpload); Serviço de Aplicação de Escuteiros InMage; Agente de Serviços de Recuperação do Microsoft Azure (motor de motor); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; World Wide Web Publishing Service (W3SVC); Mysql; Serviço de Recuperação de Sites do Microsoft Azure (dra)
**Processserver funcionando como um servidor autónomo** | ProcessServer; Monitor de servidor esquelésdo; cxprocessserver; InMage PushInstall; Serviço de upload de registo (LogUpload); Serviço de Aplicação de Escuteiros InMage; Agente de Serviços de Recuperação do Microsoft Azure (motor de motor); InMage Scout VX Agent-Sentinel/Outpost (svagents); Tmansvc.
**Servidor de processo implantado em Azure para failback** | ProcessServer; Monitor de servidor esquelésdo; cxprocessserver; InMage PushInstall; Serviço de upload de registo (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Passo 3: Verifique o batimento cardíaco do servidor de processos

Se não houver batimentocardíaco do servidor de processo (código de erro 806), faça o seguinte:

1. Verifique se o VM do servidor de processoestá a funcionar.
2. Verifique se estes registos estão em erro.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Verifique a conectividade e a replicação

 Falhas de replicação iniciais e em curso são muitas vezes causadas por problemas de conectividade entre as máquinas de origem e o servidor de processo, ou entre o servidor de processo e o Azure. Estes passos são resumidos no gráfico seguinte, seguidos de procedimentos para ajudá-lo a executar os passos.

![Conectividade e replicação de resolução de problemas](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Passo 4: Verificar a sincronização do tempo na máquina de origem

Certifique-se de que a data/hora do sistema para a máquina replicada está sincronizada. [Saiba mais](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Passo 5: Verifique o software antivírus na máquina de origem

Verifique se nenhum software antivírus na máquina replicada está a bloquear a recuperação do site. Se precisar excluir a Recuperação do Site de programas antivírus, reveja [este artigo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Passo 6: Verificar a conectividade da máquina de origem


1. Instale o [cliente Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) na máquina de origem, se necessário. Não use Ping.
2. A partir da máquina de origem, pingo o servidor de processo na porta HTTPS com Telnet. Por defeito 9443 é a porta HTTPS para o tráfego de replicação.

    `telnet <process server IP address> <port>`

3. Verifique se a ligação foi bem sucedida.


**Conetividade** | **Detalhes** | **Ação**
--- | --- | ---
**Sucesso** | A Telnet mostra um ecrã em branco e o servidor de processos é acessível. | Não são necessárias mais ações.
**Sem sucesso** | Não pode ligar-se. | Certifique-se de que a porta de entrada 9443 é permitida no servidor de processos. Por exemplo, se tiver uma rede de perímetro ou uma sub-rede por ecrã. Verifique a conectividade de novo.
**Parcialmente bem sucedido** | Pode ligar-se, mas a máquina de origem informa que o servidor de processo não pode ser contactado. | Continue com o próximo procedimento de resolução de problemas.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Passo 7: Resolução de problemas num servidor de processo inalcançável

Se o servidor de processo não estiver acessível a partir da máquina de origem, o erro 78186 será apresentado. Se não for abordado, este problema levará a que pontos de recuperação consistentes com aplicações e pontos de recuperação consistentes não sejam gerados como esperado.

Problemas, verificando se a máquina de origem pode alcançar o endereço IP do servidor de processo, e executar a ferramenta cxpsclient na máquina de origem, para verificar a ligação de ponta a ponta.


### <a name="check-the-ip-connection-on-the-process-server"></a>Verifique a ligação IP no servidor de processos

Se a telnet tiver sucesso, mas a máquina de origem informa que o servidor de processo não pode ser contactado, verifique se pode chegar ao endereço IP do servidor de processo.

1. Num navegador web, tente chegar ao endereço IP https://<PS_IP>:<PS_Data_Port>/.
2. Se esta verificação mostrar um erro de certificado HTTPS, é normal. Se ignorar o erro, deve ver um 400 - Pedido Mau. Isto significa que o servidor não pode atender o pedido de navegador, e que a ligação HTTPS padrão está bem.
3. Se esta verificação não funcionar, note a mensagem de erro do navegador. Por exemplo, um erro 407 indicará um problema com a autenticação por procuração.

### <a name="check-the-connection-with-cxpsclient"></a>Verifique a ligação com o cxpsclient

Além disso, pode executar a ferramenta cxpsclient para verificar a ligação de ponta a ponta.

1. Executar a ferramenta da seguinte forma:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. No servidor de processos, verifique os registos gerados nestas pastas:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Verifique os registos vm de origem para obter falhas de upload (erro 78028)

Problema com uploads de dados bloqueados de máquinas de origem para o serviço de processo pode resultar em pontos de recuperação consistentes com falha e aplicação não sendo gerados. 

1. Para resolver falhas de carregamento de rede, pode procurar erros neste registo:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. A utilização do resto dos procedimentos neste artigo pode ajudá-lo a resolver problemas de upload de dados.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Passo 8: Verifique se o servidor de processo está a empurrar dados

Verifique se o servidor de processo está a empurrar ativamente os dados para o Azure.

  1. No servidor de processos, abra o Task Manager (prima Ctrl+Shift+Esc).
  2. Selecione o separador **desempenho** > **Monitor de Recursos Abertos**.
  3. Na página **De Controlo de Recursos,** selecione o separador **Rede.** No âmbito **dos Processos com Atividade**de Rede, verifique se o cbengine.exe está a enviar ativamente um grande volume de dados.

       ![Volumes em processos com atividade de rede](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se o cbengine.exe não estiver a enviar um grande volume de dados, complete os passos nas seguintes secções.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Passo 9: Verifique a ligação do servidor de processo supérrio ao armazenamento de blob Azure

1. No Monitor de Recursos, selecione **cbengine.exe**.
2. No âmbito das **Ligações TCP,** verifique se existe conectividade desde o servidor de processos até ao armazenamento Azure.

  ![Conectividade entre cbengine.exe e o URL de armazenamento Bluee Blob](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Verifique os serviços

Se não houver conectividade do servidor de processo supérfice ao URL de armazenamento de blob Azure, verifique se os serviços estão em execução.

1. No Painel de Controlo, selecione **Serviços**.
2. Verifique se os seguintes serviços estão em funcionamento:

    - cxprocessserver
    - Agente VX de Escuteiro InMage – Sentinel/Posto Avançado
    - Agente de Serviços de Recuperação do Microsoft Azure
    - Serviço de Recuperação de Sites do Microsoft Azure
    - tmansvc

3. Inicie ou reinicie qualquer serviço que não esteja a funcionar.
4. Verifique se o servidor de processo está ligado e acessível. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Passo 10: verifique a ligação do servidor de processo seleções ao endereço IP público do Azure

1. No servidor de processos, em **%programfiles%\Microsoft Azure Recovery Services Agent\Temp,** abra o mais recente ficheiro CBEngineCurr.errlog.
2. No ficheiro, procure **443**, ou falhou a tentativa de **ligação**de cordas .

  ![Registos de erros na pasta Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Se vir problemas, localize o seu endereço IP público Azure no ficheiro CBEngineCurr.currLog utilizando a porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Na linha de comando do servidor de processo, utilize a Telnet para dar a conhecer o seu endereço IP público Do Azure.
6. Se não conseguir ligar, siga o próximo procedimento.

## <a name="step-11-check-process-server-firewall-settings"></a>Passo 11: Verifique as definições de firewall do servidor de processos. 

Verifique se a firewall baseada no endereço IP no servidor de processo está a bloquear o acesso.

1. Para regras de firewall baseadas em endereçoip:

    a) Descarregue a lista completa das gamas IP do Centro de [Dados microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Adicione as gamas de endereços IP à sua configuração de firewall, para garantir que a firewall permite a comunicação ao Azure (e à porta HTTPS predefinida, 443).

    c) Permitir intervalos de endereços IP para a região azure da sua subscrição, e para a região azure west US (utilizada para controlo de acesso e gestão de identidade).

2. Para firewalls baseados em URL, adicione os URLs listados na tabela seguinte à configuração da firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Passo 12: Verificar as definições de proxy do servidor de processos 

1. Se utilizar um servidor proxy, certifique-se de que o nome do servidor proxy é resolvido pelo servidor DNS. Verifique o valor que forneceu quando configurar o servidor de configuração na chave de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Certifique-se de que as mesmas definições são utilizadas pelo agente de recuperação do site Azure para enviar dados.

    a) Pesquisa por **Microsoft Azure Backup**.

    b) Abra **a Cópia de Segurança Microsoft Azure**e selecione **Action**  >  **Change Properties**.

    c) No **separador Proxy Configuração,** o endereço proxy deve ser o mesmo que o endereço proxy que está mostrado nas definições de registo. Caso contrário, mude-o para o mesmo endereço.

## <a name="step-13-check-bandwidth"></a>Passo 13: Verifique a largura de banda

Aumente a largura de banda entre o servidor de processo e o Azure e verifique se o problema ainda ocorre.


## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda, publique a sua pergunta no [Microsoft Q&Uma página de perguntas para a Recuperação do Site Azure](https://docs.microsoft.com/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
