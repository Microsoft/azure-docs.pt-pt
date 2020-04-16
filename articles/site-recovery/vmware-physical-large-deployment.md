---
title: Recuperação de VMware/desastre físico em escala com recuperação do site Azure
description: Saiba como configurar a recuperação de desastres para o Azure para um grande número de VMware VMware no local ou servidores físicos com recuperação do site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a3a2317554f02dc1f1198d8019bbfdb50e3cc71c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409775"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Configurar a recuperação de desastres em escala para VMware VMs/servidores físicos

Este artigo descreve como configurar a recuperação de desastres para o Azure para grandes números (> 1000) de VMware no local ou servidores físicos no seu ambiente de produção, utilizando o serviço de recuperação de [sites Azure.](site-recovery-overview.md)


## <a name="define-your-bcdr-strategy"></a>Defina a sua estratégia BCDR

Como parte da sua estratégia de continuidade de negócios e recuperação de desastres (BCDR), define os objetivos do ponto de recuperação (RPOs) e os objetivos de tempo de recuperação (RTOs) para as suas aplicações de negócio e cargas de trabalho. A RTO mede a duração do tempo e do nível de serviço no âmbito do qual uma aplicação ou processo empresarial deve ser restaurado e disponível, a fim de evitar problemas de continuidade.
- A Recuperação do Site fornece replicação contínua para VMware VMs e servidores físicos, e um [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) para RTO.
- Como planeia uma recuperação de desastres em larga escala para VMware VMs e descubra os recursos Azure de que necessita, pode especificar um valor de RTO que será usado para cálculos de capacidade.


## <a name="best-practices"></a>Melhores práticas

Algumas boas práticas gerais para a recuperação de desastres em larga escala. Estas boas práticas são discutidas mais detalhadamente nas secções seguintes do documento.

- **Identificar os requisitos**do alvo : Calcule as necessidades de capacidade e recursos em Azure antes de configurar a recuperação de desastres.
- **Plano para componentes**de recuperação do site : Descubra quais os componentes de recuperação do site (servidor de configuração, servidores de processos) que precisa para satisfazer a sua capacidade estimada.
- **Configurar um ou mais servidores de processo sem escalas**: Não utilize o servidor de processo que está a funcionar por padrão no servidor de configuração. 
- **Execute as últimas atualizações**: A equipa de Recuperação do Site lança regularmente novas versões dos componentes da Recuperação do Site, e deve certificar-se de que está a executar as versões mais recentes. Para ajudar nisso, rastreie [as novidades](site-recovery-whats-new.md) para atualizações e [ative e instale atualizações](service-updates-how-to.md) à medida que forem lançadas.
- **Monitorpromente**: À medida que obtém a recuperação de desastres em funcionamento, deve monitorizar proativamente o estado e a saúde das máquinas replicadas e dos recursos de infraestrutura.
- **Exercícios de recuperação**de desastres: Deve executar exercícios de recuperação de desastres regularmente. Estes não têm impacto no seu ambiente de produção, mas ajudam a garantir que a falha para o Azure funcionará como esperado quando necessário.



## <a name="gather-capacity-planning-information"></a>Recolher informações sobre planeamento de capacidades

Recolha informações sobre o seu ambiente no local, para ajudar a avaliar e estimar as suas necessidades de capacidade (Azure).
- Para vMware, execute o Planificador de Implementação para VMware VMs para fazer isto.
- Para servidores físicos, recolher a informação manualmente.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Executar o Planejador de Implementação para VMware VMs

O Planificador de Implementação ajuda-o a recolher informações sobre o seu ambiente vMware no local.

