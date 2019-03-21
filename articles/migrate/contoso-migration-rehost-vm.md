---
title: Realojar uma aplicação da Contoso com a migração para VMs do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como a aplicação de realojamento no local com uma migração lift-and-shift no local das máquinas para o Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4a6ed900753747c1d5bf394aced54da11177320f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118396"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migração de Contoso: Realojar uma aplicação no local em VMs do Azure


Este artigo demonstra como Contoso realojar a aplicação da SmartHotel360 no local no Azure, ao migrar as VMs da aplicação para as VMs do Azure.


Este documento é um de uma série de artigos que mostram como a empresa fictícia Contoso migra recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e cenários que ilustram como configurar uma infraestrutura de migração, avaliação dos recursos no local para migração e execução de diferentes tipos de migrações. Cenários de crescem em complexidade. Vamos adicionar artigos adicionais ao longo do tempo.

**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Descrição geral da série de artigos, estratégia de migração da Contoso e as aplicações de exemplo que são utilizadas da série. | Disponível
[Artigo 2: Implementar a infraestrutura do Azure](contoso-migration-infrastructure.md) | Contoso prepara a sua infraestrutura no local e a respetiva infraestrutura do Azure para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avalie os recursos no local para migração para o Azure](contoso-migration-assessment.md)  | Contoso é executada uma avaliação da sua aplicação da SmartHotel360 no local em execução no VMware. Contoso avalia as VMs de aplicação com o serviço Azure Migrate e a base de dados de SQL Server do aplicativo com o Assistente de migração de dados. | Disponível
[Artigo 4: Realojar a uma aplicação numa VM do Azure e a instância gerida da base de dados do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso é executada uma migração lift-and-shift para o Azure para a sua aplicação da SmartHotel360 no local. Contoso migra a aplicação front-end VM com [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migra a base de dados de aplicação para uma instância gerida da base de dados SQL do Azure utilizando o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível
5 do artigo: Realojar uma aplicação em VMs do Azure | Contoso migra a sua aplicação da SmartHotel360 VMs para VMs do Azure com o serviço Site Recovery. | Este artigo
[Artigo 6: Realojar a uma aplicação em VMs do Azure e, num grupo de Disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migra a aplicação da SmartHotel360. A Contoso utiliza o Site Recovery para migrar as VMs da aplicação. Ele usa o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server que está protegido por um grupo de Disponibilidade AlwaysOn. | Disponível
[Artigo 7: Realojar uma aplicação do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | Contoso é uma migração lift-and-shift da aplicação de osTicket do Linux para VMs do Azure, com o Azure Site Recovery concluída | Disponível
[Artigo 8: Realojar uma aplicação do Linux em VMs do Azure e MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migra a aplicação de osTicket do Linux para VMs do Azure com o Azure Site Recovery e migra a base de dados de aplicação para uma instância do servidor MySQL do Azure com o MySQL Workbench. | Disponível
[Artigo 9: Refatorizar uma aplicação na base de dados de aplicações Web do Azure e SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Contoso migra a aplicação da SmartHotel360 para uma aplicação Web do Azure e migra a base de dados de aplicação para uma instância de servidor SQL do Azure com o Assistente de migração de base de dados | Disponível
[Artigo 10: Refatorizar uma aplicação do Linux em aplicações Web do Azure e MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migra sua osTicket de aplicação do Linux para uma aplicação web do Azure em várias regiões do Azure utilizando o Gestor de tráfego do Azure, integrado com o GitHub para a entrega contínua. Contoso migra a base de dados de aplicação para uma base de dados do Azure para a instância do MySQL. | Disponível
[Artigo 11: Refatorar o TFS em serviços do Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migra sua implantação do Team Foundation Server no local para serviços do Azure DevOps no Azure. | Disponível
[Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados do Azure SQL](contoso-migration-rearchitect-container-sql.md) | Contoso migra sua SmartHotel de aplicação para o Azure. Em seguida, rearchitects a camada de web de aplicação como um contentor do Windows em execução no Azure Service Fabric e base de dados com a base de dados do Azure SQL. | Disponível
[Artigo 13: Recriar uma aplicação no Azure](contoso-migration-rebuild.md) | Contoso recria o seu aplicativo de SmartHotel com uma variedade de capacidades do Azure e serviços, incluindo o serviço de aplicações do Azure, Azure Kubernetes Service (AKS), as funções do Azure, serviços cognitivos do Azure e Azure Cosmos DB. | Disponível
[Artigo 14: Dimensionar uma migração para o Azure](contoso-migration-scale.md) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível


Neste artigo, a Contoso vai migrar o Windows de duas camadas. Aplicação de NET SmartHotel360 em execução em VMs de VMware, para o Azure. Se pretender utilizar esta aplicação, são fornecidas como código-fonte aberto e pode baixá-lo partir [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Fatores comerciais

A equipe de liderança de TI tem trabalhado em estreita colaboração com parceiros de negócios para compreender o que quer alcançar com esta migração:

- **Resolver o crescimento do negócio**: Contoso está a crescer e assim há pressão em seus sistemas no local e a infraestrutura.
- **Limitar o risco**: A aplicação da SmartHotel360 é fundamental para a empresa Contoso. Quer mover a aplicação para o Azure com zero risco.
- **Expandir**: Contoso não quer modificar a aplicação, mas pretende garantir que está estável.


## <a name="migration-goals"></a>Objetivos de migração

A equipe de cloud de Contoso tiver afixado para baixo de objetivos para essa migração. Esses objetivos são utilizados para determinar o melhor método de migração:

- Após a migração, a aplicação no Azure deve ter as mesmas capacidades de desempenho, como sucede no VMware. A aplicação permanecerá como crítica na cloud, pois é no local.
- Contoso não quer investir nesta aplicação. É importante para os negócios, mas em sua forma atual Contoso simplesmente quer movê-lo com segurança para a cloud.
- Contoso não quer alterar o modelo de ops para esta aplicação. Contoso quer interagir com ele, na cloud, da mesma forma que eles fazem agora.
- Contoso não quer alterar qualquer funcionalidade de aplicação. Apenas a localização da aplicação serão alterados.


## <a name="solution-design"></a>Design da solução

Depois de fixar-se para baixo de objetivos e requisitos, a Contoso projeta e rever uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que Contoso irá utilizar para a migração.

### <a name="current-app"></a>Aplicação atual

- A aplicação está em camadas em duas VMs (**WEBVM** e **SQLVM**).
- As VMs estão localizadas no anfitrião ESXi do VMware **contosohost1.contoso.com** (versão 6.5).
- O ambiente do VMware é gerido pelo vCenter Server 6.5 (**vcenter.contoso.com**), em execução numa VM.
- A Contoso tiver um datacenter no local (contoso-datacenter), com um controlador de domínio no local (**contosodc1**).

### <a name="proposed-architecture"></a>Arquitetura proposta

- Uma vez que a aplicação é uma carga de trabalho de produção, as VMs no Azure da aplicação irão residir no grupo de recursos de produção ContosoRG.
- As VMs da aplicação serão migradas para a região primária do Azure (E.U.A. Leste 2) e colocada na rede de produção (VNET-PROD-EUS2).
- O front-end web VM irá residir na sub-rede de front-end (PROD-FE-EUS2) na rede de produção.
- A base de dados VM irá residir na sub-rede da base de dados (PROD-DB-EUS2) na rede de produção.
- As VMs no local no Centro de dados Contoso serão encerradas depois de terminar a migração.

![Arquitetura do cenário](./media/contoso-migration-rehost-vm/architecture.png)

### <a name="database-considerations"></a>Considerações sobre a base de dados

Como parte do processo de design da solução, a Contoso fez uma comparação de funcionalidades entre a base de dados do Azure SQL e SQL Server. As seguintes considerações ajudado a prepará-los para decidir ficar com o SQL Server em execução numa VM de IaaS do Azure:

- Utilizar VM do Azure com o SQL Server parece ser uma solução ideal, se a Contoso precisa personalizar o sistema operativo ou o servidor de base de dados, ou se ele poderá querer colocalizar e executar aplicações de terceiros na mesma VM.
- No futuro com o Software Assurance, Contoso pode de trocar licenças existentes para tarifas com desconto no banco de dados instância gerida do SQL com o Azure Hybrid Benefit para o SQL Server. Isso pode economizar até 30% na instância gerida.



### <a name="solution-review"></a>Revisão de solução

Contoso avalia a estrutura proposta ao juntar-se de uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Profissionais de TI** | Ambas as VMs de aplicação serão movidas para o Azure sem alterações, fazer a migração simples.<br/><br/> Uma vez que a Contoso está usando a migração lift-and-shift para ambas as VMs de aplicação, nenhuma ferramenta de configuração ou migração especial é necessárias para a base de dados de aplicação.<br/><br/> Contoso pode aproveitar seus investimentos em Software Assurance, com o benefício híbrido do Azure.<br/><br/> Contoso irão manter o controlo total da aplicação VMs no Azure.
**Contras** | WEBVM e SQLVM executem o Windows Server 2008 R2. O sistema operativo é suportado pelo Azure para funções específicas (Julho de 2018). [Saiba mais](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> Os escalões web e os dados da aplicação irão permanecer um ponto único de ativação pós-falha.</br><br/> SQLVM está em execução no SQL Server 2008 R2 que não está no suporte base. No entanto, é suportada para VMs do Azure (Julho de 2018). [Saiba mais](https://support.microsoft.com/en-us/help/956893).<br/><br/> Contoso tem de continuar a suportar a aplicação como VMs do Azure, em vez de mudar para um serviço gerido, como o serviço de aplicações do Azure e base de dados do Azure SQL.



### <a name="migration-process"></a>Processo de migração

Contoso migrará o front-end de aplicação e a base de dados VMs para as VMs do Azure com o Site Recovery:

- Como primeiro passo, a Contoso prepara e configura componentes do Azure para o Site Recovery e prepara a infraestrutura de VMware no local.
- Que já têm o [infraestrutura do Azure](contoso-migration-infrastructure.md) in-loco, então, Contoso apenas tem de adicionar alguns componentes do Azure especificamente para o Site Recovery.
- Com tudo o que preparado, Contoso pode começar a replicar as VMs.
- Depois de ativar a replicação e a trabalhar, Contoso migrará a VM ao failover para o Azure.

![Processo de migração](./media/contoso-migration-rehost-vm/migraton-process.png)



### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço orquestra e gere a migração e recuperação após desastre para VMs do Azure e VMs e servidores físicos no local.  | Durante a replicação para o Azure, são cobradas taxas de armazenamento do Azure. VMs do Azure são criadas e incorrer em custos, quando ocorre a ativação pós-falha. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços e custos.


## <a name="prerequisites"></a>Pré-requisitos

Eis o que a Contoso precisa de executar este cenário.

**Requisitos** | **Detalhes**
--- | ---
**Subscrição do Azure** | Contoso criadas subscrições num artigo anterior desta série. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se criar uma conta gratuita, será o administrador da sua subscrição e poderá executar todas as ações.<br/><br/> Se utilizar uma subscrição existente e não for o administrador, terá de trabalhar com o administrador para lhe atribuir permissões de proprietário ou contribuinte.<br/><br/> Se precisar de permissões mais granulares, reveja [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md).
**Infraestrutura do Azure** | [Saiba como](contoso-migration-infrastructure.md) Contoso configurar uma infraestrutura do Azure.<br/><br/> Saiba mais sobre específicas [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) requisitos para a recuperação de Site.
**Servidores no local** | Servidores vCenter devem estar a executar a versão 5.5, 6.0 ou 6.5 no local<br/><br/> Anfitriões ESXi devem executar a versão 5.5, 6.0 ou 6.5<br/><br/> Uma ou mais VMs de VMware deve estar em execução no anfitrião ESXi.
**VMs no local** | As VMs têm de cumprir [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Passos do cenário

Eis como Contoso administradores executar a migração:

> [!div class="checklist"]
> * **Passo 1: Preparar o Azure para o Site Recovery**: Criação de uma conta de armazenamento do Azure para manter os dados replicados e um cofre dos serviços de recuperação.
> * **Passo 2: Preparar o VMware no local para o Site Recovery**: Preparar contas para instalação de deteção e o agente da VM e preparar a ligação para as VMs do Azure após a ativação pós-falha.
> * **Passo 3: Replicar VMs**: Configurar a replicação e iniciar a replicação de VMs para o armazenamento do Azure.
> * **Passo 4: Migrar as VMs com o Site Recovery**: Executar uma ativação pós-falha de teste para se certificar de que está tudo a funcionar e, em seguida, execute uma ativação pós-falha completa para migrar as VMs para o Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Passo 1: Preparar o Azure para o serviço Site Recovery

Aqui estão os componentes do Azure, a Contoso precisa de migrar as VMs para o Azure:

- Uma VNet na qual as VMs do Azure estarão localizadas quando forem criadas durante a ativação pós-falha.
- Uma conta de armazenamento do Azure para armazenar dados replicados.
- Um cofre dos serviços de recuperação no Azure.

Eles configurar estes da seguinte forma:

1. Configurar uma rede-Contoso já configurado uma rede que pode ser para o Site Recovery quando eles [implementado a infraestrutura do Azure](contoso-migration-infrastructure.md)

    - A aplicação da SmartHotel360 é uma aplicação de produção e as VMs serão migradas para a rede de produção do Azure (VNET-PROD-EUS2) na região E.U.A. Leste 2 primária.
    - Ambas as VMs serão colocadas no grupo de recursos ContosoRG, que é utilizado para recursos de produção.
    - O front-end de aplicação VM (WEBVM) serão migrados para a sub-rede de front-end (PROD-FE-EUS2), na rede de produção.
    - A base de dados de aplicação VM (SQLVM) serão migrados para a sub-rede de base de dados (PROD-DB-EUS2), na rede de produção.

2. Configurar uma conta de armazenamento-Contoso cria uma conta de armazenamento do Azure (contosovmsacc20180528) na região primária.
   - A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.
   - Utilizam uma conta para fins gerais, com o armazenamento standard e replicação LRS.

     ![Armazenamento de recuperação de site](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Criar um cofre-com a conta de armazenamento e de rede no local, a Contoso agora cria um cofre de serviços de recuperação (ContosoMigrationVault) e o coloca no grupo de recursos ContosoFailoverRG na região E.U.A. Leste 2 primária.

    ![Cofre dos Serviços de Recuperação](./media/contoso-migration-rehost-vm/asr-vault.png)

**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) configurar o Azure para o Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Passo 2: Preparar o VMware no local para o Site Recovery

Eis o que Contoso prepara no local:

- Uma conta no vCenter server ou vSphere ESXi no anfitrião, para automatizar a deteção de VMS.
- Uma conta que permite a instalação automática do serviço de mobilidade nas VMs de VMware.
- No local as definições da VM, para que a Contoso pode ligar às VMs do Azure replicadas após a ativação pós-falha.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a deteção automática

O Site Recovery precisa de ter acesso a servidores VMware para:

- Detetar automaticamente VMs.
- Orquestre a replicação, ativação pós-falha e reativação pós-falha para VMs.
- É necessária, pelo menos, uma conta só de leitura. A conta deve ser capaz de executar operações como criar e remover discos e Ativando a VMs.

Administradores de contoso, configure a conta da seguinte forma:

1. É possível criar uma função ao nível do vCenter.
2. Essa função que atribuir as permissões necessárias.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade tem de ser instalado em cada VM.

- Site Recovery pode fazer uma instalação push automática do serviço de mobilidade quando a replicação de VM é ativada.
- É necessária, uma conta para que o Site Recovery pode acessar as VMs para a instalação push. Especificar esta conta quando configurar a replicação.
- A conta pode ser de domínio ou local, com permissões para instalar nas VMs.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Após a ativação pós-falha, a Contoso quer ligar às VMs do Azure. Para tal, os administradores da Contoso faça o seguinte antes da migração:

1. Para ter acesso através da internet:

   - Ative o RDP na VM no local antes da ativação pós-falha.
   - Certifique-se de que as regras TCP e UDP estão adicionadas para o **público** perfil.
   - Verifique se o RDP é permitido na **Firewall do Windows** > **aplicações permitidas** para todos os perfis.

2. Para ter acesso através de VPN de site a site, eles:

   - Ative o RDP na máquina no local.
   - Permitir RDP no **Firewall do Windows** -> **aplicações e funcionalidades permitidas**, para **domínio e privadas** redes.
   - Definir política de SAN do sistema operacional na VM no local para **OnlineAll**.

Além disso, quando executam uma ativação pós-falha que precisam verificar o seguinte:

- Não deve haver nenhuma atualização do Windows pendentes na VM quando acionar uma ativação pós-falha. Se existirem, não poderá iniciar sessão na VM, enquanto a atualização for concluída.
- Após a ativação pós-falha, pode verificar **diagnósticos de arranque** para ver uma captura de ecrã da VM. Se não funcionar, deve verificar que a VM está em execução e revisá-los [sugestões de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) criar e atribuir uma função para a deteção automática.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) criar uma conta para a instalação push do serviço de mobilidade.


## <a name="step-3-replicate-the-on-premises-vms"></a>Passo 3: Replicar as VMs no local

Antes dos administradores da Contoso podem executar uma migração para o Azure, tem de configurar e ativar a replicação.

### <a name="set-a-replication-goal"></a>Definir um objetivo de replicação

1. No cofre, sob o nome do cofre (ContosoVMVault) selecionarem um objetivo de replicação (**introdução** > **Site Recovery** > **preparar infraestrutura** .
2. Especifica que suas máquinas estão localizados no local, em execução no VMware e replicar para o Azure.

    ![Objetivo da replicação](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

Para continuar, confirmarem o que eles concluam planeamento da implementação, selecionando **Sim, já o fiz**. Neste cenário, a Contoso está a migrar apenas duas VMs e não precisa de planeamento da implementação.


### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Os administradores da Contoso tem de configurar o ambiente de origem. Para fazer isso, eles transferir um modelo OVF e utilizá-lo para implementar o servidor de configuração de recuperação de sites como uma elevada disponibilidade, a VM de VMware no local. Depois do servidor de configuração está a funcionar, eles registá-lo no cofre.

O servidor de configuração é executada um número de componentes:

- O componente de servidor de configuração que coordena as comunicações entre no local e o Azure e gere a replicação de dados.
- O servidor de processos atua como um gateway de replicação. Recebe dados de replicação; otimiza-os com a colocação em cache, compressão e encriptação; e envia-os para o armazenamento do Azure.
- O servidor de processos também instala o Serviço de Mobilidade nas VMs que pretende replicar, e efetua a deteção automática de VMs VMware no local.



Os administradores da Contoso executam estes passos da seguinte forma:

1. No cofre, transferem o modelo OVF **preparar infraestrutura** > **origem** > **servidor de configuração**.
    
    ![Transferir OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. Eles importam o modelo para o VMware, para criar e implementar a VM.

    ![Modelo OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3. Quando ativam a VM pela primeira vez, ele arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
4. Após a instalação terminar, iniciam sessão na VM como administrador. No primeiro início de sessão, a ferramenta de configuração do Azure Site Recovery é executado por predefinição.
5. Na ferramenta, eles especificam um nome para registar o servidor de configuração no cofre.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois da ligação é estabelecida, iniciam sessão para a subscrição do Azure. As credenciais têm de ter acesso ao Cofre no qual eles irá registar o servidor de configuração.

    ![Registar servidor de configuração](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reiniciada.
8. Iniciam sessão na máquina novamente e o Assistente de gestão de servidor de configuração é iniciado automaticamente.
9. No assistente, eles selecionam o NIC para receber o tráfego de replicação. Não é possível alterar esta definição depois de estar configurada.
10. Selecionar a subscrição, grupo de recursos e o Cofre no qual pretende registar o servidor de configuração.
        ![vault](./media/contoso-migration-rehost-vm/cswiz1.png)

10. Eles transferirem e instalar o servidor MySQL e o VMWare PowerCLI.
11. Após a validação, eles especificam o FQDN ou endereço IP do vCenter server ou vSphere no anfitrião. Deixe a porta predefinida e especifique um nome amigável para o servidor no Azure.
12. Especifica a conta que o próprio criou para a deteção automática e as credenciais que são utilizadas para instalar automaticamente o serviço de mobilidade. Para máquinas Windows, a conta tem privilégios de administrador local nas VMs.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Após a conclusão do registo, no portal do Azure, eles duplo verificam que o servidor de configuração e o servidor VMware estão listados no **origem** página no cofre. Deteção pode demorar 15 minutos ou mais.
8. Site Recovery, em seguida, liga-se aos servidores VMware com as definições especificadas e Deteta as VMs.

### <a name="set-up-the-target"></a>Configurar o destino

Agora os administradores da Contoso, especifique as definições de replicação de destino.

1. Na **preparar infraestrutura** > **destino**, eles selecionam as definições de destino.
2. Recuperação de sites verifica que existe uma conta de armazenamento do Azure e a rede na localização de destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Agora, os administradores da Contoso podem criar uma política de replicação.

1. Na **preparar infraestrutura** > **definições de replicação** > **política de replicação** >  **criar e Associar**, criaram uma política **ContosoMigrationPolicy**.
2. Se utilizarem as predefinições:
    - **Limiar RPO**: Predefinição de 60 minutos. Este valor define com que frequência são criados pontos de recuperação. Será gerado um alerta se a replicação contínua exceder este limite.
    - **Retenção do ponto de recuperação**. Predefinição de 24 horas. Este valor especifica quanto tempo dura o período de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela.
    - **Frequência de instantâneos consistentes com a aplicação**. Padrão de uma hora. Este valor Especifica a frequência com que os instantâneos consistentes com aplicações são criados.

        ![Criar política de replicação](./media/contoso-migration-rehost-vm/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração.

    ![Associar a política de replicação](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Ativar a replicação para WEBVM

Com tudo instalado, os administradores da Contoso podem agora ativar a replicação para as VMs. Eles começam com WebVM.

1. Na **replicar aplicação** > **origem** > **+ replicar** selecionarem as definições de origem.
2. Eles indicam que se deve ativar VMs, selecione o servidor vCenter e o servidor de configuração.

    ![Ativar a replicação](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Selecionar as definições de destino, incluindo o grupo de recursos e a rede do Azure e a conta de armazenamento.

    ![Ativar a replicação](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Eles selecionam **WebVM** para a replicação, verifique a política de replicação e ativar a replicação.

   - Nesta fase eles seleciona apenas WEBVM porque VNet e sub-rede tem de ser selecionadas e as VMs da aplicação serão colocadas em sub-redes diferentes.
   - Site Recovery instala automaticamente o serviço de mobilidade na VM quando a replicação está ativada.

     ![Ativar a replicação](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Controlar o progresso de replicação em **tarefas**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.
6. Na **Essentials** no portal do Azure, podem ver a estrutura para as VMs a replicar para o Azure.


### <a name="enable-replication-for-sqlvm"></a>Ativar a replicação para SQLVM

Agora os administradores da Contoso podem começar a replicar a máquina SQLVM, usando o mesmo processo, como mostrado acima.

1. Selecionar definições da origem.

    ![Ativar a replicação](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Eles, em seguida, especificam as definições de destino.

     ![Ativar a replicação](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Eles selecionam SQLVM para replicação.

    ![Ativar a replicação](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. Aplicar a mesma política de replicação que foi utilizada pela WEBVM e ativar a replicação.

    ![Vista da infraestrutura](./media/contoso-migration-rehost-vm/essentials.png)

**Precisa de mais ajuda?**

- Pode ler um passo a passo completa de todos estes passos [configurar a recuperação após desastre das VMs de VMware no local](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Instruções detalhadas estão disponíveis para ajudá-lo [configurar o ambiente de origem](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implementar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [configurar as definições de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Pode saber mais sobre [ativar a replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Passo 4: Migrar as VMs

Os administradores da Contoso executam uma ativação pós-falha de teste rápida e, em seguida, uma ativação pós-falha completa para migrar as VMs.

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Uma ativação pós-falha de teste ajuda a garantir que está tudo a funcionar conforme esperado.

1. Execute uma ativação pós-falha de teste para o ponto mais recente no tempo (**processado mais recentemente**).
2. Eles selecionam **encerrar a máquina antes de iniciar a ativação pós-falha**, para que o Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe.
3. Execuções de ativação pós-falha de teste:

    - Uma verificação dos pré-requisitos é executado para certificar-se de que todas as condições necessárias para a migração estão em vigor.
    - A ativação pós-falha processa os dados, para que possa ser criada uma VM do Azure. Se for selecionado o último ponto de recuperação, é criado um ponto de recuperação a partir dos dados.
    - É criada uma VM do Azure com base nos dados processados no passo anterior.

3. Depois de concluída a ativação pós-falha, a réplica é apresentada VM do Azure no portal do Azure. Eles verificam-se ao que a VM tem um tamanho adequado, ligado à rede certa e está em execução.
4. Depois de verificar a ativação pós-falha de teste, eles limpar a ativação pós-falha e registram e guarde todas as observações.

### <a name="create-and-customize-a-recovery-plan"></a>Criar e personalizar um plano de recuperação

 Depois de verificar que a ativação pós-falha de teste funcionou como esperado, os administradores da Contoso, crie um plano de recuperação para a migração.

- Um plano de recuperação Especifica a ordem na qual ativação pós-falha ocorre e indica como as VMs do Azure irá ser colocadas online no Azure.
- Uma vez que a aplicação é duas camadas, eles personalizar o plano de recuperação para que os dados a VM (SQLVM) é iniciado antes do front-end (WEBVM).

1. Na **(recuperação de sites) de planos de recuperação** > **+ plano de recuperação**, criam um plano e adicionar as VMs ao mesmo.

    ![Plano de recuperação](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Depois de criar o plano, eles personalizá-lo (**planos de recuperação** > **SmartHotelMigrationPlan** > **personalizar**).
2.  Remova WEBVM de **grupo 1: Iniciar**. Isto garante que a primeira ação de início afeta SQLVM apenas.
3.  Na **+ grupo** > **itens protegidos de adicionar**, adicionem WEBVM ao grupo 2: Inicie. As VMs tem de estar em dois grupos diferentes.


### <a name="migrate-the-vms"></a>Migrar as VMs


Agora, os administradores da Contoso executam uma ativação pós-falha completa para concluir a migração.

1. Eles selecionam o plano de recuperação > **ativação pós-falha**.
2. Devem selecionar para efetuar a ativação pós-falha do ponto de recuperação mais recente, e esse Site Recovery tente encerrar a VM no local antes de acionar a ativação pós-falha. Pode seguir o progresso de ativação pós-falha **tarefas** página.

    ![Ativação pós-falha](./media/contoso-migration-rehost-vm/failover1.png)


3. Após a ativação pós-falha, eles Certifique-se de que a VM do Azure é apresentado conforme esperado no portal do Azure.

    ![Ativação pós-falha](./media/contoso-migration-rehost-vm/failover2.png)

3. Após a verificação, eles concluírem a migração para cada VM. Para a replicação para a VM e interrompe a faturação do Site Recovery para o mesmo.

    ![Ativação pós-falha](./media/contoso-migration-rehost-vm/failover3.png)

**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) executar uma ativação pós-falha de teste.
- [Saiba](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) como criar um plano de recuperação.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) efetuar a ativação pós-falha para o Azure.

## <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração é concluída, as camadas de aplicações da SmartHotel360 estão agora em execução em VMs do Azure.

Agora, a Contoso precisa de concluir estes passos de limpeza:

- Remova a máquina WEBVM no inventário do vCenter.
- Remova a máquina SQLVM no inventário do vCenter.
- Remova WEBVM e SQLVM das tarefas de cópia de segurança locais.
- Atualize a documentação interna para mostrar a nova localização e os endereços IP para as VMs.
- Reveja todos os recursos que interagem com as VMs e atualizar as definições relevantes ou documentação para refletir a nova configuração.

## <a name="review-the-deployment"></a>Reveja a implementação

Com a aplicação agora em execução, a Contoso precisa agora totalmente operacionalizar e protegê-lo no Azure.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso analisa as VMs do Azure, para determinar os problemas de segurança.

- Para controlar o acesso, a equipe analisa os grupos de segurança de rede (NSGs) para as VMs. NSGs são utilizados para garantir que apenas o tráfego permitido para a aplicação pode aceder ao mesmo.
- A equipe também considerar a proteção dos dados no disco com o Azure Disk Encryption e o Cofre de chaves.

[Leia mais](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) sobre práticas de segurança para as VMs.

## <a name="bcdr"></a>BCDR

Para a continuidade do negócio e recuperação após desastre (BCDR), a Contoso usa as seguintes ações:

- Manter os dados seguros: Contoso efetua uma cópia dos dados nas VMs com o serviço de cópia de segurança do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Manter as aplicações em funcionamento: Contoso replica as VMs no Azure da aplicação para uma região secundária com o Site Recovery. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).



### <a name="licensing-and-cost-optimization"></a>Otimização de licenciamento e de custo

1. Contoso tem o licenciamento existentes para as suas VMs e irá tirar partido do benefício híbrido do Azure. Contoso converterá as VMs do Azure existente, para tirar partido destes preços.
2. Contoso irá ativar o Azure Cost Management licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gestão de custos multiclouds que o ajuda a utilizar e gerir o Azure e outros recursos da cloud. [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Azure Cost Management.

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso rehosted a aplicação da SmartHotel360 no Azure ao migrar as VMs da aplicação para VMs do Azure com o serviço Site Recovery.


## <a name="next-steps"></a>Passos Seguintes

Na [próximo artigo](contoso-migration-rehost-vm-sql-ag.md) da série, vamos mostrar-lhe como Contoso realojar da SmartHotel360 aplicações front-end VM numa VM do Azure e migra a base de dados para um grupo de Disponibilidade AlwaysOn do SQL Server no Azure.

