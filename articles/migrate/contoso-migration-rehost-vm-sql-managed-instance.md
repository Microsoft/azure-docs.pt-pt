---
title: Uma aplicação da Contoso no local de realojamento ao migrar para as VMs do Azure e a instância gerida da base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como Contoso realojar uma aplicação no local em VMs do Azure e ao utilizar a instância gerida da base de dados SQL do Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 832614c46f0269460245d081f20897b591e31fce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101572"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Migração de Contoso: Realojar a uma aplicação no local numa VM do Azure e a instância gerida da base de dados do SQL

Neste artigo, Contoso migra a sua aplicação da SmartHotel360 VM de front-end para uma VM do Azure com o serviço Azure Site Recovery. Contoso também migra a base de dados de aplicação para a instância gerida da base de dados SQL do Azure.

> [!NOTE]
> Instância de gerida de base de dados de SQL do Azure está atualmente em pré-visualização.

Este artigo é um de uma série de artigos que documenta como a empresa fictícia Contoso migra os respetivos recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e uma série de cenários que ilustram como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Cenários de crescem em complexidade. Artigos serão adicionados à série ao longo do tempo.


**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Descrição geral da estratégia de migração da Contoso, a série de artigos e as aplicações de exemplo que são utilizadas da série. | Disponível
[Artigo 2: Implementar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Contoso prepara a sua infraestrutura no local e a respetiva infraestrutura do Azure para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avalie os recursos no local para migração para o Azure](contoso-migration-assessment.md) | Contoso é executada uma avaliação da sua aplicação de SmartHotel de duas camadas no local em execução no VMware. Contoso avalia as VMs de aplicação utilizando o [do Azure Migrate](migrate-overview.md) serviço. Contoso avalia a base de dados do SQL Server de aplicação utilizando [Assistente de migração de dados](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
Artigo 4: Realojar a uma aplicação numa VM do Azure e a instância gerida da base de dados do SQL | Contoso é executada uma migração lift-and-shift para o Azure para a sua aplicação de SmartHotel no local. Contoso migra a aplicação de VM de front-end utilizando [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migra a base de dados de aplicação para uma instância de gerida de base de dados do Azure SQL utilizando o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Este artigo
[5 do artigo: Realojar a uma aplicação em VMs do Azure](contoso-migration-rehost-vm.md) | Contoso migra seu aplicativo de SmartHotel VMs para as VMs do Azure com o serviço de recuperação de sites. | Disponível
[Artigo 6: Realojar a uma aplicação em VMs do Azure e, num grupo de Disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migra a aplicação de SmartHotel. A Contoso utiliza o Site Recovery para migrar as VMs da aplicação. Ele usa o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server que está protegido por um grupo de Disponibilidade AlwaysOn. | Disponível
[Artigo 7: Realojar uma aplicação do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | Contoso é concluída uma migração lift-and-shift de seu aplicativo de osTicket do Linux para VMs do Azure com o Site Recovery. | Disponível
[Artigo 8: Realojar uma aplicação do Linux em VMs do Azure e base de dados do Azure para MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migra sua osTicket de aplicação do Linux para VMs do Azure com o Site Recovery. Ele migra a base de dados de aplicação à base de dados do Azure para MySQL com o MySQL Workbench. | Disponível
[Artigo 9: Refatorizar uma aplicação numa aplicação web do Azure e base de dados do Azure SQL](contoso-migration-refactor-web-app-sql.md) | Contoso migra sua SmartHotel de aplicação para uma aplicação web do Azure e migra a base de dados de aplicação para uma instância de servidor SQL do Azure. | Disponível
[Artigo 10: Refatorizar uma aplicação do Linux numa aplicação web do Azure e base de dados do Azure para MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migra sua osTicket de aplicação do Linux para uma aplicação web do Azure em vários sites. A aplicação web está integrada com o GitHub para a entrega contínua. Contoso migra a base de dados de aplicação para uma base de dados do Azure para a instância do MySQL. | Disponível
[Artigo 11: Refatorar o Team Foundation Server nos serviços de DevOps do Azure](contoso-migration-tfs-vsts.md) | Contoso efetua a migração de sua implantação do Team Foundation Server no local ao migrar para serviços do Azure DevOps no Azure. | Disponível
[Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados do Azure SQL](contoso-migration-rearchitect-container-sql.md) | Contoso migra sua SmartHotel de aplicação para o Azure e, em seguida, rearchitects a aplicação. Contoso rearchitects a camada de web de aplicação como um contentor do Windows e rearchitects a base de dados de aplicação ao utilizar a base de dados do Azure SQL. | Disponível
[Artigo 13: Recriar uma aplicação no Azure](contoso-migration-rebuild.md) | Contoso recria o seu aplicativo de SmartHotel com uma variedade de capacidades do Azure e serviços, incluindo o serviço de aplicações do Azure, Azure Kubernetes Service, as funções do Azure, serviços cognitivos do Azure e Azure Cosmos DB. | Disponível
[Artigo 14: Dimensionar uma migração para o Azure](contoso-migration-scale.md) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível




Pode transferir a aplicação da SmartHotel360 de exemplo utilizada neste artigo [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Fatores comerciais

Da Contoso equipa de liderança de TI tem trabalhado em estreita colaboração com parceiros de negócios da empresa para compreender o que a empresa quer alcançar com esta migração:

- **Resolver o crescimento do negócio**: Contoso está a crescer. Como resultado, pressão aumentou em sistemas no local e em infra-estrutura da empresa.
- **Aumentar a eficiência**: A Contoso precisa de remover procedimentos desnecessários e para simplificar processos para seus desenvolvedores e usuários. As necessidades de negócio IT para ser rápido e de não desperdício de tempo ou dinheiro, portanto, a empresa podem apresentar mais rapidamente sobre os requisitos dos clientes.
- **Aumente a agilidade**:  Contoso TI tem de ser mais reativos para com as necessidades do negócio. Tem de ser capaz de reagir mais rapidamente do que as alterações que ocorrem no mercado da empresa para ser bem-sucedido numa economia global. IT na Contoso não tem de obter na forma como ou se tornar um bloqueador de negócios.
- **Dimensionamento**: Como que a empresa o negócio cresce com êxito, TI da Contoso tem de fornecer sistemas que podem aumentar ao ritmo mesmo.

## <a name="migration-goals"></a>Objetivos de migração

A equipe de cloud Contoso identificou objetivos para essa migração. A empresa utiliza os objetivos de migração para determinar o melhor método de migração.

- Após a migração, a aplicação no Azure deve ter as mesmas capacidades de desempenho que a aplicação tem-se hoje mesmo no ambiente de VMWare no local da Contoso. Mover para a nuvem não significa que o desempenho de aplicações é menos crítico.
- Contoso não quer investir na aplicação. A aplicação é críticos e importantes para os negócios, mas a Contoso pretende simplesmente mover a aplicação em sua forma atual para a cloud.
- Tarefas de administração de base de dados devem ser minimizadas após a aplicação é migrada.
- Contoso não quer utilizar uma base de dados do SQL do Azure para esta aplicação. Ele está à procura de alternativas.


## <a name="solution-design"></a>Design da solução

Depois de fixar-se para baixo de seus objetivos e requisitos, a Contoso projeta e revisões de uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que será utilizada para a migração.

### <a name="current-architecture"></a>Arquitetura atual 

- A Contoso tiver um datacenter principal (**contoso-datacenter**). O Centro de dados está localizado na cidade de Nova Iorque no Leste dos Estados Unidos.
- A Contoso tiver três ramos locais adicionais nos Estados Unidos.
- O datacenter principal está ligado à internet com uma ligação de Ethernet do Metro (500 MBps) de fibra.
- Cada ramificação é localmente ligada à internet através de ligações de classe empresarial com túneis de IPsec VPN para o datacenter principal. A configuração permite que toda a rede da Contoso permanentemente estar conectados e otimiza a conectividade à internet.
- O Centro de dados principal é totalmente virtualizado com o VMware. A Contoso tiver dois anfitriões de Virtualização de ESXi 6.5 que são geridos pelo vCenter Server 6.5.
- A Contoso utiliza o Active Directory para gestão de identidades. A Contoso utiliza servidores DNS na rede interna.
- Contoso tem um controlador de domínio no local (**contosodc1**).
- Os controladores de domínio ser executadas nas VMs de VMware. Executam os controladores de domínio em ramos locais em servidores físicos.
- A aplicação da SmartHotel360 é a em camadas em duas VMs (**WEBVM** e **SQLVM**) que estão localizados num anfitrião ESXi do VMware versão 6.5 (**contosohost1.contoso.com**). 
- O ambiente do VMware é gerido pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução numa VM.

![Arquitetura de Contoso atual](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

### <a name="proposed-architecture"></a>Arquitetura proposta

Neste cenário, a Contoso deseja migrar a sua aplicação de viagens de duas camadas no local da seguinte forma:

- Migrar a base de dados de aplicação (**SmartHotelDB**) para uma instância de gerida de base de dados do Azure SQL.
- Migrar do front-end **WebVM** para uma VM do Azure.
- As VMs no local no Centro de dados Contoso serão encerradas quando a migração estiver concluída.

![Arquitetura do cenário](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="database-considerations"></a>Considerações sobre a base de dados

Como parte do processo de design da solução, a Contoso fez uma comparação de recursos entre a base de dados do Azure SQL e a instância gerida do SQL Server. As seguintes considerações ajudado a prepará-los para decidir tomar com a instância gerida.

- Gerido tem como objetivo instância entregar quase 100% de compatibilidade com a versão mais recente de SQL Server no local. A Microsoft recomenda a instância gerida para clientes que executam o SQL Server no local ou na VM de IaaS que pretendem migrar as suas aplicações para um serviço totalmente gerido com alterações mínimas.
- Contoso está planejando migrar um grande número de aplicações no local para o IaaS. Muitos deles são ISV fornecido. Contoso apercebe-se de que utilizar a instância gerida ajudará a garantir a compatibilidade de base de dados para estas aplicações, em vez de utilizar a base de dados do SQL que pode não ser suportado.
- Contoso pode simplesmente fazer uma migração lift-and-shift para a instância gerida com o serviço de migração de dados (DMS) totalmente automatizada. Com este serviço no local, Contoso pode reutilizá-lo para migrações de base de dados futuros.
- Instância gerida SQL suporta o SQL Server Agent, que é uma questão importante para a aplicação da SmartHotel360. A Contoso precisa este compatibilidade, caso contrário, terá que Refazer o design planos de manutenção necessários para a aplicação.
- Com o Software Assurance, Contoso pode trocar suas licenças existentes para tarifas com desconto no banco de dados instância gerida do SQL com o Azure Hybrid Benefit para o SQL Server. Isso pode permitir que a Contoso economizar até 30% na instância gerida.
- Instância gerida está totalmente contida na rede virtual, pelo que oferece um elevado nível de segurança e isolamento para os dados da Contoso. Contoso pode obter os benefícios da cloud pública, enquanto mantém o ambiente isolado a partir da Internet pública.
- Gerida suporta instância várias funcionalidades de segurança, incluindo a máscara de dados dinâmicos, sempre encriptado, segurança ao nível da linha e deteção de ameaças.


### <a name="solution-review"></a>Revisão de solução

Contoso avalia a estrutura proposta ao juntar-se de uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Profissionais de TI** |  WEBVM será movido para o Azure sem alterações, fazer a migração simples.<br/><br/> Instância gerida SQL suporta os objetivos e requisitos técnicos da Contoso.<br/><br/> Instância gerida irá fornecer 100% de compatibilidade com a sua implementação atual, ao movê-los para fora do SQL Server 2008 R2.<br/><br/>  Podem tirar partido de seus investimentos em Software Assurance e utilizar o benefício híbrido do Azure forSQL Server e no Windows Server.<br/><br/> Eles podem reutilizar o serviço de migração de base de dados para as migrações futuras adicionais.<br/><br/> Instância gerida SQL incorporou na tolerância a falhas que não precisa configura Contoso. Isto garante que a camada de dados já não é um ponto único de ativação pós-falha.
**Contras** | O WEBVM está a executar o Windows Server 2008 R2.  Embora este sistema operativo seja suportado pelo Azure, já não é plataforma suportada. [Saiba mais](https://support.microsoft.com/en-us/help/956893).<br/><br/> A camada web permanece um ponto único de ativação pós-falha com apenas WEBVM fornecer os serviços.<br/><br/> Contoso tem de continuar a suportar a camada de web de aplicação como uma VM, em vez de mudar para um serviço gerido, como o serviço de aplicações do Azure.<br/><br/> Para a camada de dados, instância gerida não pode ser a melhor solução se desejar personalizar o sistema operativo ou o servidor de base de dados Contoso, ou se pretende executar aplicações de terceiros, juntamente com o SQL Server. Executar o SQL Server numa VM do IaaS poderia fornecer essa flexibilidade. 

### <a name="migration-process"></a>Processo de migração

Contoso irá migrar os escalões web e os dados da sua aplicação da SmartHotel360 para o Azure, concluindo estes passos:

1. Contoso já tem a sua infraestrutura do Azure no local, pelo que apenas tem de adicionar alguns componentes do Azure específicos para este cenário.
2. A camada de dados será migrada com o serviço de migração de dados. O serviço de migração de dados liga-se para a VM do SQL Server no local através de uma ligação de VPN de site a site entre o datacenter da Contoso e o Azure. Em seguida, o serviço de migração de dados migra a base de dados.
3. A camada web será migrada através de uma migração lift-and-shift utilizando a recuperação de Site. O processo envolve a preparação do ambiente de VMware no local, configurar e ativar a replicação e para migrar as VMs por pós-falha das mesmas para o Azure.

     ![Arquitetura de migração](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

### <a name="azure-services"></a>Serviços do Azure

Serviço | Descrição | Custo
--- | --- | ---
[Serviço de migração de base de dados](https://docs.microsoft.com/azure/dms/dms-overview) | O serviço de migração de base de dados permite a migração direta de várias origens de base de dados para plataformas de dados do Azure com o período de indisponibilidade mínimo. | Saiba mais sobre [regiões suportadas](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) e [preços do serviço de migração de base de dados](https://azure.microsoft.com/pricing/details/database-migration/).
[Instância gerida da base de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | A instância gerida é um serviço de base de dados gerida que representa uma instância do SQL Server completamente gerida na cloud do Azure. Ele utiliza o mesmo código que a versão mais recente do motor de base de dados do SQL Server e tem as funcionalidades mais recentes, melhoramentos de desempenho e patches de segurança. | Utilizar uma SQL Database Managed Instance em execução no Azure incorre em encargos com base na capacidade. Saiba mais sobre [preços de instância gerida](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço Site Recovery orquestra e gere a migração e recuperação após desastre para VMs do Azure e VMs e servidores físicos no local.  | Durante a replicação para o Azure, são cobradas taxas de armazenamento do Azure.  VMs do Azure são criadas e incorrer em custos, quando ocorre a ativação pós-falha. Saiba mais sobre [custos de recuperação de sites e preços](https://azure.microsoft.com/pricing/details/site-recovery/).

 

## <a name="prerequisites"></a>Pré-requisitos

Contoso e outros utilizadores têm de cumprir os seguintes pré-requisitos para este cenário:

Requisitos | Detalhes
--- | ---
**Inscrever-se na pré-visualização de instância gerida** | Têm de estar inscritos na pré-visualização pública limitada a instância de gerida da base de dados do SQL. Precisa de uma subscrição do Azure para [Inscreva-se](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Inscrição pode demorar alguns dias para concluir, por isso, certifique-se para se inscrever antes de começar a implementar este cenário.
**Subscrição do Azure** | Deve já tiver criado uma subscrição ao efetuar a avaliação no primeiro artigo desta série. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se criar uma conta gratuita, será o administrador da sua subscrição e poderá executar todas as ações.<br/><br/> Se utilizar uma subscrição existente e não for o administrador da subscrição, terá de trabalhar com o administrador para lhe atribuir permissões de proprietário ou contribuinte.<br/><br/> Se precisar de permissões mais granulares, veja [utilizar o controlo de acesso baseado em funções para gerir o acesso do Site Recovery](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Recuperação de sites (no local)** | A instância do servidor vCenter no local deve estar a executar a versão 5.5, 6.0 ou 6.5<br/><br/> Um anfitrião ESXi a executar a versão 5.5, 6.0 ou 6.5<br/><br/> Um ou mais VMs de VMware em execução no anfitrião ESXi.<br/><br/> As VMs têm de cumprir [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Suportado [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) configuração.
**Serviço de migração de base de dados** | Para o serviço de migração de base de dados, é necessário um [compatível no dispositivo VPN local](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Tem de ser capaz de configurar o dispositivo VPN no local. Tem de ter um endereço IPv4 público de face externa. O endereço não pode estar localizado atrás de um dispositivo NAT.<br/><br/> Certifique-se de que ter acesso à base de dados do SQL Server no local.<br/><br/> Firewall do Windows deve ser capaz de acessar o motor de base de dados de origem. Saiba como [configurar a Firewall do Windows para acesso ao motor de base de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Se existir uma firewall na frente do seu computador de base de dados, adicione regras para permitir o acesso ao banco de dados e arquivos por meio da porta SMB 445.<br/><br/> As credenciais utilizadas para ligar à instância do SQL Server de origem e que a instância gerida de destino têm de ser membros da função de servidor sysadmin.<br/><br/> Precisa de uma rede partilhar na sua base de dados no local que o serviço de migração de base de dados pode usar para criar cópias de segurança da base de dados de origem.<br/><br/> Certifique-se de que a conta de serviço a executar a instância do SQL Server de origem tem permissões de escrita na partilha de rede.<br/><br/> Tome nota de um utilizador do Windows e a palavra-passe que tem permissões de controlo total na partilha de rede. O serviço de migração de base de dados representa estas credenciais de utilizador para carregar ficheiros de cópia de segurança para o contentor de armazenamento do Azure.<br/><br/> O processo de instalação do SQL Server Express define o protocolo de TCP/IP para **desativado** por predefinição. Certifique-se de que está ativada.

## <a name="scenario-steps"></a>Passos do cenário

Eis como Contoso planos configurar a implementação:

> [!div class="checklist"]
> * **Passo 1: Configurar uma instância gerida do SQL da base de dados**: A Contoso precisa de uma instância gerida previamente criada para o qual irá migrar a base de dados do SQL Server no local.
> * **Passo 2: Preparar o serviço de migração de base de dados**: Contoso tem de registar o fornecedor de migração de base de dados, criar uma instância e, em seguida, crie um projeto de serviço de migração de base de dados. Contoso também tem de configurar uma assinatura de acesso partilhado (SAS) Uniform Resource Identifier (URI) para o serviço de migração de base de dados. Um URI de SAS fornece acesso delegado a recursos na conta de armazenamento da Contoso, para que a Contoso pode conceder permissões limitadas a objetos de armazenamento. Contoso configura um URI de SAS, para que o serviço de migração de base de dados pode aceder ao contentor de conta de armazenamento para o qual o serviço carrega os ficheiros de cópia de segurança do SQL Server.
> * **Passo 3: Preparar o Azure para o Site Recovery**: Contoso tem de criar uma conta de armazenamento para armazenar dados replicados para o Site Recovery. Ele também tem de criar um cofre dos serviços de recuperação do Azure.
> * **Passo 4: Preparar o VMware no local para o Site Recovery**: Contoso vai preparar contas para instalação de deteção e o agente VM ligar a VMs do Azure após a ativação pós-falha.
> * **Passo 5: Replicar VMs**: Para configurar a replicação, a Contoso configurar os ambientes de origem e destino de recuperação de sites, configura uma política de replicação e começa a replicar VMs para o armazenamento do Azure.
> * **Passo 6: Migrar a base de dados com o serviço de migração de base de dados**: Contoso migra a base de dados.
> * **Passo 7: Migrar as VMs com o Site Recovery**: Contoso é executada uma ativação pós-falha de teste para verificar se tudo está a funcionar. Em seguida, a Contoso é executada uma ativação pós-falha completa para migrar as VMs para o Azure.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>Passo 1: Preparar uma instância gerida da base de dados SQL

Para configurar uma instância de gerida de base de dados do Azure SQL, a Contoso precisa de uma sub-rede que cumpre os seguintes requisitos:

- A sub-rede tem de estar dedicada. Tem de estar vazio e não pode conter qualquer outro serviço em nuvem. A sub-rede não pode ser uma sub-rede de gateway.
- Depois de criar a instância gerida, Contoso não deve adicionar recursos à sub-rede.
- A sub-rede não pode ter um grupo de segurança de rede associado a ele.
- A sub-rede tem de ter uma definidas pelo utilizador (UDR) rota tabela de encaminhamento. O único caminho atribuído deve ser 0.0.0.0/0 próximo salto de internet. 
- DNS personalizado opcional: Se o DNS personalizado é especificado na rede virtual do Azure, o endereço IP de resoluções de recursiva do Azure (por exemplo, 168.63.129.16) tem de ser adicionado à lista. Saiba como [configurar o DNS personalizado para uma instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- A sub-rede não pode ter um ponto final de serviço (armazenamento ou SQL) associado a ele. Pontos finais de serviço devem ser desativados na rede virtual.
- A sub-rede tem de ter um mínimo de 16 endereços IP. Saiba como [tamanho de sub-rede da instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration).
- No ambiente de híbrida da Contoso, definições de DNS personalizadas são necessárias. Contoso configura as definições de DNS para utilizar um ou mais servidores de DNS do Azure da empresa. Saiba mais sobre [personalização de DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Configurar uma rede virtual para a instância gerida

Administradores de contoso, configure a rede virtual da seguinte forma: 

1. Eles criam uma nova rede virtual (**VNET-SQLMI-EU2**) na região E.U.A. Leste 2 primária. Ele adiciona a rede virtual para o **ContosoNetworkingRG** grupo de recursos.
2. Eles atribuir um espaço de endereços de 10.235.0.0/24. Eles garantem que o intervalo não se sobrepõe quaisquer outras redes na sua empresa.
3. Eles adicionar duas sub-redes para a rede:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Esta sub-rede é utilizada para anexar um diretório para a instância gerida.

      ![Instância - gerida criar rede virtual](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

4. Depois da rede virtual e sub-redes são implementadas, eles emparelhamento de redes da seguinte forma:

    - Itens de mesmo nível **VNET-SQLMI-EUS2** com **VNET-HUB-EUS2** (rede virtual do concentrador nos e.u.a. Leste 2).
    - Itens de mesmo nível **VNET-SQLMI-EUS2** com **VNET-PROD-EUS2** (a rede de produção).

      ![Peering de rede](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

5. Configuram as definições de DNS personalizadas. Pontos DNS para controladores de domínio do Azure da Contoso. O DNS do Azure é secundário. Os controladores de domínio do Azure de Contoso estão localizados da seguinte forma:

    - Localizado na **PROD-DC-EUS2** sub-rede, na rede de produção E.U.A. Leste 2 (**VNET-PROD-EUS2**)
    - **CONTOSODC3** endereço: 10.245.42.4
    - **CONTOSODC4** endereço: 10.245.42.5
    - Resolvedor de DNS do Azure: 168.63.129.16

      ![Servidores DNS da rede](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*Precisa de mais ajuda?*

- Obtenha uma visão geral dos [mi](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance).
- Saiba como [criar uma rede virtual para uma instância gerida do SQL da base de dados](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration).
- Saiba como [configurar o peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering).
- Saiba como [atualizar as definições de DNS do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

### <a name="set-up-routing"></a>Configurar o encaminhamento

A instância gerida é colocada numa rede virtual privada. A Contoso precisa de uma tabela de rotas para a rede virtual comunicar com o serviço de gestão do Azure. Se a rede virtual não é possível comunicar com o serviço que gere-lo, a rede virtual fica inacessível.

Contoso considera esses fatores:

- A tabela de rotas contém um conjunto de regras (rotas) que especificam como devem ser encaminhados os pacotes enviados a partir da instância gerida na rede virtual.
- A tabela de rotas é associada a sub-redes em que são implementadas instâncias geridas. Cada pacote que sai de uma sub-rede é processado com base na tabela de rotas associada.
- Uma sub-rede pode ser associada a tabela de rotas apenas um.
- Não existem não existem custos adicionais para a criação de tabelas de rotas no Microsoft Azure.

  Para configurar o encaminhamento Contoso admins, faça o seguinte:

1. Eles criam uma tabela UDR (rota) na **ContosoNetworkingRG** grupo de recursos.

    ![Tabela de rotas](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Para atender aos requisitos de instância gerida, depois da tabela de rotas (**MIRouteTable**) é implementado, adicionar uma rota que tem um prefixo de endereço de 0.0.0.0/0. O **tipo de próximo salto** opção estiver definida como **Internet**.

    ![Prefixo da tabela de rota](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. eles associar a tabela de rotas com o **SQLMI-DB-EUS2** sub-rede (na **VNET-SQLMI-EUS2** rede). 

    ![Sub-rede de tabela de rota](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*Precisa de mais ajuda?*

Saiba como [definir rotas para uma instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

### <a name="create-a-managed-instance"></a>Criar uma Instância Gerida

Agora, os administradores da Contoso podem aprovisionar uma instância gerida do SQL da base de dados:

1. Uma vez que a instância gerida atende uma aplicação de negócio, que implementam a instância gerida na região E.U.A. Leste 2 primária da empresa. Eles adicionar a instância gerida para o **ContosoRG** grupo de recursos.
2. Selecionar um escalão de preço, a computação de tamanho e o armazenamento para a instância. Saiba mais sobre [preços de instância gerida](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Instância Gerida](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Depois de implementar a instância gerida, dois novos recursos aparecem na **ContosoRG** grupo de recursos:

    - Um cluster virtual, no caso Contoso tem várias instâncias geridas.
    - Base de dados do SQL Server de instância gerida. 

      ![Instância Gerida](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*Precisa de mais ajuda?*

Saiba como [aprovisionar uma instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-migration-service"></a>Passo 2: Preparar o serviço de migração de base de dados

Para preparar o serviço de migração de base de dados, os administradores da Contoso tem de fazer algumas coisas:

- Registe o fornecedor de serviço de migração de base de dados no Azure.
- Fornece o serviço de migração de base de dados com o acesso ao armazenamento do Azure para carregar os ficheiros de cópia de segurança que são utilizados para migrar uma base de dados. Para fornecer acesso ao armazenamento do Azure, é criar um contentor de armazenamento de Blobs do Azure. Elas geram um URI de SAS para o contentor de armazenamento de Blobs. 
- Crie um projeto de serviço de migração de base de dados.

Em seguida, eles concluírem os seguintes passos:

1. Eles registram o fornecedor de migração de base de dados na sua subscrição.
    ![Serviço de migração de base de dados - registar](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. É criar um contentor de armazenamento de Blobs. Contoso gera um URI de SAS para que o serviço de migração de base de dados pode acessá-lo.

    ![Serviço de migração de base de dados: gerar um URI de SAS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Eles criam uma instância de serviço de migração de base de dados. 

    ![Serviço de migração de base de dados - criar instância](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Eles colocam a instância do serviço de migração de base de dados no **PROD-DC-EUS2** sub-rede da **VNET-PROD-DC-EUS2** rede virtual.
    - O serviço de migração de base de dados é colocado aqui, como o serviço tem de estar numa rede virtual que pode aceder a VM do servidor de SQL no local através de um gateway VPN.
    - O **VNET-PROD-EUS2** é configurar o peering **VNET-HUB-EUS2** e está autorizado a utilizar gateways remotos. O **utilizar gateways remotos** opção garante que o serviço de migração de base de dados pode comunicar conforme necessário.

        ![Serviço de migração de base de dados - configurar rede](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*Precisa de mais ajuda?*

- Saiba como [configurar o serviço de migração de base de dados](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Saiba como [criar e utilizar a SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Passo 3: Preparar o Azure para o serviço Site Recovery

Vários elementos do Azure são necessários para a Contoso configurar a recuperação de Site para a migração da sua VM de escalão web (**WEBMV**):

- Uma rede virtual no qual estão localizados recursos com ativação pós-falha.
- Uma conta de armazenamento para armazenar dados replicados. 
- Um cofre dos serviços de recuperação no Azure.

Administradores de contoso, configure o Site Recovery da seguinte forma:

1. Uma vez que a VM é um front-end de publicação na web ' para a aplicação da SmartHotel360, Contoso efetua a ativação pós-falha da VM para a sua rede de produção existente (**VNET-PROD-EUS2**) e a sub-rede (**PROD-FE-EUS2**). A rede e sub-rede estão localizados na região E.U.A. Leste 2 primária. Configure a rede de Contoso quando ele [implementado a infraestrutura do Azure](contoso-migration-infrastructure.md).
2. Eles criam uma conta de armazenamento (**contosovmsacc20180528**). A Contoso utiliza uma conta para fins gerais. Contoso seleciona o armazenamento standard e replicação de armazenamento localmente redundante.

    ![Recuperação de sites - criar conta de armazenamento](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Com a conta de armazenamento e de rede no local, ser criar um cofre (**ContosoMigrationVault**). Contoso coloca o Cofre na **ContosoFailoverRG** grupo de recursos na região E.U.A. Leste 2 primária.

    ![Os serviços de recuperação - criar Cofre](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*Precisa de mais ajuda?*

Saiba como [configurar o Azure para o Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Passo 4: Preparar o VMware no local para o Site Recovery

Para preparar o VMware para o Site Recovery, os administradores da Contoso tem de concluir estas tarefas:

- Prepare uma conta no vCenter Server instância ou vSphere ESXi no anfitrião. A conta automatiza a deteção de VMS.
- Prepare uma conta que permite a instalação automática do serviço de mobilidade nas VMs de VMware que a Contoso pretende replicar.
- Prepare VMs no local para ligar a VMs do Azure quando forem criadas após a ativação pós-falha.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a deteção automática

O Site Recovery precisa de ter acesso a servidores VMware para:

- Detetar automaticamente VMs. É necessário um mínimo de uma conta só de leitura.
- Orquestrar a replicação, a ativação pós-falha e a reativação pós-falha. A Contoso precisa de uma conta que pode executar operações como criar e remover discos e a ativar VMs.

Administradores de contoso configurar a conta ao concluir estas tarefas:

1. Cria uma função ao nível do vCenter.
2. Atribui as permissões necessárias para essa função.

*Precisa de mais ajuda?*

Saiba como [criar e atribuir uma função para a deteção automática](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para instalação do serviço de mobilidade

O serviço de mobilidade tem de ser instalado na VM que a Contoso pretende replicar. Contoso considera esses fatores sobre o serviço de mobilidade:

- Site Recovery pode fazer uma instalação push automática deste componente quando Contoso permite a replicação para a VM.
- Para instalação push automática do, Contoso tem de preparar uma conta que utiliza o Site Recovery para aceder à VM.
- Esta conta é especificada quando a replicação está configurada na consola do Azure.
- Contoso tem de ter um domínio ou conta local com permissões para instalar na VM.

*Precisa de mais ajuda*

Saiba como [criar uma conta para a instalação push do serviço de mobilidade](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Após a ativação pós-falha para o Azure, a Contoso quer ser capaz de ligar as VMs replicadas no Azure. Para ligar as VMs replicadas no Azure, os administradores da Contoso tem de concluir algumas tarefas na VM no local antes da migração: 

1. Para obter acesso através da internet, de ativar o RDP na VM no local antes da ativação pós-falha. Eles garantem que as regras TCP e UDP estão adicionadas para o **pública** perfil e que o RDP é permitido na **Firewall do Windows** > **aplicações permitidas** para todos os perfis.
2. Para obter acesso através de VPN de site a site da Contoso, eles ative o RDP na máquina no local. Elas permitem que o RDP na **Firewall do Windows** > **aplicações e funcionalidades permitidas** para **domínio e privadas** redes.
3. Eles definir a política de SAN do sistema operacional na VM no local para **OnlineAll**.

Os administradores da Contoso também é necessário verificar esses itens quando executam uma ativação pós-falha:

- Não deve haver nenhuma atualização do Windows pendentes na VM quando é acionada uma ativação pós-falha. Se as atualizações do Windows estão pendentes, os utilizadores Contoso não é possível iniciar sessão para a máquina virtual até que a atualização esteja concluída.
- Após a ativação pós-falha, os administradores devem verificar **diagnósticos de arranque** para ver uma captura de ecrã da VM. Se eles não é possível ver o diagnóstico de arranque, deve verificar que a VM está em execução e, em seguida, reveja [sugestões de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure"></a>Passo 5: Replicar VMs no local para o Azure

Antes de executar uma migração para o Azure, os administradores da Contoso tem de configurar e ativar a replicação para a VM no local.

### <a name="set-a-replication-goal"></a>Definir um objetivo de replicação

1. No cofre, sob o nome do cofre (**ContosoVMVault**), definir um objetivo de replicação (**introdução** > **Site Recovery**  >   **Preparar infraestrutura**).
2. Especifica que as máquinas estão localizados no local, que são VMs do VMware, replicar para o Azure.

    ![Preparar infraestrutura - objetivo de proteção](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

Para continuar, os administradores da Contoso confirmar que eles completaram planeamento da implementação. Eles selecionam **Sim, já o fiz**. Nesta implementação, Contoso está a migrar uma única VM, o planeamento da implementação não é necessária.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Agora, os administradores de Contoso configurar o ambiente de origem. Para configurar o seu ambiente de origem, eles transferir um modelo OVF e utilizá-lo para implementar o servidor de configuração e seus componentes associados como de elevada disponibilidade, a VM de VMware no local. Os componentes no servidor incluem:

- O servidor de configuração coordena as comunicações entre a infraestrutura no local e o Azure. O servidor de configuração gere a replicação de dados.
- O servidor de processos atua como um gateway de replicação. O servidor de processos:
    - Recebe dados de replicação.
    - Otimiza a data de replicação ao utilizar a colocação em cache, compressão e encriptação.
    - Envia a data de replicação para armazenamento do Azure.
- O servidor de processos também instala o serviço de mobilidade nas VMs que serão replicadas. O servidor de processo efetua a deteção automática de VMs de VMware no local.
- Depois do servidor de configuração VM é criada e iniciada, a Contoso registra o servidor no cofre.

Para configurar a origem de Contoso os administradores de ambiente, efetue o seguinte:

1. Eles transferem o modelo OVF a partir do portal do Azure (**preparar infraestrutura** > **origem** > **servidor de configuração**).
    
    ![Adicionar um servidor de configuração](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Eles importam o modelo para o VMware, para criar e implementar a VM.

    ![Implementar modelo OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Quando ativam a VM pela primeira vez, ele começa numa experiência de instalação do Windows Server 2016. Eles aceitar o contrato de licença e introduz uma palavra-passe de administrador.
4. Quando a instalação estiver concluída, iniciam sessão na VM como administrador. Na primeira vez início de sessão, a ferramenta de configuração do Azure Site Recovery é executado automaticamente.
5. Na ferramenta de configuração de recuperação de Site, eles introduza um nome a utilizar para registar o servidor de configuração no cofre.
6. A ferramenta verifica a ligação da VM para o Azure. Depois da ligação é estabelecida, selecione **iniciar sessão** para iniciar sessão para a subscrição do Azure. As credenciais têm de ter acesso ao Cofre no qual o servidor de configuração está registado. 

    ![Registar o servidor de configuração](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reiniciada. Iniciam sessão na máquina novamente. O Assistente de gestão de servidor de configuração é iniciado automaticamente.
8. No assistente, eles selecionam o NIC para receber o tráfego de replicação. Não é possível alterar esta definição depois de estar configurada.
9. Selecionar a subscrição, grupo de recursos e cofre dos serviços de recuperação no qual pretende registar o servidor de configuração.

    ![Selecione o Cofre dos serviços de recuperação](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Eles transferir e instala o servidor MySQL e o VMWare PowerCLI. Em seguida, eles valida as definições do servidor.
11. Após a validação, eles introduza o FQDN ou endereço IP do vCenter Server instância ou vSphere no anfitrião. Deixe a porta predefinida e introduza um nome a apresentar para a instância de servidor vCenter no Azure.
12. Especifica a conta que criou anteriormente para que o Site Recovery pode detetar automaticamente as VMs de VMware que estão disponíveis para replicação. 
13. Ele introduzir credenciais, para que o serviço de mobilidade é instalado automaticamente quando a replicação está ativada. Para máquinas Windows, a conta tem permissões de administrador local nas VMs. 

    ![Configurar servidor vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Quando o registo estiver concluído, no portal do Azure, eles verificarem novamente que o servidor de configuração e o servidor VMware estão listados no **origem** página no cofre. Deteção pode demorar 15 minutos ou mais. 
8. Site Recovery liga-se aos servidores VMware com as definições especificadas e Deteta as VMs.

### <a name="set-up-the-target"></a>Configurar o destino

Agora, os administradores de Contoso configurar o ambiente de replicação de destino:

1. Na **preparar infraestrutura** > **destino**, eles selecionam as definições de destino.
2. Recuperação de sites verifica que existe uma conta de armazenamento e rede de destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Quando a origem e destino estão configurados, os administradores da Contoso criar uma política de replicação e associa a política com o servidor de configuração:

1. Na **preparar infraestrutura** > **definições de replicação** > **política de replicação** >  **criar e Associar**, eles criam a **ContosoMigrationPolicy** política.
2. Se utilizarem as predefinições:
   - **Limiar RPO**: Predefinição de 60 minutos. Este valor define com que frequência são criados pontos de recuperação. Será gerado um alerta se a replicação contínua exceder este limite.
   - **Retenção do ponto de recuperação**: Predefinição de 24 horas. Este valor especifica quanto tempo dura o período de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela.
   - **Frequência de instantâneos consistentes com a aplicação**: Padrão de 1 hora. Este valor Especifica a frequência com que os instantâneos consistentes com aplicações são criados.
 
     ![Política de replicação - criar](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. A política é associada automaticamente ao servidor de configuração. 

    ![Política de replicação - associar](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*Precisa de mais ajuda?*

- Pode ler um passo a passo completa dos seguintes passos no [configurar a recuperação após desastre das VMs de VMware no local](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Instruções detalhadas estão disponíveis para ajudá-lo [configurar o ambiente de origem](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implementar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [configurar as definições de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Ativar a replicação

Agora, os administradores da Contoso podem iniciar a replicação WebVM.

1. Na **replicar aplicação** > **origem** > **replicar**, eles selecionam as definições de origem.
2. Eles indicam que querem ativar máquinas virtuais, selecione a instância de servidor vCenter e definir o servidor de configuração.

    ![Ativar a replicação - origem](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Especifica as definições de destino, incluindo o grupo de recursos e a rede em que a VM do Azure estarão localizada após a ativação pós-falha. Especifica a conta de armazenamento no qual serão armazenados os dados replicados.

    ![Ativar a replicação - destino](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Eles selecionam **WebVM** para replicação. Site Recovery instala o serviço de mobilidade em cada VM quando a replicação está ativada. 

    ![Ativar a replicação – selecione a VM](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Eles verificam a política de replicação correta está selecionada e, ativa a replicação **WEBVM**. Controlar o progresso de replicação em **tarefas**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.
6. Na **Essentials** no portal do Azure, eles podem ver o estado para as VMs que estão a replicar para o Azure:

    ![Vista da infraestrutura](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*Precisa de mais ajuda?*

Pode ler um passo a passo completa dos seguintes passos no [ativar a replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database"></a>Passo 6: Migrar a base de dados 

Os administradores da Contoso tem de criar um projeto de serviço de migração de base de dados e, em seguida, migre a base de dados.

### <a name="create-a-database-migration-service-project"></a>Criar um projeto de serviço de migração de base de dados

1. É criar um projeto de serviço de migração de base de dados. Eles selecionam a **SQL Server** tipo de servidor de origem e **instância gerida da base de dados SQL do Azure** como destino.

     ![Serviço de migração de base de dados - novo projeto de migração](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. É aberto o Assistente de migração.

### <a name="migrate-the-database"></a>Migrar a base de dados 

1. No Assistente de migração, eles especificam a VM de origem onde está localizada a base de dados no local. Eles introduza as credenciais para aceder a base de dados.

    ![Serviço de migração de base de dados - detalhes da origem](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. eles selecionam a base de dados para migrar (**SmartHotel.Registration**):

    ![Serviço de migração de base de dados - bases de dados de selecionar origem](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Para o destino, ele introduzir o nome da instância gerida no Azure e as credenciais de acesso.

    ![Serviço de migração de base de dados - detalhes do destino](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Na **nova atividade** > **executar migração**, eles especificam definições para executar a migração:
    - Credenciais de origem e de destino.
    - A base de dados para migrar.
    - A partilha de rede criada na VM no local. O serviço de migração de base de dados leva as cópias de segurança de origem para esta partilha. 
        - A conta de serviço que executa a instância do SQL Server de origem tem de ter permissões de escrita nesta partilha.
        - O caminho do FQDN para a partilha tem de ser utilizado.
    - O URI de SAS que fornece o serviço de migração de base de dados com o acesso ao contentor de conta de armazenamento para o qual o serviço carrega os ficheiros de cópia de segurança para a migração.

        ![Serviço de migração de base de dados - configurar definições de migração](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Guardar as definições de migração e, em seguida, executar a migração.
6. Na **descrição geral**, eles monitoram o estado de migração.

    ![Serviço de migração de base de dados - Monitor](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Quando a migração estiver concluída, eles Certifique-se de que as bases de dados de destino existem na instância gerida.

    ![Serviço de migração de base de dados – verificar a migração de base de dados](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm"></a>Passo 7: Migrar a VM

Os administradores de contoso executados uma rápida ativação pós-falha de teste e, em seguida, migrar a VM.

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Antes de migrar uma ativação pós-falha de teste WEBVM, ajuda a garantir que tudo funciona conforme esperado. Os administradores concluam os seguintes passos:

1. Execute uma ativação pós-falha de teste para o ponto mais recente no tempo (**processado mais recentemente**).
2. Eles selecionam **encerrar a máquina antes de iniciar a ativação pós-falha**. Com esta opção selecionada, o Site Recovery tenta encerrar a VM de origem antes de ele aciona a ativação pós-falha. Ativação pós-falha continua, mesmo que o encerramento falhe. 
3. Execuções de ativação pós-falha de teste: 
    1. Uma verificação dos pré-requisitos é executado para certificar-se de que todas as condições necessárias para a migração estão em vigor.
    2. A ativação pós-falha processa os dados para que possa ser criada uma VM do Azure. Se o ponto de recuperação mais recente estiver selecionado, é criado um ponto de recuperação dos dados.
    3.  VM do Azure é criada ao utilizar os dados processados no passo anterior.
3. Quando a ativação pós-falha estiver concluída, a réplica é apresentada VM do Azure no portal do Azure. eles Certifique-se de que tudo o que está a funcionar corretamente: a VM tem um tamanho adequado, está ligado à rede correta e está em execução. 
4. Depois de verificar a ativação pós-falha de teste, eles limpar a ativação pós-falha e registram todas as observações. 

### <a name="migrate-the-vm"></a>Migrar a VM

1. Depois de verificar que a ativação pós-falha de teste funcionou como esperado, os administradores da Contoso crie um plano de recuperação para a migração e adicione WEBVM ao plano de:

     ![Criar plano de recuperação - selecionar itens](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Eles são executados uma ativação pós-falha no plano de selecionar o ponto de recuperação mais recente. Especifica que o Site Recovery deve tentar encerrar a VM no local antes de ele aciona a ativação pós-falha.

    ![Ativação pós-falha](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Após a ativação pós-falha, eles Certifique-se de que a VM do Azure é apresentado conforme esperado no portal do Azure.

   ![Detalhes do plano de recuperação](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Depois de verificar, eles concluírem a migração para concluir o processo de migração, parar a replicação para a VM e parar a faturação para a VM do Site Recovery.

    ![Ativação pós-falha - concluir a migração](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Atualizar a cadeia de ligação

Como a etapa final do processo de migração, os administradores da Contoso, atualize a cadeia de ligação da aplicação para apontar para a base de dados migrada está em execução na instância gerida da Contoso.

1. No portal do Azure, é possível localizar a cadeia de ligação selecionando **configurações** > **cadeias de ligação**.

    ![Cadeias de ligação](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Eles atualizar a cadeia de caracteres com o nome de utilizador e palavra-passe da instância gerida do SQL da base de dados.
3. Após a configuração a cadeia de caracteres, estas atualizações vem substituir a cadeia de ligação atual no arquivo Web. config do seu aplicativo.
4. Depois de atualizar o arquivo e salvá-lo, reiniciar IIS no WEBVM executando `IISRESET /RESTART` numa janela de linha de comandos.
5. Após o reinício do IIS, a aplicação utiliza a base de dados que está a executar a instância gerida do SQL da base de dados.
6. Neste ponto, eles podem encerrar no local a máquina SQLVM. A migração foi concluída.

*Precisa de mais ajuda?*

- Saiba como [executar uma ativação pós-falha de teste](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Saiba como [criar um plano de recuperação](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Saiba como [efetuar a ativação pós-falha para o Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração completa, a aplicação da SmartHotel360 está em execução numa VM do Azure e a base de dados da SmartHotel360 está disponível na instância gerida do Azure SQL da base de dados.  

Agora, a Contoso precisa de efetuar as seguintes tarefas de limpeza:  

- Remova a máquina WEBVM do inventário do vCenter Server.
- Remova a máquina SQLVM do inventário do vCenter Server.
- Remova WEBVM e SQLVM das tarefas de cópia de segurança locais.
- Atualize a documentação interna para mostrar o novo local e o endereço IP para WEBVM.
- Remova SQLVM da documentação interna. Em alternativa, a Contoso pode rever a documentação para mostrar SQLVM como eliminada e já não na VM de inventário.
- Reveja todos os recursos que interagem com as VMs desativadas. Atualize definições relevantes ou documentação para refletir a nova configuração.

## <a name="review-the-deployment"></a>Reveja a implementação

Com os recursos migrados no Azure, a Contoso precisa totalmente operacionalizar e proteger sua infra-estrutura de novo.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso analisa a VMs do Azure e a SQL Database Managed Instance para verificar a existência de quaisquer problemas de segurança com a sua implementação:

- A equipe analisa os grupos de segurança de rede que são utilizados para controlar o acesso para a VM. Os grupos ajudam a garantir que apenas o tráfego que tem permissão para a aplicação de segurança de rede pode passar.
- Equipe de segurança da Contoso também é considerar os proteger os dados no disco com o Azure Disk Encryption e o Azure Key Vault.
- A equipe permite que a deteção de ameaças na instância gerida. Deteção de ameaças envia um alerta para o sistema de suporte técnico da Contoso segurança equipe/serviço para abrir um pedido, desde que seja detetada uma ameaça. Saiba mais sobre [deteção para a instância gerida de ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Gerido de segurança de instância - deteção de ameaças](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

Para saber mais sobre as práticas de segurança para VMs, veja [melhores práticas de segurança para cargas de trabalho de IaaS no Azure](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms).

### <a name="bcdr"></a>BCDR

Para a continuidade do negócio e recuperação após desastre (BCDR), a Contoso usa as seguintes ações:

- Manter os dados seguros: Contoso efetua uma cópia dos dados nas VMs com o serviço de cópia de segurança do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Manter as aplicações em funcionamento: Contoso replica as VMs no Azure da aplicação para uma região secundária com o Site Recovery. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).
- Contoso lado aprende mais sobre a gestão de instância gerida de SQL, incluindo [cópias de segurança de base de dados](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).


### <a name="licensing-and-cost-optimization"></a>Otimização de licenciamento e de custo

- A Contoso tiver um licenciamento existentes para WEBVM. Para tirar partido dos preços com o benefício híbrido do Azure, a Contoso converte a VM do Azure existente.
- Contoso permite que o Azure Cost Management licenciado pela Cloudyn, uma subsidiária da Microsoft. Gestão de custos é uma solução de gestão de custos multiclouds que o ajuda a Contoso, utilizar e gerir o Azure e outros recursos da cloud. Saiba mais sobre [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Conclusão

Neste artigo, Contoso realojar a aplicação da SmartHotel360 no Azure ao migrar a aplicação de VM de front-end para o Azure com o serviço de recuperação de sites. Contoso migra a base de dados no local para uma instância de gerida de base de dados do Azure SQL utilizando o serviço de migração de base de dados do Azure.

## <a name="next-steps"></a>Passos Seguintes

No próximo artigo da série, a Contoso [realojar a aplicação da SmartHotel360 em VMs do Azure](contoso-migration-rehost-vm.md) com o serviço Azure Site Recovery.

