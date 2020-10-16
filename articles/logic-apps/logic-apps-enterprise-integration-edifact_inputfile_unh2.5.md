---
title: Segmentos UNH 2.5 em mensagens EDIFACT
description: Resolver mensagens EDIFACT com segmentos UNH2.5 em Azure Logic Apps com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: c6d95cb938a341b94a32989e82c42d37956c0a80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89179849"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Lidar com documentos EDIFACT com segmentos UNH2.5 em Azure Logic Apps

Se um segmento UNH2.5 existir num documento EDIFACT, o segmento é utilizado para a procura de esquemas. Por exemplo, nesta amostra mensagem EDIFACT, o campo unh `EAN008` é:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Para lidar com esta mensagem, siga estes passos descritos abaixo:

1. Atualize o esquema.

1. Verifique as definições do acordo.

## <a name="update-the-schema"></a>Atualizar o esquema

Para processar a mensagem, é necessário implementar um esquema que tenha o nome do nó raiz UNH2.5. Por exemplo, o nome de raiz do esquema para o campo unh amostra é `EFACT_D03B_ORDERS_EAN008` . Para cada um `D03B_ORDERS` que tenha um segmento unh2.5 diferente, você tem que implementar um esquema individual.

## <a name="add-schema-to-edifact-agreement"></a>Adicionar esquema ao acordo EDIFACT

### <a name="edifact-decode"></a>Descodificar o EDIFACT

Para descodificar a mensagem de entrada, crie o esquema nas definições de receção do acordo EDIFACT:

1. No [portal Azure,](https://portal.azure.com)abra a sua conta de integração.

1. Adicione o esquema à sua conta de integração.

1. Configurar o esquema nas definições de receção do acordo EDIFACT.

1. Selecione o acordo EDIFACT e **selecione Editar como JSON**. Adicione o valor UNH2.5 à secção do Acordo de `schemaReferences` Receção:

   ![Adicione UNH2.5 para receber o acordo](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Codificação EDIFACT

Para codificar a mensagem de entrada, configurar o esquema no acordo EDIFACT enviar definições

1. No [portal Azure,](https://portal.azure.com)abra a sua conta de integração.

1. Adicione o esquema à sua conta de integração.

1. Configurar o esquema nas definições de envio do acordo EDIFACT.

1. Selecione o acordo EDIFACT e **selecione Editar como JSON**. Adicione o valor UNH2.5 à secção do Acordo de `schemaReferences` Envio:

   ![Adicione UNH2.5 para enviar acordo](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [acordos de conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md)
