---
title: Recuperação após desastre para contas de integração B2B - Azure Logic Apps | Documentos da Microsoft
description: Preparar-se para a recuperação de desastre entre regiões no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/10/2017
ms.openlocfilehash: 8d024e0bc90724892bc53f8895b270716ad0cefc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099438"
---
# <a name="cross-region-disaster-recovery-for-b2b-integration-accounts-in-azure-logic-apps"></a>Recuperação de desastres em várias regiões para contas de integração B2B no Azure Logic Apps

B2B cargas de trabalho envolvem transações de dinheiro, como ordens e notas fiscais. Durante um evento de desastre, é fundamental para uma empresa para rapidamente, recupere para cumprir os SLAs de nível de negócios acordados com seus parceiros. Este artigo demonstra como criar um plano de continuidade de negócio para cargas de trabalho de B2B. 

* Preparação de recuperação após desastre 
* Efetuar a ativação pós-falha para a região secundária durante um evento de desastre 
* Reverter para a região primária após um evento de desastre

## <a name="disaster-recovery-readiness"></a>Preparação de recuperação após desastre  

1. Identificar uma região secundária e criar uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na região secundária.

2. Adicione parceiros, os esquemas e contratos para os fluxos de mensagem necessário onde o estado de execução tem de ser replicadas para a conta de integração da região secundária.

   > [!TIP]
   > Certifique-se de que existe consistência na Convenção de nomenclatura o artefacto de conta de integração em várias regiões. 

3. Para obter o estado de execução da região primária, crie uma aplicação lógica na região secundária. 

   Esta aplicação lógica deve ter uma *acionador* e uma *ação*. 
   O acionador deve ligar à conta de integração da região primária e a ação deve ligar à conta de integração da região secundária. 
   Com base num intervalo de tempo, o acionador consulta a região primária, a tabela de estado de execução e obtém os registos novos, se houver. A ação atualiza-as para a conta de integração da região secundária. 
   Isto ajuda a obter o estado de incremental runtime da região primária para a região secundária.

4. Continuidade do negócio na conta de integração de aplicações lógicas foi concebida para suportar com base em protocolos B2B - X12, AS2 e EDIFACT. Para obter passos detalhados, selecione as respetivas ligações.

5. Recomenda-se implementar todos os recursos de região primária numa região secundária demasiado. 

   Recursos de região primária incluem a base de dados do Azure SQL ou do Azure Cosmos DB, do Azure Service Bus e dos Hubs de eventos do Azure utilizada para mensagens, gestão de API do Azure e a funcionalidade do Azure Logic Apps no App Service do Azure.   

6. Estabelece uma ligação a partir de uma região primária para uma região secundária. Para extrair o estado de execução de uma região primária, crie uma aplicação lógica numa região secundária. 

   A aplicação lógica deve ter um acionador e uma ação. 
   O acionador deve ligar a uma conta de integração de região primária. 
   A ação deve ligar a uma conta de integração de região secundária. 
   Com base num intervalo de tempo, o acionador consulta a região primária, a tabela de estado de execução e obtém os registos novos, se houver. 
   A ação atualiza-las para uma conta de integração da região secundária. 
   Este processo ajuda a obter o estado de incremental runtime da região primária para a região secundária.

Continuidade do negócio numa conta de integração do Logic Apps fornece suporte com base nos protocolos de B2B X12, AS2 e EDIFACT. Para obter passos detalhados sobre como utilizar X12 e AS2, consulte [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) e [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) neste artigo.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Efetuar a ativação pós-falha para uma região secundária durante um evento de desastre

Durante um evento de desastre, quando a região primária não está disponível para continuidade do negócio, direcionar o tráfego para a região secundária. Uma ajuda de região secundária a uma empresa para recuperar as funções rapidamente para satisfazer o RPO/RTO acordados pelos respetivos parceiros. Também minimiza os esforços para efetuar a ativação pós-falha de uma região para outra região. 

