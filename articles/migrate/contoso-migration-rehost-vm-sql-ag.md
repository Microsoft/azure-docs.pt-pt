---
title: Uma aplicação da Contoso de realojamento ao migrar para o grupo de disponibilidade de VMs do Azure e SQL Server AlwaysOn | Documentos da Microsoft
description: Saiba como Contoso realojar a uma aplicação no local ao migrar para o grupo de disponibilidade de VMs do Azure e SQL Server AlwaysOn
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 535ba0049e91e09de3d1dcf05fc8ede80ef403ef
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58088065"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-sql-server-alwayson-availability-group"></a>Migração de Contoso: Realojar a uma aplicação no local em VMs do Azure e grupo de Disponibilidade AlwaysOn do SQL Server

Este artigo demonstra como Contoso realojar a aplicação da SmartHotel360 no Azure. Contoso migra do front-end de aplicação VM para uma VM do Azure e a base de dados de aplicação para uma VM do Azure SQL Server, em execução num cluster de ativação pós-falha do Windows Server com gGroups de Disponibilidade AlwaysOn do SQL Server.

Este documento é um de uma série de artigos que mostram como a empresa fictícia Contoso migra recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e cenários que ilustram como configurar uma infraestrutura de migração, avaliação dos recursos no local para migração e execução de diferentes tipos de migrações. Cenários de crescem em complexidade. Vamos adicionar artigos adicionais ao longo do tempo.

