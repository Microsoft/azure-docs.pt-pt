---
title: Gerir atualizações para várias máquinas virtuais do Azure
description: Este artigo descreve como gerir atualizações para máquinas virtuais Azure e não-Azure.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: c9a3c88ea0c3e656adf0f8c514b418cfc07c9590
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335778"
---
# <a name="manage-updates-for-multiple-machines"></a>Gerir atualizações de várias máquinas

Pode utilizar a solução Update Management para gerir atualizações e patches para as suas máquinas virtuais Windows e Linux. Na sua conta da [Automatização do Azure](automation-offering-get-started.md), pode:

- Máquinas virtuais a bordo
- Avaliar o estado das atualizações disponíveis
- Agendar instalação de atualizações necessárias
- Rever os resultados da implementação para verificar se as atualizações foram aplicadas com sucesso a todas as máquinas virtuais para as quais a Atualização de Gestão está ativada

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a Gestão de Atualizações, precisa de:

- Uma máquina virtual ou um computador que tenha instalado um dos sistemas operativos suportados.

- Acesso a um repositório de atualização para VMs Linux a bordo da solução.

Para conhecer os requisitos do sistema para a Gestão de Atualizações, consulte os [requisitos do cliente](automation-update-management.md#clients)da Atualização Management .

## <a name="enable-update-management-for-azure-virtual-machines"></a>Ativar a Gestão de Atualizações para máquinas virtuais Azure

No portal Azure, abra a sua conta De automação e, em seguida, selecione **a gestão de Atualização.**

**Selecione Adicionar VMs Azure**.

![Separador Adicionar VM do Azure](./media/manage-update-multi/update-onboard-vm.png)

Selecione uma máquina virtual para carregar.

Em **'Ativar Gestão de Actualizações',** selecione **Ativar** a bordo da máquina virtual.

![Caixa de diálogo Ativar Gestão de Atualizações](./media/manage-update-multi/update-enable.png)

Quando o embarque estiver terminado, a Atualização Management está ativada para a sua máquina virtual.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Ativar a Gestão de Atualizações para máquinas e computadores virtuais não-Azure

O agente Log Analytics para Windows e Linux precisa de ser instalado nos VMs que estão a funcionar na sua rede corporativa ou noutro ambiente de nuvem, de forma a capacitá-los com a Gestão de Atualizações. Para aprender os requisitos do sistema e métodos suportados para implantar o agente em máquinas alojadas fora de Azure, consulte a [visão geral do agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Ver computadores anexados à sua conta Deautomação

Depois de ativar a Atualização da Gestão das suas máquinas, pode visualizar informações da máquina selecionando **Computadores**. Pode ver informações sobre *o nome da máquina,* estado de *conformidade,* *ambiente,* *tipo OS,* *atualizações críticas e de segurança instaladas,* *outras atualizações instaladas*e disponibilidade do agente de *atualização* para os seus computadores.

  ![Separador Ver computadores](./media/manage-update-multi/update-computers-tab.png)

Os computadores que foram recentemente ativados para a Gestão de Atualizações podem ainda não ter sido avaliados. O estado de conformidade desses computadores não é **avaliado.** Aqui está uma lista de possíveis valores para o estado de conformidade:

- **Conformidade**: Computadores que não faltam atualizações críticas ou de segurança.

- **Não conforme**: Computadores que faltam pelo menos uma atualização crítica ou de segurança.

- **Não avaliados**: Os dados de avaliação da atualização não foram recebidos do computador dentro do prazo previsto. Para os computadores Linux, o prazo de espera é na última hora. Para computadores Windows, o prazo esperado é nas últimas 12 horas.

Para ver o estado do agente, selecione o link na coluna de prontidão do **agente Atualizar.** A seleção desta opção abre o painel **Híbrido Trabalhador** e mostra o estado do Trabalhador Híbrido. A imagem que se segue mostra um exemplo de um agente que não foi ligado à Atualização por um longo período de tempo:

![Separador Ver computadores](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Ver avaliações de atualizações

Depois de a Gestão de Atualizações ser ativada, o painel **Gestão de atualizações** abre. Pode ver uma lista de atualizações em falta no separador **Atualizações em falta**.

## <a name="collect-data"></a>Recolher dados

Os agentes que estão instalados em máquinas virtuais e computadores recolhem dados sobre atualizações. Os agentes enviam os dados para a Azure Update Management.

### <a name="supported-agents"></a>Agentes suportados

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| Origem ligada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A Update Management recolhe informações sobre atualizações do sistema a partir de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Sim |A Update Management recolhe informações sobre atualizações do sistema a partir de agentes Linux e inicia a instalação de atualizações necessárias sobre distribuições suportadas. |
| Grupo de gestão do Operations Manager |Sim |A Update Management recolhe informações sobre atualizações do sistema de agentes de um grupo de gestão conectado. |
| conta de Armazenamento do Azure |Não |O Azure Storage não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência da recolha

Depois de um computador completar uma varredura para a conformidade da atualização, o agente reencaminha as informações a granel para os registos do Monitor Azure. Num computador Windows, a varredura de conformidade é executada a cada 12 horas por padrão.

Além do calendário de digitalização, a varredura para a conformidade da atualização é iniciada dentro de 15 minutos após o reinício do MMA, antes da instalação da atualização e após a instalação da atualização.

Para um computador Linux, a varredura de conformidade é realizada de hora a hora por padrão. Se o agente MMA for reiniciado, é iniciado um exame de conformidade dentro de 15 minutos.

O dashboard pode demorar entre 30 minutos a 6 horas a apresentar os dados atualizados a partir dos computadores geridos.

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que se alinha com o seu horário de lançamento e janela de serviço. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

>[!NOTE]
>Ao agendar uma implementação de atualização, cria um recurso de [agenda](shared-resources/schedules.md) ligado ao livro de execução **Patch-MicrosoftOMSComputers** que trata da implementação da atualização nas máquinas-alvo. Se eliminar o recurso de agenda do portal Azure ou utilizar o PowerShell após a criação da implementação, quebra a implementação da atualização programada e apresenta um erro ao tentar reconfigurá-lo a partir do portal. Só é possível eliminar o recurso de agenda eliminando o calendário de implementação correspondente.
>

Para agendar uma nova atualização para uma ou mais máquinas virtuais, sob gestão de **Atualização,** selecione a **atualização**do Schedule .

No painel de implementação da **nova atualização,** especifique as seguintes informações:

- **Nome**: Introduza um nome único para identificar a implementação da atualização.
- **Sistema operativo**: Selecione **Windows** ou **Linux**.
- **Grupos para atualizar**: Defina uma consulta com base numa combinação de subscrição, grupos de recursos, localizações e etiquetas para construir um grupo dinâmico de VMs Azure para incluir na sua implementação. Para VMs não-Azure, pesquisas guardadas são usadas para criar um grupo dinâmico para incluir na sua implementação. Para saber mais, [Grupos Dinâmicos.](automation-update-management-groups.md)
- **Máquinas para atualizar**: Selecione um Save Search, Grupo Importado ou selecione Máquinas, para escolher as máquinas que pretende atualizar.

   >[!NOTE]
   >A seleção da opção Saved Search não devolve as identidades da máquina, apenas os seus nomes. Se tiver vários VMs com o mesmo nome em vários grupos de recursos, estes são devolvidos nos resultados. Recomenda-se a utilização da opção **De atualização de Grupos** para garantir que inclui VMs únicos que correspondam aos seus critérios.

   Se escolher **Máquinas,** a prontidão da máquina é mostrada na coluna de prontidão do **agente Atualizar.** Pode ver o estado de saúde da máquina antes de agendar a implementação da atualização. Para conhecer os diferentes métodos de criação de grupos informáticos nos registos do Monitor Azure, consulte [grupos informáticos em registos do Monitor Azure](../azure-monitor/platform/computer-groups.md)

  ![Novo painel de implementação de atualização](./media/manage-update-multi/update-select-computers.png)

- **Classificação da atualização**: Selecione os tipos de software a incluir na implementação da atualização. Para uma descrição dos tipos de classificação, consulte classificações de [atualização](automation-view-update-assessments.md#update-classifications). Os tipos de classificação são:
  - Atualizações críticas
  - Atualizações de segurança
  - Update rollups
  - Pacotes de funcionalidades
  - Service packs
  - Atualizações de definições
  - Ferramentas
  - Atualizações

- **Atualizações a incluir/excluir**: esta opção abre a página **Incluir/Excluir**. As atualizações a serem incluídas ou excluídas estão em separadores diferentes. Para obter informações adicionais sobre como a inclusão é tratada, consulte [Agendar uma Implementação de Atualização](automation-tutorial-update-management.md#schedule-an-update-deployment).

> [!NOTE]
> É importante saber que as exclusões sobrepõem-se às inclusãos. Por exemplo, se definir uma `*`regra de exclusão de, então não são instalados patches ou pacotes, uma vez que todos estão excluídos. Os patches excluídos continuam a mostrar como desaparecidos da máquina. Para as máquinas Linux se um pacote estiver incluído mas tiver um pacote dependente que foi excluído, o pacote não está instalado.

> [!NOTE]
> Não é possível especificar atualizações que tenham sido substituídos para inclusão com a implementação da atualização.
>

- **Definições da agenda**: pode aceitar a data e hora predefinidas, que é 30 minutos após a hora atual. Também pode especificar um tempo diferente.

   Também pode especificar se a implementação ocorre uma vez ou de acordo com um agendamento periódico. Para configurar um horário recorrente, em **Recurrence, selecione** **Recorrente**.

   ![Caixa de diálogo Definições de Agendamento](./media/manage-update-multi/update-set-schedule.png)

- **Scripts prévios + Scripts posteriores**: selecione os scripts para executar antes e após a sua implementação. Para saber mais, veja [Gerir Scripts prévios e posteriores](pre-post-scripts.md).
- **Janela de manutenção (minutos)**: Especifique o período de tempo em que pretende que a atualização ocorra. Esta definição ajuda a garantir que as alterações são realizadas nos seus períodos de administração definidos.

- **Controlo de reiniciar** - Esta definição determina como as reinicializações são manipuladas para a implementação da atualização.

   |Opção|Descrição|
   |---|---|
   |Reiniciar se necessário| **(Predefinido)** Se necessário, inicia-se um reboot se a janela de manutenção o permitir.|
   |Reiniciar sempre|É iniciado um reboot independentemente de ser necessário um. |
   |Nunca reiniciar|Independentemente de ser necessário reiniciar, as reinicializações são suprimidas.|
   |Reiniciar apenas - não irá instalar atualizações|Esta opção ignora a instalação de atualizações e apenas inicia um reboot.|

Quando terminar de configurar o horário, selecione o botão **Criar** para voltar ao painel de status. A tabela **agendada** mostra o calendário de implantação que criou.

> [!NOTE]
> A Gestão de Atualizações suporta a implementação de atualizações da primeira parte e a transferência prévia de patches. Isto requer alterações nos sistemas que estão a ser corrigidos, consulte o suporte de [primeira parte e pré-descarregamento](automation-configure-windows-update.md#pre-download-updates) para aprender a configurar estas definições nos seus sistemas.

## <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualização** em **Gestão de atualizações**.

Se a implementação estiver em execução, o estado será **Em curso**. Após a implementação terminar com sucesso, o estado muda para **Succeed .**

Se uma ou mais atualizações falharem na implementação, o estado é **Falha parcial**.

![Estado da implementação de atualizações](./media/manage-update-multi/update-view-results.png)

Para ver o dashboard relativo a uma implementação de atualizações, selecione a implementação concluída.

O painel de resultados da **Atualização** mostra o número total de atualizações e os resultados de implementação da máquina virtual. A tabela à direita dá uma desagregação detalhada de cada atualização e dos resultados da instalação. Os resultados da instalação podem ser um dos seguintes valores:

- **Não tentada**: A atualização não foi instalada porque não havia tempo suficiente com base na janela de manutenção definida.
- **Sucesso**: A atualização foi bem sucedida.
- **Falha :** A atualização falhou.

Para ver todas as entradas de registo que a implementação criou, selecione **Todos os registos**.

Para ver o fluxo de trabalho do livro de execução que gere a implementação da atualização na máquina virtual alvo, selecione o azulejo de saída.

Para ver informações detalhadas sobre os erros da implementação, selecione **Erros**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre registos de Gestão de Atualização, saída e erros, consulte os registos de [atualização da Query para Gestão](automation-update-management-query-logs.md)de Atualizações .
