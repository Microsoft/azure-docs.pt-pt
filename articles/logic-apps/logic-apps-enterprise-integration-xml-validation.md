---
title: Validar XML para a integração empresarial de B2B - Azure Logic Apps | Documentos da Microsoft
description: Validar XML com esquemas para soluções de B2B no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.date: 07/08/2016
ms.openlocfilehash: 8db0dbadd944007ff953f9ea69695bf988ffebb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996339"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validar XML para enterprise integração B2B no Azure Logic Apps Enterprise Integration Pack

Muitas vezes, em cenários de B2B, os parceiros num contrato devem certificar-se de que as mensagens que do exchange que são válidas antes de iniciar o processamento de dados. Pode validar os documentos em relação a um esquema predefinido com o uso do conector de validação de XML no Enterprise Integration Pack.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Validar um documento com o conector de validação de XML

1. Criar uma aplicação lógica, e [associar a aplicação para a conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md "aprender a ligar uma conta de integração para uma aplicação lógica") que tenha o esquema que pretende utilizar para validar os dados XML.

2. Adicionar uma **pedido - pedido de HTTP de uma quando é recebido** acionador à sua aplicação lógica.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Para adicionar o **validação de XML** ação, escolha **adicionar uma ação**.

4. Para filtrar todas as ações para aquele que pretende, introduza *xml* na caixa de pesquisa. Escolher **validação de XML**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Para especificar o conteúdo XML que pretende validar, selecione **conteúdo**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Selecione a marca do corpo como o conteúdo que pretende validar.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Para especificar o esquema que pretende utilizar para validar o anterior *conteúdo* de entrada, escolha **nome do esquema**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Guarde o seu trabalho  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Agora já precisar configurar o conector de validação. Num aplicativo do mundo real, pode querer armazenar os dados validados num aplicativo de linha de negócio (LOB) como o SalesForce. Para enviar a saída validada para Salesforce, adicione uma ação.

Para testar a ação de validação, fazer um pedido para o ponto final HTTP.

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")   

