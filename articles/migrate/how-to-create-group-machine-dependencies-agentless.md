---
title: Agrupar máquinas no Azure migrar usando a visualização de dependência sem agente
description: Descreve como criar grupos usando dependências de máquina de maneira sem agente.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: c8ddd343cd00b24506382521361ebad33ad112a7
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049763"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>Configurar a visualização de dependência sem agente para avaliação

Este artigo descreve como configurar o mapeamento de dependência sem agente nas migrações para Azure: avaliação do servidor. 

> [!IMPORTANT]
> A visualização de dependência sem agente está atualmente em versão prévia para VMs VMware do Azure descobertas usando um dispositivo de migrações para Azure.
> Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Essa visualização é coberta pelo atendimento ao cliente e pode ser usada para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="about-dependency-mapping"></a>Sobre mapeamento de dependência

O mapeamento de dependência ajuda a Visualizar dependências entre computadores que você deseja avaliar e migrar. Normalmente, você usa o mapeamento de dependência quando deseja avaliar computadores com níveis mais altos de confiança.

- Nas migrações para Azure: avaliação de servidor, você reúne computadores em grupos para avaliação. Os grupos geralmente consistem em computadores que você deseja migrar juntos, e o mapeamento de dependência ajuda a verificar dependências de máquina, para que você possa agrupar as máquinas com precisão.
- Usando o mapeamento, você pode descobrir sistemas interdependentes que precisam ser migrados juntos. Você também pode identificar se um sistema em execução ainda está atendendo aos usuários ou é um candidato para encerramento em vez de migração.
- A visualização de dependências ajuda a garantir que nada seja deixado para trás e evitar interrupções surpresa durante a migração.

## <a name="about-agentless-visualization"></a>Sobre a visualização sem agente

A visualização de dependência sem agente não exige a instalação de agentes em máquinas. Ele funciona capturando os dados de conexão TCP de computadores para os quais está habilitado.

- Depois que a descoberta de dependência é iniciada, o dispositivo reúne dados de computadores em um intervalo de sondagem de cinco minutos.
- Os seguintes dados são coletados:
    - Ligações TCP
    - Nomes de processos que têm conexões ativas
    - Nomes de aplicativos instalados que executam os processos acima
    - Não. de conexões detectadas em cada intervalo de sondagem

## <a name="current-limitations"></a>Limitações atuais

- A visualização de dependência sem agente está disponível atualmente somente para VMs VMware.
- Agora você não pode adicionar ou remover um servidor de um grupo, na exibição de análise de dependência.
- O mapa de dependências para um grupo de servidores não está disponível no momento.
- No momento, os dados de dependência não podem ser baixados no formato tabular.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de [ter criado](how-to-add-tool-first-time.md) um projeto Azure Migrate.
- Atualmente, a análise de dependência sem agente está disponível apenas para máquinas VMware.
- Se já criou um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta de avaliação do servidor Azure Migrate.
- Certifique-se de ter descoberto as suas máquinas VMware em Azure Migrate; pode fazê-lo instalando um aparelho Azure Migrate para [VMware](how-to-set-up-appliance-vmware.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para Azure: avaliação do servidor. [Saiba mais](migrate-appliance.md).
- [Reveja os requisitos](migrate-support-matrix-vmware.md#agentless-dependency-visualization) para a criação de uma visualização de dependência sem agente.



## <a name="create-a-user-account-for-discovery"></a>Criar uma conta de usuário para descoberta

Configure uma conta de usuário que tenha as permissões necessárias para que a avaliação do servidor possa acessar a VM para descoberta. Você pode especificar uma conta de usuário.

- **Autorização necessária nos VMs do Windows**: A conta de utilizador tem de ser um administrador local ou de domínio.
- **Autorização necessária sobre Os VMs Linux**: O privilégio de raiz é exigido na conta. Alternadamente, a conta de utilizador requer estas duas capacidades em ficheiros /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Adicionar a conta de usuário ao dispositivo

Você precisa adicionar a conta de usuário ao dispositivo.

Adicione a conta da seguinte maneira:

1. Abra o aplicativo de gerenciamento de dispositivo. Navegue para o painel de **detalhes Provide vCenter.**
2. Na **aplicação Discover e dependências na secção VMs,** clique em **Adicionar credenciais**
3. Escolha o **sistema operativo**.
4. Forneça um nome amigável para a conta.
5. Fornecer o nome de **utilizador** e **palavra-passe**
6. Clique em **Guardar**.
7. Clique em **Guardar e começar a descoberta**.

    ![Adicionar conta de usuário da VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Iniciar descoberta de dependência

Escolha os computadores nos quais você deseja habilitar a descoberta de dependência.

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Clique no ícone de análise da **Dependência.**
3. Clique em **adicionar servidores**.
3. Na página **adicionar servidores,** escolha o aparelho que está a descobrir as máquinas relevantes.
4. Na lista computador, selecione os computadores.
5. Clique em **adicionar servidores**.

    ![Iniciar descoberta de dependência](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Você poderá visualizar as dependências 6 horas depois de iniciar a descoberta de dependência.

## <a name="visualize-dependencies"></a>Visualizar dependências

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Pesquise o computador para o qual você deseja exibir o mapa de dependências.
3. Clique em **ver dependências** na coluna **Dependencies.**
4. Altere o período de tempo para o qual pretende visualizar o mapa utilizando a descida da duração da **hora.**
5. Expandir o grupo **Cliente** para listar as máquinas que têm uma dependência da máquina selecionada.
6. Expanda o grupo **Portuário** para listar as máquinas que têm uma dependência da máquina selecionada.
7. Para navegar para a vista do mapa de qualquer uma das máquinas dependentes, clique no nome da máquina e, em seguida, clique no mapa do **servidor load**

    ![Expandir o grupo de portas do servidor e carregar o mapa do servidor](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandir grupo de clientes ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expanda a máquina selecionada para exibir detalhes de nível de processo para cada dependência.

    ![Expanda servidor para mostrar processos](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> As informações do processo para uma dependência não estão sempre disponíveis. Se não estiver disponível, a dependência será representada com o processo marcado como "processo desconhecido".

## <a name="stop-dependency-discovery"></a>Parar descoberta de dependência

Escolha os computadores nos quais você deseja parar a descoberta de dependência.

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Clique no ícone de análise da **Dependência.**
3. Clique em **Remover servidores**.
3. Na página **remover servidores,** escolha o **aparelho** que está a descobrir os VMs em que procura impedir a descoberta da dependência.
4. Na lista computador, selecione os computadores.
5. Clique em **Remover servidores**.


## <a name="next-steps"></a>Passos seguintes

[Agrupar as máquinas](how-to-create-a-group.md)
