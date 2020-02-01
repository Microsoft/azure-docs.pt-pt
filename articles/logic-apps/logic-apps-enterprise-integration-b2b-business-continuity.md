---
title: Recuperação de desastre para contas de integração
description: Configurar suas contas de integração e artefatos B2B com a recuperação de desastre entre regiões nos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 09b77862ad3379efeb8b3063a9d6c60b062ca2d7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905137"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Configurar a recuperação de desastre entre regiões para contas de integração em aplicativos lógicos do Azure

As cargas de trabalho B2B envolvem transações de dinheiro como pedidos e faturas. Durante um evento de desastre, é essencial que uma empresa se recupere rapidamente para atender aos SLAs de nível de negócios acordados com seus parceiros. Este artigo demonstra como criar um plano de continuidade de negócios para cargas de trabalho B2B. 

* Preparação para recuperação de desastre 
* Failover para a região secundária durante um evento de desastre 
* Fazer fallback para a região primária após um evento de desastre

## <a name="disaster-recovery-readiness"></a>Preparação para recuperação de desastre  

1. Identifique uma região secundária e crie uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na região secundária.

2. Adicione parceiros, esquemas e contratos para os fluxos de mensagens necessários em que o status de execução precisa ser replicado para a conta de integração de região secundária.

   > [!TIP]
   > Verifique se há consistência na Convenção de nomenclatura do artefato da conta de integração entre regiões. 

3. Para efetuar pull do status de execução da região primária, crie um aplicativo lógico na região secundária. 

   Esse aplicativo lógico deve ter um *gatilho* e uma *ação*. 
   O gatilho deve se conectar à conta de integração da região primária e a ação deve se conectar à conta de integração da região secundária. 
   Com base no intervalo de tempo, o gatilho sonda a tabela de status de execução da região primária e efetua pull dos novos registros, se houver. A ação atualiza-os para a conta de integração da região secundária. 
   Isso ajuda a obter o status de tempo de execução incremental da região primária para a região secundária.

4. A continuidade dos negócios na conta de integração de aplicativos lógicos foi projetada para oferecer suporte com base nos protocolos B2B – X12, AS2 e EDIFACT. Para encontrar etapas detalhadas, selecione os respectivos links.

5. A recomendação é implantar todos os recursos de região primária em uma região secundária também. 

   Os recursos de região primária incluem banco de dados SQL do Azure ou Azure Cosmos DB, barramento de serviço do Azure e hubs de eventos do Azure usados para mensagens, gerenciamento de API do Azure e recurso de aplicativos lógicos do Azure no serviço Azure App.   

6. Estabeleça uma conexão de uma região primária para uma região secundária. Para efetuar pull do status de execução de uma região primária, crie um aplicativo lógico em uma região secundária. 

   O aplicativo lógico deve ter um gatilho e uma ação. 
   O gatilho deve se conectar a uma conta de integração da região primária. 
   A ação deve se conectar a uma conta de integração da região secundária. 
   Com base no intervalo de tempo, o gatilho sonda a tabela de status de execução da região primária e efetua pull dos novos registros, se houver. 
   A ação atualiza-os para uma conta de integração de região secundária. 
   Esse processo ajuda a obter o status de tempo de execução incremental da região primária para a região secundária.

A continuidade dos negócios em uma conta de integração de aplicativos lógicos fornece suporte com base nos protocolos B2B X12, AS2 e EDIFACT. Para obter etapas detalhadas sobre como usar X12 e AS2, consulte [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) e [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) neste artigo.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Fazer failover para uma região secundária durante um evento de desastre

Durante um evento de desastre, quando a região primária não está disponível para continuidade de negócios, direcione o tráfego para a região secundária. Uma região secundária ajuda uma empresa a recuperar funções rapidamente para atender ao RPO/RTO acordado por seus parceiros. Ele também minimiza os esforços de failover de uma região para outra região. 

