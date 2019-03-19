---
title: A faturação do Azure e o cenário de orçamento de gestão de custos | Documentos da Microsoft
description: Saiba como utilizar a automatização do Azure para encerrar VMs com base nos limiares de orçamento específico.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/13/2019
ms.author: erikre
ms.openlocfilehash: c92789c12f4454f5d76590e5323b78223b49c97f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113066"
---
# <a name="manage-costs-with-azure-budgets"></a>Gerir os custos com os Orçamentos do Azure

Controle de custos é um componente crítico para maximizar o valor do seu investimento na cloud. Existem vários cenários em que a visibilidade de custo, relatórios e orquestração com base no custo são críticos às operações de fazer negócios conosco. [APIs de gestão de custos do Azure](https://docs.microsoft.com/rest/api/consumption/) fornecem um conjunto de APIs para oferecer suporte a cada um destes cenários. As APIs fornecem detalhes de utilização, permitindo-lhe ver os custos de nível de instância granular.

Orçamentos são frequentemente utilizados como parte do controlo de custos. Podem ser confinados orçamentos no Azure. Por exemplo, pode restringir sua exibição de orçamento, com base numa coleção de recursos, grupos de recursos ou subscrição. Além de utilizar os API de orçamentos para ser notificado por e-mail quando for atingido um limiar de orçamento, pode usar [grupos de ação do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) para acionar um conjunto organizado de ações como resultado de um evento de orçamento.

Um cenário comum de orçamentos de um cliente a executar uma carga de trabalho não críticas poderia ocorrer quando pretende gerir em relação a um orçamento e também obter um custo previsíveis, ao examinar a fatura mensal. Este cenário requer alguns orquestração com base no custo de recursos que fazem parte do ambiente do Azure. Neste cenário, um orçamento mensal de US $1000 para a subscrição está definido. Além disso, os limiares de notificação são definidos para acionar alguns orquestrações. Este cenário começa com um limiar de custo de 80%, que irá parar todas as VMs no grupo de recursos **opcional**. Em seguida, com o limiar de custo de 100%, serão paradas todas as instâncias VM.
Para configurar este cenário, irá concluir as seguintes ações, seguindo as etapas fornecidas em cada secção deste tutorial.

Estas ações incluídas neste tutorial permitem-lhe:

- Crie um Runbook da automatização do Azure para parar VMs ao utilizar webhooks.
- Criar uma aplicação de lógica do Azure para ser acionado com base no valor de limiar de orçamento e chamar o runbook com os parâmetros certos.
- Crie um grupo de ação de Monitor do Azure que será configurado para acionar a aplicação de lógica do Azure quando é cumprido o limiar de orçamento.
- Crie o orçamento do Azure com os limiares pretendidos e conectá-lo para o grupo de ação.

## <a name="create-an-azure-automation-runbook"></a>Criar um Runbook da automatização do Azure

[A automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) é um serviço que permite-lhe a maioria das suas tarefas de gestão de recursos de scripts e executar essas tarefas como agendada ou a pedido. Como parte deste cenário, irá criar uma [runbook de automatização do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-types) que será utilizada para parar VMs. Irá utilizar o [parar VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) gráfica runbook a partir do [galeria](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) para criar este cenário. Ao importar este runbook na conta do Azure e ao publicá-la, será capaz de parar VMs quando for atingido um limiar de orçamento.

### <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com as credenciais da sua conta do Azure.
2. Clique nas **criar um recurso** botão encontrado no canto superior esquerdo do Azure.
3. Selecione **ferramentas de gestão** > **automatização**.
   > [!NOTE]
   > Se não tiver uma conta do Azure, pode criar uma [conta gratuita](https://azure.microsoft.com/free/).
4. Introduza as suas informações de conta. Para **criar conta Run do Azure**, escolha **Sim** ao ativar automaticamente as definições necessárias para simplificar a autenticação para o Azure.
5. Quando terminar, clique em **Criar**, para iniciar a implementação da conta de Automatização.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importe o runbook de parar VMs do Azure V2

Utilizar um [runbook de automatização do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-types), importar a [parar VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) runbook gráfico da galeria.

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/) com as credenciais da sua conta do Azure.
2.  Abra a sua conta de automatização, selecionando **todos os serviços** > **contas de automatização**. Em seguida, selecione a sua conta de automatização.
3.  Clique em **Galeria de Runbooks** partir do **automatização de processos** secção.
4.  Definir o **origem da galeria** ao **Script Center** e selecione **OK**.
5.  Localize e selecione o [parar VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) item da galeria no portal do Azure.
6.  Clique nas **importação** botão para apresentar o **importação** painel e selecione **OK**. Será apresentado o painel de descrição geral do runbook.
7.  Depois do runbook tem de concluir o processo de importação, selecione **editar** para apresentar a opção de editor e a publicação do runbook gráfico.

    ![Azure - editar runbook de gráfico](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Clique nas **Publish** botão para publicar o runbook e, em seguida, selecione **Sim** quando lhe for pedido. Quando publica um runbook, substitui qualquer versão publicada existente pela versão de rascunho. Neste caso, não tem, nenhuma versão publicada porque tem de criar o runbook.

    Para obter mais informações sobre como publicar um runbook, consulte [criar um runbook gráfico](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Criar webhooks para o runbook

Utilizar o [parar VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) runbook gráfico, irá criar dois Webhooks para iniciar o runbook na automatização do Azure através de um único pedido HTTP. O webhook primeiro invocará o runbook com um limiar de orçamento de 80% com o nome do grupo de recursos como um parâmetro, permitindo que as VMs opcionais ser parado. Em seguida, o webhook segundo invocará o runbook sem parâmetros (em 100%), que irá parar todas as instâncias VM restantes.

1. Do **Runbooks** página no [portal do Azure](https://portal.azure.com/), clique nas **StopAzureV2Vm** runbook que exibe o painel de descrição geral do runbook.
2. Clique em **Webhook** na parte superior da página para abrir o **adicionar Webhook** painel.
3. Clique em **criar novo webhook** para abrir o **criar um novo webhook** painel.
4. Definir o **Name** do Webhook para **opcional**. O **Enabled** propriedade tem de ser **Sim**. O **Expires** valor não precisa de ser alterado. Para obter mais informações sobre as propriedades do Webhook, veja [detalhes de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. Junto ao valor da URL, clique no ícone de cópia para copiar o URL do webhook.
   > [!IMPORTANT]
   > Guarde o URL do webhook com o nome **opcional** num local seguro. Irá utilizar o URL mais tarde neste tutorial. Por motivos de segurança, depois de criar o webhook, não pode ver ou obter o URL novamente.
6. Clique em **OK** para criar o novo webhook.
7. Clique em **configurar parâmetros e definições de execução** para ver o parâmetro de valores para o runbook.
   > [!NOTE]
   > Se o runbook tiver parâmetros obrigatórios, em seguida, não é possível criar o webhook, a menos que os valores são fornecidos.
8. Clique em **OK** para aceitar os valores de parâmetros do webhook.
9. Clique em **criar** para criar o webhook.
10. Em seguida, siga os passos acima para criar um webhook com o nome do segundo **Complete**.
    > [!IMPORTANT]
    > Certifique-se de que guarde ambos os URLs de webhook para utilizar mais tarde neste tutorial. Por motivos de segurança, depois de criar o webhook, não pode ver ou obter o URL novamente.

Agora, deve ter dois webhooks configurados que estão cada disponíveis através de URLs que guardou.

![Webhooks - opcionais e completos](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Está agora concluído com a configuração da automatização do Azure. Pode testar os webhooks com um simple teste do Postman para validar que o webhook funcione. Em seguida, tem de criar a aplicação de lógica de orquestração.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Criar uma aplicação de lógica do Azure para orquestração

Aplicações lógicas ajudam-lhe criar, agendar e automatizar processos como fluxos de trabalho para que possa integrar aplicações, dados, sistemas e serviços em empresas ou organizações. Neste cenário, o [aplicação lógica](https://docs.microsoft.com/azure/logic-apps/) criar irá fazer um pouco mais do que apenas a chamada do webhook da automatização que criou.

Orçamentos podem ser configurados para acionar uma notificação quando é cumprido um limiar especificado. Pode fornecer vários limiares para receber notificações em e a aplicação lógica irá demonstrar a capacidade para efetuar ações diferentes com base num limiar cumprido. Neste exemplo, definirá até um cenário onde pode obter algumas notificações, a notificação primeiro é para quando for atingida a 80% do orçamento e a segunda notificação é quando 100% do orçamento foi atingido. A aplicação lógica será utilizada para encerrar a todas as VMs no grupo de recursos. Primeiro, o **opcional** irá ser atingido o limiar de 80%, em seguida, irá ser atingido o limiar de segundo em que todas as VMs na subscrição serão encerradas.

O Logic apps permite-lhe fornecer um esquema de exemplo para o acionador HTTP, mas exigir que defina os **Content-Type** cabeçalho. Porque o grupo de ação não tem cabeçalhos personalizados para o webhook, deve analisar o payload de um passo separado. Irá utilizar o **analisar** ação e fornecer-lhe um payload de exemplo.

### <a name="create-the-logic-app"></a>Criar a aplicação lógica

A aplicação lógica irá efetuar várias ações. A lista seguinte fornece um conjunto de alto nível de ações que irá executar a aplicação lógica:
- Reconhece quando é recebido um pedido HTTP
- Analisar transmitido em dados JSON para determinar o valor de limiar for atingido
- Utilizar uma instrução condicional para verificar se o valor do limiar atingiu a 80% ou mais do intervalo de orçamento, mas não maior que ou igual a 100%.
    - Se este limiar for atingido, envie um POST de HTTP utilizando o webhook com o nome **opcional**. Esta ação irá encerrar as VMs no grupo de "Opcional".
- Use uma instrução condicional para verificar se o limiar tem atingiu ou excedeu 100% do valor de orçamento.
    - Se o valor do limiar for atingido, envie um POST de HTTP utilizando o webhook com o nome **Complete**. Esta ação encerrará todas as VMs restantes.

Os passos seguintes são necessários para criar aplicação lógica que executará as etapas acima:

1.  Na [portal do Azure](https://portal.azure.com/), selecione **criar um recurso** > **integração** > **aplicação lógica**.

    ![Azure - selecione o recurso de aplicação lógica](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  Na **Criar aplicação lógica** painel, forneça os detalhes tem de criar a sua aplicação lógica, selecione **afixar ao dashboard**e clique em **criar**.

    ![Azure - criar uma aplicação lógica](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Quando o Azure implementar a sua aplicação lógica, o **estruturador de aplicações lógicas** é aberto e mostra um painel com uma introdução acionadores frequentemente utilizados e vídeos.

### <a name="add-a-trigger"></a>Adicionar um acionador

Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho. As ações são todos os passos que ocorrem após o acionador.

1.  Sob **modelos** da **estruturador de aplicações lógicas** painel, escolha **aplicação lógica em branco**.
2.  Adicionar uma [acionador](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) introduzindo "pedido de http" no **estruturador de aplicações lógicas** caixa de pesquisa para localizar e selecione o acionador com o nome **pedir – pedido de HTTP de uma quando é recebido**.

    ![Acionador de Http do Azure - aplicação lógica-](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Selecione **novo passo** > **adicionar uma ação**.

    ![Azure - novo passo - adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Procure "analisar JSON" na **estruturador de aplicações lógicas** caixa de pesquisa para localizar e selecionar o **Data Operations - Parse JSON** [ação](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts).

    ![Adicionar Azure - aplicação lógica - analisar a ação de JSON](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Introduza "Payload" como o **conteúdo** nome para o payload de Parse JSON ou utilize a etiqueta de "Corpo" de conteúdo dinâmico.
6.  Selecione o **utilizar payload de amostra para gerar esquema** opção a **Parse JSON** caixa.

    ![Dados JSON de exemplo de utilização do Azure - aplicação lógica - para gerar esquema](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Cole o payload JSON de exemplo seguinte na caixa de texto: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    A caixa de texto será apresentado como o seguinte:

    ![Azure - aplicação lógica - o payload JSON de exemplo](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Clique em **Concluído**.

### <a name="add-the-first-conditional-action"></a>Adicionar a primeira ação condicional

Utilizar uma instrução condicional para verificar se o valor do limiar atingiu a 80% ou mais do intervalo de orçamento, mas não maior que ou igual a 100%. Se este limiar for atingido, envie um POST de HTTP utilizando o webhook com o nome **opcional**. Esta ação irá encerrar as VMs no **opcional** grupo.

1.  Selecione **novo passo** > **adicionar uma condição**.

    ![Azure - aplicação lógica - adicionar uma condição](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  Na **condição** , clique na caixa de texto que contém **escolher um valor** para apresentar uma lista de valores disponíveis.

    ![Caixa de condição do Azure - aplicação lógica-](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Clique em **expressão** na parte superior da lista e introduza a seguinte expressão no editor de expressões: `float()`

    ![Expressão do Azure - aplicação lógica - número de vírgula flutuante](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Selecione **conteúdo dinâmico**, coloque o cursor dentro da (parênteses) e selecione **NotificationThresholdAmount** da lista para preencher a expressão completa.

    A expressão será a seguinte:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Selecione **OK** para definir a expressão.
6.  Selecione **é maior que ou igual a** na caixa de lista pendente da **condição**.
7.  Na **escolher um valor** caixa da condição introduza `.8`.

    ![Expressão de número de vírgula flutuante de Azure - aplicação lógica - com um valor](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Clique em **Add** > **Adicionar linha** na caixa de condição para adicionar uma parte adicional da condição.
9.  Na **condição** , clique na caixa de texto que contém **escolher um valor**.
10. Clique em **expressão** na parte superior da lista e introduza a seguinte expressão no editor de expressões: `float()`
11. Selecione **conteúdo dinâmico**, coloque o cursor dentro da (parênteses) e selecione **NotificationThresholdAmount** da lista para preencher a expressão completa.
12. Selecione **OK** para definir a expressão.
13. Selecione **é inferior a** na caixa de lista pendente da **condição**.
14. Na **escolher um valor** caixa da condição introduza `1`.

    ![Expressão de número de vírgula flutuante de Azure - aplicação lógica - com um valor](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. Na **se for true** caixa, selecione **adicionar uma ação**. Irá adicionar uma ação de HTTP POST que será desligado a VMs opcionais.

    ![Azure - aplicação lógica - adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Introduza **HTTP** para procurar a ação de HTTP e selecione o **HTTP – HTTP** ação.

    ![Ação de HTTP de adicionar o Azure - aplicação lógica-](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Selecione **Post** como o para o **método** valor.
18. Introduza o URL do webhook com o nome **opcional** que criou anteriormente no tutorial como o **Uri** valor.

    ![URI de ação de HTTP do Azure - aplicação lógica-](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Selecione **adicionar uma ação** no **se for verdadeiro** caixa. Irá adicionar uma ação de e-mail que irá enviar um e-mail a notificar o destinatário de que as VMs opcionais foram encerradas.
20. Procure "send email" e selecione um *enviar e-mail* ação com base no serviço de e-mail que utiliza.

    ![Ação do Azure - aplicação lógica - enviar um e-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Par contas Microsoft pessoais, selecione **Outlook.com**. Para contas escolares ou profissionais do Azure, selecione **Office 365 Outlook** (Outlook do Office 365). Se ainda não tiver uma ligação, é-lhe pedido que inicie sessão na sua conta de e-mail. O Logic Apps cria uma ligação para a sua conta de e-mail.

    Terá de permitir que a aplicação lógica aceder às suas informações de e-mail.

    ![Aviso de acesso do Azure - aplicação lógica-](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Adicionar a **para**, **assunto**, e **corpo** texto do e-mail que notifica o destinatário de que as VMs opcionais foram encerradas. Utilize o **BudgetName** e o **NotificationThresholdAmount** conteúdo dinâmico para preencher os campos assunto e corpo.

    ![Detalhes de E-Mail do Azure - aplicação lógica-](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Adicionar a segunda ação condicional

Use uma instrução condicional para verificar se o limiar tem atingiu ou excedeu 100% do valor de orçamento. Se o valor do limiar for atingido, envie um POST de HTTP utilizando o webhook com o nome **Complete**. Esta ação encerrará todas as VMs restantes.

1.  Selecione **novo passo** > **adicionar uma condição**.

    ![Azure - aplicação lógica - adicionar ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  Na **condição** , clique na caixa de texto que contém **escolher um valor** para apresentar uma lista de valores disponíveis.
3.  Clique em **expressão** na parte superior da lista e introduza a seguinte expressão no editor de expressões: `float()`
4.  Selecione **conteúdo dinâmico**, coloque o cursor dentro da (parênteses) e selecione **NotificationThresholdAmount** da lista para preencher a expressão completa.

    A expressão será a seguinte:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Selecione **OK** para definir a expressão.
6.  Selecione **é maior que ou igual a** na caixa de lista pendente da **condição**.
7.  Na **escolha uma caixa de valor** da condição introduza `1`.

    ![Valor de condição de conjunto do Azure - aplicação lógica-](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  Na **se for true** caixa, selecione **adicionar uma ação**. Irá adicionar uma ação de HTTP POST que será desligado a todas as VMs restantes.

    ![Azure - aplicação lógica - adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Introduza **HTTP** para procurar a ação de HTTP e selecione o **HTTP – HTTP** ação.
10. Selecione **Post** como o para o **método** valor.
11. Introduza o URL do webhook com o nome **concluída** que criou anteriormente no tutorial como o **Uri** valor.

    ![Azure - aplicação lógica - adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Selecione **adicionar uma ação** no **se for verdadeiro** caixa. Irá adicionar uma ação de e-mail que irá enviar um e-mail a notificar o destinatário que foram encerradas, as VMs restantes.
13. Procure "send email" e selecione um *enviar e-mail* ação com base no serviço de e-mail que utiliza.
14. Adicionar a **para**, **assunto**, e **corpo** texto do e-mail que notifica o destinatário de que as VMs opcionais foram encerradas. Utilize o **BudgetName** e o **NotificationThresholdAmount** conteúdo dinâmico para preencher os campos assunto e corpo.

    ![Detalhes do Azure - aplicação lógica - enviar e-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Clique em **salvar** na parte superior a **Estruturador da aplicação lógica** painel.

### <a name="logic-app-summary"></a>Resumo de aplicação lógica

Eis o que a aplicação lógica é semelhante a quando tiver terminado. No básico o máximo de cenários onde não é necessário qualquer orquestração baseados no limiar, poderia chamar diretamente o script de automação da **Monitor** e a ignorar a **aplicação lógica** passo.

   ![Visão completa do Azure - aplicação lógica-](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Quando guardou a aplicação lógica, foi gerado um URL que poderá chamar. Irá utilizar este URL na secção seguinte deste tutorial.

## <a name="create-an-azure-monitor-action-group"></a>Criar um grupo de ação de Monitor do Azure

Um grupo de ação é uma coleção de preferências de notificações que definir. Quando for acionado um alerta, um grupo de ação específica pode receber o alerta a ser notificado. Um alerta do Azure proativamente gera uma notificação com base em condições específicas e fornece a oportunidade de tomar medidas. Um alerta pode utilizar dados de várias origens, incluindo métricas e registos.

Os grupos de ação são o único ponto de extremidade que se integrará ao seu orçamento. Pode definir as notificações em vários canais, mas para este cenário, enfocarei a aplicação lógica que criou anteriormente neste tutorial.

### <a name="create-an-action-group-in-azure-monitor"></a>Criar um grupo de ação no Azure Monitor

Ao criar o grupo de ação, vai apontar para a aplicação lógica que criou anteriormente no tutorial.

1.  Se não ainda tiver sessão iniciada para o [portal do Azure](https://portal.azure.com/), início de sessão e selecione **todos os serviços** > **Monitor**.
2.  Selecione **grupos de ações** partir do **definição** secção.
3.  Selecione **adicionar um grupo de ação** partir do **grupos de ação** painel.
4.  Adicionar e verificar os seguintes itens:
    - Nome do grupo de ações
    - Nome abreviado
    - Subscrição
    - Grupo de recursos

    ![Azure - aplicação lógica - adicionar um grupo de ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  Dentro de **grupo de ação de adicionar** painel, adicionar uma ação de LogicApp. Nomeie a ação **orçamento BudgetLA**. Na **aplicação lógica** painel, selecione a **subscrição** e o **grupo de recursos**. Em seguida, selecione o **aplicação lógica** que criou anteriormente no tutorial.
6.  Clique em **OK** para definir a aplicação lógica. Em seguida, selecione **OK** no **adicionar grupo de ação** painel para criar o grupo de ação.

Já está com todos os componentes de suporte necessários para orquestrar efetivamente o seu orçamento. Agora tudo o que precisa fazer é criar o orçamento e configurá-lo para utilizar o grupo de ação que criou.

## <a name="create-the-azure-budget"></a>Criar o orçamento do Azure

Pode criar um orçamento no portal do Azure com o [funcionalidade de orçamento](../cost-management/tutorial-acm-create-budgets.md) no Cost Management. Em alternativa, pode criar um orçamento com APIs REST, cmdlets do Powershell, ou utilizar a CLI. O procedimento seguinte utiliza a API REST. Antes de chamar a API REST, terá um token de autorização. Para criar um token de autorização, pode utilizar o [ARMClient](https://github.com/projectkudu/ARMClient) projeto. O **ARMClient** permite-lhe autenticar-se para o Azure Resource Manager e obter um token para chamar as APIs.

### <a name="create-an-authentication-token"></a>Criar um token de autenticação

1.  Navegue para o [ARMClient](https://github.com/projectkudu/ARMClient) projeto no GitHub.
2.  Clone o repositório para obter uma cópia local.
3.  Abra o projeto no Visual Studio e criá-lo.
4.  Assim que a compilação for concluída com êxito, o executável deve estar no *\bin\debug* pasta.
5.  Execute o ARMClient. Abra uma linha de comandos e navegue para o *\bin\debug* pasta da raiz do projeto.
6.  Para iniciar sessão e autenticar, introduza o seguinte comando no prompt de comando:<br>
    `ARMClient login prod`
7.  Copiar o **guid da subscrição** da saída.
8.  Para copiar um token de autorização para a área de transferência, introduza o seguinte comando no prompt de comando, mas se esqueça de utilizar o ID de subscrição copiada do passo acima: <br>
    `ARMClient token <subscription GUID from previous step>`

    Depois de concluir o passo acima, verá o seguinte:<br>
    **Token copiado para a área de transferência com êxito.**
9.  Guarde o token a ser utilizado para obter os passos na secção seguinte deste tutorial.

### <a name="create-the-budget"></a>Criar o orçamento

Em seguida, configurará **Postman** para criar um orçamento, chamando as APIs de REST de consumo do Azure. Postman é um ambiente de desenvolvimento de API. Irá importar ficheiros de ambiente e uma coleção no Postman. A coleção contém definições agrupadas de pedidos HTTP que chamam as APIs de REST de consumo do Azure. O ficheiro de ambiente contém variáveis que são utilizadas pela coleção.

1.  Transfira e abra o [cliente Postman REST](https://www.getpostman.com/) para executar as APIs REST.
2.  No Postman, crie um novo pedido.

    ![Postman - criar um novo pedido](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Guarde a nova solicitação como uma coleção, para que o novo pedido não tem nada nele.

    ![-Guardar o novo pedido do postman](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Alterar o pedido de um `Get` para um `Put` ação.
5.  Modificar o URL seguinte, substituindo `{subscriptionId}` com o **ID da subscrição** que utilizou na secção anterior deste tutorial. Além disso, modificar o URL para incluir "SampleBudget" como o valor de `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Selecione o **cabeçalhos** separador do Postman.
7.  Adicionar um novo **chave** com o nome "Autorização".
8.  Definir o **valor** para o token que foi criado com o ArmClient no final da última secção.
9.  Selecione **corpo** separador do Postman.
10. Selecione o **brutos** botão de opção.
11. Na caixa de texto, cole a abaixo da definição de orçamento de exemplo, no entanto tem de substituir a **subscriptionid**, **budgetname**, e **actiongroupname** parâmetros com sua ID de subscrição, um nome exclusivo para o seu orçamento e o nome do grupo de ação que criou na URL e o corpo do pedido:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                },
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. Prima **enviar** para enviar o pedido.

Agora tem todas as peças que precisa chamar o [orçamentos API](https://docs.microsoft.com/rest/api/consumption/budgets). A referência de API de orçamentos tem detalhes adicionais sobre os pedidos específicos, incluindo o seguinte:
    - **budgetName** -orçamentos vários são suportados.  Nomes de orçamento tem de ser exclusivos.
    - **categoria** -tem de ser **custo** ou **utilização**. A API suporta os custos e orçamentos de utilização.
    - **intervalo de agregação** -um orçamento mensal, trimestral ou anualmente. Repõe a quantidade no final do período.
    - **filtros** -filtros permitem-lhe restringir o orçamento para um conjunto específico de recursos dentro do âmbito selecionado. Por exemplo, um filtro pode ser uma coleção de grupos de recursos para um orçamento de nível de subscrição.
    - **notificações** – determina os detalhes de notificação e limiares. Pode configurar vários limiares e fornecer um endereço de e-mail ou um grupo de ação para receber uma notificação.

## <a name="summary"></a>Resumo

Ao seguir este tutorial, ficou a saber:
- Como criar um Runbook da automatização do Azure para parar VMs.
- Como criar uma aplicação de lógica do Azure que é acionado com base nos valores de limiar de orçamento e chamar o runbook relacionado com os parâmetros certos.
- Como criar um grupo de ação de Monitor do Azure que irá foi configurado para acionar a aplicação de lógica do Azure, quando é cumprido o limiar de orçamento.
- Como criar o orçamento do Azure com os limiares pretendidos e conectá-lo para o grupo de ação.

Agora tem um orçamento totalmente funcional para a sua subscrição que irá encerrar verticalmente as VMs quando atingir seus limites de orçamento configurado.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre cenários de faturas do Azure, consulte [faturação e cenários de automação do gerenciamento de custo](billing-cost-management-automation-scenarios.md).
