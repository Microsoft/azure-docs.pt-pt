---
title: Descubra aplicações, papéis e funcionalidades em servidores no local com a Azure Migrate
description: Saiba como descobrir aplicações, papéis e funcionalidades em servidores no local com avaliação do servidor de migração Azure.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: ff9f5489b513cd1405e6b093d7537e4cbcead041
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744618"
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



## <a name="deploy-the-azure-migrate-appliance"></a>Implante o aparelho Azure Migrate

1. [Reveja](migrate-appliance.md#appliance---vmware) os requisitos para a implantação do aparelho Azure Migrate.
2. Reveja os URLs Azure a que o aparelho terá de aceder nas [nuvens](migrate-appliance.md#government-cloud-urls) [públicas](migrate-appliance.md#public-cloud-urls) e governamentais.
3. [Reveja os dados](migrate-appliance.md#collected-data---vmware) que o aparelho recolhe durante a descoberta e avaliação.
4. [Note](migrate-support-matrix-vmware.md#port-access) os requisitos de acesso à porta do aparelho.
5. [Implante o aparelho Azure Migrate](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implantar o aparelho, faça o download e importe um modelo DEOVA em VMware para criar o aparelho como VMware VM. Configure o aparelho e, em seguida, registe-o com a Migração Azure.
6. Ao utilizar o aparelho, para iniciar uma descoberta contínua, especifique o seguinte:
    - O nome do vCenter Server ao qual pretende ligar.
    - Credenciais que criou para o aparelho ligar ao VCenter Server.
    - As credenciais de conta que criou para o aparelho ligar aos VMs Windows/Linux.

Depois de o aparelho ser implantado e ter fornecido credenciais, o aparelho inicia a descoberta contínua de metadados VM e dados de desempenho, juntamente com e descoberta de apps, funcionalidades e funções.  A duração da descoberta da aplicação depende de quantos VMs você tem. Normalmente demora uma hora para a descoberta de aplicações de 500 VMs.

## <a name="prepare-a-user-account"></a>Preparar uma conta de utilizador

Crie uma conta para ser descoberta e adicione-a ao aparelho.

### <a name="create-a-user-account-for-discovery"></a>Criar uma conta de utilizador para descoberta

Configurar uma conta de utilizador para que a Avaliação do Servidor possa aceder ao VM para ser descoberta. [Conheça](migrate-support-matrix-vmware.md#application-discovery) os requisitos da conta.


### <a name="add-the-user-account-to-the-appliance"></a>Adicione a conta do utilizador ao aparelho

Adicione a conta de utilizador ao aparelho.

1. Abra a aplicação de gestão de aparelhos. 
2. Navegue para o painel de **detalhes Provide vCenter.**
3. Em **Discover aplicação e dependências em VMs,** clique em **Adicionar credenciais**
3. Escolha o **sistema operativo,** forneça um nome amigável para a conta e o **nome de utilizador**/**Palavra-passe**
6. Clique em **Guardar**.
7. Clique em **Guardar e começar a descoberta**.

    ![Adicionar conta de utilizador VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)





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
