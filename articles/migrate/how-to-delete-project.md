---
title: Eliminar um projeto do Azure Migrate
description: Descreve como criar um projeto Azure Migrate e adicionar uma ferramenta de avaliação/migração.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512733"
---
# <a name="delete-an-azure-migrate-project"></a>Eliminar um projeto do Azure Migrate

Este artigo descreve como apagar um projeto [Azure Migrate.](migrate-overview.md)


## <a name="before-you-start"></a>Antes de começar

Antes de apagar um projeto:

- Quando se apaga um projeto, o projeto e os metadados da máquina descobertos são eliminados.
- Se tiver anexado um espaço de trabalho de Log Analytics à ferramenta de avaliação do servidor para análise de dependência, decida se pretende eliminar o espaço de trabalho. 
    - O espaço de trabalho não é automaticamente apagado. Apague-o manualmente.
    - Verifique para que é utilizado um espaço de trabalho antes de o apagar. O mesmo espaço de trabalho log Analytics pode ser usado para vários cenários.
    - Antes de eliminar o projeto, pode encontrar uma ligação ao espaço de trabalho em **Azure Migrate - Servers** > **Azure Migrate - Servers Azure Assessment**, no espaço de trabalho **OMS**.
    - Para eliminar um espaço de trabalho após a eliminação de um projeto, encontre o espaço de trabalho no grupo de recursos relevante, e siga [estas instruções](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Apagar um projeto


1. No portal Azure, abra o grupo de recursos em que o projeto foi criado.
2. Na página do grupo de recursos, selecione **Tipos ocultos .**
3. Selecione o projeto e os recursos associados que pretende eliminar.
    - O tipo de recurso para projetos de migração Azure é **projetos Microsoft.Migrate/migrate**.
    - Na secção seguinte, reveja os recursos criados para a descoberta, avaliação e migração num projeto Azure Migrate.
    - Se o grupo de recursos apenas contiver o projeto Azure Migrate, pode eliminar todo o grupo de recursos.
    - Se pretender apagar um projeto da versão anterior do Azure Migrate, os passos são os mesmos. O tipo de recursos para estes projetos é **o projeto Migração.**


## <a name="created-resources"></a>Recursos criados

Estas tabelas resumem os recursos criados para a descoberta, avaliação e migração num projeto Azure Migrate.

> [!NOTE]
> Apague o cofre com cuidado porque pode conter chaves de segurança.

### <a name="vmwarephysical-server"></a>VMware/servidor físico

**Recurso** | **Tipo**
--- | ---
"Appliancename"kv | Key Vault
Site "Appliancename". | Microsoft.OffAzure/VMwareSites
"Nome do Projeto" | Projetos microsoft.migrate/migrate
Projeto "ProjectName". | Microsoft.Migrar/avaliarProjetos
"ProjectName"rsvault | Cofre dos Serviços de Recuperação
"ProjectName"-MigrateVault-* | Cofre dos Serviços de Recuperação
migrateappligwsa* | Conta de armazenamento
migrateapplilsa* | Conta de armazenamento
migrateapplicsa* | Conta de armazenamento
migrateapplikv* | Key Vault
migrateapplisbns16041 | Espaço de Nomes do Service Bus

### <a name="hyper-v-vm"></a>VM do Hyper-V 

**Recurso** | **Tipo**
--- | ---
"Nome do Projeto" | Projetos microsoft.migrate/migrate
Projeto "ProjectName". | Microsoft.Migrar/avaliarProjetos
HyperV*kv | Key Vault
HiperV*Site | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Cofre dos Serviços de Recuperação


## <a name="next-steps"></a>Passos seguintes

Saiba como adicionar ferramentas adicionais de [avaliação](how-to-assess.md) e [migração.](how-to-migrate.md) 
