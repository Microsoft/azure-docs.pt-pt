---
title: Matriz de suporte de migração Azure
description: Fornece um resumo das definições de suporte e limitações para o serviço Azure Migrate.
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: raynew
ms.openlocfilehash: 5c3adecf62f9bd15c820baf116dbc01e5d3542fa
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362159"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte de migração Azure

Pode utilizar o [serviço Azure Migrate](migrate-overview.md) para avaliar e migrar máquinas para a nuvem Microsoft Azure. Este artigo resume as definições e limitações gerais de suporte para cenários e implementações do Azure Migrate.

## <a name="supported-assessmentmigration-scenarios"></a>Cenários de avaliação/migração apoiados

A tabela resume cenários de descoberta, avaliação e migração.

**Implementação** | **Detalhes** 
--- | --- 
**Descoberta específica da aplicação** | Pode descobrir aplicações, papéis e funcionalidades em VMware VMs. Atualmente esta funcionalidade limita-se apenas à descoberta. A avaliação está atualmente ao nível da máquina. Ainda não oferecemos app, role ou avaliação específica de funcionalidades. 
**Avaliação no local** | Avaliar as cargas de trabalho no local e os dados em execução em VMware VMs, VMs Hiper-V e servidores físicos. Avaliar utilizando a Avaliação do Servidor Migratório Azure e o Microsoft Data Migration Assistant (DMA), bem como outras ferramentas e ofertas ISV.
**Migração no local para Azure** | Migrar cargas de trabalho e dados em servidores físicos, VMware VMs, VMs Hiper-V, servidores físicos e VMS baseadoem em nuvem para Azure. Migrar utilizando a Avaliação do Servidor Migratório Azure e o Serviço de Migração de Bases de Dados Azure (DMS), bem como outras ferramentas e ofertas ISV.


## <a name="supported-tools"></a>Ferramentas suportadas

O suporte específico da ferramenta é resumido na tabela.

