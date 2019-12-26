---
title: Adicionar parceiros comerciais para integrações B2B
description: Criar parceiros comerciais em sua conta de integração para usar com os aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792437"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Adicionar parceiros comerciais a contas de integração para aplicativos lógicos do Azure

Nos [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), você pode criar fluxos de trabalho de integração entre empresas (B2B) automatizados usando uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) com seus aplicativos lógicos. Para representar sua organização e outras, você cria e adiciona parceiros comerciais como artefatos à sua conta de integração. Os parceiros são entidades que participam de transações B2B e trocam mensagens entre si.

Antes de criar esses parceiros, certifique-se de discutir e compartilhar informações com seus parceiros sobre como identificar e validar as mensagens que o outro envia. Depois de concordar com esses detalhes, você estará pronto para criar parceiros em sua conta de integração.

## <a name="partner-roles-in-integration-accounts"></a>Funções de parceiro em contas de integração

Para definir os detalhes sobre as mensagens trocadas com seus parceiros, você cria e adiciona [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md) como artefatos à sua conta de integração. Os contratos exigem pelo menos dois parceiros em sua conta de integração. Sua organização é sempre o *parceiro de host* no contrato. A organização que troca mensagens com sua organização é o *parceiro convidado*. Um parceiro convidado pode ser outra empresa ou até mesmo um departamento em sua organização. Depois de adicionar esses parceiros, você pode criar um contrato.

Em um contrato, você especifica os detalhes para lidar com mensagens de entrada e saída da perspectiva do parceiro host. Para mensagens de entrada, as **configurações de recebimento** especificam como o parceiro host recebe mensagens do parceiro convidado no contrato. Para mensagens de saída, as **configurações de envio** especificam como o parceiro host envia mensagens para o parceiro convidado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você ainda não tiver uma assinatura do Azure, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seus parceiros, contratos e outros artefatos B2B. Esta conta de integração deve ser associada à sua assinatura do Azure.

## <a name="create-partner"></a>Criar parceiro

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, insira "integração" e selecione **contas de integração**.

   ![Selecione "contas de integração"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Em **contas de integração**, selecione a conta de integração à qual você deseja adicionar seus parceiros.

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Escolha o bloco **parceiros** .

   ![Escolher o bloco "parceiros"](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. Em **parceiros**, escolha **Adicionar**. Em **Adicionar parceiro**, forneça os detalhes do parceiro, conforme descrito na tabela a seguir.

   ![Escolha "Adicionar" e forneça detalhes do parceiro](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do parceiro |
   | **Qualificado** | Sim | O corpo de autenticação que fornece identidades de negócios exclusivas para organizações, por exemplo, **D-U-N-S (Dun & Bradstreet)** . <p>Os parceiros podem optar por uma identidade de negócios definida mutuamente. Para esses cenários, selecione **mutuamente definido** para EDIFACT ou **definido mutuamente (X12)** para X12. <p>Para RosettaNet, selecione somente **duns**, que é o padrão. |
   | **Valor** | Sim | Um valor que identifica os documentos que seus aplicativos lógicos recebem. <p>Para RosettaNet, esse valor deve ser um número de nove dígitos que corresponde ao número de DUNS. |
   ||||

   > [!NOTE]
   > Para parceiros que usam o RosettaNet, você pode especificar informações adicionais primeiro criando esses parceiros e [editando-os posteriormente](#edit-partner).

1. Quando terminar, escolha **OK**.

   Seu novo parceiro agora aparece na lista de **parceiros** . Além disso, o bloco **parceiros** atualiza o número atual de parceiros.

   ![Novo parceiro](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Editar parceiro

1. Na [portal do Azure](https://portal.azure.com), localize e selecione sua conta de integração.
Escolha o bloco **parceiros** .

   ![Escolher o bloco "parceiros"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. Em **parceiros**, selecione o parceiro que você deseja editar e escolha **Editar**. Em **Editar**, faça as alterações.

   ![Faça e salve suas alterações](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Para RosettaNet, em **Propriedades do parceiro RosettaNet**, você pode especificar essas informações adicionais:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Classificação do parceiro** | Não | O tipo de organização do parceiro |
   | **Código de cadeia de suprimentos** | Não | O código da cadeia de suprimentos do parceiro, por exemplo, "tecnologia da informação" ou "componentes eletrônicos" |
   | **Nome do contato** | Não | O nome do contato do parceiro |
   | **E-mail** | Não | O endereço de email do parceiro |
   | **Fax** | Não | O número de fax do parceiro |
   | **Ligue** | Não | O número de telefone do parceiro |
   ||||

1. Quando terminar, escolha **OK** para salvar as alterações.

## <a name="delete-partner"></a>Excluir parceiro

1. Na [portal do Azure](https://portal.azure.com), localize e selecione sua conta de integração. Escolha o bloco **parceiros** .

   ![Escolher o bloco "parceiros"](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. Em **parceiros**, selecione o parceiro que você deseja excluir. Escolha **excluir**.

   ![Excluir parceiro](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md)