---
title: Segmentos UNH 2.5 em mensagens EDIFACT
description: Resolver mensagens EDIFACT com segmentos UNH2.5 em Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792535"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Lidar com documentos EDIFACT com segmentos UNH2.5 em Aplicações Lógicas Azure

Se existe um segmento UNH2.5 num documento EDIFACT, o segmento é utilizado para a procura de esquemas. Por exemplo, nesta mensagem EDIFACT da amostra, o campo UNH é: `EAN008`

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Para lidar com esta mensagem, siga estes passos descritos abaixo:

1. Atualize o esquema.

1. Verifique as definições do contrato.

## <a name="update-the-schema"></a>Atualizar o esquema

Para processar a mensagem, é necessário implementar um esquema que tenha o nome do nó raiz UNH2.5. Por exemplo, o nome raiz do esquema para `EFACT_D03B_ORDERS_EAN008`o campo UNH da amostra é . Para `D03B_ORDERS` cada um que tem um segmento UNH2.5 diferente, você tem que implementar um esquema individual.

## <a name="add-schema-to-edifact-agreement"></a>Adicione esquema ao acordo EDIFACT

### <a name="edifact-decode"></a>Descodificação EDIFACT

Para descodificar a mensagem de entrada, configurar o esquema nas definições de receção do acordo EDIFACT:

1. No [portal Azure,](https://portal.azure.com)abra a sua conta de integração.

1. Adicione o esquema à sua conta de integração.

1. Configure o esquema nas definições de receção do acordo EDIFACT.

1. Selecione o acordo EDIFACT e selecione **Editar como JSON**. Adicione o valor UNH2.5 à `schemaReferences` secção do Acordo de Receção:

   ![Adicione UNH2.5 para receber acordo](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT Encode

Para codificar a mensagem de entrada, configure o esquema no acordo EDIFACT enviar definições

1. No [portal Azure,](https://portal.azure.com)abra a sua conta de integração.

1. Adicione o esquema à sua conta de integração.

1. Configure o esquema nas definições de envio do acordo EDIFACT.

1. Selecione o acordo EDIFACT e clique em **Editar como JSON**.  Adicione o valor UNH2.5 nos **esquemas** do Acordo de Envio

1. Selecione o acordo EDIFACT e selecione **Editar como JSON**. Adicione o valor UNH2.5 à `schemaReferences` secção do Acordo de Envio:

   ![Adicione UNH2.5 para enviar acordo](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os acordos](../logic-apps/logic-apps-enterprise-integration-agreements.md) de conta de integração