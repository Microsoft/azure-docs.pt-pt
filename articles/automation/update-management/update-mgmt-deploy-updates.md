---
title: Como criar implementações de atualização para a Azure Automation Update Management
description: Este artigo descreve como agendar as implementações de atualização e rever o seu estado.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 2a81376b284e0d1df84a69b969335c0e63999a00
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450687"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Como implementar atualizações e rever resultados

Este artigo descreve como agendar uma implementação de atualização e rever o processo após a implementação estar concluída.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Agendar uma implementação de atualização cria um recurso de [agenda](../shared-resources/schedules.md) ligado ao runbook **Patch-MicrosoftOMSComputers** que trata da implementação da atualização nas máquinas-alvo. Tem de agendar uma implementação que siga o seu horário de lançamento e a janela de serviço para instalar atualizações. Pode escolher os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

>[!NOTE]
>Se eliminar o recurso de agenda do portal Azure ou utilizar o PowerShell após a criação da implementação, a eliminação quebra a implementação da atualização programada e apresenta um erro quando tenta reconfigurar o recurso de agenda do portal. Só é possível eliminar o recurso de agenda, eliminando o calendário de implementação correspondente.  

Para agendar uma nova implementação de atualização:

1. Na sua conta Demômes, vá à **gestão de Atualização** sob **gestão de Atualização**e, em seguida, selecione **a implementação da atualização da Agenda**.

2. Sob **nova implementação de atualização,** no campo **Nome** introduza um nome único para a sua implementação.

3. Selecione o sistema operativo para o alvo para a implementação da atualização.

4. Nos **Grupos para atualizar (pré-visualização)** região, defina uma consulta que combine subscrição, grupos de recursos, localizações e tags para construir um grupo dinâmico de VMs Azure para incluir na sua implementação. Para saber mais, consulte [Utilize grupos dinâmicos com a Gestão de Atualização.](update-mgmt-groups.md)

5. Nas **Máquinas para atualizar a** região, selecione uma pesquisa guardada, um grupo importado ou escolha **Máquinas** do menu suspenso e selecione máquinas individuais. Com esta opção, pode ver a prontidão do agente Log Analytics para cada máquina. Para conhecer os diferentes métodos de criação de grupos informáticos nos registos do Monitor Azure, consulte [grupos de computador nos registos do Azure Monitor](../../azure-monitor/platform/computer-groups.md).

6. Utilize a região **de classificações de atualização** para especificar [classificações de atualização](update-mgmt-view-update-assessments.md#work-with-update-classifications) para produtos. Para cada produto, desmarcar todas as classificações de atualização suportadas, mas as que incluir na sua implementação de atualização.

7. Utilize a região **de atualizações Incluir/excluir** para selecionar atualizações específicas para implementação. A página Incluir/Excluir apresenta as atualizações por números de ID de artigo do KB para incluir ou excluir.
    
   > [!IMPORTANT]
   > Lembre-se que as exclusões substituem as inclusãos. Por exemplo, se definir uma regra de exclusão de , A Gestão de `*` Atualização exclui todos os patches ou pacotes da instalação. Os remendos excluídos ainda mostram como desaparecidos das máquinas. Para as máquinas Linux, se incluir um pacote que tem um pacote dependente que foi excluído, a Update Management não instala o pacote principal.

   > [!NOTE]
   > Não é possível especificar atualizações que tenham sido substituídos para incluir na implementação da atualização.

8. Selecione **definições de agendamento**. A hora de início predefinida é 30 minutos depois da hora atual. Pode definir a hora de início para qualquer hora a partir de 10 minutos no futuro.

9. Utilize o campo **de Recorrência** para especificar se a implementação ocorre uma vez ou utiliza um horário recorrente e, em seguida, selecione **OK**.

10. Na região **pré-scripts + post-scripts (Pré-visualização),** selecione os scripts para executar antes e depois da sua implantação. Para saber mais, consulte [Gerir pré-scripts e post-scripts](update-mgmt-pre-post-scripts.md).
    
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

13. Quando terminar de configurar o calendário de implementação, selecione **Criar**.

    ![Painel Definições de Agendamento de Atualizações](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. Volta ao dashboard de estado. **Selecione as implementações de Atualização Programada** para mostrar o calendário de implementação que criou.

## <a name="schedule-an-update-deployment-programmatically"></a>Agendar uma atualização programática

Para aprender a criar uma implementação de atualização com a API REST, consulte [configurações de atualização de software - Criar](/rest/api/automation/softwareupdateconfigurations/create).

Pode utilizar um livro de amostras para criar uma atualização semanal. Para saber mais sobre este livro de bordo, consulte [Criar uma atualização semanal para um ou mais VMs num grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Verificar o estado da implementação

Após o início da implementação programada, pode ver o seu estado no **separador 'Actualização'** sob **a gestão de Atualização.** O estado será **Em curso**, se a implementação estiver em execução. Quando a implantação termina com sucesso, o estado muda para **Sucesso**. Se houver falhas com uma ou mais atualizações na implementação, o estado está **parcialmente falhado**.

## <a name="view-results-of-a-completed-update-deployment"></a>Ver resultados de uma implementação de atualização concluída

Quando a implementação estiver terminada, pode selecioná-la para ver os seus resultados.

[![Atualizar o painel de estado de implementação para uma implementação específica](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

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

Para aprender a criar alertas para o notificar sobre os resultados da atualização, consulte [criar alertas para a Gestão de Atualização.](update-mgmt-configure-alerts.md)