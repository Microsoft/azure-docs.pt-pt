---
title: Resolução de problemas no servidor de processo de recuperação do site Azure
description: Este artigo descreve como resolver problemas com o servidor de processo de recuperação do site Azure
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 4816b597d66aea3bbe7f834004f924b5108de939
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87499760"
---
# <a name="troubleshoot-the-process-server"></a>Resolução de problemas do servidor de processo

O servidor de processo [de recuperação do site](site-recovery-overview.md) é utilizado quando configura a recuperação de desastres para o Azure para vMware vMs no local e servidores físicos. Este artigo descreve como resolver problemas com o servidor de processos, incluindo problemas de replicação e conectividade.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre o servidor de processos.

## <a name="before-you-start"></a>Antes de começar

Antes de começar a resolução de problemas:

1. Certifique-se de que compreende como [monitorizar os servidores de processos.](vmware-physical-azure-monitor-process-server.md)
2. Reveja as melhores práticas abaixo.
3. Certifique-se de que segue [considerações de capacidade](site-recovery-plan-capacity-vmware.md#capacity-considerations)e utiliza orientação de dimensionamento para o servidor de [configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) ou [servidores de processo autónomos](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Melhores práticas para implementação de servidor de processo

Para um melhor desempenho dos servidores de processo, resumimos uma série de boas práticas gerais.

**Melhor prática** | **Detalhes**
--- |---
**Utilização** | Certifique-se de que o servidor de configuração/servidor de processo autónomo só é utilizado para o fim a que se destina. Não coloque mais nada na máquina.
**Endereço IP** | Certifique-se de que o servidor de processo tem um endereço IPv4 estático e não tem NAT configurado.
**Controlar a utilização da memória/CPU** |Mantenha o USO DA CPU e o uso da memória abaixo dos 70%.
**Garantir espaço livre** | Espaço livre refere-se ao espaço do disco cache no servidor de processo. Os dados de replicação são armazenados na cache antes de serem enviados para o Azure.<br/><br/> Mantenha o espaço livre acima dos 25%. Se for abaixo dos 20%, a replicação é acelerada para máquinas replicadas que estão associadas ao servidor de processo.

## <a name="check-process-server-health"></a>Verifique a saúde do servidor do processo

O primeiro passo na resolução de problemas é verificar a saúde e o estado do servidor de processo. Para isso, reveja todos os alertas, verifique se os serviços necessários estão em execução e verifique se há um batimento cardíaco do servidor de processo. Estes passos são resumidos no gráfico seguinte, seguidos de procedimentos para ajudá-lo a executar os passos.

![Saúde do servidor do processo de resolução de problemas](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Passo 1: Alertas de saúde do servidor do processo de resolução de problemas

O servidor de processo gera uma série de alertas de saúde. Estes alertas, e as ações recomendadas, são resumidos na tabela seguinte.

**Tipo de alerta** | **Erro** | **Resolução de problemas**
--- | --- | --- 
![Bom estado de funcionamento][green] | Nenhum  | O servidor de processo está conectado e saudável.
![Aviso][yellow] | Os serviços especificados não estão a funcionar. | 1. Verifique se os serviços estão a funcionar.<br/> 2. Se os serviços estiverem a funcionar como esperado, siga as instruções abaixo para [resolver problemas de conectividade e problemas de replicação](#check-connectivity-and-replication).
![Aviso][yellow]  | A utilização do CPU > 80% nos últimos 15 minutos. | 1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs que utilizam o servidor de processo alinha-se com [os limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a criação de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [resolver problemas de conectividade e problemas de replicação](#check-connectivity-and-replication).
![Crítico][red] |  A utilização do CPU > 95% nos últimos 15 minutos. | 1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs que utilizam o servidor de processo alinha-se com [os limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a criação de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [resolver problemas de conectividade e problemas de replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, executar o [Planejador de Implementação](https://aka.ms/asr-v2a-deployment-planner) para a replicação do servidor VMware/servidor físico.
![Aviso][yellow] | O uso da memória > 80% nos últimos 15 minutos. |  1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs que utilizam o servidor de processo alinha-se com [os limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a criação de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções associadas ao aviso.<br/> 4. Se o problema persistir, siga as instruções abaixo para [resolver problemas de conectividade e problemas de replicação](#check-connectivity-and-replication).
![Crítico][red] | O uso da memória > 95% nos últimos 15 minutos. | 1. Não adicione novas máquinas e considere a criação de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/> 2. Siga as instruções associadas ao aviso.<br/> 3. 4. Se o problema continuar, siga as instruções abaixo para [resolver problemas de conectividade e problemas de replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [Plano de Implementação](https://aka.ms/asr-v2a-deployment-planner) para problemas de replicação de VMware/servidor físico.
![Aviso][yellow] | Espaço livre de pasta cache < 30% nos últimos 15 minutos. | 1. Não adicione novas máquinas e considere a criação de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>2. Verifique se o número de VMs que utilizam o servidor de processo está alinhado com [as diretrizes](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Siga as instruções abaixo para [resolver problemas de conectividade e problemas de replicação](#check-connectivity-and-replication).
![Crítico][red] |  Espaço gratuito < 25% para os últimos 15 minutos | 1. Siga as instruções associadas à advertência para esta questão.<br/> 2. 3. Siga as instruções abaixo para [resolver problemas de conectividade e problemas de replicação](#check-connectivity-and-replication).<br/> 3. Se o problema persistir, executar o [Planejador de Implementação](https://aka.ms/asr-v2a-deployment-planner) para a replicação do servidor VMware/servidor físico.
![Crítico][red] | Sem batimentos cardíacos do servidor de processo durante 15 minutos ou mais. O serviço tmansvs não está a comunicar com o servidor de configuração. | 1) Verifique se o servidor de processo está a funcionar.<br/> 2. Verifique se o tmassvc está em execução no servidor de processo.<br/> 3. Siga as instruções abaixo para [resolver problemas de conectividade e problemas de replicação](#check-connectivity-and-replication).


![Chave de mesa](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Passo 2: Verificar serviços de servidor de processo

Os serviços que devem estar a ser geridos no servidor de processos são resumidos na tabela seguinte. Existem pequenas diferenças nos serviços, dependendo da forma como o servidor de processo é implementado. 

Para todos os serviços, exceto o Agente de Serviços de Recuperação do Microsoft Azure (obengine), verifique se o StartType está definido para **Automático** ou **Automático (Início Atrasado)**.
 
**Implementação** | **Serviços de execução**
--- | ---
**Processar servidor no servidor de configuração** | ProcessServer; ProcessServerMonitor; cxprocessador; InMage PushInstall; Serviço de upload de registo (LogUpload); Serviço de Aplicação de Escuteiros InMage; Microsoft Azure Recovery Services Agent (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; Serviço mundial de publicação web (W3SVC); MySQl; Microsoft Azure Site Recovery Service (dra)
**Servidor de processo funcionando como um servidor autónomo** | ProcessServer; ProcessServerMonitor; cxprocessador; InMage PushInstall; Serviço de upload de registo (LogUpload); Serviço de Aplicação de Escuteiros InMage; Microsoft Azure Recovery Services Agent (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); Tmansvc.
**Servidor de processo implantado em Azure para falha** | ProcessServer; ProcessServerMonitor; cxprocessador; InMage PushInstall; Serviço de upload de registo (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Passo 3: Verifique o batimento cardíaco do servidor do processo

Se não houver batimentos cardíacos do servidor de processo (código de erro 806), faça o seguinte:

1. Verifique se o VM do servidor de processo está a funcionar.
2. Verifique se estes registos têm erros.

    C:\ProgramData\ASR\home\home\svsystems\eventmanager *.log C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Verifique a conectividade e a replicação

 Falhas de replicação iniciais e em curso são muitas vezes causadas por problemas de conectividade entre máquinas de origem e o servidor de processo, ou entre o servidor de processo e Azure. Estes passos são resumidos no gráfico seguinte, seguidos de procedimentos para ajudá-lo a executar os passos.

![Fluxograma mostrando passos para resolver problemas de conectividade e replicação.](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Passo 4: Verificar sincronização de tempo na máquina de origem

Certifique-se de que a data/hora do sistema para a máquina replicada está sincronizada. [Saiba mais](/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Passo 5: Verifique o software antivírus na máquina de origem

Verifique se nenhum software antivírus na máquina replicada está a bloquear a Recuperação do Local. Se tiver de excluir a Recuperação do Local de programas antivírus, [reveja este artigo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Passo 6: Verifique a conectividade da máquina de origem


1. Instale o [cliente Telnet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771275(v=ws.10)) na máquina de origem, se necessário. Não use ping.
2. A partir da máquina de origem, pingo no servidor de processo na porta HTTPS com telnet. Por predefinição 9443 é a porta HTTPS para tráfego de replicação.

    `telnet <process server IP address> <port>`

3. Verifique se a ligação foi bem sucedida.


**Conetividade** | **Detalhes** | **Ação**
--- | --- | ---
**Bem sucedido** | Telnet mostra um ecrã em branco e o servidor de processo é acessível. | Não são necessárias mais medidas.
**Sem sucesso** | Não se pode ligar | Certifique-se de que a porta de entrada 9443 é permitida no servidor de processo. Por exemplo, se tiver uma rede de perímetro ou uma sub-rede rastreada. Verifique a conectividade novamente.
**Parcialmente bem sucedido** | Pode ligar-se, mas a máquina de origem informa que o servidor de processo não pode ser alcançado. | Continue com o próximo procedimento de resolução de problemas.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Passo 7: Resolução de problemas de um servidor de processo inalcançável

Se o servidor de processo não estiver acessível a partir da máquina de origem, será apresentado o erro 78186. Se não for abordado, este problema levará a que tanto os pontos de recuperação consistentes com aplicações como os pontos de recuperação consistentes com falhas não sejam gerados como esperado.

Resolução de problemas verificando se a máquina de origem pode chegar ao endereço IP do servidor de processo, e executar a ferramenta cxpsclient na máquina de origem, para verificar a ligação de ponta a ponta.


### <a name="check-the-ip-connection-on-the-process-server"></a>Verifique a ligação IP no servidor de processos

Se o telnet for bem sucedido, mas a máquina de origem informa que o servidor de processo não pode ser alcançado, verifique se pode chegar ao endereço IP do servidor de processo.

1. Num navegador web, tente contactar o endereço IP https://<PS_IP>:<PS_Data_Port>/.
2. Se esta verificação mostrar um erro de certificado HTTPS, é normal. Se ignorar o erro, deve ver um 400 - Mau Pedido. Isto significa que o servidor não pode servir o pedido do navegador, e que a ligação HTTPS padrão está bem.
3. Se esta verificação não funcionar, então note a mensagem de erro do navegador. Por exemplo, um erro 407 indicará um problema com a autenticação por procuração.

### <a name="check-the-connection-with-cxpsclient"></a>Verifique a ligação com cxpsclient

Além disso, pode executar a ferramenta cxpsclient para verificar a ligação de ponta a ponta.

1. Executar a ferramenta da seguinte forma:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. No servidor de processos, verifique os registos gerados nestas pastas:

    C:\ProgramData\ASR\home\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Verifique os registos VM de fonte para falhas de upload (erro 78028)

O problema com os uploads de dados bloqueados das máquinas de origem para o serviço de processo pode resultar em pontos de recuperação consistentes e consistentes com falhas e aplicações não serem gerados. 

1. Para resolver falhas no upload da rede, pode procurar erros neste registo:

    C:\Ficheiros de programas (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. Utilize o resto dos procedimentos neste artigo pode ajudá-lo a resolver problemas de upload de dados.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Passo 8: Verifique se o servidor de processo está a empurrar dados

Verifique se o servidor de processo está a empurrar ativamente os dados para o Azure.

  1. No servidor de processos, abra o Gestor de Tarefas (prima Ctrl+Shift+Esc).
  2. Selecione o separador **Desempenho** > **Monitor de Recursos Abertos**.
  3. Na página **Monitor de Recursos,** selecione o separador **'Rede'.** No âmbito **de Processos com Atividade da Rede,** verifique se cbengine.exe está a enviar ativamente um grande volume de dados.

       ![Screenshot mostrando um grande número de volumes em processos com atividade de rede.](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se cbengine.exe não estiver a enviar um grande volume de dados, preencha os passos nas seguintes secções.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Passo 9: Verifique a ligação do servidor de processo ao armazenamento de bolhas Azure

1. No Monitor de Recursos, selecione **cbengine.exe**.
2. No **âmbito das Ligações TCP,** verifique se existe conectividade desde o servidor de processo até ao armazenamento do Azure.

  ![Screenshot mostrando conectividade entre cbengine.exe e o URL de armazenamento Azure Blob.](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Verificar serviços

Se não houver conectividade desde o servidor de processos até ao URL de armazenamento de blob Azure, verifique se os serviços estão a funcionar.

1. No Painel de Controlo, selecione **Serviços.**
2. Verifique se os seguintes serviços estão a funcionar:

    - cxprocessador
    - InMage Scout VX Agent – Sentinel/Outpost
    - Agente de serviços de recuperação da Microsoft Azure
    - Serviço de Recuperação do Site microsoft Azure
    - tmansvc

3. Iniciar ou reiniciar qualquer serviço que não esteja a funcionar.
4. Verifique se o servidor de processo está ligado e acessível. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Passo 10: verifique a ligação do servidor de processo ao endereço IP público do Azure

1. No servidor de processos, em **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**, abra o mais recente ficheiro CBEngineCurr.errlog.
2. No ficheiro, a procura por **443**, ou para a tentativa de ligação de cordas **falhou**.

  ![Registos de erro na pasta Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Se vir problemas, localize o seu endereço IP público Azure no ficheiro CBEngineCurr.currLog utilizando a porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Na linha de comando do servidor de processo, utilize a Telnet para obter o seu endereço IP público Azure.
6. Se não conseguir ligar, siga o próximo procedimento.

## <a name="step-11-check-process-server-firewall-settings"></a>Passo 11: Verifique as definições de firewall do servidor do processo. 

Verifique se a firewall baseada em endereços IP no servidor de processos está a bloquear o acesso.

1. Para as regras de firewall baseadas em endereços IP:

    a) Descarregue a lista completa das gamas IP do Centro de [Dados do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Adicione as gamas de endereços IP à configuração da firewall, para garantir que a firewall permite a comunicação ao Azure (e à porta HTTPS predefinida, 443).

    c) Permitir intervalos de endereços IP para a região Azure da sua subscrição, e para a região Azure West US (utilizada para controlo de acessos e gestão de identidade).

2. Para firewalls baseadas em URL, adicione os URLs listados na tabela seguinte à configuração de firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Passo 12: Verificar definições de procuração de servidor de processo 

1. Se utilizar um servidor proxy, certifique-se de que o nome do servidor proxy é resolvido pelo servidor DNS. Verifique o valor que forneceu quando configura o servidor de configuração na chave de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Certifique-se de que as mesmas definições são utilizadas pelo agente de recuperação do local Azure para enviar dados.

    a) Procure por **Backup Microsoft Azure**.

    b) Abra **a Cópia de Segurança do Microsoft Azure**e selecione **Action**  >  **Change Properties**.

    c) No **separador Configuração Proxy,** o endereço proxy deve ser o mesmo que o endereço de procuração mostrado nas definições de registo. Caso contrário, altere-o para o mesmo endereço.

## <a name="step-13-check-bandwidth"></a>Passo 13: Verifique a largura de banda

Aumente a largura de banda entre o servidor de processo e o Azure e, em seguida, verifique se o problema ainda ocorre.


## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda, publique a sua pergunta na [página de perguntas do Microsoft Q&Uma página de perguntas para a recuperação do site Azure](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
