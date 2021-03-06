---
title: Eliminar um projeto do Azure Migrate
description: Neste artigo, saiba como pode eliminar um projeto Azure Migrate utilizando o portal Azure.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714745"
---
# <a name="delete-an-azure-migrate-project"></a>Eliminar um projeto do Azure Migrate

Este artigo descreve como eliminar um projeto [Azure Migrate.](./migrate-services-overview.md)


## <a name="before-you-start"></a>Antes de começar

Antes de apagar um projeto:

- Quando elimina um projeto, o projeto e os metadados de máquina descobertos são eliminados.
- Se anexou um espaço de trabalho do Log Analytics à ferramenta de Avaliação do Servidor para análise de dependência, decida se pretende eliminar o espaço de trabalho. 
    - O espaço de trabalho não é apagado automaticamente. Apague-o manualmente.
    - Verifique para que espaço de trabalho é utilizado antes de eliminá-lo. O mesmo espaço de trabalho log Analytics pode ser usado para vários cenários.
    - Antes de eliminar o projeto, pode encontrar uma ligação para o espaço de trabalho em **Azure Migrate - Servers**  >  **Azure Migrate - Avaliação do Servidor,** no **espaço de trabalho OMS**.
    - Para eliminar um espaço de trabalho após a eliminação de um projeto, encontre o espaço de trabalho no grupo de recursos relevante e siga [estas instruções](../azure-monitor/logs/delete-workspace.md).


## <a name="delete-a-project"></a>Excluir um projeto


1. No portal Azure, abra o grupo de recursos em que o projeto foi criado.
2. Na página do grupo de recursos, selecione **Mostrar os tipos ocultos**.
3. Selecione o projeto e os recursos associados que pretende eliminar.
    - O tipo de recurso para projetos Azure Migrate é **Microsoft.Migrate/migrateprojects**.
    - Na secção seguinte, reveja os recursos criados para a descoberta, avaliação e migração num projeto da Azure Migrate.
    - Se o grupo de recursos apenas contiver o projeto Azure Migrate, pode eliminar todo o grupo de recursos.
    - Se pretender eliminar um projeto da versão anterior do Azure Migrate, os passos são os mesmos. O tipo de recurso para estes projetos é **o projeto Migração.**


## <a name="created-resources"></a>Recursos criados

Estas tabelas resumem os recursos criados para a descoberta, avaliação e migração num projeto da Azure Migrate.

> [!NOTE]
> Elimine o cofre com cuidado porque pode conter chaves de segurança.

### <a name="projects-with-public-endpoint-connectivity"></a>Projetos com conectividade de ponto final público

#### <a name="vmwarephysical-server"></a>VMware/servidor físico

**Recurso** | **Tipo**
--- | ---
"Nome de eletrodoméstico"kv | Key Vault
Site "Appliancename" | Microsoft.OffAzure/VMwareSites
"Nome do Projeto" | Microsoft.Migrate/migrateprojects
Projeto "ProjectName" | Microsoft.Migrate/assessmentProjects
"ProjectName"rsvault | Cofre dos Serviços de Recuperação
"ProjectName"-MigrateVault-* | Cofre dos Serviços de Recuperação
migrarappligwsa* | Conta de armazenamento
migrarapplilsa* | Conta de armazenamento
migrarapplicsa* | Conta de armazenamento
migrarapplikv* | Key Vault
migrarapplisbns* | Espaço de Nomes do Service Bus

#### <a name="hyper-v-vm"></a>VM do Hyper-V

**Recurso** | **Tipo**
--- | ---
"Nome do Projeto" | Microsoft.Migrate/migrateprojects
Projeto "ProjectName" | Microsoft.Migrate/assessmentProjects
HiperV*kv | Key Vault
HiperV*Site | Microsoft.OffAzure/HiperVSites
"ProjectName"-MigrateVault-* | Cofre dos Serviços de Recuperação

<br/>
As tabelas que se seguem resumem os recursos criados pela Azure Migrate para descobrir, avaliar e migrar servidores através de uma rede privada utilizando [o link privado Azure](./how-to-use-azure-migrate-with-private-endpoints.md).

### <a name="projects-with-private-endpoint-connectivity"></a>Projetos com conectividade de ponto final privado

#### <a name="vmware-vms---agentless-migrations"></a>VMware VMs - migrações sem agentes

**Tipo** | **Recurso** | **Ponto final privado <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "Nome do Projeto" | "ProjectName" \* pe 
Site de descoberta (local principal) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | Projeto "ApplianceName"* | Projeto "ApplianceName" \* \* pe 
Key Vault | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/VMwareSites | "ApplianceName"*site | ND
Cofre dos Serviços de Recuperação | "ApplianceName"*cofre | ND
Conta de armazenamento | "ApplianceName"*eua | "ApplianceName" \* usa \* pe
Cofre dos Serviços de Recuperação | "ProjectName"-MigrateVault-* | ND
Conta de armazenamento | migrarappligwsa* | ND
Conta de armazenamento | migrarapplilsa* | ND
Key Vault | migrarapplikv* | ND
Espaço de Nomes do Service Bus | migrarapplisbns* | ND

#### <a name="hyper-v-vms"></a>VMs Hyper-V 

**Tipo** | **Recurso** | **Ponto final privado <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "Nome do Projeto" | "ProjectName" \* pe 
Site de descoberta (local principal) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | Projeto "ApplianceName"* | Projeto "ApplianceName" \* \* pe 
Key Vault | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/HiperVSites | "ApplianceName"*site | ND
Cofre dos Serviços de Recuperação | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe

#### <a name="physical-servers--aws-vms--gcp-vms"></a>Servidores físicos / VMs AWS / VMs GCP 

**Tipo** | **Recurso** | **Ponto final privado <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "Nome do Projeto" | "ProjectName" \* pe 
Site de descoberta (local principal) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | Projeto "ApplianceName"* | Projeto "ApplianceName" \* \* pe 
Key Vault | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/servidores | "ApplianceName"*site | ND
Cofre dos Serviços de Recuperação | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe


## <a name="next-steps"></a>Passos seguintes

Saiba como adicionar ferramentas adicionais de [avaliação](how-to-assess.md) e [migração.](how-to-migrate.md) 
