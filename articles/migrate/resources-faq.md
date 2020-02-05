---
title: Perguntas comuns sobre as migrações para Azure
description: Obtenha respostas para perguntas comuns sobre o serviço migrações para Azure.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: a9d008406e59c66a8b742194e2e2d388c778ac9e
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989353"
---
# <a name="azure-migrate-common-questions"></a>Migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre as migrações para Azure. Se você tiver outras consultas depois de ler este artigo, poste-as no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum). Se você tiver outras dúvidas, leia estes artigos:

- [Perguntas](common-questions-appliance.md) sobre o dispositivo migrações para Azure.
- [Perguntas](common-questions-discovery-assessment.md) sobre descoberta, avaliação e visualização de dependência.


## <a name="what-is-azure-migrate"></a>O que é o Azure Migrate?

As migrações para Azure fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e VMs de nuvem privada/pública, para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros. [Saiba mais](migrate-services-overview.md).


## <a name="what-can-i-do-with-azure-migrate"></a>O que posso fazer com as migrações para Azure?

Use as migrações para Azure para descobrir, avaliar e migrar dados, aplicativos e infraestrutura locais para o Azure. As migrações para Azure dão suporte à avaliação e migração de VMs VMware locais, VMs do Hyper-V, servidores físicos, outras VMs virtualizadas, bancos de dados, aplicativos Web e áreas de trabalho virtuais. 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Qual é a diferença entre migrações e Site Recovery do Azure?

As migrações para Azure fornecem um hub centralizado para avaliação e migração para o Azure. [Azure site Recovery](../site-recovery/site-recovery-overview.md) é uma solução de recuperação de desastre. A ferramenta migrações para Azure: Server Migration usa algumas funcionalidades de Site Recovery de back-end para a migração de comparação entre alguns computadores locais.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre a avaliação do servidor de migrações para Azure e o MAP Toolkit?

A avaliação do servidor fornece avaliação para ajudar com a preparação da migração e a avaliação de cargas de trabalho para migração para o Azure. [O kit de ferramentas de avaliação e planejamento da Microsoft (MAP)](https://www.microsoft.com/download/details.aspx?id=7826) ajuda com outras tarefas, incluindo o planejamento de migração para versões mais recentes de sistemas operacionais de cliente/servidor do Windows e acompanhamento de uso de software. Para esses cenários, continue a usar o MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual é a diferença entre a avaliação do servidor e o Site Recovery Planejador de Implantações?

A avaliação do servidor é uma ferramenta de planejamento de migração. O Site Recovery Planejador de Implantações é uma ferramenta de planejamento de recuperação de desastre.

- **Planejar a migração local para o Azure**: se você planeja migrar seus servidores locais para o Azure, use a avaliação do servidor para planejamento de migração. Ele avalia as cargas de trabalho locais e fornece orientações e ferramentas para ajudá-lo a migrar. Depois que o plano de migração estiver em vigor, você poderá usar ferramentas, incluindo migração de servidor de migrações para Azure, para migrar os computadores para o Azure.
- **Planejar a recuperação de desastre para o Azure**: se você planeja configurar a recuperação de desastre do local para o azure com site Recovery, use o Planejador de Implantações de site Recovery. O Planejador de Implantações fornece uma avaliação profunda e específica Site Recovery do seu ambiente local para fins de recuperação de desastres. Ele fornece recomendações sobre a recuperação de desastre, como replicação e failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Como funciona a migração de servidor com o Site Recovery?

- Se você usar as migrações para Azure: migração de servidor para executar uma migração sem agente de VMs do VMware locais, a migração será nativa para migrações para Azure e Site Recovery não será usada.
- Se você usar migrações para Azure: migração de servidor para executar uma migração baseada em agente de VMs VMware ou migrar VMs Hyper-V ou servidores físicos, a migração de servidor de migrações para Azure usa o mecanismo de replicação Azure Site Recovery.


## <a name="which-geographies-are-supported"></a>Quais regiões têm suporte?

Examine as geografias com suporte para migrações do Azure para [VM VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) e para [VMs do Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v).

## <a name="how-do-i-get-started"></a>Como começo?

Você identifica a ferramenta de que precisa e a adiciona a um projeto de migrações para Azure. Se você estiver adicionando uma ferramenta ISV ou um movimentador:
- Comece obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com a política de ferramentas. O licenciamento para ferramentas está de acordo com o modelo de licenciamento de ISV ou ferramenta.
- Em cada ferramenta, há uma opção para se conectar a migrações para Azure. Siga as instruções e a documentação da ferramenta para conectar a ferramenta com as migrações para Azure.
Acompanhe centralmente sua jornada de migração de dentro do projeto de migrações para Azure, no Azure e em outras ferramentas.

## <a name="how-do-i-delete-a-project"></a>Como fazer excluir um projeto?

[Saiba como](how-to-delete-project.md) excluir um projeto. 


## <a name="do-machines-retain-domain-identity-after-migration"></a>As máquinas mantêm a identidade de domínio após a migração?

Assumindo que os serviços de domínio estão [integrados no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/), então a adesão ao domínio, e a identidade da máquina no domínio, é mantida após a migração. 

## <a name="next-steps"></a>Passos seguintes
Leia a [visão geral de migrações para Azure](migrate-services-overview.md).
