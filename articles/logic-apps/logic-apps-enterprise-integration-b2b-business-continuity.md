---
title: Recuperação de desastres para contas de integração
description: Configurar as suas contas de integração e artefactos B2B com recuperação de desastres transversais em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 09b77862ad3379efeb8b3063a9d6c60b062ca2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905137"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Criação de recuperação de desastres transversais para contas de integração em Aplicações Lógicas Azure

As cargas de trabalho B2B envolvem transações de dinheiro, como encomendas e faturas. Durante um evento de desastre, é fundamental que um negócio recupere rapidamente para cumprir as SLAs a nível de negócios acordadas com os seus parceiros. Este artigo demonstra como construir um plano de continuidade de negóciopara cargas de trabalho B2B. 

* Prontidão de recuperação de desastres 
* Falhar na região secundária durante um evento de desastre 
* Recua para a região primária após um evento de desastre

## <a name="disaster-recovery-readiness"></a>Prontidão de recuperação de desastres  

1. Identificar uma região secundária e criar uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na região secundária.

2. Adicione parceiros, schemas e acordos para os fluxos de mensagens necessários onde o estado de execução precisa de ser replicado para a conta de integração da região secundária.

   > [!TIP]
   > Certifique-se de que há consistência na convenção de nomeação do artefacto de integração entre as regiões. 

3. Para retirar o estatuto de execução da região primária, crie uma aplicação lógica na região secundária. 

   Esta aplicação lógica deve ter um *gatilho* e uma *ação.* 
   O gatilho deve ligar-se à conta de integração da região primária, e a ação deve ligar-se à conta de integração da região secundária. 
   Com base no intervalo de tempo, as sondagens de gatilho da região primária gerem a tabela de status e puxam os novos registos, se houver. A ação atualiza-os à conta de integração da região secundária. 
   Isto ajuda a obter o estatuto de tempo de execução incremental da região primária para a região secundária.

4. A continuidade do negócio na conta de integração de Apps Lógicas foi concebida para suportar com base em protocolos B2B - X12, AS2 e EDIFACT. Para encontrar passos detalhados, selecione os respetivos links.

5. A recomendação é a de utilizar também todos os recursos da região primária numa região secundária. 

   Os recursos da região primária incluem a Base de Dados Azure SQL ou a Azure Cosmos DB, o Azure Service Bus e os Hubs de Eventos Azure utilizados para mensagens, a Azure API Management e a funcionalidade Azure Logic Apps no Azure App Service.   

6. Estabelecer uma ligação de uma região primária a uma região secundária. Para retirar o estatuto de execução de uma região primária, crie uma aplicação lógica numa região secundária. 

   A aplicação lógica deve ter um gatilho e uma ação. 
   O gatilho deve ligar-se a uma conta de integração da região primária. 
   A ação deve ligar-se a uma conta de integração da região secundária. 
   Com base no intervalo de tempo, as sondagens de gatilho da região primária gerem a tabela de status e puxam os novos registos, se houver. 
   A ação atualiza-os a uma conta de integração da região secundária. 
   Este processo ajuda a obter o estatuto de tempo de execução incremental da região primária para a região secundária.

A continuidade do negócio numa conta de integração de Apps Lógicas fornece suporte com base nos protocolos B2B X12, AS2 e EDIFACT. Para obter passos detalhados sobre a utilização de X12 e AS2, consulte [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) e [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) neste artigo.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Falhar numa região secundária durante um evento de desastre

Durante um evento de catástrofe, quando a região primária não está disponível para continuidade empresarial, o tráfego direto para a região secundária. Uma região secundária ajuda uma empresa a recuperar rapidamente funções para satisfazer o RPO/RTO acordado pelos seus parceiros. Também minimiza os esforços para falhar de uma região para outra região. 

