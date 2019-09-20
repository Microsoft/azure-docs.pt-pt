---
title: Cenário de orçamento de gestão de custos e de faturação do Azure | Microsoft Docs
description: Saiba como utilizar a automatização do Azure para encerrar VMs com base em limiares de orçamento específicos.
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 37f129526cb184a2eeee9e36028e8f00b5bbc247
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "68443464"
---
# <a name="manage-costs-with-azure-budgets"></a>Gerir os custos com os Orçamentos do Azure

O controlo de custos é um elemento essencial para maximizar o valor do seu investimento na cloud. Existem vários cenários em que a visibilidade de custos, os relatórios e a orquestração baseada em custos são fundamentais para as operações de negócios continuadas. As [APIs de Gestão de Custos do Azure](https://docs.microsoft.com/rest/api/consumption/) fornecem um conjunto de APIs que suportam cada um destes cenários. As APIs fornecem detalhes de utilização e permitem que veja os custos ao nível da instância granular.

Os orçamentos são normalmente utilizados como parte do controlo de custos. Os orçamentos podem ser delimitados no Azure. Por exemplo, pode restringir a vista do orçamento com base na subscrição, nos grupos de recursos ou numa coleção de recursos. Além de utilizar a API de orçamentos para ser notificado por e-mail quando é atingido um limiar de orçamento, pode utilizar os [grupos de ação do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) para acionar um conjunto de ações orquestradas como resultado de um evento de orçamento.

Um cliente que executa uma carga de trabalho não crítica pode recorrer a um cenário de orçamentos comum quando quer gerir a utilização em função do orçamento e também obter um custo previsível quando olha para a fatura mensal. Este cenário requer uma orquestração baseada nos custos dos recursos que fazem parte do ambiente do Azure. Neste cenário, é definido um orçamento mensal de 1000 $ para a subscrição. Além disso, são definidos limiares de notificação para acionar algumas orquestrações. Este cenário começa com um limiar de custos de 80%, o que irá encerrar todas as VMs no grupo de recursos **Opcional**. Em seguida, no limiar de custos a 100%, serão encerradas todas as instâncias de VM.
Para configurar este cenário, realizará as seguintes ações ao seguir os passos fornecidos em cada secção deste tutorial.

As ações incluídas neste tutorial permitem-lhe:

- Criar um Runbook de Automatização do Azure para encerrar as VMs com webhooks.
- Criar uma Aplicação Lógica do Azure para ser acionada com base no valor do limiar de orçamento e chamar o runbook com os parâmetros corretos.
- Criar um Grupo de Ações do Azure Monitor que será configurado para acionar a Aplicação Lógica do Azure quando o limiar do orçamento for atingido.
- Criar o orçamento do Azure com os limiares desejados e ligá-lo ao grupo de ações.

## <a name="create-an-azure-automation-runbook"></a>Criar um Runbook de Automatização do Azure

A [Automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) é um serviço que permite gerar scripts para a maioria das tarefas de gestão de recursos e executar essas tarefas de acordo com a agenda ou a pedido. Como parte deste cenário, vai criar um [runbook de Automatização do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-types) que será utilizado para encerrar as VMs. Vai utilizar o runbook gráfico [Encerrar VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) da [galeria](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) para criar este cenário. Ao importar este runbook para a sua conta do Azure e ao publicá-lo, será capaz de encerrar as VMs quando for atingido um limiar de orçamento.

### <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com as credenciais da sua conta do Azure.
2. Clique no botão **Criar um recurso**, localizado no canto superior esquerdo do Azure.
3. Selecione **Ferramentas de Gestão** > **Automatização**.
   > [!NOTE]
   > Se ainda não tiver uma conta do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/).
4. Introduza as suas informações de conta. Para **Criar uma conta Run As do Azure**, escolha **Sim** para ativar automaticamente os parâmetros necessários para simplificar a autenticação no Azure.
5. Quando terminar, clique em **Criar**, para iniciar a implementação da conta de Automatização.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importar o runbook Encerrar VMs do Azure V2

