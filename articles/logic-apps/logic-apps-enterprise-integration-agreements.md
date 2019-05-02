---
title: Criar e gerir contratos de parceiros comerciais - Azure Logic Apps
description: Criar e gerir os contratos entre parceiros comerciais com o Azure Logic Apps e o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.date: 04/05/2019
ms.openlocfilehash: 26d653b873e959f0804e0456ed87ee68c39413e5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720683"
---
# <a name="create-and-manage-trading-partner-agreements-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Criar e gerir contratos de parceiros comerciais com o Azure Logic Apps e o Enterprise Integration Pack

R [parceiro comercial](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*contrato* ajuda as organizações e empresas comunicar de forma totalmente integrada entre si, definindo o protocolo de norma da indústria específico a utilizar ao trocar mensagens empresa-empresa (B2B). Os contratos de apresentam benefícios comuns, por exemplo:

* Permitem às organizações trocar informações com um formato bem conhecido.
* Melhore a eficiência ao conduzir transações B2B.
* São fáceis de criar, gerir e utilizar para criar soluções de integração de enterprise.

Este artigo mostra como criar um AS2, EDIFACT e X12 contrato que pode usar ao criar soluções de integração para cenários B2B de enterprise utilizando as [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) e [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Depois de criar um contrato, em seguida, pode utilizar o AS2, EDIFACT e X12 conectores para a troca de mensagens B2B.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [Inscreva-se uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seu contrato e outros artefactos de B2B. Esta conta de integração tem de estar associada a sua subscrição do Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que já tenha criado na sua conta de integração. Um contrato requer um parceiro do anfitrião e um parceiro convidado. Ambos os parceiros têm de utilizar o mesmo qualificador de "identidade de negócio" como o contrato que pretende criar, por exemplo, AS2, X 12 ou EDIFACT.

* Opcional: A aplicação lógica em que pretende utilizar o seu contrato e um acionador que inicia o fluxo de trabalho da sua aplicação lógica. Apenas criar a sua conta de integração e artefactos de B2B, não terá uma aplicação lógica. No entanto, antes da aplicação lógica pode utilizar os artefactos de B2B na sua conta de integração, deve vincular sua conta de integração à sua aplicação lógica. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Criar contratos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza "integração" como o filtro. Nos resultados, selecione este recurso: **Contas de integração**

   ![Localize a conta de integração](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Sob **contas de integração**, selecione a conta de integração em que pretende criar o contrato.

   ![Selecione onde pretende criar o contrato de conta de integração](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. No painel da direita, em **componentes**, escolha a **contratos** mosaico.

   ![Escolha "Contratos"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Sob **contratos**, escolha **Add**. Na **adicionar** painel, fornecem informações sobre o seu contrato, por exemplo:

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*agreement-name*> | O nome do contrato por |
   | **Tipo de contrato** | Sim | **AS2**, **X12**, ou **EDIFACT** | O tipo de protocolo para o seu contrato. Quando cria o ficheiro de contrato, o conteúdo desse arquivo tem de corresponder ao tipo de contrato. | |  
   | **Parceiro do anfitrião** | Sim | <*host-partner-name*> | O parceiro do anfitrião representa a organização que especifica o contrato |
   | **Host Identity** | Sim | <*host-partner-identifier*> | Identificador do parceiro anfitrião |
   | **Parceiro convidado** | Sim | <*guest-partner-name*> | O parceiro convidado representa a organização que está negociando com o parceiro do anfitrião |
   | **Identidade do convidado** | Sim | <*guest-partner-identifier*> | Identificador do parceiro convidado |
   | **Receber definições** | Varia | Varia | Estas propriedades especificam como lidar com todas as mensagens de entrada recebidas pelo contrato. Para obter mais informações, consulte o tipo do respetivo contrato: <p>- [Definições de mensagem AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Definições de mensagem EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Definições da mensagem X12](logic-apps-enterprise-integration-x12.md) |
   | **Definições de envio** | Varia | Varia | Estas propriedades especificam como lidar com todas as mensagens de saída enviadas pelo contrato. Para obter mais informações, consulte o tipo do respetivo contrato: <p>- [Definições de mensagem AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Definições de mensagem EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Definições da mensagem X12](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Quando terminar a criação de seu contrato, no **Add** página, selecione **OK**e retornar à sua conta de integração.

   O **contratos** lista mostra agora o seu novo contrato.

## <a name="edit-agreements"></a>Editar contratos

1. Na [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **todos os serviços**.

1. Na caixa de pesquisa, introduza "integração" como o filtro. Nos resultados, selecione este recurso: **Contas de integração**

1. Sob **contas de integração**, selecione a conta de integração com o contrato que pretende editar.

1. No painel da direita, em **componentes**, escolha a **contratos** mosaico.

1. Sob **contratos**, selecione o seu contrato e escolha **editar**.

1. Fazer e, em seguida, guarde as alterações.

## <a name="delete-agreements"></a>Eliminar contratos

1. Na [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **todos os serviços**.

1. Na caixa de pesquisa, introduza "integração" como o filtro. Nos resultados, selecione este recurso: **Contas de integração**

1. Sob **contas de integração**, selecione a conta de integração com o contrato que pretende eliminar.

1. No painel da direita, em **componentes**, escolha a **contratos** mosaico.

1. Sob **contratos**, selecione o seu contrato e escolha **eliminar**.

1. Confirme que pretende eliminar o contrato selecionado.

## <a name="next-steps"></a>Passos Seguintes

* [Trocar mensagens AS2](logic-apps-enterprise-integration-as2.md)
* [Trocar mensagens EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Trocar mensagens X12](logic-apps-enterprise-integration-x12.md)
