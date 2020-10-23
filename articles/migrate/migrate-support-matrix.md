---
title: Matriz de suporte Azure Migrate
description: Fornece um resumo das configurações e limitações de suporte para o serviço Azure Migrate.
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: raynew
ms.openlocfilehash: 2aab8e9caeaf61c2c8dd1bf29894b13a887e44de
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424811"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte Azure Migrate

Pode utilizar o [serviço Azure Migrate](./migrate-services-overview.md) para avaliar e migrar máquinas para a nuvem Microsoft Azure. Este artigo resume configurações e limitações gerais de suporte para cenários e implementações da Azure Migrate.

## <a name="supported-assessmentmigration-scenarios"></a>Cenários de avaliação/migração apoiados

A tabela resume os cenários de descoberta, avaliação e migração suportados.

**Implementação** | **Detalhes** 
--- | --- 
**Deteção** | Pode descobrir metadados de máquina e dados de desempenho dinâmicos.
**Descoberta de aplicativos** | Pode descobrir apps, funções e funcionalidades em execução em VMware VMs. Atualmente esta funcionalidade está limitada apenas à descoberta. A avaliação está atualmente ao nível da máquina. Ainda não oferecemos avaliações baseadas em aplicativos, papéis ou funcionalidades. 
**Avaliação** | Avaliar as cargas de trabalho e os dados em execução em VMware VMs, Hiper-VMs e servidores físicos. Avalie usando a avaliação do servidor Azure Migrate, o Microsoft Data Migration Assistant (DMA), bem como outras ferramentas e ofertas isv.
**Migração** | Migrar cargas de trabalho e dados em execução em servidores físicos, VMware VMs, VMs Hiper-V, servidores físicos e VMS baseados na nuvem para Azure. Migrar usando o Azure Migrate Server Assessment e o Azure Database Migration Service (DMS), bem como outras ferramentas e ofertas isv.

> [!NOTE]
> Atualmente, as ferramentas ISV não podem enviar dados para a Azure Migrate no Governo de Azure. Pode utilizar ferramentas integradas da Microsoft ou utilizar ferramentas parceiras de forma independente.

## <a name="supported-tools"></a>Ferramentas suportadas


O suporte específico da ferramenta é resumido na tabela.

