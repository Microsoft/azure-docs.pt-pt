---
title: Agrupar máquinas no Azure migrar usando a visualização de dependência sem agente
description: Descreve como criar grupos usando dependências de máquina de maneira sem agente.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: b13ad375adf20a606b48755a6d57851abf89e549
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454662"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>Configurar a visualização de dependência sem agente para avaliação

Este artigo descreve como configurar o mapeamento de dependência sem agente nas migrações para Azure: avaliação do servidor. Esse recurso está atualmente disponível em versão prévia para máquinas VMware descobertas usando um dispositivo de migrações para Azure. 

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

- Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto de migrações para Azure.
- Atualmente, a análise de dependência sem agente está disponível apenas para máquinas VMware.
- Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta migrações do Azure: Server Assessment.
- Verifique se você descobriu suas máquinas VMware nas migrações para Azure; Você pode fazer isso Configurando um dispositivo de migrações para Azure para [VMware](how-to-set-up-appliance-vmware.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para Azure: avaliação do servidor. [Saiba mais](migrate-appliance.md).
- Verifique se as VMs VMware têm suporte para a visualização de dependência sem agente, conforme resumido na tabela a seguir.


### <a name="supported-operating-systems"></a>Sistemas operativos suportados

Os sistemas operacionais com suporte para a visualização de dependência sem agente são os seguintes.

**Tipo** | **Sistemas operativos suportados**
--- | ---
**Windows** | Microsoft Windows Server 2016 <br/> Microsoft Windows Server 2012 R2 <br/> Microsoft Windows Server 2012 <br/> Microsoft Windows Server 2008 R2 (64 bits)
**Linux** | Red Hat Enterprise Linux 7, 6, 5 <br/> Ubuntu Linux 14, 4, 16, 4 <br/> Debian 7, 8 <br/> Oracle Linux 6, 7 <br/> CentOS 5, 6, 7  


## <a name="create-a-user-account-for-discovery"></a>Criar uma conta de usuário para descoberta

Configure uma conta de usuário que tenha as permissões necessárias para que a avaliação do servidor possa acessar a VM para descoberta. Você pode especificar uma conta de usuário.

- **Permissão necessária em VMs do Windows**: a conta de usuário requer acesso "convidado".
- **Permissão necessária em VMs do Linux**: o privilégio raiz é necessário na conta. Como alternativa, a conta de usuário requer esses dois recursos em arquivos/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Adicionar a conta de usuário ao dispositivo

Você precisa adicionar a conta de usuário ao dispositivo.

Adicione a conta da seguinte maneira:

1. Abra o aplicativo de gerenciamento de dispositivo. Navegue até o painel **fornecer detalhes do vCenter** .
2. Na seção **descobrir aplicativo e dependências nas VMs** , clique em **Adicionar credenciais**
3. Escolha o **sistema operacional**.
4. Forneça um nome amigável para a conta.
5. Forneça o **nome de usuário** e a **senha**
6. Clique em **Guardar**.
7. Clique em **salvar e iniciar descoberta**.

    ![Adicionar conta de usuário da VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Iniciar descoberta de dependência

Escolha os computadores nos quais você deseja habilitar a descoberta de dependência.

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Clique no ícone **análise de dependência** .
3. Clique em **Iniciar descoberta de dependência**.
3. Na página **Iniciar descoberta de dependências** , escolha o dispositivo que está descobrindo os computadores relevantes.
4. Na lista computador, selecione os computadores.
5. Clique em **Iniciar descoberta de dependência**.

    ![Iniciar descoberta de dependência](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Você poderá visualizar as dependências 6 horas depois de iniciar a descoberta de dependência.

## <a name="visualize-dependencies"></a>Visualize dependências

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Pesquise o computador para o qual você deseja exibir o mapa de dependências.
3. Clique em **Exibir dependências** na coluna **dependências** .
4. Altere o período de tempo para o qual você deseja exibir o mapa usando a lista suspensa **duração de tempo** .
5. Expanda o grupo de **clientes** para listar os computadores que têm uma dependência no computador selecionado.
6. Expanda o grupo de **portas** para listar os computadores que têm uma dependência do computador selecionado.
7. Para navegar até o modo de exibição de mapa de qualquer um dos computadores dependentes, clique no nome do computador e, em seguida, clique em **carregar mapa do servidor**

    ![Expandir o grupo de portas do servidor e carregar o mapa do servidor](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandir grupo de clientes ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expanda a máquina selecionada para exibir detalhes de nível de processo para cada dependência.

    ![Expanda servidor para mostrar processos](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> As informações do processo para uma dependência não estão sempre disponíveis. Se não estiver disponível, a dependência será representada com o processo marcado como "processo desconhecido".

## <a name="stop-dependency-discovery"></a>Parar descoberta de dependência

Escolha os computadores nos quais você deseja parar a descoberta de dependência.

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Clique no ícone **análise de dependência** .
3. Clique em **parar descoberta de dependência**.
3. Na página **parar descoberta de dependências** , escolha o **dispositivo** que está descobrindo as VMs nas quais você procura parar a descoberta de dependência.
4. Na lista computador, selecione os computadores.
5. Clique em **parar descoberta de dependência**


## <a name="next-steps"></a>Passos seguintes

[Agrupar os computadores](how-to-create-a-group.md)
