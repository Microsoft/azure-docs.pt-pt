---
title: Adicionar parceiros comerciais para B2B integrações - Azure Logic Apps | Documentos da Microsoft
description: Crie parceiros comerciais para a sua conta de integração no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.date: 07/08/2016
ms.openlocfilehash: 137ed89c276338b534cad8fdf81ec31b5e5610b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846076"
---
# <a name="add-trading-partners-for-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Adicionar parceiros comerciais para contas de integração no Azure Logic Apps com o Enterprise Integration Pack

Os parceiros são entidades que participam em transações do empresa-empresa (B2B) e trocam mensagens entre si. Antes de poder criar parceiros que representam a e a outra organização nestas transações, tem ambos partilham informações que identificam e valida as mensagens enviadas por si. Depois de discutir esses detalhes e estiver pronto para começar a sua relação comercial, pode criar parceiros na sua conta de integração para representar os dois.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Que funções desempenham parceiros na sua conta de integração?

Para definir os detalhes sobre as mensagens trocadas entre parceiros, vai criar contratos entre os parceiros. No entanto, antes de poder criar um contrato, tem de ter adicionado, pelo menos, dois parceiros à sua conta de integração. Sua organização tem de ser parte do contrato, como o **parceiro do anfitrião**. O parceiro, ou **parceiro convidado** representa a organização que troca mensagens com a sua organização. O parceiro convidado pode ser outra empresa, ou até mesmo um departamento na sua organização.

Depois de adicionar estes parceiros, pode criar um contrato.

Receber e enviar as definições são orientadas do ponto de vista do parceiro alojado de. Por exemplo, as definições de recebimento num contrato de determinam como o parceiro hospedado recebe as mensagens enviadas a partir de um parceiro convidado. Da mesma forma, as definições de envio sobre o contrato indicam como o parceiro alojado envia mensagens para o parceiro convidado.

## <a name="create-partner"></a>Criar o parceiro

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza "integração" e, em seguida, selecione **contas de integração**.

   ![Localizar a conta de integração](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. Sob **contas de integração**, selecione a conta de integração em que pretende adicionar aos seus parceiros.

   ![Selecione a conta de integração](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Escolha o **parceiros** mosaico.

   ![Escolha "Parceiros"](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. Sob **parceiros**, escolha **Add**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Introduza um nome para o seu parceiro, em seguida, selecione um **qualificador**. Introduza um **valor** para identificar documentos que recebem as suas aplicações. Quando tiver terminado, escolha **OK**.

   ![Adicionar os detalhes do parceiro](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Escolha o **parceiros** mosaico novamente.

   ![Escolha o que mosaico "Parceiros"](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Seu novo parceiro aparece agora. 

   ![Novo parceiro de vista](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Editar parceiro

1. Na [portal do Azure](https://portal.azure.com), localize e selecione a sua conta de integração. Escolha o **parceiros** mosaico.

   ![Escolha o que mosaico "Parceiros"](./media/logic-apps-enterprise-integration-partners/edit.png)

2. Sob **parceiros**, selecione o parceiro que pretende editar.

   ![Selecione o parceiro para eliminar](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. Sob **parceiro da atualização**, faça as alterações.
Depois de terminar, escolha **guardar**. 

   ![Faça e guarde as alterações](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Para cancelar as alterações, selecione **descartar**.

## <a name="delete-partner"></a>Eliminar parceiro

1. Na [portal do Azure](https://portal.azure.com), localize e selecione a sua conta de integração. Escolha o **parceiros** mosaico.

   ![Escolha o que mosaico "Parceiros"](./media/logic-apps-enterprise-integration-partners/delete.png)

2. Sob **parceiros**, selecione o parceiro que pretende eliminar.
Escolher **eliminar**.

   ![Eliminar parceiro](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre os contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração do enterprise")  