Espera-se uma latência enquanto copia os números de controlo de uma região primária para uma região secundária. Para evitar o envio de duplicados números de controlo gerados aos parceiros durante um evento de catástrofe, a recomendação é aumentar os números de controlo nos acordos da região secundária utilizando [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Recua para um evento pós-desastre na região primária

Para recuar para uma região primária quando estiver disponível, siga estes passos:

1. Pare de aceitar mensagens de parceiros da região secundária.  

2. Incremente os números de controlo gerados para todos os acordos da região primária utilizando [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Tráfego direto da região secundária para a região primária.

4. Verifique se a aplicação lógica criada na região secundária para retirar o estado de execução da região primária está ativada.

## <a name="x12"></a>X12 

A continuidade do negócio dos documentos EDI X12 baseia-se em números de controlo:

> [!TIP]
> Também pode usar o modelo de [arranque rápido X12](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) para criar aplicações lógicas. A criação de contas de integração primária e secundária são pré-requisitos para usar o modelo. O modelo ajuda a criar duas aplicações lógicas, uma para números de controlo recebidos e outra para números de controlo gerados. Os respetivos gatilhos e ações são criados nas aplicações lógicas, ligando o gatilho à conta de integração primária e a ação à conta de integração secundária.

**Pré-requisitos**

Para permitir a recuperação de desastres para mensagens de entrada, selecione as definições de verificação duplicadas nas Definições de Receção do acordo X12.

![Selecione configurações de verificação duplicada](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Crie uma [aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) numa região secundária.    

2. Procure em **X12**, e selecione **X12 - Quando um número de controlo for modificado**.   

   ![Pesquisa por X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   O gatilho leva-o a estabelecer uma ligação a uma conta de integração. 
   O gatilho deve ser ligado a uma conta de integração da região primária.

3. Introduza um nome de ligação, selecione a sua conta de integração da *região primária* na lista e escolha **Criar**.   

   ![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. A definição de sincronização de números de **controlo DateTime para iniciar** o controlo é opcional. A **Frequência** pode ser definida para **dia,** **hora,** **minuto**ou **segundo** com intervalo.   

   ![Datatime e Frequência](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Selecione **novo passo** > **Adicione uma ação**.

   ![Novo passo, Adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Procure em **X12**e selecione **X12 - Adicione ou atualize os números**de controlo .   

   ![Adicionar ou atualizar números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Para ligar uma ação a uma conta de integração da região secundária, selecione **Ligação** > Change**Adicionar nova ligação** para uma lista das contas de integração disponíveis. Introduza um nome de ligação, selecione a sua conta de integração da *região secundária* na lista e escolha **Criar**. 

   ![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Mude para entradas cruas clicando no ícone no canto superior direito.

   ![Mude para inputs crus](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Selecione Body a partir do apanhador de conteúdo dinâmico e guarde a aplicação lógica.

   ![Campos de conteúdo dinâmicos](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Com base no intervalo de tempo, as sondagens de gatilho da região primária receberam a tabela de números de controlo e puxam os novos registos. 
   A ação atualiza os registos na conta de integração da região secundária. 
   Se não houver atualizações, o estado do gatilho aparece como **Skipped**.   

   ![Tabela de números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base no intervalo de tempo, o estado de execução incremental replica-se de uma região primária para uma região secundária. Durante um evento de catástrofe, quando a região primária não está disponível, o tráfego direto para a região secundária para a continuidade das empresas. 

## <a name="edifact"></a>EDIFACT 

A continuidade do negócio dos documentos EDI EDIFACT baseia-se em números de controlo.

**Pré-requisitos**

Para permitir a recuperação de desastres para mensagens de entrada, selecione as definições de verificação duplicadas nas Definições de Receção do seu contrato EDIFACT.

![Selecione configurações de verificação duplicada](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Crie uma [aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) numa região secundária.    

2. Procure no **EDIFACT**, e selecione **EDIFACT - Quando um número de controlo for modificado**.

   ![Pesquisa por EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   O gatilho leva-o a estabelecer uma ligação a uma conta de integração. 
   O gatilho deve ser ligado a uma conta de integração da região primária. 

3. Introduza um nome de ligação, selecione a sua conta de integração da *região primária* na lista e escolha **Criar**.    

   ![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. A definição de sincronização de números de **controlo DateTime para iniciar** o controlo é opcional. A **Frequência** pode ser definida para **dia,** **hora,** **minuto**ou **segundo** com intervalo.    

   ![Datatime e Frequência](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Selecione **novo passo** > **Adicione uma ação**.    

   ![Novo passo, Adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Procure no **EDIFACT**, e selecione **EDIFACT - Adicione ou atualize os números**de controlo .   

   ![Adicionar ou atualizar números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Para ligar uma ação a uma conta de integração da região secundária, selecione **Ligação** > Change**Adicionar nova ligação** para uma lista das contas de integração disponíveis. Introduza um nome de ligação, selecione a sua conta de integração da *região secundária* na lista e escolha **Criar**.

   ![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Mude para entradas cruas clicando no ícone no canto superior direito.

   ![Mude para inputs crus](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Selecione Body a partir do apanhador de conteúdo dinâmico e guarde a aplicação lógica.   

   ![Campos de conteúdo dinâmicos](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Com base no intervalo de tempo, as sondagens de gatilho da região primária receberam a tabela de números de controlo e puxam os novos registos.
   A ação atualiza os registos da conta de integração da região secundária. 
   Se não houver atualizações, o estado do gatilho aparece como **Skipped**.

   ![Tabela de números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base no intervalo de tempo, o estado de execução incremental replica-se de uma região primária para uma região secundária. Durante um evento de catástrofe, quando a região primária não está disponível, o tráfego direto para a região secundária para a continuidade das empresas. 

## <a name="as2"></a>AS2 

A continuidade do negócio para documentos que utilizam o protocolo AS2 baseia-se no ID da mensagem e no valor MIC.

> [!TIP]
> Também pode usar o modelo de [arranque rápido AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) para criar aplicações lógicas. A criação de contas de integração primária e secundária são pré-requisitos para usar o modelo. O modelo ajuda a criar uma aplicação lógica que tem um gatilho e uma ação. A aplicação lógica cria uma ligação de um gatilho para uma conta de integração primária e uma ação para uma conta de integração secundária.

1. Crie uma [aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) na região secundária.  

2. Procure no **AS2**, e selecione **AS2 - Quando for criado um valor MIC**.   

   ![Pesquisa por AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Um gatilho leva-o a estabelecer uma ligação a uma conta de integração. 
   O gatilho deve ser ligado a uma conta de integração da região primária. 
   
3. Introduza um nome de ligação, selecione a sua conta de integração da *região primária* na lista e escolha **Criar**.

   ![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. O **DataTime para iniciar a** definição de sincronização de valor MIC é opcional. A **Frequência** pode ser definida para **dia,** **hora,** **minuto**ou **segundo** com intervalo.   

   ![Datatime e Frequência](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Selecione **novo passo** > **Adicione uma ação**.  

   ![Novo passo, Adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Procure no **AS2**, e selecione **AS2 - Adicione ou atualize conteúdos MIC**.  

   ![Adição ou atualização mic](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Para ligar uma ação a uma conta de integração secundária, selecione **Ligação** > Change**Adicionar nova ligação** para uma lista das contas de integração disponíveis. Introduza um nome de ligação, selecione a sua conta de integração da *região secundária* na lista e escolha **Criar**.

   ![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Mude para entradas cruas clicando no ícone no canto superior direito.

   ![Mude para inputs crus](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Selecione Body a partir do apanhador de conteúdo dinâmico e guarde a aplicação lógica.   

   ![Conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Com base no intervalo de tempo, o gatilho dispara a mesa da região primária e puxa os novos recordes. A ação atualiza-os à conta de integração da região secundária. 
   Se não houver atualizações, o estado do gatilho aparece como **Skipped**.  

   ![Mesa da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Com base no intervalo de tempo, o estado de execução incremental replica-se da região primária para a região secundária. Durante um evento de catástrofe, quando a região primária não está disponível, o tráfego direto para a região secundária para a continuidade das empresas. 

## <a name="next-steps"></a>Passos seguintes

[Monitorizar mensagens B2B com os registos do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)

