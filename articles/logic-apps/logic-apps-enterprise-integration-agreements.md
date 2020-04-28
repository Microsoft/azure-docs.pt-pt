---
title: Acordos de parceiros comerciais
description: Criar e gerir acordos entre parceiros comerciais utilizando aplicações da Azure Logic e pacote de integração empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74790732"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Criar e gerir acordos de parceiros comerciais em Aplicações Lógicas Azure

Um*acordo* [de parceiro](../logic-apps/logic-apps-enterprise-integration-partners.md) 
comercial ajuda organizações e empresas a comunicarem-se perfeitamente, definindo o protocolo específico padrão da indústria para utilizar na troca de mensagens business-to-business (B2B). Os acordos proporcionam benefícios comuns, por exemplo:

* Permitir que as organizações troquem informações utilizando um formato bem conhecido.
* Melhorar a eficiência na realização de transações B2B.
* São fáceis de criar, gerir e usar para construir soluções de integração empresarial.

Este artigo mostra como criar um acordo AS2, EDIFACT ou X12 que você pode usar na construção de soluções de integração empresarial para cenários B2B usando o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) e [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Depois de criar um acordo, pode então utilizar os conectores AS2, EDIFACT ou X12 para a troca de mensagens B2B.

Para criar acordos para a troca de mensagens RosettaNet, consulte [as mensagens Exchange RosettaNet](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma assinatura Azure, [inscreva-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar o seu acordo e outros artefactos B2B. Esta conta de integração deve ser associada à sua subscrição Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que já criou na sua conta de integração. Um acordo requer tanto um parceiro anfitrião como um parceiro convidado. Ambos os parceiros devem usar a mesma qualificação de "identidade de negócio" que o acordo que pretende criar, como as AS2, X12 ou EDIFACT.

* Opcional: A aplicação lógica onde pretende utilizar o seu acordo e um gatilho que inicia o fluxo de trabalho da sua aplicação lógica. Para criar asua conta de integração e artefactos B2B, não precisa de uma aplicação lógica. No entanto, antes que a sua aplicação lógica possa utilizar os artefactos B2B na sua conta de integração, deve ligar a sua conta de integração à sua aplicação lógica. Se você é novo em aplicações lógicas, reveja [o que são As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Criar acordos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
No menu Azure principal, selecione **Todos os serviços.** Na caixa de pesquisa, introduza "integração" como filtro. A partir dos resultados, selecione este recurso: **Contas de integração**

   ![Encontre a sua conta de integração](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. No âmbito **das contas integrais,** selecione a conta de integração onde pretende criar o acordo.

   ![Selecione a conta de integração onde criar o acordo](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. No painel da direita, nos **componentes,** escolha o azulejo dos **Acordos.**

   ![Escolha "Acordos"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Nos **termos de Acordos,** escolha **Adicionar**. No painel **Adicionar,** forneça informações sobre o seu acordo, por exemplo:

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*nome do acordo*> | O nome do seu acordo |
   | **Tipo de acordo** | Sim | **AS2,** **X12**ou **EDIFACT** | O tipo de protocolo para o seu acordo. Quando criar o seu ficheiro de acordo, o conteúdo nesse ficheiro deve corresponder ao tipo de acordo. | |  
   | **Parceiro anfitrião** | Sim | <*nome de parceiro anfitrião*> | O parceiro anfitrião representa a organização que especifica o acordo |
   | **Identidade de acolhimento** | Sim | <*parceiro-parceiro-identificador*> | O identificador do parceiro anfitrião |
   | **Parceiro Convidado** | Sim | <*nome de parceiro convidado*> | O parceiro convidado representa a organização que está a fazer negócios com o parceiro anfitrião |
   | **Identidade de Hóspede** | Sim | <*parceiro-parceiro-identificador*> | O identificador do parceiro convidado |
   | **Receber Definições** | Varia | Varia | Estas propriedades especificam como o parceiro anfitrião recebe todas as mensagens do parceiro convidado no acordo. Para mais informações, consulte o respetivo tipo de acordo: <p>- [Definições de mensagem AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Definições de mensagem EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Definições de mensagem X12](logic-apps-enterprise-integration-x12.md) |
   | **Enviar Definições** | Varia | Varia | Estas propriedades especificam como o parceiro anfitrião envia todas as mensagens de saída para o parceiro convidado no acordo. Para mais informações, consulte o respetivo tipo de acordo: <p>- [Definições de mensagem AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Definições de mensagem EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Definições de mensagem X12](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Quando terminar de criar o seu acordo, na página **Adicionar,** escolha **OK**, e volte à sua conta de integração.

   A lista de **Acordos** mostra agora o seu novo acordo.

## <a name="edit-agreements"></a>Editar acordos

1. No [portal Azure,](https://portal.azure.com)no menu Principal Azure, selecione **Todos os serviços.**

1. Na caixa de pesquisa, introduza "integração" como filtro. A partir dos resultados, selecione este recurso: **Contas de integração**

1. No âmbito **das contas integrais,** selecione a conta de integração que tem o acordo que pretende editar.

1. No painel da direita, nos **componentes,** escolha o azulejo dos **Acordos.**

1. Nos **Termos de Acordos,** selecione o seu acordo e escolha **Editar**.

1. Faça e, em seguida, guarde as suas alterações.

## <a name="delete-agreements"></a>Eliminar acordos

1. No [portal Azure,](https://portal.azure.com)no menu Principal Azure, selecione **Todos os serviços.**

1. Na caixa de pesquisa, introduza "integração" como filtro. A partir dos resultados, selecione este recurso: **Contas de integração**

1. No âmbito **das contas integrais,** selecione a conta de integração que tem o acordo que pretende eliminar.

1. No painel da direita, nos **componentes,** escolha o azulejo dos **Acordos.**

1. Nos **Termos dos Acordos,** selecione o seu acordo e escolha **Eliminar**.

1. Confirme que pretende eliminar o acordo selecionado.

## <a name="next-steps"></a>Passos seguintes

* [Trocar mensagens AS2](logic-apps-enterprise-integration-as2.md)
* [Trocar mensagens EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Trocar mensagens X12](logic-apps-enterprise-integration-x12.md)