Com um [runbook de Automatização do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-types), importe o runbook gráfico [Encerrar VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) da galeria.

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/) com as credenciais da sua conta do Azure.
2.  Abra a Conta de automatização ao selecionar **Todos os serviços** > **Contas de Automatização**. Em seguida, selecione a Conta de Automatização.
3.  Clique em **Galeria de runbooks** na secção **Automatização de Processos**.
4.  Defina a **Origem de Galeria** como **Centro de Scripts** e selecione **OK**.
5.  Localize e selecione o item da galeria [Encerrar as VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) no portal do Azure.
6.  Clique no botão **Importar** para apresentar o painel **Importar** e selecione **OK**. Será apresentado o painel de descrição geral do runbook.
7.  Quando o runbook concluir o processo de importação, selecione **Editar** para apresentar o editor do runbook gráfico e a opção de publicação.

    ![Azure – Editar runbook gráfico](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Clique no botão **Publicar** para publicar o runbook e, em seguida, selecione **Sim** quando lhe for pedido. Quando publica um runbook, substitui qualquer versão publicada existente pela versão de rascunho. Neste caso, ainda não tem uma versão publicada porque acabou de criar o runbook.

    Para obter mais informações sobre como publicar um runbook, veja [Criar um runbook gráfico](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Criar webhooks para o runbook

Ao utilizar o runbook gráfico [Encerrar VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b), criará dois Webhooks para iniciar o runbook na Automatização do Azure com um único pedido HTTP. O primeiro webhook invocará o runbook num limiar de orçamento de 80% com o nome do grupo de recursos como parâmetro, o que permite que sejam encerradas as VMs opcionais. Em seguida, o segundo webhook invocará o runbook sem qualquer parâmetro (a 100%), o que encerrará todas as restantes instâncias de VM.

1. Na página **Runbooks** no [portal do Azure](https://portal.azure.com/), clique no runbook **StopAzureV2Vm** para apresentar o painel de descrição geral do runbook.
2. Clique em **Webhook** na parte superior da página para abrir o painel **Adicionar Webhook**.
3. Clique em **Criar novo webhook** para abrir o painel **Criar um novo webhook**.
4. Defina o **Nome** do Webhook como **Opcional**. A propriedade **Ativada** deve ser **Sim**. O valor **Expira em** não precisa de ser alterado. Para obter mais informações sobre as propriedades do Webhook, veja [Detalhes de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. Junto do valor do URL, clique no ícone de cópia para copiar o URL do webhook.
   > [!IMPORTANT]
   > Guarde o URL do webhook chamado **Opcional** num local seguro. Utilizará este URL mais adiante neste tutorial. Por motivos de segurança, depois de criar o webhook, não poderá ver nem obter o URL novamente.
6. Clique em **OK** para criar o novo webhook.
7. Clique em **Configurar parâmetros e definições de execução**  para ver os valores dos parâmetros do runbook.
   > [!NOTE]
   > Se o runbook tiver parâmetros obrigatórios, não poderá criar o webhook, a não ser que sejam fornecidos os valores.
8. Clique em **OK** para aceitar os valores dos parâmetros do webhook.
9. Clique em **Criar** para criar o webhook.
10. Em seguida, siga os passos acima para criar um segundo webhook chamado **Completo**.
    > [!IMPORTANT]
    > Lembre-se de guardar os URLs dos webhooks para serem utilizados posteriormente neste tutorial. Por motivos de segurança, depois de criar o webhook, não poderá ver nem obter o URL novamente.

Agora, deverá ter dois webhooks configurados, que poderá aceder através dos URLs guardados anteriormente.

![Webhooks – Opcional e Completo](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Agora, concluiu a configuração da Automatização do Azure. Pode testar os webhooks com um teste simples do Postman que valida o funcionamento do webhook. Em seguida, tem de criar a Aplicação Lógica para a orquestração.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Criar uma Aplicação Lógica do Azure para a orquestração

As Aplicações Lógicas ajudam a criar, agendar e automatizar os processos como fluxos de trabalho, para que possa integrar aplicações, dados, sistemas e serviços em empresas ou organizações. Neste cenário, a [Aplicação Lógica](https://docs.microsoft.com/azure/logic-apps/) que criar fará um pouco mais do que simplesmente chamar o webhook de automatização criado anteriormente.

Os orçamentos podem ser configurados para acionar uma notificação quando é atingido um limiar especificado. Pode indicar vários limiares cujas notificações irá receber e a Aplicação Lógica demonstrará a capacidade de executar diferentes ações com base no limiar atingido. Neste exemplo, vai configurar um cenário em que obtém duas notificações; a primeira notificação é para quando atingir 80% do orçamento e a segunda notificação é para quando atingir 100% do orçamento. A aplicação lógica será utilizada para encerrar todas as VMs no grupo de recursos. Primeiro, o limiar **Opcional** será atingido aos 80% e, em seguida, será atingido o segundo limiar, que fará com que todas as VMs na subscrição sejam encerradas.

As aplicações lógicas permitem que indique um esquema de exemplo para o acionador HTTP, mas requerem que defina o cabeçalho  **Content-Type**. Como o grupo de ações não tem cabeçalhos personalizados para o webhook, tem de analisar a carga num passo separado. Vai utilizar a ação **Analisar** e incluir um payload de exemplo.

### <a name="create-the-logic-app"></a>Criar a aplicação lógica

A aplicação lógica executará várias ações. A lista a seguir fornece um conjunto de ações de alto nível que a aplicação lógica executará:
- Reconhece quando é recebido um pedido HTTP
- Analisa os dados transmitidos no JSON para determinar o valor de limiar que foi atingido
- Utiliza uma instrução condicional para verificar se o valor do limiar atingiu os 80% ou mais do intervalo do orçamento, mas sem ultrapassar nem chegar aos 100%.
    - Se este valor de limiar tiver sido atingido, envie um HTTP POST com o webhook denominado **Opcional**. Esta ação encerrará as VMs no grupo “Opcional”.
- Utilize uma instrução condicional para verificar se o valor do limiar atingiu ou excedeu os 100% do valor do orçamento.
    - Se este valor de limiar tiver sido atingido, envie um HTTP POST com o webhook denominado **Concluído**. Esta ação encerrará todas as restantes VMs.

Os passos a seguir são necessários para criar a aplicação lógica que executará os passos acima:

1.  No [portal do Azure](https://portal.azure.com/), selecione **Criar um recurso** > **Integração** > **Aplicação Lógica**.

    ![Azure – Selecionar o recurso da Aplicação Lógica](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  No painel **Criar aplicação lógica**, indique os detalhes necessários para criar a aplicação lógica, selecione **Afixar no dashboard** e clique em **Criar**.

    ![Azure – Criar uma Aplicação Lógica](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Depois de o Azure implementar a aplicação lógica, o **Estruturador de Aplicações Lógicas** abre e mostra um painel com um vídeo de introdução e os acionadores habitualmente utilizados.

### <a name="add-a-trigger"></a>Adicionar um acionador

Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho. As ações são todos os passos que ocorrem após o acionador.

1.  Em **Modelos **, no painel** Estruturador de Aplicações Lógicas **, escolha** Aplicação Lógica em Branco**.
2.  Adicione um [acionador](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) ao introduzir “pedido http” na caixa de pesquisa do **Estruturador de Aplicações Lógicas** para localizar e selecionar o acionador chamado **Pedido – Quando um pedido HTTP é recebido**.

    ![Azure – Aplicação lógica – Acionador Http](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Selecione **Novo passo** > **Adicionar uma ação**.

    ![Azure – Novo passo – Adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Procure “analisar JSON” na caixa de pesquisa do **Estruturador de Aplicações Lógicas** para localizar e selecionar a [ação](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) **Operações de Dados – Analisar JSON**.

    ![Azure – Aplicação lógica – Adicionar ação analisar JSON](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Insira “Payload” como o nome do **Conteúdo** do payload Analisar JSON ou utilize a etiqueta “Corpo” do conteúdo dinâmico.
6.  Selecione a opção **Utilizar payload de exemplo para gerar esquema** na caixa **Analisar JSON**.

    ![Azure – Aplicação lógica – Utilizar dados JSON de exemplo para gerar esquema](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Cole o seguinte payload JSON de exemplo na caixa de texto: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    A caixa de texto será apresentada da seguinte maneira:

    ![Azure – Aplicação lógica – Payload JSON de exemplo](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Clique em **Concluído**.

### <a name="add-the-first-conditional-action"></a>Adicionar a primeira ação condicional

Utiliza uma instrução condicional para verificar se o valor do limiar atingiu os 80% ou mais do intervalo do orçamento, mas sem ultrapassar nem chegar aos 100%. Se este valor de limiar tiver sido atingido, envie um HTTP POST com o webhook denominado **Opcional**. Esta ação encerrará as VMs no grupo **Opcional**.

1.  Selecione **Novo passo** > **Adicionar uma condição**.

    ![Azure – Aplicação lógica – Adicionar uma condição](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  Na caixa **Condição**, clique na caixa de texto que contém **Escolher um valor** para apresentar uma lista dos valores disponíveis.

    ![Azure – Aplicação lógica – Caixa Condição](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Clique em **Expressão** na parte superior da lista e introduza a seguinte expressão no editor de expressões: `float()`

    ![Azure – Aplicação lógica – Expressão float](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Selecione **Conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para povoar toda a expressão.

    A expressão será a seguinte:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Selecione **OK** para definir a expressão.
6.  Selecione **é maior ou igual a** na caixa pendente da **Condição**.
7.  Na caixa **Escolher um valor** da condição, introduza `.8`.

    ![Azure – Aplicação lógica – Expressão float com um valor](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Clique em **Adicionar** > **Adicionar linha** na caixa Condição para adicionar uma parte adicional da condição.
9.  Na caixa **Condição**, clique na caixa de texto que contém **Escolher um valor**.
10. Clique em **Expressão** na parte superior da lista e introduza a seguinte expressão no editor de expressões: `float()`
11. Selecione **Conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para povoar toda a expressão.
12. Selecione **OK** para definir a expressão.
13. Selecione **é menor que** na caixa pendente da **Condição**.
14. Na caixa **Escolher um valor** da condição, introduza `1`.

    ![Azure – Aplicação lógica – Expressão float com um valor](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. Na caixa **Se verdadeiro**, selecione **Adicionar uma ação**. Vai adicionar uma ação HTTP POST que encerrará as VMs opcionais.

    ![Azure – Aplicação lógica – Adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Introduza **HTTP** para procurar a ação HTTP e selecione a ação **HTTP – HTTP**.

    ![Azure – Aplicação lógica – Adicionar ação HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Selecione **Publicar** como valor do **Método**.
18. Introduza o URL do webhook chamado **Opcional** que criou anteriormente neste tutorial como o valor do **Url**.

    ![Azure – Aplicação lógica – URL da ação HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Selecione **Adicionar uma ação** na caixa **Se verdadeiro**. Vai adicionar uma ação de e-mail que enviará um e-mail a notificar o destinatário de que as VMs opcionais foram encerradas.
20. Procure “enviar e-mail e selecione uma ação *enviar e-mail* com base no serviço de e-mail que utiliza.

    ![Azure – Aplicação Lógica – Ação enviar e-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Par contas Microsoft pessoais, selecione **Outlook.com**. Para contas escolares ou profissionais do Azure, selecione **Office 365 Outlook** (Outlook do Office 365). Se ainda não tiver uma ligação, é-lhe pedido que inicie sessão na sua conta de e-mail. O Logic Apps cria uma ligação para a sua conta de e-mail.

    Terá de permitir que a Aplicação Lógica aceda às suas informações de e-mail.

    ![Azure – Aplicação Lógica – Aviso de acesso](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Adicione o texto **Para**, **Assunto** e **Corpo** ao e-mail que notifica o destinatário de que as VMs opcionais foram encerradas. Utilize o conteúdo dinâmico **BudgetName** e **NotificationThresholdAmount** para povoar os campos do assunto e do corpo.

    ![Azure – Aplicação Lógica – Detalhes do e-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Adicionar a segunda ação condicional

Utilize uma instrução condicional para verificar se o valor do limiar atingiu ou excedeu os 100% do valor do orçamento. Se este valor de limiar tiver sido atingido, envie um HTTP POST com o webhook denominado **Concluído**. Esta ação encerrará todas as restantes VMs.

1.  Selecione **Novo passo** > **Adicionar uma Condição**.

    ![Azure – Aplicação lógica – Adicionar ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  Na caixa **Condição**, clique na caixa de texto que contém **Escolher um valor** para apresentar uma lista dos valores disponíveis.
3.  Clique em **Expressão** na parte superior da lista e introduza a seguinte expressão no editor de expressões: `float()`
4.  Selecione **Conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para povoar toda a expressão.

    A expressão será a seguinte:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Selecione **OK** para definir a expressão.
6.  Selecione **é maior ou igual a** na caixa pendente da **Condição**.
7.  Na caixa **Escolher um valor** da condição, introduza `1`.

    ![Azure – Aplicação lógica – Definir valor da condição](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  Na caixa **Se verdadeiro**, selecione **Adicionar uma ação**. Vai adicionar uma ação HTTP POST que encerrará as VMs restantes.

    ![Azure – Aplicação lógica – Adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Introduza **HTTP** para procurar a ação HTTP e selecione a ação **HTTP – HTTP**.
10. Selecione **Publicar** como valor do **Método**.
11. Introduza o URL do webhook chamado **Concluído** que criou anteriormente neste tutorial como o valor do **Url**.

    ![Azure – Aplicação lógica – Adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Selecione **Adicionar uma ação** na caixa **Se verdadeiro**. Vai adicionar uma ação de e-mail que enviará um e-mail a notificar o destinatário de que as restantes VMs foram encerradas.
13. Procure “enviar e-mail e selecione uma ação *enviar e-mail* com base no serviço de e-mail que utiliza.
14. Adicione o texto **Para**, **Assunto** e **Corpo** ao e-mail que notifica o destinatário de que as VMs opcionais foram encerradas. Utilize o conteúdo dinâmico **BudgetName** e **NotificationThresholdAmount** para povoar os campos do assunto e do corpo.

    ![Azure – Aplicação Lógica – Detalhes de enviar e-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Clique em **Guardar**, na parte superior do painel **Estruturador de Aplicações Lógicas**.

### <a name="logic-app-summary"></a>Resumo da Aplicação Lógica

A aplicação lógica terá o aspeto deste exemplo, quando tiver terminado. No mais básico dos cenários em que não precisa de nenhuma orquestração baseada em limiares, pode chamar diretamente o script de automatização no **Monitor** e ignorar o passo da **Aplicação Lógica**.

   ![Azure – Aplicação lógica – Vista completa](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Quando guardou a aplicação lógica, foi gerado um URL que poderá chamar. Vai utilizar este URL na secção seguinte deste tutorial.

## <a name="create-an-azure-monitor-action-group"></a>Criar um Grupo de Ações do Azure Monitor

Um grupo de ações é uma coleção de preferências de notificação definida por si. Quando é acionado um alerta, um grupo de ações específico pode receber o alerta através de notificação. Um alerta do Azure gera proativamente uma notificação com base em condições específicas e fornece a oportunidade de agir. Um alerta pode utilizar dados de várias origens, incluindo métricas e registos.

Os grupos de ações são o único ponto final que integrará no orçamento. Pode configurar notificações em vários canais, mas, para este cenário, vai concentrar-se na aplicação lógica criada anteriormente neste tutorial.

### <a name="create-an-action-group-in-azure-monitor"></a>Criar um grupo de ações no Azure Monitor

Ao criar o grupo de ações, vai apontar para aplicação lógica que criou anteriormente neste tutorial.

1.  Se ainda não tiver iniciado sessão no [portal do Azure](https://portal.azure.com/), inicie sessão e selecione **Todos os serviços** > **Monitor**.
2.  Selecione **Grupos de ações** na secção **Configuração**.
3.  Selecione **Adicionar um grupo de ações** no painel **Grupo de ações**.
4.  Adicione e verifique os seguintes itens:
    - Nome do grupo de ações
    - Nome abreviado
    - Subscrição
    - Grupo de recursos

    ![Azure – Aplicação lógica – Adicionar um grupo de ações](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  No painel **Adicionar grupo de ações**, adicione uma ação LogicApp. Dê à ação o nome **Budget-BudgetLA**. No painel **Aplicação Lógica**, selecione a **Subscrição** e o **Grupo de recursos**. Em seguida, selecione a **Aplicação lógica** que criou anteriormente neste tutorial.
6.  Clique em **OK** para definir a aplicação lógica. Em seguida, selecione **OK** no painel **Adicionar grupo de ações** para criar o grupo de ações.

Concluiu todos os componentes de suporte necessários para orquestrar o orçamento com eficiência. Agora, só precisa de criar o orçamento e configurá-lo para utilizar o grupo de ações que criou.

## <a name="create-the-azure-budget"></a>Criar o Orçamento do Azure

Pode criar um orçamento no portal do Azure com a [funcionalidade Orçamento](../cost-management/tutorial-acm-create-budgets.md) no Cost Management. Ou, pode criar um orçamento com as APIs REST, os cmdlets do PowerShell ou utilizar a CLI. O procedimento a seguir utiliza a API REST. Antes de chamar a API REST, precisará de um token de autorização. Para criar um token de autorização, pode utilizar o projeto [ARMClient](https://github.com/projectkudu/ARMClient). O **ARMClient** permite que se autentique no Azure Resource Manager e obtenha um token para chamar as APIs.

### <a name="create-an-authentication-token"></a>Criar um token de autenticação

1.  Navegue até ao projeto [ARMClient](https://github.com/projectkudu/ARMClient) no GitHub.
2.  Clone o repositório para obter uma cópia local.
3.  Abra o projeto no Visual Studio e compile-o.
4.  Após a compilação ter sido bem-sucedida, o executável deverá estar na pasta *\bin\debug*.
5.  Execute o ARMClient. Abra uma linha de comandos e navegue até à pasta *\bin\debug* na raiz do projeto.
6.  Para iniciar sessão e autenticar, introduza o seguinte comando na linha de comandos:<br>
    `ARMClient login prod`
7.  Copie o **guid de subscrição** da saída.
8.  Para copiar um token de autorização para a área de transferência, introduza o seguinte comando na linha de comandos, mas confirme que utiliza o ID de subscrição copiado do passo acima: <br>
    `ARMClient token <subscription GUID from previous step>`

    Após ter concluído o passo acima, verá o seguinte:<br>
    **Token copiado para a área de transferência com êxito.**
9.  Guarde o token para utilizar nos passos na próxima secção deste tutorial.

### <a name="create-the-budget"></a>Criar o Orçamento

Em seguida, vai configurar o **Postman** para criar um orçamento ao chamar as APIs REST de Consumo do Azure. O Postman é um Ambiente de desenvolvimento de APIs. Vai importar ficheiros de ambiente e de coleção para o Postman. A coleção contém definições agrupadas de pedidos HTTP que chamam as APIs REST de Consumo do Azure. O ficheiro de ambiente contém variáveis que são utilizadas pela coleção.

1.  Transfira e abra o [Cliente REST do Postman](https://www.getpostman.com/) para executar as APIs REST.
2.  No Postman, crie um novo pedido.

    ![Postman – Criar um novo pedido](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Guarde o novo pedido como uma coleção, para que o novo pedido não tenha nada nele.

    ![Postman – Guardar o novo pedido](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Altere o pedido de uma ação `Get` para uma `Put`.
5.  Modifique o URL a seguir ao substituir `{subscriptionId}` pelo **ID da Subscrição** que utilizou na secção anterior deste tutorial. Além disso, modifique o URL para incluir “SampleBudget” como o valor de `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Selecione o separador **Cabeçalhos** no Postman.
7.  Adicione uma nova **Chave** chamada “Autorização”.
8.  Defina o **Valor** para o token que foi criado com o ArmClient no final da última secção.
9.  Selecione o separador **Corpo** no Postman.
10. Selecione opção de botão **não processado**.
11. Na caixa de texto, cole na definição de orçamento do exemplo abaixo, mas tem de substituir os parâmetros **subscriptionid**, **budgetname** e **actiongroupname** pelo seu ID da subscrição, pelo nome exclusivo do seu orçamento e pelo nome do grupo de ações que criou tanto no URL quanto no corpo do pedido:

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
12. Prima **Enviar** para enviar o pedido.

Agora tem todas as partes necessárias para chamar a [API de orçamentos](https://docs.microsoft.com/rest/api/consumption/budgets). A referência da API de orçamentos tem detalhes adicionais sobre os pedidos específicos, incluindo o seguinte:
    - **budgetname** – suporta vários orçamentos.  Os nomes dos orçamentos têm de ser exclusivos.
    - **categoria** – tem de ser **Custo** ou **Utilização**. A API suporta orçamentos de custos e de utilização.
    - **timeGrain** – um orçamento mensal, trimestral ou anual. O valor é redefinido no final do período.
    - **filtros** – os filtros permitem restringir o orçamento a um conjunto específico de recursos dentro do âmbito selecionado. Por exemplo, um filtro pode ser uma coleção de grupos de recursos para um orçamento de nível de subscrição.
    - **notificações** – determina os detalhes e os limiares de notificação. Pode configurar vários limiares e indicar um endereço de e-mail ou um grupo de ações para que recebam uma notificação.

## <a name="summary"></a>Resumo

Ao seguir este tutorial, ficou a saber:
- Como criar um Runbook de Automatização do Azure para encerrar VMs.
- Como criar uma Aplicação Lógica do Azure para ser acionada com base nos valores de limiar de orçamento e chamar o runbook associado com os parâmetros corretos.
- Como criar um Grupo de Ações do Azure Monitor que será configurado para acionar a Aplicação Lógica do Azure quando o limiar do orçamento for atingido.
- Como criar o orçamento do Azure com os limiares desejados e ligá-lo ao grupo de ações.

Agora tem um orçamento totalmente funcional para a sua subscrição que encerrará as VMs quando forem atingidos os limiares de orçamento configurados.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre cenários de faturação do Azure, veja [Cenários de automatização de faturação e gestão de custos](billing-cost-management-automation-scenarios.md).
