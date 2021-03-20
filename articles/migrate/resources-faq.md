---
title: Azure Migrar FAQ
description: Obtenha respostas a perguntas comuns sobre o serviço Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: a5db000ad76f23be60d279923a590204c2fcf5e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377385"
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

- A utilização do Azure Migrate proporciona interoperabilidade e extensibilidade futura com ferramentas Azure Migrate, outros serviços Azure e ferramentas de terceiros.
- A ferramenta Azure Migrate: Server Migration é construída de propósito para a migração de servidores para Azure. Está otimizado para a migração. Não é preciso aprender sobre conceitos e cenários que não são diretamente relevantes para a migração. 
- Não existem taxas de utilização da ferramenta para a migração durante 180 dias, a partir do momento em que a replicação é iniciada para um VM. Dá-lhe tempo para completar a migração. Você só paga pelos recursos de armazenamento e rede usados na replicação, e pelos encargos computacional consumidos durante as migrações de teste.
- A Azure Migrate apoia todos os cenários de migração apoiados pela Recuperação do Site. Além disso, para VMware VMs, Azure Migrate fornece uma opção de migração sem agente.
- Estamos a dar prioridade a novas funcionalidades de migração para o Azure Migrate: Ferramenta de migração de servidores apenas. Estas funcionalidades não são direcionadas para a Recuperação do Local.

[A recuperação do local de Azure](../site-recovery/site-recovery-overview.md) deve ser utilizada apenas para recuperação de desastres.

A ferramenta Azure Migrate: Server Migration usa alguma funcionalidade de recuperação do site back-end para a migração de elevadores e turnos de algumas máquinas no local.

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Tenho um projeto com a experiência clássica anterior de Azure Migrate. Como começo a usar a nova versão?

O clássico Azure Migrate vai reformar-se em fevereiro de 2024. Depois de fevereiro de 2024, a versão clássica do Azure Migrate deixará de ser suportada e os metadados de inventário no projeto clássico serão eliminados. Não é possível atualizar projetos ou componentes na versão anterior para a nova versão. Você precisa [criar um novo projeto Azure Migrate](create-manage-projects.md), e [adicionar-lhe ferramentas de avaliação e migração.](./create-manage-projects.md) Use os tutoriais para entender como usar as ferramentas de avaliação e migração disponíveis. Se tiver um espaço de trabalho log Analytics ligado a um projeto clássico, pode anexá-lo a um projeto de versão atual depois de eliminar o projeto clássico.

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