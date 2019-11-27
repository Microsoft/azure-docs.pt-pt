---
title: Configurar a recuperação de desastre do SAP NetWeaver com o Azure Site Recovery
description: Este artigo descreve como configurar a recuperação de desastre para implantações de aplicativo do SAP NetWeaver usando o Azure Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 29b3e4af33702c75e92b5e36c5521d9af12b1013
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533856"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configurar a recuperação de desastre para uma implantação de aplicativo SAP NetWeaver de várias camadas

A maioria das implantações do SAP de tamanho grande e médio porte usam alguma forma de solução de recuperação de desastres. A importância das soluções de recuperação de desastres robustas e em teste aumentou conforme os principais processos de negócios são movidos para aplicativos como o SAP. O Azure Site Recovery foi testado e integrado aos aplicativos SAP. O Site Recovery excede os recursos da maioria das soluções de recuperação de desastre locais e a um TCO (custo total de propriedade) mais baixo do que as soluções concorrentes.

Com Site Recovery, você pode:
* **Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver Production que são executados localmente** , replicando componentes para o Azure.
* **Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver Production que são executados no Azure** replicando componentes para outro Datacenter do Azure.
* **Simplifique a migração na nuvem** usando site Recovery para migrar sua implantação do SAP para o Azure.
* **Simplifique as atualizações, os testes e** a criação de protótipos de projetos da SAP criando um clone de produção sob demanda para testar aplicativos SAP.

Este artigo descreve como proteger implantações de aplicativo do SAP NetWeaver usando o [Azure site Recovery](site-recovery-overview.md). O artigo aborda as práticas recomendadas para proteger uma implantação do SAP NetWeaver de três camadas no Azure, replicando para outro Datacenter do Azure usando Site Recovery. Ele descreve os cenários e as configurações com suporte e como executar failovers de teste (análise de recuperação de desastre) e failovers reais.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você sabe como realizar as seguintes tarefas:

