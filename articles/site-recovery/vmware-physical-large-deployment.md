---
title: Configurar a recuperação após desastre para o Azure para um grande número de VMs de VMware ou servidores físicos com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como configurar a recuperação após desastre para o Azure para um grande número de VMs de VMware no local ou servidores físicos com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: 593a82221ec94e43d50b7382ba89053d5f68864a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65805925"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Configurar a recuperação após desastre à escala da VMware VMs/servidores físicos

Este artigo descreve como configurar a recuperação após desastre para o Azure para (> 1000) um grande número de VMs de VMware no local ou servidores físicos no seu ambiente de produção, utilizando o [do Azure Site Recovery](site-recovery-overview.md) serviço.


## <a name="define-your-bcdr-strategy"></a>Definir a estratégia BCDR

Como parte da continuidade do negócio e da estratégia de recuperação (BCDR) após desastre, deve definir objetivos de ponto de recuperação (RPOs) e objetivos de tempo de recuperação (RTOs) para as suas aplicações empresariais e cargas de trabalho. RTO mede a duração de tempo e o serviço de nível no qual uma aplicação de negócio ou um processo tem de ser restaurada e disponível, para evitar problemas de continuidade.
- O site Recovery fornece replicação contínua para VMs de VMware e servidores físicos e um [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) de RTO.
- Quando planear a recuperação de desastres em grande escala para VMs de VMware e descobrir os recursos do Azure que necessários, pode especificar um valor RTO que será utilizado para cálculos de capacidade.


## <a name="best-practices"></a>Melhores práticas

Algumas práticas recomendadas gerais para recuperação após desastre em grande escala. Essas práticas recomendadas são abordadas mais detalhadamente nas próximas seções do documento.

- **Identificar requisitos de destino**: Estimar as necessidades de capacidade e recursos no Azure antes de definir a recuperação após desastre.
- **Planear para componentes do Site Recovery**: Descobrir quais componentes do Site Recovery (servidor de configuração, servidores de processos) que precisa para satisfazer a capacidade estimada.
- **Configurar um ou mais servidores de processos de escalamento horizontal**: Não utilize o servidor de processos que está a ser executado por predefinição no servidor de configuração. 
- **Execute as atualizações mais recentes**: A equipe do Site Recovery lança novas versões de componentes do Site Recovery regularmente e certifique-se de que está a executar as versões mais recentes. Para ajudar com isso, controlar [quais são as Novidades](site-recovery-whats-new.md) para as atualizações, e [ativar e instalar atualizações](service-updates-how-to.md) lançamento.
- **Monitorizar proativamente**: À medida que se a recuperação após desastre em funcionamento, deve monitorar proativamente o estado de funcionamento de máquinas replicadas e recursos de infraestrutura.
- **Testes de recuperação após desastre**: Deve executar após desastre testes de recuperação em intervalos regulares. Estas não tem impacto no seu ambiente de produção, mas ajudam a garantir que a ativação pós-falha para o Azure irá funcionar conforme esperado quando necessário.



## <a name="gather-capacity-planning-information"></a>Recolher informações de planeamento de capacidade

Recolha informações sobre o ambiente no local, para ajudar a avaliar e calcular as que necessidades de capacidade do seu destino (Azure).
- Para o VMware, execute o planeador de implementação para VMs de VMware para o fazer.
- Para servidores físicos, recolha as informações manualmente.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Executar o planeador de implementação para VMs de VMware

O planeador de implementações ajuda-o a recolher informações sobre o ambiente do VMware no local.

