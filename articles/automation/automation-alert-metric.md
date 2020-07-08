---
title: Monitor Azure Automation com alertas métricos
description: Este artigo diz como monitorizar os livros com base em métricas.
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: 20aaee5b699e9721bf9083030604df1385da1915
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83828751"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Monitorizar runbooks com alertas de métrica

Neste artigo, aprende-se a criar alertas com base no estado de conclusão dos livros de corrida.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure a https://portal.azure.com

## <a name="create-alert"></a>Criar alerta

Os alertas permitem definir uma condição para monitorizar e uma ação a tomar quando essa condição for satisfeita.

No portal Azure, navegue para a sua conta Automation. Em **Monitorização**, selecione **Alertas** e clique **+ Nova Regra de Alerta**. A margem para o alvo já está definida na sua conta Demôm automação.

### <a name="configure-alert-criteria"></a>Configure critérios de alerta

1. Clique **+ Adicionar critérios**. Selecione **Métricas** para o **tipo sinal**, e escolha **Trabalhos Totais** da tabela.

2. A página **lógica de sinal de configuração** é onde se define a lógica que desencadeia o alerta. Sob o gráfico histórico é apresentado com duas dimensões, **Nome do Runbook** e **Estado**. Dimensões são propriedades diferentes para uma métrica que pode ser usada para filtrar resultados. Para **Nome do Livro de Execuções**, selecione o livro de recortes que pretende alertar ou deixe em branco para alertar em todos os runbooks. Para **o Estado**, selecione um estado a partir do drop-down para o quais pretende monitorizar. O nome do livro e os valores de estado que aparecem na queda são apenas para empregos que decorreram na última semana.

   Se pretender alertar sobre um status ou livro de bordo que não seja mostrado no dropdown, clique no **\+** seguinte à dimensão. Esta ação abre um diálogo que lhe permite introduzir um valor personalizado, que não emitia para essa dimensão recentemente. Se introduzir um valor que não exista para uma propriedade, o seu alerta não será desencadeado.

   > [!NOTE]
   > Se não aplicar um nome para a dimensão **RunbookName,** se houver algum livro de aplicação que satisfaça os critérios de estado, que inclua livros de sistema ocultos, receberá um alerta.

3. Em **lógica de alerta,** defina a condição e o limiar para o seu alerta. Uma pré-visualização da sua condição definida é mostrada por baixo.

4. Em **Avaliação com base em**, selecione a timepan para a consulta e a frequência com que deseja que essa consulta seja escorada. Por exemplo, se escolher **Nos últimos 5 minutos** para **o período** e a **cada 1 minuto** de **frequência,** o alerta procura o número de livros que satisfaçam os seus critérios ao longo dos últimos 5 minutos. Esta consulta é executada a cada minuto, e uma vez que os critérios de alerta definidos já não se encontram numa janela de 5 minutos, o alerta resolve-se sozinho. Quando terminar, clique em **Concluído**.

   ![Selecione um recurso para o alerta](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definir detalhes do alerta

1. Menos **de 2. Defina detalhes de alerta,** dê ao alerta um nome e descrição amigáveis. Desaça a **Severidade** para corresponder à sua condição de alerta. Há cinco severidades que variam de 0 a 5. Os alertas são tratados da mesma forma independente da gravidade, podendo corresponder à gravidade para corresponder à sua lógica de negócio.

1. Na parte inferior da secção, está um botão que lhe permite ativar a regra após a conclusão. Por defeito, as regras são ativadas na criação. Se selecionar Não, pode criar o alerta e é criado num estado **de desativação.** Na página **Regras** do Monitor Azure, pode selecioná-lo e clicar em **Ativar** o alerta quando estiver pronto.

### <a name="define-the-action-to-take"></a>Definir a ação a tomar

1. Menos **de 3. Definir grupo de ação,** clique **+ Novo grupo de ação.** Um grupo de ação é um grupo de ações que você pode usar em mais de um alerta. Estes podem incluir, mas não se limitam a, notificações de e-mail, runbooks, webhooks, e muito mais. Para saber mais sobre grupos de ação, consulte [Criar e gerir grupos de ação](../azure-monitor/platform/action-groups.md)

1. Na caixa **Nome do grupo de ação**, atribua um nome amigável e um nome abreviado ao grupo de ação. O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.

1. Na secção **Ações** em **AÇÃO TYPE,** selecione **Email/SMS/Push/Voice**.

1. Na página **E-mail/SMS/Push/Voz**, atribua um nome. Assinale a caixa **E-mail** e introduza um endereço de e-mail válido a utilizar.

   ![Configurar o grupo de ação de e-mail](./media/automation-alert-activity-log/add-action-group.png)

1. Clique em **OK** na página **E-mail/SMS/Push/Voz** para fechá-la e clique em **OK** para fechar a página **Adicionar grupo de ação**. O Nome especificado nesta página é guardado como o **NOME DE AÇÃO**.

1. Quando terminar, clique em **Guardar**. Esta ação cria a regra que o alerta quando um livro de bordo concluído com um determinado estatuto.

> [!NOTE]
> Ao adicionar um endereço de e-mail a um Grupo de Ação, é enviado um e-mail de notificação indicando que o endereço foi adicionado a um Grupo de Ação.

## <a name="receive-notification"></a>Receber notificação

Quando os critérios de alerta são cumpridos, o grupo de ação executa a ação definida. No exemplo deste artigo, é enviado um e-mail. A imagem a seguir é um exemplo de um e-mail que recebe após o alerta ser desencadeado:

![Alerta de e-mail](./media/automation-alert-activity-log/alert-email.png)

Uma vez que a métrica já não está fora do limiar definido, o alerta é desativado e o grupo de ação executa a ação definida. Se for selecionado um tipo de ação por e-mail, é enviado um e-mail de resolução indicando que foi resolvido.

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações, consulte [Utilize um alerta para acionar um manual da Azure Automation](automation-create-alert-triggered-runbook.md).
