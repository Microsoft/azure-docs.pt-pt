---
title: Monitor Azure Automation runbooks com alertas métricos
description: Este artigo diz como monitorizar os livros com base em métricas.
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: 20aaee5b699e9721bf9083030604df1385da1915
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828751"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Monitorizar runbooks com alertas de métrica

Neste artigo, aprende-se a criar alertas com base no estado de conclusão dos livros de execução.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure a https://portal.azure.com

## <a name="create-alert"></a>Criar alerta

Os alertas permitem definir uma condição para monitorizar e uma ação a tomar quando essa condição é satisfeita.

No portal Azure, navegue para a sua conta de Automação. Em **monitorização,** selecione **Alertas** e clique **+ Nova Regra de Alerta**. A margem para o alvo já está definida na sua conta Desmótica.

### <a name="configure-alert-criteria"></a>Configurar critérios de alerta

1. Clique **+ Adicionar critérios.** Selecione **Métricas** para o **tipo de sinal,** e escolha **O Trabalho Total** da tabela.

2. A página lógica do **sinal Configurar** é onde define a lógica que desencadeia o alerta. Sob o gráfico histórico é-lhe apresentada duas dimensões, Nome do Livro de **Execução** e **Estado**. As dimensões são propriedades diferentes para uma métrica que pode ser usada para filtrar resultados. Para **'Nome do Livro de Execução',** selecione o livro de execução que pretende alertar ou deixe em branco para alertar em todos os livros de execução. Para **o Estado**, selecione um estado a partir da queda que pretende monitorizar. O nome do livro de corridas e os valores de estatuto que aparecem no abandono são apenas para empregos que correram na última semana.

   Se quiser alertar sobre um estado ou um livro de execução que não seja mostrado no dropdown, clique no **\+** próximo à dimensão. Esta ação abre um diálogo que lhe permite entrar num valor personalizado, que não emitiu para essa dimensão recentemente. Se introduzir um valor que não existe para uma propriedade, o seu alerta não será desencadeado.

   > [!NOTE]
   > Se não aplicar um nome para a dimensão **RunbookName,** se existirem livros de execução que satisfaçam os critérios de estado, que incluam livros de execução do sistema oculto, receberá um alerta.

3. Sob **a lógica de Alerta,** defina a condição e o limiar para o seu alerta. Uma pré-visualização da sua condição definida é mostrada por baixo.

4. Em **avaliação com base em**, selecione a hora de tempo para a consulta e quantas vezes quer que a consulta seja realizada. Por exemplo, se escolher **Ao longo dos últimos 5 minutos** para **period** e a cada **1 minutos** para **frequência,** o alerta procura o número de livros que cumpriram os seus critérios nos últimos 5 minutos. Esta consulta é executada a cada minuto, e uma vez que os critérios de alerta que definiu já não são encontrados numa janela de 5 minutos, o alerta resolve-se sozinho. Quando terminar, clique em **Concluído**.

   ![Selecione um recurso para o alerta](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definir detalhes do alerta

1. Abaixo de **2. Defina detalhes**de alerta, dê ao alerta um nome e descrição amigáveis. Desloque a **Severity** para corresponder à sua condição de alerta. Há cinco severidades que variam de 0 a 5. Os alertas são tratados da mesma forma independente da gravidade, pode corresponder à gravidade para corresponder à sua lógica de negócio.

1. Na parte inferior da secção, encontra-se um botão que lhe permite ativar a regra após a conclusão. Por predefinição, as regras são ativadas na criação. Se selecionar Não, pode criar o alerta e é criado num estado **de desativação.** A partir da página **Regras** no Monitor Azure, pode selecioná-lo e clicar **em Ativar** para ativar o alerta quando estiver pronto.

### <a name="define-the-action-to-take"></a>Definir a ação a tomar

1. Abaixo dos **3. Defina grupo de ação,** clique **+ Novo grupo de ação**. Um grupo de ação é um grupo de ações que você pode usar em mais de um alerta. Estes podem incluir, mas não se limitam a. notificações de e-mail, livros de execução, webhooks, e muito mais. Para saber mais sobre grupos de ação, consulte [Criar e gerir grupos](../azure-monitor/platform/action-groups.md) de ação

1. Na caixa **Nome do grupo de ação**, atribua um nome amigável e um nome abreviado ao grupo de ação. O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.

1. Na secção **Ações** em ÂMBITO DO TIPO DE **AÇÃO,** selecione **Email/SMS/Push/Voice**.

1. Na página **E-mail/SMS/Push/Voz**, atribua um nome. Assinale a caixa **E-mail** e introduza um endereço de e-mail válido a utilizar.

   ![Configurar o grupo de ação de e-mail](./media/automation-alert-activity-log/add-action-group.png)

1. Clique em **OK** na página **E-mail/SMS/Push/Voz** para fechá-la e clique em **OK** para fechar a página **Adicionar grupo de ação**. O nome especificado nesta página é guardado como NOME DE **AÇÃO**.

1. Quando terminar, clique em **Guardar**. Esta ação cria a regra que o alerta quando um livro de execução concluído com um determinado estatuto.

> [!NOTE]
> Ao adicionar um endereço de e-mail a um Grupo de Ação, é enviado um e-mail de notificação indicando que o endereço foi adicionado a um Grupo de Ação.

## <a name="receive-notification"></a>Receber notificação

Quando os critérios de alerta são cumpridos, o grupo de ação executa a ação definida. No exemplo deste artigo, é enviado um e-mail. A imagem que se segue é um exemplo de um e-mail que recebe sem aviso:

![Alerta de e-mail](./media/automation-alert-activity-log/alert-email.png)

Uma vez que a métrica já não esteja fora do limiar definido, o alerta é desativado e o grupo de ação executa a ação definida. Se for selecionado um tipo de ação de e-mail, é enviado um e-mail de resolução indicando que foi resolvido.

## <a name="next-steps"></a>Passos seguintes

* Para mais informações, consulte Utilize um alerta para desencadear um livro de [execução da Automação Azure](automation-create-alert-triggered-runbook.md).
