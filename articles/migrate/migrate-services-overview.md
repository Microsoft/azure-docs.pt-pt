---
title: Acerca do Azure Migrate | Microsoft Docs
description: Disponibiliza uma descrição geral do serviço do Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 083eaaa5be35d515a477ce6286f226b267569e1e
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840352"
---
# <a name="about-azure-migrate"></a>Acerca do Azure Migrate

Este artigo fornece uma descrição geral rápida do Azure Migrate.

O Azure Migrate ajuda-o a migrar para o Azure. O Azure Migrate fornece um hub centralizado para controlar a deteção, avaliação e migração dos dados, aplicações e infraestrutura no local para o Azure. O hub fornece as ferramentas do Azure para avaliação e migração, bem como ofertas de (ISV fornecedor) de terceiros independentes de software. Fornece:

- **Plataforma de migração unificada**: Utilizar um único portal para iniciar, executar e controlar o seu percurso de migração para o Azure.
- **Gama de ferramentas**: O Azure Migrate fornece ferramentas nativas e integra-se com outros serviços do Azure, bem como com as ferramentas de ISV. Selecione as ferramentas corretas de avaliação e migração, com base nos requisitos organizacionais. 
- **Azure Migrate avaliação Server**: Utilizar a ferramenta de avaliação do servidor para avaliar a no local VMs de VMware e VMs de Hyper-V, para a migração para o Azure.
- **Azure Migrate a migração do servidor**: Utilize a ferramenta de migração do servidor para migrar na cloud no local de VMs de VMware, VMs de Hyper-V, VMs e servidores físicos para o Azure.
- **Avaliação da base de dados de migração do Azure**: Avalie as bases de dados no local para migração para o Azure.
- **Migração de base de dados de migração do Azure**: Migre bases de dados no local para o Azure.


## <a name="azure-migrate-versions"></a>Versões do Azure Migrate

Existem duas versões do serviço Azure Migrate:

- **Versão atual**: Utilize esta versão para criar projetos do Azure Migrate, detetar máquinas no local e orquestrar as migrações e avaliações. [Saiba mais](whats-new.md) sobre o que há de novo nesta versão.
- **Versão anterior**: Se estava a utilizar a versão anterior do Azure Migrate (tinha suporte apenas a avaliação de VMs de VMware no local), agora, deve utilizar a versão atual. Já não pode criar projetos do Azure Migrate a utilizar a versão anterior, ou executar deteções de novo. No entanto, pode continuar a aceder projetos existentes. Para tal, no portal do Azure > **todos os serviços**, procure **Azure Migrate**. No dashboard do Azure Migrate, existe uma notificação e um link para acessar projetos de Azure Migrate antigos.

## <a name="isv-integration"></a>Integração de ISV

Além de ferramentas nativas do Azure, o Azure Migrate integra um número de ofertas de ISV. Identifique a ferramenta necessárias e adicioná-lo a um projeto do Azure Migrate. Pode controlar centralmente seu percurso de migração a partir do projeto do Azure Migrate, em ferramentas do Azure e de ISV.

**ISV** | **Funcionalidade**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar
[Dispositivo 42](https://docs.device42.com/) | Avaliar
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrar

### <a name="selecting-an-isv-tool"></a>Selecionar uma ferramenta de ISV

Depois de adicionar uma ferramenta de ISV para projetos do Azure Migrate, comece com a ferramenta ao obter uma licença ou inscrever-se numa avaliação gratuita, de acordo com a política de ISV. Em cada ferramenta, há uma opção para ligar ao Azure Migrate. Siga as instruções de ferramenta e a documentação, para ligar a ferramenta com o Azure Migrate.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate avaliação de servidor

Avaliação de servidor migrar do Azure Deteta e avalia as VMs de VMware no local e VMs de Hyper-V, para a migração para o Azure. Ajuda-o a identificar o seguinte:

- **Preparação para o Azure:** Avalie se a máquinas no local estão prontas para a migração para o Azure.
- **Dimensionamento do Azure:** Estime o tamanho das VMs do Azure após a migração.
- **Estimativa de custos do Azure:** As estimativas de custos para executar servidores no local no Azure.
- **Visualização de dependência:** Identifica dependências entre servidores e a melhor forma de mover servidores dependentes para o Azure. 

Avaliação de servidor utiliza um dispositivo de simples que implemente no local e registe-se com a avaliação de servidor.

- A aplicação da Deteta máquinas no local, liga a avaliação do servidor e envia continuamente metadados e dados relacionados ao desempenho para o Azure Migrate.
- A deteção é sem agente. Nada tem de ser instalado em VMs detetadas.
- Depois das máquinas são detectadas, agrupa-los em grupos que normalmente consistem em VMs que pretende migrar em conjunto.
- Cria uma avaliação para um grupo. Em seguida, pode analisar a avaliação, para descobrir sua estratégia de migração.

## <a name="azure-migrate-server-migration"></a>Azure Migrate a migração do servidor

O Azure ajuda a migrar a migração do servidor a migração no local VMs de VMware, VMs Hyper-V, servidores físicos, outras máquinas virtualizadas e public cloud VMs, para o Azure. Pode migrar máquinas após avaliá-los ou sem uma avaliação. 

## <a name="azure-migrate-database-assessment"></a>Avaliação da base de dados de migração do Azure

O Azure Migrate integra-se com dados Assistente de migração (DMA) para avaliar a bases de dados do SQL Server no local para migração para a BD SQL do Azure, instância gerida do SQL do Azure ou VMs do Azure com o SQL Server. DMA fornece informações sobre potenciais problemas de bloqueios para migração. Ele identifica funcionalidades não suportadas, bem como novos recursos que podem se beneficiar após a migração e ajuda-o a identificar o caminho certo para a migração de base de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Migração de base de dados de migração do Azure

O Azure Migrate integra-se com o Azure da base de dados Migration Service (DMS), a migração de bases de dados no local para o Azure. Utilize o DMS para migrar bases de dados no local para VMs do Azure com o SQL, BD SQL do Azure e instâncias de geridas de SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Avaliação de aplicação Web e migração

Do hub do Azure Migrate, pode avaliar e migrar aplicações web no local para o Azure.

- **Avaliar aplicações web online**: Utilize o Assistente de migração de serviço do Azure aplicações para avaliar a sites no local para migração para o serviço de aplicações do Azure.
- **Migrar aplicações web**: Migre aplicações de web do .NET e o PHP para o Azure com o Assistente de migração de serviço do Azure aplicações.

[Saiba mais.](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Migração de dados offline

Pode utilizar a Data Box offline a família de produtos para mover grandes quantidades de dados para o Azure. [Saiba mais](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Passos Seguintes

- Experimente os nossos tutoriais para avaliar [VMs de VMware](tutorial-assess-vmware.md) e [VMs de Hyper-V](tutorial-assess-hyper-v.md).
- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
