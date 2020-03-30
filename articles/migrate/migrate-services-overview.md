---
title: Acerca do Azure Migrate
description: Conheça o serviço Azure Migrate.
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: a9723f15d496393d27bdd227ec1121a7878b37e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127734"
---
# <a name="about-azure-migrate"></a>Acerca do Azure Migrate

Este artigo fornece uma visão geral rápida do serviço Azure Migrate.

A Azure Migrate fornece um hub centralizado para avaliar e migrar no local servidores, infraestruturas, aplicações e dados para o Azure. A Azure Migrate fornece as seguintes funcionalidades:

- **Plataforma de migração unificada**: Um único portal para iniciar, correr e acompanhar a sua viagem de migração até Azure.
- **Gama de ferramentas**: Uma gama de ferramentas para avaliação e migração. As ferramentas incluem Azure Migrate: Server Assessment, e Azure Migrate: Server Migration. A Azure Migrate integra-se com outros serviços Azure, e com outras ferramentas e ofertas independentes de fornecedores de software (ISV).
- **Avaliação e migração**: No centro de migração Azure pode avaliar e migrar:
    - **Servidores**: Avaliar e migrar servidores no local para VMs Azure.
    - **Bases**de dados : Avaliar e migrar bases de dados no local para O Azure SQL DB, ou para a Instância Gerida Azure SQL.
    - **Aplicações web**: Avaliar e migrar aplicações web no local para o Serviço de Aplicações Azure, utilizando o Assistente de Serviço de Aplicações Azure.
    - **Ambientes de trabalho virtuais**: Avalie e emigra a sua infraestrutura virtual de ambiente de trabalho no local (VDI), para windows Virtual Desktop em Azure.
    - **Dados**: Migrar grandes quantidades de dados para o Azure de forma rápida e económica, utilizando produtos da Caixa de Dados Azure. 


## <a name="integrated-tools"></a>Ferramentas integradas

O hub Azure Migrate inclui estas ferramentas.

