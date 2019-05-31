---
title: Monitorizar alertas de cópias de segurança para máquinas virtuais do Azure
description: Monitorizar eventos e alertas a partir de tarefas de cópia de segurança de máquina virtual do Azure. Envie um e-mail com base em alertas.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: 350e13a4b1c01329bef1ec270af5ba007cd788aa
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399753"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Monitorizar alertas de cópias de segurança de máquinas virtuais do Azure

Os alertas são as respostas do serviço que foi cumprido ou ultrapassado um limiar de evento. Saber quando o início de problemas pode ser essencial para manter os custos de negócio para baixo. Alertas normalmente não ocorrem com base numa agenda e, portanto, é útil saber, logo que possível, depois de ocorrerem de alertas. Por exemplo, quando uma tarefa de cópia de segurança ou restauro falhar, um alerta ocorre dentro de cinco minutos da falha. No dashboard do cofre, o mosaico alertas de cópia de segurança apresenta os eventos de críticas e de nível de aviso. Nas definições de alertas de cópia de segurança, pode ver todos os eventos. Mas o que fazer se um alerta ocorre quando está trabalhando numa questão separada? Se não sabe quando o alerta acontece, pode ser uma inconveniência secundária ou poderia comprometer os dados. Para certificar-se de que as pessoas certas estão cientes de um alerta - quando ocorrer, configure o serviço para enviar notificações de alerta por e-mail. Para obter detalhes sobre como configurar notificações por e-mail, consulte [configurar notificações](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Como faço para localizar informações sobre os alertas?

Para ver informações sobre o evento que acionou um alerta, tem de abrir a seção de alertas de cópia de segurança. Existem duas formas de abrir a seção de alertas de cópia de segurança: os alertas de cópia de segurança a partir de mosaico no dashboard do cofre ou da seção de alertas e eventos.

Para abrir o painel de alertas de cópia de segurança a partir do mosaico alertas de cópia de segurança:

* Sobre o **alertas de cópia de segurança** mosaico no dashboard do cofre, clique em **crítico** ou **aviso** para ver os eventos operacionais para obter esse nível de gravidade.

    ![Mosaico alertas de cópia de segurança](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Para abrir o painel de alertas de cópia de segurança da seção de alertas e eventos:

1. A partir do dashboard do cofre, clique em **todas as definições**. ![Botão de todas as definições](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Sobre o **configurações** painel, clique em **alertas e eventos**. ![Botão de alertas e eventos](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Sobre o **eventos e alertas** painel, clique em **alertas de cópia de segurança**. ![Botão de alertas de cópia de segurança](./media/backup-azure-monitor-vms/backup-alerts.png)

    O **alertas de cópia de segurança** seção abre e apresenta os alertas filtrados.

    ![Mosaico alertas de cópia de segurança](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Para ver informações detalhadas sobre um alerta específico, na lista de eventos, clique no alerta para abrir o respetivo **detalhes** secção.

    ![Detalhe do evento](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Para personalizar os atributos apresentados na lista, consulte [ver os atributos de eventos adicionais](backup-azure-monitor-vms.md)

## <a name="configure-notifications"></a>Configurar notificações

 Pode configurar o serviço para enviar notificações por e-mail para os alertas que ocorreram durante a última hora, ou quando ocorrem determinados tipos de eventos.

Para configurar notificações por e-mail para alertas

1. No menu alertas de cópia de segurança, clique em **configurar notificações**

    ![Menu de alertas de cópia de segurança](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Abre a secção de notificações de configuração.

    ![Configurar o painel de notificações](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Na secção de notificações de configuração, para notificações por E-Mail, clique em **no**.

    Os destinatários e a gravidade caixas de diálogo tem uma estrela junto a eles, porque essa informação é necessária. Indique, pelo menos, um endereço de e-mail e selecione a gravidade de pelo menos um.
3. Na **destinatários (E-Mail)** caixa de diálogo, escreva os endereços de e-mail para quem receber as notificações. Utilize o formato: username@domainname.com. Separe vários endereços de e-mail com um ponto e vírgula (;).
4. Na **notificar** área, escolha **por alerta** para enviar a notificação quando ocorre o alerta especificado, ou **resumo de hora a hora** para enviar um resumo da última hora.
5. Na **gravidade** caixa de diálogo, selecione um ou mais níveis que deseja acionar a notificação por e-mail.
6. Clique em **Guardar**.

### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Que tipos de alertas estão disponíveis para cópia de segurança de VM de IaaS do Azure

   | Nível de alerta | Alertas enviados |
   | --- | --- |
   | Crítico | Falha de cópia de segurança, falha na recuperação |
   | Aviso | Atualmente, não existem alertas de aviso estão disponíveis para cópias de segurança de VM do Azure (por exemplo: algumas gravadores falharam ao criar um instantâneo) |
   | Informativo | Atualmente, não existem alertas informativos estão disponíveis para cópia de segurança de VM do Azure |

### <a name="situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Situações em que o e-mail não é enviado, mesmo que as notificações estão configuradas

Existem situações em que não é enviado um alerta, mesmo que as notificações foram corretamente configuradas. No e-mail de situações seguintes não são enviadas notificações para evitar o ruído de alerta:

* Se as notificações estão configuradas para resumo de hora a hora e um alerta for emitido e resolvido dentro da hora.
* A tarefa é cancelada.
* Uma tarefa de cópia de segurança é acionada e, em seguida, falha, e está em curso outra tarefa de cópia de segurança.
* Inicia uma tarefa de cópia de segurança agendada para uma VM habilitados no Resource Manager, mas a VM já não existe.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Utilizar registos de atividade para receber notificações para cópias de segurança com êxito

> [!NOTE]
> Foi movido para um novo modelo de bombeando os registos de atividade de cópia de segurança do Azure nos cofres dos serviços de recuperação. Infelizmente, isso tem impacto no geração de registos de atividade em Clouds soberanas do Azure. Se os utilizadores de Cloud soberanas do Azure criou ou não está configurado todos os alertas de registos de Atividades através do Azure Monitor, conforme mencionado aqui, eles não seriam acionados. Nesse caso, seria Aconselhamos tais usuários para utilizar as definições de diagnóstico e de área de trabalho LA ou [Power BI a solução em relatórios](backup-azure-configure-reports.md) para obter as informações relevantes. Além disso, em todas as regiões públicas do Azure, se um utilizador está a recolher registos de atividades de serviços de recuperação numa área de trabalho de análise de registo como mencionado [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity), estes registos também não iria aparecer.

Se quiser ser notificado quando as cópias de segurança são com êxito, pode utilizar alertas criadas sobre o [registos de atividades](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) do cofre.

### <a name="login-into-azure-portal"></a>Inicie sessão no portal do Azure

Início de sessão no portal do Azure e continuar para o Cofre de serviços de recuperação do Azure relevante e clique em "secção registo de atividades" nas propriedades.

### <a name="identify-appropriate-log"></a>Identificar o registo adequado

Aplica os filtros mostrados na imagem seguinte para verificar se está a receber os registos de atividades para cópias de segurança com êxito. Altere o período de tempo em conformidade para ver registos.

![Registos de Atividade](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Pode clicar o segmento de "JSON" para obter mais detalhes e visualizá-lo ao copiar-colá-lo num editor de texto. Ele deve exibir os detalhes do cofre e o item que acionou o registo de atividades ou seja, o item de cópia de segurança.

Em seguida, clique em "Adicionar alerta de registo de atividade" para gerar alertas para todos os tais logs.

### <a name="add-activity-log-alert"></a>Adicionar alerta de registo de atividade

Clicar em "Adicionar alerta de registo de atividade" mostrará a uma tela conforme mostrado abaixo

![Alerta de registo de atividade](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png) da subscrição e grupo de recursos são utilizados para armazenar o alerta. Os critérios serão preenchidos previamente. Certifique-se de que todos os valores são relevantes para seus requisitos.

Para cópias de segurança com êxito, o "nível" está marcado como "Informativo" e o estado "Com êxito".

Se selecionar um "recurso" acima, será gerado o alerta quando os registos de Atividades são registados para esse recurso ou o cofre. Se pretender que a regra seja aplicável a todos os cofres, deixe o "recurso" estar vazio.

### <a name="define-action-on-alert-firing"></a>Defina a ação no alerta acionadas

Utilize o "grupo de ação" para definir a ação após a gerar um alerta. Pode clicar em "Tipo de ação" para saber mais sobre as ações disponíveis desse tipo. o e-mail/SMS/integração com o ITSM etc.

![Grupo de ação de registo de atividade](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)

Assim que clicar em OK, será gerado um alerta de registo de atividades e os registos de atividade subsequente registados para as cópias de segurança com êxito irão disparar a ação, conforme definido no grupo de ação.

### <a name="limitations-on-alerts"></a>Limitações sobre alertas

Alertas com base em eventos estão sujeitos aos seguintes limitações:

1. Alertas são acionados em todas as máquinas virtuais no cofre dos serviços de recuperação. Não é possível personalizar o alerta para um subconjunto das máquinas virtuais num cofre dos serviços de recuperação.
2. Os alertas são enviados a partir de "alerts-noreply@mail.windowsazure.com". Atualmente não é possível modificar o remetente de e-mail.

## <a name="next-steps"></a>Passos Seguintes

Para informações sobre como voltar a criar uma máquina virtual a partir de um ponto de recuperação, verifique [restaurar VMs do Azure](backup-azure-arm-restore-vms.md).

Se precisar de informações sobre como proteger suas máquinas virtuais, consulte o artigo [primeira impressão: Cópia de segurança de VMs para um cofre dos serviços de recuperação](backup-azure-vms-first-look-arm.md).

Saiba mais sobre as tarefas de gestão para cópias de segurança VM no artigo [cópias de segurança do Azure de gerir máquinas virtuais](backup-azure-manage-vms.md).
