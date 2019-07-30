---
title: Cenário de orçamento de cobrança e gerenciamento de custos do Azure | Microsoft Docs
description: Saiba como usar a automação do Azure para desligar VMs com base em limites de orçamento específicos.
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
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443464"
---
# <a name="manage-costs-with-azure-budgets"></a>Gerir os custos com os Orçamentos do Azure

O controle de custo é um componente crítico para maximizar o valor de seu investimento na nuvem. Há vários cenários em que a visibilidade de custos, os relatórios e a orquestração baseada em custo são fundamentais para operações de negócios contínuas. As [APIs de gerenciamento de custos do Azure](https://docs.microsoft.com/rest/api/consumption/) fornecem um conjunto de APIs para dar suporte a cada um desses cenários. As APIs fornecem detalhes de uso, permitindo que você exiba os custos de nível de instância granular.

Os orçamentos são normalmente usados como parte do controle de custo. Os orçamentos podem ser delimitados no Azure. Por exemplo, você pode restringir sua exibição de orçamento com base na assinatura, nos grupos de recursos ou em uma coleção de recursos. Além de usar a API de orçamentos para notificá-lo por email quando um limite de orçamento é atingido, você pode usar [Azure monitor grupos de ação](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) para disparar um conjunto de ações orquestradas como resultado de um evento de orçamento.

Um cenário de orçamentos comum para um cliente que executa uma carga de trabalho não crítica pode ocorrer quando desejam gerenciar em um orçamento e também obter um custo previsível ao examinar a nota fiscal mensal. Esse cenário requer uma orquestração baseada em custo de recursos que fazem parte do ambiente do Azure. Nesse cenário, um orçamento mensal de $1000 para a assinatura é definido. Além disso, os limites de notificação são definidos para disparar algumas orquestrações. Esse cenário começa com um limite de custo de 80%, o que irá parar todas as VMs no grupo de recursos **opcional**. Em seguida, no limite de custo de 100%, todas as instâncias de VM serão interrompidas.
Para configurar esse cenário, você concluirá as seguintes ações seguindo as etapas fornecidas em cada seção deste tutorial.

Essas ações incluídas neste tutorial permitem que você:

- Crie um runbook de automação do Azure para parar as VMs usando WebHooks.
- Crie um aplicativo lógico do Azure para ser disparado com base no valor do limite de orçamento e chame o runbook com os parâmetros corretos.
- Crie um grupo de ação Azure Monitor que será configurado para disparar o aplicativo lógico do Azure quando o limite de orçamento for atingido.
- Crie o orçamento do Azure com os limites desejados e conecte-o ao grupo de ações.

## <a name="create-an-azure-automation-runbook"></a>Criar um runbook de automação do Azure

A [automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) é um serviço que permite gerar scripts para a maioria das tarefas de gerenciamento de recursos e executar essas tarefas como agendadas ou sob demanda. Como parte desse cenário, você criará um [runbook de automação do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-types) que será usado para parar as VMs. Você usará o runbook gráfico [parar VMs do Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) da [Galeria](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) para criar esse cenário. Ao importar esse runbook para sua conta do Azure e publicá-lo, você poderá parar as VMs quando um limite de orçamento for atingido.

### <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com as credenciais da sua conta do Azure.
2. Clique no botão **criar um recurso** encontrado no canto superior esquerdo do Azure.
3. Selecione**automação**das **ferramentas** > de gerenciamento.
   > [!NOTE]
   > Se você não tiver uma conta do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/).
4. Insira as informações da sua conta. Para **criar conta Executar como do Azure**, escolha **Sim** para habilitar automaticamente as configurações necessárias para simplificar a autenticação no Azure.
5. Quando terminar, clique em **Criar**, para iniciar a implementação da conta de Automatização.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importar o runbook parar VMs do Azure v2

