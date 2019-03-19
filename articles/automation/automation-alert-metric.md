---
title: Monitorizar os runbooks de automatização do Azure com alertas de métricas
description: Este artigo explica como runbooks de automatização do Azure com base em métricas de monitorização
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7932d057a348957d369ba325044055ac8dfe3428
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58170195"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Runbooks de monitorização com alertas de métricas

Neste artigo, saiba como criar alertas com base no status de conclusão de runbooks.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure a https://portal.azure.com

## <a name="create-alert"></a>Criar alerta

Alertas permitem-lhe definir uma condição para monitorizar e uma ação a tomar quando essa condição é cumprida.

No portal do Azure, navegue para a sua conta de automatização. Sob **monitorização**, selecione **alertas** e clique em **+ nova regra de alerta**. O âmbito para o destino já está definido para a sua conta de automatização.

### <a name="configure-alert-criteria"></a>Configurar os critérios de alerta

1. Clique em **+ adicionar critérios**. Selecione **métricas** para o **sinalizar tipo**e escolha **Total de tarefas** da tabela.

2. O **lógica de sinal de configurar** página é onde define a lógica que aciona o alerta. No gráfico, verá duas dimensões, de histórico tal **nome do Runbook** e **estado**. As dimensões são as propriedades diferentes para uma métrica que podem ser utilizadas para filtrar os resultados. Para **nome do Runbook**, selecione o runbook que pretende alertar ou deixe em branco para o alerta em todos os runbooks. Para **estado**, selecione um Estado a lista pendente que pretende monitorizar. Os valores de nome e o estado de runbook que aparecem na lista pendente são apenas para tarefas que ter ficado na última semana.

   Se deseja alertar relativamente um Estado ou o runbook que não é mostrado na lista pendente, clique nas **\+** junto à dimensão. Esta ação abre uma caixa de diálogo que permite-lhe introduzir um valor personalizado, o que não tenha emitido para aquela dimensão recentemente. Se introduzir um valor que não existe para uma propriedade não acionado o alerta.

   > [!NOTE]
   > Se não se aplicam um nome para o **RunbookName** de dimensão, se existirem todos os runbooks que cumprem os critérios de estado, que inclui os runbooks do sistema oculto, receberá um alerta.

3. Sob **lógica de alerta**, definir a condição e o limiar para o alerta. Uma pré-visualização da sua condição definida é mostrada abaixo.

4. Sob **Evaluated com base em**, selecione o período de tempo para a consulta e a frequência com que pretende que essa consulta foi executada. Por exemplo, se escolher **durante os últimos 5 minutos** para **período** e **cada 1 minuto** para **frequência**, o alerta procura o número de runbooks que cumprem os critérios nos últimos 5 minutos. Esta consulta é executada a cada minuto e, depois que definiu os critérios de alerta já não for encontrada numa janela de 5 minutos, o alerta é resolvido em si. Quando terminar, clique em **Concluído**.

   ![Selecione um recurso para o alerta](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definir detalhes do alerta

1. Em **2. Definir detalhes do alerta**, atribua ao alerta um nome amigável e uma descrição. Definir o **gravidade** de acordo com a condição do alerta. Existem cinco gravidades entre 0 e 5. Os alertas são tratados o mesmo independente da gravidade, pode corresponder a gravidade de acordo com a lógica de negócio.

1. Na parte inferior da seção, é um botão que permite-lhe ativar a regra após a conclusão. Por predefinição, as regras são ativadas durante a criação. Se selecionar não, pode criar o alerta e é criado num **desativado** estado. Do **regras** página no Azure Monitor, pode selecioná-lo e clique em **ativar** para ativar o alerta quando estiver pronto.

### <a name="define-the-action-to-take"></a>Definir a ação a tomar

1. Em **3. Definir grupo de ação**, clique em **+ Novo grupo de ação**. Um grupo de ação é um grupo de ações que pode utilizar em mais do que um alerta. Estas podem incluir, mas não estão limitadas a, envie um e-mail notificações, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ação](../azure-monitor/platform/action-groups.md)

1. Na caixa **Nome do grupo de ação**, atribua um nome amigável e um nome abreviado ao grupo de ação. O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.

1. Na **ações** secção sob **tipo de ação**, selecione **E-Mail/SMS/Push/voz**.

1. Na página **E-mail/SMS/Push/Voz**, atribua um nome. Assinale a caixa **E-mail** e introduza um endereço de e-mail válido a utilizar.

   ![Configurar o grupo de ação de e-mail](./media/automation-alert-activity-log/add-action-group.png)

1. Clique em **OK** na página **E-mail/SMS/Push/Voz** para fechá-la e clique em **OK** para fechar a página **Adicionar grupo de ação**. O nome especificado nesta página é guardado como o **nome da ação**.

1. Quando terminar, clique em **Guardar**. Esta ação cria a regra que o alerta quando um runbook foi concluído com um determinado Estado.

> [!NOTE]
> Ao adicionar um endereço de e-mail para um grupo de ação, um e-mail de notificação é enviado ao indicar que o endereço foi adicionado a um grupo de ação.

## <a name="notification"></a>Notificação

Quando os critérios de alerta são cumpridos, o grupo de ação é executada a ação definida. No exemplo deste artigo, é enviado um e-mail. A imagem seguinte é um exemplo de uma mensagem de e-mail que recebe após o alerta é acionado:

![Alerta de e-mail](./media/automation-alert-activity-log/alert-email.png)

Assim que a métrica não se encontra fora do limiar definido, o alerta é desativado e o grupo de ação é executada a ação definida. Se for selecionado um tipo de ação de e-mail, é enviado um e-mail de resolução declarando foi resolvido.

## <a name="next-steps"></a>Passos Seguintes

Avance para o artigo seguinte para saber mais sobre outras formas de integrar alertings na sua conta de automatização.

> [!div class="nextstepaction"]
> [Utilize um alerta para acionar um runbook da automatização do Azure](automation-create-alert-triggered-runbook.md)
