---
title: Gerencie atualizações e patches para os seus VMs Azure
description: Este artigo fornece uma visão geral de como utilizar a Azure Automation Update Management para gerir atualizações e patches para os seus VMs Azure e não-Azure.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 62c661f75aef77117a61be7e802562e6dde17ba5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604680"
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

Depois de ativar a Atualização de Gestão, abre-se a página de gestão da Atualização. Se alguma atualização for identificada como desaparecida, uma lista de atualizações em falta é mostrada no separador **de atualizações Em falta.**

No **link Informação,** selecione o link de atualização para abrir o artigo de suporte para a atualização. Pode aprender informações importantes sobre a atualização.

![Ver o estado de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Clique em qualquer outro lugar da atualização para abrir o painel de pesquisa de Registo para a atualização selecionada. A consulta para a pesquisa de registos está predefinida para essa atualização específica. Pode modificar esta consulta ou criar a sua própria consulta para visualizar informações detalhadas sobre atualizações implementadas ou em falta no seu ambiente.

![Ver o estado de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurar alertas

Neste passo, aprende-se a configurar um alerta para que saiba o estado de uma implementação de atualização.

### <a name="alert-conditions"></a>Condições de alerta

Na sua conta de Automação, vá a **Alertas** sob **Monitorização**e, em seguida, clique em **Nova regra**de alerta .

A sua conta De automação já está selecionada como recurso. Se quiser troco, clique em **Selecionar**. Na página Selecione uma página de recursos, escolha **Contas de Automação** do Filtro por menu de dropdown **do tipo de recurso.** Selecione a sua conta De automação e, em seguida, clique em **Done**.

Clique em **Adicionar condição** para selecionar o sinal adequado para a sua implementação da atualização. A tabela seguinte mostra os detalhes dos dois sinais disponíveis.

|Nome do sinal|Dimensões|Descrição|
|---|---|---|
|`Total Update Deployment Runs`|- Nome de implementação de atualização<br>- Estatuto|Este sinal alerta sobre o estado geral de uma implementação de atualização.|
|`Total Update Deployment Machine Runs`|- Nome de implementação de atualização</br>- Estatuto</br>- Computador-alvo</br>- Id de execução de implementação de atualização|Este sinal alerta para o estado de uma implementação de atualização direcionada a máquinas específicas.|

Para uma dimensão, selecione um valor válido da lista. Se o valor que deseja não estiver **\+** na lista, clique no sinal ao lado da dimensão e escreva o nome personalizado. Em seguida, selecione o valor a procurar. Se pretender selecionar todos os valores para uma dimensão, clique no botão **Select. \* ** Se não escolher um valor para uma dimensão, a Atualização de Gestão ignora essa dimensão.

![Configurar lógica de sinal](./media/automation-tutorial-update-management/signal-logic.png)

Em **Lógica de alerta**, para **Limiar**, introduza **1**. Quando tiver terminado, selecione **Concluído**.

### <a name="alert-details"></a>Detalhes do alerta

Abaixo de **2. Defina detalhes de alerta,** introduza um nome e descrição para o alerta. Defina **Gravidade** para **Informativo (Gravidade 2)** para uma execução com êxito ou **Informativo (Gravidade 1)** para uma execução que falhou.

![Configurar lógica de sinal](./media/automation-tutorial-update-management/define-alert-details.png)

Em **grupos de ação,** selecione **Criar Novo**. Um grupo de ação é um grupo de ações que podem ser utilizadas em vários alertas. As ações podem incluir notificações de e-mail, livros de execução, webhooks e muito mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

No campo de nome do **grupo Action,** insira um nome para o alerta e um nome curto. A Atualização Management utiliza o nome curto no lugar de um nome completo de grupo de ação ao enviar notificações utilizando o grupo especificado.

No âmbito **de Ações**, insira um nome para a ação, como Notificação de **E-mail.** Para **tipo de ação,** selecione **Email/SMS/Push/Voice**. Para **mais detalhes,** selecione **Editar detalhes**.

Na página E-mail/SMS/Push/Voz, introduza um nome. Selecione a caixa de verificação de **e-mail** e, em seguida, introduza um endereço de e-mail válido.

![Configurar um grupo de ações de e-mail](./media/automation-tutorial-update-management/configure-email-action-group.png)

No painel de e-mail/SMS/Push/Voice, clique em **OK**. No painel do grupo de ação Add, clique em **OK**.

Para personalizar o assunto do e-mail de alerta, sob **a regra Criar,** sob **ações personalizadas,** selecione **o assunto do e-mail**. Quanto terminar, selecione **Criar regra de alerta**. O alerta diz-lhe quando uma implementação de atualização for bem sucedida, e quais as máquinas que faziam parte da execução da atualização.

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Em seguida, agende uma implementação que siga o seu agendamento e o período de administração da versão para instalar atualizações. Pode escolher os tipos de atualização para incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

>[!NOTE]
>Agendar uma implementação de atualização cria um recurso de [agenda](shared-resources/schedules.md) ligado ao livro de execução **Patch-MicrosoftOMSComputers** que trata da implementação da atualização nas máquinas-alvo. Se eliminar o recurso de agenda do portal Azure ou utilizar o PowerShell após a criação da implementação, a eliminação quebra a implementação da atualização programada e apresenta um erro ao tentar reconfigurar o recurso de agenda a partir do portal. Só é possível eliminar o recurso de agenda eliminando o calendário de implementação correspondente.  

Para agendar uma nova implementação de atualização para a VM, aceda a **Gestão de atualizações**e, em seguida, selecione **Agendar implementação da atualização**.

Em **Nova implementação de atualização**, especifique as seguintes informações:

* **Nome**: indique um nome exclusivo para a implementação da atualização.

* **Sistema operativo**: selecione o SO de destino para a implementação da atualização.

* **Grupos para atualizar (pré-visualização)**: Defina uma consulta que combine subscrição, grupos de recursos, localizações e tags para construir um grupo dinâmico de VMs Azure para incluir na sua implementação. Para saber mais, consulte [Grupos Dinâmicos.](automation-update-management-groups.md)

* **Máquinas para atualizar:** Selecione uma pesquisa guardada, grupo importado ou escolha **Máquinas** do menu suspenso e selecione máquinas individuais. Se escolher **Máquinas,** a prontidão de cada máquina é mostrada na coluna de prontidão do **agente Atualizar.** Para conhecer os diferentes métodos de criação de grupos informáticos nos registos do Monitor Azure, consulte [grupos informáticos nos registos do Monitor Azure](../azure-monitor/platform/computer-groups.md).

* **Classificação da atualização**: Para cada produto, desmarque todas as classificações de atualização suportadas, mas as que incluirão na sua implementação da atualização. Para este tutorial, deixe todos os tipos selecionados para todos os produtos.

  Os tipos de classificação são:

   |SO  |Tipo  |
   |---------|---------|
   |Windows     | Atualizações críticas</br>Atualizações de segurança</br>Update rollups</br>Pacotes de funcionalidades</br>Service packs</br>Atualizações de definições</br>Ferramentas</br>Atualizações<br>Controlador        |
   |Linux     | Atualizações críticas e de segurança</br>Outras atualizações       |

   Para descrições dos tipos de classificação, consulte classificações de [atualização](automation-view-update-assessments.md#update-classifications).

* **Atualizações para incluir/excluir** - Abre a página Incluir/Excluir. As atualizações a incluir ou excluídas estão em separados, especificando os números de IDENTIFICAção do artigo KB. Ao especificar um ou mais números de IDENTIFICAÇÃO, é necessário remover ou desmarcar todas as classificações com a implementação da atualização. Isto garante que nenhuma outra atualização está incluída no seu pacote de atualizações ao especificar iDs de atualização.

> [!NOTE]
> É importante saber que as exclusões sobrepõem-se às inclusãos. Por exemplo, se definir uma `*`regra de exclusão de , A Gestão de Atualizações não instala patches ou pacotes, uma vez que todos estão excluídos. Os patches excluídos continuam a mostrar como desaparecidos da máquina. Para as máquinas Linux, se incluir um pacote que tenha um pacote dependente que tenha sido excluído, a Update Management não instala o pacote principal.

> [!NOTE]
> Não é possível especificar atualizações que tenham sido substituídos para inclusão com a implementação da atualização.
>

* **Definições da agenda**: o painel Definições da Agenda abre. A hora de início predefinida é 30 minutos depois da hora atual. Pode definir a hora de início para qualquer hora a partir de 10 minutos no futuro.

   Também pode especificar se a implementação ocorre uma vez ou configurar um agendamento periódico. Em **Periodicidade**, selecione **Uma vez**. Deixe o padrão como 1 dia e clique **OK**. Estas entradas estabelecem um horário recorrente.

* **Scripts prévios + Scripts posteriores**: selecione os scripts para executar antes e após a sua implementação. Para saber mais, veja [Gerir Scripts prévios e posteriores](pre-post-scripts.md).

* **Janela de manutenção (minutos)**: deixe o valor predefinido. As janelas de manutenção controlam a quantidade de tempo permitida para que as atualizações possam instalar. Considere os seguintes detalhes ao especificar uma janela de manutenção:

  * As janelas de manutenção controlam quantas atualizações estão instaladas.
  * A Atualização A Gestão não para de instalar novas atualizações se se aproximar do fim de uma janela de manutenção.
  * A Atualização De gestão não encerra atualizações em curso se a janela de manutenção for excedida.
  * Se a janela de manutenção for excedida no Windows, é frequente porque uma atualização de pacote de serviço está a demorar muito tempo a instalar.

  > [!NOTE]
  > Para evitar que as atualizações sejam aplicadas fora de uma janela de manutenção em Ubuntu, reconfigure o pacote Deactualização Não Atendida para desativar atualizações automáticas. Para obter informações sobre como configurar o pacote, consulte [o tópico de Atualizações Automáticas no Guia do Servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Opções**de reiniciar : Utilize para especificar opções para manusear reboots. Estão disponíveis as seguintes opções:
  * Reiniciar se necessário (padrão)
  * Reiniciar sempre
  * Nunca reiniciar
  * Apenas reiniciar - não instala atualizações

> [!NOTE]
> As teclas de registo listadas nas [teclas do Registo utilizadas para gerir](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) o reinício podem causar um evento de reinício se as **opções de Reboot** estiverem definidas para nunca **reiniciar**.

Quando terminar de configurar o horário, clique em **Criar**.

![Painel Definições de Agendamento de Atualizações](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Volta ao dashboard de estado. Selecione **as implementações de Atualização Programada** para mostrar o calendário de implementação que acaba de ser criado.

> [!NOTE]
> A Update Management suporta a implementação de atualizações de primeira parte e patches de pré-descarregamento. Este suporte requer alterações nos sistemas que estão a ser corrigidos. Consulte o [suporte de primeira parte e pré-download](automation-configure-windows-update.md) para aprender a configurar estas definições nos seus sistemas.

Também pode criar implementações de atualizações programáticamente. Para aprender a criar uma implementação de atualização com a Rest API, consulte configurações de [atualização](/rest/api/automation/softwareupdateconfigurations/create)de software - Criar . Há também um livro de amostras que pode usar para criar uma implementação semanal de atualização. Para saber mais sobre este livro de execução, consulte Criar uma implementação semanal de [atualização para um ou mais VMs num grupo](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)de recursos .

## <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualização** em **Gestão de atualizações**. O estado será **Em curso**, se a implementação estiver em execução. Quando a implementação termina com sucesso, o estado muda para **Succeed .** Se houver falhas com uma ou mais atualizações na implementação, o estado é **parcialmente falhado**.

Selecione a implementação da atualização completa para ver o seu painel de instrumentos.

![Dashboard de estado de implementação de atualização para uma implementação específica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Em **Resultados da atualização**, um resumo indica o número total de atualizações e os resultados de implementação da VM. A tabela à direita mostra uma divisão detalhada de cada atualização e os resultados da instalação.

Os valores disponíveis são:

* **Não tentada**: A atualização não foi instalada porque não havia tempo disponível, com base na duração da janela de manutenção definida.
* **Sucesso**: A atualização foi bem sucedida.
* **Falha :** A atualização falhou.

Selecione **Todos os registos** para ver todas as entradas de registo que a implementação criou.

Selecione **Saída** para ver o fluxo de tarefas do runbook responsável pela gestão da implementação da atualização na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre os erros da implementação.

Quando a sua implementação de atualização for bem sucedida, recebe um e-mail de confirmação semelhante ao seguinte:

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
> [Solução de Gestão de Atualizações](automation-update-management.md)