---
title: Acerca do Azure Migrate
description: Conheça o serviço Azure Migrate.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fe6386346282cf182397f6420c541d629ba0aab3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81768395"
---
# <a name="about-azure-migrate"></a>Acerca do Azure Migrate

Este artigo fornece uma visão geral rápida do serviço Azure Migrate.

A Azure Migrate fornece um hub centralizado para avaliar e migrar para os servidores, infraestruturas, aplicações e dados do Azure.

A Azure Migrate fornece as seguintes funcionalidades:

- Plataforma de **migração unificada**: Um único portal para iniciar, correr e acompanhar a sua migração para Azure.
- **Gama de ferramentas**: Uma gama de ferramentas para avaliação e migração. As ferramentas incluem Azure Migrate: Server Assessment e Azure Migrate: Server Migration. A Azure Migrate integra-se com outros serviços Azure e com outras ferramentas e ofertas independentes de fornecedores de software (ISV).
- **Avaliação e migração**: No centro de migração Azure, pode avaliar e migrar:
    - **Servidores**: Avaliar os servidores no local e migrar para máquinas virtuais Azure.
    - **Bases**de dados : Avaliar as bases de dados no local e emigrar para a Base de Dados Azure SQL ou para uma instância gerida pela Base de Dados Azure SQL.
    - **Aplicações web**: Avaliar as aplicações web no local e emigrar para o Serviço de Aplicações Azure utilizando o Assistente de Migração do Serviço de Aplicações Azure.
    - **Ambientes**de trabalho virtuais : Avalie a sua infraestrutura virtual de ambiente de trabalho no local (VDI) e emigre-a para o Windows Virtual Desktop em Azure.
    - **Dados**: Migrar grandes quantidades de dados para o Azure de forma rápida e económica utilizando produtos da Caixa de Dados Azure.

## <a name="integrated-tools"></a>Ferramentas integradas

O hub Azure Migrate inclui estas ferramentas:

**Ferramenta** | **Avaliar e migrar** | **Detalhes**
--- | --- | ---
**Azure Migrate: Avaliação do servidor** | Avaliar servidores. | Descubra e avalie vMs no local, VMs hiper-V e servidores físicos em preparação para a migração para O Azure.
**Migração Azure: Migração do servidor** | Servidores migradores. | VMs migratórios, VMs hiper-V, servidores físicos, outras máquinas virtualizadas e VMs de nuvem pública para Azure.
**Assistente de Migração de Dados** | Avaliar as bases de dados do SQL Server no local para migração para a Base de Dados Azure SQL, uma instância gerida pela Base de Dados Azure SQL, ou VMs Azure que executam o Servidor SQL. | O Assistente de Migração de Dados ajuda a identificar potenciais problemas que bloqueiam a migração. Identifica funcionalidades não suportadas, novas funcionalidades que podem beneficiar-lhe após a migração e o caminho certo para a migração de bases de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Azure Database Migration Service** | Migrar bases de dados no local para VMs Azure com o Servidor SQL, base de dados Azure SQL ou Base de Dados Azure SQL geridas. | [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview) sobre o Serviço de Migração de Bases de Dados.
**Movere** | Avaliar servidores. | [Saiba mais](#movere) sobre Movere.
**Assistente de migração de aplicativos web** | Avalie as aplicações web no local e emigra-as para o Azure. |  Utilize o Assistente de Migração do Serviço de Aplicações Azure para avaliar os websites no local para migração para o Serviço de Aplicações Azure.<br/><br/> Use o Assistente de Migração para migrar .NET e aplicações web PHP para O Azure. [Saiba mais](https://appmigration.microsoft.com/) sobre o Assistente de Migração do Serviço de Aplicações Azure.
**Azure Data Box** | Migrar dados offline. | Utilize os produtos Azure Data Box para mover grandes quantidades de dados offline para o Azure. [Saiba mais](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Se estiver no Governo Azure, ferramentas integradas externas e ofertas isv não podem enviar dados para projetos da Azure Migrate. Pode utilizar ferramentas de forma independente.

## <a name="isv-integration"></a>Integração ISV

A Azure Migrate integra-se com várias ofertas do ISV. 

**ISV**    | **Funcionalidade**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Servidores migradores.
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar servidores.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar servidores.
[Device42](https://docs.device42.com/) | Avaliar servidores.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar vDI.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Servidores migradores.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar servidores.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar servidores e bases de dados.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Ferramenta de avaliação do servidor

A ferramenta Azure Migrate: Server Assessment descobre e avalia vMs no local, VMs hiper-V e servidores físicos para migração para Azure.

Aqui está o que a ferramenta faz:

- **Prontidão azure**: Avalia se as máquinas no local estão prontas para a migração para Azure.
- Tamanho **azul**: Estima o tamanho dos VMs Azure após a migração.
- **Estimativa de custos do Azure**: Estima os custos para o funcionamento dos servidores no local em Azure.
- **Análise de dependência**: Identifica dependências de servidores cruzados e estratégias de otimização para mover servidores interdependentes para o Azure. Saiba mais sobre a Avaliação do Servidor com [análise de dependência.](concepts-dependency-visualization.md)

A Avaliação do Servidor utiliza um [aparelho ligeiro de migração Azure](migrate-appliance.md) que se implanta no local.

- O aparelho funciona num VM ou num servidor físico. Pode instalá-lo facilmente usando um modelo descarregado.
- O aparelho descobre máquinas no local. Também envia continuamente metadados de máquinas e dados de desempenho para o Azure Migrate.
- A descoberta do aparelho é sem agente. Nada está instalado em máquinas descobertas.
- Após a descoberta do aparelho, pode recolher máquinas descobertas em grupos e fazer avaliações para cada grupo.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Ferramenta de migração do servidor

A ferramenta Azure Migrate: Server Migration ajuda-o a migrar para OAzure:

- VMs VMware no local
- VMs Hyper-V
- Servidores físicos
- Outras máquinas virtualizadas
- VMs de nuvem pública

Pode migrar máquinas depois de as avaliar ou migrar sem uma avaliação.

Para a migração sem agentes de VMware VMs e migração de VMs Hiper-V, a Server Migration utiliza um aparelho Migratório Azure que você implanta no local. O aparelho também é utilizado se configurar a avaliação do servidor. Está descrito na secção anterior.

## <a name="selecting-assessment-and-migration-tools"></a>Selecionar ferramentas de avaliação e migração

No hub Azure Migrate, seleciona a ferramenta que pretende utilizar para avaliação ou migração e adicione-a a um projeto Azure Migrate. Se adicionar uma ferramenta ISV ou Movere:

- Para começar, obtenha uma licença ou inscreva-se para um teste gratuito seguindo as instruções da ferramenta. Cada ISV ou ferramenta especifica o licenciamento da ferramenta.
- Cada ferramenta tem uma opção de ligação ao Azure Migrate. Siga as instruções da ferramenta para ligar.
- Acompanhe a sua migração através de todas as ferramentas do projeto Azure Migrate.

## <a name="movere"></a>Movere

Movere é uma plataforma de serviço (SaaS). Aumenta a inteligência empresarial apresentando com precisão ambientes de TI inteiros num único dia. Organizações e empresas crescem, mudam e otimizam digitalmente. Ao fazê-lo, a Movere proporciona-lhes a confiança necessária para ver e controlar os seus ambientes, independentemente da plataforma, aplicação ou geografia.

A Microsoft [adquiriu](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) a Movere, e já não é vendida como uma oferta autónoma. A Movere está disponível através da Microsoft Solution Assessment e do Microsoft Cloud Economics Program. [Saiba mais](https://www.movere.io) sobre Movere.

Encorajamo-lo a olhar também para o Azure Migrate, o nosso serviço de migração incorporado. A Azure Migrate fornece um centro central para simplificar a migração da nuvem. O hub suporta de forma abrangente cargas de trabalho como servidores físicos e virtuais, bases de dados e aplicações. A visibilidade de ponta a ponta permite-lhe acompanhar facilmente o progresso ao longo da descoberta, avaliação e migração.

Com ferramentas ISV Azure e partner incorporadas, a Azure Migrate tem uma vasta gama de funcionalidades, incluindo:

- Descoberta de servidores virtuais e físicos.
- Direitos baseados no desempenho.
- Planeamento de custos.
- Avaliações baseadas em importações.
- Análise de dependência de aplicações sem agente.

Se procura ajuda especializada para começar, a Microsoft tem fornecedores de [serviços geridos](https://azure.microsoft.com/partners) por especialistas em Azure para guiá-lo. Confira o [site azure migrate](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Versões Azure Migrate

Existem duas versões do serviço Azure Migrate.

- **Versão atual**: Utilize esta versão para criar projetos Azure Migrate, descubra máquinas no local e orquequequeas avaliações e migrações. [Saiba mais](whats-new.md) sobre o que há de novo nesta versão.
- **Versão anterior**: A versão anterior do Azure Migrate suporta apenas a avaliação de VMware vMware no local. Se utilizou a versão anterior, deve agora utilizar a versão atual. Já não é possível criar projetos Azure Migrate utilizando a versão anterior. E recomendamos que não faça novas descobertas com ele.

    Para aceder a projetos existentes no portal Azure, procure e selecione **Azure Migrate.** O painel **Azure Migrate** tem uma notificação e um link para aceder a antigos projetos da Azure Migrate.

## <a name="next-steps"></a>Passos seguintes

- Experimente os nossos tutoriais para avaliar [VMs VMs,](tutorial-prepare-vmware.md) [VMs Hiper-V](tutorial-prepare-hyper-v.md)ou [servidores físicos.](tutorial-prepare-physical.md)
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
