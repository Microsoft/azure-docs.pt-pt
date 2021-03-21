---
title: Acerca do Azure Migrate
description: Conheça o serviço Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 858f006de7425a9fa8bea25e356a148b877aa30d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040605"
---
# <a name="about-azure-migrate"></a>Acerca do Azure Migrate

Este artigo fornece uma visão geral rápida do serviço Azure Migrate.

A Azure Migrate fornece um hub centralizado para avaliar e migrar para Azure nos servidores, infraestruturas, aplicações e dados. Fornece o seguinte:

- **Plataforma de migração unificada**: um único portal para iniciar, correr e acompanhar a sua migração para Azure.
- **Gama de ferramentas**: Uma gama de ferramentas para avaliação e migração. As ferramentas Azure Migrate incluem Azure Migrate: Server Assessment e Azure Migrate: Server Migration. A Azure Migrate também se integra com outros serviços e ferramentas Azure, e com ofertas independentes de fornecedores de software (ISV).
- **Avaliação e migração**: No centro Azure Migrate, pode avaliar e migrar:
    - **Servidores**: Avalie os servidores no local e emigre-os para máquinas virtuais Azure ou Solução VMware Azure (AVS) (Preview).
    - **Bases de dados**: Avaliar bases de dados no local e migrar para a Base de Dados Azure SQL ou para a SQL Managed Instance.
    - **Aplicações web**: Avaliar aplicações web no local e migrar para o Azure App Service utilizando o Assistente de Migração do Serviço de Aplicações Azure.
    - **Ambientes de trabalho virtuais**: Avalie a infraestrutura virtual de ambiente de trabalho (VDI) no local e emigre-a para o Windows Virtual Desktop em Azure.
    - **Dados**: Migrar grandes quantidades de dados para a Azure de forma rápida e económica utilizando os produtos Azure Data Box.

## <a name="integrated-tools"></a>Ferramentas integradas

O hub Azure Migrate inclui estas ferramentas:

