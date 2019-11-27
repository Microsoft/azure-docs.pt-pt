---
title: Gerir atualizações para várias máquinas virtuais do Azure
description: Este artigo descreve como gerenciar atualizações para máquinas virtuais do Azure e não Azure.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 11/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 70f4f4163a143354cd1fe5adf031c4d9cd87a46e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278659"
---
# <a name="manage-updates-for-multiple-machines"></a>Gerir atualizações de várias máquinas

Você pode usar a solução Gerenciamento de Atualizações para gerenciar atualizações e patches para suas máquinas virtuais Windows e Linux. Na sua conta da [Automatização do Azure](automation-offering-get-started.md), pode:

- Carregar máquinas virtuais
- Avaliar o status das atualizações disponíveis
- Agendar a instalação de atualizações necessárias
- Examine os resultados da implantação para verificar se as atualizações foram aplicadas com êxito a todas as máquinas virtuais para as quais o Gerenciamento de Atualizações está habilitado

## <a name="prerequisites"></a>Pré-requisitos

Para usar Gerenciamento de Atualizações, você precisa de:

- Uma máquina virtual ou um computador que tenha instalado um dos sistemas operativos suportados.

- Acesso a um repositório de atualizações para VMs do Linux integradas à solução.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Gerenciamento de Atualizações tem suporte nos seguintes sistemas operacionais:

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Dá suporte apenas a avaliações de atualização.         |
|Windows Server 2008 R2 SP1 e posterior     |O Windows PowerShell 4,0 ou posterior é necessário. ([Baixe o WMF 4,0](https://www.microsoft.com/download/details.aspx?id=40855))</br> O Windows PowerShell 5,1 é recomendado para maior confiabilidade. ([Baixe o WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) e 7 (x64)      | |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | |
|Ubuntu 14, 4 LTS, 16, 4 LTS e 18, 4 LTS (x86/x64)      | |

> [!NOTE]
> Para evitar que as atualizações sejam aplicadas fora de uma janela de manutenção no Ubuntu, reconfigure o pacote Unattended-Upgrade para desativar as atualizações automáticas. Para obter informações, veja [Automatic Updates topic in the Ubuntu Server Guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html) (Tópico de Atualizações Automáticas no Guia do Ubuntu Server).

## <a name="enable-update-management-for-azure-virtual-machines"></a>Habilitar Gerenciamento de Atualizações para máquinas virtuais do Azure

No portal do Azure, abra sua conta de automação e, em seguida, selecione **Gerenciamento de atualizações**.

Selecione **Adicionar VMs do Azure**.

![Adicionar guia de VM do Azure](./media/manage-update-multi/update-onboard-vm.png)

Selecione uma máquina virtual para carregar.

Em **habilitar gerenciamento de atualizações**, selecione **habilitar** para carregar a máquina virtual.

![Caixa de diálogo Ativar Gestão de Atualizações](./media/manage-update-multi/update-enable.png)

Quando a integração for concluída, Gerenciamento de Atualizações será habilitada para sua máquina virtual.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Habilitar Gerenciamento de Atualizações para máquinas virtuais e computadores não Azure

O agente de Log Analytics para Windows e Linux precisa ser instalado nas VMs que estão em execução na sua rede corporativa ou em outro ambiente de nuvem para habilitá-las com Gerenciamento de Atualizações. Para saber os requisitos do sistema e os métodos com suporte para implantar o agente em computadores hospedados fora do Azure, consulte [visão geral do agente de log Analytics](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Exibir computadores anexados à sua conta de automação

Depois de habilitar Gerenciamento de Atualizações para seus computadores, você poderá exibir as informações do computador selecionando **computadores**. Você pode ver informações sobre o *nome do computador*, o status de *conformidade*, o *ambiente*, o *tipo de sistema operacional*, *as atualizações críticas e de segurança instaladas*, *outras atualizações instaladas*e a *preparação do agente de atualização* para seus computadores.

  ![Separador Ver computadores](./media/manage-update-multi/update-computers-tab.png)

Os computadores que foram habilitados recentemente para Gerenciamento de Atualizações podem não ter sido avaliados ainda. O status do estado de conformidade para esses computadores **não é avaliado**. Aqui está uma lista de possíveis valores para o estado de conformidade:

- Em **conformidade**: computadores que não estão faltando atualizações críticas ou de segurança.

- **Não compatível**: computadores que não têm pelo menos uma atualização crítica ou de segurança.

- **Não avaliado**: os dados de avaliação de atualização não foram recebidos do computador no período de tempo esperado. Para computadores Linux, o tempo de espera esperado é na última hora. Para computadores com Windows, o período de tempo esperado é nas últimas 12 horas.

Para exibir o status do agente, selecione o link na coluna **prontidão do agente de atualização** . A seleção dessa opção abre o painel de **Hybrid Worker** e mostra o status do Hybrid Worker. A imagem a seguir mostra um exemplo de um agente que não foi conectado a Gerenciamento de Atualizações por um longo período de tempo:

![Separador Ver computadores](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Ver avaliações de atualizações

Depois de a Gestão de Atualizações ser ativada, o painel **Gestão de atualizações** abre. Pode ver uma lista de atualizações em falta no separador **Atualizações em falta**.

## <a name="collect-data"></a>Recolher dados

Os agentes instalados em máquinas virtuais e computadores coletam dados sobre atualizações. Os agentes enviam os dados para o Azure Gerenciamento de Atualizações.

### <a name="supported-agents"></a>Agentes suportados

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| Origem ligada | Suportadas | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes do Windows e, em seguida, inicia a instalação das atualizações necessárias. |
| Agentes do Linux |Sim |Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes do Linux e inicia a instalação de atualizações necessárias em distribuições com suporte. |
| Grupo de gestão do Operations Manager |Sim |Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectado. |
| Conta de armazenamento do Azure |Não |O armazenamento do Azure não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência da recolha

Depois que um computador conclui uma verificação de conformidade de atualização, o agente encaminha as informações em massa para Azure Monitor logs. Em um computador Windows, a verificação de conformidade é executada a cada 12 horas por padrão.

Além do agendamento da verificação, a verificação de conformidade da atualização é iniciada dentro de 15 minutos do MMA que está sendo reiniciado, antes da instalação da atualização e após a instalação da atualização.

Para um computador Linux, a verificação de conformidade é executada a cada hora por padrão. Se o agente MMA for reiniciado, uma verificação de conformidade será iniciada dentro de 15 minutos.

Pode levar entre 30 minutos e 6 horas para que o painel exiba dados atualizados de computadores gerenciados.

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implantação que se alinhe ao seu cronograma de liberação e à sua janela de serviço. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova implantação de atualização para uma ou mais máquinas virtuais, em **Gerenciamento de atualizações**, selecione **agendar implantação de atualização**.

No painel **nova implantação de atualização** , especifique as seguintes informações:

- **Nome**: Insira um nome exclusivo para identificar a implantação da atualização.
- **Sistema operacional**: selecione **Windows** ou **Linux**.
- **Grupos a serem atualizados**: defina uma consulta com base em uma combinação de assinatura, grupos de recursos, locais e marcas para criar um grupo dinâmico de VMs do Azure para incluir em sua implantação. Para VMs não Azure, as pesquisas salvas são usadas para criar um grupo dinâmico para incluir em sua implantação. Para saber mais, consulte [grupos dinâmicos](automation-update-management-groups.md).
- **Computadores a serem atualizados**: selecione uma pesquisa salva, um grupo importado ou selecione computadores para escolher os computadores que deseja atualizar.

   >[!NOTE]
   >A seleção da opção de pesquisa salva não retorna identidades de computador, apenas seus nomes. Se você tiver várias VMs com o mesmo nome em vários grupos de recursos, elas serão retornadas nos resultados. O uso da opção **grupos a serem atualizados** é recomendado para garantir que você inclua VMs exclusivas que correspondam aos seus critérios.

   Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**. Você pode ver o estado de integridade do computador antes de agendar a implantação da atualização. Para saber mais sobre os diferentes métodos de criação de grupos de computadores em logs de Azure Monitor, consulte [grupos de computadores em logs de Azure monitor](../azure-monitor/platform/computer-groups.md)

  ![Novo painel de implantação de atualização](./media/manage-update-multi/update-select-computers.png)

- **Classificação de atualização**: selecione os tipos de software a serem incluídos na implantação da atualização. Para obter uma descrição dos tipos de classificação, consulte [Atualizar classificações](automation-view-update-assessments.md#update-classifications). Os tipos de classificação são:
  - Atualizações críticas
  - Atualizações de segurança
  - Update rollups
  - Pacotes de funcionalidades
  - Service packs
  - Atualizações de definições
  - Ferramentas
  - Atualizações

- **Atualizações a incluir/excluir**: esta opção abre a página **Incluir/Excluir**. As atualizações a serem incluídas ou excluídas estão em separadores diferentes. Para obter informações adicionais sobre como a inclusão é tratada, consulte [agendar uma implantação de atualização](automation-tutorial-update-management.md#schedule-an-update-deployment).

- **Definições da agenda**: pode aceitar a data e hora predefinidas, que é 30 minutos após a hora atual. Você também pode especificar uma hora diferente.

   Também pode especificar se a implementação ocorre uma vez ou de acordo com um agendamento periódico. Para configurar um agendamento recorrente, em **recorrência**, selecione **recorrente**.

   ![Caixa de diálogo Definições de Agendamento](./media/manage-update-multi/update-set-schedule.png)

- **Scripts prévios + Scripts posteriores**: selecione os scripts para executar antes e após a sua implementação. Para saber mais, veja [Gerir Scripts prévios e posteriores](pre-post-scripts.md).
- **Janela de manutenção (minutos)** : especifique o período de tempo em que você deseja que a implantação de atualização ocorra. Esta definição ajuda a garantir que as alterações são realizadas nos seus períodos de administração definidos.

- **Controle de reinicialização** – essa configuração determina como as reinicializações são tratadas para a implantação da atualização.

   |Opção|Descrição|
   |---|---|
   |Reinicializar se necessário| **(Padrão)** Se necessário, uma reinicialização será iniciada se a janela de manutenção permitir.|
   |Reiniciar sempre|Uma reinicialização é iniciada independentemente de ser necessária. |
   |Nunca reiniciar|Independentemente de uma reinicialização ser necessária, as reinicializações são suprimidas.|
   |Reiniciar apenas - não irá instalar atualizações|Essa opção ignora a instalação de atualizações e inicia apenas uma reinicialização.|

Quando terminar de configurar a agenda, selecione o botão **criar** para retornar ao painel de status. A tabela **agendada** mostra a agenda de implantação que você criou.

> [!NOTE]
> A Gestão de Atualizações suporta a implementação de atualizações da primeira parte e a transferência prévia de patches. Isso requer alterações nos sistemas que estão sendo corrigidos, consulte [suporte de primeira parte e pré-download](automation-configure-windows-update.md#pre-download-updates) para saber como definir essas configurações em seus sistemas.

## <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualização** em **Gestão de atualizações**.

Se a implementação estiver em execução, o estado será **Em curso**. Depois que a implantação for concluída com êxito, o status será alterado para **êxito**.

Se uma ou mais atualizações falharem na implementação, o estado é **Falha parcial**.

![Estado da implementação de atualizações](./media/manage-update-multi/update-view-results.png)

Para ver o dashboard relativo a uma implementação de atualizações, selecione a implementação concluída.

O painel **resultados da atualização** mostra o número total de atualizações e os resultados da implantação para a máquina virtual. A tabela à direita fornece uma análise detalhada de cada atualização e os resultados da instalação. Os resultados da instalação podem ser um dos seguintes valores:

- **Sem tentativa**: a atualização não foi instalada porque o tempo insuficiente estava disponível com base na janela de manutenção definida.
- **Com êxito**: a atualização foi executada com êxito.
- **Falhou**: a atualização falhou.

Para ver todas as entradas de registo que a implementação criou, selecione **Todos os registos**.

Para ver o fluxo de trabalho do runbook que gerencia a implantação de atualização na máquina virtual de destino, selecione o bloco saída.

Para ver informações detalhadas sobre os erros da implementação, selecione **Erros**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Gerenciamento de Atualizações, incluindo logs, saída e erros, confira [Gerenciamento de atualizações solução no Azure](../operations-management-suite/oms-solution-update-management.md).

