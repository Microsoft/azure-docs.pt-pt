---
title: Servidores migratórios e VMs para Azure com recuperação do site Azure
description: Descreve como migrar no local e VMs Azure IaaS para Azure usando o serviço de Recuperação do Local Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: aaf01dcd63c21e4741456f4f7fccaf22b8fbfffc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281315"
---
# <a name="about-migration"></a>Acerca da migração

Utilize o serviço Azure Migrate para migrar VMs e servidores para Azure, em vez do serviço de Recuperação do Local Azure. [Saiba mais](../migrate/migrate-services-overview.md) sobre Azure Migrate.


## <a name="why-use-azure-migrate"></a>Porquê utilizar o Azure Migrate?

A utilização do Azure Migrate para a migração proporciona uma série de vantagens:
 
 
- Azure Migrate fornece um centro centralizado para descoberta, avaliação e migração para Azure.
- A utilização do Azure Migrate proporciona interoperabilidade e extensibilidade futura com ferramentas Azure Migrate, outros serviços Azure e ferramentas de terceiros.
- A ferramenta Azure Migrate:Server Migration é construída de propósito para a migração do servidor para Azure. Está otimizado para a migração. Não é preciso aprender sobre conceitos e cenários que não são diretamente relevantes para a migração. 
- Não existem taxas de utilização da ferramenta para a migração durante 180 dias, a partir do momento em que a replicação é iniciada para um VM. Isto dá-lhe tempo para completar a migração. Você só paga pelos recursos de armazenamento e rede usados na replicação, e pelos encargos computacional consumidos durante as migrações de teste.
- Para VMware VMs, Azure Migrate fornece migração sem agentes, além da migração baseada em agentes.
- Estamos a dar prioridade a novas funcionalidades de migração apenas para a ferramenta migração do Azure Migrate:Server Migration.

## <a name="when-to-use-site-recovery"></a>Quando utilizar a Recuperação do Local?

A recuperação do local deve ser utilizada:

- Para a recuperação de desastres de máquinas no local para Azure.
- Para a recuperação de desastres de Azure VMs, entre regiões de Azure.

Embora recomendemos a utilização do Azure Migrate para migrar servidores no local para Azure, se já iniciou a sua jornada de migração com a Recuperação do Site, pode continuar a usá-lo para completar a sua migração.  

## <a name="next-steps"></a>Passos seguintes

> [Reveja questões comuns](../migrate/resources-faq.md) sobre Azure Migrate.
