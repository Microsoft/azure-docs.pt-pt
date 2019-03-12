---
title: Gerir atualizações para várias máquinas virtuais do Azure
description: Este artigo descreve como gerir atualizações para máquinas virtuais do Azure.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 01/10/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 65ea01047743c5894ac2ae8b38a197b57cb6971c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531324"
---
# <a name="manage-updates-for-multiple-machines"></a>Gerir atualizações de várias máquinas

Pode utilizar a solução de gestão de atualizações para gerir atualizações e correções para as suas máquinas de virtuais do Windows e Linux. Na sua conta da [Automatização do Azure](automation-offering-get-started.md), pode:

- Adicionar máquinas virtuais
- Avaliar o estado das atualizações disponíveis
- Agendar a instalação de atualizações necessárias
- Reveja os resultados de implementação para verificar se as atualizações foram aplicadas com êxito para todas as máquinas virtuais para as quais a gestão de atualizações está ativada

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a gestão de atualizações, terá de:

- Uma conta Run As de Automatização. Para saber como criar um, veja [introdução à automatização do Azure](automation-offering-get-started.md).
- Uma máquina virtual ou um computador que tenha instalado um dos sistemas operativos suportados.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Gestão de atualizações é suportada nos seguintes sistemas operativos:

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Suporta apenas avaliações de atualização.         |
|Windows Server 2008 R2 SP1 e posterior     |É necessário o Windows PowerShell 4.0 ou posterior. ([Transferir WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1 é recomendada para maior confiabilidade. ([Transferir WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

> [!NOTE]
> Para evitar que as atualizações sejam aplicadas fora de uma janela de manutenção no Ubuntu, reconfigure o pacote Unattended-Upgrade para desativar as atualizações automáticas. Para obter informações, veja [Automatic Updates topic in the Ubuntu Server Guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html) (Tópico de Atualizações Automáticas no Guia do Ubuntu Server).

Os agentes do Linux têm de ter acesso a um repositório de atualização.

Esta solução não suporta um agente do Log Analytics para Linux que está configurado para reportar a várias áreas de trabalho do Log Analytics do Azure.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Ativar a gestão de atualizações para máquinas virtuais do Azure

No portal do Azure, abra a sua conta de automatização e, em seguida, selecione **gestão de atualizações**.

Selecione **adicionar VMs do Azure**.

![Adicionar guia de VM do Azure](./media/manage-update-multi/update-onboard-vm.png)

Selecione uma máquina virtual para carregar. 

Sob **ativar gestão de atualizações**, selecione **ativar** para carregar a máquina virtual.

![Caixa de diálogo Ativar Gestão de Atualizações](./media/manage-update-multi/update-enable.png)

Quando a integração é concluída, a gestão de atualizações está ativada para a máquina virtual.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Ativar a gestão de atualizações para computadores e máquinas de virtuais não pertencente ao Azure

Para saber como ativar a gestão de atualizações para computadores e máquinas de virtuais do Windows não Azure, veja [computadores Windows ligar ao serviço Azure Monitor no Azure](../log-analytics/log-analytics-windows-agent.md).

Para saber como ativar a gestão de atualizações para computadores e máquinas de virtuais de Linux não Azure, veja [ligar os computadores Linux para registos do Azure Monitor](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Ver computadores ligados à sua conta de automatização

Depois de ativar a gestão de atualizações para as suas máquinas, pode ver informações da máquina, selecionando **computadores**. Pode ver informações *nome da máquina*, *estado de conformidade*, *ambiente*, *tipo de SO*, *críticos e atualizações de segurança instaladas*, *outras atualizações instaladas*, e *atualizar preparação do agente* para os seus computadores.

  ![Separador Ver computadores](./media/manage-update-multi/update-computers-tab.png)

Computadores que tenham sido ativadas recentemente para gerenciamento de atualizações podem não ter sido avaliados ainda. O estado do Estado de conformidade para esses computadores é **não avaliados**. Aqui está uma lista de valores possíveis para o estado de conformidade:

- **Em conformidade**: Computadores que não tem em falta crítica ou de atualizações de segurança.

- **Não compatível**: Computadores que estão em falta, pelo menos, uma crítica ou de atualização de segurança.

- **Não avaliado**: Os dados da avaliação de atualização ainda não foram recebidos do computador dentr do período de tempo esperado. Para computadores Linux, o período de tempo esperada é nas últimas 3 horas. Para computadores Windows, o período de tempo esperado é nas últimas 12 horas.

Para ver o estado do agente, selecione a ligação na **preparação do agente de ATUALIZAÇÃO** coluna. A seleção desta opção abre o **função de trabalho híbrida** painel e mostra o estado da função de trabalho híbrida. A imagem seguinte mostra um exemplo de um agente que ainda não foi ligado ao gerenciamento de atualizações por um longo período de tempo:

![Separador Ver computadores](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Ver avaliações de atualizações

Depois de a Gestão de Atualizações ser ativada, o painel **Gestão de atualizações** abre. Pode ver uma lista de atualizações em falta no separador **Atualizações em falta**.

## <a name="collect-data"></a>Recolher dados

Agentes instalados nas máquinas virtuais e computadores recolher dados sobre as atualizações. Os agentes de enviam os dados para a gestão de atualizações do Azure.

### <a name="supported-agents"></a>Agentes suportados

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| Origem ligada | Suportadas | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |Gestão de atualizações recolhe informações sobre atualizações do sistema de agentes do Windows e, em seguida, inicia a instalação das atualizações necessárias. |
| Agentes do Linux |Sim |Gestão de atualizações recolhe informações sobre atualizações do sistema de agentes do Linux e, em seguida, inicia a instalação das atualizações obrigatórias em distribuições suportadas. |
| Grupo de gestão do Operations Manager |Sim |Gestão de atualizações recolhe informações sobre atualizações do sistema de agentes num grupo de gestão ligado. |
| Conta de armazenamento do Azure |Não |O armazenamento do Azure não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência da recolha

Depois de um computador realiza uma análise de conformidade de atualização, o agente reencaminha as informações em massa para os registos do Azure Monitor. Num computador Windows, a análise de conformidade é executada a cada 12 horas por predefinição.

Além do agendamento da análise, a análise da compatibilidade de atualização é iniciada dentro de 15 minutos do MMA ser reiniciado, antes da instalação da atualização e após a instalação de atualização.

Para um computador Linux, a análise de conformidade é realizada em três horas por predefinição. Se o agente MMA ser reiniciado, é iniciada uma análise de conformidade em 15 minutos.

Pode demorar entre 30 minutos e 6 horas para o dashboard apresentar os dados atualizados dos computadores gerenciados.

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que se alinha com a janela de agendamento e o serviço de versão. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova implementação de atualização para uma ou mais máquinas virtuais, em **gestão de atualizações**, selecione **agendar a implementação da atualização**.

Na **nova implementação de atualização** painel, especifique as seguintes informações:

- **Nome**: Introduza um nome exclusivo para identificar a implementação de atualização.
- **Sistema operativo**: Selecione **Windows** ou **Linux**.
- **Grupos de atualização (pré-visualização)**: Defina uma consulta com base numa combinação de subscrição, grupos de recursos, localizações e as etiquetas para criar um grupo dinâmico de VMs do Azure para incluir na sua implementação. Para saber mais, veja [Grupos Dinâmicos](automation-update-management.md#using-dynamic-groups)
- **Computadores a atualizar**: Selecione uma pesquisa guardada, grupo importada, ou máquinas, para as máquinas que pretende atualizar. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**. Pode ver o estado de funcionamento da máquina antes de agendar a implementação da atualização. Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos registos do Azure Monitor, consulte o artigo [grupos de computadores nos registos do Azure Monitor](../azure-monitor/platform/computer-groups.md)

  ![Novo painel de implementação de atualização](./media/manage-update-multi/update-select-computers.png)

- **Classificação da atualização**: Selecione os tipos de software para incluir na implementação de atualização. Para obter uma descrição dos tipos de classificação, consulte [classificações de atualizações](automation-update-management.md#update-classifications). Os tipos de classificação são:
  - Atualizações críticas
  - Atualizações de segurança
  - Update rollups
  - Pacotes de funcionalidades
  - Service packs
  - Atualizações de definições
  - Ferramentas
  - Atualizações

- **Atualizações a incluir/excluir**: esta opção abre a página **Incluir/Excluir**. As atualizações a serem incluídas ou excluídas estão em separadores diferentes. Para obter informações adicionais sobre como é processada a inclusão, veja [comportamento de inclusão](automation-update-management.md#inclusion-behavior).

- **Definições da agenda**: Pode aceitar a data e hora predefinidas, que é 30 minutos depois da hora atual. Também pode especificar uma hora diferente.

   Também pode especificar se a implementação ocorre uma vez ou de acordo com um agendamento periódico. Para configurar um agendamento periódico, em **periodicidade**, selecione **periódico**.

   ![Caixa de diálogo Definições de Agendamento](./media/manage-update-multi/update-set-schedule.png)

- **Pré- scripts de + pós-scripts de**: Selecione os scripts sejam executados antes e após a sua implementação. Para saber mais, veja [Gerir Scripts prévios e posteriores](pre-post-scripts.md).
- **Janela de manutenção (minutos)**: Especifique o período de tempo que pretende que a implementação da atualização ocorra. Esta definição ajuda a garantir que as alterações são realizadas nos seus períodos de administração definidos.

- **Controlo de reinício** -esta definição determina a forma como os reinícios são processados para a implementação da atualização.

   |Opção|Descrição|
   |---|---|
   |Se for necessário o reinício| **(Predefinição)**  Se for necessário, uma reinicialização é iniciada, se permitir a janela de manutenção.|
   |Reiniciar sempre|Uma reinicialização é iniciada, independentemente se for requerida. |
   |Nunca reiniciar|Independentemente de se uma reinicialização é necessária, reinicializações são suprimidas.|
   |Reiniciar apenas - não irá instalar atualizações|Esta opção ignora a instalação de atualizações e apenas inicia uma reinicialização.|

Quando tiver terminado de configurar a agenda, selecione o **criar** botão para regressar ao dashboard de estado. O **agendada** tabela mostra o agendamento da implementação que criou.

> [!NOTE]
> A Gestão de Atualizações suporta a implementação de atualizações da primeira parte e a transferência prévia de patches. Isso requer alterações nos sistemas que está a ser corrigidos, consulte [primeira parte e pré transferir suporte](automation-update-management.md#firstparty-predownload) para aprender a configurar estas definições em seus sistemas.

## <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualização** em **Gestão de atualizações**.

Se a implementação estiver em execução, o estado será **Em curso**. Após a conclusão da implementação com êxito, o estado muda para **bem-sucedido**.

Se uma ou mais atualizações falharem na implementação, o estado é **Falha parcial**.

![Estado da implementação de atualizações](./media/manage-update-multi/update-view-results.png)

Para ver o dashboard relativo a uma implementação de atualizações, selecione a implementação concluída.

O **resultados da atualização** painel mostra o número total de atualizações e os resultados de implementação para a máquina virtual. A tabela à direita mostra uma divisão detalhada de cada atualização e os resultados da instalação. Os resultados da instalação podem ser um dos seguintes valores:

- **Não tentada**: A atualização não foi instalada porque não havia tempo suficiente disponível com base na janela de manutenção definida.
- **Foi efetuada com êxito**: A atualização foi concluída com êxito.
- **Falha ao**: A atualização falhou.

Para ver todas as entradas de registo que a implementação criou, selecione **Todos os registos**.

Para ver o fluxo de trabalho do runbook que gere a implementação da atualização na máquina virtual de destino, selecione o mosaico de saída.

Para ver informações detalhadas sobre os erros da implementação, selecione **Erros**.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre o gerenciamento de atualizações, incluindo registos, saída e erros, veja [solução de gestão de atualizações no Azure](../operations-management-suite/oms-solution-update-management.md).

