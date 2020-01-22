---
title: Gerenciar atualizações e patches para suas VMs do Azure
description: Este artigo fornece uma visão geral de como usar a automação do Azure Gerenciamento de Atualizações para gerenciar atualizações e patches para suas VMs do Azure e não Azure.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 01/21/2020
ms.custom: mvc
ms.openlocfilehash: 3922f8a2478f00c632b6daf294f23c7b5ad8c261
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310140"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Gerenciar atualizações e patches para suas VMs do Azure

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

1. No menu [portal do Azure](https://portal.azure.com) , selecione **máquinas virtuais** ou pesquise e selecione **máquinas virtuais** na **Home** Page.
1. Selecione a VM para a qual você deseja habilitar Gerenciamento de Atualizações.
1. Na página da VM, em **OPERAÇÕES**, selecione **Gestão de atualizações**. O painel **Ativar Gestão de Atualizações** abre.

A validação é executada para determinar se a Gestão de Atualizações está ativada para esta VM. Essa validação inclui verificações de um espaço de trabalho Log Analytics e uma conta de automação vinculada e se a solução Gerenciamento de Atualizações está habilitada no espaço de trabalho.

A área de trabalho do [Log Analytics](../azure-monitor/platform/data-platform-logs.md) serve para recolher dados gerados por funcionalidades e serviços, como a Gestão de Atualizações. A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

O processo de validação também verifica se a VM é provisionada com o agente de Log Analytics e Hybrid Runbook Worker de automação. Este agente serve para comunicar com a Automatização do Azure e para obter informações sobre o estado de atualização. O agente requer que a porta 443 esteja aberta para comunicar com o serviço de Automatização do Azure e para transferir atualizações.

Se for detetada a falta de qualquer um dos seguintes pré-requisitos durante a inclusão, estes serão adicionados automaticamente:

* Área de trabalho do [Log Analytics](../azure-monitor/platform/data-platform-logs.md)
* Uma [conta de Automatização](./automation-offering-get-started.md)
* Uma [Função de Trabalho de Runbook Híbrida](./automation-hybrid-runbook-worker.md) (ativada na VM)

Em **Gestão de Atualizações**, defina a localização, a área de trabalho do Log Analytics e a conta de Automatização a utilizar. Em seguida, selecione **Ativar**. Se estas opções não estiverem disponíveis, significa que outra solução de automatização está ativada para a VM. Nesse caso, a mesma área de trabalho e conta de Automatização têm de ser utilizadas.

![Janela Ativar a solução de Gestão de Atualizações](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

A ativação da solução pode demorar alguns minutos. Durante este período, não feche a janela do browser. Depois que a solução é habilitada, as informações sobre as atualizações ausentes na VM fluem para Azure Monitor logs. Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

## <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois de a Gestão de Atualizações ser ativada, o painel **Gestão de atualizações** abre. Se alguma atualização for identificada como ausente, uma lista de atualizações ausentes será mostrada na guia **atualizações ausentes** .

Em **link de informações**, selecione o link atualizar para abrir o artigo de suporte para a atualização. Você pode aprender informações importantes sobre a atualização.

![Ver o estado de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Clique em qualquer outro local na atualização para abrir o painel **Pesquisa de Registos** para a atualização selecionada. A consulta para a pesquisa de registos está predefinida para essa atualização específica. Pode modificar esta consulta ou criar a sua própria consulta para ver informações detalhadas sobre as atualizações que foram implementadas ou estão em falta no seu ambiente.

![Ver o estado de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurar alertas

Nesta etapa, você aprenderá a configurar um alerta para que você saiba o status de uma implantação de atualização.

### <a name="alert-conditions"></a>Condições de alerta

Em sua conta de automação, em **monitoramento** , vá para **alertas**e clique em **+ nova regra de alerta**.

Sua conta de automação já está selecionada como o recurso. Se você quiser alterá-lo, poderá clicar em **selecionar** e, na página **selecionar um recurso** , selecionar **contas de automação** na lista suspensa **Filtrar por tipo de recurso** . Selecione a sua Conta de Automatização e, em seguida, selecione **Concluído**.

Clique em **Adicionar condição** para selecionar o sinal apropriado para sua implantação de atualização. A tabela a seguir mostra os detalhes dos dois sinais disponíveis para implantações de atualização:

|Nome do sinal|Dimensões|Descrição|
|---|---|---|
|**Total de execuções de implantação de atualização**|-Nome da implantação da atualização</br>-Status|Esse sinal é usado para alertar sobre o status geral de uma implantação de atualização.|
|**Total de execuções do computador de implantação de atualização**|-Nome da implantação da atualização</br>-Status</br>-Computador de destino</br>-ID de execução da implantação de atualização|Esse sinal é usado para alertar sobre o status de uma implantação de atualização direcionada a computadores específicos|

Para os valores de dimensão, selecione um valor válido na lista. Se o valor que você está procurando não estiver na lista, clique no sinal de **\+** ao lado da dimensão e digite o nome personalizado. Em seguida, você pode selecionar o valor que deseja procurar. Se você quiser selecionar todos os valores de uma dimensão, clique no botão **selecionar \*** . Se você não escolher um valor para uma dimensão, essa dimensão será ignorada durante a avaliação.

![Configurar lógica de sinal](./media/automation-tutorial-update-management/signal-logic.png)

Em **Lógica de alerta**, para **Limiar**, introduza **1**. Quando tiver terminado, selecione **Concluído**.

### <a name="alert-details"></a>Detalhes do alerta

Em **2. Defina os detalhes do alerta**, insira um nome e uma descrição para o alerta. Defina **Gravidade** para **Informativo (Gravidade 2)** para uma execução com êxito ou **Informativo (Gravidade 1)** para uma execução que falhou.

![Configurar lógica de sinal](./media/automation-tutorial-update-management/define-alert-details.png)

Em **grupos de ações**, selecione **criar novo**. Um grupo de ação é um grupo de ações que podem ser utilizadas em vários alertas. As ações podem incluir, mas não estão limitadas a notificações por e-mail, runbooks, webhooks e muitas mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

Na caixa **Nome do grupo de ações**, introduza um nome para o alerta e um nome abreviado. O nome abreviado é utilizado em vez de um nome de grupo de ações completo quando as notificações são enviadas ao utilizar deste grupo.

Em **Ações**, introduza um nome para a ação, como **Notificações por E-mail**. Em **TIPO DE AÇÃO**, selecione **E-Mail/SMS/Push/voz**. Em **DETALHES**, selecione **Editar detalhes**.

Na página **E-mail/SMS/Push/Voz**, introduza um nome. Selecione a caixa de verificação **E-mail** e, em seguida, introduza um endereço de e-mail válido.

![Configurar um grupo de ações de e-mail](./media/automation-tutorial-update-management/configure-email-action-group.png)

No painel **E-mail/SMS/Push/Voz**, selecione **OK**. No painel **Adicionar grupo de ações**, selecione **OK**.

Para personalizar o assunto do e-mail de alerta, em **Criar regra**, em **Personalizar Ações**, selecione **Assunto do e-mail**. Quanto terminar, selecione **Criar regra de alerta**. O alerta informa-o quando a implementação de uma atualização for concluída com êxito e que máquinas faziam parte da execução dessa implementação de atualização.

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Em seguida, agende uma implementação que siga o seu agendamento e o período de administração da versão para instalar atualizações. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

>[!NOTE]
>Quando você agenda uma implantação de atualização, ela cria um recurso de [agendamento](shared-resources/schedules.md) vinculado ao runbook **patch-MicrosoftOMSComputers** que manipula a implantação de atualização nos computadores de destino. Se você excluir o recurso de agenda do portal do Azure ou usando o PowerShell depois de criar a implantação, ele interromperá a implantação de atualização agendada e apresentará um erro quando você tentar reconfigurá-la no Portal. Você só pode excluir o recurso de agendamento excluindo a agenda de implantação correspondente.  
>

Para agendar uma nova implementação de atualização para a VM, aceda a **Gestão de atualizações**e, em seguida, selecione **Agendar implementação da atualização**.

Em **Nova implementação de atualização**, especifique as seguintes informações:

* **Nome**: indique um nome exclusivo para a implementação da atualização.

* **Sistema operativo**: selecione o SO de destino para a implementação da atualização.

* **Grupos a atualizar (pré-visualização)** : defina uma consulta com base numa combinação de subscrição, grupos de recursos, localizações e etiquetas para criar um grupo dinâmico de VMs do Azure para incluir na sua implementação. Para saber mais, confira [grupos dinâmicos](automation-update-management-groups.md)

* **Computadores a atualizar**: Selecione uma Pesquisa guardada, o Grupo importado ou escolha a Máquina a partir do menu pendente e selecione máquinas individuais. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**. Para saber mais sobre os diferentes métodos de criação de grupos de computadores em logs de Azure Monitor, consulte [grupos de computadores em logs de Azure monitor](../azure-monitor/platform/computer-groups.md)

* **Classificação de atualização**: selecione as classificações de atualização com suporte disponíveis para cada produto que pode ser incluído na implantação da atualização. Para este tutorial, deixe todos os tipos selecionados.

  Os tipos de classificação são:

   |SO  |Tipo  |
   |---------|---------|
   |Windows     | Atualizações críticas</br>Atualizações de segurança</br>Update rollups</br>Pacotes de funcionalidades</br>Service packs</br>Atualizações de definições</br>Ferramentas</br>Atualizações        |
   |Linux     | Atualizações críticas e de segurança</br>Outras atualizações       |

   Para obter uma descrição dos tipos de classificação, veja [Classificações de atualizações](automation-view-update-assessments.md#update-classifications).

* **Atualizações a incluir/excluir**: esta opção abre a página **Incluir/Excluir**. As atualizações a serem incluídas ou excluídas estão em separadores diferentes.

> [!NOTE]
> É importante saber que as exclusões substituem as inclusões. Por exemplo, se você definir uma regra de exclusão de `*`, nenhum patch ou pacote será instalado, pois todos eles serão excluídos. Os patches excluídos ainda aparecem como ausentes no computador. Para computadores Linux, se um pacote estiver incluído, mas tiver um pacote dependente que foi excluído, o pacote não será instalado.

> [!NOTE]
> Você não pode especificar atualizações que foram substituídas para inclusão na implantação da atualização.
>

* **Definições da agenda**: o painel **Definições da Agenda** abre. A hora de início predefinida é 30 minutos depois da hora atual. Pode definir a hora de início para qualquer hora a partir de 10 minutos no futuro.

   Também pode especificar se a implementação ocorre uma vez ou configurar um agendamento periódico. Em **Periodicidade**, selecione **Uma vez**. Deixe a predefinição como 1 dia e selecione **OK**. Esta ação configura uma agenda periódica.

* **Scripts prévios + Scripts posteriores**: selecione os scripts para executar antes e após a sua implementação. Para saber mais, veja [Gerir Scripts prévios e posteriores](pre-post-scripts.md).

* **Janela de manutenção (minutos)** : deixe o valor predefinido. As janelas de manutenção controlam a quantidade de tempo permitida para a instalação das atualizações. Considere os detalhes a seguir ao especificar uma janela de manutenção.

  * As janelas de manutenção controlam quantas atualizações foram tentadas para serem instaladas.
  * Gerenciamento de Atualizações não interromperá a instalação de novas atualizações se o final de uma janela de manutenção estiver se aproximando.
  * Gerenciamento de Atualizações não terminará as atualizações em andamento se a janela de manutenção for excedida.
  * Se a janela de manutenção for excedida no Windows, isso geralmente ocorre devido a uma atualização service pack demorando muito para ser instalada.

  > [!NOTE]
  > Para evitar que as atualizações sejam aplicadas fora de uma janela de manutenção no Ubuntu, reconfigure o pacote de atualização autônoma para desabilitar as atualizações automáticas. Para obter informações sobre como configurar o pacote, consulte [atualizações automáticas tópico no guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Opções de reinício**: esta definição determina como os reinícios devem ser tratados. As opções disponíveis são:
  * Reiniciar se for preciso (Predefinição)
  * Reiniciar sempre
  * Nunca reiniciar
  * Reiniciar apenas - não irá instalar atualizações

> [!NOTE]
> As chaves do registro listadas em [chaves do registro usadas para gerenciar](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) a reinicialização podem causar um evento de reinicialização se o **controle de reinicialização** for definido como **nunca**

Quando tiver terminado de configurar a agenda, selecione **Criar**.

![Painel Definições de Agendamento de Atualizações](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Volta ao dashboard de estado. Selecione **Implementações de atualização agendadas** para mostrar a agenda de implementação que criou.

> [!NOTE]
> A Gestão de Atualizações suporta a implementação de atualizações da primeira parte e a transferência prévia de patches. Isto exige alterações nos sistemas que estão a ser corrigidos, veja [suporte da primeira parte e da pré-transferência](automation-configure-windows-update.md) para aprender a configurar estas definições nos seus sistemas.

As **implantações de atualização** também podem ser criadas programaticamente. Para saber como criar uma **implantação de atualização** com a API REST, consulte [Software Update Configurations-Create](/rest/api/automation/softwareupdateconfigurations/create). Também há um runbook de exemplo que pode ser usado para criar uma implantação semanal de **atualização**. Para saber mais sobre esse runbook, confira [criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualização** em **Gestão de atualizações**. O estado será **Em curso**, se a implementação estiver em execução. Quando a implementação concluir, se for bem sucedida, o estado muda para **Com êxito**. Quando existirem falhas numa ou mais atualizações na implementação, o estado é **Falha parcial**.

Selecione a implementação da atualização concluída para ver o dashboard relativo a essa implementação de atualização.

![Dashboard de estado de implementação de atualização para uma implementação específica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Em **Resultados da atualização**, um resumo indica o número total de atualizações e os resultados de implementação da VM. A tabela à direita mostra uma divisão detalhada de cada atualização e os resultados da instalação.

A lista seguinte mostra os valores disponíveis:

* **Não tentado**: a atualização não foi instalada porque não havia tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Com êxito**: a atualização foi executada com êxito.
* **Falhou**: a atualização falhou.

Selecione **Todos os registos** para ver todas as entradas de registo criadas pela implementação.

Selecione **Saída** para ver o fluxo de tarefas do runbook responsável pela gestão da implementação da atualização na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre os erros da implementação.

Quando a implementação da atualização for concluída com êxito, é enviado um e-mail semelhante ao exemplo seguinte para indicar o êxito da implementação:

![Configurar o grupo de ação de e-mail](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Passos seguintes

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

