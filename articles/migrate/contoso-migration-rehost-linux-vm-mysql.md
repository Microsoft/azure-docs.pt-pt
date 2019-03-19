---
title: Realojar uma aplicação de suporte técnico de serviço do Linux da Contoso para o Azure e MySQL do Azure | Documentos da Microsoft
description: Saiba como Contoso realojar uma aplicação do Linux no local através da migração para VMs do Azure e MySQL do Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 114f4ccccaa861928263eb59b4e43379989abcca
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077858"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Migração de Contoso: Volte a alojar uma aplicação do Linux no local nas VMs do Azure e no MySQL do Azure

Este artigo mostra como Contoso realojar a aplicação de suporte técnico seu serviço de Linux de duas camadas no local (osTicket), ao migrar para o Azure e MySQL do Azure.

Este documento é um de uma série de artigos que mostram como a empresa fictícia Contoso migra os respetivos recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e cenários que ilustram como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Cenários de crescem em complexidade. Vamos adicionar artigos adicionais ao longo do tempo.

**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Descrição geral da série de artigos, estratégia de migração da Contoso e as aplicações de exemplo que são utilizadas da série. | Disponível
[Artigo 2: Implementar a infraestrutura do Azure](contoso-migration-infrastructure.md) | Contoso prepara a sua infraestrutura no local e a respetiva infraestrutura do Azure para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avalie os recursos no local para migração para o Azure](contoso-migration-assessment.md)  | Contoso é executada uma avaliação da sua aplicação da SmartHotel360 no local em execução no VMware. Contoso avalia as VMs de aplicação com o serviço Azure Migrate e a base de dados de SQL Server do aplicativo com o Assistente de migração de dados. | Disponível
[Artigo 4: Realojar a uma aplicação numa VM do Azure e a instância gerida da base de dados do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso é executada uma migração lift-and-shift para o Azure para a sua aplicação da SmartHotel360 no local. Contoso migra a aplicação front-end VM com [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migra a base de dados de aplicação para uma instância gerida da base de dados SQL do Azure utilizando o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível   
[5 do artigo: Realojar a uma aplicação em VMs do Azure](contoso-migration-rehost-vm.md) | Contoso migra a sua aplicação da SmartHotel360 VMs para VMs do Azure com o serviço Site Recovery. | Disponível
[Artigo 6: Realojar a uma aplicação em VMs do Azure e, num grupo de Disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migra a aplicação da SmartHotel360. A Contoso utiliza o Site Recovery para migrar as VMs da aplicação. Ele usa o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server que está protegido por um grupo de Disponibilidade AlwaysOn. | Disponível 
[Artigo 7: Realojar uma aplicação do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | Contoso é uma migração lift-and-shift da aplicação de osTicket do Linux para VMs do Azure, com o Azure Site Recovery concluída | Disponível
Artigo 8: Realojar uma aplicação do Linux em VMs do Azure e MySQL do Azure | Contoso migra a aplicação de osTicket do Linux para VMs do Azure com o Azure Site Recovery e migra a base de dados de aplicação para uma instância do servidor MySQL do Azure com o MySQL Workbench. | Este artigo
[Artigo 9: Refatorizar uma aplicação na base de dados de aplicações Web do Azure e SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Contoso migra a aplicação da SmartHotel360 para uma aplicação Web do Azure e migra a base de dados de aplicação para uma instância de servidor SQL do Azure com o Assistente de migração de base de dados | Disponível
[Artigo 10: Refatorizar uma aplicação do Linux em aplicações Web do Azure e MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migra sua osTicket de aplicação do Linux para uma aplicação web do Azure em várias regiões do Azure utilizando o Gestor de tráfego do Azure, integrado com o GitHub para a entrega contínua. Contoso migra a base de dados de aplicação para uma base de dados do Azure para a instância do MySQL. | Disponível 
[Artigo 11: Refatorar o TFS em serviços do Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migra sua implantação do Team Foundation Server no local para serviços do Azure DevOps no Azure. | Disponível
[Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados do Azure SQL](contoso-migration-rearchitect-container-sql.md) | Contoso migra sua SmartHotel de aplicação para o Azure. Em seguida, rearchitects a camada de web de aplicação como um contentor do Windows em execução no Azure Service Fabric e base de dados com a base de dados do Azure SQL. | Disponível
[Artigo 13: Recriar uma aplicação no Azure](contoso-migration-rebuild.md) | Contoso recria o seu aplicativo de SmartHotel com uma variedade de capacidades do Azure e serviços, incluindo o serviço de aplicações do Azure, Azure Kubernetes Service (AKS), as funções do Azure, serviços cognitivos do Azure e Azure Cosmos DB. | Disponível
[Artigo 14: Dimensionar uma migração para o Azure](contoso-migration-scale.md) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível


Neste artigo, a Contoso migra uma aplicação de suporte técnico de serviço de Linux Apache MySQL PHP (LAMP) de duas camadas (osTicket) para o Azure. Se gostaria de utilizar esta aplicação de código-fonte aberto, pode transferi-lo a partir [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Fatores comerciais

A equipe de liderança de TI tem trabalhado em estreita colaboração com parceiros de negócios para compreender o que quer atingir:

- **Resolver o crescimento do negócio**: Contoso está a crescer e assim há pressão sobre a infra-estrutura e sistemas no local.
- **Limitar o risco**: O suporte técnico do serviço de aplicações é essencial para a empresa. Contoso deseja movê-lo para o Azure com zero risco.
- **Expandir**:  Contoso não quer alterar a aplicação neste momento. Simplesmente quer manter a aplicação estável.


## <a name="migration-goals"></a>Objetivos de migração

A equipe de cloud Contoso tiver afixado para baixo de objetivos para essa migração, para determinar o melhor método de migração:

- Após a migração, a aplicação no Azure deve ter as mesmas capacidades de desempenho, como sucede no respetivo ambiente de VMware no local.  A aplicação permanecerá como crítica na cloud, pois é no local. 
- Contoso não quer investir nesta aplicação.  É importante para os negócios, mas em sua forma atual Contoso simplesmente quero movê-la em segurança para a cloud.
- Tendo concluído duas migrações de aplicações do Windows, o Contoso quer aprender a utilizar uma infraestrutura baseada em Linux no Azure.
- Contoso quer minimizar as tarefas de administração de base de dados depois do aplicativo é movido para a cloud.

## <a name="proposed-architecture"></a>Arquitetura proposta

Neste cenário:

- A aplicação está em camadas em duas VMs (OSTICKETWEB e OSTICKETMYSQL).
- As VMs estão localizadas no anfitrião ESXi do VMware **contosohost1.contoso.com** (versão 6.5).
- O ambiente do VMware é gerido pelo vCenter Server 6.5 (**vcenter.contoso.com**), em execução numa VM.
- A Contoso tiver um datacenter no local (contoso-datacenter), com um controlador de domínio no local (**contosodc1**).
- A aplicação de camada de web OSTICKETWEB será migrada para uma VM de IaaS do Azure.
- A base de dados de aplicação será migrado para a base de dados do Azure para o serviço de MySQL PaaS.
- Uma vez que a Contoso está a migrar uma carga de trabalho de produção, irão residir os recursos no grupo de recursos de produção **ContosoRG**.
- Os recursos serão replicados para a região primária (E.U.A. Leste 2) e colocados na rede de produção (VNET-PROD-EUS2):
    - A web VM irá residir na sub-rede de front-end (PROD-FE-EUS2).
    - A instância de base de dados irá residir na sub-rede da base de dados (PROD-DB-EUS2).
- A base de dados de aplicação será migrado para o MySQL do Azure com as ferramentas do MySQL.
- As VMs no local no Centro de dados Contoso serão encerradas depois de terminar a migração.


![Arquitetura do cenário](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>Processo de migração

Contoso concluirá o processo de migração da seguinte forma:

Para migrar a VM da web:

1. Como primeiro passo, os Contoso configura o Azure e infraestrutura necessária para implementar o Site Recovery no local.
2. Depois de preparar os componentes do Azure e no local, a Contoso configura e permite a replicação para a VM de web.
3. Após a replicação de segurança operacional, a Contoso migra a VM ao failover para o Azure.

Para migrar a base de dados:

1. Contoso Aprovisiona uma instância do MySQL no Azure.
2. Contoso configura o MySQL workbench e efetua uma cópia de segurança da base de dados localmente.
3. Contoso, em seguida, restaure a base de dados da cópia de segurança local para o Azure.

![Processo de migração](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço orquestra e gere a migração e recuperação após desastre para VMs do Azure e VMs e servidores físicos no local.  | Durante a replicação para o Azure, são cobradas taxas de armazenamento do Azure.  VMs do Azure são criadas e incorrer em custos, quando ocorre a ativação pós-falha. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços e custos.
[Base de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/) | A base de dados baseia-se no motor do servidor MySQL de código-fonte aberto. Ele fornece uma Comunidade totalmente gerida e prontas para empresas base de dados do MySQL, como um serviço para desenvolvimento de aplicações e a implantação. 

 
## <a name="prerequisites"></a>Pré-requisitos

Eis o que a Contoso precisa de para este cenário.

**Requisitos** | **Detalhes**
--- | ---
**Subscrição do Azure** | Contoso criadas subscrições durante um artigo anterior. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se criar uma conta gratuita, será o administrador da sua subscrição e poderá executar todas as ações.<br/><br/> Se utilizar uma subscrição existente e não for o administrador, terá de trabalhar com o administrador para lhe atribuir permissões de proprietário ou contribuinte.<br/><br/> Se precisar de permissões mais granulares, reveja [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infraestrutura do Azure** | Contoso configurar a infraestrutura do Azure, conforme descrito em [infraestrutura do Azure para a migração](contoso-migration-infrastructure.md).<br/><br/> Saiba mais sobre específicas [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) requisitos para a recuperação de Site.
**Servidores no local** | O vCenter server no local deve estar a executar a versão 5.5, 6.0 ou 6.5<br/><br/> Um anfitrião ESXi a executar a versão 5.5, 6.0 ou 6.5<br/><br/> Um ou mais VMs de VMware em execução no anfitrião ESXi.
**VMs no local** | [Reveja os requisitos de VM do Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) que são suportadas para migração com o Site Recovery.<br/><br/> Certifique-se suportado [sistemas de ficheiros e armazenamento de Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> As VMs têm de cumprir [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Passos do cenário

Eis como administradores de Contoso irão concluir a migração:

> [!div class="checklist"]
> * **Passo 1: Preparar o Azure para o Site Recovery**: Criação de uma conta de armazenamento do Azure para armazenar dados replicados e criar um cofre dos serviços de recuperação.
> * **Passo 2: Preparar o VMware no local para o Site Recovery**: Preparar contas para instalação de deteção e o agente da VM e preparar a ligação para as VMs do Azure após a ativação pós-falha.
>   * **Passo 3: Aprovisionar a base de dados]**: No Azure, que Aprovisiona uma instância da base de dados MySQL do Azure.
> * **Passo 4: Replicar VMs**: Configurar o ambiente de origem e destino de recuperação de Site, configure uma política de replicação e iniciar a replicação de VMs para o armazenamento do Azure.
> * **Passo 5: Migrar a base de dados**: Se configuraram a migração com ferramentas do MySQL.
> * **Passo 6: Migrar as VMs com o Site Recovery**: Por último, eles executam uma ativação pós-falha de teste para se certificar de que está tudo a funcionar e, em seguida, execute uma ativação pós-falha completa para migrar as VMs para o Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Passo 1: Preparar o Azure para o serviço Site Recovery

A Contoso precisa de alguns componentes do Azure para o Site Recovery:

- A ativação pós-falha uma VNet em que recursos estão localizados. Contoso já criado a VNet durante [implementação de infraestrutura do Azure](contoso-migration-infrastructure.md)
- Uma nova conta de armazenamento do Azure para armazenar dados replicados. 
- Um cofre dos serviços de recuperação no Azure.

Os administradores de Contoso criar uma conta de armazenamento e cofre da seguinte forma:

1. Eles criam uma conta de armazenamento (**contosovmsacc20180528**) na região E.U.A. Leste 2.

   - A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.
   - Se utilizarem uma conta de fins gerais, com o armazenamento standard e replicação LRS.

     ![Armazenamento de recuperação de site](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Com a conta de armazenamento e de rede no local, que criar um cofre (ContosoMigrationVault) e colocá-la a **ContosoFailoverRG** grupo de recursos na região E.U.A. Leste 2 primária.

    ![Cofre dos Serviços de Recuperação](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) configurar o Azure para o Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Passo 2: Preparar o VMware no local para o Site Recovery

Os administradores da Contoso preparar a infraestrutura de VMware no local da seguinte forma:

- Criação de uma conta no vCenter server, para automatizar a deteção de VMS.
- Criação de uma conta que permite a instalação automática do serviço de mobilidade nas VMs de VMware que vão ser replicados.
- Eles preparam VMs no local, para que se possam ligar a VMs do Azure quando forem criadas após a migração.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a deteção automática

O Site Recovery precisa de ter acesso a servidores VMware para:

- Detetar automaticamente VMs. É necessária, pelo menos, uma conta só de leitura.
- Orquestrar a replicação, a ativação pós-falha e a reativação pós-falha. Precisa de uma conta que pode executar operações como criar e remover discos e Ativando a VMs.

Administradores de contoso, configure a conta da seguinte forma:

1. É possível criar uma função ao nível do vCenter.
2. Eles, em seguida, atribua essa função as permissões necessárias.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade tem de ser instalado em cada VM que a Contoso pretende migrar.

- Site Recovery pode fazer uma instalação push automática deste componente quando ativa a replicação para as VMs.
- Para a instalação automática. Recuperação de sites precisa de uma conta com permissões para aceder à VM. 
- Detalhes da conta foram inseridos durante a configuração de replicação. 
- A conta pode ser domínio ou conta local, desde que ele tenha permissões de instalação.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Após a ativação pós-falha para o Azure, a Contoso quer ser capaz de ligar às VMs do Azure. Para fazer isso, os administradores da Contoso precisam de fazer o seguinte:

- Para aceder através da internet, permitem SSH no Linux VM no local antes da migração.  Ubuntu isso pode ser concluído com o seguinte comando: **Sudo apt-get ssh instalar -y**.
- Após a ativação pós-falha, deverá verificar **diagnósticos de arranque** para ver uma captura de ecrã da VM.
- Se esta opção não funcionar, terá de verificar que a VM está em execução e revisá-los [sugestões de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) criar e atribuir uma função para a deteção automática.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) criar uma conta para a instalação push do serviço de mobilidade.


## <a name="step-3-provision-azure-database-for-mysql"></a>Passo 3: Aprovisionar a base de dados do Azure para MySQL

Os administradores da Contoso aprovisionar uma instância de base de dados MySQL na região E.U.A. Leste 2 primária.

1. No portal do Azure, é criar uma base de dados do Azure para MySQL recurso. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Se adicionam o nome **contosoosticket** para a base de dados do Azure. Eles adicionarem a base de dados para o grupo de recursos de produção **ContosoRG**e especificar credenciais para o mesmo.
3. A base de dados do MySQL no local é a versão 5.7, para que selecionarem esta versão para compatibilidade. Eles usam os tamanhos padrão, o que corresponder aos seus requisitos de base de dados.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. Para **opções de redundância da cópia de segurança**, optar por utilizar **Georredundante**. Esta opção permite-lhes restaurar a base de dados na sua região secundária do E.U.A. Central, se ocorrer uma falha. Só pode configurar esta opção quando aprovisionam a base de dados.

     ![Redundância](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. Na **VNET-PROD-EUS2** rede > **pontos finais de serviço**, adicionam um ponto final do serviço (uma sub-rede de base de dados) para o serviço do SQL.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. Depois de adicionar a sub-rede, eles criaram uma regra de rede virtual que permite o acesso a partir da sub-rede de base de dados na rede de produção.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>Passo 4: Replicar as VMs no local

Antes de eles podem migrar a VM de web para o Azure, os administradores da Contoso configurar e ativar a replicação.

### <a name="set-a-protection-goal"></a>Definir um objetivo de proteção

1. No cofre, sob o nome do cofre (ContosoVMVault) definir um objetivo de replicação (**introdução** > **Site Recovery** > **preparar a infraestrutura**.
2. Especifica que suas máquinas estão localizados no local, que são VMs do VMware, e que pretende replicar para o Azure.

    ![Objetivo da replicação](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

Para continuar, confirmarem o que eles completaram planeamento da implementação, selecionando **Sim, já o fiz**. Contoso são apenas a migrar uma única VM neste cenário e não precisa de planeamento da implementação.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Os administradores de contoso agora configurar o ambiente de origem. Para fazer isso, com um modelo OVF que implementam um servidor de configuração de recuperação de sites como uma elevada disponibilidade, no local a VM de VMware. Depois do servidor de configuração está a funcionar, eles registá-lo no cofre.

O servidor de configuração é executada um número de componentes:

- O componente de servidor de configuração que coordena as comunicações entre no local e o Azure e gere a replicação de dados.
- O servidor de processos atua como um gateway de replicação. Recebe dados de replicação; otimiza-os com a colocação em cache, compressão e encriptação; e envia-os para o armazenamento do Azure.
- O servidor de processos também instala o Serviço de Mobilidade nas VMs que pretende replicar, e efetua a deteção automática de VMs VMware no local.

Os administradores da Contoso fazê-lo da seguinte forma:


1. Eles transferiram o modelo OVF **preparar infraestrutura** > **origem** > **servidor de configuração**.
    
    ![Transferir OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. Importar o modelo para o VMware, para criar a VM e implemente a VM.

    ![Modelo OVF](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. Quando ativam a VM pela primeira vez, ele arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
4. Após a instalação terminar, iniciam sessão na VM como administrador. No primeiro início de sessão, a ferramenta de configuração do Azure Site Recovery é executado por predefinição.
5. Na ferramenta, eles especificam um nome a utilizar para registar o servidor de configuração no cofre.
6. A ferramenta verifica se a VM pode ligar ao Azure.
7. Depois da ligação é estabelecida, iniciam sessão para a subscrição do Azure. As credenciais têm de ter acesso ao Cofre no qual eles irá registar o servidor de configuração.

    ![Registar servidor de configuração](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. A ferramenta executa algumas tarefas de configuração e, em seguida, é reiniciada.
9. Iniciam sessão na máquina novamente e o Assistente de gestão de servidor de configuração é iniciado automaticamente.
10. No assistente, eles selecionam o NIC para receber o tráfego de replicação. Não é possível alterar esta definição depois de estar configurada.
11. Selecionar a subscrição, grupo de recursos e cofre no qual pretende registar o servidor de configuração.

    ![Cofre](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. Agora, transferir e instalar o servidor MySQL e o VMWare PowerCLI. 
13. Após a validação, eles especificam o FQDN ou endereço IP do vCenter server ou vSphere no anfitrião. Deixe a porta predefinida e especifique um nome amigável para o servidor vCenter.
14. Eles introduza a conta que o próprio criou para a deteção automática e as credenciais que o Site Recovery irá utilizar para instalar automaticamente o serviço de mobilidade. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. Após a conclusão do registo, no portal do Azure, verifique que o servidor de configuração e o servidor VMware estão listados no **origem** página no cofre. Deteção pode demorar 15 minutos ou mais. 
15. Com tudo instalado, o Site Recovery liga-se aos servidores VMware e Deteta as VMs.

### <a name="set-up-the-target"></a>Configurar o destino

Agora os administradores da Contoso introduza as definições de replicação de destino.

1. Na **preparar infraestrutura** > **destino**, eles selecionam as definições de destino.
2. Recuperação de sites verifica que existe uma conta de armazenamento do Azure e a rede no destino especificado.


### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Com a origem e destino, configurar, os administradores da Contoso estão prontos para criar uma política de replicação.

1. Na **preparar infraestrutura** > **definições de replicação** > **política de replicação** >  **criar e Associar**, criaram uma política **ContosoMigrationPolicy**.
2. Se utilizarem as predefinições:
    - **Limiar RPO**: Predefinição de 60 minutos. Este valor define com que frequência são criados pontos de recuperação. Será gerado um alerta se a replicação contínua exceder este limite.
    - **Retenção do ponto de recuperação**. Predefinição de 24 horas. Este valor especifica quanto tempo dura o período de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela.
    - **Frequência de instantâneos consistentes com a aplicação**. Padrão de uma hora. Este valor Especifica a frequência com que os instantâneos consistentes com aplicações são criados.
 
        ![Criar política de replicação](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração. 

    ![Associar a política de replicação](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**Precisa de mais ajuda?**

- Pode ler um passo a passo completa de todos estes passos [configurar a recuperação após desastre das VMs de VMware no local](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Instruções detalhadas estão disponíveis para ajudá-lo [configurar o ambiente de origem](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implementar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [configurar as definições de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sobre o agente convidado do Azure para Linux.

### <a name="enable-replication-for-the-web-vm"></a>Ativar a replicação para a VM de Web

Agora, os administradores da Contoso podem começar a replicar a **OSTICKETWEB** VM.

1. Na **replicar aplicação** > **origem** > **+ replicar** selecionarem as definições de origem.
2. Eles indicam que se deve ativar as máquinas virtuais e selecione as definições de origem, incluindo o servidor vCenter e o servidor de configuração.

    ![Ativar a replicação](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Agora, eles especificam as definições de destino. Estes incluem o grupo de recursos e rede em que a VM do Azure estarão localizada após a ativação pós-falha e a conta de armazenamento no qual serão armazenados os dados replicados. 

     ![Ativar a replicação](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Eles selecionam **OSTICKETWEB** para replicação. 

    ![Ativar a replicação](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. Nas propriedades da VM, eles selecionam a conta que deve ser utilizada para instalar automaticamente o serviço de mobilidade na VM.

     ![Serviço de Mobilidade](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. na **as definições de replicação** > **configurar as definições de replicação**, eles verificam a que a política de replicação correto é aplicado e selecione **ativar replicação**. O serviço de mobilidade será instalado automaticamente.
6.  Controlar o progresso de replicação em **tarefas**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.


**Precisa de mais ajuda?**

Pode ler um passo a passo completa de todos estes passos [ativar a replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>Passo 5: Migrar a base de dados

Os administradores da Contoso migrar a base de dados com cópia de segurança e restauro, com ferramentas do MySQL. Instalar o MySQL Workbench, cópia de segurança da base de dados do OSTICKETMYSQL e, em seguida, restaurá-la à base de dados do Azure para o servidor MySQL.

### <a name="install-mysql-workbench"></a>Instalar MySQL Workbench

1. Eles verificam a [pré-requisitos e downloads do MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Instalar o MySQL Workbench para Windows em conformidade com o [instruções de instalação](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. No Workbench do MySQL, eles criam uma ligação ao MySQL para OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. Eles exportar a base de dados como **osticket**, num arquivo autônomo local.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. Depois da base de dados foi feito o backup localmente, eles criaram uma ligação à base de dados do Azure para a instância do MySQL.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. Agora, podem importar (restauro) a base de dados na instância do MySQL do Azure, o independente do ficheiro. Um novo esquema (osticket) é criado para a instância.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>Passo 6: Migrar as VMs com o Site Recovery

Por fim, os administradores de Contoso executados uma rápida ativação pós-falha de teste e, em seguida, migre a VM.

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Executar uma ativação pós-falha de teste ajuda a verificar se tudo o que está a funcionar conforme esperado, antes da migração. 

1. Execute uma ativação pós-falha de teste para o ponto mais recente no tempo (**processado mais recentemente**).
2. Eles selecionam **encerrar a máquina antes de iniciar a ativação pós-falha**, para que o Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. 
3. Execuções de ativação pós-falha de teste: 

    - Uma verificação dos pré-requisitos é executado para certificar-se de que todas as condições necessárias para a migração estão em vigor.
    - A ativação pós-falha processa os dados, para que possa ser criada uma VM do Azure. Se for selecionado o último ponto de recuperação, é criado um ponto de recuperação a partir dos dados.
    - É criada uma VM do Azure com base nos dados processados no passo anterior.

3. Depois de concluída a ativação pós-falha, a réplica é apresentada VM do Azure no portal do Azure. Eles verificam-se ao que a VM tem um tamanho adequado, está ligada à rede certa, e que está em execução. 
4. Depois de verificar, eles limpar a ativação pós-falha e registram e guarde todas as observações.

### <a name="migrate-the-vm"></a>Migrar a VM

Para migrar a VM, os administradores da Contoso cria um plano de recuperação que inclui a VM em efetuar a ativação pós-falha do plano para o Azure.

1. Criar um plano e adicione **OSTICKETWEB** a ele.

    ![Plano de recuperação](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. Eles são executados uma ativação pós-falha no plano. Selecione o ponto de recuperação mais recente e especificar que o Site Recovery deve tentar encerrar a VM no local antes de acionar a ativação pós-falha. Pode seguir o progresso de ativação pós-falha **tarefas** página.

    ![Ativação pós-falha](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. Durante a ativação pós-falha, o servidor vCenter emite comandos para parar as duas VMs em execução no anfitrião ESXi.

    ![Ativação pós-falha](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. Após a ativação pós-falha, eles Certifique-se de que a VM do Azure é apresentado conforme esperado no portal do Azure.

    ![Ativação pós-falha](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. Depois de verificar a VM, eles concluírem a migração. Para a replicação para a VM e interrompe a faturação do Site Recovery para a VM.

    ![Ativação pós-falha](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) executar uma ativação pós-falha de teste. 
- [Saiba](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) como criar um plano de recuperação.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) efetuar a ativação pós-falha para o Azure.


### <a name="connect-the-vm-to-the-database"></a>Ligar a VM para a base de dados

Como a etapa final do processo de migração, os administradores da Contoso, atualize a cadeia de ligação da aplicação para apontar para a base de dados do Azure para MySQL. 

1. Eles tornam a uma ligação SSH à OSTICKETWEB VM utilizar Putty ou de outro cliente SSH. A VM é privada, de modo que ligar-se com o endereço IP privado.

    ![Ligar à base de dados](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![Ligar à base de dados](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. Eles atualizam as definições para que o **OSTICKETWEB** VM pode comunicar com o **OSTICKETMYSQL** base de dados. Atualmente a configuração é codificado com o endereço IP local 172.16.0.43.

    **Antes da atualização**
    
    ![Atualizar o IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **Após a atualização**
    
    ![Atualizar o IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![Atualizar o IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. Eles reiniciem o serviço com **systemctl reiniciar apache2**.

    ![Reiniciar](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Por fim, atualizam os registros DNS **OSTICKETWEB**, em um dos controladores de domínio Contoso.

    ![Atualizar DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração é concluída, os escalões de aplicação osTicket estão em execução em VMs do Azure.

Agora, a Contoso precisa de fazer o seguinte: 
- Remover as VMs do VMware no inventário do vCenter
- Remova as VMs no local das tarefas de cópia de segurança locais.
- Documentação de atualização interna Mostrar novas localizações e endereços IP. 
- Reveja todos os recursos que interagem com as VMs no local e atualizar as definições relevantes ou documentação para refletir a nova configuração.
- Contoso utilizado o serviço Azure Migrate com o mapeamento da dependência para avaliar o **OSTICKETWEB** VM para a migração. Agora eles devem remover os agentes (Microsoft Monitoring Agent/dependência Agent), que eles são instalados para essa finalidade, da VM.

## <a name="review-the-deployment"></a>Reveja a implementação

Com a aplicação agora em execução, Contoso tem totalmente operacionalizar e proteger sua infra-estrutura de novo.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso reveja a VM e a base de dados para determinar os problemas de segurança.

- Eles analisam os grupos de segurança de rede (NSGs) para a VM, para controlar o acesso. NSGs são utilizados para garantir que apenas o tráfego permitido para a aplicação pode passar.
- Ao considerar a proteção dos dados nos discos de VM com a encriptação de disco e o Cofre de chaves do Azure.
- Comunicação entre a instância VM e a base de dados não está configurada para SSL. Eles, terá de fazê-lo para se certificar de que o tráfego de base de dados não pode ser invadido por hackers.

[Leia mais](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) sobre práticas de segurança para as VMs.

### <a name="bcdr"></a>BCDR

Para a continuidade do negócio e recuperação após desastre, a Contoso usa as seguintes ações:

- **Manter os dados seguros**: Contoso efetua uma cópia dos dados na aplicação da VM com o serviço de cópia de segurança do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Eles não precisam de configurar a cópia de segurança da base de dados. Base de dados do Azure para MySQL automaticamente cria e armazena cópias de segurança do servidor. Optaram por fazer a utilizar a redundância geográfica para a base de dados, pelo que é resiliente e prontos para produção.
- **Manter as aplicações em funcionamento**: Contoso replica as VMs no Azure da aplicação para uma região secundária com o Site Recovery. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).


### <a name="licensing-and-cost-optimization"></a>Otimização de licenciamento e de custo

- Depois de implementar recursos, a Contoso atribui etiquetas do Azure, de acordo com as decisões que tomadas durante a [infraestrutura do Azure](contoso-migration-infrastructure.md#set-up-tagging) implementação.
- Não existem não existem problemas de licenciamento para os servidores de Contoso Ubuntu.
- Contoso irá ativar o Azure Cost Management licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gestão de custos multiclouds que o ajuda a utilizar e gerir o Azure e outros recursos da cloud.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Azure Cost Management.


## <a name="next-steps"></a>Passos Seguintes

Neste cenário, mostramos o cenário de realojamento final. Contoso migrou o VM da aplicação de osTicket do Linux no local de front-end para uma VM do Azure e migrados a base de dados de aplicação para uma instância do MySQL do Azure.

O próximo conjunto de tutoriais da série de migração, vamos mostrar-lhe como Contoso efetuada um conjunto mais complexo das migrações, envolvendo a refatoração de aplicação, em vez de migrações de migração lift-and-shift simples.
