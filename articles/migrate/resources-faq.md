---
title: Perguntas comuns sobre as migrações para Azure
description: Obtenha respostas para perguntas comuns sobre o serviço migrações para Azure.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: baf01c0a0d5c6154305f7137c24deb0365b5e812
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062103"
---
# <a name="azure-migrate-common-questions"></a>Migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre as migrações para Azure. Se tiver mais dúvidas depois de ler este artigo, publique-as no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum). Se você tiver outras dúvidas, leia estes artigos:

- [Perguntas](common-questions-appliance.md) sobre o aparelho Azure Migrate.
- [Perguntas](common-questions-discovery-assessment.md) sobre descoberta, avaliação e visualização da dependência.


## <a name="what-is-azure-migrate"></a>O que é o Azure Migrate?

As migrações para Azure fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e VMs de nuvem privada/pública, para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros. [Saiba mais](migrate-services-overview.md).


## <a name="what-can-i-do-with-azure-migrate"></a>O que posso fazer com as migrações para Azure?

Use as migrações para Azure para descobrir, avaliar e migrar dados, aplicativos e infraestrutura locais para o Azure. As migrações para Azure dão suporte à avaliação e migração de VMs VMware locais, VMs do Hyper-V, servidores físicos, outras VMs virtualizadas, bancos de dados, aplicativos Web e áreas de trabalho virtuais. 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Qual é a diferença entre migrações e Site Recovery do Azure?

As migrações para Azure fornecem um hub centralizado para avaliação e migração para o Azure. [A Recuperação do Sítio Azure](../site-recovery/site-recovery-overview.md) é uma solução de recuperação de desastres. A ferramenta migrações para Azure: Server Migration usa algumas funcionalidades de Site Recovery de back-end para a migração de comparação entre alguns computadores locais.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre a avaliação do servidor de migrações para Azure e o MAP Toolkit?

A avaliação do servidor fornece avaliação para ajudar com a preparação da migração e a avaliação de cargas de trabalho para migração para o Azure. O [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) ajuda com outras tarefas, incluindo o planeamento migratório para versões mais recentes dos sistemas operativos cliente/servidor windows e o rastreio de utilização do software. Para esses cenários, continue a usar o MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual é a diferença entre a avaliação do servidor e o Site Recovery Planejador de Implantações?

A avaliação do servidor é uma ferramenta de planejamento de migração. O Site Recovery Planejador de Implantações é uma ferramenta de planejamento de recuperação de desastre.

- **Planeie a migração no local para o Azure**: Se planeia migrar os seus servidores no local para o Azure, utilize a Avaliação do Servidor para o planeamento da migração. Ele avalia as cargas de trabalho locais e fornece orientações e ferramentas para ajudá-lo a migrar. Depois que o plano de migração estiver em vigor, você poderá usar ferramentas, incluindo migração de servidor de migrações para Azure, para migrar os computadores para o Azure.
- **Planeie a recuperação de desastres para o Azure**: Se planeia configurar a recuperação de desastres das instalações para Azure com a Recuperação do Local, utilize o Planificador de Implantação de Recuperação do Local. O Planejador de Implantações fornece uma avaliação profunda e específica Site Recovery do seu ambiente local para fins de recuperação de desastres. Ele fornece recomendações sobre a recuperação de desastre, como replicação e failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Como funciona a migração de servidor com o Site Recovery?

- Se você usar as migrações para Azure: migração de servidor para executar uma migração sem agente de VMs do VMware locais, a migração será nativa para migrações para Azure e Site Recovery não será usada.
- Se você usar migrações para Azure: migração de servidor para executar uma migração baseada em agente de VMs VMware ou migrar VMs Hyper-V ou servidores físicos, a migração de servidor de migrações para Azure usa o mecanismo de replicação Azure Site Recovery.


## <a name="which-geographies-are-supported"></a>Quais regiões têm suporte?

Reveja as geografias apoiadas pelo Azure Migrate para [VMware VM](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) e para [VMs Hiper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v).

## <a name="how-do-i-get-started"></a>Como posso começar?

Você identifica a ferramenta de que precisa e a adiciona a um projeto de migrações para Azure. Se você estiver adicionando uma ferramenta ISV ou um movimentador:
- Comece obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com a política de ferramentas. O licenciamento para ferramentas está de acordo com o modelo de licenciamento de ISV ou ferramenta.
- Em cada ferramenta, há uma opção para se conectar a migrações para Azure. Siga as instruções e a documentação da ferramenta para conectar a ferramenta com as migrações para Azure.
Acompanhe centralmente sua jornada de migração de dentro do projeto de migrações para Azure, no Azure e em outras ferramentas.

## <a name="how-do-i-delete-a-project"></a>Como fazer excluir um projeto?

[Aprenda a](how-to-delete-project.md) apagar um projeto. 




## <a name="next-steps"></a>Passos seguintes
Leia a visão geral do [Azure Migrate.](migrate-services-overview.md)
