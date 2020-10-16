---
title: Monitor Azure Automation com alertas métricos
description: Este artigo descreve como configurar um alerta métrico com base no estado de conclusão do runbook.
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 8767687f0b72d3469bef570770ac81fa8300097f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88055938"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Monitorizar runbooks com alertas de métrica

Neste artigo, aprende-se a criar um [alerta métrico](../azure-monitor/platform/alerts-metric-overview.md) baseado no estado de conclusão do runbook.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com)

## <a name="create-alert"></a>Criar alerta

Os alertas permitem definir uma condição para monitorizar e uma ação a tomar quando essa condição for satisfeita.

1. Lançar o serviço Azure Automation no portal Azure clicando em **Todos os serviços,** procurando e selecionando **Contas de Automação.**

2. Na sua lista de contas De Automação, selecione a conta para a quais pretende criar um alerta. 

3. Em **Monitorização**, selecione **Alertas** e, em seguida, selecione **+ Nova Regra de Alerta**. A margem para o alvo já está definida e associada à sua conta Demôm automação.

### <a name="configure-alert-criteria"></a>Configure critérios de alerta

1. Clique **em Selecionar a condição**. Selecione **Métricas** para o **tipo sinal**, e escolha **Trabalhos Totais** da lista.

2. A página **lógica de sinal de configuração** é onde se define a lógica que desencadeia o alerta. Sob o gráfico histórico é apresentado com duas dimensões, **Nome do Runbook** e **Estado**. Dimensões são propriedades diferentes para uma métrica que pode ser usada para filtrar resultados. Para **Nome do Livro de Execuções**, selecione o livro de recortes que pretende alertar ou deixe em branco para alertar em todos os runbooks. Para **o Estado**, selecione um estado a partir do drop-down para o quais pretende monitorizar. O nome do livro e os valores de estado que aparecem na queda são apenas para empregos que decorreram na última semana.

   Se pretender alertar sobre um status ou livro de bordo que não seja mostrado no dropdown, clique na opção **De valor personalizado Adicionar** ao lado da dimensão. Esta ação abre um diálogo que lhe permite especificar um valor personalizado, que não emitia para essa dimensão recentemente. Se introduzir um valor que não exista para uma propriedade, o seu alerta não será desencadeado.

   > [!NOTE]
   > Se não especificar um nome para a dimensão Nome do **Runbook,** se existirem alguns livros que satisfaçam os critérios de estado, que incluam livros de sistema ocultos, receberá um alerta.

    Por exemplo, alertar quando um livro de recortes devolve um estado _falhado,_ além de especificar o nome do livro de verificação, para a dimensão **Status** adicionar o valor de dimensão personalizada **Falhado**.

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="Especifique o valor da dimensão personalizada" border="false":::

3. Em **lógica de alerta,** defina a condição e o limiar para o seu alerta. Uma pré-visualização da sua condição definida é mostrada por baixo.

4. Em **Avaliação com base em**, selecione a timepan para a consulta e quantas vezes deseja que essa consulta seja executada. Por exemplo, se escolher **Nos últimos 5 minutos** para **o período**, e a **cada 1 minuto** de **frequência,** o alerta procura o número de livros que satisfaçam os seus critérios nos últimos 5 minutos. Esta consulta é executada a cada minuto, e uma vez que os critérios de alerta definidos já não se encontram numa janela de 5 minutos, o alerta resolve-se sozinho. Quando terminar, clique em **'Fazer'.**

   ![Selecione um recurso para o alerta](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>Definir a ação a tomar

1. No **grupo Action**, selecione Specify action **group**. Um grupo de ação é um grupo de ações que você pode usar em mais de um alerta. Estes podem incluir, mas não se limitam a, notificações de e-mail, runbooks, webhooks, e muito mais. Para saber mais sobre grupos de ação e passos para criar um que envie uma notificação por e-mail, consulte [Criar e gerir grupos de ação.](../azure-monitor/platform/action-groups.md)

### <a name="define-alert-details"></a>Definir detalhes do alerta

1. Em **informação sobre as regras de alerta,** forneça ao alerta um nome e descrição amigáveis. Desaça a **Severidade** para corresponder à sua condição de alerta. Há cinco severidades que variam de 0 a 5. Os alertas são tratados da mesma forma independente da gravidade, podendo corresponder à gravidade para corresponder à sua lógica de negócio.

1. Por predefinição, as regras são ativadas na criação, a menos que **selecione Não** para a opção Ative a **regra de alerta após a criação**. Para alertas criados num estado desativação, pode capacitá-los no futuro quando estiver pronto. **Selecione Criar a regra de alerta** para guardar as suas alterações.

## <a name="receive-notification"></a>Receber notificação

Quando os critérios de alerta são cumpridos, o grupo de ação executa a ação definida. No exemplo deste artigo, é enviado um e-mail. A imagem a seguir é um exemplo de um e-mail que recebe após o alerta ser desencadeado:

![Alerta de e-mail](./media/automation-alert-activity-log/alert-email.png)

Uma vez que a métrica já não está fora do limiar definido, o alerta é desativado e o grupo de ação executa a ação definida. Se for selecionado um tipo de ação por e-mail, é enviado um e-mail de resolução indicando que foi resolvido.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações, consulte [Utilize um alerta para acionar um manual da Azure Automation](automation-create-alert-triggered-runbook.md).
