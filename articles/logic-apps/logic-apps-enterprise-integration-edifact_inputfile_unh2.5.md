---
title: UNH 2,5 segmentos em mensagens EDIFACT-aplicativos lógicos do Azure
description: Resolver mensagens EDIFACT com segmentos do UNH 2.5 em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: c30c35375a45171c938f80e94dd7d9be4c3ee8b1
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679938"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Manipular documentos do EDIFACT com segmentos do UNH 2.5 no aplicativo lógico do Azure

Se existir um segmento UNH 2.5 em um documento EDIFACT, o segmento será usado para pesquisa de esquema. Por exemplo, neste exemplo de mensagem EDIFACT, o campo UNH é `EAN008`:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Para lidar com essa mensagem, siga estas etapas descritas abaixo:

1. Atualize o esquema.

1. Verifique as configurações do contrato.

## <a name="update-the-schema"></a>Atualizar o esquema

Para processar a mensagem, você precisa implantar um esquema que tenha o nome do nó raiz do UNH 2.5. Por exemplo, o nome da raiz do esquema para o campo UNH de exemplo é `EFACT_D03B_ORDERS_EAN008`. Para cada `D03B_ORDERS` que tem um segmento UNH 2.5 diferente, você precisa implantar um esquema individual.

## <a name="add-schema-to-edifact-agreement"></a>Adicionar esquema ao contrato EDIFACT

### <a name="edifact-decode"></a>Decodificação EDIFACT

Para decodificar a mensagem de entrada, configure o esquema nas configurações de recebimento do contrato de EDIFACT:

1. Na [portal do Azure](https://portal.azure.com), abra sua conta de integração.

1. Adicione o esquema à sua conta de integração.

1. Configure o esquema nas configurações de recebimento do contrato de EDIFACT.

1. Selecione o contrato EDIFACT e selecione **Editar como JSON**. Adicione o valor de UNH 2,5 à seção `schemaReferences` do contrato de recebimento:

   ![Adicionar o UNH 2.5 ao contrato de recebimento](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Codificação EDIFACT

Para codificar a mensagem de entrada, configure o esquema nas configurações de envio do contrato de EDIFACT

1. Na [portal do Azure](https://portal.azure.com), abra sua conta de integração.

1. Adicione o esquema à sua conta de integração.

1. Configure o esquema nas configurações de envio do contrato de EDIFACT.

1. Selecione contrato de EDIFACT e clique em **Editar como JSON**.  Adicionar o valor de UNH 2,5 no **schemaReferences** do contrato de envio

1. Selecione o contrato EDIFACT e selecione **Editar como JSON**. Adicione o valor de UNH 2.5 à seção `schemaReferences` do contrato de envio:

   ![Adicionar o UNH 2.5 ao contrato de envio](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [contratos de conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md)