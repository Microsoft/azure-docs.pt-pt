---
title: Recuperação de desastres para contas de integração
description: Crie as suas contas de integração e artefactos B2B com recuperação de desastres entre regiões em Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 1e006dd690e9c008afada8a490da6c3238bc0791
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565416"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Criar recuperação de desastres entre regiões para contas de integração em Azure Logic Apps

As cargas de trabalho B2B envolvem transações monetárias como encomendas e faturas. Durante um evento de desastre, é fundamental que um negócio recupere rapidamente para satisfazer as SLAs de nível de negócios acordadas com os seus parceiros. Este artigo demonstra como construir um plano de continuidade de negócios para cargas de trabalho B2B. 

* Prontidão de recuperação de desastres 
* Falha na região secundária durante um evento de desastre 
* Recue para a região primária após um evento de desastre

## <a name="disaster-recovery-readiness"></a>Prontidão de recuperação de desastres  

1. Identificar uma região secundária e criar uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na região secundária.

2. Adicione parceiros, esquemas e acordos para os fluxos de mensagens necessários onde o estado de execução precisa de ser replicado para a conta de integração da região secundária.

   > [!TIP]
   > Certifique-se de que há consistência na convenção de nomeação do artefacto de integração em todas as regiões. 

3. Para retirar o estado de execução da região primária, crie uma aplicação lógica na região secundária. 

   Esta aplicação lógica deve ter um *gatilho* e uma *ação.* 
   O gatilho deve ligar-se à conta de integração da região primária, e a ação deve ligar-se à conta de integração da região secundária. 
   Com base no intervalo de tempo, o gatilho sonda a tabela de estatutos da região primária e puxa os novos registos, se houver. A ação atualiza-os para a conta de integração da região secundária. 
   Isto ajuda a obter o estado de funcionação incremental da região primária para a região secundária.

4. A continuidade do negócio na conta de integração de Apps Lógicas foi projetada para suportar com base em protocolos B2B - X12, AS2 e EDIFACT. Para encontrar etapas detalhadas, selecione os respetivos links.

5. A recomendação é a de utilizar também todos os recursos da região primária numa região secundária. 

   Os recursos da região primária incluem Azure SQL Database ou Azure Cosmos DB, Azure Service Bus e Azure Event Hubs usados para mensagens, Azure API Management, e o Azure Logic Apps recurso no Azure App Service.   

6. Estabelecer uma ligação de uma região primária para uma região secundária. Para retirar o estado de execução de uma região primária, crie uma aplicação lógica numa região secundária. 

   A aplicação lógica deve ter um gatilho e uma ação. 
   O gatilho deve ligar-se a uma conta de integração da região primária. 
   A ação deve ligar-se a uma conta de integração da região secundária. 
   Com base no intervalo de tempo, o gatilho sonda a tabela de estatutos da região primária e puxa os novos registos, se houver. 
   A ação atualiza-os para uma conta de integração da região secundária. 
   Este processo ajuda a obter o estado de funcionamento incremental da região primária para a região secundária.

A continuidade do negócio numa conta de integração de Apps Lógicas fornece suporte baseado nos protocolos B2B X12, AS2 e EDIFACT. Para obter passos detalhados sobre a utilização de X12 e AS2, consulte [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) e [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) neste artigo.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Falhar numa região secundária durante um evento de desastre

Durante um evento de catástrofe, quando a região primária não está disponível para a continuidade do negócio, o tráfego direto para a região secundária. Uma região secundária ajuda uma empresa a recuperar rapidamente funções para satisfazer o RPO/RTO acordado pelos seus parceiros. Minimiza igualmente os esforços de resso para além de uma região para outra. 