* [Replicar uma máquina virtual no Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Criar uma rede de recuperação](site-recovery-azure-to-azure-networking-guidance.md)
* [Fazer um failover de teste para o Azure](azure-to-azure-walkthrough-test-failover.md)
* [Fazer um failover para o Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicação do SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Cenários suportados
Você pode usar Site Recovery para implementar uma solução de recuperação de desastre nos seguintes cenários:
* Sistemas SAP em execução em um datacenter do Azure que Replica para outro Datacenter do Azure (recuperação de desastre do Azure para o Azure). Para obter mais informações, consulte [arquitetura de replicação do Azure para o Azure](https://aka.ms/asr-a2a-architecture).
* Sistemas SAP em execução em servidores VMware (ou físicos) locais que são replicados para um site de recuperação de desastre em um datacenter do Azure (recuperação de desastre do VMware para o Azure). Este cenário requer alguns componentes adicionais. Para obter mais informações, consulte [arquitetura de replicação do VMware para o Azure](https://aka.ms/asr-v2a-architecture).
* Sistemas SAP em execução no Hyper-V local que são replicados para um site de recuperação de desastre em um datacenter do Azure (recuperação de desastre do Hyper-V para o Azure). Este cenário requer alguns componentes adicionais. Para obter mais informações, consulte [arquitetura de replicação do Hyper-V para o Azure](https://aka.ms/asr-h2a-architecture).

Neste artigo, usamos um cenário de recuperação de desastre do **Azure para o Azure** para demonstrar os recursos de recuperação de desastre do SAP do site Recovery. Como Site Recovery replicação não é específica do aplicativo, o processo descrito também deve se aplicar a outros cenários.

### <a name="required-foundation-services"></a>Serviços de base necessários
No cenário que discutimos neste artigo, os seguintes serviços de Fundação são implantados:
* Azure ExpressRoute ou gateway de VPN do Azure
* Pelo menos um Active Directory controlador de domínio e servidor DNS, em execução no Azure

Recomendamos que você estabeleça essa infraestrutura antes de implantar Site Recovery.

## <a name="reference-sap-application-deployment"></a>Fazer referência à implantação do aplicativo SAP

Essa arquitetura de referência mostra a execução do SAP NetWeaver em um ambiente do Windows no Azure com alta disponibilidade.  Essa arquitetura é implantada com tamanhos específicos de VM (máquina virtual) que podem ser alterados para atender às necessidades da sua organização.

![Diagrama de um padrão típico de implantação do SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Considerações sobre recuperação de desastre

Para a recuperação após desastre (DR), tem de ser capaz de fazer a ativação pós-falha para uma região secundária. Cada camada utiliza uma estratégia diferente para fornecer proteção de recuperação após desastre (DR).

#### <a name="vms-running-sap-web-dispatcher-pool"></a>VMs executando o pool de Dispatcher da Web SAP 
O componente Web Dispatcher é utilizado como um balanceador de carga para tráfego SAP entre os servidores de aplicações SAP. Para obter alta disponibilidade para o componente Web Dispatcher, Azure Load Balancer é usado para implementar a instalação do Dispatcher da Web paralela em uma configuração de Round Robin para a distribuição de tráfego HTTP (S) entre os expatchers da Web disponíveis no pool de balanceadores. Isso será replicado usando Azure Site Recovery (ASR) e scripts de automação serão usados para configurar o balanceador de carga na região de recuperação de desastre. 

#### <a name="vms-running-application-servers-pool"></a>VMs executando pool de servidores de aplicativos
Para gerir grupos de logon para servidores de aplicações ABAP, a transação de SMLG é usada. Ele usa a função dentro do servidor de mensagens dos serviços de Central de balanceamento de carga para distribuir a carga de trabalho entre o agrupamento de servidores de aplicações SAP para SAPGUIs e RFC tráfego. Isso será replicado usando Azure Site Recovery 

#### <a name="vms-running-sap-central-services-cluster"></a>VMs executando o cluster do SAP central Services
Esta arquitetura de referência é executada Central de serviços em VMs na camada de aplicativos. Os serviços Central é um potencial ponto único de falha (SPOF) quando implantado numa única VM — uma implementação típica quando a elevada disponibilidade não é um requisito.<br>

Para implementar uma solução de alta disponibilidade, é possível usar um cluster de disco compartilhado ou um cluster de compartilhamento de arquivos. Para configurar VMs para um cluster de disco compartilhado, use o cluster de failover do Windows Server. A testemunha em nuvem é recomendada como uma testemunha de quorum. 
 > [!NOTE]
 > O Azure Site Recovery não Replica a testemunha de nuvem, portanto, é recomendável implantar a testemunha de nuvem na região de recuperação de desastre.

Para dar suporte ao ambiente de cluster de failover, o [sios Datakeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) executa a função de volume compartilhado clusterizado replicando discos independentes pertencentes aos nós do cluster. O Azure não suporta nativamente discos partilhados e, portanto, requer soluções fornecidas pelo SIOS. 

Outra maneira de lidar com o clustering é implementar um cluster de compartilhamento de arquivos. O [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) modificou recentemente o padrão de implantação de serviços centrais para acessar os diretórios globais do/sapmnt por meio de um caminho UNC. No entanto, ainda é recomendável garantir que o compartilhamento UNC do/sapmnt seja altamente disponível. Isso pode ser feito na instância dos serviços centrais usando o cluster de failover do Windows Server com o SOFS (servidor de arquivos do Scale Out) e o recurso Espaços de Armazenamento Diretos (S2D) no Windows Server 2016. 
 > [!NOTE]
 > Atualmente Azure Site Recovery suporte apenas à replicação de ponto consistente de falha de máquinas virtuais usando espaços de armazenamento diretos e o nó passivo do SIOS datakeeper


## <a name="disaster-recovery-considerations"></a>Considerações sobre a recuperação após desastre

Você pode usar Azure Site Recovery para orquestrar o failover da implantação completa do SAP nas regiões do Azure.
Abaixo estão as etapas para configurar a recuperação de desastres 

1. Replicar máquinas virtuais 
2. Criar uma rede de recuperação
3.  Replicar um controlador de domínio
4.  Replicar a camada base de dados 
5.  Fazer uma ativação pós-falha de teste 
6.  Fazer uma ativação pós-falha 

Abaixo está a recomendação para a recuperação de desastre de cada camada usada neste exemplo. 

 **Camadas SAP** | **Recomendação**
 --- | ---
**Pool de Dispatcher da Web SAP** |  Replicar usando o site Recovery 
**Pool de servidores de aplicativos SAP** |  Replicar usando o site Recovery 
**Cluster de serviços centrais do SAP** |  Replicar usando o site Recovery 
**Máquinas virtuais do Active Directory** |  Replicação do Active Directory 
**Servidores de banco de dados SQL** |  Replicação do SQL Always on

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para iniciar a replicação de todas as máquinas virtuais do aplicativo SAP para o datacenter de recuperação de desastre do Azure, siga as orientações em [replicar uma máquina virtual para o Azure](azure-to-azure-walkthrough-enable-replication.md).


* Para obter orientação sobre como proteger Active Directory e DNS, consulte [proteger o Active Directory e](site-recovery-active-directory.md) o documento DNS.

* Para obter orientação sobre como proteger a camada de banco de dados em execução no SQL Server, consulte [proteger SQL Server](site-recovery-sql.md) documento.

## <a name="networking-configuration"></a>Configuração de rede

Se você usar um endereço IP estático, poderá especificar o endereço IP que você deseja que a máquina virtual execute. Para definir o endereço IP, vá para **configurações de computação e rede** > **placa de interface de rede**.

![Captura de tela que mostra como definir um endereço IP privado no painel Site Recovery placa de interface de rede](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Criando um plano de recuperação
Um plano de recuperação dá suporte ao sequenciamento de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Ao criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [criar um plano de recuperação usando site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionando máquinas virtuais a grupos de failover

1.  Crie um plano de recuperação adicionando o servidor de aplicativos, o Web Dispatcher e as VMs de serviços SAP central.
2.  Clique em ' Personalizar ' para agrupar as VMs. Por padrão, todas as VMs fazem parte do ' grupo 1 '.



### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Para que seus aplicativos funcionem corretamente, talvez seja necessário realizar algumas operações nas máquinas virtuais do Azure após o failover ou durante um failover de teste. Você pode automatizar algumas operações pós-failover. Por exemplo, você pode atualizar a entrada DNS e alterar associações e conexões adicionando scripts correspondentes ao plano de recuperação.


Você pode implantar os scripts de Azure Site Recovery usados com mais frequência em sua conta de automação clicando no botão ' implantar no Azure ' abaixo. Quando você estiver usando qualquer script publicado, certifique-se de seguir as orientações no script.

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de pré-ação a ' grupo 1 ' para failover do grupo de disponibilidade do SQL. Use o script ' ASR-SQL-FailoverAG ' publicado nos scripts de exemplo. Certifique-se de seguir as diretrizes no script e faça as alterações necessárias no script adequadamente.
2. Adicione um script de ação post para anexar um balanceador de carga nas máquinas virtuais com failover da camada da Web (grupo 1). Use o script ' ASR-AddSingleLoadBalancer ' publicado nos scripts de exemplo. Certifique-se de seguir as diretrizes no script e faça as alterações necessárias no script adequadamente.

![Plano de recuperação SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1.  Na portal do Azure, selecione o cofre dos serviços de recuperação.
2.  Selecione o plano de recuperação que você criou para aplicativos SAP.
3.  Selecione **Ativação Pós-falha de Teste**.
4.  Para iniciar o processo de failover de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5.  Quando o ambiente secundário estiver ativo, execute validações.
6.  Quando as validações forem concluídas, para limpar o ambiente de failover, selecione **limpar failover de teste**.

Para obter mais informações, consulte [failover de teste para o Azure no site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1.  Na portal do Azure, selecione o cofre dos serviços de recuperação.
2.  Selecione o plano de recuperação que você criou para aplicativos SAP.
3.  Selecione **Ativação pós-falha**.
4.  Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [failover em site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como criar uma solução de recuperação de desastre para implantações do SAP NetWeaver usando Site Recovery, consulte o white paper para download da [SAP NetWeaver: criando uma solução de recuperação de desastre com o Azure site Recovery](https://aka.ms/asr_sap). O white paper discute recomendações para várias arquiteturas SAP, lista aplicativos com suporte e tipos de VM para SAP no Azure e descreve as opções de plano de teste para sua solução de recuperação de desastres.
* Saiba mais sobre como [replicar outras cargas de trabalho](site-recovery-workload.md) usando site Recovery.
