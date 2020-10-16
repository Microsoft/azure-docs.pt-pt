---
title: Descubra aplicativos, funções e funcionalidades em servidores no local com a Azure Migrate
description: Saiba como descobrir apps, funções e funcionalidades em servidores no local com avaliação do servidor Azure Migrate.
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: 535c8ae8c2d6e5d9d175e663a58d47dc76aa0529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86118652"
---
# <a name="discover-machine-apps-roles-and-features"></a>Descubra aplicativos, papéis e funcionalidades de máquinas

Este artigo descreve como descobrir aplicações, funções e funcionalidades em servidores no local, utilizando Azure Migrate:Server Assessment.

Descobrir o inventário de apps, funções e funcionalidades em execução em máquinas no local ajuda a identificar e adaptar um caminho de migração para Azure para as suas cargas de trabalho. A descoberta de aplicativos utiliza o aparelho Azure Migrate para realizar a descoberta, utilizando credenciais de hóspedes VM. A descoberta de aplicativos é sem agente. Nada está instalado em VMs.

> [!NOTE]
> A descoberta de aplicações encontra-se atualmente em pré-visualização apenas para VMware VMs, e está limitada apenas à descoberta. Ainda não oferecemos avaliação baseada em aplicativos. 


## <a name="before-you-start"></a>Antes de começar

- Certifique-se de que:
    - [Criei](how-to-add-tool-first-time.md) um projeto Azure Migrate.
    - [Adicionei](how-to-assess.md) a ferramenta Azure Migrate:Server Assessment a um projeto.
- Reveja [o suporte e requisitos de descoberta de aplicações.](migrate-support-matrix-vmware.md#vmware-requirements)
- Certifique-se de que os VMs onde está a executar a descoberta de aplicações têm a versão PowerShell 2.0 ou posteriormente instalada, e as Ferramentas VMware (mais tarde de 10.2.0) estão instaladas.
- Verifique os [requisitos](migrate-appliance.md) para a colocação do aparelho Azure Migrate.


## <a name="deploy-the-azure-migrate-appliance"></a>Implementar o aparelho Azure Migrate

1. [Reveja](migrate-appliance.md#appliance---vmware) os requisitos para a colocação do aparelho Azure Migrate.
2. Reveja os URLs Azure que o aparelho terá de aceder nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
3. [Reveja](migrate-appliance.md#collected-data---vmware) os dados que o aparelho recolhe durante a descoberta e avaliação.
4. [Observe](migrate-support-matrix-vmware.md#port-access-requirements) os requisitos de acesso à porta para o aparelho.
5. [Desloque o aparelho Azure Migrate](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implantar o aparelho, descarregue e importe um modelo OVA em VMware para criar o aparelho como VMware VM. Configuure o aparelho e registe-o com o Azure Migrate.
6. Ao implantar o aparelho, para iniciar uma descoberta contínua, especifique o seguinte:
    - O nome do servidor vCenter ao qual pretende ligar.
    - Credenciais que criou para o aparelho ligar ao servidor vCenter.
    - As credenciais de conta que criou para o aparelho ligar-se aos VMs Windows/Linux.

Depois de o aparelho ser implantado e tiver fornecido credenciais, o aparelho inicia a descoberta contínua de metadados VM e dados de desempenho, juntamente com a descoberta e descoberta de apps, funcionalidades e funções.  A duração da descoberta de aplicações depende de quantos VMs você tem. Normalmente leva uma hora para a descoberta de aplicativos de 500 VMs.

## <a name="verify-permissions"></a>Verificar permissões

[Criou uma conta de leitura do vCenter Server apenas](tutorial-prepare-vmware.md#set-up-permissions-for-assessment) para a descoberta e avaliação. A conta apenas de leitura necessita de privilégios habilitados para **operações de hóspedes de máquinas virtuais,**  >  **Guest Operations**de forma a interagir com o VM para a descoberta de apps.

### <a name="add-the-user-account-to-the-appliance"></a>Adicione a conta de utilizador ao aparelho

Adicione a conta de utilizador da seguinte forma:

1. Abra a aplicação de gestão do aparelho. 
2. Navegue para o painel **de detalhes do Provide vCenter.**
3. Na **aplicação Discover e dependências em VMs,** clique em **Adicionar credenciais**
3. Escolha o **sistema Operativo,** forneça um nome amigável para a conta e a **User name** / **palavra-passe** do nome de utilizador
6. Clique em **Guardar**.
7. Clique **em Guardar e iniciar a descoberta.**

    ![Adicionar conta de utilizador VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>Rever e exportar o inventário

Após o fim da descoberta, se forneceu credenciais para a descoberta de apps, pode rever e exportar o inventário de aplicações no portal Azure.

1. Em **Azure Migrate - Servidores**  >  **Azure Migrate: Avaliação**do servidor , clique na contagem visualizada para abrir a página dos **servidores Descobertos.**

    > [!NOTE]
    > Nesta fase, também pode configurar opcionalmente a análise de dependência para máquinas descobertas, para que possa visualizar dependências através de máquinas que pretende avaliar. [Saiba mais](concepts-dependency-visualization.md) sobre a análise da dependência.

2. Nas **Aplicações descobertas,** clique na contagem visualizada.
3. No **inventário de aplicações,** pode rever as aplicações, funções e funcionalidades descobertas.
4. Para exportar o inventário, em **Servidores Descobertos,** clique no **inventário da aplicação Export**.

O inventário da aplicação é exportado e descarregado no formato Excel. A folha **de Inventário de Aplicações** exibe todas as aplicações descobertas em todas as máquinas.

## <a name="next-steps"></a>Passos seguintes

- [Crie uma avaliação](how-to-create-assessment.md) para servidores descobertos.
- Avaliar bases de dados do SQL Server utilizando [Azure Migrate:Base de dados Avaliação](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
