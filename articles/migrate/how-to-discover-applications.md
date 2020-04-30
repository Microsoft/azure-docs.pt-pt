---
title: Descubra aplicações, papéis e funcionalidades em servidores no local com a Azure Migrate
description: Saiba como descobrir aplicações, papéis e funcionalidades em servidores no local com avaliação do servidor de migração Azure.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79453587"
---
# <a name="discover-machine-apps-roles-and-features"></a>Descubra aplicações, papéis e funcionalidades de máquinas

Este artigo descreve como descobrir aplicações, funções e funcionalidades em servidores no local, utilizando o Azure Migrate: Server Assessment.

Descobrir o inventário de apps e papéis/funcionalidades em execução nas suas máquinas no local ajuda-o a identificar e planear um caminho de migração para o Azure que é adaptado para as suas cargas de trabalho.

> [!NOTE]
> A descoberta de aplicações está atualmente em pré-visualização apenas para VMware VMs, e está limitada apenas à descoberta. Ainda não oferecemos uma avaliação baseada em aplicativos. Avaliação baseada em máquinas para VMware no local, VMs hiper-V e servidores físicos.

Descoberta de aplicativos usando Azure Migrate: A avaliação do servidor é sem agente. Nada está instalado em máquinas e VMs. A Avaliação do Servidor utiliza o aparelho Azure Migrate para realizar a descoberta juntamente com credenciais de hóspedes da máquina. O aparelho acede remotamente às máquinas VMware utilizando APIs VMware.


## <a name="before-you-start"></a>Antes de começar

1. Certifique-se de [ter criado](how-to-add-tool-first-time.md) um projeto Azure Migrate.
2. Certifique-se de que [adicionou](how-to-assess.md) a ferramenta de avaliação do servidor Azure Migrate a um projeto.
4. Verifique os [requisitos vMware](migrate-support-matrix-vmware.md#vmware-requirements) para descobrir e avaliar VMware VMs com o aparelho Azure Migrate.
5. Verifique os [requisitos](migrate-appliance.md) para a colocação do aparelho Azure Migrate.
6. [Verifique o suporte e os requisitos](migrate-support-matrix-vmware.md#application-discovery) para a descoberta da aplicação.

## <a name="prepare-for-app-discovery"></a>Prepare-se para a descoberta de apps

1. [Prepare-se para a colocação do aparelho](tutorial-prepare-vmware.md). A preparação inclui verificar as definições do aparelho e criar uma conta que o aparelho utilizará para aceder ao VCenter Server.
2. Certifique-se de que tem uma conta de utilizador (uma para servidores Windows e Linux) com permissões de administrador para máquinas nas quais pretende descobrir aplicações, funções e funcionalidades.
3. [Implante o aparelho Azure Migrate](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implantar o aparelho, faça o download e importe um modelo DEOVA em VMware para criar o aparelho como VMware VM. Configure o aparelho e, em seguida, registe-o com a Migração Azure.
2. Ao utilizar o aparelho, para iniciar uma descoberta contínua, especifique o seguinte:
    - O nome do vCenter Server ao qual pretende ligar.
    - Credenciais que criou para o aparelho ligar ao VCenter Server.
    - As credenciais de conta que criou para o aparelho ligar aos VMs Windows/Linux.

Depois de o aparelho ser implantado e ter fornecido credenciais, o aparelho inicia a descoberta contínua de metadados VM e dados de desempenho, juntamente com e descoberta de apps, funcionalidades e funções.  A duração da descoberta da aplicação depende de quantos VMs você tem. Normalmente demora uma hora para a descoberta de aplicações de 500 VMs.

## <a name="review-and-export-the-inventory"></a>Rever e exportar o inventário

Após o fim da descoberta, se forneceu credenciais para a descoberta de apps, pode rever e exportar o inventário da aplicação no portal Azure.

1. Em **Azure Migrate - Servers** > **Azure Migrate: Servers Group Assessment**, clique na contagem visualizada para abrir a página de **servidores Descobertos.**

    > [!NOTE]
    > Nesta fase também pode configurar opcionalmente o mapeamento de dependência para máquinas descobertas, para que possa visualizar dependências através de máquinas que pretende avaliar. [Saiba mais](how-to-create-group-machine-dependencies.md).

2. Nas **Aplicações descobertas,** clique na contagem exibida.
3. No **inventário de aplicações,** pode rever as aplicações, funções e funcionalidades descobertas.
4. Para exportar o inventário, em **Servidores Descobertos,** clique no inventário de **aplicativos exportação.**

O inventário da aplicação é exportado e descarregado em formato Excel. A folha de Inventário de **Aplicações** exibe todas as aplicações descobertas em todas as máquinas.

## <a name="next-steps"></a>Passos seguintes

- [Crie uma avaliação](how-to-create-assessment.md) para a migração de elevadores e mudanças dos servidores descobertos.
- Avaliar uma base de dados do Servidor SQL utilizando [o Azure Migrate: Avaliação da base de dados](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