- Execute o planeador de implementação durante um período que representa as alterações a dados típica para as suas VMs. Isso irá gerar recomendações e estimativas mais precisas.
- Recomendamos que execute o planeador de implementação na máquina do servidor de configuração, uma vez que o planeador de implementações calcula o débito do servidor no qual está em execução. [Saiba mais](site-recovery-vmware-deployment-planner-run.md#get-throughput) sobre medir o débito.
- Se ainda não tiver um configuração de servidor de configuração:
    - [Obtenha uma visão geral](vmware-physical-azure-config-process-server-overview.md) de componentes do Site Recovery.
    - [Configurar um servidor de configuração](vmware-azure-deploy-configuration-server.md), para executar o planeador de implementação no mesmo.

Em seguida, execute o planeador de implementações da seguinte forma:

1. [Saiba mais sobre](site-recovery-deployment-planner.md) o planeador de implementação. Pode baixar a versão mais recente a partir do portal, ou [transferi-lo diretamente](https://aka.ms/asr-deployment-planner).
2. Reveja a [pré-requisitos](site-recovery-deployment-planner.md#prerequisites) e [atualizações mais recentes](site-recovery-deployment-planner-history.md) para o planeador de implementação, e [transferir e extrair](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) a ferramenta.
3. [Executar o planeador de implementação](site-recovery-vmware-deployment-planner-run.md) no servidor de configuração.
4. [Gerar um relatório](site-recovery-vmware-deployment-planner-run.md#generate-report) para resumir as estimativas e recomendações.
5. Analisar os [relatório de recomendações](site-recovery-vmware-deployment-planner-analyze-report.md) e [estimativas de custos](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Por predefinição, a ferramenta está configurada para o perfil e gera o relatório para até 1000 VMs. Pode alterar este limite, aumentando o valor chave MaxVMsSupported no ficheiro Asrdeploymentplanner.

## <a name="plan-target-azure-requirements-and-capacity"></a>Planear os requisitos de destino (Azure) e a capacidade

Utilize as suas estimativas recolhidas e recomendações, pode planear para recursos de destino e a capacidade. Se tiver executado o planeador de implementação para VMs de VMware, pode utilizar um número do [relatório de recomendações](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) para ajudá-lo.

- **As VMs compatíveis**: Utilize este número para identificar o número de VMs que estão prontos para recuperação após desastre para o Azure. Recomendações sobre a largura de banda de rede e de núcleos do Azure baseiam-se este número.
- **Necessária largura de banda de rede**: Tenha em atenção a largura de banda que precisa para a replicação de diferenças de VMs compatíveis. 
    - Quando executa o planeador de especificar o RPO pretendido em minutos. As recomendações mostram-lhe a largura de banda necessária para satisfazer esse RpO durante 100% e 90% do tempo. 
    - As recomendações de largura de banda de rede levam em conta a largura de banda necessária para o número total de servidores de configuração e servidores de processo recomendados no Planner.
- **Necessário núcleos do Azure**: Observe o número de núcleos que precisa no destino região do Azure, com base no número de VMs compatíveis. Se não tiver núcleos suficientes, a ativação pós-falha Site Recovery não vai conseguir criar as VMs do Azure necessários.
- **Tamanho de lote VM recomendado**: O tamanho de lote recomendado baseia-se a capacidade para concluir a replicação inicial para o batch dentro de 72 horas por predefinição, cumprindo um RPO de 100%. O valor de hora pode ser modificado.

Pode utilizar estas recomendações para planear para recursos do Azure, a largura de banda de rede e a criação de batches de VM.

## <a name="plan-azure-subscriptions-and-quotas"></a>Planear as subscrições do Azure e quotas

Queremos certificar-se de que as quotas disponíveis na subscrição de destino são suficientes para processar a ativação pós-falha.

**Tarefa** | **Detalhes** | **ação**
--- | --- | ---
**Núcleos de verificação** | Se os núcleos em à quota disponível não ser igual ou exceder a contagem total de destino no momento da ativação pós-falha, as ativações pós-falha irão falhar. | Para VMs de VMware, verifique se que tiver núcleos suficientes na subscrição de destino para satisfazer a recomendação de núcleo do Planeador de implementações.<br/><br/> Para servidores físicos, verifique que os núcleos do Azure cumprem suas estimativas manuais.<br/><br/> Para verificar as quotas, no portal do Azure > **subscrição**, clique em **utilização + quotas**.<br/><br/> [Saiba mais](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) sobre como aumentar as quotas.
**Limites de ativação pós-falha de verificação** | O número de ativações pós-falha não pode exceder os limites de ativação pós-falha do Site Recovery. |  Se as ativações pós-falha excederem os limites, pode adicionar subscrições e a ativação pós-falha para várias subscrições ou aumentar a quota para uma subscrição. 


### <a name="failover-limits"></a>Limites de ativação pós-falha

Os limites de indicam o número de ativações pós-falha que são suportadas pelo Site Recovery dentro de uma hora, partindo do princípio de três discos por máquina.

O que está em conformidade significam? Para iniciar uma VM do Azure, Azure requer alguns drivers estar num Estado de início de arranque e de serviços como o DHCP para ser definido para iniciar automaticamente.
- Computadores que estejam em conformidade já têm estas definições no local.
- Para máquinas que executem o Windows, proativamente pode verificar a conformidade e torná-los em conformidade se for necessário. [Saiba mais](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Máquinas do Linux apenas são consideradas em conformidade no momento da ativação pós-falha.

**Máquina está em conformidade com o Azure?** | **Limites VM do Azure (ativação pós-falha de disco gerido)**
--- | --- 
Sim | 2000
Não | 1000

- Limites partem do princípio que mínima que outras tarefas estão em curso na região de destino para a subscrição.
- Algumas regiões do Azure são mais pequenas e podem ter limites ligeiramente inferiores.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planear a infraestrutura e conectividade de VMS

Após a ativação pós-falha para o Azure tem as suas cargas de trabalho para operar como acontecia no local e para permitir que os utilizadores aceder a cargas de trabalho em execução nas VMs do Azure.

- [Saiba mais](site-recovery-active-directory.md#test-failover-considerations) prestes a falhar ao longo de sua infraestrutura no local do Active Directory ou o DNS para o Azure.
- [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) sobre a preparação para ligar a VMs do Azure após a ativação pós-falha.



## <a name="plan-for-source-capacity-and-requirements"></a>Planear a capacidade de origem e de requisitos

É importante que tem os servidores de configuração suficiente e servidores de processos de escalamento horizontal para atender aos requisitos de capacidade. Quando começar a sua implantação em larga escala, começar com um único servidor de configuração e um servidor de processos de escalamento horizontal única. Como atingir os limites de prescrita, adicione servidores adicionais.

>[!NOTE]
> Para VMs de VMware, o Deployment Planner faz algumas recomendações sobre os servidores de configuração e o processo, que precisa. Recomendamos que utilize as tabelas incluídas nos procedimentos a seguir, em vez de seguir a recomendação do Planeador de implementações. 


## <a name="set-up-a-configuration-server"></a>Configurar um servidor de configuração
 
Capacidade do servidor de configuração é afetado pelo número de máquinas a replicar e não pelos dados da taxa de abandono. Para saber se precisa de servidores de configuração adicionais, utilize estes limites VM definidos.

**CPU** | **Memória** | **Disco de cache** | **Limite de máquina de replicados**
 --- | --- | --- | ---
8 vCPUs<br> 2 sockets * 4 núcleos @ 2,5 Ghz | 16 GB | 600 TB | Até 550 máquinas<br> Pressupõe-se de que cada máquina tem três discos de 100 GB cada.

- Estes limites baseiam-se um servidor de configuração configurado com um modelo OVF.
- Os limites partem do princípio de que não está a utilizar o servidor de processos que está a ser executado por predefinição no servidor de configuração.

Se precisar de adicionar um novo servidor de configuração, siga estas instruções:

- [Configurar um servidor de configuração](vmware-azure-deploy-configuration-server.md) para a recuperação de desastres da VM de VMware, com um modelo do OVF.
- [Configurar um servidor de configuração](physical-azure-set-up-source.md) manualmente para os servidores físicos, ou para implementações do VMware que não é possível utilizar um modelo OVF.

Como configurar um servidor de configuração, tenha em atenção que:

- Quando configurar um servidor de configuração, é importante considerar a subscrição e o Cofre no qual ela reside, uma vez que estes não devem ser alterados após a configuração. Se tiver de alterar o cofre, terá de desassociar o servidor de configuração do cofre e voltar a registá-lo. Isto para a replicação de VMs no cofre.
- Se pretender configurar um servidor de configuração com vários adaptadores de rede, deve fazer isso durante o conjunto de cópia de segurança. Não é possível fazê-lo Depois de registar o servidor de configuração no cofre.

## <a name="set-up-a-process-server"></a>Configurar um servidor de processos

Capacidade do servidor de processo é afetada pelas taxas de abandono de dados e não pelo número de máquinas ativada para replicação.

- Para grandes implementações sempre deve ter pelo menos um servidor de processos de escalamento horizontal.
- Para saber se precisa de servidores adicionais, utilize a tabela seguinte.
- Recomendamos que adiciona um servidor com a especificação de mais alto. 


**CPU** | **Memória** | **Disco de cache** | **Taxa de abandono**
 --- | --- | --- | --- 
12 vCPUs<br> 2 sockets * 6 núcleos @ 2,5 Ghz | 24 GB | 1 GB | Até 2 TB por dia

Configure o servidor de processos da seguinte forma:

1. Reveja os [pré-requisitos](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Instalar o servidor no [portal](vmware-azure-set-up-process-server-scale.md#install-from-the-ui), ou a partir do [linha de comandos](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Configure máquinas replicadas para utilizar o novo servidor. Se já tiver máquinas a replicar:
    - Pode [mover](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) uma carga de trabalho do servidor de todo o processo para o novo servidor de processo.
    - Em alternativa, pode [mover](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) VMs específicas para o novo servidor de processo.



## <a name="enable-large-scale-replication"></a>Ativar a replicação em grande escala

Após o planeamento de capacidade e implantar os componentes necessários e a infraestrutura, ative a replicação para um grande número de VMs.

1. Máquinas de ordenação em lotes. Ativar a replicação para as VMs dentro de um lote e, em seguida, avançar para o lote seguinte.

    - Para VMs de VMware, pode utilizar o [tamanho de lote VM recomendado](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) no relatório do Planeador de implementações.
    - Para máquinas físicas, recomendamos que identifique lotes com base em computadores que têm um tamanho e a quantidade de dados semelhantes e no débito de rede disponível. O objetivo é máquinas de batch que têm probabilidades de concluir a replicação inicial dentro de aproximadamente o mesmo tempo.
    
2. Se alterações de disco para uma máquina é elevada, ou excede os limites na implementação thePlanner, pode mover ficheiros não críticas, que não precisa de replicar (como informações de registo ou ficheiros temporários) fora da máquina. Para VMs de VMware, pode mover estes ficheiros para um disco separado e, em seguida [excluir esse disco](vmware-azure-exclude-disk.md) da replicação.
3. Antes de ativar a replicação, verifique que máquinas cumprem [requisitos de replicação](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Configurar uma política de replicação para [VMs de VMware](vmware-azure-set-up-replication.md#create-a-policy) ou [servidores físicos](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Ativa a replicação [VMs de VMware](vmware-azure-enable-replication.md) ou [servidores físicos](physical-azure-disaster-recovery.md#enable-replication). Isso ativa a replicação inicial para as máquinas selecionadas.

## <a name="monitor-your-deployment"></a>Monitorizar a implementação

Depois de iniciar a replicação para o primeiro batch de VMs, começar a monitorizar a implementação da seguinte forma:  

1. Designa um administrador de recuperação após desastre para monitorizar o estado de funcionamento das máquinas replicadas.
2. [Monitorizar eventos](site-recovery-monitor-and-troubleshoot.md) para itens replicados e a infraestrutura.
3. [Monitorizar o estado de funcionamento](vmware-physical-azure-monitor-process-server.md) dos seus servidores de processo de escalamento horizontal.
4. Inscreva-se para obter [notificações por e-mail](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) eventos, para uma monitorização mais fácil.
5. Conduzir regular [testes de recuperação após desastre](site-recovery-test-failover-to-azure.md), para se certificar de que está tudo a funcionar conforme esperado.


## <a name="plan-for-large-scale-failovers"></a>Plano para as ativações pós-falha em grande escala

Em caso de desastre, poderá ter de efetuar a ativação pós-falha de um grande número de máquinas/cargas de trabalho para o Azure. Prepare para este tipo de evento da seguinte forma.

Pode se preparar com antecedência para ativação pós-falha da seguinte forma:

- [Preparar a sua infraestrutura e as VMs](#plan-infrastructure-and-vm-connectivity) para que as cargas de trabalho vão estar disponíveis após a ativação pós-falha e para que os usuários podem acessar as VMs do Azure.
- Tenha em atenção a [limites de ativação pós-falha](#failover-limits) mais acima neste documento. Certifique-se de que seu as ativações pós-falha irão ser abrangidos por estes limites.
- Executar regular [testes de recuperação após desastre](site-recovery-test-failover-to-azure.md). As explorações de ajudam a:
    - Encontre lacunas na sua implementação antes da ativação pós-falha.
    - Estimar o RTO de ponto-a-ponto para as suas aplicações.
    - Calcular o RPO de ponta a ponta para cargas de trabalho.
    - Identifica conflitos de intervalo de endereços IP.
    - Durante a execução de testes, recomendamos que não utilizar as redes de produção para testes, evite usar os mesmos nomes de sub-rede em redes de produção e teste e limpeza ativações pós-falha de teste após cada teste.

Para executar uma ativação pós-falha em grande escala, recomendamos o seguinte:

1. Crie planos de recuperação para ativação pós-falha da carga de trabalho.
    - Cada plano de recuperação pode acionar a ativação pós-falha de até 50 máquinas.
    - [Saiba mais](recovery-plan-overview.md) sobre os planos de recuperação.
2. Adicione scripts de runbook de automatização do Azure aos planos de recuperação, para automatizar tarefas manuais no Azure. As tarefas típicas incluem a configuração de balanceadores de carga, a atualizar o DNS etc. [Saiba mais](site-recovery-runbook-automation.md)
2. Antes de ativação pós-falha, prepare máquinas do Windows, de modo a que estão em conformidade com o ambiente do Azure. [Limites de ativação pós-falha](#plan-azure-subscriptions-and-quotas) são superiores para máquinas que estejam em conformidade. [Saiba mais](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) informações sobre runbooks.
4.  Acionar a ativação pós-falha com o [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) cmdlet do PowerShell, juntamente com um plano de recuperação.



## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Recuperação de sites do monitor](site-recovery-monitor-and-troubleshoot.md)
