---
title: Descubra o inventário de software em servidores no local com a Azure Migrate
description: Saiba como descobrir o inventário de software em servidores no local com a Azure Migrate Discovery e a avaliação.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 47ea06fa2143f9a5dc5808ccb98fc80c87fefd93
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786707"
---
# <a name="discover-installed-software-inventory-and-sql-server-instances-and-databases"></a>Descubra o inventário de software instalado e as instâncias e bases de dados do SQL Server

Este artigo descreve como descobrir o inventário de software instalado, e as instâncias e bases de dados do SQL Server em servidores em execução no seu ambiente VMware, utilizando a ferramenta Azure Migrate: Discovery e assessment.

A realização de inventário de software ajuda a identificar e adaptar um caminho de migração para Azure para as suas cargas de trabalho. O inventário de software utiliza o aparelho Azure Migrate para realizar a descoberta, utilizando credenciais de servidor. É completamente sem agente, nenhum agente é instalado nos servidores para recolher estes dados.

> [!NOTE]
> O inventário de software está atualmente em pré-visualização para servidores em execução apenas no ambiente VMware, e está limitado apenas à descoberta. Atualmente não oferecemos avaliação baseada em aplicações.<br/> A descoberta e avaliação de instâncias e bases de dados do SQL Server em execução no seu ambiente VMware está agora em pré-visualização. Para experimentar esta funcionalidade, utilize [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **Leste da Austrália**. Se já tiver um projeto no Leste da Austrália e quiser experimentar esta funcionalidade, verifique se concluiu estes [**pré-requisitos**](how-to-discover-sql-existing-project.md) no portal.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de que [criou um projeto](./create-manage-projects.md) com a ferramenta Azure Migrate: Discovery e assessment a editada.
- Reveja [os requisitos da VMware](migrate-support-matrix-vmware.md#vmware-requirements) para realizar o inventário de software.
- [Reveja os requisitos do aparelho](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) antes de configurar o aparelho.
- Reveja [os requisitos](migrate-support-matrix-vmware.md#application-discovery-requirements) de descoberta de aplicações antes de iniciar o inventário de software nos servidores.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Implementar e configurar o aparelho Azure Migrate

1. [Reveja](migrate-appliance.md#appliance---vmware) os requisitos para a colocação do aparelho Azure Migrate.
2. Reveja os URLs Azure que o aparelho terá de aceder nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
3. [Reveja](migrate-appliance.md#collected-data---vmware) os dados que o aparelho recolhe durante a descoberta e avaliação.
4. [Observe](migrate-support-matrix-vmware.md#port-access-requirements) os requisitos de acesso à porta para o aparelho.
5. [Desloque o aparelho Azure Migrate](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implementar o aparelho, descarregue e importe um modelo OVA em VMware para criar um servidor em execução no seu servidor vCenter. Depois de colocar o aparelho, é necessário registá-lo com o projeto e configurá-lo para iniciar a descoberta.
6. Ao configurar o aparelho, tem de especificar o seguinte no gestor de configuração do aparelho:
    - Os detalhes do servidor vCenter ao qual pretende ligar.
    - vCenter Server credenciais de mira para descobrir os servidores no seu ambiente VMware.
    - Credenciais de servidor, que podem ser de domínio/ Windows (não-domínio)/ Credenciais Linux (não domínio). [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as lidamos.

## <a name="verify-permissions"></a>Verificar permissões

- É necessário [criar uma conta de leitura do vCenter Server apenas](./tutorial-discover-vmware.md#prepare-vmware) para a descoberta e avaliação. A conta apenas de leitura necessita de privilégios habilitados para Operações **de Hóspedes de Máquinas Virtuais,**  >  de forma a interagir com os servidores para realizar o inventário de software.
- Pode adicionar várias credenciais de domínio e não domínio (Windows/Linux) no gestor de configuração do aparelho para a descoberta de aplicações. Precisa de uma conta de utilizador para servidores Windows e de uma conta de utilizador regular/normal (acesso não sudo) para todos os servidores Do Linux. [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as lidamos.

### <a name="add-credentials-and-initiate-discovery"></a>Adicione credenciais e inicie a descoberta

1. Abra o gestor de configuração do aparelho, complete as verificações e o registo do aparelho.
2. Navegue para o painel **de credenciais de Gestão e fontes de descoberta.**
1.  No **passo 1: Forneça credenciais do servidor vCenter**, clique em **Adicionar credenciais** para fornecer credenciais para a conta vCenter Server que o aparelho utilizará para descobrir servidores em execução no servidor vCenter.
1. No **passo 2: Fornecer detalhes do servidor vCenter**, clique na **fonte de descoberta adicionar** para selecionar o nome amigável para credenciais a partir do drop-down, especificar o endereço **IP/FQDN** do painel de identificação do servidor vCenter :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3 no gestor de configuração do aparelho para obter detalhes do servidor vCenter":::
1. No **Passo 3: Forneça credenciais de servidor para realizar inventário de software, análise de dependência de agente e descoberta de instâncias e bases de dados do SQL Server**, clique em Adicionar **credenciais** para fornecer várias credenciais de servidor para iniciar o inventário de software.
1. Clique em **Iniciar a descoberta**, para iniciar a descoberta do servidor vCenter.

 Após a conclusão do vCenter Server, o aparelho inicia a descoberta de aplicações, funções e funcionalidades instaladas (inventário de software). A duração depende do número de servidores descobertos. Para 500 servidores, leva aproximadamente uma hora para que o inventário descoberto apareça no portal Azure Migrate.

## <a name="review-and-export-the-inventory"></a>Rever e exportar o inventário

Após o inventário de software concluído, pode rever e exportar o inventário no portal Azure.

1. Em **Azure Migrate - Windows, Linux e SQL Servers**  >  **Azure Migrate: Discovery and assessment**, clique na contagem visualizada para abrir a página dos **servidores Descobertos.**

    > [!NOTE]
    > Nesta fase, pode opcionalmente também ativar a análise de dependência dos servidores descobertos, para que possa visualizar dependências em servidores que pretende avaliar. [Saiba mais](concepts-dependency-visualization.md) sobre a análise da dependência.

2. Na coluna **de inventário de software,** clique na contagem visualizada para rever as aplicações, funções e funcionalidades descobertas.
4. Para exportar o inventário, em **Servidores Descobertos,** clique no **inventário da aplicação Export**.

O inventário de software é exportado e descarregado no formato Excel. A folha **de Inventário de Software** exibe todas as aplicações descobertas em todos os servidores.

## <a name="discover-sql-server-instances-and-databases"></a>Descubra instâncias e bases de dados do SQL Server

- O inventário de software também identifica as instâncias do SQL Server em execução no seu ambiente VMware.
- Se não tiver fornecido credenciais de autenticação do Windows ou SQL Server no gestor de configuração do aparelho, adicione as credenciais para que o aparelho possa utilizá-las para ligar às respetivas instâncias do SQL Server.

Uma vez ligado, o aparelho recolhe dados de configuração e desempenho de instâncias e bases de dados do SQL Server. Os dados de configuração do SQL Server são atualizados uma vez a cada 24 horas e os dados de desempenho são capturados a cada 30 segundos. Assim, qualquer alteração às propriedades da instância do SQL Server e bases de dados, tais como estado da base de dados, nível de compatibilidade, etc. pode demorar até 24 horas para atualizar no portal.

## <a name="next-steps"></a>Passos seguintes

- [Crie uma avaliação](how-to-create-assessment.md) para servidores descobertos.
- [Avaliar os Servidores SQL](./tutorial-assess-sql.md) para a migração para a Azure SQL.
