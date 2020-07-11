---
title: Gerir atualizações para vários VMs na Azure Automation
description: Este artigo diz como gerir atualizações para vários VMs.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: a2d16bdca18b7fc0afab2a3deb325d1a75be3bb8
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185182"
---
# <a name="manage-updates-for-multiple-vms"></a>Gerir atualizações de várias VMs

Pode utilizar a Azure Automation Update Management para gerir atualizações e patches para os seus VMs Windows e Linux. Na sua conta da [Automatização do Azure](./index.yml), pode:

- Ativar VMs para gestão de atualizações.
- Avaliar o estado das atualizações disponíveis.
- Agendar a instalação das atualizações necessárias.
- Reveja os resultados da implementação para verificar se as atualizações foram aplicadas com sucesso a todos os VMs para os quais a Gestão de Atualização está ativada.

Para conhecer os requisitos do sistema para a Gestão de Atualização, consulte [os requisitos do cliente de Gestão de Atualização](automation-update-management.md#client-requirements).

## <a name="prerequisites"></a>Pré-requisitos

* Um VM ou computador com um dos sistemas operativos suportados instalados.
* Acesso a um repositório de atualização para VMs Linux habilitado para Gestão de Atualização.

## <a name="enable-update-management-for-azure-vms"></a>Ativar a gestão de atualização para VMs Azure

1. No portal Azure, abra a sua conta Demôm automação e, em seguida, selecione **a gestão de Atualização**.

2. **Selecione Adicionar VMs Azure**.

    ![Separador Adicionar VM do Azure](./media/manage-update-multi/update-onboard-vm.png)

3. Selecione um VM para ativar e selecione **Ative** under **Enable Update Management**.

    ![Caixa de diálogo Ativar Gestão de Atualizações](./media/manage-update-multi/update-enable.png)

    Quando a operação estiver concluída, a Gestão de Atualização está ativada no seu VM.

## <a name="enable-update-management-for-non-azure-vms-and-computers"></a>Ativar a gestão de atualização para VMs e computadores não Azure

O agente Log Analytics para Windows e Linux precisa de ser instalado nos VMs que estão a ser em execução na sua rede corporativa ou noutro ambiente em nuvem, de modo a capacitá-los com a Gestão de Atualização. Para conhecer os requisitos do sistema e métodos suportados para implantar o agente em máquinas hospedadas fora de Azure, consulte [a visão geral do agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Ver computadores ligados à sua conta de Automação

Depois de ativar a Gestão de Atualização das suas máquinas, pode ver as informações da máquina selecionando **Computadores**. Pode ver informações sobre o nome da máquina, estado de conformidade, ambiente, tipo de SO, atualizações críticas e de segurança instaladas, outras atualizações instaladas e a prontidão do agente de atualização para os seus computadores.

  ![Separador Ver computadores](./media/manage-update-multi/update-computers-tab.png)

Os computadores que foram recentemente ativados para a Gestão de Atualização podem ainda não ter sido avaliados. O estado de conformidade para esses computadores `Not assessed` é. Aqui está uma lista de possíveis valores para o estado de conformidade:

- `Compliant`: Computadores que não faltem a atualizações críticas ou de segurança.
- `Non-compliant`: Computadores que faltam pelo menos uma atualização crítica ou de segurança.
- `Not assessed`: Os dados de avaliação da atualização não foram recebidos do computador dentro do prazo previsto. Para os computadores Linux, o prazo esperado é a última hora. Para os computadores Windows, o prazo esperado é o das últimas 12 horas.

Para visualizar o estado do agente, selecione o link na coluna de prontidão do **agente De atualização.** A seleção desta opção abre o painel operário híbrido e mostra o estado do Trabalhador Híbrido. A imagem a seguir mostra um exemplo de um agente que não foi ligado à Gestão de Atualização por um longo período de tempo:

![Separador Ver computadores](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Ver avaliações de atualizações

Após a gestão da atualização estar ativada, abre-se o painel de Gestão de Atualização. Pode ver uma lista de atualizações em falta no separador **Atualizações em falta**.

## <a name="collect-data"></a>Recolher dados

Os agentes instalados em VMs e computadores recolhem dados sobre atualizações. Os agentes enviam os dados para a Azure Update Management.

### <a name="supported-agents"></a>Agentes suportados

A tabela a seguir descreve as fontes ligadas que a Atualização de Gestão suporta:

| Origem ligada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Yes |A Atualização Management recolhe informações sobre atualizações do sistema a partir de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Yes |A Update Management recolhe informações sobre atualizações do sistema a partir de agentes do Linux e inicia a instalação de atualizações necessárias nas distribuições suportadas. |
| Grupo de gestão do Operations Manager |Yes |A Update Management recolhe informações sobre atualizações do sistema de agentes de um grupo de gestão conectado. |
| Conta de armazenamento do Azure |Não |O Azure Storage não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência da recolha

Depois de um computador completar uma verificação para a conformidade da atualização, o agente reencaminha a informação a granel para os registos do Azure Monitor. Num computador Windows, a verificação de conformidade é executada a cada 12 horas por defeito.

Além do calendário de digitalização, a verificação para conformidade de atualização é iniciada no prazo de 15 minutos após o MMA ser reiniciado, antes da instalação de atualização e após a instalação de atualização.

Para um computador Linux, a verificação de conformidade é realizada a cada hora por defeito. Se o agente MMA for reiniciado, é iniciado um exame de conformidade dentro de 15 minutos.

O dashboard pode demorar entre 30 minutos a 6 horas a apresentar os dados atualizados a partir dos computadores geridos.

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que se alinhe com o seu horário de lançamento e janela de serviço. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

>[!NOTE]
>Quando agenda uma implementação de atualização, cria um recurso de [agenda](shared-resources/schedules.md) ligado ao livro **de execução Patch-MicrosoftOMSComputers** que trata da implementação da atualização nas máquinas-alvo. Se eliminar o recurso de agenda do portal Azure ou utilizar o PowerShell após a criação da implementação, quebra a implementação da atualização programada e apresenta um erro quando tenta reconfigurá-lo a partir do portal. Só é possível eliminar o recurso de agenda, eliminando o calendário de implementação correspondente.
>

Para agendar uma nova atualização para um ou mais VMs, sob **gestão de atualização,** selecione **a implementação da atualização da agenda**.

No painel de implantação da **nova atualização,** especifique as seguintes informações:

- **Nome**: Introduza um nome único para identificar a implementação da atualização.
- **Sistema operativo**: Selecione **Windows** ou **Linux**.
- **Grupos a atualizar**: Defina uma consulta com base numa combinação de subscrição, grupos de recursos, localizações e tags para construir um grupo dinâmico de VMs Azure para incluir na sua implementação. Para os VMs não-Azure, as pesquisas guardadas são usadas para criar um grupo dinâmico para incluir na sua implementação. Para saber mais, [Grupos Dinâmicos.](automation-update-management-groups.md)
- **Máquinas a atualizar:** Selecione uma pesquisa guardada, grupo importado ou selecione Máquinas, para escolher as máquinas que pretende atualizar.

   >[!NOTE]
   >A seleção da opção ProcuraDa Salva não devolve identidades da máquina, apenas os seus nomes. Se tiver vários VMs com o mesmo nome em vários grupos de recursos, eles são devolvidos nos resultados. Recomenda-se a utilização da opção **de atualização dos Grupos** para garantir que inclui VMs exclusivos que correspondam aos seus critérios.

   Se escolher **Máquinas,** a prontidão da máquina é mostrada na coluna de prontidão do **agente Desajuste.** Pode ver o estado de saúde da máquina antes de agendar a implementação da atualização. Para conhecer os diferentes métodos de criação de grupos informáticos em registos do Monitor Azure, consulte [grupos de computador em registos do Monitor Azure](../azure-monitor/platform/computer-groups.md)

  ![Novo painel de implementação de atualização](./media/manage-update-multi/update-select-computers.png)

- **Classificação de atualização**: Selecione os tipos de software a incluir na implementação da atualização. Para uma descrição dos tipos de classificação, consulte [as classificações de Atualização](automation-view-update-assessments.md#work-with-update-classifications). Os tipos de classificação são:
  - Atualizações críticas
  - Atualizações de segurança
  - Update rollups
  - Pacotes de funcionalidades
  - Service packs
  - Atualizações de definições
  - Ferramentas
  - Atualizações

- **Atualizações a incluir/excluir**: esta opção abre a página Incluir/Excluir. As atualizações a serem incluídas ou excluídas estão em separadores diferentes. Para obter informações adicionais sobre como a inclusão é tratada, consulte [Agendar uma Atualização .](automation-tutorial-update-management.md#schedule-an-update-deployment)

> [!NOTE]
> É importante saber que as exclusões substituem as inclusãos. Por exemplo, se definir uma regra de exclusão de `*` , então não são instalados patches ou pacotes, uma vez que estão todos excluídos. Os remendos excluídos ainda mostram como desaparecidos da máquina. Para as máquinas Linux se um pacote estiver incluído mas tiver um pacote dependente que foi excluído, a embalagem não está instalada.

> [!NOTE]
> Não é possível especificar atualizações que tenham sido substituídos para inclusão com a implementação da atualização.

- **Definições da agenda**: pode aceitar a data e hora predefinidas, que é 30 minutos após a hora atual. Também pode especificar um tempo diferente.

   Também pode especificar se a implementação ocorre uma vez ou de acordo com um agendamento periódico. Para configurar um horário recorrente, em **Recorrência,** selecione **Recorrente**.

   ![Caixa de diálogo Definições de Agendamento](./media/manage-update-multi/update-set-schedule.png)

- **Scripts prévios + Scripts posteriores**: selecione os scripts para executar antes e após a sua implementação. Para saber mais, veja [Gerir Scripts prévios e posteriores](pre-post-scripts.md).
- **Janela de manutenção (minutos)**: Especifique o período de tempo que pretende que a atualização ocorra. Esta definição ajuda a garantir que as alterações são realizadas nos seus períodos de administração definidos.

- **Controlo de reinicialização** - Esta definição determina como as reinicializações são manuseadas para a implementação da atualização.

   |Opção|Descrição|
   |---|---|
   |Reiniciar se necessário| **(Predefinição)** Se necessário, é iniciado um reboot se a janela de manutenção permitir.|
   |Reiniciar sempre|Um reboot é iniciado independentemente de ser necessário. |
   |Nunca reiniciar|Independentemente de ser necessário um reboot, as reinicializações são suprimidas.|
   |Reiniciar apenas - não irá instalar atualizações|Esta opção ignora a instalação de atualizações e apenas inicia um reboot.|

Quando terminar de configurar o horário, selecione o botão **Criar** para voltar ao painel de estado. A tabela **Agendada** mostra o calendário de implantação que criou.

> [!NOTE]
> A Gestão de Atualizações suporta a implementação de atualizações da primeira parte e a transferência prévia de patches. Isto requer alterações nos sistemas que estão a ser corrigidos, ver [o suporte de primeira parte e pré-download](automation-configure-windows-update.md#pre-download-updates) para aprender a configurar estas definições nos seus sistemas.

## <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualização** em **Gestão de atualizações**.

Se a implementação estiver em execução, o estado será **Em curso**. Após o início da implantação com sucesso, o estado muda para **Sucesso**.

Se uma ou mais atualizações falharem na implementação, o estado é **Falha parcial**.

![Estado da implementação de atualizações](./media/manage-update-multi/update-view-results.png)

Para ver o dashboard relativo a uma implementação de atualizações, selecione a implementação concluída.

O painel de resultados da Atualização mostra o número total de atualizações e os resultados de implantação do VM. A tabela à direita apresenta uma desagregação detalhada de cada atualização e dos resultados da instalação. Os resultados da instalação podem ser um dos seguintes valores:

- `Not attempted`: A atualização não foi instalada porque o tempo insuficiente estava disponível com base na janela de manutenção definida.
- `Succeeded`: A atualização foi bem sucedida.
- `Failed`: A atualização falhou.

Para ver todas as entradas de registo que a implementação criou, selecione **Todos os registos**.

Para ver o fluxo de trabalho do runbook que gere a implementação da atualização no VM alvo, selecione o azulejo de saída.

Para ver informações detalhadas sobre os erros da implementação, selecione **Erros**.

## <a name="next-steps"></a>Passos seguintes

* Se precisar de pesquisar registos de atualização, consulte [os registos de Gestão de Atualização de Consultas](automation-update-management-query-logs.md).
