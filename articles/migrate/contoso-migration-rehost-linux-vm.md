---
title: Migrar de realojamento e realojar a uma aplicação do Linux no local para VMs do Azure | Documentos da Microsoft
description: Saiba como Contoso realojar uma aplicação do Linux no local através da migração para VMs do Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 5f6cb29d8fb3fd1c3b75e18330e3ad2711a61aab
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444927"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Migração de Contoso: Realojar a uma aplicação do Linux no local nas VMS do Azure

Este artigo mostra como a Contoso é realojamento um aplicativo de suporte técnico de serviço baseado em Linux no local (**osTicket**), para VMs IaaS do Azure.

Este documento é um de uma série de artigos que documente como a empresa fictícia Contoso migra os respetivos recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e um conjunto de cenários que ilustram como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Cenários de crescem em complexidade. Vamos adicionar artigos adicionais ao longo do tempo.

**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Descrição geral da série de artigos, estratégia de migração da Contoso e as aplicações de exemplo que são utilizadas da série. | Disponível
[Artigo 2: Implementar a infraestrutura do Azure](contoso-migration-infrastructure.md) | Contoso prepara a sua infraestrutura no local e a respetiva infraestrutura do Azure para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avalie os recursos no local para migração para o Azure](contoso-migration-assessment.md)  | Contoso é executada uma avaliação da sua aplicação da SmartHotel360 no local em execução no VMware. Contoso avalia as VMs de aplicação com o serviço Azure Migrate e a base de dados de SQL Server do aplicativo com o Assistente de migração de dados. | Disponível
[Artigo 4: Realojar a uma aplicação numa VM do Azure e a instância gerida da base de dados do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso é executada uma migração lift-and-shift para o Azure para a sua aplicação da SmartHotel360 no local. Contoso migra a aplicação front-end VM com [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migra a base de dados de aplicação para uma instância gerida da base de dados SQL do Azure utilizando o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível   
[5 do artigo: Realojar a uma aplicação em VMs do Azure](contoso-migration-rehost-vm.md) | Contoso migra a sua aplicação da SmartHotel360 VMs para VMs do Azure com o serviço Site Recovery. | Disponível
[Artigo 6: Realojar a uma aplicação em VMs do Azure e, num grupo de Disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migra a aplicação da SmartHotel360. A Contoso utiliza o Site Recovery para migrar as VMs da aplicação. Ele usa o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server que está protegido por um grupo de Disponibilidade AlwaysOn. | Disponível 
Artigo 7: Realojar uma aplicação do Linux em VMs do Azure | Contoso é uma migração lift-and-shift da aplicação de osTicket do Linux para VMs do Azure, com o Azure Site Recovery concluída | Este artigo
[Artigo 8: Realojar uma aplicação do Linux em VMs do Azure e MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migra a aplicação de osTicket do Linux para VMs do Azure com o Azure Site Recovery e migra a base de dados de aplicação para uma instância do servidor MySQL do Azure com o MySQL Workbench. | Disponível
[Artigo 9: Refatorizar uma aplicação na base de dados de aplicações Web do Azure e SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Contoso migra a aplicação da SmartHotel360 para uma aplicação Web do Azure e migra a base de dados de aplicação para uma instância de servidor SQL do Azure com o Assistente de migração de base de dados | Disponível
[Artigo 10: Refatorizar uma aplicação do Linux em aplicações Web do Azure e MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migra sua osTicket de aplicação do Linux para uma aplicação web do Azure em várias regiões do Azure utilizando o Gestor de tráfego do Azure, integrado com o GitHub para a entrega contínua. Contoso migra a base de dados de aplicação para uma base de dados do Azure para a instância do MySQL. | Disponível 
[Artigo 11: Refatorar o TFS em serviços do Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migra sua implantação do Team Foundation Server no local para serviços do Azure DevOps no Azure. | Disponível
[Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados do Azure SQL](contoso-migration-rearchitect-container-sql.md) | Contoso migra sua SmartHotel de aplicação para o Azure. Em seguida, rearchitects a camada de web de aplicação como um contentor do Windows em execução no Azure Service Fabric e base de dados com a base de dados do Azure SQL. | Disponível
[Artigo 13: Recriar uma aplicação no Azure](contoso-migration-rebuild.md) | Contoso recria o seu aplicativo de SmartHotel com uma variedade de capacidades do Azure e serviços, incluindo o serviço de aplicações do Azure, Azure Kubernetes Service (AKS), as funções do Azure, serviços cognitivos do Azure e Azure Cosmos DB. | Disponível
[Artigo 14: Dimensionar uma migração para o Azure](contoso-migration-scale.md) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível



Neste artigo, Contoso serão migrados duas fileiras **osTicket** aplicação, em execução no Linux Apache MySQL PHP (LAMP) para o Azure. As VMs da aplicação serão migrada utilizando o serviço Azure Site Recovery. Se gostaria de utilizar esta aplicação de código-fonte aberto, pode transferi-lo a partir [GitHub](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Fatores comerciais

A equipe de liderança de TI tem trabalhado em estreita colaboração com parceiros de negócios para compreender o que quer alcançar com esta migração:

- **Resolver o crescimento do negócio**: Contoso está a crescer e assim há pressão sobre a infra-estrutura e sistemas no local.
- **Limitar o risco**: O suporte técnico do serviço de aplicações é essencial para a empresa Contoso. Contoso deseja movê-lo para o Azure com zero risco.
- **Expandir**:  Contoso não quer alterar a aplicação neste momento. Simplesmente quer Certifique-se de que a aplicação está estável.


## <a name="migration-goals"></a>Objetivos de migração

A equipe de cloud Contoso tiver afixado para baixo de objetivos para essa migração, para determinar o melhor método de migração:

- Após a migração, a aplicação no Azure deve ter as mesmas capacidades de desempenho, como sucede no respetivo ambiente de VMWare no local.  A aplicação permanecerá como crítica na cloud, pois é no local. 
- Contoso não quer investir nesta aplicação.  É importante para os negócios, mas em sua forma atual Contoso simplesmente quer movê-lo com segurança para a cloud.
- Contoso não quer alterar o modelo de ops para esta aplicação. Quer interagir com a aplicação na cloud da mesma forma que eles fazem agora.
- Contoso não quer alterar a funcionalidade de aplicação. Apenas a localização da aplicação serão alterados.
- Tendo concluído duas migrações de aplicações do Windows, o Contoso quer aprender a utilizar uma infraestrutura baseada em Linux no Azure.


## <a name="solution-design"></a>Design da solução

Depois de fixar-se para baixo de objetivos e requisitos, a Contoso projeta e rever uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que Contoso irá utilizar para a migração.

### <a name="current-app"></a>Aplicação atual

- A aplicação de OSTicket é a em camadas em duas VMs (**OSTICKETWEB** e **OSTICKETMYSQL**).
- As VMs estão localizadas no anfitrião ESXi do VMware **contosohost1.contoso.com** (versão 6.5).
- O ambiente do VMware é gerido pelo vCenter Server 6.5 (**vcenter.contoso.com**), em execução numa VM.
- A Contoso tiver um datacenter no local (**contoso-datacenter**), com um controlador de domínio no local (**contosodc1**)

### <a name="proposed-architecture"></a>Arquitetura proposta

- Uma vez que a aplicação é uma carga de trabalho de produção, as VMs no Azure irão residir no grupo de recursos de produção **ContosoRG**.
- As VMs serão migradas para a região primária (E.U.A. Leste 2) e colocadas na rede de produção (VNET-PROD-EUS2):
    - A web VM irá residir na sub-rede de front-end (PROD-FE-EUS2).
    - A base de dados VM irá residir na sub-rede da base de dados (PROD-DB-EUS2).
- As VMs no local no Centro de dados Contoso serão encerradas depois de terminar a migração.

![Arquitetura do cenário](./media/contoso-migration-rehost-linux-vm/architecture.png) 

### <a name="solution-review"></a>Revisão de solução

Contoso avalia a estrutura proposta ao juntar-se de uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Profissionais de TI** | Ambas as VMs de aplicação serão movidas para o Azure sem alterações, fazer a migração simples.<br/><br/> Uma vez que a Contoso está usando a migração lift-and-shift para ambas as VMs de aplicação, nenhuma ferramenta de configuração ou migração especial é necessárias para a base de dados de aplicação.<br/><br/> Contoso irão manter o controlo total da aplicação VMs no Azure. </br>/ br > as VMs da aplicação estiver a executar o Ubuntu 16.04-TLS, que é uma distribuição do Linux apoiada. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).
**Contras** | A camada web e os dados da aplicação irá permanecer um ponto único de ativação pós-falha. <br/><br/> Contoso tem de continuar a suportar a aplicação como VMs do Azure, em vez de mudar para um serviço gerido, como o serviço de aplicações do Azure e base de dados do Azure para MySQL.<br/><br/> Contoso está ciente de que ao manter as coisas simples com uma migração de VM de migração lift-and-shift, eles estiverem não tirar total partido das funcionalidades fornecidas pelo [base de dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/overview) (incorporado de elevada disponibilidade, desempenho previsível, dimensionamento simples, cópias de segurança automáticas e segurança incorporadas).

### <a name="migration-process"></a>Processo de migração

Contoso serão migrados da seguinte forma:

1. Como primeiro passo, os Contoso configura o Azure e infraestrutura necessária para implementar o Site Recovery no local.
2. Depois de preparar os componentes do Azure e no local, a Contoso configura e permite a replicação para as VMs.
3. Após a replicação está a funcionar, o Contoso migra as VMs por pós-falha das mesmas para o Azure.

![Processo de migração](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço orquestra e gere a migração e recuperação após desastre para VMs do Azure e VMs e servidores físicos no local.  | Durante a replicação para o Azure, são cobradas taxas de armazenamento do Azure.  VMs do Azure são criadas e incorrer em custos, quando ocorre a ativação pós-falha. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços e custos.

 
## <a name="prerequisites"></a>Pré-requisitos

Eis o que a Contoso precisa de para este cenário.

**Requisitos** | **Detalhes**
--- | ---
**Subscrição do Azure** | Contoso criadas subscrições num artigo desta série antecipado. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se criar uma conta gratuita, será o administrador da sua subscrição e poderá executar todas as ações.<br/><br/> Se utilizar uma subscrição existente e não for o administrador, terá de trabalhar com o administrador para lhe atribuir permissões de proprietário ou contribuinte.<br/><br/> Se precisar de permissões mais granulares, reveja [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infraestrutura do Azure** | Contoso configurar a sua infraestrutura do Azure, conforme descrito em [infraestrutura do Azure para a migração](contoso-migration-infrastructure.md).<br/><br/> Saiba mais sobre específicas [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) requisitos para a recuperação de Site.
**Servidores no local** | O vCenter server no local deve estar a executar a versão 5.5, 6.0 ou 6.5<br/><br/> Um anfitrião ESXi a executar a versão 5.5, 6.0 ou 6.5<br/><br/> Um ou mais VMs de VMware em execução no anfitrião ESXi.
**VMs no local** | [Rever máquinas Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) que são suportadas para migração com o Site Recovery.<br/><br/> Certifique-se suportado [sistemas de ficheiros e armazenamento de Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> As VMs têm de cumprir [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Passos do cenário

Eis como Contoso irá concluir a migração:

> [!div class="checklist"]
> * **Passo 1: Preparar o Azure para o Site Recovery**: Contoso cria uma conta de armazenamento do Azure para armazenar dados replicados e cria um cofre dos serviços de recuperação.
> * **Passo 2: Preparar o VMware no local para o Site Recovery**: Contoso prepara contas para ser utilizado para a instalação de deteção e o agente da VM e que prepara para ligar a VMs do Azure após a ativação pós-falha.
> * **Passo 3: Replicar VMs**: Contoso configura o ambiente de migração de origem e destino, cria uma política de replicação e começa a replicar VMs para o armazenamento do Azure.
> * **Passo 4: Migrar as VMs com o Site Recovery**: Contoso é executada uma ativação pós-falha de teste para se certificar-se de que tudo está funcionando e, em seguida, executa uma ativação pós-falha completa para migrar as VMs para o Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Passo 1: Preparar o Azure para o serviço Site Recovery

A Contoso precisa de alguns componentes do Azure para o Site Recovery:

- Uma nova conta de armazenamento do Azure para armazenar dados replicados. 
- Um cofre dos serviços de recuperação no Azure.
- A ativação pós-falha uma VNet em que recursos estão localizados. Contoso já criado a VNet durante [implementação de infraestrutura do Azure](contoso-migration-infrastructure.md), por isso, basta que criar uma conta de armazenamento e o cofre.

1. Os administradores da Contoso criar uma conta de armazenamento do Azure (contosovmsacc20180528) na região E.U.A. Leste 2.

    - A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.
    - Se utilizarem uma conta de fins gerais, com o armazenamento standard e replicação LRS.

      ![Armazenamento de recuperação de site](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Com a conta de armazenamento e de rede no local, que criar um cofre (ContosoMigrationVault) e colocá-la a **ContosoFailoverRG** grupo de recursos na região E.U.A. Leste 2 primária.

    ![Cofre dos Serviços de Recuperação](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) configurar o Azure para o Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Passo 2: Preparar o VMware no local para o Site Recovery

Os administradores da Contoso preparar a infraestrutura de VMware no local da seguinte forma:

- Criação de uma conta no vCenter server ou vSphere ESXi no anfitrião, para automatizar a deteção de VMS.
- Criação de uma conta que permite a instalação automática do serviço de mobilidade em VMs de VMware que pretende replicar.
- Eles preparam VMs no local, para que se possam ligar a VMs do Azure quando forem criadas após a migração.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a deteção automática

O Site Recovery precisa de ter acesso a servidores VMware para:

- Detetar automaticamente VMs. É necessária, pelo menos, uma conta só de leitura.
- Orquestrar a replicação, a ativação pós-falha e a reativação pós-falha. Precisa de uma conta que pode executar operações como criar e remover discos e Ativando a VMs.

Administradores de contoso, configure a conta da seguinte forma:

1. É possível criar uma função ao nível do vCenter.
2. Essa função que atribuir as permissões necessárias.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade tem de ser instalado nas VMs do Linux que será migrado.

- Site Recovery pode fazer uma instalação push automática deste componente quando a replicação está ativada para as VMs.
- Para instalação push automática do, eles precisam preparar uma conta que o Site Recovery irá utilizar para aceder as VMs.
- Detalhes de contas foram inseridos durante a configuração de replicação. 
- A conta pode ser de domínio ou conta local, com permissões para instalar em VMs.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Após a ativação pós-falha para o Azure, a Contoso quer ser capaz de ligar as VMs replicadas no Azure. Para fazer isso, há algumas coisas que os administradores de Contoso precisam de fazer:

- Para aceder a VMs do Azure através da internet, permitem SSH no Linux VM no local antes da migração.  Ubuntu isso pode ser concluído com o seguinte comando: **Sudo apt-get ssh instalar -y**.
- Depois de executarem a migração (ativação pós-falha), pode verificar **diagnósticos de arranque** para ver uma captura de ecrã da VM.
- Se não funcionar, precisam de verificar se a VM está em execução e revisá-los [sugestões de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) criar e atribuir uma função para a deteção automática.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) criar uma conta para a instalação push do serviço de mobilidade.


## <a name="step-3-replicate-the-on-premises-vms"></a>Passo 3: Replicar as VMs no local

Antes de eles podem migrar a VM de web para o Azure, os administradores da Contoso configurar e ativar a replicação.

### <a name="set-a-protection-goal"></a>Definir um objetivo de proteção

1. No cofre, sob o nome do cofre (ContosoVMVault) definir um objetivo de replicação (**introdução** > **Site Recovery** > **preparar a infraestrutura**.
2. Especifica que suas máquinas estão localizados no local, que são VMs do VMware, e que pretende replicar para o Azure.
    ![Objetivo de replicação](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

Para continuar, confirmarem o que eles completaram planeamento da implementação, selecionando **Sim, já o fiz**. Contoso são apenas a migrar uma única VM neste cenário e não precisa de planeamento da implementação.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Os administradores da Contoso agora tem de configurar o ambiente de origem. Para fazer isso, eles transferir um modelo OVF e utilizá-lo para implementar o servidor de configuração de recuperação de sites como uma elevada disponibilidade, a VM de VMware no local. Depois do servidor de configuração está a funcionar, eles registá-lo no cofre.

O servidor de configuração é executada um número de componentes:

- O componente de servidor de configuração que coordena as comunicações entre no local e o Azure e gere a replicação de dados.
- O servidor de processos atua como um gateway de replicação. Recebe dados de replicação; otimiza-os com a colocação em cache, compressão e encriptação; e envia-os para o armazenamento do Azure.
- O servidor de processos também instala o Serviço de Mobilidade nas VMs que pretende replicar, e efetua a deteção automática de VMs VMware no local.

Os administradores da Contoso executam estes passos da seguinte forma:

1. Eles transferiram o modelo OVF **preparar infraestrutura** > **origem** > **servidor de configuração**.
    
    ![Transferir OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. Importar o modelo para o VMware, para criar a VM e implemente a VM.

    ![Modelo OVF](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. Quando ativam a VM pela primeira vez, ele arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
4. Após a instalação terminar, iniciam sessão na VM como administrador. No primeiro início de sessão, a ferramenta de configuração do Azure Site Recovery é executado por predefinição.
5. Na ferramenta, eles especificam um nome a utilizar para registar o servidor de configuração no cofre.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois da ligação é estabelecida, iniciam sessão para a subscrição do Azure. As credenciais têm de ter acesso ao cofre no qual pretende registar o servidor de configuração.

    ![Registar servidor de configuração](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reiniciada.
8. Iniciam sessão na máquina novamente e o Assistente de gestão de servidor de configuração é iniciado automaticamente.
9. No assistente, eles selecionam o NIC para receber o tráfego de replicação. Não é possível alterar esta definição depois de estar configurada.
10. Selecionar a subscrição, grupo de recursos e cofre no qual pretende registar o servidor de configuração.

    ![Cofre](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. Eles, em seguida, transferirem e instalar o servidor MySQL e o VMWare PowerCLI. 
12. Após a validação, eles especificam o FQDN ou endereço IP do vCenter server ou vSphere no anfitrião. Deixe a porta predefinida e especifique um nome amigável para o servidor vCenter.
13. Especifica a conta que o próprio criou para a deteção automática e as credenciais que devem ser utilizadas para instalar automaticamente o serviço de mobilidade.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. Após a conclusão do registo, no portal do Azure, verifique que o servidor de configuração e o servidor VMware estão listados no **origem** página no cofre. Deteção pode demorar 15 minutos ou mais. 
15. Site Recovery, em seguida, liga-se aos servidores VMware e Deteta as VMs.

### <a name="set-up-the-target"></a>Configurar o destino

Agora, os administradores da Contoso configurar as definições de replicação de destino.

1. Na **preparar infraestrutura** > **destino**, eles selecionam as definições de destino.
2. Recuperação de sites verifica que existe uma conta de armazenamento do Azure e a rede no destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Depois de origem e de destino estão configurados, está prontos para criar uma política de replicação.

1. Na **preparar infraestrutura** > **definições de replicação** > **política de replicação** >  **criar e Associar**, criaram uma política **ContosoMigrationPolicy**.
2. Se utilizarem as predefinições:
    - **Limiar RPO**: Predefinição de 60 minutos. Este valor define com que frequência são criados pontos de recuperação. Será gerado um alerta se a replicação contínua exceder este limite.
    - **Retenção do ponto de recuperação**. Predefinição de 24 horas. Este valor especifica quanto tempo dura o período de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela.
    - **Frequência de instantâneos consistentes com a aplicação**. Padrão de uma hora. Este valor Especifica a frequência com que os instantâneos consistentes com aplicações são criados.
 
        ![Criar política de replicação](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração. 

    ![Associar a política de replicação](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Precisa de mais ajuda?**

- Pode ler um passo a passo completa de todos estes passos [configurar a recuperação após desastre das VMs de VMware no local](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Instruções detalhadas estão disponíveis para ajudá-lo [configurar o ambiente de origem](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implementar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [configurar as definições de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sobre o agente convidado do Azure para Linux.

**Precisa de mais ajuda?**

- Pode ler um passo a passo completa de todos estes passos [configurar a recuperação após desastre das VMs de VMware no local](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Instruções detalhadas estão disponíveis para ajudá-lo [configurar o ambiente de origem](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implementar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [configurar as definições de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sobre o agente convidado do Azure para Linux.



### <a name="enable-replication-for-osticketweb"></a>Ativar a replicação para OSTICKETWEB

Agora, os administradores da Contoso podem começar a replicar a **OSTICKETWEB** VM.

1. Na **replicar aplicação** > **origem** > **+ replicar** selecionarem as definições de origem.
2. Selecionar se quer ativar máquinas virtuais, selecione as definições de origem, incluindo o servidor vCenter e o servidor de configuração.

    ![Ativar a replicação](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. Especifica as definições de destino, incluindo o grupo de recursos e VNet na qual a VM do Azure estarão localizada após a ativação pós-falha e a conta de armazenamento no qual serão armazenados os dados replicados.

     ![Ativar a replicação](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Eles selecionam os **OSTICKETWEB** VM para replicação. 

    - Nesta fase selecionarem **OSTICKETWEB** única, uma vez que a VNet e a sub-rede tem ambos de ser selecionadas e as VMs não estão na mesma sub-rede.
    - Site Recovery instala automaticamente o serviço de mobilidade quando a replicação está ativada para a VM.

    ![Ativar a replicação](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. Nas propriedades da VM, eles selecionam a conta que é utilizada pelo servidor de processos para instalar automaticamente o serviço de mobilidade na máquina.

     ![Serviço de Mobilidade](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. na **as definições de replicação** > **configurar as definições de replicação**, eles verificam a que a política de replicação correto é aplicado e selecione **ativar replicação**.
6.  Controlar o progresso de replicação em **tarefas**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.



### <a name="enable-replication-for-osticketmysql"></a>Ativar a replicação para OSTICKETMYSQL

Agora, os administradores da Contoso podem iniciar a replicação **OSTICKETMYSQL**.

1. Na **replicar aplicação** > **origem** > **+ replicar** selecionarem as definições de origem e de destino.
2. Eles selecionam os **OSTICKETMYSQL** VM para a replicação e selecione a conta a utilizar para a instalação do serviço de mobilidade.

    ![Ativar a replicação](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Aplicar a mesma política de replicação que foi utilizada pela OSTICKETWEB e ativar a replicação.  

**Precisa de mais ajuda?**

Pode ler um passo a passo completa de todos estes passos [ativar a replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Passo 4: Migrar as VMs 

Os administradores de contoso executados uma rápida ativação pós-falha de teste e, em seguida, migrar as VMs.

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Executar uma ativação pós-falha de teste ajuda a garantir que está tudo a funcionar conforme esperado antes da migração. 

1. Execute uma ativação pós-falha de teste para o ponto mais recente no tempo (**processado mais recentemente**).
2. Eles selecionam **encerrar a máquina antes de iniciar a ativação pós-falha**, para que o Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. 
3. Execuções de ativação pós-falha de teste: 
    - Uma verificação dos pré-requisitos é executado para certificar-se de que todas as condições necessárias para a migração estão em vigor.
    - A ativação pós-falha processa os dados, para que possa ser criada uma VM do Azure. Se o ponto de recuperação mais recente estiver selecionado, é criado um ponto de recuperação dos dados.
    - É criada uma VM do Azure com base nos dados processados no passo anterior.
3. Depois de concluída a ativação pós-falha, a réplica é apresentada VM do Azure no portal do Azure. Eles verificam-se ao que a VM tem um tamanho adequado, está ligada à rede certa, e que está em execução. 
4. Depois de verificar, eles limpar a ativação pós-falha e registram e guarde todas as observações.

### <a name="create-and-customize-a-recovery-plan"></a>Criar e personalizar um plano de recuperação

 Depois de verificar que a ativação pós-falha de teste funcionou como esperado, os administradores da Contoso, crie um plano de recuperação para a migração. 

- Um plano de recuperação Especifica a ordem na qual ativação pós-falha ocorre, como VMs do Azure vão ser colocadas no Azure.
- Uma vez que pretende migrar uma aplicação de duas camadas, eles irá personalizar o plano de recuperação para que os dados de VM (SQLVM) é iniciado antes do front-end (WEBVM).


1. Na **(recuperação de sites) de planos de recuperação** > **+ plano de recuperação**, criam um plano e adicionar as VMs ao mesmo.

    ![Plano de recuperação](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Depois de criar o plano, eles selecioná-lo para personalização (**planos de recuperação** > **OsTicketMigrationPlan** > **personalizar**.
3.  Remova **OSTICKETWEB** partir **grupo 1: Iniciar**.  Isto garante que a primeira ação de início afeta **OSTICKETMYSQL** apenas.

    ![Grupo de recuperação](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  Na **+ grupo** > **itens protegidos de Add**, adicionam **OSTICKETWEB** para **grupo 2: Iniciar**.  Eles precisam, estes em dois grupos diferentes.

    ![Grupo de recuperação](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Migrar as VMs


Os administradores da Contoso estão agora prontos para executar uma ativação pós-falha no plano de recuperação, para migrar as VMs.

1. Eles selecionam o plano > **ativação pós-falha**.
2.  Eles selecionam para efetuar a ativação pós-falha do ponto de recuperação mais recente e especifica que o Site Recovery deve tentar encerrar a VM no local antes de acionar a ativação pós-falha. Pode seguir o progresso de ativação pós-falha **tarefas** página.

    ![Ativação pós-falha](./media/contoso-migration-rehost-vm/failover1.png)

3. Durante a ativação pós-falha, o servidor vCenter emite comandos para parar as duas VMs em execução no anfitrião ESXi.

    ![Ativação pós-falha](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Após a ativação pós-falha, eles Certifique-se de que a VM do Azure é apresentado conforme esperado no portal do Azure.

    ![Ativação pós-falha](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Depois de verificar a VM no Azure, eles concluírem a migração para concluir o processo de migração para cada VM. Para a replicação para a VM e interrompe a faturação do Site Recovery para a VM.

    ![Ativação pós-falha](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Ligar a VM para a base de dados

Como a etapa final do processo de migração, os administradores da Contoso atualizar a cadeia de ligação da aplicação para apontar para a base de dados de aplicação em execução no **OSTICKETMYSQL** VM. 

1. Eles tornam a uma ligação de SSH para o **OSTICKETWEB** VM com o Putty ou de outro cliente SSH. A VM é privada, de modo que ligar-se com o endereço IP privado.

    ![Ligar à base de dados](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![Ligar à base de dados](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. Que precisam para se certificar de que o **OSTICKETWEB** VM pode comunicar com o **OSTICKETMYSQL** VM. Atualmente a configuração é codificado com o endereço IP local 172.16.0.43.

    **Antes da atualização**
    
    ![Atualizar o IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Após a atualização**
    
    ![Atualizar o IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. Eles reiniciem o serviço com **systemctl reiniciar apache2**.

    ![Reiniciar](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Por fim, atualizam os registros DNS **OSTICKETWEB** e **OSTICKETMYSQL**, em um dos controladores de domínio Contoso.

    ![Atualizar DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Atualizar DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) executar uma ativação pós-falha de teste. 
- [Saiba](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) como criar um plano de recuperação.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) efetuar a ativação pós-falha para o Azure.

## <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração é concluída, os escalões de aplicação osTicket estão agora em execução em VMs do Azure.

Agora, a Contoso precisa limpar da seguinte forma: 

- Remova as VMs no local no inventário do vCenter.
- Remova as VMs no local das tarefas de cópia de segurança locais.
- Atualize a respetiva documentação interna para mostrar o novo local e os endereços IP para OSTICKETWEB e OSTICKETMYSQL.
- Reveja todos os recursos que interagem com as VMs e atualizar as definições relevantes ou documentação para refletir a nova configuração.
- Contoso utilizado o serviço Azure Migrate com o mapeamento da dependência para avaliar as VMs para a migração. Os administradores devem remover o Microsoft Monitoring Agent e o agente de dependência que instalados para essa finalidade, da VM.

## <a name="review-the-deployment"></a>Reveja a implementação

Com a aplicação agora em execução, a Contoso precisa totalmente operacionalizar e proteger sua infra-estrutura de novo.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso reveja o OSTICKETWEB e OSTICKETMYSQLVMs para determinar os problemas de segurança.

- A equipe analisa os grupos de segurança de rede (NSGs) para as VMs controlar o acesso. NSGs são utilizados para garantir que apenas o tráfego permitido para a aplicação pode passar.
- A equipe também considera a proteção dos dados nos discos de VM com a encriptação de disco e o Cofre de chaves do Azure.

[Leia mais](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) sobre práticas de segurança para as VMs.

### <a name="bcdr"></a>BCDR

Para a continuidade do negócio e recuperação após desastre, a Contoso usa as seguintes ações:

- **Manter os dados seguros**: Contoso efetua uma cópia dos dados nas VMs com o serviço de cópia de segurança do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Manter as aplicações em funcionamento**: Contoso replica as VMs no Azure da aplicação para uma região secundária com o Site Recovery. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

### <a name="licensing-and-cost-optimization"></a>Otimização de licenciamento e de custo

- Depois de implementar recursos, Contoso atribui etiquetas do Azure conforme definido durante a [implementação de infraestrutura do Azure](contoso-migration-infrastructure.md#set-up-tagging).
- A Contoso tiver sem problemas de licenciamento com os servidores do Ubuntu.
- Contoso irá ativar o Azure Cost Management licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gestão de custos multiclouds que o ajuda a utilizar e gerir o Azure e outros recursos da cloud.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Azure Cost Management. 


## <a name="next-steps"></a>Passos Seguintes

Neste artigo, mostramos como Contoso migrou no local de suporte técnico do serviço de aplicações em camadas em duas VMs do Linux para VMs de IaaS do Azure, com o Azure Site Recovery.

No próximo artigo da série, vamos mostrar-lhe como a Contoso migra o mesmo serviço de aplicações de suporte técnico para o Azure. Desta vez Contoso utiliza o Site Recovery para migrar o VM de front-end da aplicação e migra a base de dados de aplicação através de cópia de segurança e restaurar a base de dados do Azure para MySQL, usando a ferramenta workbench do MySQL. [Introdução ao](contoso-migration-rehost-linux-vm-mysql.md).
