---
title: Monitorar runbooks de automação do Azure com alertas de métrica
description: Este artigo orienta você durante o monitoramento de runbooks de automação do Azure com base nas métricas
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: 9bd028157b33817898ef69f9e47cb8b5d9b8f381
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367098"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Monitoramento de runbooks com alertas de métricas

Neste artigo, você aprenderá a criar alertas com base no status de conclusão de runbooks.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure a https://portal.azure.com

## <a name="create-alert"></a>Criar alerta

Os alertas permitem que você defina uma condição a ser monitorada e uma ação a ser tomada quando essa condição for atendida.

Na portal do Azure, navegue até sua conta de automação. Em **monitoramento**, selecione **alertas** e clique em **+ nova regra de alerta**. O escopo do destino já está definido para sua conta de automação.

### <a name="configure-alert-criteria"></a>Configurar critérios de alerta

1. Clique em **+ Adicionar critérios**. Selecione **métricas** para o **tipo de sinal**e escolha **total de trabalhos** da tabela.

2. A página **Configurar lógica de sinal** é onde você define a lógica que dispara o alerta. No gráfico histórico, você verá duas dimensões, nome do **runbook** e **status**. As dimensões são propriedades diferentes para uma métrica que pode ser usada para filtrar os resultados. Para **nome do runbook**, selecione o runbook no qual você deseja alertar ou deixe em branco para alertar sobre todos os runbooks. Para **status**, selecione um status na lista suspensa que você deseja monitorar. Os valores de nome e status do runbook que aparecem na lista suspensa são apenas para trabalhos que foram executados na última semana.

   Se você quiser alertar sobre um status ou runbook que não é mostrado no menu suspenso, clique no **\+** ao lado da dimensão. Essa ação abre uma caixa de diálogo que permite que você insira um valor personalizado que não tenha sido emitido recentemente para essa dimensão. Se você inserir um valor que não existe para uma propriedade, seu alerta não será disparado.

   > [!NOTE]
   > Se você não aplicar um nome para a dimensão **RunbookName** , se houver algum runbook que atenda aos critérios de status, que inclui runbooks de sistema ocultos, você receberá um alerta.

3. Em **lógica de alerta**, defina a condição e o limite para o alerta. Uma visualização de sua condição definida é mostrada abaixo.

4. Em **avaliado com base em**, selecione o período para a consulta e a frequência com que você deseja que a consulta seja executada. Por exemplo, se você escolher **nos últimos 5 minutos** para o **período** e a **cada 1 minuto** para a **frequência**, o alerta procurará o número de runbooks que atenderam aos seus critérios nos últimos 5 minutos. Essa consulta é executada a cada minuto e, depois que os critérios de alerta definidos não são mais encontrados em uma janela de 5 minutos, o alerta é resolvido. Quando terminar, clique em **Concluído**.

   ![Selecionar um recurso para o alerta](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definir detalhes do alerta

1. Em **2. Definir detalhes do alerta**, dê ao alerta um nome amigável e uma descrição. Defina a **severidade** para corresponder à sua condição de alerta. Há cinco severidades que variam de 0 a 5. Os alertas são tratados da mesma forma independente da severidade, você pode corresponder à severidade para corresponder à lógica de negócios.

1. Na parte inferior da seção, há um botão que permite habilitar a regra após a conclusão. Por padrão, as regras são habilitadas na criação. Se você selecionar Não, poderá criar o alerta e ele será criado em um estado **desabilitado** . Na página **regras** no Azure monitor, você pode selecioná-lo e clicar em **habilitar** para habilitar o alerta quando estiver pronto.

### <a name="define-the-action-to-take"></a>Definir a ação a ser tomada

1. Menos de **3. Definir grupo de ações**, clique em **+ novo grupo de ações**. Um grupo de ações é um grupo de ações que você pode usar em mais de um alerta. Eles podem incluir, mas não se limitam a, notificações por email, runbooks, WebHooks e muito mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ação](../azure-monitor/platform/action-groups.md)

1. Na caixa **Nome do grupo de ação**, atribua um nome amigável e um nome abreviado ao grupo de ação. O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.

1. Na seção **ações** , em **tipo de ação**, selecione **email/SMS/Push/voz**.

1. Na página **E-mail/SMS/Push/Voz**, atribua um nome. Assinale a caixa **E-mail** e introduza um endereço de e-mail válido a utilizar.

   ![Configurar o grupo de ação de e-mail](./media/automation-alert-activity-log/add-action-group.png)

1. Clique em **OK** na página **E-mail/SMS/Push/Voz** para fechá-la e clique em **OK** para fechar a página **Adicionar grupo de ação**. O nome especificado nesta página é salvo como o **nome da ação**.

1. Quando terminar, clique em **Guardar**. Essa ação cria a regra que o alerta quando um runbook é concluído com um determinado status.

> [!NOTE]
> Ao adicionar um endereço de email a um grupo de ações, um email de notificação é enviado informando que o endereço foi adicionado a um grupo de ações.

## <a name="notification"></a>Notificação

Quando os critérios de alerta são atendidos, o grupo de ações executa a ação definida. No exemplo deste artigo, um email é enviado. A imagem a seguir é um exemplo de um email que você recebe depois que o alerta é disparado:

![Alerta de email](./media/automation-alert-activity-log/alert-email.png)

Depois que a métrica não estiver mais fora do limite definido, o alerta será desativado e o grupo de ações executará a ação definida. Se um tipo de ação de email for selecionado, um email de resolução será enviado informando que ele foi resolvido.

## <a name="next-steps"></a>Passos seguintes

Continue no seguinte artigo para saber mais sobre outras maneiras de integrar alertas em sua conta de automação.

> [!div class="nextstepaction"]
> [Usar um alerta para disparar um runbook de automação do Azure](automation-create-alert-triggered-runbook.md)
