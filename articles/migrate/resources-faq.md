---
title: FaQ migração azure
description: Obtenha respostas a perguntas comuns sobre o serviço Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: c85c5c6e11beb0178139dad152f56f420b2ac26f
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926719"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: Questões comuns

Este artigo responde a perguntas comuns sobre o Azure Migrate. Se tiver dúvidas depois de ler este artigo, pode publicá-las no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum). Também pode rever estes artigos:

- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização da dependência](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>O que é o Azure Migrate?

A Azure Migrate fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local e VMs de nuvem privada e pública para Azure. O centro fornece ferramentas azure migrate para avaliação e migração e ofertas ISV de terceiros. [Saiba mais](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>O que posso fazer com o Azure Migrate?

Utilize o Azure Migrate para descobrir, avaliar e migrar no local infraestruturas, aplicações e dados para o Azure. A Azure Migrate suporta a avaliação e migração de VMware no local, VMs Hiper-V, servidores físicos, outros VMs virtualizados, bases de dados, aplicações web e ambientes de trabalho virtuais. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Qual é a diferença entre azure migrate e azure site recovery?

[A Azure Migrate](migrate-services-overview.md) fornece um centro centralizado para avaliação e migração para Azure. 

[A Recuperação do Sítio Azure](../site-recovery/site-recovery-overview.md) é uma solução de recuperação de desastres. 

A ferramenta Azure Migrate: Server Migration utiliza alguma funcionalidade de recuperação de sites para migração de elevadores e mudanças de algumas máquinas no local.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre azure migrate: Avaliação do servidor e o map Toolkit?

A Avaliação do Servidor fornece avaliação para ajudar na prontidão da migração e avaliação das cargas de trabalho para migração para Azure. O [Kit de Ferramentas de Avaliação e Planeamento (MAP)](https://www.microsoft.com/download/details.aspx?id=7826) da Microsoft ajuda com outras tarefas, incluindo o planeamento migratório para versões mais recentes de sistemas operativos de clientes windows e servidores, e rastreio de utilização de software. Para estes cenários, continue a utilizar o MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual é a diferença entre a avaliação do servidor e o planejador de implementação de recuperação do site?

A Avaliação do Servidor é uma ferramenta de planeamento de migração. O Planejador de Implementação de Recuperação do Site é uma ferramenta de planeamento de recuperação de desastres.

Escolha a sua ferramenta com base no que pretende fazer:

- **Planeie a migração no local para o Azure**: Se planeia migrar os seus servidores no local para o Azure, utilize a Avaliação do Servidor para o planeamento da migração. A Avaliação do Servidor avalia as cargas de trabalho no local e fornece orientação e ferramentas para ajudá-lo a migrar. Depois de o plano de migração estar em vigor, pode utilizar ferramentas como o Azure Migrate: Server Migration para migrar as máquinas para Azure.
- **Planeie a recuperação de desastres para o Azure**: Se planeia configurar a recuperação de desastres das instalações para Azure com a Recuperação do Local, utilize o Planificador de Implantação de Recuperação do Local. O Planificador de Implantação fornece uma avaliação profunda e específica da recuperação do local do seu ambiente no local para efeitos de recuperação de desastres. Fornece recomendações relacionadas com a recuperação de desastres, tais como a replicação e a falha.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Como funciona a Migração do Servidor com a Recuperação do Site?

- Se utilizar o Azure Migrate: Server Migration para realizar uma migração *sem agentes* de VMware VMs no local, a migração é nativa do Azure Migrate e a Recuperação do Local não é usada.
- Se utilizar o Azure Migrate: Server Migration para realizar uma migração *baseada em agentes* de VMware VMs, ou se migrar VMs Hiper-V ou servidores físicos, o Azure Migrate: Server Migration utiliza o motor de replicação de recuperação do site Azure.

## <a name="which-geographies-are-supported"></a>Que geografias são apoiadas?

- **VMware VMs**: Reveja as [geografias apoiadas](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) pelo Azure Migrate para VMware VMs.
- **Hiper-V VMs**: Reveja as [geografias apoiadas](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v) pelo Azure Migrate para VMs Hiper-V.

## <a name="how-do-i-get-started"></a>Como posso começar?

Identifique a ferramenta de que necessita e, em seguida, adicione a ferramenta a um projeto Azure Migrate. 

Para adicionar uma ferramenta ISV ou Movere:

1. Inicie-se com a obtenção de uma licença, ou inscreva-se para um teste gratuito, de acordo com a política de ferramentas. O licenciamento de ferramentas está de acordo com o isv ou o modelo de licenciamento de ferramentas.
2. Em cada ferramenta, há uma opção para ligar ao Azure Migrate. Siga as instruções e documentação da ferramenta para ligar a ferramenta ao Azure Migrate.

Você pode acompanhar sua viagem de migração de dentro do projeto Azure Migrate, através de Azure, e em outras ferramentas.

## <a name="how-do-i-delete-a-project"></a>Como apago um projeto?

Aprenda a [apagar um projeto.](how-to-delete-project.md) 

## <a name="next-steps"></a>Passos seguintes

Leia a visão geral do [Azure Migrate.](migrate-services-overview.md)
