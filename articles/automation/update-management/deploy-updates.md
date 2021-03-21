---
title: Como criar implementações de atualização para a Azure Automation Update Management
description: Este artigo descreve como agendar as implementações de atualização e rever o seu estado.
services: automation
ms.subservice: update-management
ms.date: 03/19/2021
ms.topic: conceptual
ms.openlocfilehash: 6d35d6b49ab72d8aa7b25506011147ab624273fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669683"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Como implementar atualizações e rever resultados

Este artigo descreve como agendar uma implementação de atualização e rever o processo após a implementação estar concluída. Pode configurar uma implementação de atualização a partir de uma máquina virtual Azure selecionada, a partir do servidor ativado pelo Arco selecionado, ou da conta Automation em todas as máquinas e servidores configurados.

Em cada cenário, a implementação cria alvos que selecionou máquina ou servidor, ou no caso de criar uma implementação a partir da sua conta Dem automação, pode visar uma ou mais máquinas. Quando agenda uma atualização a partir de um servidor ativado por Azure VM ou Arco, os passos são os mesmos que a implantação a partir da sua conta Demôm automação, com as seguintes exceções:

* O sistema operativo é automaticamente pré-selecionado com base no SO da máquina
* A máquina-alvo a atualizar é definida para se direcionar automaticamente
* Ao configurar o horário, pode especificar **Update now**, ocorre uma vez, ou utiliza um horário recorrente.

> [!IMPORTANT]
> Ao criar uma implementação de atualização, aceita os termos dos Termos de Licença de Software (EULA) fornecidos pela empresa que oferece atualizações para o seu sistema operativo.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Agendar uma implementação de atualização cria um recurso de [agenda](../shared-resources/schedules.md) ligado ao runbook **Patch-MicrosoftOMSComputers** que trata da implementação da atualização na máquina ou máquinas-alvo. Tem de agendar uma implementação que siga o seu horário de lançamento e a janela de serviço para instalar atualizações. Pode escolher os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações críticas ou de segurança e excluir rollups de atualização.

>[!NOTE]
>Se eliminar o recurso de agenda do portal Azure ou utilizar o PowerShell após a criação da implementação, a eliminação quebra a implementação da atualização programada e apresenta um erro quando tenta reconfigurar o recurso de agenda do portal. Só é possível eliminar o recurso de agenda, eliminando o calendário de implementação correspondente.  

Para agendar uma nova implementação de atualização, execute os seguintes passos. Dependendo do recurso selecionado (isto é, conta de automação, servidor ativado pelo Arco, Azure VM), os passos abaixo aplicam-se a todos com pequenas diferenças enquanto configuram o calendário de implementação.

1. No portal, para agendar uma implantação para:

   * Uma ou mais máquinas, navegue para **contas de Automação** e selecione a sua conta Demôm automação com a Gestão de Atualização ativada na lista.
   * Para um Azure VM, navegue para **máquinas Virtuais** e selecione o seu VM da lista.
   * Para um servidor ativado pelo Arc, navegue para **Servers - Azure Arc** e selecione o seu servidor da lista.

2. Dependendo do recurso selecionado, para navegar para a Gestão de Atualização:

   * Se selecionou a sua conta Demômes, vá à **gestão de Atualização** sob **gestão de Atualização** e, em seguida, selecione **a implementação da atualização da Agenda**.
   * Se selecionou um Azure VM, vá a **atualizações do Anfitrião +** e, em seguida, selecione **Ir para a Gestão de Atualização**.
   * Se selecionou um servidor ativado pelo Arco, vá à **Gestão de Atualização** e, em seguida, selecione **a implementação da atualização da agenda**.

3. Sob **nova implementação de atualização,** no campo **Nome** introduza um nome único para a sua implementação.

4. Selecione o sistema operativo para o alvo para a implementação da atualização.

    > [!NOTE]
    > Esta opção não está disponível se selecionar um servidor Azure VM ou Arc ativado. O sistema operativo é automaticamente identificado.