**Ferramenta** | **Avaliar** | **Migrar** 
--- | --- | ---
Avaliação do servidor migratório Azure | Avaliar [VMs VMware,](tutorial-prepare-vmware.md) [Hiper-V VMs](tutorial-prepare-hyper-v.md)e [servidores físicos.](tutorial-prepare-physical.md) |  Não disponível (NA)
Migração do servidor migratório Azure Migrate | ND | [VMs migratórios,](tutorial-migrate-vmware.md) [VMs hiper-V](tutorial-migrate-hyper-v.md)e [servidores físicos.](tutorial-migrate-physical-virtual-machines.md)
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | ND | VMs migratórios, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Avaliar VMs VMware, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública. | ND
[Tecnologia Corent](https://go.microsoft.com/fwlink/?linkid=2084928) | Avaliar e migrar VMware VMs, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública. |  VMs migratórios, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública.
[Dispositivo 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Avaliar VMs VMware, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública.| ND
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Avaliar as bases de dados do SQL Server no local. | ND
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | ND | Migrar SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar a infraestrutura virtual de ambiente de trabalho (VDI) | ND
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Avaliar VMs VMs, Hiper-VMs, VMs Xen, máquinas físicas, estações de trabalho (incluindo VDI), cargas de trabalho em nuvem pública | ND
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | ND | VMs migratórios VMWare, VMs Hiper-V, VMs Xen, VMs KVM, máquinas físicas, cargas de trabalho em nuvem pública 
[Turbonómico](https://go.microsoft.com/fwlink/?linkid=2094295)  | Avaliar VMs VMware, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública. | ND
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Avaliar VMs VMware, VMs Hiper-V, servidores físicos, cargas de trabalho em nuvem pública e bases de dados do Servidor SQL. | ND
[Assistente de Migração webapp](https://appmigration.microsoft.com/) | Avaliar aplicações web | Migrar aplicativos web.


## <a name="azure-migrate-projects"></a>Projetos azure migrate

**Suporte** | **Detalhes**
--- | ---
Subscrição | Você pode ter vários projetos Azure Migrate em uma subscrição.
Permissões azure | Você precisa de permissões de Colaborador ou Proprietário na subscrição para criar um projeto Azure Migrate.
VMs VMware  | Avalie até 35.000 VMware VMs num único projeto.
VMs Hyper-V | Avalie até 35.000 VMs hiper-V num único projeto.

Um projeto pode incluir vMs VMware e VMs Hiper-V, até os limites de avaliação.

## <a name="azure-permissions"></a>Permissões azure

Para que a Azure Migrate trabalhe com o Azure, precisa destas permissões antes de começar a avaliar e migrar máquinas.

**Tarefa** | **Permissões** | **Detalhes**
--- | --- | ---
Criar um projeto Azure Migrate | A sua conta Azure precisa de permissões para criar um projeto. | Configurar para [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-create-project) [Hiper-V,](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)ou [servidores físicos.](tutorial-prepare-physical.md#assign-permissions-to-create-project)
Registe o aparelho Azure Migrate| A Azure Migrate utiliza um [aparelho ligeiro de migração Azure](migrate-appliance.md) para avaliar máquinas com avaliação do servidor de migração Azure e para executar a [migração sem agentes](server-migrate-overview.md) de VMware VMs com migração do servidor migratório Azure Migrate. Este aparelho descobre máquinas e envia metadados e dados de desempenho para o Azure Migrate.<br/><br/> Durante o registo, os fornecedores de registo (Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault) estão registados com a subscrição escolhida no aparelho, de modo a que a subscrição funcione com o fornecedor de recursos. Para se registar, precisa de acesso do Colaborador ou proprietário na subscrição.<br/><br/> **VMware**-Durante o embarque, a Azure Migrate cria duas aplicações Azure Ative Directory (Azure AD). A primeira aplicação comunica entre os agentes do aparelho e o serviço Azure Migrate. A aplicação não tem permissões para fazer chamadas de gestão de recursos do Azure ou ter acesso RBAC para recursos. A segunda aplicação acede a um Cofre chave Azure criado apenas na subscrição do utilizador para migração vMware sem agente. Na migração sem agente, a Azure Migrate cria um Cofre chave para gerir as chaves de acesso da conta de armazenamento de replicação na sua subscrição. Tem acesso RBAC no Cofre de Chaves Azure (no inquilino do cliente) quando a descoberta é iniciada a partir do aparelho.<br/><br/> **Hyper-V**- Durante o embarque. A Azure Migrate cria uma aplicação Azure AD. A aplicação comunica entre os agentes do aparelho e o serviço Azure Migrate. A aplicação não tem permissões para fazer chamadas de gestão de recursos do Azure ou ter acesso RBAC para recursos. | Configurar para [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) [Hiper-V,](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)ou [servidores físicos.](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)
Crie um cofre chave para a migração sem agente VMware | Para migrar VMware VMs com migração de servidores de migração azure sem agente, a Azure Migrate cria um Cofre chave para gerir as chaves de acesso da conta de armazenamento de replicação na sua subscrição. Para criar o cofre, você estabelece permissões (Proprietário, ou Contribuinte e Administrador de Acesso ao Utilizador) no grupo de recursos em que o projeto Azure Migrate reside. | [Estabeleça](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) permissões.

## <a name="supported-geographies"></a>Geografias apoiadas

Pode criar um projeto Azure Migrate em várias geografias. Embora só possa criar projetos nestas geografias, pode avaliar ou migrar máquinas para outros locais-alvo. A geografia do projeto só é usada para armazenar os metadados descobertos.

**Geografia** | **Localização de armazenamento de metadados**
--- | ---
Azure Government | US Gov - Virginia
Ásia-Pacífico | Ásia Oriental ou Sudeste Asiático
Austrália | Austrália Leste ou Austrália Sudeste
Brasil | Sul do Brasil
Canadá | Canadá Central ou Canadá Leste
Europa | Norte da Europa ou Europa Ocidental
França | França Central
Índia | Índia Central ou Sul da Índia
Japão |  Japão Leste ou Japão Oeste
Coreia | Coreia Central ou Coreia do Sul
Reino Unido | Reino Unido Sul ou Reino Unido Oeste
Estados Unidos | Centro dos EUA ou Oeste DOS EUA 2


 > [!NOTE]
 > O apoio ao Governo Azure está atualmente disponível apenas para a [versão mais antiga](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) do Azure Migrate.



## <a name="vmware-assessment-and-migration"></a>Avaliação e migração vMware

[Reveja](migrate-support-matrix-vmware.md) a matriz de suporte de suporte de emigração do servidor Azure Migrate e do servidor para VMware VMware.

## <a name="hyper-v-assessment-and-migration"></a>Avaliação e migração do Hyper-V

[Reveja](migrate-support-matrix-hyper-v.md) a matriz de suporte de suporte de emigração do servidor Azure Migrate e do servidor para VMs hiper-V.



## <a name="azure-migrate-versions"></a>Versões Azure Migrate

Existem duas versões do serviço Azure Migrate:

- **Versão atual**: Utilizando esta versão pode criar novos projetos Azure Migrate, descobrir avaliações no local e orquestrar avaliações e migrações. [Saiba mais](whats-new.md#release-version-july-2019).
- **Versão anterior**: Para o cliente que utiliza a versão anterior do Azure Migrate (apenas foi suportada a avaliação dos VMs no local), deverá agora utilizar a versão atual. Na versão anterior, não é possível criar novos projetos da Azure Migrate ou realizar novas descobertas.

## <a name="next-steps"></a>Passos seguintes

- [Avaliar vMs vmware](tutorial-assess-vmware.md) para migração.
- [Avaliar os VMs hiper-V](tutorial-assess-hyper-v.md) para migração.