**Ferramenta** | **Avaliar/Migrar** | **Detalhes**
--- | --- | ---
**Azure Migrate:Avaliação do Servidor** | Avaliar servidores. | Descubra e avalie no local VMs VMs, VMs Hiper-V e servidores físicos, em preparação para a migração para O Azure.
**Migração Azure:Migração do servidor** | Servidores migradores. | VMs migratórios, VMs Hiper-V, servidores físicos, outras máquinas virtualizadas, e VMs de nuvem pública, para Azure. 
**Assistente de Migração de Bases de Dados (DMA)** | Avalie as bases de dados do SQL Server no local para migração para O Azure SQL DB, Azure SQL Managed Instance ou para Os VMs Azure que executam o Servidor SQL. | O DMA ajuda a identificar potenciais problemas de bloqueio para a migração. Identifica funcionalidades não suportadas, novas funcionalidades de que pode beneficiar após a migração, e ajuda-o a identificar o caminho certo para a migração de bases de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Serviço de Migração de Bases de Dados (DMS)** | Migrar bases de dados no local para VMs Azure executando SQL, Azure SQL DB e Pôr Pino SQL Managed Instances. | [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview) sobre o DMS.
**Movere** | Avaliar servidores. | [Saiba mais](#movere) sobre Movere.
**Assistente de migração de aplicativos web** | Avaliar e migrar aplicações web no local para o Azure. |  Utilize o Assistente de Migração do Serviço de Aplicações Azure para avaliar os websites no local para migração para o Serviço de Aplicações Azure.<br/><br/> Utilize o Assistente para migrar as aplicações web .NET e PHP para o Azure. [Saiba mais](https://appmigration.microsoft.com/) sobre o Assistente de Migração do Serviço de Aplicações Azure.
**Azure Data Box** | Migração de dados offline. | Utilize os produtos Azure Data Box para mover grandes quantidades de dados offline para o Azure. [Saiba mais](https://docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>Integração ISV

A Azure Migrate integra-se com uma série de ofertas isv. 

**ISV**    | **Funcionalidade**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Servidores migradores
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar servidores
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar servidores
[Dispositivo 42](https://docs.device42.com/) | Avaliar servidores
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar o VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Servidores migradores
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar servidores
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar servidores e bases de dados


## <a name="azure-migrate-server-assessment-tool"></a>Ferramenta de avaliação do servidor migratório Azure

A ferramenta Azure Migrate:Server Assessment descobre e avalia vMs no local, VMs hiper-V e servidores físicos para migração para Azure. Aqui está o que a ferramenta faz:

- **Prontidão azure:** Avalia se as máquinas no local estão prontas para a migração para Azure.
- **Tamanho azul:** Estima o tamanho dos VMs Azure após a migração.
- **Estimativa de custos do Azure:** Custos estimados para executar servidores no local em Azure.
- **Análise de dependência:** Se utilizar a Avaliação do Servidor com análise de [dependência,](concepts-dependency-visualization.md)pode identificar efetivamente dependências de servidores cruzados e otimizar estratégias para mover servidores interdependentes para o Azure.


A Avaliação do Servidor utiliza um [aparelho ligeiro de migração Azure](migrate-appliance.md) que se implanta no local.

- O aparelho funciona num VM ou num servidor físico. Pode instalá-lo facilmente usando um modelo descarregado.
- O aparelho descobre máquinas no local e envia continuamente metadados de máquinas e dados de desempenho para o Azure Migrate.
- A descoberta do aparelho é sem agente. Nada está instalado em máquinas descobertas.
- Após a descoberta do aparelho, reúne máquinas descobertas em grupos e executa avaliações para um grupo.

## <a name="azure-migrate-server-migration-tool"></a>Ferramenta de Migração do Servidor do Azure Migrate

A ferramenta De migração Azure Migrate:Server ajuda-o a migrar vMs no local, VMs Hiper-V, servidores físicos, outras máquinas virtualizadas e VMs de nuvem pública, para Azure. Pode migrar máquinas depois de as avaliar, ou emigrar sem uma avaliação. 

Para a migração sem agentes de VMware VMs e migração de VMs Hiper-V, a Server Migration utiliza um aparelho Azure Migrate que você implanta no local. O aparelho também é utilizado se configurar a avaliação do servidor, e é descrito na secção anterior.


## <a name="selecting-assessmentmigration-tools"></a>Selecionar ferramentas de avaliação/migração

No hub Azure Migrate, seleciona a ferramenta que pretende utilizar para avaliação ou migração e adicione-a a um projeto Azure Migrate. Se adicionar uma ferramenta ISV ou Movere:

- Para começar, obtenha uma licença, ou inscreva-se para um teste gratuito, de acordo com as instruções da ferramenta. O licenciamento de ferramentas é determinado pelo ISV ou ferramenta. 
- Em cada ferramenta, há uma opção para ligar ao Azure Migrate. Siga as instruções da ferramenta para ligar.
- Você rastreia sua jornada de migração de dentro do projeto Azure Migrate, através de todas as ferramentas.


## <a name="movere"></a>Movere

Movere é uma plataforma SaaS que aumenta a inteligência empresarial apresentando com precisão ambientes de TI inteiros num único dia. À medida que as organizações crescem, mudam e otimizam digitalmente, a solução proporciona às empresas a confiança de que precisam para terem visibilidade e controlo dos seus ambientes, independentemente da plataforma, aplicação ou geografia. A Movere foi [adquirida](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) pela Microsoft e já não é vendida como uma oferta autónoma.  A Movere está disponível através dos Programas de Avaliação de Soluções da Microsoft e da Economia da Nuvem. [Saiba mais](https://www.movere.io) sobre Movere. 

Encorajamo-lo a olhar também para o Azure Migrate, o nosso serviço de migração incorporado. A Azure Migrate fornece um centro central para simplificar a sua migração para a nuvem. O hub possui um suporte abrangente para diferentes cargas de trabalho, incluindo servidores físicos e virtuais, bases de dados e aplicações. A visibilidade de ponta a ponta facilita o acompanhamento do progresso ao longo da descoberta, avaliação e migração. Com ferramentas ISV Azure e partner incorporadas, a Azure Migrate também tem uma vasta gama de funcionalidades, incluindo a descoberta de servidores virtuais e físicos, dimensionamento de direitobaseado no desempenho, planeamento de custos, avaliações baseadas em importação e aplicação sem agente análise de dependência. Se procura ajuda especializada para começar, a Microsoft tem um fornecedor de [serviços gerido](https://azure.microsoft.com/partners) por especialistas em Azure para guiá-lo ao longo da viagem. Confira o [site azure migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Versões Azure Migrate

Existem duas versões do serviço Azure Migrate:

- **Versão atual**: Utilize esta versão para criar projetos Azure Migrate, descubra máquinas no local e orquequequeas avaliações e migrações. [Saiba mais](whats-new.md) sobre o que há de novo nesta versão.
- **Versão anterior**: Se utilizou a versão anterior do Azure Migrate (apenas foi suportada a avaliação dos VMware VMware no local), deverá agora utilizar a versão atual. Já não é possível criar projetos Da Migração Azure utilizando a versão anterior, e recomendamos que não realize novas descobertas. Para aceder a projetos existentes, no portal Azure, procure e selecione **Azure Migrate.** No painel de migração **Azure,** há uma notificação e um link para aceder a antigos projetos azure migrate.



## <a name="next-steps"></a>Passos seguintes

- Experimente os nossos tutoriais para avaliar [VMs VMs,](tutorial-prepare-vmware.md) [VMs Hiper-V](tutorial-prepare-hyper-v.md)ou [servidores físicos.](tutorial-prepare-physical.md)
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
