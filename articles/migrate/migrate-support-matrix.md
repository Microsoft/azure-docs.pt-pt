---
title: Matriz de suporte Azure Migrate
description: Fornece um resumo das configurações e limitações de suporte para o serviço Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: b9ea447b0204ad91065f27d265584c8787167fc2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870726"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte Azure Migrate

Pode utilizar o [serviço Azure Migrate](./migrate-services-overview.md) para avaliar e migrar servidores para a nuvem Microsoft Azure. Este artigo resume configurações e limitações gerais de suporte para cenários e implementações da Azure Migrate.

## <a name="supported-assessmentmigration-scenarios"></a>Cenários de avaliação/migração apoiados

A tabela resume os cenários de descoberta, avaliação e migração suportados.

**Implementação** | **Detalhes** 
--- | --- 
**Deteção** | Pode descobrir metadados de servidor e dados de desempenho dinâmicos.
**Descoberta de aplicativos** | Pode descobrir apps, funções e funcionalidades em execução em VMware VMs. Atualmente esta funcionalidade está limitada apenas à descoberta. A avaliação está atualmente ao nível do servidor. Ainda não oferecemos avaliações baseadas em aplicativos, papéis ou funcionalidades. 
**Avaliação** | Avaliar as cargas de trabalho e os dados em execução em VMware VMs, Hiper-VMs e servidores físicos. Avaliar usando Azure Migrate: Avaliação do servidor, Assistente de Migração de Dados do Microsoft (DMA), bem como outras ferramentas e ofertas isv.
**Migração** | Migrar cargas de trabalho e dados em execução em servidores físicos, VMware VMs, VMs Hiper-V, servidores físicos e VMS baseados na nuvem para Azure. Migrar usando a Azure Migrate: Server Assessment and Azure Database Migration Service (DMS), bem como outras ferramentas e ofertas isv.

> [!NOTE]
> Atualmente, as ferramentas ISV não podem enviar dados para a Azure Migrate no Governo de Azure. Pode utilizar ferramentas integradas da Microsoft ou utilizar ferramentas parceiras de forma independente.

## <a name="supported-tools"></a>Ferramentas suportadas


O suporte específico da ferramenta é resumido na tabela.

