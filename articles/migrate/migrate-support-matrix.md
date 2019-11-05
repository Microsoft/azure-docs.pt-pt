---
title: Matriz de suporte para migrações para Azure
description: Fornece um resumo das configurações de suporte e limitações para o serviço migrações para Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: raynew
ms.openlocfilehash: 18032250bc5c321d638ad46204738f49f1a0c744
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480136"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte para migrações para Azure

Você pode usar o [serviço migrações para Azure](migrate-overview.md) para avaliar e migrar computadores para a nuvem Microsoft Azure. Este artigo resume as configurações de suporte geral e as limitações para cenários e implantações de migração do Azure.


## <a name="azure-migrate-versions"></a>Versões de migrações para Azure

Há duas versões do serviço migrações para Azure:

- **Versão atual**: usando esta versão, você pode criar novos projetos de migrações para Azure, descobrir avaliações locais e orquestrações e migrações. [Saiba mais](whats-new.md#release-version-july-2019).
- **Versão anterior**: para o cliente que usa a versão anterior da migração do Azure (somente a avaliação de VMs do VMware local foi suportada), agora você deve usar a versão atual. Na versão anterior, você não pode criar novos projetos de migrações para Azure ou executar novas descobertas.

## <a name="supported-assessmentmigration-scenarios"></a>Cenários de avaliação/migração com suporte

A tabela resume os cenários de descoberta, avaliação e migração com suporte.

**Implementação** | **Detalhes** 
--- | --- 
**Descoberta específica do aplicativo** | Você pode descobrir aplicativos, funções e recursos em execução em VMs VMware. Atualmente, esse recurso está limitado apenas à descoberta. A avaliação está atualmente no nível do computador. Ainda não oferecemos aplicativo, função ou avaliação específica de recurso. 
**Avaliação local** | Avalie as cargas de trabalho e os dados locais em execução em VMs VMware e VMs do Hyper-V. Avalie usando o DMA (avaliação de servidor e Assistente de Migração de Dados da Microsoft) de migrações do Azure, bem como ferramentas de terceiros que incluem Cloudamize, colocação Tech e O turbonomic Server.
**Migração local para o Azure** | Migre cargas de trabalho e dados em execução em servidores físicos, VMs VMware, VMs Hyper-V, servidores físicos e VMS baseadas em nuvem para o Azure. Migre usando a avaliação de servidor de migrações para Azure e o serviço de migração de banco de dados do Azure (DMS), bem como o uso de ferramentas de terceiros que incluem Carbonite e CorentTech.

O suporte a ferramentas específicas é resumido da seguinte maneira.

**Ferramenta** | **Avaliação/migração** | **Detalhes**
--- | --- | ---
Avaliação do servidor de migrações para Azure | Avaliação | Experimente a avaliação do servidor para [Hyper-V](tutorial-prepare-hyper-v.md) e [VMware](tutorial-prepare-vmware.md).
Cloudamize | Avaliação | [Saiba mais](https://www.cloudamize.com/platform#tab-0).
CorentTech | Avaliação | [Saiba mais](https://www.corenttech.com/).
Turbonomic | Avaliação | [Saiba mais](https://turbonomic.com/solutions/technologies/azure-cloud/).
Migração de servidor de migrações para Azure | Migração | Experimente a migração de servidor para o [Hyper-V](tutorial-migrate-hyper-v.md) e o [VMware](tutorial-migrate-vmware.md).
Carbonite | Migração | [Saiba mais](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migração | [Saiba mais](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Projetos de migrações para Azure

**Suporte** | **Detalhes**
--- | ---
Subscrição | Você pode ter vários projetos de migrações para Azure em uma assinatura.
Permissões do Azure | Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto de migrações para Azure.
VMs VMware  | Avalie até 35.000 VMs VMware em um único projeto.
VMs Hyper-V | Avalie até 35.000 VMs do Hyper-V em um único projeto.

Um projeto pode incluir VMs do VMware e VMs do Hyper-V, até os limites de avaliação.

## <a name="supported-geographies"></a>Geografia com suporte

Você pode criar um projeto de migrações para Azure em uma série de geografias. Embora você só possa criar projetos nessas regiões, você pode avaliar ou migrar computadores para outros locais de destino. A geografia do projeto é usada somente para armazenar os metadados descobertos.

**Geografia** | **Local de armazenamento de metadados**
--- | ---
Azure Government | Gov (US) - Virginia
Ásia-Pacífico | Ásia Oriental ou sudeste asiático
Austrália | Leste da Austrália ou sudeste da Austrália
Brasil | Sul do Brasil
Canadá | Canadá central ou leste do Canadá
Europa | Europa Setentrional ou Europa Ocidental
França | França Central
Índia | Índia central ou sul da Índia
Japão |  Leste do Japão ou oeste do Japão
Coreia | Coreia central ou sul da Coreia
Reino Unido | Sul do Reino Unido ou Oeste do Reino Unido
Estados Unidos | EUA Central ou oeste dos EUA 2


 > [!NOTE]
 > No momento, o suporte para Azure governamental está disponível apenas para a [versão mais antiga](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) das migrações para Azure.



## <a name="vmware-assessment-and-migration"></a>Avaliação e migração do VMware

[Reveja](migrate-support-matrix-vmware.md) a matriz de suporte de migração de servidor e avaliação do Azure Migrations para VMs VMware.

## <a name="hyper-v-assessment-and-migration"></a>Avaliação e migração do Hyper-V

[Revise](migrate-support-matrix-hyper-v.md) a matriz de suporte de migração de servidor e avaliação do Azure migrações para VMs do Hyper-V.


## <a name="next-steps"></a>Passos seguintes

- [Avalie as VMs do VMware](tutorial-assess-vmware.md) para migração.
- [Avalie as VMs do Hyper-V](tutorial-assess-hyper-v.md) para migração.