Há uma latência esperada enquanto copia os números de controlo de uma região primária para uma região secundária. Para evitar o envio de números de controlo gerados duplicados aos parceiros durante um evento de catástrofe, a recomendação é aumentar os números de controlo nos acordos da região secundária utilizando [cmdlets PowerShell](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Recue para uma região primária pós-desastre

Para voltar a uma região primária quando estiver disponível, siga estes passos:

1. Pare de aceitar mensagens de parceiros na região secundária.  

2. Incremente os números de controlo gerados para todos os acordos da região primária utilizando [cmdlets PowerShell](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Tráfego direto da região secundária para a região primária.

4. Verifique se a aplicação lógica criada na região secundária para retirar o estado de funcionação da região primária está ativada.

## <a name="x12"></a>X12 

A continuidade do negócio dos documentos EDI X12 baseia-se em números de controlo:

> [!TIP]
> Também pode usar o [modelo de arranque rápido X12](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) para criar aplicações lógicas. A criação de contas de integração primária e secundária são pré-requisitos para usar o modelo. O modelo ajuda a criar duas aplicações lógicas, uma para números de controlo recebidos e outra para números de controlo gerados. Os respetivos gatilhos e ações são criados nas aplicações lógicas, ligando o gatilho à conta de integração primária e a ação à conta de integração secundária.

**Pré-requisitos**

Para permitir a recuperação de desastres para mensagens de entrada, selecione as definições de verificação duplicadas nas Definições de Receção do Acordo X12.

![Selecione definições de verificação duplicadas](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Crie uma [aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) numa região secundária.    

2. Pesse no **X12** e selecione **X12 - Quando um número de controlo for modificado**.   

   ![Pesquisa rumo ao X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   O gatilho pede-lhe que estabeleça uma ligação a uma conta de integração. 
   O gatilho deve ser ligado a uma conta de integração da região primária.

3. Introduza um nome de ligação, selecione a sua *conta de integração da região primária* na lista e escolha **Criar**.   

   ![Screenshot que mostra onde introduzir um nome de ligação e selecione a sua conta de integração da região primária. ](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. A **definição de sincronização do número de controlo da Data** é opcional. A **Frequência** pode ser definida como **Dia,** **Hora,** **Minuto** ou **Segundo** com intervalo.   

   ![Screenshot que mostra o DateTime para iniciar a definição de sincronização do número de controlo.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Selecione **Novo passo** > **Adicionar uma ação**.

   ![Screenshot que mostra o botão novo passo e a opção Adicionar uma opção de ação.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Pesse no **X12** e selecione **X12 - Adicione ou atualize os números de controlo**.   

   ![Adicionar ou atualizar números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Para ligar uma ação a uma conta de integração da região secundária, selecione **Alterar a ligação**  >  **Adicione nova ligação** para uma lista das contas de integração disponíveis. Introduza um nome de ligação, selecione a sua conta de *integração da região secundária* na lista e escolha **Criar**. 

   ![Screenshot que mostra onde adicionar um nome de conta de integração de região secundária.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Mude para entradas brutas clicando no ícone no canto superior direito.

   ![Screenshot que mostra o ícone para selecionar para mudar para cru.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Selecione Body do selecionador de conteúdo dinâmico e guarde a aplicação lógica.

   ![Campos de conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Com base no intervalo de tempo, as sondagens de gatilho da região primária receberam a tabela de números de controlo e puxam os novos registos. 
   A ação atualiza os registos na conta de integração da região secundária. 
   Se não houver atualizações, o estado do gatilho aparece como **Skipped**.   

   ![Tabela de números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base no intervalo de tempo, o estado de execução incremental replica-se de uma região primária para uma região secundária. Durante um evento de catástrofe, quando a região primária não está disponível, o tráfego direto para a região secundária para a continuidade do negócio. 

## <a name="edifact"></a>EDIFACT 

A continuidade do negócio dos documentos EDI EDIFACT baseia-se em números de controlo.

**Pré-requisitos**

Para permitir a recuperação de desastres para mensagens de entrada, selecione as definições de verificação duplicadas nas Definições de Receção do seu acordo EDIFACT.

![Screenshot que mostra as definições de verificação duplicadas nas Definições de Receção do seu acordo EDIFACT.](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Crie uma [aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) numa região secundária.    

2. Pesse no **EDIFACT** e selecione **EDIFACT - Quando um número de controlo é modificado**.

   ![Pesquisa por EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   O gatilho pede-lhe que estabeleça uma ligação a uma conta de integração. 
   O gatilho deve ser ligado a uma conta de integração da região primária. 

3. Introduza um nome de ligação, selecione a sua *conta de integração da região primária* na lista e escolha **Criar**.    

   ![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. A **definição de sincronização do número de controlo da Data** é opcional. A **Frequência** pode ser definida como **Dia,** **Hora,** **Minuto** ou **Segundo** com intervalo.    

   ![Screenshot que mostra as definições de DataTime e Frequência.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Selecione **Novo passo** > **Adicionar uma ação**.    

   ![Screenshot que mostra onde selecionar Adicionar uma ação.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Pesse no **EDIFACT** e selecione **EDIFACT - Adicione ou atualize os números de controlo**.   

   ![Screenshot que mostra onde adicionar ou atualizar números de controlo.](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Para ligar uma ação a uma conta de integração da região secundária, selecione **Alterar a ligação**  >  **Adicione nova ligação** para uma lista das contas de integração disponíveis. Introduza um nome de ligação, selecione a sua conta de *integração da região secundária* na lista e escolha **Criar**.

   ![Criar um nome de conta de integração na região secundária.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Mude para entradas brutas clicando no ícone no canto superior direito.

   ![Screenshot que realça o ícone para selecionar quando pretende mudar para entradas brutas.](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Selecione Body do selecionador de conteúdo dinâmico e guarde a aplicação lógica.   

   ![Screenshot que mostra o selecionador de conteúdo dinâmico de onde pode selecionar o Corpo.](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Com base no intervalo de tempo, as sondagens de gatilho da região primária receberam a tabela de números de controlo e puxam os novos registos.
   A ação atualiza os registos da conta de integração da região secundária. 
   Se não houver atualizações, o estado do gatilho aparece como **Skipped**.

   ![Tabela de números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base no intervalo de tempo, o estado de execução incremental replica-se de uma região primária para uma região secundária. Durante um evento de catástrofe, quando a região primária não está disponível, o tráfego direto para a região secundária para a continuidade do negócio. 

## <a name="as2"></a>AS2 

A continuidade do negócio para documentos que utilizam o protocolo AS2 baseia-se no ID de mensagem e no valor MIC.

> [!TIP]
> Também pode usar o [modelo de arranque rápido AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) para criar aplicações lógicas. A criação de contas de integração primária e secundária são pré-requisitos para usar o modelo. O modelo ajuda a criar uma aplicação lógica que tem um gatilho e uma ação. A aplicação lógica cria uma ligação de um gatilho para uma conta de integração primária e uma ação para uma conta de integração secundária.

1. Criar uma [aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) na região secundária.  

2. Pesquisa no **AS2**, e selecione **AS2 - Quando um valor MIC é criado**.   

   ![Pesquisa rumo ao AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Um gatilho pede-lhe que estabeleça uma ligação a uma conta de integração. 
   O gatilho deve ser ligado a uma conta de integração da região primária. 
   
3. Introduza um nome de ligação, selecione a sua *conta de integração da região primária* na lista e escolha **Criar**.

   ![Screenshot que mostra onde introduzir um nome de ligação quando um valor MIC é criado.](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. A **definição de sincronização de valor MIC** para iniciar é opcional. A **Frequência** pode ser definida como **Dia,** **Hora,** **Minuto** ou **Segundo** com intervalo.   

   ![Data e Frequência](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Selecione **Novo passo** > **Adicionar uma ação**.  

   ![Novo passo, Adicione uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Pesse no **AS2**, e selecione **AS2 - Adicione ou atualize conteúdos MIC**.  

   ![Adição ou atualização de MIC](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Para ligar uma ação a uma conta de integração secundária, selecione **Alterar a**  >  **ligação Adicione nova ligação** para uma lista das contas de integração disponíveis. Introduza um nome de ligação, selecione a sua conta de *integração da região secundária* na lista e escolha **Criar**.

   ![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Mude para entradas brutas clicando no ícone no canto superior direito.

   ![Mudar para entradas brutas](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Selecione Body do selecionador de conteúdo dinâmico e guarde a aplicação lógica.   

   ![Conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Com base no intervalo de tempo, o gatilho sonda a mesa da região primária e puxa os novos registos. A ação atualiza-os para a conta de integração da região secundária. 
   Se não houver atualizações, o estado do gatilho aparece como **Skipped**.  

   ![Tabela da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Com base no intervalo de tempo, o estado de execução incremental replica-se da região primária para a região secundária. Durante um evento de catástrofe, quando a região primária não está disponível, o tráfego direto para a região secundária para a continuidade do negócio. 

## <a name="next-steps"></a>Passos seguintes

[Monitorizar mensagens B2B com os registos do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)
