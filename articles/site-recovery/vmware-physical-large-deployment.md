---
title: Escala VMware/recuperação de desastres físicos com recuperação do local de Azure
description: Saiba como configurar a recuperação de desastres para o Azure para um grande número de VMware VMware no local ou servidores físicos com recuperação do site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cc87429f269fba5083b87e2c328f0e21de9707ff
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454352"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Configurar a recuperação de desastres à escala para VMware VMs/servidores físicos

Este artigo descreve como configurar a recuperação de desastres para a Azure para um grande número (> 1000) de VMware VMware no local ou servidores físicos no seu ambiente de produção, utilizando o serviço de Recuperação do [Local Azure.](site-recovery-overview.md)


## <a name="define-your-bcdr-strategy"></a>Defina a sua estratégia BCDR

Como parte da estratégia de continuidade do seu negócio e recuperação de desastres (BCDR), define objetivos de ponto de recuperação (RPOs) e objetivos de tempo de recuperação (RTOs) para as suas apps de negócio e cargas de trabalho. O RTO mede a duração do tempo e do nível de serviço no âmbito do qual uma aplicação ou processo de negócio deve ser restaurado e disponível, de modo a evitar problemas de continuidade.
- A Recuperação do Site fornece uma replicação contínua para VMware VMs e servidores físicos, e um [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) para RTO.
- Como planeia uma recuperação de desastres em larga escala para VMware VMs e descubra os recursos Azure de que precisa, pode especificar um valor RTO que será usado para cálculos de capacidade.


## <a name="best-practices"></a>Melhores práticas

Algumas boas práticas gerais para a recuperação de desastres em larga escala. Estas boas práticas são discutidas mais detalhadamente nas secções seguintes do documento.

- **Identificar os requisitos-alvo**: Estimar as capacidades e as necessidades de recursos em Azure antes de configurar a recuperação de desastres.
- **Plano para componentes de recuperação de locais**: Descubra quais os componentes de Recuperação do Site (servidor de configuração, servidores de processo) que necessita para satisfazer a sua capacidade estimada.
- **Configurar um ou mais servidores de processos de escala**: Não utilize o servidor de processo que está a ser em execução por padrão no servidor de configuração. 
- **Execute as atualizações mais recentes**: A equipa de Recuperação do Site lança regularmente novas versões de componentes de Recuperação de Sítios e deve certificar-se de que está a executar as versões mais recentes. Para ajudar nisso, acompanhe [as novidades para](site-recovery-whats-new.md) as atualizações e [ative e instale atualizações](service-updates-how-to.md) à medida que forem lançadas.
- **Monitorar proativamente**: À medida que a recuperação de desastres está a funcionar, deve monitorizar proactivamente o estado e a saúde das máquinas replicadas e dos recursos de infraestrutura.
- **Exercícios de recuperação de desastres**: Deve realizar exercícios de recuperação de desastres regularmente. Estes não têm impacto no seu ambiente de produção, mas ajudam a garantir que o fracasso em Azure funcionará como esperado quando necessário.



## <a name="gather-capacity-planning-information"></a>Recolher informação de planeamento de capacidade

Recolha informações sobre o seu ambiente no local, para ajudar a avaliar e estimar as necessidades de capacidade do seu alvo (Azure).
- Para vMware, executar o Planejador de Implementação de VMware VMs para fazer isto.
- Para servidores físicos, recolha a informação manualmente.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Executar o Planejador de Implementação para VMware VMs

O Deployment Planner ajuda-o a recolher informações sobre o seu ambiente vMware no local.

