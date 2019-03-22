---
title: Refatorizar uma aplicação da Contoso ao migrar para a aplicação de Web do Azure e SQL Database do Azure | Documentos da Microsoft
description: Saiba como Contoso realojar uma aplicação no local ao migrar para uma base de dados de aplicação Web do Azure e o servidor SQL do Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 271e18d370068e0445f183af0c694b19f0da22f2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102759"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Migração de Contoso: Refatorizar uma aplicação no local para uma base de dados de aplicação Web do Azure e SQL do Azure

Este artigo demonstra como Contoso revisasse, encontrasse aplicação seus da SmartHotel360 no Azure. O front-end de aplicação VM estes migrar para uma aplicação Web do Azure e a base de dados de aplicação para uma base de dados SQL do Azure.

Este documento é um de uma série de artigos que mostram como a empresa fictícia Contoso migra os seus recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e cenários que ilustram como configurar uma infraestrutura de migração, avaliação dos recursos no local para migração e execução de diferentes tipos de migrações. Cenários de crescem em complexidade. Vamos adicionar artigos adicionais ao longo do tempo.

**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Fornece uma descrição geral da estratégia de migração da Contoso, a série de artigos e as aplicações de exemplo que usamos. | Disponível
[Artigo 2: Implementar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como o Contoso prepara a sua infraestrutura do Azure e no local, para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração. | Disponível
[Artigo 3: Avalie os recursos no local](contoso-migration-assessment.md)  | Mostra como Contoso é executado uma avaliação de uma aplicação de SmartHotel de duas camadas no local em execução no VMware. Contoso avalia as VMs de aplicação com o [do Azure Migrate](migrate-overview.md) serviço e a base de dados do SQL Server do aplicativo com o [Assistente de migração de base de dados](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
[Artigo 4: Uma aplicação para as VMs do Azure e uma instância gerida de SQL de realojamento](contoso-migration-rehost-vm-sql-managed-instance.md) | Demonstra como Contoso é executado uma migração lift-and-shift para o Azure para a aplicação de SmartHotel. Contoso migra o front-end de aplicação VM através de [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)e a base de dados de aplicação para a instância gerida de SQL, utilizando o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível
[5 do artigo: Realojar a uma aplicação nas VMS do Azure](contoso-migration-rehost-vm.md) | Mostra como Contoso migrar a aplicação de SmartHotel VMs com apenas o Site Recovery. | Disponível
[Artigo 6: Uma aplicação para as VMs do Azure e o SQL Server sempre no grupo de disponibilidade de realojamento](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra a aplicação de SmartHotel. A Contoso utiliza o Site Recovery para migrar as VMs da aplicação e o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server protegido por um grupo de Disponibilidade AlwaysOn. | Disponível
[Artigo 7: Realojar uma aplicação do Linux para VMs do Azure](contoso-migration-rehost-linux-vm.md) | Mostra como Contoso faz uma migração lift-and-shift da aplicação de osTicket do Linux para VMs do Azure, com o Site Recovery | Disponível
[Artigo 8: Realojar uma aplicação do Linux para VMs do Azure e o servidor MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como a Contoso migra a aplicação de osTicket do Linux para as VMs do Azure com o Site Recovery e migra a base de dados de aplicação para uma instância do servidor MySQL do Azure com o MySQL Workbench. | Disponível
Artigo 9: Refatorizar uma aplicação para uma Aplicação Web do Azure e uma base de dados SQL do Azure | Demonstra como Contoso migra a aplicação de SmartHotel para uma aplicação Web do Azure e migra a base de dados de aplicação para a instância de servidor SQL do Azure | Este artigo
[Artigo 10: Refatorizar uma aplicação do Linux para aplicações Web do Azure e MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Mostra como a Contoso migra a aplicação de osTicket do Linux para aplicações Web do Azure em vários sites, integrados com o GitHub para a entrega contínua. Estes migrar a base de dados de aplicação para uma instância do MySQL do Azure. | Disponível
[Artigo 11: Refatorar o TFS em serviços do Azure DevOps](contoso-migration-tfs-vsts.md) | Mostra como a Contoso migra a implantação do Team Foundation Server (TFS) no local ao migrar para serviços do Azure DevOps no Azure. | Disponível
[Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados do Azure SQL](contoso-migration-rearchitect-container-sql.md) | Mostra como Contoso migra e rearchitects a respetiva aplicação SmartHotel para o Azure. Eles rearquitetar a camada de web de aplicação como um contentor do Windows e a base de dados de aplicação numa base de dados SQL do Azure. | Disponível
[Artigo 13: Recriar uma aplicação no Azure](contoso-migration-rebuild.md) | Mostra como Contoso reconstruir a sua aplicação de SmartHotel com uma gama de capacidades do Azure e serviços, incluindo serviços de aplicações, Kubernetes do Azure, as funções do Azure, serviços cognitivos e Cosmos DB. | Disponível
[Artigo 14: Dimensionar uma migração para o Azure](contoso-migration-scale.md) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível

Neste artigo, a Contoso migra o Windows de duas camadas. Aplicação de NET SmartHotel360 em execução em VMs de VMware para o Azure. Se gostaria de utilizar esta aplicação, são fornecidas como código-fonte aberto e pode baixá-lo partir [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Fatores comerciais

A equipe de liderança de TI tem trabalhado em estreita colaboração com parceiros de negócios para compreender o que quer alcançar com esta migração:

- **Resolver o crescimento do negócio**: Contoso está a crescer e há pressão em sistemas no local e em infra-estrutura.
- **Aumentar a eficiência**: A Contoso precisa de remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários.  Necessidades de negócio IT velocidade e não desperdício de tempo ou dinheiro, entregando assim mais rapidamente sobre os requisitos dos clientes.
- **Aumente a agilidade**:  Contoso TI tem de ser mais reativos para com as necessidades do negócio. Tem de ser capaz de reagir mais rapidamente do que as alterações no marketplace, para ativar o sucesso numa economia global.  Ele não pode obter na forma como ou se tornar um bloqueador de negócios.
- **Dimensionamento**: À medida que a empresa cresce com êxito, TI da Contoso tem de fornecer sistemas que são capazes de aumentar ao ritmo mesmo.
- **Os custos**: Contoso quer minimizar os custos de licenciamento.

## <a name="migration-goals"></a>Objetivos de migração

A equipe de cloud de Contoso tiver afixado para baixo de objetivos para essa migração. Essas metas foram utilizadas para determinar o melhor método de migração.

**Requisitos** | **Detalhes**
--- | --- 
**Aplicação** | A aplicação no Azure permanecerá como crítica, pois é hoje mesmo.<br/><br/> Ele deve ter as mesmas capacidades de desempenho, como o faz atualmente no VMWare.<br/><br/> A equipe não quer investir na aplicação. Por agora, os administradores serão simplesmente mover a aplicação com segurança para a cloud.<br/><br/> A equipe pretende parar a dar suporte a Windows Server 2008 R2, no qual a aplicação é atualmente executado.<br/><br/> A equipe também quer mudar para uma plataforma de base de dados PaaS modernos, o que irá minimizar a necessidade de gerenciamento de SQL Server 2008 R2.<br/><br/> Contoso pretende tirar partido do seu investimento no licenciamento do SQL Server e o Software Assurance sempre que possível.<br/><br/> Além disso, a Contoso pretende reduzir o ponto único de falha na camada web.
**Limitações** | A aplicação é constituída por uma aplicação ASP.NET e um serviço WCF em execução na mesma VM. Pretende dividir isso em duas aplicações web com o serviço de aplicações do Azure. 
**Azure** | Contoso quer mover a aplicação para o Azure, mas não quer executá-lo em VMs. Contoso quer tirar partido dos serviços de PaaS do Azure para os escalões web e de dados. 
**DevOps** | Contoso quer mudar para um modelo de DevOps, com o Azure DevOps para suas compilações e lançar pipelines.

## <a name="solution-design"></a>Design da solução

Depois de fixar-se para baixo de objetivos e requisitos, a Contoso projeta e rever uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que serão utilizados para a migração.

### <a name="current-app"></a>Aplicação atual

- A aplicação de local da SmartHotel360 é em camadas em duas VMs (WEBVM e SQLVM).
- As VMs estão localizadas no anfitrião ESXi do VMware **contosohost1.contoso.com** (versão 6.5)
- O ambiente do VMware é gerido pelo vCenter Server 6.5 (**vcenter.contoso.com**), em execução numa VM.
- A Contoso tiver um datacenter no local (contoso-datacenter), com um controlador de domínio no local (**contosodc1**).
- As VMs no local no Centro de dados Contoso serão encerradas depois de terminar a migração.


### <a name="proposed-solution"></a>Solução proposta

- Para o escalão de base de dados da aplicação, Contoso em comparação com a base de dados do Azure SQL com o SQL Server a utilizar [este artigo](https://docs.microsoft.com/azure/sql-database/sql-database-features). Contoso decidido a escolher a base de dados do Azure SQL por diversos motivos:
    - Base de dados SQL do Azure é um serviço gerido da base de dados relacional. Ele proporciona um desempenho previsível em vários níveis de serviço, com quase sem administração. As vantagens incluem escalabilidade dinâmica sem tempo de inatividade, a otimização inteligente incorporada e a escalabilidade global e a disponibilidade.
    - Contoso pode aproveitar o lightweight dados Assistente de migração (DMA) para avaliar e migrar a base de dados no local para o SQL do Azure.
    - Com o Software Assurance, Contoso pode trocar licenças existentes para as tarifas com desconto num banco de dados do SQL, com o Azure Hybrid Benefit para o SQL Server. Isso poderia fornecer redução de até 30%.
    - Base de dados SQL fornece uma série de recursos de segurança, incluindo a máscara de dados sempre encriptado, dinâmico e deteção de ameaça e segurança ao nível da linha.
- Para a camada de web de aplicação Contoso decidiu usar o App Service do Azure. Este serviço de PaaS permite que implementar a aplicação com apenas algumas alterações de configuração. Contoso vai utilizar o Visual Studio para fazer a alteração e implementar duas aplicações web. Um para o Web site e outro para o serviço do WCF.
- Para atender aos requisitos para um pipeline de DevOps, a Contoso tiver selecionado utilizar do Azure DevOps para gestão de código de origem (SCM) com repositórios Git. Compilações automatizadas e lançamento serão utilizados para criar o código e implementação-la para as aplicações Web do Azure.
  
### <a name="solution-review"></a>Revisão de solução
Contoso avalia o design proposto ao juntar-se de uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Profissionais de TI** | O código da aplicação da SmartHotel360 não terá de ser alterado para a migração para o Azure.<br/><br/> Contoso pode aproveitar seus investimentos em Software Assurance com o Azure Hybrid Benefit para o SQL Server e o Windows Server.<br/><br/> Após a migração, Windows Server 2008 R2 não precisam de suporte. [Saiba mais](https://support.microsoft.com/lifecycle).<br/><br/> Contoso pode configurar a camada web da aplicação com várias instâncias, para que ele deixou de ser um ponto único de falha.<br/><br/> A base de dados deixará de estar dependente da SQL Server 2008 R2 de classificação por vencimento.<br/><br/> Base de dados SQL suporta os requisitos técnicos. Contoso avaliado à base de dados no local com o Assistente de migração de base de dados e que é compatível.<br/><br/> Base de dados SQL tem tolerância a falhas incorporada que Contoso não precisa de configurar. Isto garante que a camada de dados já não é um ponto único de ativação pós-falha.
**Contras** | Serviços de aplicações do Azure só suporta uma implementação de aplicações para cada aplicação Web. Isso significa que duas aplicações Web tem de ser aprovisionada (uma para o Web site) e outra para o serviço do WCF.<br/><br/> Se a Contoso utiliza o Assistente de migração de dados em vez de serviço de migração de dados para migrar a base de dados, ele não terá a infraestrutura pronta para a migração de bases de dados em escala. Contoso tem de criar outra região para se certificar de ativação pós-falha se a região primária estiver indisponível.

## <a name="proposed-architecture"></a>Arquitetura proposta

![Arquitetura do cenário](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Processo de migração

1. Contoso Aprovisiona uma instância de SQL do Azure e migra a base de dados da SmartHotel360 a ele.
2. Contoso Aprovisiona e configura as aplicações Web e implementa a aplicação da SmartHotel360.

    ![Processo de migração](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Assistente de migração de base de dados (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso usará o DMA para avaliar e detetar problemas de compatibilidade que podem afetar sua funcionalidade de base de dados no Azure. O DMA avalia a paridade de funcionalidades entre o SQL origens e destinos e recomenda melhorias de desempenho e confiabilidade. | É uma ferramenta transferível gratuita.
[Base de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) | Um serviço de base de dados da cloud relacional completamente gerido inteligente. | Custo com base nas funcionalidades, débito e tamanho. [Saiba mais](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Serviços de aplicações do Azure - aplicações Web](https://docs.microsoft.com/azure/app-service/overview) | Criar poderosas aplicações na cloud com uma plataforma totalmente gerida | Custo com base na duração de tamanho, a localização e a utilização. [Saiba mais](https://azure.microsoft.com/pricing/details/app-service/windows/).
[DevOps do Azure](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Fornece uma integração contínua e um pipeline de implementação contínua (CI/CD) para o desenvolvimento de aplicações. O pipeline é iniciado com um repositório de Git para o gerenciamento de código da aplicação, um sistema de compilação para produzir pacotes e outros artefactos de compilação e um sistema de gerenciamento de liberação para implementar as alterações no desenvolvimento, teste e ambientes de produção. 

## <a name="prerequisites"></a>Pré-requisitos

Esta é a Contoso tem de executar este cenário:

**Requisitos** | **Detalhes**
--- | ---
**Subscrição do Azure** | Contoso criadas subscrições durante um artigo de início. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se criar uma conta gratuita, será o administrador da sua subscrição e poderá executar todas as ações.<br/><br/> Se utilizar uma subscrição existente e não for o administrador, terá de trabalhar com o administrador para lhe atribuir permissões de proprietário ou contribuinte.
**Infraestrutura do Azure** | [Saiba como](contoso-migration-infrastructure.md) Contoso configurar uma infraestrutura do Azure.


## <a name="scenario-steps"></a>Passos do cenário

Eis como Contoso será executada a migração:

> [!div class="checklist"]
> * **Passo 1: Aprovisionar uma instância de base de dados SQL no Azure**: Contoso Aprovisiona uma instância do SQL no Azure. Depois do Web site da aplicação está a migrar para o Azure, vai apontar a aplicação de web do serviço WCF para esta instância.
> * **Passo 2: Migrar a base de dados com o DMA**: Contoso migra a base de dados de aplicação com o Assistente de migração de base de dados.
> * **Passo 3: Aprovisionar aplicações Web**: Contoso Aprovisiona as duas aplicações web.
> * **Passo 4: Configurar o Azure DevOps**: Contoso cria um novo projeto de DevOps do Azure e importa o repositório de Git.
> * **Passo 5: Configurar cadeias de ligação**: Contoso configura as cadeias de ligação para que podem comunicar a aplicação de web de camada web, a aplicação de web do serviço WCF e a instância SQL.
> * **Passo 6: Configurar a compilação e lançar pipelines**: Como passo final, Contoso configura a compilação e versão pipelines para criar a aplicação e as implanta duas aplicações Web do Azure separada.


## <a name="step-1-provision-an-azure-sql-database"></a>Passo 1: Aprovisionar uma base de dados SQL do Azure

1. Selecione os administradores da Contoso para criar uma base de dados SQL no Azure. 

    ![Aprovisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. Especifica um nome de base de dados de acordo com a base de dados em execução na VM no local (**SmartHotel.Registration**). A base de dados são colocados no grupo de recursos de ContosoRG. Este é o grupo de recursos que utilizam recursos de produção no Azure.

    ![Aprovisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. Eles configurar uma nova instância do SQL Server (**smarthotel-sql-eus2**) na região primária.

    ![Aprovisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. Eles definir o escalão de preço para corresponder ao seu servidor e base de dados precisa. E devem selecionar para poupar dinheiro com o benefício híbrido do Azure, uma vez que já têm uma licença do SQL Server.
5. Para o dimensionamento utilizam v Core-com base em comprar e defina os limites para as suas necessidades esperados.

    ![Aprovisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. Em seguida, é criar a instância de base de dados.

    ![Aprovisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. Depois da instância é criada, eles abrir a base de dados e anote os detalhes que necessitam, quando utilizarem a assistência de migração de base de dados para a migração.

    ![Aprovisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**Precisa de mais ajuda?**

- [Obtenha ajuda](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) aprovisionar uma base de dados SQL.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) limites de recursos de núcleos.


## <a name="step-2-migrate-the-database-with-dma"></a>Passo 2: Migrar a base de dados com o DMA

Os administradores da Contoso irão migrar a base de dados da SmartHotel360 com o DMA.

### <a name="install-dma"></a>Instalar o DMA

1. Eles transferirem a ferramenta a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) para a VM do SQL Server no local (**SQLVM**).
2. Eles executam a configuração (Downloadmigrationassistant) na VM.
3. Sobre o **concluir** página, selecione **iniciar Microsoft Data Migration Assistant** antes de concluir o assistente.

### <a name="migrate-the-database-with-dma"></a>Migrar a base de dados com o DMA

1. No DMA, eles criam um novo projeto (**SmartHotelDB**) e selecione **migração** 
2. Selecionar o tipo de servidor de origem como **SQL Server**e o destino como **base de dados do Azure SQL**. 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. Nos detalhes de migração, adicione **SQLVM** que o servidor de origem e o **SmartHotel.Registration** base de dados. 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. Eles recebem um erro que parece estar associado a autenticação. No entanto depois de investigar, o problema é o ponto final (.) no nome da base de dados. Como solução, eles decidem se pretendem aprovisionar uma base de dados do SQL nova com o nome **SmartHotel-registo**, para resolver o problema. Quando executam DMA novamente, eles podem selecionar **SmartHotel-registo**e continuar com o assistente.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. Na **selecionar objetos**, selecione as tabelas de base de dados e gerar um script SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. Depois do DMS cria o script, pode clicar em **implementar esquema**.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DMA confirma que a implementação foi concluída com êxito.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. Agora que iniciar a migração.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. Depois de concluída a migração, os administradores da Contoso podem verificar que a base de dados está em execução na instância do SQL do Azure.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Se eliminar a base de dados do SQL extra **SmartHotel.Registration** no portal do Azure.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Passo 3: Aplicações Web de aprovisionamento

Com a base de dados migrados, os administradores da Contoso podem agora aprovisionar as duas aplicações web.

1. Eles selecionam **aplicação Web** no portal.

    ![Aplicação Web](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Eles fornecem um nome de aplicação (**SHWEB EUS2**), executá-lo no Windows e colocá-lo a anular o grupo de recursos de produção **ContosoRG**. Eles criam um novo serviço de aplicações e o plano.

    ![Aplicação Web](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Depois da aplicação web é aprovisionada, eles repetir o processo para criar uma aplicação web para o serviço do WCF (**SHWCF EUS2**)

    ![Aplicação Web](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Depois de terminar, eles navegam para o endereço de aplicações para verificar que tenha sido criado com êxito.


## <a name="step-4-set-up-azure-devops"></a>Passo 4: Configurar o DevOps do Azure


A Contoso precisa de criar a infraestrutura de DevOps e pipelines para a aplicação.  Para fazer isso, os administradores da Contoso criar um novo projeto de DevOps, importar o código e, em seguida, configure a compilação e lançar pipelines.

1. Na conta do Azure DevOps da Contoso, eles criam um novo projeto (**ContosoSmartHotelRefactor**) e selecione **Git** para controle de versão.

   ![Novo projeto](./media/contoso-migration-refactor-web-app-sql/vsts1.png)
2. Eles importam o repositório do Git que atualmente possui o seu código de aplicação. Está a ser um [repositório público](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) e pode baixá-lo.

    ![Baixe o código da aplicação](./media/contoso-migration-refactor-web-app-sql/vsts2.png)
    
3. Depois do código foi importado, eles conectar o Visual Studio para o repositório e clonagem o código usando o Team Explorer.

    ![Ligar ao projeto](./media/contoso-migration-refactor-web-app-sql/devops1.png)

4. Depois do repositório é clonado para a máquina do desenvolvedor, eles abrem o ficheiro de solução para a aplicação. O serviço web app e o wcf cada ter separar projeto dentro do arquivo.

    ![Ficheiro de solução](./media/contoso-migration-refactor-web-app-sql/vsts4.png)
    

## <a name="step-5-configure-connection-strings"></a>Passo 5: Configurar cadeias de ligação

Os administradores da Contoso tem de certificar-se de que as aplicações web e base de dados pode comunicar. Para fazer isso, eles configurarem cadeias de ligação no código e nas aplicações web.

1. Na aplicação web para o serviço do WCF (**SHWCF EUS2**) > **definições** > **definições da aplicação**, adicionam uma nova cadeia de ligação com o nome  **DefaultConnection**.
2. A cadeia de ligação é obtida a partir da **SmartHotel-registo** de base de dados e deve ser atualizado com as credenciais corretas.

    ![Cadeia de ligação](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Eles com o Visual Studio, abrem a **SmartHotel.Registration.wcf** projeto a partir do arquivo da solução. O **connectionStrings** secção do ficheiro Web. config para o serviço WCF SmartHotel.Registration.Wcf devem ser atualizados com a cadeia de ligação.

     ![Cadeia de ligação](media/contoso-migration-refactor-web-app-sql/string2.png)

4. O **cliente** secção do ficheiro Web. config para o SmartHotel.Registration.Web deve ser alterada para apontar para a nova localização do serviço WCF. Este é o URL da aplicação web WCF que aloja o ponto final de serviço.

    ![Cadeia de ligação](media/contoso-migration-refactor-web-app-sql/strings3.png)

5. Depois das alterações no código, os administradores precisam consolidar as alterações. Eles usando o Team Explorer no Visual Studio, consolidar e sincronizar.


## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>Passo 6: Configurar a compilação e lançar pipelines em DevOps do Azure

Os administradores de contoso agora configurar DevOps do Azure para executar a compilação e versão do processo.

1. No Azure DevOps, clicarem **criar e lançar** > **novo pipeline**.

    ![Novo pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline1.png)

2. Eles selecionam **Azure repositórios Git** e o repositório relevante.

    ![Git e do repositório](./media/contoso-migration-refactor-web-app-sql/pipeline2.png)

3. Na **selecionar um modelo**, eles selecionam o modelo do ASP.NET para a respetiva compilação.

     ![Modelo do ASP.NET](./media/contoso-migration-refactor-web-app-sql/pipeline3.png)
    
4. O nome **ContosoSmartHotelRefactor-ASP.NET-CI** é utilizado para a compilação. Clicarem **guardar e colocar em fila**.

     ![Guardar e fila](./media/contoso-migration-refactor-web-app-sql/pipeline4.png)

5. Isso ativa a compilação primeiro. Clicarem no número de compilação para ver o processo. Depois de terminar podem ver os comentários de processo e clique em **artefactos** para rever os resultados de compilação.

    ![Rever](./media/contoso-migration-refactor-web-app-sql/pipeline5.png)

6. A pasta **Drop** contém os resultados da compilação.

   - Os ficheiros dois zip são os pacotes que contêm as aplicações.
   - Estes ficheiros são utilizados no pipeline de versões para implementação de aplicações Web do Azure

     ![Artefacto](./media/contoso-migration-refactor-web-app-sql/pipeline6.png)

7. Clicarem **versões** > **+ novo pipeline**.

    ![Novo pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline7.png)

8. Eles selecionam o modelo de implementação do serviço de aplicações do Azure.

    ![Modelo de serviço de aplicações do Azure](./media/contoso-migration-refactor-web-app-sql/pipeline8.png)

9. Nome de pipeline de versões **ContosoSmartHotel360Refactor**e especifique o nome da aplicação web WCF (SHWCF EUS2) para o **estágio** nome.

    ![Ambiente](./media/contoso-migration-refactor-web-app-sql/pipeline9.png)

10. Em fases, clicarem **1 tarefa, 1 tarefa** para configurar a implementação do serviço WCF.

    ![Implementar o WCF](./media/contoso-migration-refactor-web-app-sql/pipeline10.png)

11. Verificarem que a subscrição está selecionado e autorizados e selecione o **nome do serviço de aplicações**.

     ![Selecione o serviço de aplicações](./media/contoso-migration-refactor-web-app-sql/pipeline11.png)

12. No pipeline > **artefactos**, eles selecionam **+ adicionar um artefato**e selecione para criar com o **ContosoSmarthotel360Refactor** pipeline.

     ![Compilação](./media/contoso-migration-refactor-web-app-sql/pipeline12.png)

15. Clicarem o bolt extremamente no artefacto é verificado., para ativar o acionador de implementação contínua.

     ![Bolt de palestras](./media/contoso-migration-refactor-web-app-sql/pipeline13.png)

16. O acionador de implementação contínua deve ser definido como **ativado**.

    ![Implementação contínua ativada](./media/contoso-migration-refactor-web-app-sql/pipeline14.png) 

17. Agora, eles mover de volta para a tarefa de fase 1, tarefas e clique em **implementar serviço de aplicações do Azure**.

    ![Implementar o serviço de aplicações](./media/contoso-migration-refactor-web-app-sql/pipeline15.png)

18. Na **selecionar um ficheiro ou pasta**, eles localizar o **SmartHotel.Registration.Wcf.zip** ficheiro que foi criar durante a compilação e clique em **guardar**.

    ![Guardar o WCF](./media/contoso-migration-refactor-web-app-sql/pipeline16.png)

19. Clicarem **Pipeline** > **fases** **+ adicionar**, para adicionar um ambiente para **SHWEB EUS2**. Eles selecionam outra implementação de serviço de aplicações do Azure.

    ![Adicionar o ambiente](./media/contoso-migration-refactor-web-app-sql/pipeline17.png)

20. Eles repetir o processo para publicar a aplicação web (**SmartHotel.Registration.Web.zip**) o ficheiro para a aplicação web correta.

    ![Publicar a aplicação web](./media/contoso-migration-refactor-web-app-sql/pipeline18.png)

21. Depois de guardar, o pipeline de versões mostrará da seguinte forma.

     ![Resumo de pipeline de lançamento](./media/contoso-migration-refactor-web-app-sql/pipeline19.png)

22. Mover novamente para **crie**e clique em **Acionadores** > **ativar a integração contínua**. Isto permite que o pipeline, de modo que, quando as alterações forem consolidadas no código, e compilação completa e o lançamento ocorre.

    ![Ativar a integração contínua](./media/contoso-migration-refactor-web-app-sql/pipeline20.png)

23. Clicarem **guardar e colocar em fila** para executar o pipeline completo. Uma nova compilação é disparada que por sua vez, cria a primeira versão da aplicação para o serviço de aplicações do Azure.

    ![Guardar o pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline21.png)

24. Os administradores da Contoso podem seguir a compilação e versão do processo de pipeline de DevOps do Azure. Após a compilação for concluída, o lançamento será iniciado.

    ![Criar e lançar a aplicação](./media/contoso-migration-refactor-web-app-sql/pipeline22.png)

25. Após a conclusão do pipeline, ambos os sites que foram implementados e a aplicação está ativa e em execução online.

    ![Concluir a versão](./media/contoso-migration-refactor-web-app-sql/pipeline23.png)

Neste momento, a aplicação for migrada com êxito para o Azure.



## <a name="clean-up-after-migration"></a>Limpar após a migração

Após a migração, a Contoso precisa de concluir estes passos de limpeza:  

- Remova as VMs no local no inventário do vCenter.
- Remova as VMs das tarefas de cópia de segurança locais.
- Atualize a documentação interna para mostrar as novas localizações para a aplicação da SmartHotel360. Mostre a base de dados como em execução na base de dados SQL do Azure e o front-end como em execução em duas aplicações web.
- Reveja todos os recursos que interagem com as VMs desativadas e atualizar as definições relevantes ou documentação para refletir a nova configuração.


## <a name="review-the-deployment"></a>Reveja a implementação

Com os recursos migrados no Azure, a Contoso precisa totalmente operacionalizar e proteger sua infra-estrutura de novo.

### <a name="security"></a>Segurança

- A Contoso precisa de se certificar de que seus novos **SmartHotel-registo** base de dados é segura. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Em particular, a Contoso deve atualizar as aplicações web para utilizar SSL com certificados.

### <a name="backups"></a>Cópias de segurança

- A Contoso precisa de analisar os requisitos de cópia de segurança da base de dados do SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso também precisa saber sobre a gestão de cópias de segurança da base de dados SQL e restaura. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) sobre cópias de segurança automáticas.
- Contoso deve considerar a implementação de grupos de ativação pós-falha para fornecer ativação pós-falha para a base de dados. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- A Contoso precisa de considerar a implementar a aplicação Web nos E.U.A. Leste 2 principal e a região E.U.A. Central para resiliência. Contoso foi possível configurar o Gestor de tráfego para garantir que a ativação pós-falha em caso de falhas regionais.

### <a name="licensing-and-cost-optimization"></a>Otimização de licenciamento e de custo

- Depois de todos os recursos são implementados, Contoso deve atribuir etiquetas do Azure com base em seus [planejamento da infraestrutura](contoso-migration-infrastructure.md#set-up-tagging).
- Licenciamento de todos os baseia-se para o custo dos serviços PaaS que Contoso está a consumir. Isso é deduzido do EA.
- Contoso irá ativar o Azure Cost Management licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gestão de custos multiclouds que o ajuda a utilizar e gerir o Azure e outros recursos da cloud.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Azure Cost Management.

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso refatorado a aplicação da SmartHotel360 no Azure ao migrar o VM da front-end de aplicação para duas aplicações Web do Azure. A base de dados de aplicação foi migrada para uma base de dados SQL do Azure.