**Ferramenta** | **Avaliar e migrar** | **Detalhes**
--- | --- | ---
**Azure Migrate: Avaliação do servidor** | Avaliar servidores. | Descubra e avalie no local VMware VMs, VMs hiper-V e servidores físicos em preparação para a migração para Azure.
**Azure Migrate: Migração de servidores** | Migrar servidores. | Migrar VMware VMs, VMs Hiper-V, servidores físicos, outras máquinas virtualizadas e VMs de nuvem pública para Azure.
**Assistente de Migração de Dados** | Avaliar bases de dados do SQL Server para migração para Azure SQL Database, Azure SQL Managed Instance ou Azure VMs que executam o SQL Server. | Data Migration Assistant ajuda a identificar potenciais problemas que bloqueiam a migração. Identifica funcionalidades não apoiadas, novas funcionalidades que podem beneficiar-te após a migração e o caminho certo para a migração de bases de dados. [Saiba mais](/sql/dma/dma-overview).
**Azure Database Migration Service** | Migrar bases de dados no local para VMs Azure que executam o SQL Server, Azure SQL Database ou SQL Managed Instances. | [Saiba mais](../dms/dms-overview.md) sobre o Serviço de Migração de Bases de Dados.
**Movere** | Avaliar servidores. | [Saiba mais](#movere) sobre o Movere.
**Assistente de migração de aplicativos web** | Avalie as aplicações web no local e emigre-as para Azure. |  Utilize o Assistente de Migração do Serviço de Aplicações Azure para avaliar os sites no local para migração para o Azure App Service.<br/><br/> Utilize o Assistente de Migração para migrar aplicações web .NET e PHP para Azure. [Saiba mais](https://appmigration.microsoft.com/) sobre o Assistente de Migração do Serviço de Aplicações Azure.
**Azure Data Box** | Migrar dados offline. | Utilize produtos Azure Data Box para mover grandes quantidades de dados offline para o Azure. [Saiba mais](../databox/index.yml).

> [!NOTE]
> Se estiver no Governo Azure, ferramentas integradas externas e ofertas isv não podem enviar dados para projetos da Azure Migrate. Pode utilizar as ferramentas de forma independente.

## <a name="isv-integration"></a>Integração ISV

A Azure Migrate integra-se com várias ofertas ISV. 

**ISV**    | **Funcionalidade**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrar servidores.
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar servidores.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar servidores.
[Device42](https://docs.device42.com/) | Avaliar servidores.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar vDI.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrar servidores.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar servidores.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar servidores e bases de dados.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Ferramenta de avaliação do servidor

A ferramenta Azure Migrate: Ferramenta de avaliação do servidor descobre e avalia no local VMware VMs, VMs hiper-V e servidores físicos para migração para Azure. 

Aqui está o que a ferramenta faz:

- **Prontidão azul**: Avaliar se as máquinas no local estão prontas para a migração para Azure.
- **Dimensionamento azul**: Estima o tamanho dos VMs Azure ou o número de nós Azure VMware após a migração.
- **Estimativa de custos Azure**: Estimativas de custos para a execução de servidores no local em Azure.
- **Análise de dependência**: Identifica as dependências dos servidores cruzados e estratégias de otimização para mover servidores interdependentes para o Azure. Saiba mais sobre a Avaliação do Servidor com [a análise da dependência.](concepts-dependency-visualization.md)

A Avaliação do Servidor utiliza um [aparelho Azure Migrate](migrate-appliance.md) leve que implementa no local.

- O aparelho funciona num VM ou servidor físico. Pode instalá-lo facilmente usando um modelo descarregado.
- O aparelho descobre máquinas no local. Também envia continuamente metadados de máquina e dados de desempenho para a Azure Migrate.
- A descoberta do aparelho é sem agente. Nada está instalado em máquinas descobertas.
- Após a descoberta do aparelho, pode recolher máquinas descobertas em grupos e fazer avaliações para cada grupo.

> [!Note]
> A descoberta e avaliação de instâncias e bases de dados do SQL Server em execução no seu ambiente VMware está agora em pré-visualização. Para experimentar esta funcionalidade, utilize [**este link**](https://go.microsoft.com/fwlink/?linkid=2155668) para criar um projeto na região **Leste da Austrália**. Se já tiver um projeto no Leste da Austrália e quiser experimentar esta funcionalidade, verifique se concluiu estes [**pré-requisitos**](how-to-discover-sql-existing-project.md) no portal.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Ferramenta de migração de servidores

A ferramenta Azure Migrate: Servidor Migração ajuda-o a migrar para Azure:

**Migrate** | **Detalhes**
--- | ---
VMs VMware no local | Migrar VMs para Azure utilizando migração sem agentes ou por agentes.<br/><br/> Para a migração sem agentes, a Migração do Servidor utiliza o mesmo aparelho Azure Migrate que também pode ser usado pela Avaliação do Servidor para a descoberta e avaliação de VMware VMs.<br/><br/> Para a migração baseada em agentes, a Migração do Servidor utiliza um aparelho de replicação.
VMs de Hyper-V no local | Migrar VMs para Azure.<br/><br/> A Migração do Servidor utiliza agentes de fornecedor instalados no hospedeiro Hyper-V para a migração.
Servidores físicos no local | Podes migrar máquinas físicas para OZure. Você também pode migrar outras máquinas virtualizadas, e VMs de outras nuvens públicas, tratando-as como servidores físicos para fins de migração. | A migração do servidor utiliza um aparelho de replicação para a migração.


## <a name="selecting-assessment-and-migration-tools"></a>Selecionando ferramentas de avaliação e migração

No hub Azure Migrate, seleciona a ferramenta que pretende utilizar para avaliação ou migração e adiciona-a a um projeto Azure Migrate. Se adicionar uma ferramenta ISV ou Movere:

- Para começar, obtenha uma licença ou inscreva-se para um teste gratuito seguindo as instruções da ferramenta. Cada ISV ou ferramenta especifica o licenciamento da ferramenta.
- Cada ferramenta tem a opção de ligar ao Azure Migrate. Siga as instruções da ferramenta para ligar.
- Acompanhe a sua migração em todas as ferramentas do projeto Azure Migrate.

## <a name="movere"></a>Movere

O Movere é uma plataforma de serviço (SaaS). Aumenta a inteligência empresarial apresentando com precisão ambientes de TI inteiros num único dia. As organizações e as empresas crescem, mudam e otimizam digitalmente. Ao fazê-lo, a Movere proporciona-lhes a confiança necessária para ver e controlar os seus ambientes, seja qual for a plataforma, aplicação ou geografia.

A Microsoft [adquiriu a](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) Movere, e já não é vendida como uma oferta autónoma. O Movere está disponível através da Microsoft Solution Assessment e do Microsoft Cloud Economics Program. [Saiba mais](https://www.movere.io) sobre o Movere.

Encorajamo-lo a também olhar para a Azure Migrate, o nosso serviço de migração integrado. Azure Migrate fornece um centro central para simplificar a sua migração em nuvem. O hub suporta amplamente cargas de trabalho como servidores físicos e virtuais, bases de dados e aplicações. A visibilidade de ponta a ponta permite-lhe acompanhar facilmente o progresso ao longo da descoberta, avaliação e migração.

Com ferramentas ISV Azure e partner incorporadas, a Azure Migrate tem uma vasta gama de funcionalidades, incluindo:

- Descoberta de servidores virtuais e físicos.
- Direitos baseados no desempenho.
- Planeamento de custos.
- Avaliações baseadas em importações.
- Análise de dependência de aplicações sem agente.

Se procura ajuda especializada para começar, a Microsoft tem fornecedores de [serviços geridos especializados](https://azure.microsoft.com/partners) para guiá-lo. Consulte o [site da Azure Migrate.](https://azure.microsoft.com/services/azure-migrate/) 

## <a name="azure-migrate-versions"></a>Versões Azure Migrate

Existem duas versões do serviço Azure Migrate.

- **Versão atual**: Utilize esta versão para criar projetos Azure Migrate, descubra máquinas no local e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: A versão anterior de Azure Migrate, também conhecida como clássico Azure Migrate, suporta apenas a avaliação dos VMware VMware no local. O clássico Azure Migrate vai reformar-se em fevereiro de 2024. Depois de fevereiro de 2024, a versão clássica do Azure Migrate deixará de ser suportada e os metadados de inventário em projetos clássicos serão eliminados. Não é possível atualizar projetos ou componentes na versão anterior para a nova versão. Você precisa [criar um novo projeto Azure Migrate](create-manage-projects.md), e [adicionar-lhe ferramentas de avaliação e migração.](./create-manage-projects.md) Use os tutoriais para entender como usar as ferramentas de avaliação e migração disponíveis. Se tiver um espaço de trabalho log Analytics ligado a um projeto clássico, pode anexá-lo a um projeto de versão atual depois de eliminar o projeto clássico.

    Para aceder aos projetos existentes no portal Azure, procure e selecione **Azure Migrate.** O dashboard **Azure Migrate** tem uma notificação e uma ligação para aceder a projetos antigos da Azure Migrate.

## <a name="next-steps"></a>Passos seguintes

- Experimente os nossos tutoriais para avaliar [VMware VMs,](./tutorial-discover-vmware.md) [Hiper-VMs,](./tutorial-discover-hyper-v.md)ou [servidores físicos.](./tutorial-discover-physical.md)
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.