Existe uma latência esperada ao copiar os números de controlo da região primária para uma região secundária. Para evitar o envio de números de controlo gerado duplicado para parceiros durante um evento de desastre, a recomendação é de incrementar os números de controle os contratos de região secundária, utilizando [cmdlets do PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Reverter para um evento de pós-desastre da região primária

Para reverter para a região primária quando estiver disponível, siga estes passos:

1. Pare a aceitação das mensagens de parceiros na região secundária.  

2. Incrementar os números de controlo gerado para todos os contratos a região primária, utilizando [cmdlets do PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Direcionar o tráfego da região secundária para a região primária.

4. Verifique que a aplicação lógica criada na região secundária para extrair o estado de execução da região primária está ativada.

## <a name="x12"></a>X12 

Continuidade do negócio para EDI X 12 documentos se baseia em números de controlo:

> [!TIP]
> Também pode utilizar o [X12 rápido iniciar modelo](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) para criar aplicações lógicas. Criação de contas de integração principais e secundários é pré-requisitos para utilizar o modelo. O modelo ajuda a criar aplicações lógicas dois, uma para números de controlo recebido e outro para números de controlo gerado. Cada acionador e ações são criadas no logic apps, o acionador a ligar a conta de integração de principal e a ação para a conta de integração secundário.

**Pré-requisitos**

Para ativar a recuperação após desastre para as mensagens de entrada, selecione as definições de verificação duplicada no X12 definições de receção do contrato.

![Selecione as definições de verificação duplicado](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Criar uma [aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) numa região secundária.    

2. Pesquisar nos **X12**e selecione **X12-quando um número de controlo é modificado**.   

   ![Pesquisa de X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   O acionador pede-lhe estabelecer uma ligação para uma conta de integração. 
   O acionador deve estar ligado a uma conta de integração da região primária.

3. Introduza um nome de ligação, selecione seu *conta de integração da região primária* na lista e escolha **criar**.   

   ![Nome de conta de integração de região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. O **DateTime para iniciar a sincronização do número de controlo** definição é opcional. O **frequência** pode ser definido como **dia**, **hora**, **minuto**, ou **segundo** com um intervalo.   

   ![DateTime e a frequência](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Selecione **novo passo** > **adicionar uma ação**.

   ![Novo passo, adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Pesquisar nos **X12**e selecione **X12-adicionar ou atualizar números de controlo**.   

   ![Adicionar ou atualizar números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Para ligar uma ação para uma conta de integração da região secundária, selecione **Alterar ligação** > **Adicionar nova ligação** para obter uma lista das contas de integração disponível. Introduza um nome de ligação, selecione seu *conta de integração da região secundária* na lista e escolha **criar**. 

   ![Nome de conta de integração de região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Mudar para entradas não processadas ao clicar no ícone no canto superior direito.

   ![Mudar para entradas não processadas](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Selecionar o corpo do Seletor de conteúdo dinâmico e guarde a aplicação lógica.

   ![Campos de conteúdo dinâmicos](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Com base num intervalo de tempo, o acionador consulta a tabela de número de controlo de região primária, recebido e obtém os registos de novo. 
   A ação atualiza os registos na conta de integração de região secundária. 
   Se não existirem não existem atualizações, o estado de Acionador aparece como **ignorados**.   

   ![Tabela de número de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base num intervalo de tempo, o estado de incremental runtime replica a partir de uma região primária para uma região secundária. Durante um evento de desastre, quando a região primária não está disponível, suporte direto tráfego para a região secundária para a continuidade do negócio. 

## <a name="edifact"></a>EDIFACT 

Continuidade do negócio para documentos EDIFACT de EDI baseia-se em números de controlo.

**Pré-requisitos**

Para ativar a recuperação após desastre para as mensagens de entrada, selecione as definições de verificação duplicado nas definições de receção do seu contrato de EDIFACT.

![Selecione as definições de verificação duplicado](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Criar uma [aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) numa região secundária.    

2. Pesquisar nos **EDIFACT**e selecione **EDIFACT - quando um número de controlo é modificado**.

   ![Pesquisa de EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   O acionador pede-lhe estabelecer uma ligação para uma conta de integração. 
   O acionador deve estar ligado a uma conta de integração da região primária. 

3. Introduza um nome de ligação, selecione seu *conta de integração da região primária* na lista e escolha **criar**.    

   ![Nome de conta de integração de região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. O **DateTime para iniciar a sincronização do número de controlo** definição é opcional. O **frequência** pode ser definido como **dia**, **hora**, **minuto**, ou **segundo** com um intervalo.    

   ![DateTime e a frequência](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Selecione **novo passo** > **adicionar uma ação**.    

   ![Novo passo, adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Pesquisar nos **EDIFACT**e selecione **EDIFACT - adicionar ou atualizar números de controlo**.   

   ![Adicionar ou atualizar números de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Para ligar uma ação para uma conta de integração da região secundária, selecione **Alterar ligação** > **Adicionar nova ligação** para obter uma lista das contas de integração disponível. Introduza um nome de ligação, selecione seu *conta de integração da região secundária* na lista e escolha **criar**.

   ![Nome de conta de integração de região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Mudar para entradas não processadas ao clicar no ícone no canto superior direito.

   ![Mudar para entradas não processadas](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Selecionar o corpo do Seletor de conteúdo dinâmico e guarde a aplicação lógica.   

   ![Campos de conteúdo dinâmicos](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Com base num intervalo de tempo, o acionador consulta a tabela de número de controlo de região primária, recebido e obtém os registos de novo.
   A ação atualiza os registos para a conta de integração da região secundária. 
   Se não existirem não existem atualizações, o estado de Acionador aparece como **ignorados**.

   ![Tabela de número de controlo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base num intervalo de tempo, o estado de incremental runtime replica a partir de uma região primária para uma região secundária. Durante um evento de desastre, quando a região primária não está disponível, suporte direto tráfego para a região secundária para a continuidade do negócio. 

## <a name="as2"></a>AS2 

Continuidade do negócio para documentos que utilizam o protocolo AS2 baseia-se o ID da mensagem e o valor MIC.

> [!TIP]
> Também pode utilizar o [modelo de início rápido de AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) para criar aplicações lógicas. Criação de contas de integração principais e secundários é pré-requisitos para utilizar o modelo. O modelo ajuda a criar uma aplicação lógica que tem um acionador e uma ação. A aplicação lógica cria uma ligação a partir de um acionador para uma conta de integração de principal e uma ação para uma conta de integração secundário.

1. Criar uma [aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) na região secundária.  

2. Pesquisar nos **AS2**e selecione **AS2 - valor de MIC um quando é criado**.   

   ![Pesquisa de AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Um acionador pede-lhe estabelecer uma ligação para uma conta de integração. 
   O acionador deve estar ligado a uma conta de integração da região primária. 
   
3. Introduza um nome de ligação, selecione seu *conta de integração da região primária* na lista e escolha **criar**.

   ![Nome de conta de integração de região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. O **DateTime para iniciar a sincronização de valor MIC** definição é opcional. O **frequência** pode ser definido como **dia**, **hora**, **minuto**, ou **segundo** com um intervalo.   

   ![DateTime e a frequência](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Selecione **novo passo** > **adicionar uma ação**.  

   ![Novo passo, adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Pesquisar nos **AS2**e selecione **AS2 - adicionar ou atualizar conteúdos MIC**.  

   ![Adição de MIC ou de atualização](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Para ligar uma ação para uma conta de integração secundário, selecione **Alterar ligação** > **Adicionar nova ligação** para obter uma lista das contas de integração disponível. Introduza um nome de ligação, selecione seu *conta de integração da região secundária* na lista e escolha **criar**.

   ![Nome de conta de integração de região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Mudar para entradas não processadas ao clicar no ícone no canto superior direito.

   ![Mudar para entradas não processadas](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Selecionar o corpo do Seletor de conteúdo dinâmico e guarde a aplicação lógica.   

   ![Conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Com base num intervalo de tempo, o acionador consulta a tabela da região primária e obtém os registos de novo. A ação atualiza-as para a conta de integração da região secundária. 
   Se não existirem não existem atualizações, o estado de Acionador aparece como **ignorados**.  

   ![Tabela da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Com base num intervalo de tempo, o estado de incremental runtime replica da região primária para a região secundária. Durante um evento de desastre, quando a região primária não está disponível, suporte direto tráfego para a região secundária para a continuidade do negócio. 

## <a name="next-steps"></a>Passos Seguintes

[Monitorizar mensagens B2B](logic-apps-monitor-b2b-message.md)

