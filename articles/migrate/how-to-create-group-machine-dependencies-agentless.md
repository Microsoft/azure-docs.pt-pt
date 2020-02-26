---
title: Cria ção de visualização de dependência sem agente em Azure Migrate
description: Configurar grupos utilizando visualização da dependência sem agente na Avaliação do Servidor Migratório Azure.
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: c9425ad1fa78f14a194d3fe13c259dadf4eb5eb6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589135"
---
# <a name="set-up-agentless-dependency-visualization"></a>Configurar a visualização da dependência sem agente 

Este artigo descreve como configurar a visualização da dependência em Azure Migrate:Server Assessment. [A visualização da dependência](concepts-dependency-visualization.md#what-is-dependency-visualization) ajuda-o a identificar e compreender dependências entre máquinas que pretende avaliar e migrar para Azure.

A visualização da dependência sem agente ajuda-o a identificar dependências da máquina sem instalar agentes em máquinas. Funciona capturando os dados de ligação TCP a partir de máquinas para as quais está ativado.

> [!IMPORTANT]
> A visualização da dependência sem agente está atualmente em pré-visualização apenas para VMs Azure VMware, descoberto com a ferramenta De avaliação de migração Azure:Server.
> As funcionalidades podem ser limitadas ou incompletas.
> Esta pré-visualização é coberta pelo apoio ao cliente e pode ser usada para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Limitações atuais

- Neste momento não pode adicionar ou remover um servidor de um grupo, na visão de análise de dependência.
- Um mapa de dependência para um grupo de servidores não está disponível atualmente.
- Atualmente, os dados de dependência não podem ser descarregados em formato tabular.

## <a name="before-you-start"></a>Antes de começar

- [Reveja](concepts-dependency-visualization.md#agentless-visualization) os requisitos e custos associados à visualização da dependência sem agente.
- Reveja os [requisitos de suporte](migrate-support-matrix-vmware.md#agentless-dependency-visualization) para a criação de uma visualização de dependência sem agente.
- Certifique-se de [ter criado](how-to-add-tool-first-time.md) um projeto Azure Migrate.
- Se já criou um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta de avaliação do servidor Azure Migrate:Server.
- Certifique-se de que montou um [aparelho Azure Migrate](migrate-appliance.md) para descobrir as suas máquinas no local. Aprenda a configurar um aparelho para [VMware](how-to-set-up-appliance-vmware.md) VMs. O aparelho descobre máquinas no local e envia dados de metadados e desempenho para a Avaliação do Servidor Azure Migrate.Server.


## <a name="create-a-user-account-for-discovery"></a>Criar uma conta de utilizador para descoberta

Configurar uma conta de utilizador para que a Avaliação do Servidor possa aceder ao VM para ser descoberta. Pode especificar uma conta de utilizador.

- **VMs do Windows**: A conta de utilizador tem de ser um administrador local ou de domínio.
- **VMs Linux**: O privilégio de raiz é exigido na conta. Alternadamente, a conta de utilizador requer estas duas capacidades em ficheiros /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Adicione a conta do utilizador ao aparelho

Adicione a conta de utilizador ao aparelho.

1. Abra a aplicação de gestão de aparelhos. 
2. Navegue para o painel de **detalhes Provide vCenter.**
3. Em **Discover aplicação e dependências em VMs,** clique em **Adicionar credenciais**
3. Escolha o **sistema operativo**, forneça um nome amigável para a conta e o nome **de utilizador**/**Palavra-passe**
6. Clique em **Guardar**.
7. Clique em **Guardar e começar a descoberta**.

    ![Adicionar conta de utilizador VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Iniciar a descoberta da dependência

Escolha as máquinas nas quais pretende permitir a descoberta da dependência.

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Clique no ícone de análise da **Dependência.**
3. Clique em **adicionar servidores**.
3. Na página **adicionar servidores,** escolha o aparelho que está a descobrir as máquinas relevantes.
4. Na lista de máquinas, selecione as máquinas.
5. Clique em **adicionar servidores**.

    ![Iniciar a descoberta da dependência](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Pode visualizar dependências cerca de seis horas após iniciar a descoberta da dependência.

## <a name="visualize-dependencies"></a>Visualizar dependências

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Procure a máquina que pretende ver.
3. Na coluna **Dependências,** clique em **Ver dependências**
4. Altere o período de tempo para o qual pretende visualizar o mapa utilizando a descida da duração da **hora.**
5. Expandir o grupo **Cliente** para listar as máquinas com uma dependência da máquina selecionada.
6. Expanda o grupo **Portuário** para listar as máquinas que têm uma dependência da máquina selecionada.
7. Para navegar para a vista do mapa de qualquer uma das máquinas dependentes, clique no nome da máquina > carregar o mapa do **servidor**

    ![Expandir o grupo de porta do Servidor e o mapa do servidor de carga](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandir grupo de clientes ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expanda a máquina selecionada para visualizar detalhes de nível de processo para cada dependência.

    ![Expandir o servidor para mostrar processos](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> A informação do processo para uma dependência nem sempre está disponível. Se não estiver disponível, a dependência é retratada com o processo marcado como "processo desconhecido".

## <a name="stop-dependency-discovery"></a>Parar a descoberta da dependência

Escolha as máquinas nas quais pretende parar a descoberta da dependência.

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Clique no ícone de análise da **Dependência.**
3. Clique em **Remover servidores**.
3. Na página **remover servidores,** escolha o **aparelho** que está a descobrir os VMs em que procura impedir a descoberta da dependência.
4. Na lista de máquinas, selecione as máquinas.
5. Clique em **Remover servidores**.


## <a name="next-steps"></a>Passos seguintes

[Agrupar as máquinas](how-to-create-a-group.md) para avaliação.