**Ferramenta** | **Avaliação** | **Migrate** 
--- | --- | ---
Azure Migrate: Avaliação do servidor | Avaliar [VMware VMs,](./tutorial-discover-vmware.md) [VMs hiper-V](./tutorial-discover-hyper-v.md)e [servidores físicos](./tutorial-discover-physical.md). |  Não disponível (N/A)
Azure Migrate: Migração de servidores | N/D | Migrar [VMware VMs,](tutorial-migrate-vmware.md) [Hiper-VMs](tutorial-migrate-hyper-v.md)e [servidores físicos](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | N/D | Migrar VMware VMs, VMs hiper-V, servidores físicos e outras cargas de trabalho em nuvem. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Avaliar VMware VMs, VMs hiper-V, servidores físicos e outras cargas de trabalho na nuvem. | N/D
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Avaliar VMware VMs, VMs hiper-V, servidor físico e outras cargas de trabalho em nuvem. |  Migrar VMware VMs, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública.
[Dispositivo 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Avaliar VMware VMs, VMs hiper-V, servidores físicos e outras cargas de trabalho na nuvem.| N/D
[DMA](/sql/dma/dma-overview?view=sql-server-2017) | Avaliar bases de dados do SQL Server. | N/D
[DMS](../dms/dms-overview.md) | N/D | Migrate SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar a infraestrutura virtual de ambiente de trabalho (VDI) | N/D
[Movere](https://www.movere.io/) | Avaliar VMware VMs, VMs Hiper-V, Xen VMs, servidores físicos, estações de trabalho (incluindo VDI) e outras cargas de trabalho em nuvem. | N/D
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | N/D | VMS migram VMWare, VMs hiper-V, Xen VMs, VMs KVM, servidores físicos e outras cargas de trabalho em nuvem 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Avaliar VMware VMs, VMs hiper-V, servidores físicos e outras cargas de trabalho na nuvem. | N/D
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Avaliar VMware VMs, VMs hiper-V, servidores físicos e outras cargas de trabalho em nuvem e bases de dados do SQL Server. | N/D
[Assistente de Migração webapp](https://appmigration.microsoft.com/) | Avaliar aplicativos web | Migrar aplicativos web.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | N/D |  Migrar VMware VMs, VMs hiper-V, servidores físicos e outras cargas de trabalho em nuvem.


## <a name="project"></a>Project

**Suporte** | **Detalhes**
--- | ---
Subscrição | Pode ter vários projetos dentro de uma subscrição.
Permissões Azure | O utilizador necessita de permissões de Colaborador ou Proprietário na subscrição para criar um projeto.
VMs VMware  | Avalie até 35.000 VMware VMs num único projeto.
VMs Hyper-V    | Avalie até 35.000 Hiper-VMs num único projeto.

Um projeto pode incluir VMware VMs e VMs hiper-V, até os limites de avaliação.

## <a name="azure-permissions"></a>Permissões Azure

Para a Azure Migrar para trabalhar com o Azure, precisa destas permissões antes de começar a avaliar e migrar servidores.

**Tarefa** | **Permissões** | **Detalhes**
--- | --- | ---
Criar um projeto | A sua conta Azure precisa de permissões para criar um projeto. | Configurar para [VMware,](./tutorial-discover-vmware.md#prepare-an-azure-user-account) [Hiper-V,](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)ou [servidores físicos](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Registe o aparelho Azure Migrate| A Azure Migrate usa um [aparelho Azure Migrate](migrate-appliance.md) leve para avaliar servidores com Azure Migrate: Avaliação do servidor e para executar [migração sem agentes](server-migrate-overview.md) de VMware VMs com Azure Migrate: Migração de servidores. Este aparelho descobre servidores e envia metadados e dados de desempenho para a Azure Migrate.<br/><br/> Durante o registo, os fornecedores de registo (Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault) estão registados com a subscrição escolhida no aparelho, de modo a que a subscrição funcione com o fornecedor de recursos. Para se registar, precisa de acesso do Colaborador ou proprietário na subscrição.<br/><br/> **VMware**-Durante o embarque, a Azure Migrate cria duas aplicações Azure Ative Directory (Azure AD). A primeira aplicação comunica entre os agentes do aparelho e o serviço Azure Migrate. A aplicação não tem permissões para fazer chamadas de gestão de recursos Azure ou ter acesso a Azure RBAC para recursos. A segunda aplicação acede a um Azure Key Vault criado na subscrição do utilizador apenas para migração de VMware sem agente. Na migração sem agente, a Azure Migrate cria um Cofre-Chave para gerir as chaves de acesso à conta de armazenamento de replicação na sua subscrição. Tem acesso Azure RBAC no Cofre da Chave Azure (no inquilino do cliente) quando a descoberta é iniciada a partir do aparelho.<br/><br/> **Hiper-V**- Durante o embarque. Azure Migrate cria uma aplicação AD Azure. A aplicação comunica entre os agentes do aparelho e o serviço Azure Migrate. A aplicação não tem permissões para fazer chamadas de gestão de recursos Azure ou ter acesso a Azure RBAC para recursos. | Configurar para [VMware,](./tutorial-discover-vmware.md#prepare-an-azure-user-account) [Hiper-V,](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)ou [servidores físicos](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Crie um cofre chave para migração sem agente VMware | Para migrar VMware VMs com Azure Migrate sem agente: Migração do servidor, a Azure Migrate cria um Cofre-Chave para gerir as chaves de acesso à conta de armazenamento de replicação na sua subscrição. Para criar o cofre, define permissões (Proprietário, ou Administrador de Dados e Acesso ao Utilizador) no grupo de recursos onde o projeto reside. | [Estabeleça](./tutorial-discover-vmware.md#prepare-an-azure-user-account) permissões.

## <a name="supported-geographies-public-cloud"></a>Geografias apoiadas (nuvem pública)

Pode criar um projeto em várias geografias na nuvem pública.

- Embora só possa criar projetos nestas geografias, pode avaliar ou migrar servidores para outros locais-alvo.
- A geografia do projeto é usada apenas para armazenar os metadados descobertos.
- Quando se cria um projeto, seleciona-se uma geografia. O projeto e recursos conexos são criados numa das regiões da geografia. A região é atribuída pelo serviço Azure Migrate.

**Geografia** | **Localização de armazenamento de metadados**
--- | ---
Ásia-Pacífico | Ásia Oriental ou Sudeste Asiático
Austrália | Austrália Leste ou Austrália Sudeste
Brasil | Sul do Brasil
Canadá | Canadá Central ou Canadá Leste
Europa | Europa do Norte ou Europa Ocidental
França | França Central
Índia | Índia Central ou Sul da Índia
Japão |  Japão Leste ou Japão Oeste
Coreia | Coreia do Sul ou Coreia do Sul
Suíça | Suíça Norte
Reino Unido | Reino Unido Sul ou Reino Unido Oeste
Estados Unidos da América | Central EUA ou Oeste 2

> [!NOTE]
> Para a geografia suíça, a Suíça West só está disponível para utilizadores da API REST e precisa de uma subscrição aprovada.

## <a name="supported-geographies-azure-government"></a>Geografias apoiadas (Governo Azure)

**Tarefa** | **Geografia** | **Detalhes**
--- | --- | ---
Criar o projeto | Estados Unidos da América | Metadados são armazenados em Eua Gov Arizona, EUA Gov Virginia
Avaliação do destino | Estados Unidos da América | Regiões-alvo: EUA Gov Arizona, US Gov Virginia, US Gov Texas
Replicação do alvo | Estados Unidos da América | Regiões-alvo: US DoD Central, US DoD East, US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia


## <a name="vmware-assessment-and-migration"></a>Avaliação e migração de VMware

[Reveja](migrate-support-matrix-vmware.md) o Azure Migrate: Avaliação do servidor e Azure Migrate: Matriz de suporte à migração do servidor para VMware VMs.

## <a name="hyper-v-assessment-and-migration"></a>Avaliação e migração do Hyper-V

[Reveja](migrate-support-matrix-hyper-v.md) o Azure Migrate: Avaliação do servidor e Azure Migrate: Matriz de suporte à migração do servidor para VMs hiper-V.



## <a name="azure-migrate-versions"></a>Versões Azure Migrate

Existem duas versões do serviço Azure Migrate:

- **Versão atual**: Utilizando esta versão pode criar novos projetos, descobrir avaliações no local e orquestrar avaliações e migrações. [Saiba mais](whats-new.md).
- **Versão anterior**: Para o cliente utilizar a versão anterior do Azure Migrate (apenas foi suportada a avaliação dos VMS VMware no local), deverá agora utilizar a versão atual. Na versão anterior, não é possível criar novos projetos ou realizar novas descobertas.

## <a name="next-steps"></a>Passos seguintes

- [Avaliar VMware VMs](./tutorial-assess-vmware-azure-vm.md) para migração.
- [Avaliar os VMs hiper-V](tutorial-assess-hyper-v.md) para migração.
