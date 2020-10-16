---
title: Adicionar parceiros de negociação para integrações B2B
description: Crie parceiros de negociação na sua conta de integração para utilizar com aplicações Azure Logic
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 8e3805fae5bf6cc5ad8cf759d3ba75220c6ddbd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565076"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Adicionar parceiros comerciais às contas de integração do Azure Logic Apps

Nas [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)pode criar fluxos de trabalho automatizados de integração entre negócios (B2B) utilizando uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) com as suas aplicações lógicas. Para representar a sua organização e outros, cria e adiciona parceiros comerciais como artefactos à sua conta de integração. Os parceiros são entidades que participam em transações B2B e trocam mensagens entre si.

Antes de criar estes parceiros, certifique-se de discutir e partilhar informações com os seus parceiros sobre como identificar e validar as mensagens que o outro envia. Depois de concordar com estes detalhes, está pronto para criar parceiros na sua conta de integração.

## <a name="partner-roles-in-integration-accounts"></a>Funções de parceiros em contas de integração

Para definir os detalhes sobre as mensagens trocadas com os seus parceiros, cria e adiciona [acordos](../logic-apps/logic-apps-enterprise-integration-agreements.md) como artefactos à sua conta de integração. Os acordos requerem pelo menos dois parceiros na sua conta de integração. A sua organização é sempre o *parceiro anfitrião* no acordo. A organização que troca mensagens com a sua organização é o *parceiro convidado.* Um parceiro convidado pode ser outra empresa, ou mesmo um departamento na sua própria organização. Depois de adicionar estes parceiros, pode criar um acordo.

Num acordo, especifica os detalhes para lidar com mensagens recebidas e saídas do ponto de vista do parceiro anfitrião. Para mensagens **recebidas, as Definições de Receção** especificam como o parceiro anfitrião recebe mensagens do parceiro convidado no acordo. Para mensagens de saída, as **Definições de Envio** especificam como o parceiro anfitrião envia mensagens para o parceiro convidado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, [inscreva-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar os seus parceiros, acordos e outros artefactos B2B. Esta conta de integração deve estar associada à sua subscrição Azure.

## <a name="create-partner"></a>Criar parceiro

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu Azure principal, selecione **Todos os serviços**. Na caixa de pesquisa, introduza "integração", e selecione **contas de Integração**.

   ![Selecione "Contas de integração"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Em **Contas de Integração,** selecione a conta de integração onde pretende adicionar os seus parceiros.

   ![Selecione conta de integração](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Escolha o azulejo dos **Parceiros.**

   ![Screenshot que mostra o azulejo dos Parceiros.](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. Em **Parceiros,** escolha **Adicionar**. Em **Add Partner**, forneça os detalhes do parceiro conforme descrito pela tabela abaixo.

   ![Escolha "Adicionar" e forneça detalhes do parceiro](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do parceiro |
   | **Eliminatória** | Sim | O organismo autenticante que fornece identidades empresariais únicas a organizações, por exemplo, **D-U-N-S (Dun & Bradstreet)**. <p>Os parceiros podem optar por uma identidade de negócio mutuamente definida. Para estes cenários, selecione **Mutually Defined** for EDIFACT ou **Mutually Defined (X12)** para X12. <p>Para a RosettaNet, selecione **apenas DUNS,** que é o padrão. |
   | **Valor** | Sim | Um valor que identifica os documentos que as suas aplicações lógicas recebem. <p>Para a RosettaNet, este valor deve ser um número de nove dígitos que corresponde ao número DUNS. |
   ||||

   > [!NOTE]
   > Para os parceiros que usam o RosettaNet, pode especificar informações adicionais primeiro criando estes parceiros e, em seguida, [editando-os posteriormente](#edit-partner).

1. Quando tiver terminado, selecione **OK**.

   O seu novo parceiro aparece agora na lista de **Sócios.** Além disso, o azulejo **parceiros** atualiza o número atual de parceiros.

   ![Novo parceiro](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Editar parceiro

1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua conta de integração.
Escolha o azulejo dos **Parceiros.**

   ![Escolha azulejo "Parceiros"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. Em **Parceiros,** selecione o parceiro que pretende editar e escolha **Editar.** Em **Editar,** faça as suas alterações.

   ![Faça e guarde as suas alterações](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Para a RosettaNet, em **propriedades de parceiros RosettaNet,** pode especificar estas informações adicionais:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Classificação de Parceiros** | Não | Tipo de organização do parceiro |
   | **Código da cadeia de abastecimento** | Não | O código da cadeia de fornecimento do parceiro, por exemplo, "Tecnologias de Informação" ou "Componentes Eletrónicos" |
   | **Nome de contato** | Não | O nome de contacto do parceiro |
   | **E-mail** | Não | O endereço de e-mail do parceiro |
   | **Fax** | Não | O número do fax do parceiro |
   | **Telefone** | Não | O número de telefone do parceiro |
   ||||

1. Quando terminar, escolha **OK** para guardar as suas alterações.

## <a name="delete-partner"></a>Excluir parceiro

1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua conta de integração. Escolha o azulejo dos **Parceiros.**

   ![Screenshot que mostra o azulejo parceiro que seleciona quando pretende eliminar um parceiro.](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. Em **Under Partners**, selecione o parceiro que pretende eliminar. Escolha **Eliminar**.

   ![Excluir parceiro](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [acordos](../logic-apps/logic-apps-enterprise-integration-agreements.md)