**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Descrição geral da série de artigos, estratégia de migração da Contoso e as aplicações de exemplo que são utilizadas da série. | Disponível
[Artigo 2: Implementar a infraestrutura do Azure](contoso-migration-infrastructure.md) | Contoso prepara a sua infraestrutura no local e a respetiva infraestrutura do Azure para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avalie os recursos no local para migração para o Azure](contoso-migration-assessment.md)  | Contoso é executada uma avaliação da sua aplicação da SmartHotel360 no local em execução no VMware. Contoso avalia as VMs de aplicação com o serviço Azure Migrate e a base de dados de SQL Server do aplicativo com o Assistente de migração de dados. | Disponível
[Artigo 4: Realojar a uma aplicação numa VM do Azure e a instância gerida da base de dados do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso é executada uma migração lift-and-shift para o Azure para a sua aplicação da SmartHotel360 no local. Contoso migra a aplicação front-end VM com [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migra a base de dados de aplicação para uma instância gerida da base de dados SQL do Azure utilizando o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível   
[5 do artigo: Realojar a uma aplicação em VMs do Azure](contoso-migration-rehost-vm.md) | Contoso migra a sua aplicação da SmartHotel360 VMs para VMs do Azure com o serviço Site Recovery. | Disponível
Artigo 6: Realojar a uma aplicação em VMs do Azure e, num grupo de Disponibilidade AlwaysOn do SQL Server | Contoso migra a aplicação da SmartHotel360. A Contoso utiliza o Site Recovery para migrar as VMs da aplicação. Ele usa o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server que está protegido por um grupo de Disponibilidade AlwaysOn. | Este artigo
[Artigo 7: Realojar uma aplicação do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | Contoso é uma migração lift-and-shift da aplicação de osTicket do Linux para VMs do Azure, com o Azure Site Recovery concluída | Disponível
[Artigo 8: Realojar uma aplicação do Linux em VMs do Azure e o servidor MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migra a aplicação de osTicket do Linux para VMs do Azure com o Azure Site Recovery e migra a base de dados de aplicação para uma instância do servidor MySQL do Azure com o MySQL Workbench. | Disponível
[Artigo 9: Refatorizar uma aplicação na base de dados de aplicações Web do Azure e SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Contoso migra a aplicação da SmartHotel360 para uma aplicação Web do Azure e migra a base de dados de aplicação para uma instância de servidor SQL do Azure com o Assistente de migração de base de dados | Disponível
[Artigo 10: Refatorizar uma aplicação do Linux em aplicações Web do Azure e MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migra sua osTicket de aplicação do Linux para uma aplicação web do Azure em várias regiões do Azure utilizando o Gestor de tráfego do Azure, integrado com o GitHub para a entrega contínua. Contoso migra a base de dados de aplicação para uma base de dados do Azure para a instância do MySQL. | Disponível 
[Artigo 11: Refatorar o TFS em serviços do Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migra sua implantação do Team Foundation Server no local para serviços do Azure DevOps no Azure. | Disponível
[Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados do Azure SQL](contoso-migration-rearchitect-container-sql.md) | Contoso migra sua SmartHotel de aplicação para o Azure. Em seguida, rearchitects a camada de web de aplicação como um contentor do Windows em execução no Azure Service Fabric e base de dados com a base de dados do Azure SQL. | Disponível
[Artigo 13: Recriar uma aplicação no Azure](contoso-migration-rebuild.md) | Contoso recria o seu aplicativo de SmartHotel com uma variedade de capacidades do Azure e serviços, incluindo o serviço de aplicações do Azure, Azure Kubernetes Service (AKS), as funções do Azure, serviços cognitivos do Azure e Azure Cosmos DB. | Disponível
[Artigo 14: Dimensionar uma migração para o Azure](contoso-migration-scale.md) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível


Neste artigo, a Contoso migra a aplicação do Windows .NET SmartHotel360 de duas camadas em execução em VMs de VMware para o Azure. Se gostaria de utilizar esta aplicação, são fornecidas como código-fonte aberto e pode baixá-lo partir [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Fatores comerciais

A equipe de liderança de TI tem trabalhado em estreita colaboração com parceiros de negócios para compreender o que quer alcançar com esta migração:

- **Resolver o crescimento do negócio**: Contoso está a crescer e assim há pressão em sistemas no local e em infra-estrutura.
- **Aumentar a eficiência**: A Contoso precisa de remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários.  Necessidades de negócio IT velocidade e não desperdício de tempo ou dinheiro, entregando assim mais rapidamente sobre os requisitos dos clientes.
- **Aumente a agilidade**:  Contoso TI tem de ser mais reativos para com as necessidades do negócio. Tem de ser capaz de reagir mais rapidamente do que as alterações no marketplace, para ativar o sucesso numa economia global.  IT não pode obter na forma como ou, se tornar um bloqueador de negócios.
- **Dimensionamento**: À medida que a empresa cresce com êxito, TI da Contoso tem de fornecer sistemas que são capazes de aumentar ao ritmo mesmo.

## <a name="migration-goals"></a>Objetivos de migração

A equipe de cloud de Contoso tiver afixado para baixo de objetivos para essa migração. Essas metas foram utilizadas para determinar o melhor método de migração:

- Após a migração, a aplicação no Azure deve ter as mesmas capacidades de desempenho, como sucede no VMWare.  A aplicação permanecerá como crítica na cloud, pois é no local.
- Contoso não quer investir nesta aplicação.  É importante para os negócios, mas em sua forma atual Contoso simplesmente quero movê-la em segurança para a cloud.
- A base de dados no local para a aplicação tenha tido problemas de disponibilidade. Contoso pretende implementá-la no Azure como um cluster de elevada disponibilidade, com capacidades de ativação pós-falha.
- Contoso quer atualizar a partir da sua plataforma atual do SQL Server 2008 R2, para o SQL Server 2017.
- Contoso não quer utilizar uma base de dados do SQL do Azure para esta aplicação e está à procura de alternativas.


## <a name="solution-design"></a>Design da solução

Depois de fixar-se para baixo de seus objetivos e requisitos, a Contoso projeta e revisões de uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que será utilizada para a migração.

### <a name="current-architecture"></a>Arquitetura atual

- A aplicação está em camadas em duas VMs (WEBVM e SQLVM).
- As VMs estão localizadas no anfitrião ESXi do VMware **contosohost1.contoso.com** (versão 6.5)
- O ambiente do VMware é gerido pelo vCenter Server 6.5 (**vcenter.contoso.com**), em execução numa VM.
- A Contoso tiver um datacenter no local (contoso-datacenter), com um controlador de domínio no local (**contosodc1**).


### <a name="proposed-architecture"></a>Arquitetura proposta

Neste cenário:

- Contoso serão migrados do front-end de aplicação WEBVM para uma VM de IaaS do Azure.
    - O VM no Azure de front-end será implementado no grupo de recursos de ContosoRG (utilizado para recursos de produção).
    -  Este irá estar localizado na rede de produção do Azure (VNET-PROD-EUS2) na região e.u.a. Leste 2 primária.
- A base de dados de aplicação será migrado para uma VM do Azure SQL Server.
    - Vai estar localizada na rede de base de dados do Azure da Contoso (PROD-DB-EUS2) na região e.u.a. Leste 2 primária.
    - Será colocado num cluster de ativação pós-falha do Windows Server com dois nós, que utiliza o SQL Server grupos de Disponibilidade AlwaysOn.
    - No Azure, os dois nós de VM do SQL Server no cluster serão implementados no grupo de recursos de ContosoRG.
    - Os nós VM estarão localizados na rede de produção do Azure (VNET-PROD-EUS2) na região e.u.a. Leste 2 primária.
    - VMs executarão o Windows Server 2016 com o SQL Server 2017 Enterprise Edition. Contoso não tem licenças para este sistema operativo, para que este irá utilizar uma imagem no Azure Marketplace que fornece a licença como um custo adicional para seu compromisso de EA do Azure.
    - Para além dos nomes exclusivos, ambas as VMs utilizam as mesmas definições.
- Contoso irá implementar um balanceador de carga interno que escuta tráfego no cluster e o direciona para o nó de cluster adequado.
    - O Balanceador de carga interno será implantado na ContosoNetworkingRG (utilizado para recursos de rede).
- As VMs no local no Centro de dados Contoso serão encerradas depois de terminar a migração.

![Arquitetura do cenário](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="database-considerations"></a>Considerações sobre a base de dados

Como parte do processo de design da solução, a Contoso fez uma comparação de funcionalidades entre a base de dados do Azure SQL e SQL Server. As seguintes considerações ajudado a prepará-los para decidir ficar com uma VM de IaaS do Azure com o SQL Server:

 - Utilizar VM do Azure com o SQL Server parece ser uma solução ideal, se a Contoso precisa personalizar o sistema operativo ou o servidor de base de dados, ou se ele poderá querer colocalizar e executar aplicações de terceiros na mesma VM.
 - Utilizar o Assistente de migração de dados, Contoso pode facilmente avaliar e migrar para uma base de dados do SQL do Azure.
 

### <a name="solution-review"></a>Revisão de solução

Contoso avalia o design proposto ao juntar-se de uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Profissionais de TI** | WEBVM será movido para o Azure sem alterações, fazer a migração simples.<br/><br/> O escalão do SQL Server será executado no SQL Server 2017 e Windows Server 2016. Esta ação extingue seu atual sistema de operativo do Windows Server 2008 R2 e executar o SQL Server 2017 suporta os objetivos e requisitos técnicos da Contoso. IT fornece 100% de compatibilidade ao se afastar do SQL Server 2008 R2.<br/><br/> Contoso pode aproveitar seus investimentos em Software Assurance, com o benefício híbrido do Azure.<br/><br/> Uma implementação do SQL Server no Azure de elevada disponibilidade fornece tolerância a falhas para que a camada de dados de aplicação já não é um ponto único de ativação pós-falha.
**Contras** | WEBVM está a executar o Windows Server 2008 R2. O sistema operativo é suportado pelo Azure para funções específicas (Julho de 2018). [Saiba mais](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> A camada web da aplicação irá permanecer um ponto único de ativação pós-falha.</br><br/> Contoso tem de continuar a suportar a camada web como uma VM do Azure, em vez de mudar para um serviço gerido, como o serviço de aplicações do Azure.<br/><br/> Com a solução escolhida, Contoso será necessário continuar a gestão de duas VMs do SQL Server, em vez de mudar para uma plataforma gerida, tais como a instância gerida da base de dados SQL do Azure. Além disso, com o Software Assurance, Contoso poderia trocar suas licenças existentes para as tarifas com desconto na instância gerida da base de dados SQL do Azure.


### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Assistente de migração de base de dados](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA é executada localmente a partir da máquina do SQL Server no local e migra a base de dados numa VPN de site a site para o Azure. | DMA é uma ferramenta gratuita e transferível.
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Site Recovery orquestra e gere a migração e recuperação após desastre para VMs do Azure e VMs e servidores físicos no local.  | Durante a replicação para o Azure, são cobradas taxas de armazenamento do Azure.  VMs do Azure são criadas e incorrer em custos, quando ocorre a ativação pós-falha. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços e custos.

 

## <a name="migration-process"></a>Processo de migração

Os administradores da Contoso irão migrar as VMs da aplicação para o Azure.

- Estes irá migrar o VM de front-end para VM do Azure com o Site Recovery:
    - Como primeiro passo, eles irá preparar e configurar os componentes do Azure e preparar a infraestrutura de VMware no local.
    - Com tudo o que preparado, podem iniciar a VM a replicar.
    - Depois de ativar a replicação e a trabalhar, eles migrar a VM ao failover para o Azure.
- Estes irá migrar a base de dados para um cluster do SQL Server no Azure, utilizando o Assistente de dados de migração (DMA).
    - Como primeiro passo eles irá precisar de aprovisionar VMs do SQL Server no Azure, configurar o cluster e um balanceador de carga interno e configurar grupos de Disponibilidade AlwaysOn.
    - Com isso funcionando, eles podem migrar a base de dados
- Após a migração, eles irá ativar a proteção de AlwaysOn para a base de dados.

![Processo de migração](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Pré-requisitos

Eis o que a Contoso precisa de fazer para este cenário.

**Requisitos** | **Detalhes**
--- | ---
**Subscrição do Azure** | Contoso já criada uma subscrição num artigo antecipado nesta série. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se criar uma conta gratuita, será o administrador da sua subscrição e poderá executar todas as ações.<br/><br/> Se utilizar uma subscrição existente e não for o administrador, terá de trabalhar com o administrador para lhe atribuir permissões de proprietário ou contribuinte.<br/><br/> Se precisar de permissões mais granulares, reveja [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infraestrutura do Azure** | [Saiba como](contoso-migration-infrastructure.md) Contoso configurar uma infraestrutura do Azure.<br/><br/> Saiba mais sobre específicas [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) requisitos para a recuperação de Site.
**Recuperação de sites (no local)** | O vCenter server no local deve estar a executar a versão 5.5, 6.0 ou 6.5<br/><br/> Um anfitrião ESXi a executar a versão 5.5, 6.0 ou 6.5<br/><br/> Um ou mais VMs de VMware em execução no anfitrião ESXi.<br/><br/> As VMs têm de cumprir [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Suportado [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) configuração.<br/><br/> As VMs que pretende replicar tem de cumprir [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).



## <a name="scenario-steps"></a>Passos do cenário

Eis como Contoso será executada a migração:

> [!div class="checklist"]
> * **Passo 1: Preparar um cluster**: Crie um cluster para a implementação de dois nós de VM do SQL Server no Azure.
> * **Passo 2: Implementar e configurar o cluster**: Prepare um cluster de servidor SQL do Azure.  Bases de dados são migrados para este cluster previamente criada.
> * **Passo 3: Implementar o Balanceador de carga**: Implemente um balanceador de carga para balancear o tráfego para os nós do SQL Server.
> * **Passo 4: Preparar o Azure para o Site Recovery**: Crie uma conta de armazenamento do Azure para manter os dados replicados e um cofre dos serviços de recuperação. 
> * **Passo 5: Preparar o VMware no local para o Site Recovery**: Prepare contas para instalação de deteção e o agente VM. Preparar VMs no local para que os utilizadores podem ligar para VMs do Azure m; após a migração.
> * **Passo 6: Replicar VMs**: Ative a replicação de VM para o Azure.
> * **Passo 7: Instalar o DMA**: Baixe e instale o Assistente de migração de base de dados.
> * **Passo 7: Migrar a base de dados com o DMA**: Migre a base de dados para o Azure.
> * **Passo 9: Proteger a base de dados**: Crie um grupo de Disponibilidade AlwaysOn para o cluster.
> * **Passo 10: Migrar a aplicação web VM**: Execute uma ativação pós-falha de teste, para confirmar que está tudo a funcionar conforme o previsto. Em seguida, execute uma ativação pós-falha completa para o Azure. 


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Passo 1: Preparar um cluster de grupo de Disponibilidade AlwaysOn do SQL Server

Administradores de contoso como configurar o cluster da seguinte forma:

1. Eles criam duas VMs do SQL Server ao selecionar a imagem do SQL Server 2017 Enterprise Windows Server 2016 no Azure Marketplace. 

    ![SKU DE VM DO SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. Na **criar máquina virtual do assistente** > **Noções básicas**, eles configurarem:

    - Nomes para as VMs de mensagens em fila: **SQLAOG1** e **SQLAOG2**.
    - Uma vez que as máquinas são críticas para a empresa, permitem SSD para o tipo de disco da VM.
    - Especifica as credenciais da máquina.
    - Que implementam as VMs nos EUA Leste 2 principal região, no grupo de recursos de ContosoRG.

3. Na **tamanho**, começam com D2s_V3 SKU para ambas as VMs. Irá aumentar mais tarde, conforme eles precisam.
4. Na **definições**, que fazem o seguinte:

    - Uma vez que estas VMs são bases de dados críticos para a aplicação, que utilizam discos geridos.
    - Eles colocam as máquinas na rede de produção de E.U.A. Leste 2 principal região (**VNET-PROD-EUS2**), na sub-rede da base de dados (**PROD-DB-EUS2**).
    - Eles criam um novo conjunto de disponibilidade: **SQLAOGAVSET**, com dois domínios de falha e cinco domínios de atualização.

      ![VM do SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. Na **definições do SQL Server**, elas limitam a conectividade SQL para a rede virtual (privado), na porta 1433 predefinida. Para a autenticação que utilizam as mesmas credenciais que usam no local (**contosoadmin**).

    ![VM do SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Precisa de mais ajuda?**

- [Obtenha ajuda](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) aprovisionar uma VM do SQL Server.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) configurar VMs para diferentes SKUs do SQL Server.

## <a name="step-2-deploy-and-set-up-the-cluster"></a>Passo 2: Implementar e configurar o cluster

Eis como administradores de Contoso como configurar o cluster:

1. Eles configurar uma conta de armazenamento do Azure para atuar como o testemunho de nuvem.
2. Eles adicionar as VMs do SQL Server para o domínio do Active Directory no Centro de dados Contoso no local.
3. Eles criaram o cluster no Azure.
4. Configurar o testemunho de nuvem.
5. Por último, permitem que grupos de disponibilidade SQL Always On.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Configurar uma conta de armazenamento como testemunho de nuvem

Para configurar um testemunho de nuvem, a Contoso precisa de uma conta de armazenamento do Azure que irá conter o arquivo de blob usado para arbitragem de cluster. A mesma conta de armazenamento pode ser utilizada para configurar o testemunho de nuvem para múltiplos clusters. 

Os administradores da Contoso criar uma conta de armazenamento da seguinte forma:

1. Especifica um nome reconhecível para a conta (**contosocloudwitness**).
2. Que implementam uma conta de todas as finalidades geral, com o LRS.
3. A conta são colocados numa terceira região - Centro-Sul. Eles colocam-o fora da região primária e secundária, para que ela permanece disponível em caso de falha regional.
4. Eles colocá-lo no seu grupo de recursos que retém recursos de infraestrutura - **ContosoInfraRG**.

    ![Testemunho de nuvem](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. Quando eles criam a conta de armazenamento primária e chaves de acesso secundária são geradas para o mesmo. Eles precisam da chave de acesso primária para criar o testemunho de nuvem. A chave é apresentado sob o nome de conta de armazenamento > **chaves de acesso**.

    ![Chave de acesso](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Adicionar VMs do SQL Server para o domínio Contoso

1. Contoso adiciona SQLAOG1 e SQLAOG2 ao domínio contoso.com.
2. Em seguida, em cada VM instalarem o recurso de Cluster de ativação pós-falha do Windows e as ferramentas.

### <a name="set-up-the-cluster"></a>Como configurar o cluster

Antes de configurar o cluster, os administradores da Contoso tirar um instantâneo do disco do SO em cada máquina.

![instantâneo](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

1. Em seguida, executam um script que tem colocado em conjunto para criar o Cluster de ativação pós-falha do Windows.

    ![Criar cluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

2. Depois que tenha criado o cluster, eles Certifique-se de que as VMs aparecem como nós de cluster.

     ![Criar cluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Configurar o testemunho de nuvem

1. Os administradores de contoso configurar o testemunho de nuvem utilizando o **Assistente de configuração do quórum** no Gestor de clusters de ativação pós-falha.
2. No assistente devem selecionar para criar um testemunho de nuvem com a conta de armazenamento.
3. Após a configuração do testemunho de nuvem, no aparece no snap-in Gestor de clusters de ativação pós-falha.

    ![Testemunho de nuvem](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

### <a name="enable-sql-server-always-on-availability-groups"></a>Ativar grupos de disponibilidade Always On do SQL Server

Os administradores da Contoso agora podem ativar Always On:

1. No SQL Server Configuration Manager, permitem **grupos de Disponibilidade AlwaysOn** para o **SQL Server (MSSQLSERVER)** serviço.

    ![Ative o AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. Eles reiniciem o serviço para que as alterações entrem em vigor.

Com o AlwaysOn ativar, Contoso pode configurar o grupo de Disponibilidade AlwaysOn que irá proteger a base de dados da SmartHotel360.

**Precisa de mais ajuda?**

- [Leia sobre](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) testemunha e configurar uma conta de armazenamento para o mesmo para a cloud.
- [Obtenha instruções](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) para configurar um cluster e criar um grupo de disponibilidade.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Passo 3: Implementar o Balanceador de carga do Azure

Os administradores da Contoso agora pretendem implementar um balanceador de carga interno que fica à frente de nós do cluster. O Balanceador de carga escuta tráfego e o direciona para o nó apropriado.

![Balanceamento de carga](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

Eles criam o Balanceador de carga da seguinte forma:

1. No portal do Azure > **Networking** > **Balanceador de carga**, eles configurar um novo Balanceador de carga interno: **ILB-PROD-DB-EUS2-SQLAOG**.
2. Eles colocam o Balanceador de carga na rede de produção **VNET-PROD-EUS2**, na sub-rede da base de dados **PROD-DB-EUS2**.
3. Eles atribuir um endereço IP estático: 10.245.40.100.
4. Como um elemento de rede, que implementam o Balanceador de carga no grupo de recursos de rede **ContosoNetworkingRG**.

    ![Balanceamento de carga](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

Depois de implementar o Balanceador de carga interno, tem de configurá-lo. Criar um conjunto de endereços de back-end, configurar uma sonda de estado de funcionamento e configurar uma regra de balanceamento de carga.

### <a name="add-a-backend-pool"></a>Adicionar um conjunto de back-end

Para distribuir o tráfego para as VMs no cluster, os administradores da Contoso configurar um conjunto de endereços de back-end que contém os endereços IP das NICs para as VMs que irão receber o tráfego de rede do Balanceador de carga.

1. Nas definições de Balanceador de carga no portal, Contoso, adicione um conjunto de back-end: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. Eles associar o agrupamento de conjunto de disponibilidade SQLAOGAVSET. As VMs no conjunto de (**SQLAOG1** e **SQLAOG2**) são adicionados ao agrupamento.

    ![Conjunto back-end](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Os administradores da Contoso crie uma sonda de estado de funcionamento para que o Balanceador de carga pode monitorizar o estado de funcionamento da aplicação. A sonda dinamicamente adiciona ou remove as VMs da rotação de Balanceador de carga, com base em como eles respondem às verificações do Estado de funcionamento.

Eles criaram a sonda da seguinte forma: 

1. Nas definições de Balanceador de carga no portal, a Contoso cria uma sonda de estado de funcionamento: **SQLAlwaysOnEndPointProbe**.
2. Configuram a sonda para monitorizar as VMs na porta TCP 59999.
3. Eles defina um intervalo de 5 segundos entre sondas e um limiar de 2. Se as duas pesquisas falharem, a VM será considerada mau estado de funcionamento.

    ![Sonda](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Configurar o Balanceador de carga para receber o tráfego


Agora, os administradores da Contoso configure uma regra de Balanceador de carga para definir como o tráfego é distribuído pelas VMS.

- O endereço IP Front-end processa o tráfego de entrada.
- O conjunto IP de back-end recebe o tráfego.

Eles criaram a regra da seguinte forma:

1. Nas definições de Balanceador de carga no portal, eles adicionar uma nova regra de balanceamento de carga: **SQLAlwaysOnEndPointListener**.
2. Eles definir um serviço de escuta de front-end para receber o tráfego recebido do cliente SQL em TCP 1433.
3. Especifica o conjunto de back-end para o qual o tráfego será encaminhado e a porta em que as VMs escutam o tráfego.
4. Elas permitem flutuante (devolução direta do servidor) de IP. Isso é sempre necessário para o SQL AlwaysOn.

    ![Sonda](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Precisa de mais ajuda?**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) do Balanceador de carga do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) criar um balanceador de carga.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Passo 4: Preparar o Azure para o serviço Site Recovery

Aqui estão os componentes do Azure, a Contoso precisa de implementar o Site Recovery:

- Uma VNet na qual as VMs serão localizadas quando estiver a criar durante a ativação pós-falha.
- Uma conta de armazenamento do Azure para armazenar dados replicados. 
- Um cofre dos serviços de recuperação no Azure.

Os administradores da Contoso configurar estes da seguinte forma:

1.  Contoso já criada uma rede/sub-rede podem utilizar para o Site Recovery quando eles [implementado a infraestrutura do Azure](contoso-migration-rehost-vm-sql-ag.md).

    - A aplicação da SmartHotel360 é uma aplicação de produção e WEBVM será migrado para a rede de produção do Azure (VNET-PROD-EUS2) na região e.u.a. Leste 2 primária.
    - WEBVM será colocado no grupo de recursos ContosoRG, que é utilizado para os recursos de produção, e na sub-rede de produção (PROD-FE-EUS2).

2. Os administradores da Contoso criar uma conta de armazenamento do Azure (contosovmsacc20180528) na região primária.

    - Utilizam uma conta para fins gerais, com o armazenamento standard e replicação LRS.
    - A conta tem de ser na mesma região que o cofre.

      ![Armazenamento de recuperação de site](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Com a conta de armazenamento e de rede no local, agora, crie um cofre dos serviços de recuperação (**ContosoMigrationVault**) e colocá-la a **ContosoFailoverRG** grupo de recursos na região E.U.A. Leste 2 primária .

    ![Cofre dos Serviços de Recuperação](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) configurar o Azure para o Site Recovery.


## <a name="step-5-prepare-on-premises-vmware-for-site-recovery"></a>Passo 5: Preparar o VMware no local para o Site Recovery

Eis o que os administradores de Contoso preparar no local:

- Uma conta no vCenter server ou vSphere ESXi no anfitrião, para automatizar a deteção de VMS.
- Uma conta que permite a instalação automática do serviço de mobilidade em VMs de VMware que pretende replicar.
- No local as definições da VM, para que Contoso possam ligar à VM do Azure replicadas após a ativação pós-falha.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a deteção automática

O Site Recovery precisa de ter acesso a servidores VMware para:

- Detetar automaticamente VMs. 
- Orquestrar a replicação, a ativação pós-falha e a reativação pós-falha.
- É necessária, pelo menos, uma conta só de leitura. Precisa de uma conta que pode executar operações como criar e remover discos e Ativando a VMs.

Administradores de contoso, configure a conta da seguinte forma:

1. É possível criar uma função ao nível do vCenter.
2. Eles, em seguida, atribua essa função as permissões necessárias.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade tem de ser instalado em cada VM.

- Site Recovery pode fazer uma instalação push automática deste componente quando a replicação está ativada para a VM.
- Precisa de uma conta que pode utilizar o Site Recovery para aceder à VM para a instalação push. Esta conta é especificada quando configurou a replicação na consola do Azure.
- A conta pode ser de domínio ou local, com permissões para instalar na VM.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Após a ativação pós-falha, a Contoso quer ser capaz de ligar a VMs do Azure. Para tal, os administradores da Contoso faça o seguinte antes da migração:

1. Para ter acesso através da internet:

   - Ative o RDP na VM no local antes da ativação pós-falha
   - Certifique-se de que as regras TCP e UDP estão adicionadas para o **público** perfil.
   - Verifique se o RDP é permitido na **Firewall do Windows** > **aplicações permitidas** para todos os perfis.
 
2. Para ter acesso através de VPN de site a site, eles:

   - Ative o RDP na máquina no local.
   - Permitir RDP no **Firewall do Windows** -> **aplicações e funcionalidades permitidas**, para **domínio e privadas** redes.
   - Definir política de SAN do sistema operacional na VM no local para **OnlineAll**.

Além disso, quando executam uma ativação pós-falha que precisam verificar o seguinte:

- Não deve haver nenhuma atualização do Windows pendentes na VM quando acionar uma ativação pós-falha. Se existirem, os utilizadores não será possível iniciar sessão na VM, enquanto a atualização for concluída.
- Após a ativação pós-falha, pode verificar **diagnósticos de arranque** para ver uma captura de ecrã da VM. Se não funcionar, deve verificar que a VM está em execução e revisá-los [sugestões de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) criar e atribuir uma função para a deteção automática.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) criar uma conta para a instalação push do serviço de mobilidade.


## <a name="step-6-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Passo 6: Replicar VMs no local para o Azure com o Site Recovery

Para poder executar uma migração para o Azure, os administradores da Contoso tem de configurar e ativar a replicação.

### <a name="set-a-replication-goal"></a>Definir um objetivo de replicação

1. No cofre, sob o nome do cofre (ContosoVMVault) selecionarem um objetivo de replicação (**introdução** > **Site Recovery** > **preparar infraestrutura** .
2. Especifica que suas máquinas estão localizados no local, em execução no VMware e replicar para o Azure.

    ![Objetivo da replicação](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

Para continuar, o que precisam para confirmar que eles concluam planeamento da implementação, selecionando **Sim, já o fiz**. Neste cenário Contoso são a migração apenas de uma VM e não precisa de planeamento da implementação.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Os administradores da Contoso tem de configurar o seu ambiente de origem. Para fazer isso, eles transferir um modelo OVF e utilizá-lo para implementar o servidor de configuração de recuperação de sites como uma elevada disponibilidade, a VM de VMware no local. Depois do servidor de configuração está a funcionar, eles registá-lo no cofre.

O servidor de configuração é executada um número de componentes:

- O componente de servidor de configuração que coordena as comunicações entre no local e o Azure e gere a replicação de dados.
- O servidor de processos atua como um gateway de replicação. Recebe dados de replicação; otimiza-os com a colocação em cache, compressão e encriptação; e envia-os para o armazenamento do Azure.
- O servidor de processos também instala o Serviço de Mobilidade nas VMs que pretende replicar, e efetua a deteção automática de VMs VMware no local.

Os administradores da Contoso executam estes passos da seguinte forma:


1. No cofre, transferem o modelo OVF **preparar infraestrutura** > **origem** > **servidor de configuração**.
    
    ![Transferir OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. Eles importam o modelo para o VMware, para criar e implementar a VM.

    ![Modelo OVF](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. Quando ativam a VM pela primeira vez, ele arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
4. Após a instalação terminar, iniciam sessão na VM como administrador. No primeiro início de sessão, a ferramenta de configuração do Azure Site Recovery é executado por predefinição.
5. Na ferramenta, eles especificam um nome a utilizar para registar o servidor de configuração no cofre.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois da ligação é estabelecida, iniciam sessão para a subscrição do Azure. As credenciais têm de ter acesso ao cofre no qual pretende registar o servidor de configuração.

    ![Registar servidor de configuração](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reiniciada.
8. Iniciam sessão na máquina novamente e o Assistente de gestão de servidor de configuração é iniciado automaticamente.
9. No assistente, eles selecionam o NIC para receber o tráfego de replicação. Não é possível alterar esta definição depois de estar configurada.
10. Selecionar a subscrição, grupo de recursos e cofre no qual pretende registar o servidor de configuração.
        ![vault](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. Eles, em seguida, transferirem e instalar o servidor MySQL e o VMWare PowerCLI. 
11. Após a validação, eles especificam o FQDN ou endereço IP do vCenter server ou vSphere no anfitrião. Deixe a porta predefinida e especifique um nome amigável para o servidor vCenter.
12. Especifica a conta que o próprio criou para a deteção automática e as credenciais que são utilizadas para instalar automaticamente o serviço de mobilidade. Para máquinas Windows, a conta tem privilégios de administrador local nas VMs.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. Após a conclusão do registo, no portal do Azure, eles duplo verificam que o servidor de configuração e o servidor VMware estão listados no **origem** página no cofre. Deteção pode demorar 15 minutos ou mais. 
8. Site Recovery, em seguida, liga-se aos servidores VMware com as definições especificadas e Deteta as VMs.

### <a name="set-up-the-target"></a>Configurar o destino

Agora os administradores da Contoso, especifique as definições de replicação de destino.

1. Na **preparar infraestrutura** > **destino**, eles selecionam as definições de destino.
2. Recuperação de sites verifica que existe uma conta de armazenamento do Azure e a rede no destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Agora, os administradores da Contoso podem criar uma política de replicação.

1. Na **preparar infraestrutura** > **definições de replicação** > **política de replicação** >  **criar e Associar**, criaram uma política **ContosoMigrationPolicy**.
2. Se utilizarem as predefinições:
    - **Limiar RPO**: Predefinição de 60 minutos. Este valor define com que frequência são criados pontos de recuperação. Será gerado um alerta se a replicação contínua exceder este limite.
    - **Retenção do ponto de recuperação**. Predefinição de 24 horas. Este valor especifica quanto tempo dura o período de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela.
    - **Frequência de instantâneos consistentes com a aplicação**. Padrão de uma hora. Este valor Especifica a frequência com que os instantâneos consistentes com aplicações são criados.
 
        ![Criar política de replicação](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração. 

    ![Associar a política de replicação](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Ativar a replicação

Agora os administradores da Contoso podem iniciar a replicação WebVM.

1. Na **replicar aplicação** > **origem** > **+ replicar** selecionarem as definições de origem.
2. Eles indicam que se deve ativar VMs, selecione o servidor vCenter e o servidor de configuração.

    ![Ativar a replicação](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. Agora, eles especificam as definições de destino, incluindo o grupo de recursos e a VNet e a conta de armazenamento no qual serão armazenados os dados replicados.

     ![Ativar a replicação](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Selecione o WebVM para a replicação, verifica a política de replicação e permite a replicação. Site Recovery instala o serviço de mobilidade na VM quando a replicação está ativada.
 
    ![Ativar a replicação](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. Controlar o progresso de replicação em **tarefas**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.
5. Na **Essentials** no portal do Azure, podem ver a estrutura para as VMs a replicar para o Azure.

    ![Vista da infraestrutura](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Precisa de mais ajuda?**

- Pode ler um passo a passo completa de todos estes passos [configurar a recuperação após desastre das VMs de VMware no local](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Instruções detalhadas estão disponíveis para ajudá-lo [configurar o ambiente de origem](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implementar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [configurar as definições de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Pode saber mais sobre [ativar a replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-7-install-the-database-migration-assistant-dma"></a>Passo 7: Instalar o Assistente de migração de base de dados (DMA)

Os administradores da Contoso vão migrar a base de dados da SmartHotel360 para VM do Azure **SQLAOG1** com o DMA. Configuraram DMA da seguinte forma:

1. Eles transferirem a ferramenta a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) para a VM do SQL Server no local (**SQLVM**).
2. Eles executam a configuração (Downloadmigrationassistant) na VM.
3. Sobre o **concluir** página, selecione **iniciar Microsoft Data Migration Assistant** antes de concluir o assistente.

## <a name="step-8-migrate-the-database-with-dma"></a>Passo 8: Migrar a base de dados com o DMA

1. O DMA executarem uma nova migração - **SmartHotel**.
2. Eles selecionam os **tipo de servidor de destino** como **SQL Server em Azure Virtual Machines**. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. Nos detalhes de migração, adicione **SQLVM** que o servidor de origem, e **SQLAOG1** como destino. Especifica as credenciais para cada máquina.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. Eles criam uma partilha local para as base de dados e informações de configuração. Tem de estar acessível com acesso de escrita pela conta de serviço do SQL no SQLVM e SQLAOG1.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso seleciona os inícios de sessão que devem ser migrados e inicia a migração. Depois de concluído, o DMA mostra a migração como concluída com êxito.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. Eles Certifique-se de que a base de dados está em execução **SQLAOG1**.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

O DMS liga-se para a VM do SQL Server no local através de uma ligação de VPN de site a site entre o datacenter da Contoso e o Azure e, em seguida, migra a base de dados.

## <a name="step-7-protect-the-database-with-alwayson"></a>Passo 7: Proteger a base de dados com AlwaysOn

Com a base de dados de aplicação em execução no **SQLAOG1**, Contoso administradores agora podem proteger-o com grupos de Disponibilidade AlwaysOn. Configurar AlwaysOn com o SQL Management Studio e, em seguida, atribuir um serviço de escuta com o clustering do Windows. 

### <a name="create-an-alwayson-availability-group"></a>Criar um grupo de Disponibilidade AlwaysOn

1. No SQL Management Studio, clique em **sempre de elevada disponibilidade** para iniciar o **Assistente de novo grupo de disponibilidade**.
2. Na **especificar opções**, nome de grupo de disponibilidade **SHAOG**. Na **selecionar bases de dados**, selecionarem a base de dados da SmartHotel360.

    ![Grupo de Disponibilidade AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. Na **especificar réplicas**, adicione os dois nós SQL como réplicas de disponibilidade e configurá-los para fornecer ativação pós-falha automática com consolidação síncrona.

     ![Grupo de Disponibilidade AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. Configurar um serviço de escuta para o grupo (**SHAOG**) e a porta. O endereço IP do Balanceador de carga interno é adicionado como um endereço IP estático (10.245.40.100).

    ![Grupo de Disponibilidade AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. Na **selecionar sincronização de dados**, permitem o seeding automático. Com esta opção, o SQL Server cria automaticamente as réplicas secundárias para cada base de dados no grupo, para que a Contoso não tem de criar cópias de segurança e restaurar estes manualmente. Após a validação, é criado o grupo de disponibilidade.

    ![Grupo de Disponibilidade AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso Ocorreu um problema ao criar o grupo. Eles não estiverem a utilizar segurança integrada de Windows de diretório Active Directory e, portanto, tem de conceder permissões para o início de sessão do SQL para criar as funções de Cluster de ativação pós-falha do Windows.

    ![Grupo de Disponibilidade AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. Depois do grupo é criado, o Contoso pode vê-la no SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Configurar um serviço de escuta no cluster

Como uma última etapa na configuração da implementação do SQL, os administradores da Contoso configurar o Balanceador de carga interno como o serviço de escuta no cluster e traz o serviço de escuta online. Se utilizarem um script para fazer isso.

![Serviço de escuta do cluster](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Verificar a configuração

Com tudo configurado, o Contoso tem agora um grupo de disponibilidade funcional no Azure que utiliza a base de dados migrada. Os administradores verificar isto ao ligar ao balanceador de carga interno no SQL Management Studio.

![Ligue-se do ILB](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Precisa de mais ajuda?**
- Saiba como criar uma [grupo de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) e [serviço de escuta](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Manualmente [como configurar o cluster para utilizar o endereço IP do Balanceador de carga](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address).
- [Saiba mais](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) sobre como criar e utilizar a SAS.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Passo 8: Migrar a VM com o Site Recovery

Os administradores de contoso executados uma rápida ativação pós-falha de teste e, em seguida, migrar a VM.

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Executar uma ativação pós-falha de teste ajuda a garantir que está tudo a funcionar conforme esperado antes da migração. 

1. Execute uma ativação pós-falha de teste para o ponto mais recente no tempo (**processado mais recentemente**).
2. Eles selecionam **encerrar a máquina antes de iniciar a ativação pós-falha**, para que o Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. 
3. Execuções de ativação pós-falha de teste: 

    - Uma verificação dos pré-requisitos é executado para certificar-se de que todas as condições necessárias para a migração estão em vigor.
    - A ativação pós-falha processa os dados, para que possa ser criada uma VM do Azure. Se for selecionado o último ponto de recuperação, é criado um ponto de recuperação a partir dos dados.
    - É criada uma VM do Azure com base nos dados processados no passo anterior.

3. Depois de concluída a ativação pós-falha, a réplica é apresentada VM do Azure no portal do Azure. Eles verificam-se ao que a VM tem um tamanho adequado, está ligada à rede certa, e que está em execução. 
4. Depois de verificar, eles limpar a ativação pós-falha e registram e guarde todas as observações. 

### <a name="run-a-failover"></a>Executar uma ativação pós-falha

1. Depois de verificar que a ativação pós-falha de teste funcionou como esperado, os administradores da Contoso crie um plano de recuperação para a migração e adicione WEBVM ao plano.

     ![Plano de recuperação](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. Eles são executados uma ativação pós-falha no plano. Selecione o ponto de recuperação mais recente e especificar que o Site Recovery deve tentar encerrar a VM no local antes de acionar a ativação pós-falha.

    ![Ativação pós-falha](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Após a ativação pós-falha, eles Certifique-se de que a VM do Azure é apresentado conforme esperado no portal do Azure.

    ![Plano de recuperação](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Depois de verificar a VM no Azure, que concluir a migração para concluir o processo de migração, parar a replicação para a VM e parar a faturação do Site Recovery para a VM.

    ![Ativação pós-falha](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Atualizar a cadeia de ligação

Como a etapa final do processo de migração, os administradores da Contoso atualizar a cadeia de ligação da aplicação para apontar para a base de dados migrada em execução no serviço de escuta de SHAOG. Esta configuração irá ser alterada no WEBVM agora em execução no Azure.  Esta configuração está localizada na Web. config da aplicação ASP. 

1. Localize o ficheiro em C:\inetpub\SmartHotelWeb\web.config.  Altere o nome do servidor para refletir o FQDN do AOG: shaog.contoso.com.

    ![Ativação pós-falha](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. Depois de atualizar o arquivo e salvá-lo, de reiniciar o IIS no WEBVM. Eles fazerem isso usando o IISRESET /RESTART num prompt de comando.
2. Após o reinício do IIS, a aplicação está agora a utilizar a base de dados em execução no MI de SQL.


**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) executar uma ativação pós-falha de teste. 
- [Saiba](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) como criar um plano de recuperação.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) efetuar a ativação pós-falha para o Azure.

## <a name="clean-up-after-migration"></a>Limpar após a migração

Após a migração, a aplicação da SmartHotel360 está em execução numa VM do Azure e a base de dados da SmartHotel360 está localizada no cluster de SQL do Azure.

Agora, a Contoso precisa de concluir estes passos de limpeza:  

- Remova as VMs no local no inventário do vCenter.
- Remova as VMs das tarefas de cópia de segurança locais.
- Atualize a documentação interna para mostrar as novas localizações e os endereços IP para as VMs.
- Reveja todos os recursos que interagem com as VMs desativadas e atualizar as definições relevantes ou documentação para refletir a nova configuração.
- Adicione que as duas VMs novas (SQLAOG1 e SQLAOG2) devem ser adicionadas para sistemas de monitorização de produção.

## <a name="review-the-deployment"></a>Reveja a implementação

Com os recursos migrados no Azure, a Contoso precisa totalmente operacionalizar e proteger sua infra-estrutura de novo.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso analisa WEBVM de VMs do Azure, SQLAOG1 e SQLAOG2 para determinar os problemas de segurança. 

- A equipe analisa os grupos de segurança de rede (NSGs) para a VM controlar o acesso. NSGs são utilizados para garantir que apenas o tráfego permitido para a aplicação pode passar.
- A equipe considera a proteção dos dados no disco com o Azure Disk Encryption e o Cofre de chaves.
- A equipe deve avaliar a encriptação de dados transparente (TDE) e, em seguida, ativá-la na base de dados da SmartHotel360 em execução no novo AOG de SQL. [Saiba mais](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

[Leia mais](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) sobre práticas de segurança para as VMs.


## <a name="bcdr"></a>BCDR

 Para a continuidade do negócio e recuperação após desastre (BCDR), a Contoso usa as seguintes ações:
- Manter os dados seguros: Contoso efetua uma cópia dos dados em WEBVM, SQLAOG1 e VMs SQLAOG2 com o serviço de cópia de segurança do Azure. [Saiba mais.]
  (https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  - Contoso também irá aprender como utilizar o armazenamento do Azure para criar cópias de segurança do SQL Server diretamente ao armazenamento de Blobs. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-use-storage-sql-server-backup-restore).
  - Manter as aplicações em funcionamento: Contoso replica as VMs no Azure da aplicação para uma região secundária com o Site Recovery. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).


### <a name="licensing-and-cost-optimization"></a>Otimização de licenciamento e de custo

1. Contoso tem o licenciamento existentes para seus WEBVM e irá tirar partido do benefício híbrido do Azure.  Contoso converterá as VMs do Azure existentes para tirar partido destes preços.
2. Contoso irá ativar o Azure Cost Management licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gestão de custos multiclouds que o ajuda a utilizar e gerir o Azure e outros recursos da cloud.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Azure Cost Management. 

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso rehosted a aplicação da SmartHotel360 no Azure ao migrar o VM da front-end de aplicação para o Azure com o serviço Site Recovery. Contoso migradas a base de dados de aplicação para um cluster de SQL Server aprovisionado no Azure e o tiver protegido num grupo de Disponibilidade AlwaysOn do SQL Server.

## <a name="next-steps"></a>Passos Seguintes

No próximo artigo da série, vamos mostrar como Contoso realojar a aplicação de osTicket de suporte técnico do serviço em execução no Linux e implementados com a base de dados MySQL.