5. Nos **Grupos para atualizar a** região, defina uma consulta que combine subscrições, grupos de recursos, localizações e tags para construir um grupo dinâmico de VMs Azure para incluir na sua implementação. Para saber mais, consulte [Utilize grupos dinâmicos com a Gestão de Atualização.](configure-groups.md)

    > [!NOTE]
    > Esta opção não está disponível se selecionar um servidor Azure VM ou Arc ativado. A máquina é automaticamente direcionada para a implantação programada.

6. Nas **Máquinas para atualizar a** região, selecione uma pesquisa guardada, um grupo importado ou escolha **Máquinas** do menu suspenso e selecione máquinas individuais. Com esta opção, pode ver a prontidão do agente Log Analytics para cada máquina. Para conhecer os diferentes métodos de criação de grupos informáticos nos registos do Monitor Azure, consulte [grupos de computador nos registos do Azure Monitor](../../azure-monitor/logs/computer-groups.md). Pode incluir até um máximo de 1000 máquinas numa atualização programada.

    > [!NOTE]
    > Esta opção não está disponível se selecionar um servidor Azure VM ou Arc ativado. A máquina é automaticamente direcionada para a implantação programada.

7. Utilize a região **de classificações de atualização** para especificar [classificações de atualização](view-update-assessments.md#work-with-update-classifications) para produtos. Para cada produto, desmarcar todas as classificações de atualização suportadas, mas as que incluir na sua implementação de atualização.

    Se a sua implementação for destinada a aplicar apenas um conjunto selecionado de atualizações, é necessário desmarcar todas as classificações de atualização pré-selecionadas ao configurar a opção **de atualizações Incluir/excluir,** conforme descrito no passo seguinte. Isto garante apenas que as atualizações especificadas para *incluir* nesta implementação são instaladas nas máquinas-alvo.

8. Utilize a região **de atualizações Incluir/excluir** para adicionar ou excluir atualizações selecionadas da implementação. Na página **Incluir/Excluir,** introduz os números de ID do artigo KB para incluir ou excluir para atualizações do Windows. Para distros Linux suportados, especifique o nome do pacote.

   > [!IMPORTANT]
   > Lembre-se que as exclusões substituem as inclusãos. Por exemplo, se definir uma regra de exclusão de , A Gestão de `*` Atualização exclui todos os patches ou pacotes da instalação. Os remendos excluídos ainda mostram como desaparecidos das máquinas. Para as máquinas Linux, se incluir um pacote que tem um pacote dependente que foi excluído, a Update Management não instala o pacote principal.

   > [!NOTE]
   > Não é possível especificar atualizações que tenham sido substituídos para incluir na implementação da atualização.

9. Selecione **definições de agendamento**. A hora de início predefinida é 30 minutos depois da hora atual. Pode definir a hora de início para qualquer hora a partir de 10 minutos no futuro.

    > [!NOTE]
    > Esta opção é diferente se selecionar um servidor ativado pelo Arc. Pode selecionar **Update agora** ou uma hora de início 20 minutos para o futuro.

10. Utilize a **Recorrência** para especificar se a implementação ocorre uma vez ou utiliza um horário recorrente e, em seguida, selecione **OK**.

11. Na região **pré-scripts + Post-scripts,** selecione os scripts para executar antes e depois da sua implantação. Para saber mais, consulte [Gerir pré-scripts e post-scripts](pre-post-scripts.md).

12. Utilize o campo **de janela de manutenção (minutos)** para especificar o tempo permitido para as atualizações serem instaladas. Considere os seguintes detalhes ao especificar uma janela de manutenção:

    * As janelas de manutenção controlam quantas atualizações estão instaladas.
    * A Atualização A Gestão não para de instalar novas atualizações se o fim de uma janela de manutenção estiver a aproximar-se.
    * A Atualização A Gestão não termina atualizações em curso se a janela de manutenção for excedida. Quaisquer atualizações restantes a instalar não são tentadas. Se isto estiver a acontecer de forma consistente, deve reavaliar a duração da sua janela de manutenção.
    * Se a janela de manutenção for excedida no Windows, é frequentemente porque uma atualização do pacote de serviços está a demorar muito tempo a instalar.

    > [!NOTE]
    > Para evitar que sejam aplicadas atualizações fora de uma janela de manutenção em Ubuntu, reconfigure a `Unattended-Upgrade` embalagem para desativar atualizações automáticas. Para obter informações sobre como configurar o pacote, consulte o [tópico de atualizações automáticas no Guia do Servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. Utilize o campo **de opções 'Reboot'** para especificar a forma de lidar com as reinicializações durante a implementação. Estão disponíveis as seguintes opções: 
    * Reiniciar se necessário (predefinição)
    * Reiniciar sempre
    * Nunca reiniciar
    * Apenas reiniciar; esta opção não instala atualizações

    > [!NOTE]
    > As teclas de registo listadas nas [teclas de registo utilizadas para gerir o reinício](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) podem causar um evento de reinicialização se **as opções de Reboot** estiverem **definidas** para Nunca reiniciar .

14. Quando terminar de configurar o calendário de implementação, selecione **Criar**.

    ![Painel Definições de Agendamento de Atualizações](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Quando terminar de configurar o calendário de implementação de um servidor ativado pelo Arco selecionado, selecione **Review + create**.

15. Volta ao dashboard de estado. Selecione **horários de implementação** para mostrar o calendário de implementação que criou. Um máximo de 500 horários estão listados. Se tiver mais de 500 horários e pretender rever a lista completa, consulte as Configurações de [Atualização](/rest/api/automation/softwareupdateconfigurations/list) de Software - Método API da Lista REST. Especifique a versão API 2019-06-01 ou superior.

## <a name="schedule-an-update-deployment-programmatically"></a>Agendar uma atualização programática

Para aprender a criar uma implementação de atualização com a API REST, consulte [configurações de atualização de software - Criar](/rest/api/automation/softwareupdateconfigurations/create).

Pode utilizar um livro de amostras para criar uma atualização semanal. Para saber mais sobre este livro de bordo, consulte [Criar uma atualização semanal para um ou mais VMs num grupo de recursos](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group).

## <a name="check-deployment-status"></a>Verificar o estado da implementação

Após o início da sua implementação programada, pode ver o seu estado no **separador Histórico** sob **gestão de Atualização.** O estado será **Em curso**, se a implementação estiver em execução. Quando a implantação termina com sucesso, o estado muda para **Sucesso**. Se houver falhas com uma ou mais atualizações na implementação, o estado é **Falhado**.

## <a name="view-results-of-a-completed-update-deployment"></a>Ver resultados de uma implementação de atualização concluída

Quando a implementação estiver terminada, pode selecioná-la para ver os seus resultados.

[![Atualizar o painel de estado de implementação para uma implementação específica](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Nos **resultados da Atualização,** um resumo fornece o número total de atualizações e resultados de implantação nos VMs-alvo. A tabela à direita apresenta uma desagregação detalhada das atualizações e dos resultados da instalação para cada um.

Os valores disponíveis são:

* **Não tentada** - A atualização não foi instalada porque não havia tempo suficiente disponível, com base na duração da janela de manutenção definida.
* **Não selecionada** - A atualização não foi selecionada para implantação.
* **Conseguiu** - A atualização foi bem sucedida.
* **Falhou** - A atualização falhou.

Selecione **Todos os registos** para ver todas as entradas de registo que a implementação criou.

Selecione **Output** para ver o fluxo de trabalho do runbook responsável pela gestão da implementação da atualização nos VMs-alvo.

Selecione **Erros** para ver informações detalhadas sobre os erros da implementação.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a criar alertas para o notificar sobre os resultados da atualização, consulte [criar alertas para a Gestão de Atualização.](configure-alerts.md)
* Para resolver os erros gerais de Gestão de Atualização, consulte [problemas de Gestão de Atualização de Resolução de Problemas](../troubleshoot/update-management.md).
