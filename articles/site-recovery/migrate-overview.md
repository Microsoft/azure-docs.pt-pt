---
title: Compare Azure Migrate e Recuperação de Sítio para migração para Azure
description: Resume as vantagens de usar a Azure Migrate para migração, em vez de Recuperação do Local.
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: c4d30b8b21bf3a0ea27467d2120b04a107f5a73f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581411"
---
# <a name="migrating-to-azure"></a>Migrar para o Azure

Para a migração, recomendamos que utilize o serviço Azure Migrate para migrar VMs e servidores para Azure, em vez do serviço de Recuperação do Local Azure. [Saiba mais](../migrate/migrate-services-overview.md) sobre Azure Migrate.


## <a name="why-use-azure-migrate"></a>Porquê utilizar o Azure Migrate?

A utilização do Azure Migrate para a migração proporciona uma série de vantagens:
 
 
- Azure Migrate fornece um centro centralizado para descoberta, avaliação e migração para Azure.
- A utilização do Azure Migrate proporciona interoperabilidade e extensibilidade futura com ferramentas Azure Migrate, outros serviços Azure e ferramentas de terceiros.
- A ferramenta Azure Migrate:Server Migration é construída de propósito para a migração do servidor para Azure. Está otimizado para a migração. Não é preciso aprender sobre conceitos e cenários que não são diretamente relevantes para a migração. 
- Não existem taxas de utilização da ferramenta para a migração durante 180 dias, a partir do momento em que a replicação é iniciada para um VM. Isto dá-lhe tempo para completar a migração. Você só paga pelos recursos de armazenamento e rede usados na replicação, e pelos encargos computacional consumidos durante as migrações de teste.
- A Azure Migrate apoia todos os cenários de migração apoiados pela Recuperação do Site. Além disso, para VMware VMs, Azure Migrate fornece uma opção de migração sem agente.
- Estamos a dar prioridade a novas funcionalidades de migração apenas para a ferramenta migração do Azure Migrate:Server Migration. Estas funcionalidades não são direcionadas para a Recuperação do Local.

## <a name="when-to-use-site-recovery"></a>Quando utilizar a Recuperação do Local?

A recuperação do local deve ser utilizada:

- Para a recuperação de desastres de máquinas no local para Azure.
- Para a recuperação de desastres de Azure VMs, entre regiões de Azure.

Embora recomendemos a utilização do Azure Migrate para migrar servidores no local para Azure, se já iniciou a sua jornada de migração com a Recuperação do Site, pode continuar a usá-lo para completar a sua migração.  

## <a name="next-steps"></a>Passos seguintes

> [Reveja questões comuns](../migrate/resources-faq.md) sobre Azure Migrate.