- Execute o Planificador de Implantação durante um período que represente o churn típico para os seus VMs. Isto gerará estimativas e recomendações mais precisas.
- Recomendamos que execute o Planificador de Implementação na máquina do servidor de configuração, uma vez que o Planner calcula a entrada do servidor em que está a funcionar. [Saiba mais](site-recovery-vmware-deployment-planner-run.md#get-throughput) sobre a medição da entrada.
- Se ainda não tiver um servidor de configuração configurado:
    - [Obtenha uma visão geral](vmware-physical-azure-config-process-server-overview.md) dos componentes de Recuperação do Local.
    - [Configurar um servidor de configuração,](vmware-azure-deploy-configuration-server.md)para executar o Planificador de Implementação nele.

Em seguida, executar o Planificador da seguinte forma:

1. [Saiba mais sobre](site-recovery-deployment-planner.md) o Planejador de Implantação. Pode descarregar a versão mais recente a partir do portal ou [descarregá-la diretamente.](https://aka.ms/asr-deployment-planner)
2. Reveja os [pré-requisitos](site-recovery-deployment-planner.md#prerequisites) e [as últimas atualizações](site-recovery-deployment-planner-history.md) para o Planificador de Implementação e [descarregue e extrai](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) a ferramenta.
3. [Executar o Planificador](site-recovery-vmware-deployment-planner-run.md) de Implementação no servidor de configuração.
4. [Gerar um relatório](site-recovery-vmware-deployment-planner-run.md#generate-report) para resumir estimativas e recomendações.
5. Analise as [recomendações](site-recovery-vmware-deployment-planner-analyze-report.md) do relatório e as estimativas de [custos.](site-recovery-vmware-deployment-planner-cost-estimation.md)

>[!NOTE]
> Por padrão, a ferramenta está configurada para perfilar e gera relatório para até 1000 VMs. Pode alterar este limite aumentando o valor-chave suportado pelo MaxVMs no ficheiro ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Requisitos e capacidade do alvo do plano (Azure)

Usando as suas estimativas e recomendações recolhidas, pode planear recursos e capacidade seletivas. Se executou o Planificador de Implementação de VMware VMs, pode usar [algumas recomendações](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) do relatório para ajudá-lo.

- **VMs compatíveis**: Utilize este número para identificar o número de VMs que estão prontos para a recuperação de desastres para o Azure. Recomendações sobre largura de banda da rede e núcleos Azure são baseados neste número.
- **Largura de banda de rede necessária**: Note a largura de banda necessária para a replicação delta de VMs compatíveis. 
    - Quando executa o Planificador, especifique o RPO desejado em minutos. As recomendações mostram-lhe a largura de banda necessária para cumprir o RPO 100% e 90% das vezes. 
    - As recomendações de largura de banda da rede têm em conta a largura de banda necessária para o número total de servidores de configuração e servidores de processorecomendados no Planificador.
- **Núcleos Azure requeridos**: Note o número de núcleos que necessita na região-alvo do Azure, com base no número de VMs compatíveis. Se não tiver núcleos suficientes, no Failover Site Recovery não será capaz de criar os VMs Azure necessários.
- Tamanho recomendado do **lote vm**: O tamanho recomendado do lote baseia-se na capacidade de terminar a replicação inicial para o lote dentro de 72 horas por padrão, enquanto se encontra com um RPO de 100%. O valor da hora pode ser modificado.

Pode utilizar estas recomendações para planear recursos Azure, largura de banda da rede e lotação vm.

## <a name="plan-azure-subscriptions-and-quotas"></a>Plan Azure subscrições e quotas

Queremos garantir que as quotas disponíveis na subscrição-alvo são suficientes para lidar com o failover.

**Tarefa** | **Detalhes** | **Ação**
--- | --- | ---
**Verifique os núcleos** | Se os núcleos da quota disponível não forem iguais ou excederem a contagem total de alvos no momento da falha, as falhas falharão. | Para VMware VMs, verifique se tem núcleos suficientes na subscrição do alvo para atender à recomendação central do Planificador de Implementação.<br/><br/> Para servidores físicos, verifique se os núcleos Azure cumprem as suas estimativas manuais.<br/><br/> Para verificar quotas, no portal Azure > **Subscrição,** clique em **Utilização + quotas.**<br/><br/> [Saiba mais](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) sobre o aumento das quotas.
**Verificar limites de falha** | O número de falhas não deve exceder os limites de falha de recuperação do site. |  Se as falhas excederem os limites, pode adicionar subscrições e falhar em várias subscrições ou aumentar a quota para uma subscrição. 


### <a name="failover-limits"></a>Limites de failover

Os limites indicam o número de falhas que são suportadas pela Recuperação do Local dentro de uma hora, assumindo três discos por máquina.

O que significa o cumprimento? Para iniciar um Azure VM, o Azure exige que alguns condutores estejam no estado de arranque, e serviços como o DHCP devem começar automaticamente.
- As máquinas que cumpram já terão estas definições no lugar.
- Para máquinas que executam o Windows, pode verificar proativamente a conformidade e torná-las conformes se necessário. [Saiba mais](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- As máquinas Linux só são postas em conformidade no momento da falha.

**A máquina está em conformidade com o Azure?** | **Limites de VM Azure (falha no disco gerido)**
--- | --- 
Sim | 2000
Não | 1000

- Os limites pressupõem que estão em curso outros postos de trabalho mínimos na região-alvo para a subscrição.
- Algumas regiões de Azure são menores e podem ter limites ligeiramente mais baixos.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Infraestrutura de planos e conectividade VM

Depois de falhar com o Azure, precisa das suas cargas de trabalho para funcionar como fizeram no local e para permitir aos utilizadores acederem a cargas de trabalho em funcionamento nos VMs Azure.

- [Saiba mais](site-recovery-active-directory.md#test-failover-considerations) sobre falhar sobre a sua infraestrutura de Diretório Ativo ou DNS no local para o Azure.
- [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) sobre a preparação para ligar aos VMs Azure após a falha.



## <a name="plan-for-source-capacity-and-requirements"></a>Plano de capacidade e requisitos de origem

É importante que tenha servidores de configuração suficientes e servidores de processos de escala para satisfazer os requisitos de capacidade. À medida que inicia a sua implementação em larga escala, comece com um único servidor de configuração e um servidor de processo de escala única. Ao atingir os limites prescritos, adicione servidores adicionais.

>[!NOTE]
> Para VMware VMs, o Planificador de Implementação faz algumas recomendações sobre a configuração e servidores de processo saem. Recomendamos que utilize as tabelas incluídas nos seguintes procedimentos, em vez de seguir a recomendação do Planificador de Implantação. 


## <a name="set-up-a-configuration-server"></a>Configurar um servidor de configuração
 
A capacidade do servidor de configuração é afetada pelo número de máquinas que se replicam e não pela taxa de recolha de dados. Para descobrir se precisa de servidores de configuração adicionais, utilize estes limites vm definidos.

**CPU** | **Memória** | **Disco cache** | **Limite de máquina replicado**
 --- | --- | --- | ---
8 vCPUs<br> 2 tomadas * 4 núcleos @ 2.5 Ghz | 16 GB | 600 GB | Até 550 máquinas<br> Assume que cada máquina tem três discos de 100 GB cada.

- Estes limites baseiam-se num servidor de configuração configurado utilizando um modelo OVF.
- Os limites pressupõem que não está a utilizar o servidor de processo sintetizador que está a funcionar por padrão no servidor de configuração.

Se precisar de adicionar um novo servidor de configuração, siga estas instruções:

- [Configurar um servidor de configuração](vmware-azure-deploy-configuration-server.md) para a recuperação de desastres VMware VM, utilizando um modelo OVF.
- [Configurar manualmente um servidor](physical-azure-set-up-source.md) de configuração para servidores físicos ou para implementações vMware que não possam utilizar um modelo OVF.

Ao configurar um servidor de configuração, note que:

- Quando configura um servidor de configuração, é importante considerar a subscrição e o cofre dentro do qual reside, uma vez que estes não devem ser alterados após a configuração. Se precisar de alterar o cofre, tem de dissociar o servidor de configuração do cofre e registá-lo novamente. Isto impede a replicação de VMs no cofre.
- Se pretender configurar um servidor de configuração com vários adaptadores de rede, deverá fazê-lo durante a configuração. Não pode sê-lo depois de registar o servidor de configuração no cofre.

## <a name="set-up-a-process-server"></a>Configurar um servidor de processo

A capacidade do servidor de processos é afetada pelas taxas de reprodução de dados, e não pelo número de máquinas ativadas para a replicação.

- Para grandes implementações, deve ter sempre pelo menos um servidor de processo sem escalas.
- Para descobrir se precisa de servidores adicionais, utilize a tabela seguinte.
- Recomendamos que adicione um servidor com a maior especificação. 


**CPU** | **Memória** | **Disco cache** | **Taxa de churn**
 --- | --- | --- | --- 
12 vCPUs<br> 2 tomadas*6 núcleos @ 2.5 Ghz | 24 GB | 1 GB | Até 2 TB por dia

Configurar o servidor de processo da seguinte forma:

1. Reveja os [pré-requisitos.](vmware-azure-set-up-process-server-scale.md#prerequisites)
2. Instale o servidor no [portal,](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)ou a partir da linha de [comando](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Configure as máquinas replicadas para utilizar o novo servidor. Se já tem máquinas a replicar:
    - Pode [mover](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) toda uma carga de trabalho do servidor de processopara o novo servidor de processos.
    - Em alternativa, pode [mover](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) VMs específicos para o novo servidor de processos.



## <a name="enable-large-scale-replication"></a>Permitir a replicação em larga escala

Após o planeamento da capacidade e implantação dos componentes e infraestruturas necessários, permitir a replicação para um grande número de VMs.

1. Separe as máquinas em lotes. Ativa a replicação de VMs dentro de um lote e, em seguida, passa para o lote seguinte.

    - Para VMware VMs, pode utilizar o tamanho recomendado do [lote VM](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) no relatório 'Deployment Planner'.
    - Para máquinas físicas, recomendamos que identifique lotes baseados em máquinas que tenham um tamanho e quantidade semelhantes de dados, e na entrada de rede disponível. O objetivo é o lote de máquinas que possam terminar a sua replicação inicial em cerca do mesmo período de tempo.
    
2. Se o disco agitar para uma máquina for elevado ou exceder os limites no Implantação do Planner, pode mover ficheiros não críticos que não precisa de replicar (como depósitos de registo ou ficheiros temporários) da máquina. Para VMware VMs, pode mover estes ficheiros para um disco separado e, em seguida, [excluir esse disco](vmware-azure-exclude-disk.md) da replicação.
3. Antes de ativar a replicação, verifique se as máquinas cumprem [os requisitos](vmware-physical-azure-support-matrix.md#replicated-machines)de replicação .
4. Configure uma política de replicação para [VMware VMs](vmware-azure-set-up-replication.md#create-a-policy) ou [servidores físicos](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Ativar a replicação para [VMware VMs](vmware-azure-enable-replication.md) ou [servidores físicos](physical-azure-disaster-recovery.md#enable-replication). Isto dá início à replicação inicial para as máquinas selecionadas.

## <a name="monitor-your-deployment"></a>Monitorizar a implementação

Depois de iniciar a replicação para o primeiro lote de VMs, comece a monitorizar a sua implementação da seguinte forma:  

1. Designar um administrador de recuperação de desastres para monitorizar o estado de saúde das máquinas replicadas.
2. [Monitorize eventos](site-recovery-monitor-and-troubleshoot.md) para itens replicados e infraestruturas.
3. [Monitorize a saúde](vmware-physical-azure-monitor-process-server.md) dos seus servidores de processos de escala.
4. Inscreva-se para receber notificações por [e-mail para eventos,](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) para uma monitorização mais fácil.
5. Realizar exercícios regulares de recuperação de [desastres,](site-recovery-test-failover-to-azure.md)para garantir que tudo funcione como esperado.


## <a name="plan-for-large-scale-failovers"></a>Plano para falhas em larga escala

Em caso de desastre, poderá ter de falhar um grande número de máquinas/cargas de trabalho para o Azure. Prepare-se para este tipo de evento da seguinte forma.

Pode preparar-se antecipadamente para a falha da seguinte forma:

- [Prepare a sua infraestrutura e VMs](#plan-infrastructure-and-vm-connectivity) para que as suas cargas de trabalho estejam disponíveis após o failover, e para que os utilizadores possam aceder aos VMs Azure.
- Note os [limites](#failover-limits) de falha anteriores neste documento. Certifique-se de que as suas falhas se enquadram dentro destes limites.
- Faça [exercícios regulares](site-recovery-test-failover-to-azure.md)de recuperação de desastres. Os exercícios ajudam a:
    - Encontre lacunas na sua implantação antes de falhar.
    - Calcule o RTO de ponta a ponta para as suas apps.
    - Calcule o RPO de ponta a ponta para as suas cargas de trabalho.
    - Identifique conflitos de endereços IP.
    - Ao executar exercícios, recomendamos que não utilize redes de produção para exercícios, evite utilizar os mesmos nomes de sub-redes nas redes de produção e teste e limpe as falhas dos testes após cada broca.

Para executar uma falha em larga escala, recomendamos o seguinte:

1. Crie planos de recuperação para a falha na carga de trabalho.
    - Cada plano de recuperação pode desencadear falhas de até 100 máquinas.
    - [Saiba mais](recovery-plan-overview.md) sobre os planos de recuperação.
2. Adicione scripts de rcadbook da Azure Automation aos planos de recuperação, para automatizar quaisquer tarefas manuais no Azure. As tarefas típicas incluem configurar os equilibradores de carga, atualizar dNS etc. [Mais informações](site-recovery-runbook-automation.md)
2. Antes de falhar, prepare as máquinas Windows para que cumpram o ambiente Azure. [Os limites](#plan-azure-subscriptions-and-quotas) de failover são mais elevados para as máquinas que cumprem. [Saiba mais](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) sobre livros de corridas.
4.  Desencadear a falha com o [start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell cmdlet, juntamente com um plano de recuperação.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Monitorizar o Site Recovery](site-recovery-monitor-and-troubleshoot.md)
