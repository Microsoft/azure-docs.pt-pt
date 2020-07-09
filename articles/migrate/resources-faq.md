---
title: Azure Migrar FAQ
description: Obtenha respostas a perguntas comuns sobre o serviço Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81530322"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: Questões comuns

Este artigo responde a perguntas comuns sobre Azure Migrate. Se tiver dúvidas depois de ler este artigo, pode publicá-las no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum). Também pode rever estes artigos:

- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>O que é o Azure Migrate?

A Azure Migrate fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações no local e cargas de trabalho e VMs de nuvem privada e pública para Azure. O hub fornece ferramentas Azure Migrate para avaliação e migração e ofertas isv de terceiros. [Saiba mais](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>O que posso fazer com Azure Migrate?

Use a Azure Migrate para descobrir, avaliar e migrar para o local infraestruturas, aplicações e dados para Azure. A Azure Migrate suporta a avaliação e migração de VMware VMs, VMs hiper-V, servidores físicos, outros VMs virtualizados, bases de dados, aplicações web e desktops virtuais. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Qual é a diferença entre Azure Migrate e Azure Site Recovery?

[Azure Migrate](migrate-services-overview.md) fornece um centro centralizado para avaliação e migração para Azure. 

[A recuperação do local de Azure](../site-recovery/site-recovery-overview.md) é uma solução de recuperação de desastres. 

A ferramenta Azure Migrate: Server Migration usa alguma funcionalidade de recuperação do site back-end para a migração de elevadores e turnos de algumas máquinas no local.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre Azure Migrate: Avaliação do servidor e o kit de ferramentas MAP?

A Avaliação do Servidor fornece avaliação para ajudar na prontidão da migração e avaliação das cargas de trabalho para a migração para Azure. O [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) ajuda com outras tarefas, incluindo o planeamento de migração para versões mais recentes de sistemas operativos de clientes e servidores do Windows e rastreio de utilização de software. Para estes cenários, continue a utilizar o MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual é a diferença entre a Avaliação do Servidor e o Plano de Implementação da Recuperação do Site?

A Avaliação do Servidor é uma ferramenta de planeamento de migração. O Planejador de Implementação de Recuperação de Locais é uma ferramenta de planeamento de recuperação de desastres.

Escolha a sua ferramenta com base no que pretende fazer:

- **Planear a migração no local para Azure**: Se planeia migrar os seus servidores no local para Azure, utilize a Avaliação do Servidor para planeamento de migração. A Avaliação do Servidor avalia as cargas de trabalho no local e fornece orientação e ferramentas para o ajudar a migrar. Após o plano de migração estar em vigor, pode utilizar ferramentas como Azure Migrate: Server Migration para migrar as máquinas para Azure.
- **Planear a recuperação de desastres para a Azure**: Se planeia configurar a recuperação de desastres do local para Azure com a Recuperação do Local, utilize o Planejador de Implementação de Recuperação do Local. O Planejador de Implementação fornece uma avaliação profunda e específica do seu ambiente no local para efeitos de recuperação de desastres. Fornece recomendações relacionadas com a recuperação de desastres, tais como a replicação e o fracasso.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Como funciona a migração do servidor com a recuperação do site?

- Se utilizar a Azure Migrate: Migração de servidores para realizar uma migração *sem agentes* de VMware VMware no local, a migração é nativa de Azure Migrate e a Recuperação do Local não é utilizada.
- Se utilizar o Azure Migrate: Migração do Servidor para realizar uma migração *baseada em agentes* de VMware VMs, ou se migrar VMs hiper-V ou servidores físicos, Azure Migrate: A Migração do Servidor utiliza o motor de replicação do local de Azure.

## <a name="which-geographies-are-supported"></a>Que geografias são apoiadas?

Reveja as regiões suportadas em [clouds públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e do [Azure Government](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-do-i-get-started"></a>Como posso começar?

Identifique a ferramenta de que necessita e adicione a ferramenta a um projeto Azure Migrate. 

Para adicionar uma ferramenta ISV ou Movere:

1. Começa por obter uma licença, ou inscreva-te para um teste gratuito, de acordo com a política da ferramenta. O licenciamento de ferramentas está de acordo com o modelo de licenciamento ISV ou ferramenta.
2. Em cada ferramenta, há uma opção para ligar ao Azure Migrate. Siga as instruções e documentação da ferramenta para ligar a ferramenta ao Azure Migrate.

Você pode acompanhar sua viagem de migração de dentro do projeto Azure Migrate, através de Azure, e em outras ferramentas.

## <a name="how-do-i-delete-a-project"></a>Como apago um projeto?

Saiba como [apagar um projeto.](how-to-delete-project.md) 

## <a name="next-steps"></a>Passos seguintes

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).
