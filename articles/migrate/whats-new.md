---
title: O que há de novo no Azure Migrate | Documentos da Microsoft
description: Disponibiliza uma descrição geral do serviço do Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 06/10/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c3bc596076f3ec4f9d41f0da819ddd386fee63c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809996"
---
# <a name="whats-new-in-azure-migrate"></a>O que há de novo no Azure Migrate

[O Azure Migrate](migrate-services-overview.md) ajuda-o a detetar, avaliar e migrar os servidores, aplicações e dados para a cloud do Microsoft Azure. Este artigo resume os novos recursos do Azure Migrate.



## <a name="azure-migrate-new-version"></a>O Azure Migrate nova versão

Uma nova versão do Azure Migrate foi lançada em Julho de 2019. 

- **Versão (novo) atual**: Utilize esta versão para criar projetos do Azure Migrate, detetar máquinas no local e orquestrar as migrações e avaliações. 
- **Versão anterior**: Para o cliente utilizando a versão anterior do Azure Migrate (tinha suporte apenas a avaliação de VMs de VMware no local), agora deve usar a versão atual. Na versão anterior, já não pode criar novos projetos do Azure Migrate, ou executar deteções de novo. Pode continuar a aceder projetos existentes. Para fazê-lo no portal do Azure > todos os serviços, procurar Azure Migrate. Nas notificações do Azure Migrate, há um link para acessar projetos de Azure Migrate antigos.


## <a name="azure-migrate-features"></a>Funcionalidades do Azure Migrate

A nova versão do Azure Migrate fornece uma série de novos recursos:


- **Plataforma de migração unificada**: O Azure Migrate agora fornece um único portal para centralizar, gerenciar e controlar o seu percurso de migração para o Azure, com um fluxo de implantação aprimoradas e a experiência do portal.
- **Ferramentas de avaliação e migração**: O Azure Migrate fornece ferramentas nativas e integra-se com outros serviços do Azure, bem como com as ferramentas de (ISV fornecedor) independentes de software. [Saiba mais](migrate-services-overview.md#isv-integration) sobre a integração de ISV.
- **Avaliação do Azure Migrate**: Usando a ferramenta de avaliação do servidor de migrar do Azure, pode avaliar VMware VMs e VMs de Hyper-V para migração para o Azure. Também pode avaliar para migração com outros serviços do Azure e ferramentas de ISV.
- **Migração para o Azure Migrate**: Usando a ferramenta de migração do servidor de migrar do Azure, pode migrar VMs de VMware no local e VMs de Hyper-V para o Azure, bem como servidores físicos, outros servidores virtualizados e público/privado VMs da cloud. Além disso, pode migrar para o Azure com as ferramentas de ISV.
- **Aplicação do Azure Migrate**: O Azure Migrate implementa um dispositivo de simples para a deteção e avaliação no local VMs de VMware e VMs de Hyper-V.
    - Este dispositivo é utilizado pelo Azure Migrate servidor avaliação e migração do servidor de migrar do Azure para a migração sem agente.
    - A aplicação da Deteta continuamente dados de metadados e o desempenho de servidor, para fins de avaliação e migração.  
- **Migração de VM de VMware**:  Migração de servidor migrar do Azure fornece dois métodos para a migração no local VMs de VMware para o Azure.  Uma migração sem agente utilizando a aplicação do Azure Migrate e uma migração com base em agente, que utiliza uma aplicação de replicação e implementa um agente em cada VM que pretende migrar. [Saiba mais](server-migrate-overview.md)
 - **Avaliação e migração de base de dados**: Do Azure Migrate, pode avaliar bases de dados no local para migração para o Azure utilizando o Assistente de migração de base de dados do Azure. É possível migrar bases de dados com o serviço de migração de base de dados do Azure.
- **Migração de aplicativos da Web**: Pode avaliar aplicações web com um URL de ponto final público com o serviço de aplicações do Azure. Para a migração de aplicações de .NET internas, pode transferir e executar o Assistente de migração do App Service. 
- **Data Box**: Importar dados de grandes quantidades offline no Azure através do Azure Data Box, no Azure Migrate.


## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
- Experimente os nossos tutoriais para avaliar [VMs de VMware](tutorial-assess-vmware.md) e [VMs de Hyper-V](tutorial-assess-hyper-v.md).