Usando um [runbook de automação do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-types), importe o runbook gráfico [parar VMs do Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) da galeria.

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/) com as credenciais da sua conta do Azure.
2.  Abra sua conta de automação selecionando **todos os serviços** > **contas de automação**. Em seguida, selecione sua conta de automação.
3.  Clique em **Galeria de Runbooks** na seção automação de **processo** .
4.  Defina a **origem da Galeria** como **Script Center** e selecione **OK**.
5.  Localize e selecione o item parar a Galeria de [VMs do Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) dentro do portal do Azure.
6.  Clique no botão **importar** para exibir a folha **importar** e selecione **OK**. A folha de visão geral do runbook será exibida.
7.  Depois que o runbook tiver concluído o processo de importação, selecione **Editar** para exibir o editor de runbook gráfico e a opção de publicação.

    ![Azure-editar runbook gráfico](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Clique no botão **publicar** para publicar o runbook e, em seguida, selecione **Sim** quando solicitado. Ao publicar um runbook, você substitui qualquer versão publicada existente pela versão de rascunho. Nesse caso, você não tem nenhuma versão publicada porque criou o runbook.

    Para obter mais informações sobre como publicar um runbook, consulte [criar um runbook gráfico](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Criar WebHooks para o runbook

Usando o runbook gráfico [parar VMs do Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) , você criará dois WebHooks para iniciar o runbook na automação do Azure por meio de uma única solicitação HTTP. O primeiro webhook invocará o runbook em um limite de orçamento de 80% com o nome do grupo de recursos como um parâmetro, permitindo que as VMs opcionais sejam interrompidas. Em seguida, o segundo webhook invocará o runbook sem parâmetros (às 100%), o que irá parar todas as instâncias de VM restantes.

1. Na página **Runbooks** no [portal do Azure](https://portal.azure.com/), clique no runbook **StopAzureV2Vm** que exibe a folha de visão geral do runbook.
2. Clique em webhook na parte superior da página para abrir a folha **Adicionar webhook** .
3. Clique em **criar novo webhook** para abrir a folha **criar um novo webhook** .
4. Defina o **nome** do webhook como **opcional**. A propriedade **Enabled** deve ser **Sim**. O valor de expirações não precisa ser alterado. Para obter mais informações sobre as propriedades do webhook, consulte os [detalhes de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. Ao lado do valor da URL, clique no ícone de cópia para copiar a URL do webhook.
   > [!IMPORTANT]
   > Salve a URL do webhook chamada **opcional** em um local seguro. Você usará a URL mais tarde neste tutorial. Por motivos de segurança, depois de criar o webhook, você não pode exibir ou recuperar a URL novamente.
6. Clique em **OK** para criar o novo webhook.
7. Clique em **configurar parâmetros e configurações de execução** para exibir valores de parâmetro para o runbook.
   > [!NOTE]
   > Se o runbook tiver parâmetros obrigatórios, você não poderá criar o webhook, a menos que os valores sejam fornecidos.
8. Clique em **OK** para aceitar os valores de parâmetro do webhook.
9. Clique em **criar** para criar o webhook.
10. Em seguida, siga as etapas acima para criar um segundo webhook chamado **Complete**.
    > [!IMPORTANT]
    > Lembre-se de salvar as URLs de webhook a serem usadas posteriormente neste tutorial. Por motivos de segurança, depois de criar o webhook, você não pode exibir ou recuperar a URL novamente.

Agora você deve ter dois WebHooks configurados que estão disponíveis usando as URLs que você salvou.

![WebHooks – opcional e completo](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Agora, você concluiu a configuração da automação do Azure. Você pode testar os WebHooks com um teste de postmaster simples para validar que o webhook funciona. Em seguida, você deve criar o aplicativo lógico para orquestração.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Criar um aplicativo lógico do Azure para orquestração

Os aplicativos lógicos ajudam você a criar, agendar e automatizar processos como fluxos de trabalho para que você possa integrar aplicativos, dados, sistemas e serviços entre empresas ou organizações. Nesse cenário, o [aplicativo lógico](https://docs.microsoft.com/azure/logic-apps/) que você cria fará um pouco mais do que simplesmente chamar o webhook de automação que você criou.

Os orçamentos podem ser configurados para disparar uma notificação quando um limite especificado é atingido. Você pode fornecer vários limites a serem notificados em e o aplicativo lógico demonstrará a capacidade de executar ações diferentes com base no limite atendido. Neste exemplo, você configurará um cenário em que você obtém algumas notificações, a primeira notificação é para quando 80% do orçamento foi atingido e a segunda notificação é quando 100% do orçamento foi atingido. O aplicativo lógico será usado para desligar todas as VMs no grupo de recursos. Primeiro, o limite **opcional** será atingido às 80%, e o segundo limite será atingido onde todas as VMs na assinatura serão desligadas.

Os aplicativos lógicos permitem que você forneça um esquema de exemplo para o gatilho HTTP, mas exige que você defina o cabeçalho **Content-Type** . Como o grupo de ações não tem cabeçalhos personalizados para o webhook, você deve analisar a carga em uma etapa separada. Você usará a ação de **análise** e a fornecerá com um conteúdo de exemplo.

### <a name="create-the-logic-app"></a>Criar o aplicativo lógico

O aplicativo lógico executará várias ações. A lista a seguir fornece um conjunto de ações de alto nível que o aplicativo lógico executará:
- Reconhece quando uma solicitação HTTP é recebida
- Analisar os dados passados em JSON para determinar o valor de limite que foi atingido
- Use uma instrução condicional para verificar se o valor do limite atingiu 80% ou mais do intervalo de orçamento, mas não maior ou igual a 100%.
    - Se essa quantidade de limite tiver sido atingida, envie um HTTP POST usando o webhook denominado **opcional**. Essa ação desligará as VMs no grupo "opcional".
- Use uma instrução condicional para verificar se o valor do limite atingiu ou excedeu 100% do valor do orçamento.
    - Se o valor do limite for atingido, envie um HTTP POST usando o webhook chamado **Complete**. Esta ação desligará todas as VMs restantes.

As etapas a seguir são necessárias para criar o aplicativo lógico que executará as etapas acima:

1.  No [portal do Azure](https://portal.azure.com/), selecione **criar um recurso** > **aplicativo lógico**de**integração** > .

    ![Azure – selecione o recurso de aplicativo lógico](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  Na folha **criar aplicativo lógico** , forneça os detalhes necessários para criar seu aplicativo lógico, selecione **fixar no painel**e clique em **criar**.

    ![Azure-criar um aplicativo lógico](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Depois que o Azure implanta seu aplicativo lógico, o **Designer de aplicativos lógicos** é aberto e mostra uma folha com um vídeo de introdução e gatilhos comumente usados.

### <a name="add-a-trigger"></a>Adicionar um acionador

Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho. Ações são todas as etapas que acontecem após o gatilho.

1.  Em **modelos** da folha **Designer de aplicativos lógicos** , escolha **aplicativo lógico em branco**.
2.  Adicione um [gatilho](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) digitando "solicitação HTTP" na caixa de pesquisa **Designer de aplicativos lógicos** para localizar e selecionar a solicitação chamada de gatilho **– quando uma solicitação HTTP é recebida**.

    ![Gatilho do aplicativo Azure-Logic-http](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Selecione **nova etapa** > **Adicionar uma ação**.

    ![Azure-nova etapa – adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Pesquise "analisar JSON" na caixa de pesquisa do **Designer de aplicativos lógicos** para localizar e selecionar a [ação](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) **operações de dados – analisar JSON** .

    ![Aplicativo Azure-lógico-Adicionar ação JSON de análise](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Insira "Payload" como o nome do **conteúdo** para a carga JSON de análise ou use a marca "corpo" do conteúdo dinâmico.
6.  Selecione a opção **usar conteúdo de exemplo para gerar esquema** na caixa **analisar JSON** .

    ![Aplicativo Azure-lógico-use dados JSON de exemplo para gerar o esquema](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Cole a seguinte carga de exemplo JSON na caixa de texto:`{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    A caixa de texto será exibida da seguinte maneira:

    ![Aplicativo Azure-lógico-o conteúdo JSON de exemplo](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Clique em **Concluído**.

### <a name="add-the-first-conditional-action"></a>Adicionar a primeira ação condicional

Use uma instrução condicional para verificar se o valor do limite atingiu 80% ou mais do intervalo de orçamento, mas não maior ou igual a 100%. Se essa quantidade de limite tiver sido atingida, envie um HTTP POST usando o webhook denominado **opcional**. Essa ação desligará as VMs no grupo **opcional** .

1.  Selecione **nova etapa** > **Adicionar uma condição**.

    ![Aplicativo Azure-lógico-adicionar uma condição](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  Na caixa **condição** , clique no TextBox que contém **escolher um valor** para exibir uma lista de valores disponíveis.

    ![Caixa do aplicativo Azure-lógico-condição](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Clique em **expressão** na parte superior da lista e insira a seguinte expressão no editor de expressão:`float()`

    ![Aplicativo Azure-lógico-expressão float](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Selecione **conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para preencher a expressão completa.

    A expressão será a seguinte:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Selecione **OK** para definir a expressão.
6.  Select **é maior ou igual a** na caixa suspensa da **condição**.
7.  Na caixa **escolher um valor** da condição, digite `.8`.

    ![Aplicativo Azure-lógico-expressão float com um valor](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Clique em **Adicionar** > **Adicionar linha** dentro da caixa condição para adicionar uma parte adicional da condição.
9.  Na caixa **condição** , clique no TextBox que contém **escolher um valor**.
10. Clique em **expressão** na parte superior da lista e insira a seguinte expressão no editor de expressão:`float()`
11. Selecione **conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para preencher a expressão completa.
12. Selecione **OK** para definir a expressão.
13. Select **é menor que** na caixa suspensa da **condição**.
14. Na caixa **escolher um valor** da condição, digite `1`.

    ![Aplicativo Azure-lógico-expressão float com um valor](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. Na caixa **se verdadeiro** , selecione **Adicionar uma ação**. Você adicionará uma ação HTTP POST que desligará as VMs opcionais.

    ![Aplicativo Azure-lógico-adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Insira **http** para pesquisar a ação http e selecione a ação **http – http** .

    ![Aplicativo Azure-lógico-Adicionar ação HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Selecione **post** como o para o valor do **método** .
18. Insira a URL para o webhook chamado **opcional** que você criou anteriormente neste tutorial como o valor do **URI** .

    ![URI de ação do aplicativo Azure-Logic-HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Selecione **Adicionar uma ação** na caixa **se verdadeiro** . Você adicionará uma ação de email que enviará um email notificando o destinatário de que as VMs opcionais foram desligadas.
20. Pesquise "enviar email" e selecione uma ação *Enviar email* com base no serviço de email que você usa.

    ![Aplicativo Azure-lógico – enviar ação de email](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Par contas Microsoft pessoais, selecione **Outlook.com**. Para contas escolares ou profissionais do Azure, selecione **Office 365 Outlook** (Outlook do Office 365). Se ainda não tiver uma ligação, é-lhe pedido que inicie sessão na sua conta de e-mail. O Logic Apps cria uma ligação para a sua conta de e-mail.

    Você precisará permitir que o aplicativo lógico acesse suas informações de email.

    ![Azure-aviso de acesso ao aplicativo lógico](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Adicione o **a**, o **assunto**e o texto do **corpo** do email que notifica o destinatário de que as VMs opcionais foram desligadas. Use o **orcaname** e o conteúdo dinâmico **NotificationThresholdAmount** para preencher os campos de assunto e corpo.

    ![Aplicativo Azure-lógico-detalhes do email](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Adicionar a segunda ação condicional

Use uma instrução condicional para verificar se o valor do limite atingiu ou excedeu 100% do valor do orçamento. Se o valor do limite for atingido, envie um HTTP POST usando o webhook chamado **Complete**. Esta ação desligará todas as VMs restantes.

1.  Selecione **nova etapa** > **Adicionar uma condição**.

    ![Aplicativo Azure-lógico-Adicionar ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  Na caixa **condição** , clique no TextBox que contém **escolher um valor** para exibir uma lista de valores disponíveis.
3.  Clique em **expressão** na parte superior da lista e insira a seguinte expressão no editor de expressão:`float()`
4.  Selecione **conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para preencher a expressão completa.

    A expressão será a seguinte:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Selecione **OK** para definir a expressão.
6.  Select **é maior ou igual a** na caixa suspensa da **condição**.
7.  Na **caixa escolher um valor** da condição, digite `1`.

    ![Valor da condição do conjunto de aplicativos do Azure-Logic](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  Na caixa **se verdadeiro** , selecione **Adicionar uma ação**. Você adicionará uma ação HTTP POST que fechará todas as VMs restantes.

    ![Aplicativo Azure-lógico-adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Insira **http** para pesquisar a ação http e selecione a ação **http – http** .
10. Selecione **post** como o para o valor do **método** .
11. Insira a URL para o webhook chamado **Complete** que você criou anteriormente neste tutorial como o valor do **URI** .

    ![Aplicativo Azure-lógico-adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Selecione **Adicionar uma ação** na caixa **se verdadeiro** . Você adicionará uma ação de email que enviará um email notificando o destinatário de que as VMs restantes foram desligadas.
13. Pesquise "enviar email" e selecione uma ação *Enviar email* com base no serviço de email que você usa.
14. Adicione o **a**, o **assunto**e o texto do **corpo** do email que notifica o destinatário de que as VMs opcionais foram desligadas. Use o **orcaname** e o conteúdo dinâmico **NotificationThresholdAmount** para preencher os campos de assunto e corpo.

    ![Aplicativo Azure-lógico-enviar detalhes do email](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Clique em **salvar** na parte superior da folha do **Designer de aplicativo lógico** .

### <a name="logic-app-summary"></a>Resumo do aplicativo lógico

Esta é a aparência de seu aplicativo lógico quando você terminar. No mais básico dos cenários em que você não precisa de nenhuma orquestração baseada em limites, você pode chamar diretamente o script de automação do **Monitor** e ignorar a etapa do **aplicativo lógico** .

   ![Azure-aplicativo lógico-exibição completa](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Quando você salvou seu aplicativo lógico, foi gerada uma URL que você poderá chamar. Você usará essa URL na próxima seção deste tutorial.

## <a name="create-an-azure-monitor-action-group"></a>Criar um grupo de ação Azure Monitor

Um grupo de ações é uma coleção de preferências de notificação que você define. Quando um alerta é disparado, um grupo de ação específico pode receber o alerta ao ser notificado. Um alerta do Azure gera proativamente uma notificação com base em condições específicas e fornece a oportunidade de agir. Um alerta pode usar dados de várias fontes, incluindo métricas e logs.

Grupos de ação são o único ponto de extremidade que você integrará com seu orçamento. Você pode configurar notificações em vários canais, mas, para esse cenário, você se concentrará no aplicativo lógico criado anteriormente neste tutorial.

### <a name="create-an-action-group-in-azure-monitor"></a>Criar um grupo de ação no Azure Monitor

Ao criar o grupo de ações, você apontará para o aplicativo lógico que você criou anteriormente neste tutorial.

1.  Se você ainda não tiver entrado no [portal do Azure](https://portal.azure.com/), entre e selecione o**Monitor** **todos os serviços** > .
2.  Selecione **grupos de ações** na seção **configuração** .
3.  Selecione **Adicionar um grupo de ações** na folha **grupos de ações** .
4.  Adicione e verifique os seguintes itens:
    - Nome do grupo de ações
    - Nome abreviado
    - Subscription
    - Resource group

    ![Aplicativo Azure-lógico-adicionar um grupo de ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  No painel **Adicionar grupo de ações** , adicione uma ação LogicApp. Nomeie a ação **Budget-BudgetLA**. No painel **aplicativo lógico** , selecione a **assinatura** e o **grupo de recursos**. Em seguida, selecione o **aplicativo lógico** que você criou anteriormente neste tutorial.
6.  Clique em **OK** para definir o aplicativo lógico. Em seguida, selecione **OK** no painel **Adicionar grupo de ações** para criar o grupo de ações.

Você concluiu todos os componentes de suporte necessários para orquestrar seu orçamento com eficiência. Agora tudo o que você precisa fazer é criar o orçamento e configurá-lo para usar o grupo de ação que você criou.

## <a name="create-the-azure-budget"></a>Criar o orçamento do Azure

Você pode criar um orçamento no portal do Azure usando o [recurso de orçamento](../cost-management/tutorial-acm-create-budgets.md) no gerenciamento de custos. Ou, você pode criar um orçamento usando as APIs REST, os cmdlets do PowerShell ou usar a CLI. O procedimento a seguir usa a API REST. Antes de chamar a API REST, você precisará de um token de autorização. Para criar um token de autorização, você pode usar o projeto [ARMClient](https://github.com/projectkudu/ARMClient) . O **ARMClient** permite que você se autentique no Azure Resource Manager e obtenha um token para chamar as APIs.

### <a name="create-an-authentication-token"></a>Criar um token de autenticação

1.  Navegue até o projeto [ARMClient](https://github.com/projectkudu/ARMClient) no github.
2.  Clone o repositório para obter uma cópia local.
3.  Abra o projeto no Visual Studio e compile-o.
4.  Depois que a compilação for bem-sucedida, o executável deverá estar na pasta *\bin\Debug* .
5.  Execute o ARMClient. Abra um prompt de comando e navegue até a pasta *\bin\Debug* da raiz do projeto.
6.  Para fazer logon e autenticar, insira o seguinte comando no prompt de comando:<br>
    `ARMClient login prod`
7.  Copie o **GUID da assinatura** da saída.
8.  Para copiar um token de autorização para a área de transferência, digite o seguinte comando no prompt de comando, mas certifique-se de usar a ID da assinatura copiada da etapa acima: <br>
    `ARMClient token <subscription GUID from previous step>`

    Depois de concluir a etapa acima, você verá o seguinte:<br>
    **Token copiado para a área de transferência com êxito.**
9.  Salve o token a ser usado para as etapas na próxima seção deste tutorial.

### <a name="create-the-budget"></a>Criar o orçamento

Em seguida, você configurará o **postmaster** para criar um orçamento chamando as APIs REST de consumo do Azure. O postmaster é um ambiente de desenvolvimento de API. Você importará arquivos de ambiente e de coleção no postmaster. A coleção contém definições agrupadas de solicitações HTTP que chamam APIs REST de consumo do Azure. O arquivo de ambiente contém variáveis que são usadas pela coleção.

1.  Baixe e abra o [cliente REST do postmaster](https://www.getpostman.com/) para executar as APIs REST.
2.  No postmaster, crie uma nova solicitação.

    ![Postmaster-criar uma nova solicitação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Salve a nova solicitação como uma coleção, para que a nova solicitação não tenha nada sobre ela.

    ![Postmaster-salvar a nova solicitação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Altere a solicitação de um `Get` para uma `Put` ação.
5.  Modifique a URL a seguir substituindo `{subscriptionId}` pela **ID da assinatura** que você usou na seção anterior deste tutorial. Além disso, modifique a URL para incluir "SampleBudget" como o valor `{budgetName}`de:`https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Selecione a guia **cabeçalhos** no postmaster.
7.  Adicione uma nova **chave** chamada "Authorization".
8.  Defina o **valor** para o token que foi criado usando o ArmClient no final da última seção.
9.  Selecione a guia **corpo** no postmaster.
10. Selecione a opção de botão **RAW** .
11. Na caixa de texto, cole na definição de orçamento de exemplo abaixo, no entanto, você deve substituir os parâmetros SubscriptionId, **orcaname**e **actiongroupname** pela sua ID de assinatura, um nome exclusivo para seu orçamento e o nome do grupo de ação Você criou tanto na URL quanto no corpo da solicitação:

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
12. Pressione **Enviar** para enviar a solicitação.

Agora você tem todas as partes necessárias para chamar a [API](https://docs.microsoft.com/rest/api/consumption/budgets)de orçamentos. A referência de API de orçamentos tem detalhes adicionais sobre as solicitações específicas, incluindo o seguinte:
    - **budgetname** -há suporte para vários orçamentos.  Os nomes de orçamento devem ser exclusivos.
    - **categoria** -deve ser o **custo** ou o **uso**. A API dá suporte a orçamentos de uso e custo.
    - timegranular-um orçamento mensal, trimestral ou anual. O valor é redefinido no final do período.
    - **filtros** -filtros permitem restringir o orçamento a um conjunto específico de recursos dentro do escopo selecionado. Por exemplo, um filtro pode ser uma coleção de grupos de recursos para um orçamento de nível de assinatura.
    - **notificações** – determina os detalhes e os limites da notificação. Você pode configurar vários limites e fornecer um endereço de email ou um grupo de ações para receber uma notificação.

## <a name="summary"></a>Resumo

Seguindo este tutorial, você aprendeu:
- Como criar um runbook de automação do Azure para parar as VMs.
- Como criar um aplicativo lógico do Azure que é disparado com base nos valores de limite de orçamento e chamar o runbook relacionado com os parâmetros corretos.
- Como criar um grupo de ação Azure Monitor que será configurado para disparar o aplicativo lógico do Azure quando o limite de orçamento for atingido.
- Como criar o orçamento do Azure com os limites desejados e conectá-lo ao grupo de ações.

Agora você tem um orçamento totalmente funcional para sua assinatura que desligará suas VMs quando atingir os limites de orçamento configurados.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre cenários de cobrança do Azure, consulte [cenários de automação de gerenciamento de custos e cobrança](billing-cost-management-automation-scenarios.md).
