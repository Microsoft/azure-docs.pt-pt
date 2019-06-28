---
title: Adicionar parceiros comerciais para B2B integrações - Azure Logic Apps
description: Crie parceiros comerciais na sua conta de integração para utilizar com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330154"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Adicionar parceiros comerciais para contas de integração para o Azure Logic Apps

Na [do Azure Logic Apps](../logic-apps/logic-apps-overview.md), pode criar empresa-empresa (B2B) integração fluxos de trabalho automatizados com um [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) com as aplicações lógicas. Para representar a sua organização e outros, pode criar e adicionar parceiros comerciais como artefatos à sua conta de integração. Os parceiros são entidades que participam em transações B2B e trocam mensagens entre si.

Antes de criar estes parceiros, certifique-se discutir e compartilhar informações com os seus parceiros sobre como identificar e validar as mensagens que o outro envia. Depois de concordar com estes detalhes, está pronto para criar os parceiros na sua conta de integração.

## <a name="partner-roles-in-integration-accounts"></a>Funções do parceiro em contas de integração

Para definir os detalhes sobre as mensagens trocadas com os seus parceiros, crie e adicione [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md) como artefactos da sua conta de integração. Contratos requerem, pelo menos, dois parceiros na sua conta de integração. Sua organização é sempre o *parceiro do anfitrião* no contrato. A organização que troca mensagens com a sua organização é o *parceiro convidado*. Um parceiro convidado pode ser outra empresa, ou até mesmo um departamento na sua organização. Depois de adicionar estes parceiros, pode criar um contrato.

Num contrato, especifique os detalhes de manipulação de mensagens de entrada e saídas da perspectiva do parceiro anfitrião. Para mensagens de entrada, o **receber definições** especificar como o parceiro do anfitrião recebe mensagens do parceiro convidado no contrato. Para mensagens de saída, o **enviar definições** especificar a forma como o parceiro do anfitrião envia mensagens para o parceiro convidado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [Inscreva-se uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar os seus parceiros, contratos e outros artefactos de B2B. Esta conta de integração tem de estar associada a sua subscrição do Azure.

## <a name="create-partner"></a>Criar o parceiro

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza "integração" e selecione **contas de integração**.

   ![Selecione "Contas de integração"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Sob **contas de integração**, selecione a conta de integração em que pretende adicionar aos seus parceiros.

   ![Selecione a conta de integração](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Escolha o **parceiros** mosaico.

   ![Escolha o que mosaico "Parceiros"](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. Sob **parceiros**, escolha **Add**. Sob **Adicionar parceiro**, indique detalhes do parceiro, conforme descrito pela tabela abaixo.

   ![Escolha "Adicionar" e forneça os detalhes do parceiro](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | Nome do parceiro |
   | **Qualifier** | Sim | O corpo de autenticação que fornece as identidades de negócio exclusivos para as organizações, por exemplo, **D-U-N-S (Dun & Bradstreet)** . <p>Parceiros podem optar por uma identidade de negócio mutuamente definido. Nestes cenários, selecione **mutuamente definidos** para EDIFACT ou **mutuamente definido (X12)** para X12. <p>Para RosettaNet, selecione apenas **DUNS**, que é o padrão. |
   | **Valor** | Sim | Um valor que identifica os documentos que recebem as aplicações lógicas. <p>Para RosettaNet, este valor tem de ser um número de nove dígitos que corresponde ao número DUNS. |
   ||||

   > [!NOTE]
   > Para os parceiros que utilizam RosettaNet, pode especificar informações adicionais criando primeiramente estes parceiros e, em seguida [edição dos mesmos posteriormente](#edit-partner).

1. Quando tiver terminado, escolha **OK**.

   Seu novo parceiro agora aparece no **parceiros** lista. Além disso, o **parceiros** mosaico atualiza o número atual de parceiros.

   ![Novo parceiro](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Editar parceiro

1. Na [portal do Azure](https://portal.azure.com), localize e selecione a sua conta de integração.
Escolha o **parceiros** mosaico.

   ![Escolha o que mosaico "Parceiros"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. Sob **parceiros**, selecione o parceiro que pretende editar e escolha **editar**. Sob **editar**, faça as alterações.

   ![Faça e guarde as alterações](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Para RosettaNet, sob **RosettaNet parceiro propriedades**, pode especificar estas informações adicionais:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Classificação de parceiro** | Não | Tipo de organização do parceiro |
   | **Código de cadeia de fornecimento** | Não | Fornecer cadeia código o parceiro, por exemplo, "Tecnologia da informação" ou "Componentes Eletrónicos" |
   | **Nome do contacto** | Não | Nome do contacto do parceiro |
   | **e-mail** | Não | Endereço de e-mail do parceiro |
   | **Fax** | Não | Número de fax do parceiro |
   | **Telefone** | Não | Número de telefone do parceiro |
   ||||

1. Quando tiver terminado, escolha **OK** para guardar as alterações.

## <a name="delete-partner"></a>Eliminar parceiro

1. Na [portal do Azure](https://portal.azure.com), localize e selecione a sua conta de integração. Escolha o **parceiros** mosaico.

   ![Escolha o que mosaico "Parceiros"](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. Sob **parceiros**, selecione o parceiro que pretende eliminar. Escolher **eliminar**.

   ![Eliminar parceiro](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md)