- Executar o Planejador de Implementação durante um período que representa um churn típico para os seus VMs. Isto gerará estimativas e recomendações mais precisas.
- Recomendamos que execute o Plano de Implementação na máquina do servidor de configuração, uma vez que o Planner calcula a produção a partir do servidor em que está a funcionar. [Saiba mais](site-recovery-vmware-deployment-planner-run.md#get-throughput) sobre a produção de medidas.
- Se ainda não tiver um servidor de configuração configurado:
    - [Obtenha uma visão geral](vmware-physical-azure-config-process-server-overview.md) dos componentes da Recuperação do Local.
    - [Configurar um servidor de configuração,](vmware-azure-deploy-configuration-server.md)a fim de executar o Plano de Implementação nele.

Em seguida, executar o Planejador da seguinte forma:

1. [Conheça](site-recovery-deployment-planner.md) o Planejador de Implementação. Pode descarregar a versão mais recente do portal ou [descarregá-la diretamente](https://aka.ms/asr-deployment-planner).
2. Reveja os [pré-requisitos](site-recovery-deployment-planner.md#prerequisites) e [as atualizações mais recentes](site-recovery-deployment-planner-history.md) para o Deployment Planner e [descarregue e extraia](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) a ferramenta.
3. [Execute o Plano de Implementação](site-recovery-vmware-deployment-planner-run.md) no servidor de configuração.
4. [Gere um relatório](site-recovery-vmware-deployment-planner-run.md#generate-report) para resumir estimativas e recomendações.
5. Analise as [recomendações](site-recovery-vmware-deployment-planner-analyze-report.md) do relatório e [as estimativas de custos.](site-recovery-vmware-deployment-planner-cost-estimation.md)

>[!NOTE]
> Por predefinição, a ferramenta é configurada para perfilar e gera relatório até 1000 VMs. Pode alterar este limite aumentando o valor da chave MaxVMsSupported no ficheiro ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Requisitos e capacidade do plano (Azure)

Utilizando as suas estimativas e recomendações recolhidas, pode planear recursos e capacidades de destino. Se executou o Planejador de Implementação de VMware VMs, pode utilizar algumas das [recomendações](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) do relatório para o ajudar.

- **VMs compatíveis**: Utilize este número para identificar o número de VMs que estão prontos para a recuperação de desastres para Azure. Recomendações sobre largura de banda de rede e núcleos Azure são baseadas neste número.
- **Largura de banda de rede necessária**: Note a largura de banda necessária para a replicação delta de VMs compatíveis. 
    - Quando executar o Planner, especifica o RPO desejado em minutos. As recomendações mostram-lhe a largura de banda necessária para cumprir o RPO 100% e 90% das vezes. 
    - As recomendações de largura de banda de rede têm em conta a largura de banda necessária para o número total de servidores de configuração e servidores de processo recomendados no Planner.
- **Núcleos Azure necessários**: Note o número de núcleos necessários na região de Azure alvo, com base no número de VMs compatíveis. Se não tiver núcleos suficientes, na Recuperação do Local de Failover não será capaz de criar os VMs Azure necessários.
- **Tamanho do lote VM recomendado**: O tamanho recomendado do lote baseia-se na capacidade de terminar a replicação inicial do lote dentro de 72 horas por defeito, enquanto cumpre um RPO de 100%. O valor da hora pode ser modificado.

Pode utilizar estas recomendações para planear os recursos Azure, largura de banda de rede e lotação VM.

## <a name="plan-azure-subscriptions-and-quotas"></a>Assinaturas e quotas do Plano Azure

Queremos assegurar que as quotas disponíveis na subscrição-alvo sejam suficientes para lidar com o fracasso.

**Tarefa** | **Detalhes** | **Ação**
--- | --- | ---
**Verifique os núcleos** | Se os núcleos da quota disponível não forem iguais ou excederem a contagem total de alvos no momento do failover, as falhas falharão. | Para VMware VMs, verifique se tem núcleos suficientes na subscrição-alvo para cumprir a recomendação do núcleo do Plano de Implementação.<br/><br/> Para servidores físicos, verifique se os núcleos Azure cumprem as suas estimativas manuais.<br/><br/> Para verificar quotas, no portal Azure > **Subscrição,** clique em **Utilização + quotas**.<br/><br/> [Saiba mais](../azure-portal/supportability/resource-manager-core-quotas-request.md) sobre o aumento das quotas.
**Verificar limites de failover** | O número de falhas não deve exceder os limites de failover da Recuperação do Local. |  Se as falhas excederem os limites, pode adicionar subscrições e falhar em várias subscrições ou aumentar a quota para uma subscrição. 


### <a name="failover-limits"></a>Limites de insusição

Os limites indicam o número de falhas suportadas pela Recuperação do Local dentro de uma hora, assumindo três discos por máquina.

O que obedece significa? Para iniciar um Azure VM, a Azure exige que alguns condutores estejam no estado de arranque do arranque, e serviços como o DHCP devem ser definidos para iniciar automaticamente.
- As máquinas que cumpram já terão estas definições no lugar.
- Para as máquinas que executam o Windows, pode verificar proactivamente a conformidade e torná-las conformes, se necessário. [Saiba mais](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- As máquinas Linux só são postas em conformidade no momento do incumprimento.

**A máquina está de acordo com o Azure?** | **Limites Azure VM (falha de disco gerido)**
--- | --- 
Yes | 2000
No | 1000

- Os limites pressupõem que estão em curso outros postos de trabalho mínimos na região-alvo para a subscrição.
- Algumas regiões de Azure são menores e podem ter limites ligeiramente mais baixos.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Estrutura de plano e conectividade VM

Depois de falhar em Azure, precisa das suas cargas de trabalho para funcionar como fizeram no local e para permitir que os utilizadores acedam a cargas de trabalho em execução nos VMs Azure.

- [Saiba mais](site-recovery-active-directory.md#test-failover-considerations) sobre falhar sobre a sua infraestrutura de Diretório Ativo ou DNS no local para Azure.
- [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) sobre a preparação para ligar-se aos VMs Azure após o failover.



## <a name="plan-for-source-capacity-and-requirements"></a>Plano de capacidade e requisitos de origem

É importante que tenha servidores de configuração suficientes e servidores de processos de escala para satisfazer os requisitos de capacidade. Ao iniciar a sua implementação em larga escala, comece com um único servidor de configuração e um servidor de processo de escala única. Ao atingir os limites prescritos, adicione servidores adicionais.

>[!NOTE]
> Para VMware VMs, o Deployment Planner faz algumas recomendações sobre a configuração e os servidores de processo de que necessita. Recomendamos que utilize as tabelas incluídas nos seguintes procedimentos, em vez de seguir a recomendação do Plano de Implantação. 


## <a name="set-up-a-configuration-server"></a>Configurar um servidor de configuração
 
A capacidade do servidor de configuração é afetada pelo número de máquinas que se replicam, e não pela taxa de churn de dados. Para descobrir se precisa de servidores de configuração adicionais, utilize estes limites de VM definidos.

**CPU** | **Memória** | **Disco de cache** | **Limite de máquina replicado**
 --- | --- | --- | ---
8 vCPUs<br> 2 tomadas * 4 núcleos @ 2.5 Ghz | 16 GB | 600 GB | Até 550 máquinas<br> Assume que cada máquina tem três discos de 100 GB cada.

- Estes limites baseiam-se num servidor de configuração configurado utilizando um modelo OVF.
- Os limites pressupõem que não está a utilizar o servidor de processo que está a ser em execução por padrão no servidor de configuração.

Se precisar de adicionar um novo servidor de configuração, siga estas instruções:

- [Crie um servidor de configuração](vmware-azure-deploy-configuration-server.md) para a recuperação de desastres VMware VM, utilizando um modelo OVF.
- [Crie manualmente um servidor de configuração](physical-azure-set-up-source.md) para servidores físicos ou para implementações VMware que não possam utilizar um modelo OVF.

Ao configurar um servidor de configuração, note que:

- Quando configurar um servidor de configuração, é importante considerar a subscrição e o cofre dentro do qual reside, uma vez que estes não devem ser alterados após a configuração. Se precisar de mudar o cofre, tem de dissociar o servidor de configuração do cofre e reregistrá-lo. Isto para a replicação de VMs no cofre.
- Se pretender configurar um servidor de configuração com vários adaptadores de rede, deve fazê-lo durante a configuração. Não pode fazer isto depois de registar o servidor de configuração no cofre.

## <a name="set-up-a-process-server"></a>Configurar um servidor de processo

A capacidade do servidor de processo é afetada pelas taxas de churn de dados, e não pelo número de máquinas ativadas para replicação.

- Para grandes implementações deve ter sempre pelo menos um servidor de processo de escala.
- Para descobrir se precisa de servidores adicionais, utilize a tabela seguinte.
- Recomendamos que adicione um servidor com a especificação mais alta. 


**CPU** | **Memória** | **Disco de cache** | **Taxa de churn**
 --- | --- | --- | --- 
12 vCPUs<br> 2 tomadas*6 núcleos @ 2.5 Ghz | 24 GB | 1 GB | Até 2 TB por dia

Configurar o servidor de processo da seguinte forma:

1. Reveja os [pré-requisitos.](vmware-azure-set-up-process-server-scale.md#prerequisites)
2. Instale o servidor no [portal,](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)ou a partir da linha de [comando](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Configure máquinas replicadas para utilizar o novo servidor. Se já tiver máquinas a replicar:
    - Pode [mover](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) uma carga de trabalho do servidor de processo para o novo servidor de processo.
    - Em alternativa, pode [mover](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) VMs específicos para o novo servidor de processo.



## <a name="enable-large-scale-replication"></a>Permitir a replicação em larga escala

Após a capacidade de planeamento e implantação dos componentes e infraestruturas necessários, permitir a replicação para um grande número de VMs.

1. Classificar as máquinas em lotes. Ativa-se a replicação de VMs dentro de um lote e, em seguida, passa para o lote seguinte.

    - Para VMware VMs, pode utilizar o [tamanho do lote VM recomendado](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) no relatório do Planeamento de Implementação.
    - Para máquinas físicas, recomendamos que identifique lotes com base em máquinas com um tamanho e quantidade semelhantes de dados, e no rendimento disponível da rede. O objetivo é em lotear máquinas que sejam suscetíveis de terminar a sua replicação inicial em cerca do mesmo tempo.
    
2. Se o disquete de uma máquina for elevado ou exceder os limites na Implementação do Plano, pode mover ficheiros não críticos que não precisa de replicar (como depósitos de registo ou ficheiros temporários) para fora da máquina. Para VMware VMs, pode mover estes ficheiros para um disco separado e, em seguida, [excluir esse disco](vmware-azure-exclude-disk.md) da replicação.
3. Antes de ativar a replicação, verifique se as máquinas cumprem os [requisitos de replicação](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Configure uma política de replicação para [VMware VMs](vmware-azure-set-up-replication.md#create-a-policy) ou [servidores físicos](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Ativar a replicação de [VMware VMs](vmware-azure-enable-replication.md) ou [servidores físicos](physical-azure-disaster-recovery.md#enable-replication). Isto inicia a replicação inicial para as máquinas selecionadas.

## <a name="monitor-your-deployment"></a>Monitorizar a implementação

Depois de iniciar a replicação para o primeiro lote de VMs, comece a monitorizar a sua implementação da seguinte forma:  

1. Atribua um administrador de recuperação de desastres para monitorizar o estado de saúde das máquinas replicadas.
2. [Monitorize eventos](site-recovery-monitor-and-troubleshoot.md) para itens replicados e a infraestrutura.
3. [Monitorize a saúde](vmware-physical-azure-monitor-process-server.md) dos seus servidores de processos de escala.
4. Inscreva-se para receber [notificações de e-mail para eventos,](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) para facilitar a monitorização.
5. Realizar [exercícios regulares de recuperação de desastres,](site-recovery-test-failover-to-azure.md)para garantir que tudo está funcionando como esperado.


## <a name="plan-for-large-scale-failovers"></a>Plano para falhas em larga escala

Em caso de desastre, poderá ter de falhar com um grande número de máquinas/cargas de trabalho para a Azure. Prepare-se para este tipo de evento da seguinte forma.

Pode preparar-se antecipadamente para o failover da seguinte forma:

- [Prepare a sua infraestrutura e VMs](#plan-infrastructure-and-vm-connectivity) para que as suas cargas de trabalho estejam disponíveis após o failover, e para que os utilizadores possam aceder aos VMs Azure.
- Note os [limites de falha](#failover-limits) anteriormente neste documento. Certifique-se de que as suas falhas se situarão dentro destes limites.
- Faça [exercícios regulares de recuperação de desastres.](site-recovery-test-failover-to-azure.md) Os exercícios ajudam a:
    - Encontre lacunas na sua implantação antes de falhar.
    - Estimar o RTO de ponta a ponta para as suas aplicações.
    - Estimar o RPO de ponta a ponta para as suas cargas de trabalho.
    - Identifique conflitos de intervalo de endereço IP.
    - Ao fazer exercícios, recomendamos que não utilize redes de produção para exercícios e limpe as falhas de teste após cada broca.

Para executar uma falha em larga escala, recomendamos o seguinte:

1. Criar planos de recuperação para a carga de trabalho falhar.
    - Cada plano de recuperação pode desencadear falhas de até 100 máquinas.
    - [Saiba mais](recovery-plan-overview.md) sobre planos de recuperação.
2. Adicione scripts de runbook da Azure Automation aos planos de recuperação, para automatizar quaisquer tarefas manuais no Azure. As tarefas típicas incluem configurar os equilibradores de carga, atualizar o DNS, etc. [Saiba mais](site-recovery-runbook-automation.md)
2. Antes de falhar, prepare as máquinas Windows de modo a que cumpram o ambiente Azure. [Os limites de incumprimento](#plan-azure-subscriptions-and-quotas) são mais elevados para as máquinas que cumprem. [Saiba mais](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) sobre os livros de corridas.
4.  Falha no gatilho com o [start-AzRecoveryServicesAsrPlannedFailoverJob](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob) PowerShell cmdlet, juntamente com um plano de recuperação.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Monitorizar o Site Recovery](site-recovery-monitor-and-troubleshoot.md)
