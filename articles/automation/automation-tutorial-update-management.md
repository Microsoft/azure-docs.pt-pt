---
title: Gerir atualizações e correções para as VMs Windows do Azure
description: Este artigo apresenta uma descrição geral de como utilizar a Gestão de Atualizações da Automatização do Azure para gerir as atualizações e correções das suas VMs Windows do Azure.
services: automation
author: zjalexander
ms.service: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 12/04/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: fbca620fca1aeb53acc9bd70561e783b49ff1a60
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822359"
---
# <a name="manage-windows-updates-by-using-azure-automation"></a>Gerir atualizações do Windows com a Automatização do Azure

Pode utilizar a solução de Gestão de Atualizações para gerir as atualizações e correções de erros das suas máquinas virtuais. Neste tutorial, irá aprender a avaliar rapidamente o estado das atualizações disponíveis, agendar a instalação das atualizações necessárias, rever os resultados da implementação e criar um alerta para verificar se as atualizações são aplicadas com êxito.

Para obter informações sobre preços, veja [Preços de Automatização para Gestão de Atualizações](https://azure.microsoft.com/pricing/details/automation/).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Carregar uma VM para Gestão de Atualizações
> * Ver avaliações de atualizações
> * Configurar alertas
> * Agendar uma implementação de atualizações
> * Ver os resultados de uma implementação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se ainda não tiver uma, pode [ativar o crédito do Azure mensal para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [Conta de Automatização do Azure](automation-offering-get-started.md) para reter os runbooks de observador e ação e a Tarefa de Observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para carregar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

Primeiro, ative a Gestão de Atualizações na sua VM para este tutorial:

1. No portal do Azure, no menu esquerdo, selecione **Máquinas virtuais**. Selecione uma VM na lista.
2. Na página da VM, em **OPERAÇÕES**, selecione **Gestão de atualizações**. O painel **Ativar Gestão de Atualizações** abre.

A validação é executada para determinar se a Gestão de Atualizações está ativada para esta VM. Esta validação inclui a verificação da existência de uma área de trabalho do Azure Log Analytics e da conta de Automatização ligada, e se a solução de Gestão de Atualizações está na área de trabalho.

A área de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) serve para recolher dados gerados por funcionalidades e serviços, como a Gestão de Atualizações. A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

O processo de validação verifica ainda se a VM está aprovisionada com o Microsoft Monitoring Agent (MMA) e a Função de Trabalho de Runbook Híbrida de Automatização. Este agente serve para comunicar com a Automatização do Azure e para obter informações sobre o estado de atualização. O agente requer que a porta 443 esteja aberta para comunicar com o serviço de Automatização do Azure e para transferir atualizações.

Se for detetada a falta de qualquer um dos seguintes pré-requisitos durante a inclusão, estes serão adicionados automaticamente:

* Área de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* Uma [conta de Automatização](./automation-offering-get-started.md)
* Uma [Função de Trabalho de Runbook Híbrida](./automation-hybrid-runbook-worker.md) (ativada na VM)

Em **Gestão de Atualizações**, defina a localização, a área de trabalho do Log Analytics e a conta de Automatização a utilizar. Em seguida, selecione **Ativar**. Se estas opções não estiverem disponíveis, significa que outra solução de automatização está ativada para a VM. Nesse caso, a mesma área de trabalho e conta de Automatização têm de ser utilizadas.

![Janela Ativar a solução de Gestão de Atualizações](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

A ativação da solução pode demorar alguns minutos. Durante este período, não feche a janela do browser. Após a solução está ativada, informações sobre atualizações em falta na VM são transmitidas para registos do Azure Monitor. Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

## <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois de a Gestão de Atualizações ser ativada, o painel **Gestão de atualizações** abre. Se existirem atualizações em falta, é apresentada uma lista das atualizações em falta no separador **Atualizações em falta**.

Em **LIGAÇÃO PARA INFORMAÇÕES**, selecione a ligação de atualização para abrir o artigo de suporte para a atualização numa nova janela. Pode obter informações importantes sobre a atualização nesta janela.

![Ver o estado de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Clique em qualquer outro local na atualização para abrir o painel **Pesquisa de Registos** para a atualização selecionada. A consulta para a pesquisa de registos está predefinida para essa atualização específica. Pode modificar esta consulta ou criar a sua própria consulta para ver informações detalhadas sobre as atualizações que foram implementadas ou estão em falta no seu ambiente.

![Ver o estado de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurar alertas

Neste passo, vai aprender a configurar um alerta para informá-lo o estado de uma implementação de atualização.

### <a name="alert-conditions"></a>Condições de alerta

Na sua conta de automatização, sob **monitorização** aceda à **alertas**e, em seguida, clique em **+ nova regra de alerta**.

Sua conta de automatização já está selecionada como o recurso. Se pretender alterá-la pode clicar em **selecionar** e, no **selecione um recurso** , selecione **contas de automatização** no **filtrar por tipo de recurso** lista pendente. Selecione a sua Conta de Automatização e, em seguida, selecione **Concluído**.

Clique em **adicionar condição** para selecionar o sinal de que é adequado para a sua implementação de atualização. A tabela seguinte mostra os detalhes dos dois sinais disponíveis para implementações de atualizações:

|Nome do sinal|Dimensões|Descrição|
|---|---|---|
|**Execuções da implementação de atualização total**|-Atualizar o nome da implementação</br>- Status|Este sinal é utilizado para o alerta sobre o estado geral de uma implementação de atualização.|
|**Execuções do computador de implementação de atualização total**|-Atualizar o nome da implementação</br>- Status</br>-Computador de destino</br>-Atualizar o Id de execução de implementação|Este sinal é utilizado para o alerta sobre o status de uma implementação de atualização direcionada para máquinas específicas|

Para os valores de dimensão, selecione um valor válido na lista. Se o valor que está procurando não estiver na lista, clique nas **\+** início de sessão ao lado da dimensão e escreva o nome personalizado. Em seguida, pode selecionar o valor que pretende procurar. Se pretender selecionar todos os valores de uma dimensão, clique nas **selecionar \***  botão. Se não escolher um valor para uma dimensão, essa dimensão será ignorada durante a avaliação.

![Configurar lógica de sinal](./media/automation-tutorial-update-management/signal-logic.png)

Em **Lógica de alerta**, para **Limiar**, introduza **1**. Quando tiver terminado, selecione **Concluído**.

### <a name="alert-details"></a>Detalhes do alerta

Em **2. Definir detalhes do alerta**, introduza um nome e uma descrição para o alerta. Defina **Gravidade** para **Informativo (Gravidade 2)** para uma execução com êxito ou **Informativo (Gravidade 1)** para uma execução que falhou.

![Configurar lógica de sinal](./media/automation-tutorial-update-management/define-alert-details.png)

Sob **grupos de ação**, selecione **criar nova**. Um grupo de ação é um grupo de ações que podem ser utilizadas em vários alertas. As ações podem incluir, mas não estão limitadas a notificações por e-mail, runbooks, webhooks e muitas mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

Na caixa **Nome do grupo de ações**, introduza um nome para o alerta e um nome abreviado. O nome abreviado é utilizado em vez de um nome de grupo de ações completo quando as notificações são enviadas ao utilizar deste grupo.

Em **Ações**, introduza um nome para a ação, como **Notificações por E-mail**. Em **TIPO DE AÇÃO**, selecione **E-Mail/SMS/Push/voz**. Em **DETALHES**, selecione **Editar detalhes**.

Na página **E-mail/SMS/Push/Voz**, introduza um nome. Selecione a caixa de verificação **E-mail** e, em seguida, introduza um endereço de e-mail válido.

![Configurar um grupo de ações de e-mail](./media/automation-tutorial-update-management/configure-email-action-group.png)

No painel **E-mail/SMS/Push/Voz**, selecione **OK**. No painel **Adicionar grupo de ações**, selecione **OK**.

Para personalizar o assunto do e-mail de alerta, em **Criar regra**, em **Personalizar Ações**, selecione **Assunto do e-mail**. Quanto terminar, selecione **Criar regra de alerta**. O alerta informa-o quando a implementação de uma atualização for concluída com êxito e que máquinas faziam parte da execução dessa implementação de atualização.

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Em seguida, agende uma implementação que siga o seu agendamento e o período de administração da versão para instalar atualizações. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova implementação de atualização para a VM, aceda a **Gestão de atualizações**e, em seguida, selecione **Agendar implementação da atualização**.

Em **Nova implementação de atualização**, especifique as seguintes informações:

* **Nome**: Introduza um nome exclusivo para a implementação da atualização.

* **Sistema operativo**: Selecione o sistema operacional de destino para a implementação da atualização.

* **Grupos de atualização (pré-visualização)**: Defina uma consulta com base numa combinação de subscrição, grupos de recursos, localizações e as etiquetas para criar um grupo dinâmico de VMs do Azure para incluir na sua implementação. Para obter mais informações, consulte [grupos dinâmicos](automation-update-management.md#using-dynamic-groups)

* **Computadores a atualizar**: Selecione uma pesquisa guardada, grupo importada, ou escolher máquina da lista pendente e selecione máquinas individuais. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**. Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos registos do Azure Monitor, consulte o artigo [grupos de computadores nos registos do Azure Monitor](../azure-monitor/platform/computer-groups.md)

* **Classificação da atualização**: Selecione os tipos de software que a implementação de atualizações incluída na implementação. Para este tutorial, deixe todos os tipos selecionados.

  Os tipos de classificação são:

   |SO  |Type  |
   |---------|---------|
   |Windows     | Atualizações críticas</br>Atualizações de segurança</br>Update rollups</br>Pacotes de funcionalidades</br>Service packs</br>Atualizações de definições</br>Ferramentas</br>Atualizações        |
   |Linux     | Atualizações críticas e de segurança</br>Outras atualizações       |

   Para obter uma descrição dos tipos de classificação, veja [Classificações de atualizações](automation-update-management.md#update-classifications).

* **Atualizações a incluir/excluir**: esta opção abre a página **Incluir/Excluir**. As atualizações a serem incluídas ou excluídas estão em separadores diferentes. Para obter mais informações sobre como a inclusão é processada, consulte [comportamento de inclusão](automation-update-management.md#inclusion-behavior)

* **Definições da agenda**: O **definições do agendamento** painel abre-se. A hora de início predefinida é 30 minutos depois da hora atual. Pode definir a hora de início para qualquer hora a partir de 10 minutos no futuro.

   Também pode especificar se a implementação ocorre uma vez ou configurar um agendamento periódico. Em **Periodicidade**, selecione **Uma vez**. Deixe a predefinição como 1 dia e selecione **OK**. Esta ação configura uma agenda periódica.

* **Pré- scripts de + pós-scripts de**: Selecione os scripts sejam executados antes e após a sua implementação. Para saber mais, veja [Gerir Scripts prévios e posteriores](pre-post-scripts.md).
* **Janela de manutenção (minutos)**: Deixe o valor predefinido. Pode especificar a janela de tempo na qual pretende que a implementação da atualização ocorra. Esta definição ajuda a garantir que as alterações são realizadas nos seus períodos de administração definidos.

* **Opções de reinício**: Esta definição determina como devem ser tratadas reinicializações. As opções disponíveis são:
  * Reiniciar se for preciso (Predefinição)
  * Reiniciar sempre
  * Nunca reiniciar
  * Reiniciar apenas - não irá instalar atualizações

Quando tiver terminado de configurar a agenda, selecione **Criar**.

![Painel Definições de Agendamento de Atualizações](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Volta ao dashboard de estado. Selecione **Implementações de atualização agendadas** para mostrar a agenda de implementação que criou.

> [!NOTE]
> A Gestão de Atualizações suporta a implementação de atualizações da primeira parte e a transferência prévia de patches. Isto exige alterações nos sistemas que estão a ser corrigidos, veja [suporte da primeira parte e da pré-transferência](automation-update-management.md#firstparty-predownload) para aprender a configurar estas definições nos seus sistemas.

## <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualização** em **Gestão de atualizações**. O estado será **Em curso**, se a implementação estiver em execução. Quando a implementação concluir, se for bem sucedida, o estado muda para **Com êxito**. Quando existirem falhas numa ou mais atualizações na implementação, o estado é **Falha parcial**.

Selecione a implementação da atualização concluída para ver o dashboard relativo a essa implementação de atualização.

![Dashboard de estado de implementação de atualização para uma implementação específica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Em **Resultados da atualização**, um resumo indica o número total de atualizações e os resultados de implementação da VM. A tabela à direita mostra uma divisão detalhada de cada atualização e os resultados da instalação.

A lista seguinte mostra os valores disponíveis:

* **Não tentada**: A atualização não foi instalada porque não havia tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Foi efetuada com êxito**: A atualização foi concluída com êxito.
* **Falha ao**: A atualização falhou.

Selecione **Todos os registos** para ver todas as entradas de registo criadas pela implementação.

Selecione **Saída** para ver o fluxo de tarefas do runbook responsável pela gestão da implementação da atualização na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre os erros da implementação.

Quando a implementação da atualização for concluída com êxito, é enviado um e-mail semelhante ao exemplo seguinte para indicar o êxito da implementação:

![Configurar o grupo de ação de e-mail](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Carregar uma VM para Gestão de Atualizações
> * Ver avaliações de atualizações
> * Configurar alertas
> * Agendar uma implementação de atualizações
> * Ver os resultados de uma implementação

Avance para a descrição geral da solução de Gestão de Atualizações.

> [!div class="nextstepaction"]
> [Solução de Gestão de Atualizações](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)

