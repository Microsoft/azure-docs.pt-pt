---
title: Matriz de suporte do Azure Migrate
description: Fornece um resumo das definições de suporte e limitações para o serviço Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811560"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte do Azure Migrate

Pode utilizar o [serviço Azure Migrate](migrate-overview.md) para avaliar e migrar as máquinas para a cloud do Microsoft Azure. Este artigo resume as definições de suporte gerais e limitações para cenários do Azure Migrate e implementações.


## <a name="azure-migrate-versions"></a>Versões do Azure Migrate

Existem duas versões do serviço Azure Migrate:

- **Versão atual**: Com esta versão, pode criar novos projetos do Azure Migrate, descubra locais avalia e orquestrar avaliações e migrações. [Saiba mais](whats-new.md#azure-migrate-new-version).
- **Versão anterior**: Para o cliente utilizando a versão anterior do Azure Migrate (tinha suporte apenas a avaliação de VMs de VMware no local), agora deve usar a versão atual. Na versão anterior, não é possível criar novos projetos do Azure Migrate ou executar deteções de novo.

## <a name="supported-migration-scenarios"></a>Cenários de migração suportados

A tabela resume cenários de migração suportados.

**Implementação** | **Detalhes*** 
--- | --- 
**Avaliação no local** | Avalie cargas de trabalho no local e os dados em execução em VMs do VMware e VMs de Hyper-V. Avalie a avaliação do servidor de migrar do Azure e o Microsoft dados Assistente de migração (DMA), bem como ferramentas de terceiros que incluem o Cloudamize, Corent Tech e o Turbonomic Server a utilizar.
**Migração no local para o Azure** | Migre cargas de trabalho e dados em execução em servidores físicos, VMs de VMware, VMs de Hyper-V e em instâncias do AWS/GCP, para o Azure. Migrar com a avaliação do servidor de migrar do Azure e Azure Database Migration Service (DMS) e, assim como com as ferramentas de terceiros que incluem Carbonite e CorentTech.

Suporte de ferramenta específica é resumido da seguinte forma.

**Ferramenta** | **Avaliação/migração** | **Detalhes**
--- | --- | ---
Azure Migrate avaliação de servidor | Avaliação | Experimente a avaliação de servidor para [Hyper-V](tutorial-prepare-hyper-v.md) e [VMware](tutorial-prepare-vmware.md).
Cloudamize | Avaliação | [Saiba mais](https://www.cloudamize.com/platform#tab-0).
CorentTech | Avaliação | [Saiba mais](https://www.corenttech.com/).
Turbonomic | Avaliação | [Saiba mais](https://turbonomic.com/solutions/technologies/azure-cloud/).
Azure Migrate a migração do servidor | Migração | Experimente a migração do servidor para [Hyper-V](tutorial-migrate-hyper-v.md) e [VMware](tutorial-migrate-vmware.md).
Carbonite | Migração | [Saiba mais](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migração | [Saiba mais](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Projetos de migração do Azure

**Suporte** | **Detalhes**
--- | ---
Subscription | Pode ter um único projeto do Azure Migrate numa subscrição.
Permissões do Azure | Precisa de permissões de proprietário ou contribuinte na subscrição para criar um projeto do Azure Migrate.
VMs VMware  | Avalie até 35 000 VMs de VMware num único projeto.
VMs Hyper-V | Avalie até 10 000 VMs de Hyper-V num único projeto.

Um projeto pode incluir as VMs de VMware e VMs de Hyper-V, até aos limites de avaliação.


## <a name="vmware-assessment-and-migration"></a>Avaliação de VMware e migração

[Revisão](migrate-support-matrix-vmware.md) a avaliação do servidor de migrar do Azure e a migração do servidor de matriz de suporte para VMs de VMware.

## <a name="hyper-v-assessment-and-migration"></a>Avaliação do Hyper-V e migração

[Revisão](migrate-support-matrix-hyper-v.md) a avaliação do servidor de migrar do Azure e a migração do servidor de matriz de suporte para VMs de Hyper-V.


## <a name="next-steps"></a>Passos Seguintes

- [Avaliar VMware VMs](tutorial-assess-vmware.md) para migração.
- [Avaliar as VMs de Hyper-V](tutorial-assess-hyper-v.md) para migração.

