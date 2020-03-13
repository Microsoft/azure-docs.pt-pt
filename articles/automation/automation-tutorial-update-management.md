---
title: Gerencie atualizações e patches para os seus VMs Azure
description: Este artigo fornece uma visão geral de como utilizar a Azure Automation Update Management para gerir atualizações e patches para os seus VMs Azure e não-Azure.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: mvc
ms.openlocfilehash: 347f2fbc0f12aa775c42dbb14a4625dc509a20ed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239659"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Gerencie atualizações e patches para os seus VMs Azure

Pode utilizar a solução de Gestão de Atualizações para gerir as atualizações e correções de erros das suas máquinas virtuais. Neste tutorial, irá aprender a avaliar rapidamente o estado das atualizações disponíveis, agendar a instalação das atualizações necessárias, rever os resultados da implementação e criar um alerta para verificar se as atualizações são aplicadas com êxito.

Para obter informações sobre preços, veja [Preços de Automatização para Gestão de Atualizações](https://azure.microsoft.com/pricing/details/automation/).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ver avaliações de atualizações
> * Configurar alertas
> * Agendar uma implementação de atualizações
> * Ver os resultados de uma implementação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* A solução [De Gestão](automation-update-management.md) de Atualização ativada para um ou mais dos seus VMs.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para carregar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois de a Gestão de Atualizações ser ativada, o painel **Gestão de atualizações** abre. Se alguma atualização for identificada como desaparecida, uma lista de atualizações em falta é mostrada no separador **de atualizações Em falta.**

No **link Informação,** selecione o link de atualização para abrir o artigo de suporte para a atualização. Pode aprender informações importantes sobre a atualização.

![Ver o estado de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Clique em qualquer outro lugar da atualização para abrir o painel de pesquisa de **Registo** para a atualização selecionada. A consulta para a pesquisa de registos está predefinida para essa atualização específica. Pode modificar esta consulta ou criar a sua própria consulta para ver informações detalhadas sobre as atualizações que foram implementadas ou estão em falta no seu ambiente.

![Ver o estado de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurar alertas

Neste passo, aprende-se a configurar um alerta para que saiba o estado de uma implementação de atualização.

### <a name="alert-conditions"></a>Condições de alerta

Na sua Conta de Automação, em **Monitorização** vá para **Alertas,** clique em **+ Nova regra**de alerta .

A sua Conta de Automação já está selecionada como recurso. Se pretender alterá-lo, pode clicar em **Selecionar** e na página **Select a resource,** selecione **Contas de Automação** no Filtro por dropdown do **tipo de recurso.** Selecione a sua Conta de Automatização e, em seguida, selecione **Concluído**.

Clique em **Adicionar condição** para selecionar o sinal adequado para a sua implementação da atualização. O quadro seguinte mostra os detalhes dos dois sinais disponíveis para as implementações de atualizações:

|Nome do sinal|Dimensões|Descrição|
|---|---|---|
|**Execuções de implementação total de atualização**|- Nome de implementação de atualização</br>- Estatuto|Este sinal é utilizado para alertar sobre o estado geral de uma implementação de atualização.|
|**Corridas de máquinas de implantação de atualização total**|- Nome de implementação de atualização</br>- Estatuto</br>- Computador-alvo</br>- Id de execução de implementação de atualização|Este sinal é usado para alertar sobre o estado de uma implementação de atualização direcionada a máquinas específicas|

Para os valores de dimensão, selecione um valor válido da lista. Se o valor que procura não estiver na lista, clique no sinal **de\+** ao lado da dimensão e digite o nome personalizado. Em seguida, pode selecionar o valor que pretende procurar. Se pretender selecionar todos os valores a partir de uma dimensão, clique no botão **Select \*.** Se não escolher um valor para uma dimensão, essa dimensão será ignorada durante a avaliação.

![Configurar lógica de sinal](./media/automation-tutorial-update-management/signal-logic.png)

Em **Lógica de alerta**, para **Limiar**, introduza **1**. Quando tiver terminado, selecione **Concluído**.

### <a name="alert-details"></a>Detalhes do alerta

Abaixo de **2. Defina detalhes de alerta,** introduza um nome e descrição para o alerta. Defina **Gravidade** para **Informativo (Gravidade 2)** para uma execução com êxito ou **Informativo (Gravidade 1)** para uma execução que falhou.

![Configurar lógica de sinal](./media/automation-tutorial-update-management/define-alert-details.png)

Em **grupos de ação,** selecione **Criar Novo**. Um grupo de ação é um grupo de ações que podem ser utilizadas em vários alertas. As ações podem incluir, mas não estão limitadas a notificações por e-mail, runbooks, webhooks e muitas mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

Na caixa **Nome do grupo de ações**, introduza um nome para o alerta e um nome abreviado. O nome abreviado é utilizado em vez de um nome de grupo de ações completo quando as notificações são enviadas ao utilizar deste grupo.

No âmbito **de Ações**, insira um nome para a ação, como **notificações por e-mail.** No **tipo de ação,** selecione **Email/SMS/Push/Voice**. Em **detalhes,** **selecione Editar detalhes.**

Na página **E-mail/SMS/Push/Voz**, introduza um nome. Selecione a caixa de verificação **E-mail** e, em seguida, introduza um endereço de e-mail válido.

![Configurar um grupo de ações de e-mail](./media/automation-tutorial-update-management/configure-email-action-group.png)

No **painel de e-mail/SMS/Push/Voice,** selecione **Ok**. No painel do **grupo de ação Add,** selecione **Ok**.

Para personalizar o assunto do e-mail de alerta, sob **a regra Criar,** sob **ações personalizadas,** selecione **o assunto do e-mail**. Quanto terminar, selecione **Criar regra de alerta**. O alerta informa-o quando a implementação de uma atualização for concluída com êxito e que máquinas faziam parte da execução dessa implementação de atualização.

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Em seguida, agende uma implementação que siga o seu agendamento e o período de administração da versão para instalar atualizações. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

>[!NOTE]
>Ao agendar uma implementação de atualização, cria um recurso de [agenda](shared-resources/schedules.md) ligado ao livro de execução **Patch-MicrosoftOMSComputers** que trata da implementação da atualização nas máquinas-alvo. Se eliminar o recurso de agenda do portal Azure ou utilizar o PowerShell após a criação da implementação, quebra a implementação da atualização programada e apresenta um erro ao tentar reconfigurá-lo a partir do portal. Só é possível eliminar o recurso de agenda eliminando o calendário de implementação correspondente.  
>

Para agendar uma nova implementação de atualização para a VM, aceda a **Gestão de atualizações**e, em seguida, selecione **Agendar implementação da atualização**.

Em **Nova implementação de atualização**, especifique as seguintes informações:

* **Nome**: indique um nome exclusivo para a implementação da atualização.

* **Sistema operativo**: selecione o SO de destino para a implementação da atualização.

* **Grupos a atualizar (pré-visualização)** : defina uma consulta com base numa combinação de subscrição, grupos de recursos, localizações e etiquetas para criar um grupo dinâmico de VMs do Azure para incluir na sua implementação. Para saber mais, consulte [Grupos Dinâmicos](automation-update-management-groups.md)

* **Computadores a atualizar**: Selecione uma Pesquisa guardada, o Grupo importado ou escolha a Máquina a partir do menu pendente e selecione máquinas individuais. Se escolher **Máquinas,** a prontidão da máquina é mostrada na coluna de prontidão do **agente Atualizar.** Para conhecer os diferentes métodos de criação de grupos informáticos nos registos do Monitor Azure, consulte [grupos informáticos em registos do Monitor Azure](../azure-monitor/platform/computer-groups.md)

* **Classificação da atualização**: Selecione as classificações de atualização suportadas disponíveis para cada produto que possa ser incluído na implementação da atualização. Para este tutorial, deixe todos os tipos selecionados.

  Os tipos de classificação são:

   |SO  |Tipo  |
   |---------|---------|
   |Windows     | Atualizações críticas</br>Atualizações de segurança</br>Update rollups</br>Pacotes de funcionalidades</br>Service packs</br>Atualizações de definições</br>Ferramentas</br>Atualizações        |
   |Linux     | Atualizações críticas e de segurança</br>Outras atualizações       |

   Para obter uma descrição dos tipos de classificação, veja [Classificações de atualizações](automation-view-update-assessments.md#update-classifications).

* **Atualizações a incluir/excluir**: esta opção abre a página **Incluir/Excluir**. As atualizações a serem incluídas ou excluídas estão em separadores diferentes.

> [!NOTE]
> É importante saber que as exclusões sobrepõem-se às inclusãos. Por exemplo, se definir uma regra de exclusão de `*`, então não são instalados patches ou pacotes, uma vez que todos estão excluídos. Os patches excluídos continuam a mostrar como desaparecidos da máquina. Para as máquinas Linux se um pacote estiver incluído mas tiver um pacote dependente que foi excluído, o pacote não está instalado.

> [!NOTE]
> Não é possível especificar atualizações que tenham sido substituídos para inclusão com a implementação da atualização.
>

* **Definições da agenda**: o painel **Definições da Agenda** abre. A hora de início predefinida é 30 minutos depois da hora atual. Pode definir a hora de início para qualquer hora a partir de 10 minutos no futuro.

   Também pode especificar se a implementação ocorre uma vez ou configurar um agendamento periódico. Em **Periodicidade**, selecione **Uma vez**. Deixe o padrão como 1 dia e selecione **Ok**. Esta ação configura uma agenda periódica.

* **Scripts prévios + Scripts posteriores**: selecione os scripts para executar antes e após a sua implementação. Para saber mais, veja [Gerir Scripts prévios e posteriores](pre-post-scripts.md).

* **Janela de manutenção (minutos)** : deixe o valor predefinido. As janelas de manutenção controlam a quantidade de tempo permitida para que as atualizações possam instalar. Considere os seguintes detalhes ao especificar uma janela de manutenção.

  * As janelas de manutenção controlam quantas atualizações são tentadas de ser instaladas.
  * A Atualização A Gestão não para de instalar novas atualizações se se aproximar do fim de uma janela de manutenção.
  * A Atualização A Gestão não encerra atualizações em curso se a janela de manutenção for excedida.
  * Se a janela de manutenção for excedida no Windows, muitas vezes deve-se a uma atualização de pacote de serviço que demora muito tempo a instalar.

  > [!NOTE]
  > Para evitar que as atualizações sejam aplicadas fora de uma janela de manutenção em Ubuntu, reconfigure o pacote Deactualização Não Atendida para desativar atualizações automáticas. Para obter informações sobre como configurar o pacote, consulte [o tópico de Atualizações Automáticas no Guia do Servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Opções de reinício**: esta definição determina como os reinícios devem ser tratados. As opções disponíveis são:
  * Reiniciar se for preciso (Predefinição)
  * Reiniciar sempre
  * Nunca reiniciar
  * Reiniciar apenas - não irá instalar atualizações

> [!NOTE]
> As teclas de registo listadas em [teclas de registo utilizadas para gerir](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) o reinício podem causar um evento de reinício se o **Controlo de Reiniciar** estiver definido para **Nunca Reiniciar**.

Quando tiver terminado de configurar a agenda, selecione **Criar**.

![Painel Definições de Agendamento de Atualizações](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Volta ao dashboard de estado. Selecione **Implementações de atualização agendadas** para mostrar a agenda de implementação que criou.

> [!NOTE]
> A Gestão de Atualizações suporta a implementação de atualizações da primeira parte e a transferência prévia de patches. Isto exige alterações nos sistemas que estão a ser corrigidos, veja [suporte da primeira parte e da pré-transferência](automation-configure-windows-update.md) para aprender a configurar estas definições nos seus sistemas.

**Atualização As implementações** também podem ser criadas programáticamente. Para aprender a criar uma Implementação de **Atualização** com o REST API, consulte configurações de [atualização](/rest/api/automation/softwareupdateconfigurations/create)de software - Criar . Há também um livro de amostras que pode ser usado para criar uma implementação semanal de **atualização**. Para saber mais sobre este livro de execução, consulte Criar uma implementação semanal de [atualização para um ou mais VMs num grupo](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)de recursos .

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

