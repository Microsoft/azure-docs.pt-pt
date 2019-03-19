---
title: Rearquitetar uma aplicação da Contoso num contentor do Azure e SQL Database do Azure | Documentos da Microsoft
description: Saiba como Contoso rearquitetar uma aplicação em contentores do Windows Azure e base de dados do Azure SQL.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: cb984bcbe79b69c0614579d66a3b853cd38a7e12
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099091"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Migração de Contoso: Rearquitetar uma aplicação no local para um contentor do Azure e a base de dados do Azure SQL

Este artigo demonstra como Contoso migra e rearquitetar a sua aplicação da SmartHotel360 no Azure. Contoso migra do front-end de aplicação VM para um contentor do Windows Azure e a base de dados de aplicação para uma base de dados SQL do Azure.

Este documento é um de uma série de artigos que mostram como a empresa fictícia Contoso migra recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e cenários que ilustram como configurar uma infraestrutura de migração, avaliação dos recursos no local para migração e execução de diferentes tipos de migrações. Cenários de crescem em complexidade. Artigos adicionais serão adicionados ao longo do tempo.

**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Descrição geral da série de artigos, estratégia de migração da Contoso e as aplicações de exemplo que são utilizadas da série. | Disponível
[Artigo 2: Implementar a infraestrutura do Azure](contoso-migration-infrastructure.md) | Contoso prepara a sua infraestrutura no local e a respetiva infraestrutura do Azure para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avalie os recursos no local para migração para o Azure](contoso-migration-assessment.md)  | Contoso é executada uma avaliação da sua aplicação da SmartHotel360 no local em execução no VMware. Contoso avalia as VMs de aplicação com o serviço Azure Migrate e a base de dados de SQL Server do aplicativo com o Assistente de migração de dados. | Disponível
[Artigo 4: Realojar a uma aplicação numa VM do Azure e a instância gerida da base de dados do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso é executada uma migração lift-and-shift para o Azure para a sua aplicação da SmartHotel360 no local. Contoso migra a aplicação front-end VM com [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migra a base de dados de aplicação para uma instância gerida da base de dados SQL do Azure utilizando o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível   
[5 do artigo: Realojar a uma aplicação em VMs do Azure](contoso-migration-rehost-vm.md) | Contoso migra a sua aplicação da SmartHotel360 VMs para VMs do Azure com o serviço Site Recovery. | Disponível
[Artigo 6: Realojar a uma aplicação em VMs do Azure e, num grupo de Disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migra a aplicação da SmartHotel360. A Contoso utiliza o Site Recovery para migrar as VMs da aplicação. Ele usa o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server que está protegido por um grupo de Disponibilidade AlwaysOn. | Disponível 
[Artigo 7: Realojar uma aplicação do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | Contoso é uma migração lift-and-shift da aplicação de osTicket do Linux para VMs do Azure, com o Azure Site Recovery concluída | Disponível
[Artigo 8: Realojar uma aplicação do Linux em VMs do Azure e MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migra a aplicação de osTicket do Linux para VMs do Azure com o Azure Site Recovery e migra a base de dados de aplicação para uma instância do servidor MySQL do Azure com o MySQL Workbench. | Disponível
[Artigo 9: Refatorizar uma aplicação na base de dados de aplicações Web do Azure e SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Contoso migra a aplicação da SmartHotel360 para uma aplicação Web do Azure e migra a base de dados de aplicação para uma instância de servidor SQL do Azure com o Assistente de migração de base de dados | Disponível
[Artigo 10: Refatorizar uma aplicação do Linux em aplicações Web do Azure e MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migra sua osTicket de aplicação do Linux para uma aplicação web do Azure em várias regiões do Azure utilizando o Gestor de tráfego do Azure, integrado com o GitHub para a entrega contínua. Contoso migra a base de dados de aplicação para uma base de dados do Azure para a instância do MySQL. | Disponível 
[Artigo 11: Refatorar o TFS em serviços do Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migra sua implantação do Team Foundation Server no local para serviços do Azure DevOps no Azure. | Disponível
Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados SQL do Azure | Contoso migra sua SmartHotel de aplicação para o Azure. Em seguida, rearchitects a camada de web de aplicação como um contentor do Windows em execução no Azure Service Fabric e base de dados com a base de dados do Azure SQL. | Este artigo
[Artigo 13: Recriar uma aplicação no Azure](contoso-migration-rebuild.md) | Contoso recria o seu aplicativo de SmartHotel com uma variedade de capacidades do Azure e serviços, incluindo o serviço de aplicações do Azure, Azure Kubernetes Service (AKS), as funções do Azure, serviços cognitivos do Azure e Azure Cosmos DB. | Disponível 
[Artigo 14: Dimensionar uma migração para o Azure](contoso-migration-scale.md) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível

Neste artigo, a Contoso migra o WPF de Windows de duas camadas, XAML formulários da SmartHotel360 aplicação em execução em VMs de VMware para o Azure. Se gostaria de utilizar esta aplicação, são fornecidas como código-fonte aberto e pode baixá-lo partir [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Fatores comerciais

A equipe de liderança de TI da Contoso tem trabalhado em estreita colaboração com parceiros de negócios para compreender o que quer alcançar com esta migração:

- **Resolver o crescimento do negócio**: Contoso está a crescer e assim há pressão sobre seus sistemas no local e a infraestrutura.
- **Aumentar a eficiência**: A Contoso precisa de remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários.  Necessidades de negócio IT velocidade e não desperdício de tempo ou dinheiro, entregando assim mais rapidamente sobre os requisitos dos clientes.
- **Aumente a agilidade**:  Contoso TI tem de ser mais reativos para com as necessidades do negócio. Tem de ser capaz de reagir mais rapidamente do que as alterações no marketplace, para ativar o sucesso numa economia global.  Ele não pode obter na forma como ou se tornar um bloqueador de negócios.
- **Dimensionamento**: À medida que a empresa cresce com êxito, TI da Contoso tem de fornecer sistemas que são capazes de aumentar ao ritmo mesmo.
- **Os custos**: Contoso quer minimizar os custos de licenciamento.

## <a name="migration-goals"></a>Objetivos de migração

A equipe de cloud de Contoso tiver afixado para baixo de objetivos para essa migração. Essas metas foram utilizadas para determinar o melhor método de migração.

**Objetivos** | **Detalhes**
--- | --- 
**Reqs de aplicação** | A aplicação no Azure permanecerá como crítica, pois é hoje mesmo.<br/><br/> Ele deve ter as mesmas capacidades de desempenho, como o faz atualmente no VMWare.<br/><br/> Contoso quer parar o suporte do Windows Server 2008 R2, no qual a aplicação atualmente é executado e está disposto a investir na aplicação.<br/><br/> Contoso quer mudar para uma plataforma de base de dados PaaS modernos, o que irá minimizar a necessidade de gerenciamento de SQL Server 2008 R2.<br/><br/> Contoso pretende tirar partido do seu investimento no licenciamento do SQL Server e o Software Assurance sempre que possível.<br/><br/> Contoso quer ser capaz de aumentar verticalmente a camada de web de aplicação.
**Limitações** | A aplicação é constituída por uma aplicação ASP.NET e um serviço WCF em execução na mesma VM. A Contoso pretende dividir isso em duas aplicações web com o serviço de aplicações do Azure. 
**Reqs do Azure** | Contoso quer mover a aplicação para o Azure e executá-lo num contêiner para estender a vida da aplicação. Ele não quer iniciar totalmente do zero para implementar a aplicação no Azure. 
**DevOps** | Contoso quer mover para um modelo de DevOps com que serviços de DevOps do Azure para o código cria e o pipeline de versões.

## <a name="solution-design"></a>Design da solução

Depois de fixar-se para baixo de objetivos e requisitos, a Contoso projeta e rever uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que Contoso irá utilizar para a migração.

### <a name="current-app"></a>Aplicação atual

- A aplicação de local da SmartHotel360 é em camadas em duas VMs (WEBVM e SQLVM).
- As VMs estão localizadas no anfitrião ESXi do VMware **contosohost1.contoso.com** (versão 6.5)
- O ambiente do VMware é gerido pelo vCenter Server 6.5 (**vcenter.contoso.com**), em execução numa VM.
- A Contoso tiver um datacenter no local (contoso-datacenter), com um controlador de domínio no local (**contosodc1**).
- As VMs no local no Centro de dados Contoso serão encerradas depois de terminar a migração.


### <a name="proposed-architecture"></a>Arquitetura proposta

- Para o escalão de base de dados da aplicação, Contoso em comparação com a base de dados do Azure SQL com o SQL Server a utilizar [este artigo](https://docs.microsoft.com/azure/sql-database/sql-database-features). Ele decidido a escolher a base de dados do Azure SQL por diversos motivos:
    - Base de dados SQL do Azure é um serviço gerido da base de dados relacional. Ele proporciona um desempenho previsível em vários níveis de serviço, com quase sem administração. As vantagens incluem escalabilidade dinâmica sem tempo de inatividade, a otimização inteligente incorporada e a escalabilidade global e a disponibilidade.
    - A leve dados Assistente de migração (DMA) para avaliar e migrar a base de dados no local para o SQL do Azure tira partido da Contoso.
    - Com o Software Assurance, Contoso pode trocar suas licenças existentes para as tarifas com desconto num banco de dados do SQL, com o Azure Hybrid Benefit para o SQL Server. Isso poderia fornecer redução de até 30%.
    - Base de dados SQL fornece uma série de recursos de segurança, incluindo a máscara de dados sempre encriptado, dinâmico e deteção de ameaça e segurança ao nível da linha.
- Para a camada de web de aplicação Contoso decidiu convertê-lo para o contentor do Windows usando os serviços do Azure DevOps.
    - Contoso irá implementar a aplicação com o Azure Service Fabric e extraia a imagem de contentor do Windows do Azure Container Registry (ACR).
    - Um protótipo de extensão da aplicação para incluir a análise de sentimentos irá ser implementado como outro serviço no Service Fabric, ligado ao Cosmos DB.  Isto irá ler as informações de Tweets e apresentar na aplicação.
- Para implementar um pipeline de DevOps, Contoso usará DevOps do Azure para a gestão de código de origem (SCM), com repositórios Git.  Compilações automatizadas e versões serão utilizadas para criar código e implementação-la para o Azure Container Registry e o Azure Service Fabric.

    ![Arquitetura do cenário](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Revisão de solução
Contoso avalia a estrutura proposta ao juntar-se de uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Profissionais de TI** | O código da aplicação da SmartHotel360 precisará ser alterado para migração para o Azure Service Fabric. No entanto, o esforço é mínimo, com as ferramentas do SDK do Service Fabric para as alterações.<br/><br/> Com a mudança para o Service Fabric, a Contoso pode começar a desenvolver microsserviços para adicionar a aplicação rapidamente ao longo do tempo, sem riscos para a base de código original.<br/><br/> Contentores do Windows oferecem os mesmos benefícios do contentores em geral. Eles aumentam a agilidade, portabilidade e controle.<br/><br/> Contoso pode tirar partido do seu investimento no Software Assurance com o Azure Hybrid Benefit para o SQL Server e o Windows Server.<br/><br/> Após a migração já não será necessário suportar o Windows Server 2008 R2. [Saiba mais](https://support.microsoft.com/lifecycle).<br/><br/> Contoso pode configurar a camada web da aplicação com várias instâncias, para que ele deixou de ser um ponto único de falha.<br/><br/> Já não é dependente da SQL Server 2008 R2 de classificação por vencimento.<br/><br/> Base de dados SQL oferece suporte a requisitos técnicos da Contoso. Os administradores de contoso avaliado à base de dados no local com o Assistente de migração de base de dados e encontrar compatível.<br/><br/> Base de dados SQL tem tolerância a falhas incorporada que Contoso não precisa de configurar. Isto garante que a camada de dados já não é um ponto único de ativação pós-falha.
**Contras** | Os contentores são mais complexos do que outras opções de migração. A curva de aprendizado em contentores pode ser um problema para a Contoso.  Eles introduzem um novo nível de complexidade que fornece muito valor apesar da curva.<br/><br/> A equipe de operações na Contoso terá a incrementação para compreender e suporte Azure, contentores e microsserviços para a aplicação.<br/><br/> Se a Contoso utiliza o Assistente de migração de dados em vez de serviço de migração de dados para migrar a base de dados, ele não terá a infraestrutura pronta para a migração de bases de dados em escala.



### <a name="migration-process"></a>Processo de migração

1. Contoso Aprovisiona o cluster do Azure service fabric para Windows.
2. Aprovisiona uma instância de SQL do Azure e migra a base de dados da SmartHotel360 a ele.
3. Contoso converte a camada Web VM para um contentor de Docker usando as ferramentas do SDK do Service Fabric.
4. Liga-se o cluster do service fabric e o ACR e implementa a aplicação utilizar o Azure service fabric.

    ![Processo de migração](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Assistente de migração de base de dados (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Avalia e detetar problemas de compatibilidade que podem afetar a funcionalidade de banco de dados no Azure. O DMA avalia a paridade de funcionalidades entre o SQL origens e destinos e recomenda melhorias de desempenho e confiabilidade. | É uma ferramenta transferível gratuita.
[Base de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) | Fornece um serviço de base de dados da cloud relacional completamente gerido inteligente. | Custo com base nas funcionalidades, débito e tamanho. [Saiba mais](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Armazena imagens de todos os tipos de implementações de contentor. | Custo com base em recursos, armazenamento e a duração de utilização. [Saiba mais](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Baseia-se e opere aplicações sempre ativa, dimensionáveis e distribuídas | Encargos com base no tamanho, a localização e a duração de nós de computação. [Saiba mais](https://azure.microsoft.com/pricing/details/service-fabric/).
[DevOps do Azure](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Fornece uma integração contínua e um pipeline de implementação contínua (CI/CD) para o desenvolvimento de aplicações. O pipeline é iniciado com um repositório de Git para o gerenciamento de código da aplicação, um sistema de compilação para produzir pacotes e outros artefactos de compilação e um sistema de gerenciamento de liberação para implementar as alterações no desenvolvimento, teste e ambientes de produção.

## <a name="prerequisites"></a>Pré-requisitos

Eis o que a Contoso precisa de executar este cenário:

**Requisitos** | **Detalhes**
--- | ---
**Subscrição do Azure** | Contoso criada as subscrições anteriormente desta série de artigos. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se criar uma conta gratuita, será o administrador da sua subscrição e poderá executar todas as ações.<br/><br/> Se utilizar uma subscrição existente e não for o administrador, terá de trabalhar com o administrador para lhe atribuir permissões de proprietário ou contribuinte.
**Infraestrutura do Azure** | [Saiba como](contoso-migration-infrastructure.md) Contoso configurar anteriormente uma infraestrutura do Azure.
**Pré-requisitos do desenvolvedor** | A Contoso precisa de ferramentas a seguir numa estação de trabalho do desenvolvedor:<br/><br/> - [Visual Studio 2017 Community Edition: Versão 15.5](https://www.visualstudio.com/)<br/><br/> -Carga de trabalho .NET ativada.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [SDK do Service Fabric v 3.0 ou posterior](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (Windows 10) ou o Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) definido para utilizar contentores do Windows.



## <a name="scenario-steps"></a>Passos do cenário

Eis como Contoso é executada a migração:

> [!div class="checklist"]
> * **Passo 1: Aprovisionar uma instância de base de dados SQL no Azure**: Contoso Aprovisiona uma instância do SQL no Azure. Depois da VM é migrada para um contentor do Azure web de front-end, a instância de contentor com o front-end da web de aplicação irá apontar para esta base de dados.
> * **Passo 2: Criar um Azure Container Registry (ACR)**: Contoso aprovisiona um registo de contentor de enterprise para as imagens de contentor do docker.
> * **Passo 3: Aprovisionar o Azure Service Fabric**: Ele fornece um Cluster do Service Fabric.
> * **Passo 4: Gerir certificados de recursos de infraestrutura do serviço**: Contoso configura certificados para acesso de serviços de DevOps do Azure para o cluster.
> * **Passo 5: Migrar a base de dados com o DMA**: Ele migra a base de dados de aplicação com o Assistente de migração de base de dados.
> * **Passo 6: Configurar os serviços do Azure DevOps**: Contoso configura um projeto novo nos serviços de DevOps do Azure e importa o código para o repositório de Git.
> * **Passo 7: Converta a aplicação**: Contoso converte a aplicação para um contentor utilizando ferramentas de DevOps do Azure e o SDK.
> * **Passo 8: Configurar a criação e versão**: Contoso configura os pipelines de compilação e versão para criar e publicar a aplicação para o ACR e o Cluster do Service Fabric.
> * **Passo 9: Expandir as aplicações**: Depois da aplicação é pública, Contoso estende para tirar partido das capacidades do Azure e republishes-lo para o Azure com o pipeline.



## <a name="step-1-provision-an-azure-sql-database"></a>Passo 1: Aprovisionar uma base de dados SQL do Azure

Os administradores da Contoso aprovisionar uma base de dados SQL do Azure.

1. Eles selecionam para criar uma **base de dados SQL** no Azure. 

    ![Aprovisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Especifica um nome de base de dados de acordo com a base de dados em execução na VM no local (**SmartHotel.Registration**). A base de dados são colocados no grupo de recursos de ContosoRG. Este é o grupo de recursos que utilizam recursos de produção no Azure.

    ![Aprovisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Eles configurar uma nova instância do SQL Server (**smarthotel-sql-eus2**) na região primária.

    ![Aprovisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Se definir o escalão de preço para fazer face às necessidades de servidor e base de dados. E devem selecionar para poupar dinheiro com o benefício híbrido do Azure, uma vez que já têm uma licença do SQL Server.
5. Para o dimensionamento utilizam v Core-com base em comprar e defina os limites para os requisitos de esperado.

    ![Aprovisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Em seguida, é criar a instância de base de dados.

    ![Aprovisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. Depois da instância é criada, eles abrir a base de dados e anote os detalhes que necessitam, quando utilizarem a assistência de migração de base de dados para a migração.

    ![Aprovisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Precisa de mais ajuda?**

- [Obtenha ajuda](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) aprovisionar uma base de dados SQL.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) limites de recursos de núcleos.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Passo 2: Criar um ACR e aprovisionar um contentor do Azure

O contentor do Azure é criado com os ficheiros exportados a partir da VM de Web. O contentor está hospedado no Azure Container Registry (ACR).


1. Os administradores da Contoso criar um registo de contentor no portal do Azure.

     ![Registo de Contentor](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Eles fornecem um nome para o registo (**contosoacreus2**) e coloque-o na região primária, no grupo de recursos que utilizam para seus recursos de infraestrutura. Ativar o acesso para os utilizadores administradores e defini-lo como um SKU premium, de modo a que podem tirar partido de georreplicação.

    ![Registo de Contentor](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Passo 3: Aprovisionar o Azure Service Fabric

O contentor da SmartHotel360 será executado no Cluster do Azure Service Fabric. Os administradores da Contoso criar o Cluster do Service Fabric da seguinte forma:

1. Criar um recurso de infraestrutura do serviço do Azure Marketplace

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. Na **Noções básicas**, fornecem um nome exclusivo de DS para o cluster e credenciais para aceder a VM no local. Eles colocam o recurso no grupo de recursos de produção (**ContosoRG**) na região E.U.A. Leste 2 primária.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. Na **configuração do tipo de nó**, eles um nome de tipo de nó, as definições de durabilidade, tamanho da VM e pontos finais da aplicação de entrada.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. Na **criar Cofre de chaves**, ser criar um novo cofre de chaves no seu grupo de recursos de infraestrutura, para abrigar o certificado.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. Na **políticas de acesso** dão acesso a máquinas virtuais a implementar o Cofre de chaves.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. Especifica um nome para o certificado.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. Na página Resumo, eles copie a ligação que é utilizada para transferir o certificado. Eles precisam, esta opção para ligar ao Cluster do Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. Depois de passar a validação, eles aprovisionam o cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. No Assistente para importar certificados, eles importam o certificado transferido para máquinas de desenvolvimento. O certificado é utilizado para autenticar para o cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. Depois do cluster for aprovisionado, se ligam para o Cluster do Service Fabric Explorer.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. Terá de selecionar o certificado correto.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. As cargas do Service Fabric Explorer e o administrador da Contoso, podem gerir o cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-4-manage-service-fabric-certificates"></a>Passo 4: Gerir certificados de Service Fabric

A Contoso precisa de certificados de cluster para permitir o acesso de serviços de DevOps do Azure para o cluster. Os administradores da Contoso defina esta opção cópia de segurança.

1. São abertos do portal do Azure e navegue para o Cofre de chaves.
2. Abra os certificados e copie o thumbprint do certificado que foi criado durante o processo de aprovisionamento.

    ![Copie o thumbprint](./media/contoso-migration-rearchitect-container-sql/cert1.png)
 
3. Eles copiá-lo num arquivo de texto para futura referência.
4. Agora, eles adicionar um certificado de cliente que irá tornar-se de um certificado de cliente de administrador no cluster. Isso permite que os serviços de DevOps do Azure ligar ao cluster para a implementação de aplicação no pipeline de versões. Para fazer eles, eles abrir o Cofre de chaves no portal em selecione **certificados** > **gerar/importar**.

    ![Gerar o certificado de cliente](./media/contoso-migration-rearchitect-container-sql/cert2.png)

5. Introduza o nome do certificado e fornecer um nome único do X.509 no **assunto**.

     ![Nome do certificado](./media/contoso-migration-rearchitect-container-sql/cert3.png)

6. Depois do certificado é criado, transferem-lo localmente no formato PFX.

     ![Transferir certificado](./media/contoso-migration-rearchitect-container-sql/cert4.png)

7. Agora, eles voltar para a lista de certificados no Cofre de chaves e copie o thumbprint do certificado de cliente apenas foi criado. Eles guardá-lo no arquivo de texto.

     ![Thumbprint do certificado de cliente](./media/contoso-migration-rearchitect-container-sql/cert5.png)

8. Para a implementação de serviços do Azure DevOps, eles precisam determinar o valor da Base64 do certificado. Eles fazem-lo na estação de trabalho de desenvolvedor local com o PowerShell. Eles colar o resultado num arquivo de texto para utilização posterior.

    ```powershell
        [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\path\to\certificate.pfx")) 
    ```

     ![Valor de Base64](./media/contoso-migration-rearchitect-container-sql/cert6.png)

9. Por fim, eles adicionar o novo certificado para o cluster do Service Fabric. Para tal, no portal, abra o cluster e clique em **segurança**.

     ![Adicionar certificado de cliente](./media/contoso-migration-rearchitect-container-sql/cert7.png)

10. Clicarem **Add** > **cliente administrativo**e cole o thumbprint do novo certificado de cliente. Em seguida, clique em **adicionar**. Esta ação pode demorar até 15 minutos.

     ![Adicionar certificado de cliente](./media/contoso-migration-rearchitect-container-sql/cert8.png)

## <a name="step-5-migrate-the-database-with-dma"></a>Passo 5: Migrar a base de dados com o DMA

Os administradores da Contoso agora podem migrar a base de dados da SmartHotel360 com o DMA.

### <a name="install-dma"></a>Instalar o DMA

1. Eles transferirem a ferramenta a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) para a VM do SQL Server no local (**SQLVM**).
2. Eles executam a configuração (Downloadmigrationassistant) na VM.
3. Sobre o **concluir** página, selecione **iniciar Microsoft Data Migration Assistant** antes de concluir o assistente.

### <a name="configure-the-firewall"></a>Configurar a firewall

Para ligar à base de dados SQL do Azure, os administradores da Contoso configurar uma regra de firewall para permitir o acesso.

1. Na **firewalls e redes virtuais** propriedades para a base de dados, que permite o acesso aos serviços do Azure e adicionar uma regra para o endereço IP do cliente da VM do SQL Server no local.
2. É criada uma regra de firewall ao nível do servidor.

    ![Firewall](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Precisa de mais ajuda?

[Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) criar e gerir regras de firewall para a base de dados do Azure SQL.

### <a name="migrate"></a>Migrar

Os administradores da Contoso agora migrar a base de dados.

1. O DMA criar um novo projeto (**SmartHotelDB**) e selecione **migração** 
2. Selecionar o tipo de servidor de origem como **SQL Server**e o destino como **base de dados do Azure SQL**. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. Nos detalhes de migração, adicione **SQLVM** que o servidor de origem e o **SmartHotel.Registration** base de dados. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. Eles recebem um erro que parece estar associado a autenticação. No entanto depois de investigar, o problema é o ponto final (.) no nome da base de dados. Como solução, eles decidem se pretendem aprovisionar uma base de dados do SQL nova com o nome **SmartHotel-registo**, para resolver o problema. Quando executam DMA novamente, eles podem selecionar **SmartHotel-registo**e continuar com o assistente.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. Na **selecionar objetos**, selecione as tabelas de base de dados e gerar um script SQL.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. Depois do DMS cria o script, pode clicar em **implementar esquema**.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA confirma que a implementação foi concluída com êxito.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. Agora que iniciar a migração.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. Depois de concluída a migração, a Contoso pode verificar que a base de dados está em execução na instância do SQL do Azure.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Se eliminar a base de dados do SQL extra **SmartHotel.Registration** no portal do Azure.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)


## <a name="step-6-set-up-azure-devops-services"></a>Passo 6: Configurar serviços de DevOps do Azure

A Contoso precisa de criar a infraestrutura de DevOps e pipelines para a aplicação.  Para tal, os administradores da Contoso criar um novo projeto de DevOps do Azure, importar seu código e, em seguida, criar e lançar pipelines.

1.   Na conta do Azure DevOps da Contoso, eles criam um novo projeto (**ContosoSmartHotelRearchitect**) e selecione **Git** para controle de versão.
![Novo projeto](./media/contoso-migration-rearchitect-container-sql/vsts1.png)

2. Eles importam o repositório do Git que atualmente possui o seu código de aplicação. Está a ser um [repositório público](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) e pode baixá-lo.

    ![Baixe o código da aplicação](./media/contoso-migration-rearchitect-container-sql/vsts2.png)

3. Depois do código foi importado, eles conectar o Visual Studio para o repositório e clonagem o código usando o Team Explorer.

4. Depois do repositório é clonado para a máquina do desenvolvedor, eles abrem o ficheiro de solução para a aplicação. O serviço web app e o wcf cada ter separar projeto dentro do arquivo.

    ![Ficheiro de solução](./media/contoso-migration-rearchitect-container-sql/vsts4.png)

## <a name="step-7-convert-the-app-to-a-container"></a>Passo 7: Converta a aplicação para um contentor

A aplicação no local é uma aplicação de três camadas tradicional:

- Ela contém WebForms e um serviço WCF a ligar ao SQL Server.
- Ele usa o Entity Framework para integrar com os dados na base de dados SQL, expondo-as através de um serviço WCF.
- O aplicativo de WebForms interage com o serviço do WCF.

Os administradores da Contoso irão converter a aplicação para um contentor utilizando o Visual Studio e as ferramentas do SDK, da seguinte forma:


1. Com o Visual Studio, ele Revise o arquivo de solução aberta (SmartHotel.Registration.sln) no **SmartHotel360-interna-reservas-apps\src\Registration** diretório do repositório local.  São apresentadas duas aplicações. O front-end web SmartHotel.Registration.Web e a aplicação de serviço do WCF SmartHotel.Registration.WCF.

    ![Contentor](./media/contoso-migration-rearchitect-container-sql/container2.png)


2. Eles com o botão direito na aplicação web > **Add** > **suporte a contentores do Orchestrator**.
3. Na **adicionar suporte para contentores orquestra**, eles selecionam **Service Fabric**.

    ![Contentor](./media/contoso-migration-rearchitect-container-sql/container3.png)
    
4. Eles repetir o processo para a aplicação de SmartHotel.Registration.WCF.
5. Agora, eles verificam como a solução foi alterada.

   - A nova aplicação é **SmartHotel.RegistrationApplication/**
   - Ele contém dois serviços: **SmartHotel.Registration.WCF** e **SmartHotel.Registration.Web**.

     ![Contentor](./media/contoso-migration-rearchitect-container-sql/container4.png)

6. Visual Studio criou o ficheiro de Docker e obtida as imagens necessárias localmente na máquina do desenvolvedor.

    ![Contentor](./media/contoso-migration-rearchitect-container-sql/container5.png)

7. Um arquivo de manifesto (**servicemanifest. XML**) é criado e aberto pelo Visual Studio. Este ficheiro diz ao Service Fabric como configurar o contentor quando for implementada para o Azure.

    ![Contentor](./media/contoso-migration-rearchitect-container-sql/container6.png)

8. Outro ficheiro de manifesto (* * applicationmanifest. xml) contém os aplicativos de configuração para os contentores.

    ![Contentor](./media/contoso-migration-rearchitect-container-sql/container7.png)

9. Abrir o **ApplicationParameters/Cloud.xml** de ficheiros e atualizar a cadeia de ligação para ligar a aplicação à base de dados SQL do Azure. A cadeia de ligação pode estar localizada na base de dados no portal do Azure.

    ![Cadeia de ligação](./media/contoso-migration-rearchitect-container-sql/container8.png)

10. O código atualizado de consolidarem e enviarem para serviços de DevOps do Azure.

    ![Consolidação](./media/contoso-migration-rearchitect-container-sql/container9.png)

## <a name="step-8-build-and-release-pipelines-in-azure-devops-services"></a>Passo 8: Criar e lançar pipelines nos serviços de DevOps do Azure

Os administradores de contoso agora configurar serviços de DevOps do Azure para executar a compilação e versão processo à ação as práticas de DevOps.

1. Nos serviços de DevOps do Azure, clique **criar e lançar** > **novo pipeline**.

    ![Novo pipeline](./media/contoso-migration-rearchitect-container-sql/pipeline1.png)

2. Eles selecionam **Git de serviços do Azure DevOps** e o repositório relevante.

    ![Git e do repositório](./media/contoso-migration-rearchitect-container-sql/pipeline2.png)

3. Na **selecionar um modelo**, eles selecionam recursos de infraestrutura com o suporte do Docker.

     ![Recursos de infraestrutura e a Docker](./media/contoso-migration-rearchitect-container-sql/pipeline3.png)
    
4. Alterar as imagens de etiqueta de ação **crie uma imagem**e configurar a tarefa para utilizar o ACR aprovisionado.

     ![Registo](./media/contoso-migration-rearchitect-container-sql/pipeline4.png)

5. Na **enviar imagens** tarefa, configure a imagem a ser enviadas para o ACR e selecione para incluir a etiqueta mais recente.
6. Na **Acionadores**, ative a integração contínua e adicionar o ramo principal.

    ![Acionadores](./media/contoso-migration-rearchitect-container-sql/pipeline5.png)

7. Clicarem **guardar e colocar em fila** para iniciar uma compilação.
8. Após a compilação for concluída com êxito, eles passar para o pipeline de lançamento. Nos serviços do Azure DevOps clicarem **versões** > **novo pipeline**.

    ![Pipeline de lançamento](./media/contoso-migration-rearchitect-container-sql/pipeline6.png)    

9. Eles selecionam os **implementação do Azure Service Fabric** modelo e o nome do estágio (**SmartHotelSF**).

    ![Ambiente](./media/contoso-migration-rearchitect-container-sql/pipeline7.png)

10. Eles fornecem um nome do pipeline (**ContosoSmartHotel360Rearchitect**). Para o estágio, clicarem **1 tarefa, 1 tarefa** para configurar a implementação do Service Fabric.

    ![Fase e tarefas](./media/contoso-migration-rearchitect-container-sql/pipeline8.png)

11. Agora, clicar **New** para adicionar uma nova ligação do cluster.

    ![Nova ligação](./media/contoso-migration-rearchitect-container-sql/pipeline9.png)

12. Na **ligação de serviço do Service Fabric adicionar**, configurar a ligação e as definições de autenticação que serão utilizadas pelos serviços de DevOps do Azure para implementar a aplicação. O ponto de extremidade do cluster pode estar localizado no portal do Azure e adicionam **tcp: / /** como um prefixo.
13. As informações do certificado recolheram é a entrada na **Thumbprint do certificado de servidor** e **certificado de cliente**.

    ![Certificado](./media/contoso-migration-rearchitect-container-sql/pipeline10.png)

13. Eles clicam no pipeline > **adicionar um artefato**.

     ![Artefacto](./media/contoso-migration-rearchitect-container-sql/pipeline11.png)

14. Eles selecione o projeto e criar o pipeline, com a versão mais recente.

     ![Compilação](./media/contoso-migration-rearchitect-container-sql/pipeline12.png)

15. Tenha em atenção que o bolt extremamente no artefacto é verificado.

     ![Estado de artefacto](./media/contoso-migration-rearchitect-container-sql/pipeline13.png)

16. Além disso, tenha em atenção que o acionador de implementação contínua está ativado.

    ![Implementação contínua ativada](./media/contoso-migration-rearchitect-container-sql/pipeline14.png) 

17. Clicarem **salvar** > **criar uma versão**.

    ![Libertar](./media/contoso-migration-rearchitect-container-sql/pipeline15.png)

18. Após a conclusão da implementação, SmartHotel360 agora executará o Service Fabric.

    ![Publicar](./media/contoso-migration-rearchitect-container-sql/publish4.png)

19. Para ligar à aplicação, de direcionar o tráfego para o endereço IP público do Balanceador de carga do Azure à frente de nós do Service Fabric.

    ![Publicar](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-9-extend-the-app-and-republish"></a>Passo 9: Expanda a aplicação e voltar a publicar

Após a aplicação da SmartHotel360 e a base de dados estão em execução no Azure, a Contoso pretende expandir as aplicações.

- Os desenvolvedores da Contoso estão criando um protótipo de um novo aplicativo do .NET Core que será executada no cluster do Service Fabric.
- A aplicação será utilizada para extrair dados de sentimento de cosmos DB.
- Estes dados serão sob a forma de Tweets que são processados utilizando uma função do Azure sem servidor e a API de análise de texto dos serviços cognitivos.

### <a name="provision-azure-cosmos-db"></a>Aprovisionar o Azure Cosmos DB

Como primeiro passo, os administradores da Contoso aprovisionar uma base de dados do Cosmos do Azure.

1. Se criaram um recurso do Azure Cosmos DB no Azure Marketplace.

    ![Extensão](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Eles fornecem um nome de base de dados (**contososmarthotel**), selecione a API de SQL e colocar o recurso no grupo de recursos de produção, na região E.U.A. Leste 2 primária.

    ![Extensão](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. Na **introdução**, eles selecionam **Data Explorer**e adicione uma nova coleção.
4. Na **adicionar coleção** fornecem IDs e definir a capacidade de armazenamento e débito.

    ![Extensão](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. No portal, que abrem a nova base de dados > **recolha** > **documentos** e clique em **novo documento**.
6. Eles cole o seguinte código JSON para a janela de documento. Estes são dados de exemplo na forma de um único tweet.

    ```json
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel360",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Extensão](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. Eles localizam o ponto de final do Cosmos DB e a chave de autenticação. Estes são utilizados na aplicação para ligar à coleção. Na base de dados, clique **chaves**e copiar o URI e a chave primária para o bloco de notas.

    ![Extensão](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>Atualizar a aplicação de sentimentos

Com o Cosmos DB aprovisionado, os administradores da Contoso podem configurar a aplicação para ligar à mesma.

1. No Visual Studio, abrir o ficheiro ApplicationModern\ApplicationParameters\cloud.xml no Explorador de soluções.

    ![Aplicação de sentimentos](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Mostram os dois parâmetros seguintes:

   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![Aplicação de sentimentos](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Voltar a publicar a aplicação

Após expandir a aplicação, os administradores da Contoso publicá-la novamente para o Azure com o pipeline.

1. Consolidarem e enviarem seu código para serviços de DevOps do Azure. Isso ativa os pipelines de compilação e versão.

2. Após a conclusão da compilação e implementação, SmartHotel360 agora executará o Service Fabric. O console de gerenciamento de recursos de infraestrutura do serviço mostra agora três serviços.

    ![Voltar a publicar](./media/contoso-migration-rearchitect-container-sql/republish3.png)

3. Agora pode clicarem através dos serviços que a aplicação de SentimentIntegration está em execução

    ![Voltar a publicar](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Limpar após a migração

Após a migração, a Contoso precisa de concluir estes passos de limpeza:  

- Remova as VMs no local no inventário do vCenter.
- Remova as VMs das tarefas de cópia de segurança locais.
- Atualize a documentação interna para mostrar as novas localizações para a aplicação da SmartHotel360. Mostre a base de dados como em execução na base de dados SQL do Azure e o front-end como em execução no Service Fabric.
- Reveja todos os recursos que interagem com as VMs desativadas e atualizar as definições relevantes ou documentação para refletir a nova configuração.


## <a name="review-the-deployment"></a>Reveja a implementação

Com os recursos migrados no Azure, a Contoso precisa totalmente operacionalizar e proteger sua infra-estrutura de novo.

### <a name="security"></a>Segurança

- Os administradores da Contoso tem de garantir que seus novos **SmartHotel-registo** base de dados é segura. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Em particular, eles devem atualizar o contentor para utilizar SSL com certificados.
- Eles devem considerar a utilização do Cofre de chaves para proteger segredos para as aplicações do Service Fabric. [Saiba mais](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Cópias de segurança

- A Contoso precisa de analisar os requisitos de cópia de segurança da base de dados do SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Os administradores da Contoso devem considerar a implementação de grupos de ativação pós-falha para fornecer ativação pós-falha para a base de dados. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Podem tirar partido de georreplicação para a SKU de premium do ACR. [Saiba mais](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Contoso tem de considerar implementar a aplicação Web na região E.U.A. Central e principal E.U.A. Leste 2 quando a aplicação Web para contentores for disponibilizada. Os administradores da Contoso foi possível configurar o Gestor de tráfego para garantir que a ativação pós-falha em caso de falhas regionais.
- O cosmos DB faz o backup automaticamente. Contoso [Leia sobre](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) este processo para obter mais informações.

### <a name="licensing-and-cost-optimization"></a>Otimização de licenciamento e de custo

- Depois de todos os recursos são implementados, Contoso deve atribuir etiquetas do Azure com base em [planejamento da infraestrutura](contoso-migration-infrastructure.md#set-up-tagging).
- Licenciamento de todos os baseia-se para o custo dos serviços PaaS que Contoso está a consumir. Isso é deduzido do EA.
- Contoso irá ativar o Azure Cost Management licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gestão de custos multiclouds que o ajuda a utilizar e gerir o Azure e outros recursos da cloud.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Azure Cost Management.

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso refatorado a aplicação da SmartHotel360 no Azure ao migrar o VM da front-end de aplicação no Service Fabric. A base de dados de aplicação foi migrada para uma base de dados SQL do Azure.