Há uma latência esperada ao copiar números de controle de uma região primária para uma região secundária. Para evitar o envio de números de controle gerados duplicados para parceiros durante um evento de desastre, a recomendação é incrementar os números de controle nos contratos de região secundária usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Fazer fallback para um evento de pós-desastre na região primária

Para fazer fallback para uma região primária quando ela estiver disponível, siga estas etapas:

1. Pare de aceitar mensagens de parceiros na região secundária.  

2. Incrementar os números de controle gerados para todos os contratos de região primária usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Direcione o tráfego da região secundária para a região primária.

4. Verifique se o aplicativo lógico criado na região secundária para obter o status de execução da região primária está habilitado.

## <a name="x12"></a>X12 

A continuidade dos negócios para documentos EDI X12 é baseada em números de controle:

> [!TIP]
> Você também pode usar o [modelo de início rápido do X12](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) para criar aplicativos lógicos. A criação de contas de integração primária e secundária são pré-requisitos para usar o modelo. O modelo ajuda a criar dois aplicativos lógicos, um para números de controle recebidos e outro para números de controle gerados. Os respectivos gatilhos e ações são criados nos aplicativos lógicos, conectando o gatilho à conta de integração primária e a ação à conta de integração secundária.

**Pré-requisitos**

Para habilitar a recuperação de desastre para mensagens de entrada, selecione as configurações de verificação duplicadas nas configurações de recebimento do contrato X12.

