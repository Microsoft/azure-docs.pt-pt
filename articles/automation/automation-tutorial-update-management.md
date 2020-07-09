---
title: Gerir atualizações e patches para os seus VMs Azure em Azure Automation
description: Este artigo diz como utilizar a Gestão de Atualização para gerir atualizações e patches para os seus VMs Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 79cffa7aedd0fc04dd4a747ef28bc67cacf37905
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84204893"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Gerir atualizações e patches para os seus VMs Azure

Este artigo descreve como pode utilizar a função [de Gestão](automation-update-management.md) de Azure Automation Update Management para gerir atualizações e patches para os seus VMs Azure. Para obter informações sobre preços, veja [Preços de Automatização para Gestão de Atualizações](https://azure.microsoft.com/pricing/details/automation/).

> [!NOTE]
> A Update Management suporta a implementação de atualizações de primeira parte e o pré-download de patches. Este suporte requer alterações nos sistemas que estão a ser corrigidos. Consulte [as definições de Configuração da Atualização do Windows para a Gestão de Atualizações de Automação Azure](automation-configure-windows-update.md) para aprender a configurar estas definições nos seus sistemas.

Antes de utilizar os procedimentos deste artigo, certifique-se de que ativou a Gestão de Atualização nos seus VMs utilizando uma destas técnicas:

* [Ativar a Gestão de Atualizações a partir de uma conta de Automatização](automation-onboard-solutions-from-automation-account.md)
* [Ativar a Gestão de Atualização navegando no portal Azure](automation-onboard-solutions-from-browse.md)
* [Ativar a Gestão de Atualizações a partir de um runbook](automation-onboard-solutions.md)
* [Ativar a Gestão de Atualizações a partir de uma VM do Azure](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitar o âmbito de implantação

A Atualização Management utiliza uma configuração de âmbito dentro do espaço de trabalho para direcionar os computadores para receber atualizações. Para obter mais informações, consulte [o âmbito de implementação da Gestão de Atualização de Limites](automation-scope-configurations-update-management.md).

## <a name="view-update-assessment"></a>Ver avaliação de atualizações

Para visualizar uma avaliação de atualização:

1. Na sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização**. 

2. As atualizações para o seu ambiente estão listadas na página de gestão de Atualização. Se alguma atualização for identificada como desaparecida, uma lista de atualizações em falta é mostrada no separador **Atualizações Desaparecidas.**

3. No **link Informação**, selecione o link para uma atualização para abrir o artigo de suporte que lhe dá informações importantes sobre a atualização.

    ![Ver o estado de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. Clique em qualquer outro lugar da atualização para abrir o painel de pesquisa de registo. A consulta para a pesquisa de registos está predefinida para essa atualização específica. Pode modificar esta consulta ou criar a sua própria consulta para visualizar informações detalhadas.

    ![Ver o estado de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurar alertas

Siga os passos abaixo para configurar alertas para informá-lo sobre o estado de uma implementação de atualização:

1. Na sua conta de Automação, vá a **Alertas** em **Monitorização**e, em seguida, clique em **Nova regra de alerta**.

2. Na página 'Criar' regra de alerta, a sua conta Demôm automação já está selecionada como recurso. Se quiser alterá-lo, clique em **Editar recurso**. 

3. Na página Selecione uma página de recursos, escolha Contas de **Automação** do Menu Filtro por tipo **de recurso.** 

4. Selecione a conta Automation that you want to use e, em seguida, clique em **Fazer**.

5. Clique **em Adicionar a condição** para selecionar o sinal apropriado para a sua implementação de atualização. A tabela seguinte mostra os detalhes dos dois sinais disponíveis.

    |Nome do sinal|Dimensões|Description
    |---|---|---|
    |`Total Update Deployment Runs`|- Nome de implementação de atualização<br>- Estado    |Alertas sobre o estado geral de uma implementação de atualização.|
    |`Total Update Deployment Machine Runs`|- Nome de implementação de atualização</br>- Estado</br>- Computador-alvo</br>- Atualizar o ID de execução de implementação    |Alertas sobre o estado de uma atualização direcionada a máquinas específicas.|

6. Para uma dimensão, selecione um valor válido da lista. Se o valor que deseja não estiver na lista, clique **\+** ao lado da dimensão e escreva no nome personalizado. Em seguida, selecione o valor a procurar. Se pretender selecionar todos os valores para uma dimensão, clique no botão **Selecione. \* ** Se não escolher um valor para uma dimensão, a Gestão de Atualização ignora essa dimensão.

    ![Configurar lógica de sinal](./media/automation-tutorial-update-management/signal-logic.png)

7. Em **lógica de alerta,** introduza valores nos campos de **agregação** e **limiar** de tempo e clique em **'Fazer'.**

8. No painel seguinte, insira um nome e uma descrição para o alerta.

9. Desaprova o campo **Severidade** para **Informational (Sev 2)** para uma execução bem sucedida ou **informativo (Sev 1)** para uma execução falhada.

    ![Configurar lógica de sinal](./media/automation-tutorial-update-management/define-alert-details.png)

10. Clique em **Sim** ou **Não,** dependendo da forma como pretende ativar a regra de alerta.

11. Se não quiser ter alertas para esta regra, selecione **Alertas de Supressão**.

## <a name="configure-action-groups-for-your-alerts"></a>Configurar grupos de ação para os seus alertas

Uma vez configurados os alertas, pode criar um grupo de ação, que é um grupo de ações a utilizar em vários alertas. As ações podem incluir notificações de e-mail, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

1. Selecione um alerta e, em seguida, **selecione Criar Novos** em **Grupos de Ação**. 

2. Insira um nome completo e um nome curto para o grupo de ação. A Atualização Gestão utiliza o nome curto ao enviar notificações utilizando o grupo especificado.

3. Em **Ações**, insira um nome que especifica a ação, por exemplo, **Notificação por E-mail**. 

4. Para **o Tipo de Ação**, selecione o tipo apropriado, por exemplo, **Email/SMS/Push/Voice**. 

5. Clique **em Editar detalhes.**

6. Preencha o painel para o seu tipo de ação. Por exemplo, se utilizar **o Email/SMS/Push/Voice,** insira um nome de ação, selecione a caixa de verificação **de e-mail,** introduza um endereço de e-mail válido e, em seguida, clique **em OK**.

    ![Configurar um grupo de ações de e-mail](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. No painel de grupo de ação Add, clique **em OK**.

8. Para um e-mail de alerta, pode personalizar o tema do e-mail. **Selecione Personalizar as ações** sob **regra Criar**e, em seguida, selecione **e-mail assunto**. 

9. Quando terminar, clique em **Criar regra de alerta**. 

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Agendar uma implementação de atualização cria um recurso de [agenda](shared-resources/schedules.md) ligado ao runbook **Patch-MicrosoftOMSComputers** que trata da implementação da atualização nas máquinas-alvo. Tem de agendar uma implementação que siga o seu horário de lançamento e a janela de serviço para instalar atualizações. Pode escolher os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

>[!NOTE]
>Se eliminar o recurso de agenda do portal Azure ou utilizar o PowerShell após a criação da implementação, a eliminação quebra a implementação da atualização programada e apresenta um erro quando tenta reconfigurar o recurso de agenda do portal. Só é possível eliminar o recurso de agenda, eliminando o calendário de implementação correspondente.  

Para agendar uma nova implementação de atualização:

1. Na sua conta Demômes, vá à **gestão de Atualização** sob **gestão de Atualização**e, em seguida, selecione **a implementação da atualização da Agenda**.

2. Em **Nova implementação de atualização,** utilize o campo **Nome** para introduzir um nome único para a sua implementação.

3. Selecione o sistema operativo para o alvo para a implementação da atualização.

4. Nos **Grupos para atualizar (pré-visualização)** região, defina uma consulta que combine subscrição, grupos de recursos, localizações e tags para construir um grupo dinâmico de VMs Azure para incluir na sua implementação. Para saber mais, consulte [Utilize grupos dinâmicos com a Gestão de Atualização.](automation-update-management-groups.md)

5. Nas **Máquinas para atualizar a** região, selecione uma pesquisa guardada, um grupo importado ou escolha **Máquinas** do menu suspenso e selecione máquinas individuais. Com esta opção, pode ver a prontidão do agente Log Analytics para cada máquina. Para conhecer os diferentes métodos de criação de grupos informáticos nos registos do Monitor Azure, consulte [grupos de computador nos registos do Azure Monitor](../azure-monitor/platform/computer-groups.md).

6. Utilize a região **de classificações de atualização** para especificar [classificações de atualização](automation-view-update-assessments.md#work-with-update-classifications) para produtos. Para cada produto, desmarcar todas as classificações de atualização suportadas, mas as que incluir na sua implementação de atualização.

7. Utilize a região **de atualizações Incluir/excluir** para selecionar atualizações específicas para implementação. A página Incluir/Excluir apresenta as atualizações por números de ID de artigo do KB para incluir ou excluir. 
    
   > [!IMPORTANT]
   > Lembre-se que as exclusões substituem as inclusãos. Por exemplo, se definir uma regra de exclusão de , A Gestão de `*` Atualização exclui todos os patches ou pacotes da instalação. Os remendos excluídos ainda mostram como desaparecidos das máquinas. Para as máquinas Linux, se incluir um pacote que tem um pacote dependente que foi excluído, a Update Management não instala o pacote principal.

   > [!NOTE]
   > Não é possível especificar atualizações que tenham sido substituídos para incluir na implementação da atualização.

8. Selecione **definições de agendamento**. A hora de início predefinida é 30 minutos depois da hora atual. Pode definir a hora de início para qualquer hora a partir de 10 minutos no futuro.

9. Utilize o campo **de Recorrência** para especificar se a implementação ocorre uma vez ou utiliza um horário recorrente e, em seguida, clique em **OK**.

10. Na região **pré-scripts + post-scripts (Pré-visualização),** selecione os scripts para executar antes e depois da sua implantação. Para saber mais, consulte [Gerir pré-scripts e post-scripts](pre-post-scripts.md).
    
11. Utilize o campo **de janela de manutenção (minutos)** para especificar o tempo permitido para as atualizações serem instaladas. Considere os seguintes detalhes ao especificar uma janela de manutenção:

    * As janelas de manutenção controlam quantas atualizações estão instaladas.
    * A Atualização A Gestão não para de instalar novas atualizações se o fim de uma janela de manutenção estiver a aproximar-se.
    * A Atualização A Gestão não termina atualizações em curso se a janela de manutenção for excedida.
    * Se a janela de manutenção for excedida no Windows, é frequentemente porque uma atualização do pacote de serviços está a demorar muito tempo a instalar.

    > [!NOTE]
    > Para evitar que sejam aplicadas atualizações fora de uma janela de manutenção em Ubuntu, reconfigure a `Unattended-Upgrade` embalagem para desativar atualizações automáticas. Para obter informações sobre como configurar o pacote, consulte o [tópico de atualizações automáticas no Guia do Servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. Utilize o campo **de opções 'Reboot'** para especificar a forma de lidar com as reinicializações durante a implementação. Estão disponíveis as seguintes opções: 
    * Reiniciar se necessário (predefinição)
    * Reiniciar sempre
    * Nunca reiniciar
    * Apenas reiniciar; esta opção não instala atualizações

    > [!NOTE]
    > As teclas de registo listadas nas [teclas de registo utilizadas para gerir o reinício](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) podem causar um evento de reinicialização se **as opções de Reboot** estiverem **definidas**para Nunca reiniciar .

13. Quando terminar de configurar o calendário de implementação, clique em **Criar**.

    ![Painel Definições de Agendamento de Atualizações](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. Volta ao dashboard de estado. **Selecione as implementações de Atualização Programada** para mostrar o calendário de implementação que criou.

## <a name="schedule-an-update-deployment-programmatically"></a>Agendar uma atualização programática

Para aprender a criar uma implementação de atualização com a API REST, consulte [configurações de atualização de software - Criar](/rest/api/automation/softwareupdateconfigurations/create). 

Pode utilizar um livro de amostras para criar uma atualização semanal. Para saber mais sobre este livro de bordo, consulte [Criar uma atualização semanal para um ou mais VMs num grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Verificar o estado da implementação

Após o início da implementação programada, pode ver o seu estado no **separador 'Actualização'** sob **a gestão de Atualização.** O estado será **Em curso**, se a implementação estiver em execução. Quando a implantação termina com sucesso, o estado muda para **Sucesso**. Se houver falhas com uma ou mais atualizações na implementação, o estado está **parcialmente falhado**.

## <a name="view-results-of-a-completed-update-deployment"></a>Ver resultados de uma implementação de atualização concluída

Quando a colocação estiver terminada, pode selecioná-la para ver o seu painel de instrumentos.

![Dashboard de estado de implementação de atualização para uma implementação específica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Nos **resultados da Atualização,** um resumo fornece o número total de atualizações e resultados de implantação nos VMs-alvo. A tabela à direita apresenta uma desagregação detalhada das atualizações e dos resultados da instalação para cada um.

Os valores disponíveis são:

* **Não tentada** - A atualização não foi instalada porque não havia tempo suficiente disponível, com base na duração da janela de manutenção definida.
* **Não selecionada** - A atualização não foi selecionada para implantação.
* **Conseguiu** - A atualização foi bem sucedida.
* **Falhou** - A atualização falhou.

Selecione **Todos os registos** para ver todas as entradas de registo que a implementação criou.

Selecione **Output** para ver o fluxo de trabalho do runbook responsável pela gestão da implementação da atualização nos VMs-alvo.

Selecione **Erros** para ver informações detalhadas sobre os erros da implementação.

## <a name="view-the-deployment-alert"></a>Ver o alerta de implantação

Quando a sua atualização estiver concluída, recebe o alerta especificado durante a configuração para a implementação. Por exemplo, aqui está um e-mail confirmando um patch.

![Configurar o grupo de ação de e-mail](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre configurações de âmbito, consulte [o âmbito de implementação da Gestão da Atualização de Limites](automation-scope-configurations-update-management.md).
* Se necessitar de pesquisar registos armazenados no seu espaço de trabalho Log Analytics, consulte [as pesquisas de Registo nos registos do Azure Monitor](../log-analytics/log-analytics-log-searches.md).
* Se terminar com as implementações, consulte [o espaço de trabalho desvinculação da conta de Automação para a Gestão de Atualização](automation-unlink-workspace-update-management.md).
* Para eliminar os seus VMs da Gestão de Atualização, consulte [remover VMs da Gestão de Atualização](automation-remove-vms-from-update-management.md).
* Para resolver os erros gerais de Gestão de Atualização, consulte [problemas de Gestão de Atualização de Resolução de Problemas](troubleshoot/update-management.md).
* Para resolver problemas com o agente de atualização do Windows, consulte [problemas de agente de atualização do Windows de resolução de resolução](troubleshoot/update-agent-issues.md)de problemas .
* Para resolver problemas com o agente de atualização Linux, consulte [problemas de agente de atualização do Linux de resolução](troubleshoot/update-agent-issues-linux.md)de resolução de problemas .