**Ferramenta** | **Avaliar** | **Migrar** 
--- | --- | ---
Avaliação do servidor Azure Migrate | Avaliar [VMware VMs,](./tutorial-discover-vmware.md) [VMs hiper-V](./tutorial-discover-hyper-v.md)e [servidores físicos](./tutorial-discover-physical.md). |  Não disponível (NA)
Migração do Servidor do Azure Migrate | ND | Migrar [VMware VMs,](tutorial-migrate-vmware.md) [Hiper-VMs](tutorial-migrate-hyper-v.md)e [servidores físicos](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | ND | Migrar VMware VMs, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Avaliar VMware VMs, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública. | ND
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Avaliar e migrar VMware VMs, Hiper-VMs, servidores físicos, cargas de trabalho em nuvem pública. |  Migrar VMware VMs, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública.
[Dispositivo 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Avaliar VMware VMs, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública.| ND
[DMA](/sql/dma/dma-overview?view=sql-server-2017) | Avaliar bases de dados do SQL Server. | ND
[DMS](../dms/dms-overview.md) | ND | Migrate SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar a infraestrutura virtual de ambiente de trabalho (VDI) | ND
[Movere](https://www.movere.io/) | Avaliar VMware VMs, VMs Hiper-V, Xen VMs, máquinas físicas, estações de trabalho (incluindo VDI), cargas de trabalho em nuvem pública | ND
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | ND | VMS migram VMWare, VMs hiper-V, Xen VMs, VMs KVM, máquinas físicas, cargas de trabalho em nuvem pública 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Avaliar VMware VMs, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública. | ND
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Avaliar VMware VMs, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública e bases de dados do SQL Server. | ND
[Assistente de Migração webapp](https://appmigration.microsoft.com/) | Avaliar aplicativos web | Migrar aplicativos web.


## <a name="azure-migrate-projects"></a>Projetos Azure Migrate

**Suporte** | **Detalhes**
--- | ---
Subscrição | Pode ter vários projetos da Azure Migrate numa subscrição.
Permissões Azure | Precisa de permissões de Colaborador ou Proprietário na subscrição para criar um projeto Azure Migrate.
VMs VMware  | Avalie até 35.000 VMware VMs num único projeto.
VMs Hyper-V    | Avalie até 35.000 Hiper-VMs num único projeto.

Um projeto pode incluir VMware VMs e VMs hiper-V, até os limites de avaliação.

## <a name="azure-permissions"></a>Permissões Azure

Para a Azure Migrar para trabalhar com o Azure, precisa destas permissões antes de começar a avaliar e migrar máquinas.

**Tarefa** | **Permissões** | **Detalhes**
--- | --- | ---
Criar um projeto do Azure Migrate | A sua conta Azure precisa de permissões para criar um projeto. | Configurar para [VMware,](./tutorial-discover-vmware.md#prepare-an-azure-user-account) [Hiper-V,](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)ou [servidores físicos](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Registe o aparelho Azure Migrate| A Azure Migrate usa um [aparelho Azure Migrate](migrate-appliance.md) leve para avaliar máquinas com avaliação do servidor Azure Migrate e para executar [migração sem agentes](server-migrate-overview.md) de VMware VMs com Migração de Servidores Azure Migrate. Este aparelho descobre máquinas e envia metadados e dados de desempenho para a Azure Migrate.<br/><br/> Durante o registo, os fornecedores de registo (Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault) estão registados com a subscrição escolhida no aparelho, de modo a que a subscrição funcione com o fornecedor de recursos. Para se registar, precisa de acesso do Colaborador ou proprietário na subscrição.<br/><br/> **VMware**-Durante o embarque, a Azure Migrate cria duas aplicações Azure Ative Directory (Azure AD). A primeira aplicação comunica entre os agentes do aparelho e o serviço Azure Migrate. A aplicação não tem permissões para fazer chamadas de gestão de recursos Azure ou ter acesso a Azure RBAC para recursos. A segunda aplicação acede a um Azure Key Vault criado na subscrição do utilizador apenas para migração de VMware sem agente. Na migração sem agente, a Azure Migrate cria um Cofre-Chave para gerir as chaves de acesso à conta de armazenamento de replicação na sua subscrição. Tem acesso Azure RBAC no Cofre da Chave Azure (no inquilino do cliente) quando a descoberta é iniciada a partir do aparelho.<br/><br/> **Hiper-V**- Durante o embarque. Azure Migrate cria uma aplicação AD Azure. A aplicação comunica entre os agentes do aparelho e o serviço Azure Migrate. A aplicação não tem permissões para fazer chamadas de gestão de recursos Azure ou ter acesso a Azure RBAC para recursos. | Configurar para [VMware,](./tutorial-discover-vmware.md#prepare-an-azure-user-account) [Hiper-V,](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)ou [servidores físicos](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Crie um cofre chave para migração sem agente VMware | Para migrar VMware VMs com a migração do servidor Azure Migrate Sem agente, a Azure Migrate cria um Cofre-Chave para gerir as chaves de acesso à conta de armazenamento de replicação na sua subscrição. Para criar o cofre, define permissões (Proprietário, ou Administrador de Dados e De acesso ao Utilizador) no grupo de recursos em que reside o projeto Azure Migrate. | [Estabeleça](./tutorial-discover-vmware.md#prepare-an-azure-user-account) permissões.

## <a name="supported-geographies-public-cloud"></a>Geografias apoiadas (nuvem pública)

Você pode criar um projeto Azure Migrate em várias geografias na nuvem pública.

- Embora só possa criar projetos nestas geografias, pode avaliar ou migrar máquinas para outros locais-alvo.
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

[Reveja](migrate-support-matrix-vmware.md) a matriz de suporte ao servidor Azure Migrate e à migração do servidor para VMware VMs.

## <a name="hyper-v-assessment-and-migration"></a>Avaliação e migração do Hyper-V

[Reveja](migrate-support-matrix-hyper-v.md) a matriz de suporte ao servidor Azure Migrate e à migração do servidor para VMs hiper-V.



## <a name="azure-migrate-versions"></a>Versões Azure Migrate

Existem duas versões do serviço Azure Migrate:

- **Versão atual**: Utilizando esta versão, pode criar novos projetos Azure Migrate, descobrir avaliações no local e orquestrar avaliações e migrações. [Saiba mais](whats-new.md).
- **Versão anterior**: Para o cliente utilizar a versão anterior do Azure Migrate (apenas foi suportada a avaliação dos VMS VMware no local), deverá agora utilizar a versão atual. Na versão anterior, não é possível criar novos projetos Azure Migrate ou realizar novas descobertas.

## <a name="next-steps"></a>Passos seguintes

- [Avaliar VMware VMs](./tutorial-assess-vmware-azure-vm.md) para migração.
- [Avaliar os VMs hiper-V](tutorial-assess-hyper-v.md) para migração.