![Selecionar configurações de verificação duplicadas](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Crie um [aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) em uma região secundária.    

2. Pesquise em **X12**e selecione **X12 – quando um número de controle é modificado**.   

   ![Pesquisar por X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   O gatilho solicita que você estabeleça uma conexão com uma conta de integração. 
   O gatilho deve ser conectado a uma conta de integração da região primária.

3. Insira um nome de conexão, selecione sua *conta de integração de região primária* na lista e escolha **criar**.   

   ![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. A configuração **DateTime para iniciar a sincronização do número de controle** é opcional. A **frequência** pode ser definida como **dia**, **hora**, **minuto**ou **segundo** com um intervalo.   

   ![DateTime e Frequency](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Selecione **Novo passo** > **Adicionar uma ação**.

   ![Nova etapa, adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Pesquise em **X12**e selecione **X12-adicionar ou atualizar números de controle**.   

   ![Adicionar ou atualizar números de controle](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Para conectar uma ação a uma conta de integração da região secundária, selecione **alterar conexão** > **Adicionar nova conexão** para obter uma lista das contas de integração disponíveis. Insira um nome de conexão, selecione sua *conta de integração de região secundária* na lista e escolha **criar**. 

   ![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Alterne para entradas brutas clicando no ícone no canto superior direito.

   ![Alternar para entradas brutas](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Selecione corpo no seletor de conteúdo dinâmico e salve o aplicativo lógico.

   ![Campos de conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Com base no intervalo de tempo, o gatilho sonda a tabela de número de controle recebida da região primária e efetua pull dos novos registros. 
   A ação atualiza os registros na conta de integração da região secundária. 
   Se não houver nenhuma atualização, o status do gatilho será exibido como **ignorado**.   

   ![Tabela de números de controle](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base no intervalo de tempo, o status de tempo de execução incremental é replicado de uma região primária para uma região secundária. Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade dos negócios. 

## <a name="edifact"></a>EDIFACT 

A continuidade dos negócios para documentos EDI EDIFACT é baseada em números de controle.

**Pré-requisitos**

Para habilitar a recuperação de desastre para mensagens de entrada, selecione as configurações de verificação duplicadas nas configurações de recebimento do contrato EDIFACT.

![Selecionar configurações de verificação duplicadas](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Crie um [aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) em uma região secundária.    

2. Pesquise em **EDIFACT**e selecione **EDIFACT – quando um número de controle é modificado**.

   ![Pesquisar por EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   O gatilho solicita que você estabeleça uma conexão com uma conta de integração. 
   O gatilho deve ser conectado a uma conta de integração da região primária. 

3. Insira um nome de conexão, selecione sua *conta de integração de região primária* na lista e escolha **criar**.    

   ![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. A configuração **DateTime para iniciar a sincronização do número de controle** é opcional. A **frequência** pode ser definida como **dia**, **hora**, **minuto**ou **segundo** com um intervalo.    

   ![DateTime e Frequency](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Selecione **Novo passo** > **Adicionar uma ação**.    

   ![Nova etapa, adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Pesquise em **EDIFACT**e selecione **EDIFACT-adicionar ou atualizar números de controle**.   

   ![Adicionar ou atualizar números de controle](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Para conectar uma ação a uma conta de integração da região secundária, selecione **alterar conexão** > **Adicionar nova conexão** para obter uma lista das contas de integração disponíveis. Insira um nome de conexão, selecione sua *conta de integração de região secundária* na lista e escolha **criar**.

   ![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Alterne para entradas brutas clicando no ícone no canto superior direito.

   ![Alternar para entradas brutas](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Selecione corpo no seletor de conteúdo dinâmico e salve o aplicativo lógico.   

   ![Campos de conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Com base no intervalo de tempo, o gatilho sonda a tabela de número de controle recebida da região primária e efetua pull dos novos registros.
   A ação atualiza os registros para a conta de integração da região secundária. 
   Se não houver nenhuma atualização, o status do gatilho será exibido como **ignorado**.

   ![Tabela de números de controle](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base no intervalo de tempo, o status de tempo de execução incremental é replicado de uma região primária para uma região secundária. Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade dos negócios. 

## <a name="as2"></a>AS2 

A continuidade dos negócios para documentos que usam o protocolo AS2 é baseada na ID da mensagem e no valor do MIC.

> [!TIP]
> Você também pode usar o [modelo de início rápido do AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) para criar aplicativos lógicos. A criação de contas de integração primária e secundária são pré-requisitos para usar o modelo. O modelo ajuda a criar um aplicativo lógico que tem um gatilho e uma ação. O aplicativo lógico cria uma conexão de um gatilho para uma conta de integração primária e uma ação para uma conta de integração secundária.

1. Crie um [aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) na região secundária.  

2. Pesquise **AS2**e selecione **AS2 – quando um valor de Mic é criado**.   

   ![Pesquisar AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Um gatilho solicita que você estabeleça uma conexão com uma conta de integração. 
   O gatilho deve ser conectado a uma conta de integração da região primária. 
   
3. Insira um nome de conexão, selecione sua *conta de integração de região primária* na lista e escolha **criar**.

   ![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. A configuração **DateTime para iniciar a sincronização de valor de Mic** é opcional. A **frequência** pode ser definida como **dia**, **hora**, **minuto**ou **segundo** com um intervalo.   

   ![DateTime e Frequency](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Selecione **Novo passo** > **Adicionar uma ação**.  

   ![Nova etapa, adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Pesquise no **AS2**e selecione **AS2 – adicionar ou atualizar o conteúdo do MIC**.  

   ![Adição ou atualização de MIC](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Para conectar uma ação a uma conta de integração secundária, selecione **alterar conexão** > **Adicionar nova conexão** para obter uma lista das contas de integração disponíveis. Insira um nome de conexão, selecione sua *conta de integração de região secundária* na lista e escolha **criar**.

   ![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Alterne para entradas brutas clicando no ícone no canto superior direito.

   ![Alternar para entradas brutas](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Selecione corpo no seletor de conteúdo dinâmico e salve o aplicativo lógico.   

   ![Conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Com base no intervalo de tempo, o gatilho sonda a tabela de região primária e efetua pull dos novos registros. A ação atualiza-os para a conta de integração da região secundária. 
   Se não houver nenhuma atualização, o status do gatilho será exibido como **ignorado**.  

   ![Tabela de região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Com base no intervalo de tempo, o status de tempo de execução incremental é replicado da região primária para a região secundária. Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade dos negócios. 

## <a name="next-steps"></a>Passos seguintes

[Monitor de mensagens B2B com registos do Monitor Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)

