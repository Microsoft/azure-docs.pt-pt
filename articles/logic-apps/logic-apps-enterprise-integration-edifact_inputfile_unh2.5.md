---
title: As mensagens de EDIFACT com UNH 2.5 segements - Azure Logic Apps | Documentos da Microsoft
description: Resolver documentos EDIFACT com segmentos UNH2.5 no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 926c9ebe8675d8b50d4544be813ae0b15492ae35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681677"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Processar documentos EDIFACT com segmentos UNH2.5 no Azure Logic Apps

Quando UNH2.5 está presente no documento EDIFACT, está a ser utilizado para a pesquisa de esquema. 

Exemplo: É o campo UNH **EAN008** na mensagem de EDIFACT  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

Passos a seguir para manipular a mensagem 
1. Atualizar o esquema
2. Verifique as definições de contrato  

## <a name="update-the-schema"></a>Atualizar o esquema
Para processar a mensagem, terá de implementar um esquema com o nome do nó raiz UNH2.5.  Para determinados um exemplo, o nome de raiz de esquema seria **EFACT_D03B_ORDERS_EAN008**  

Para cada D03B_ORDERS com um segmento UNH2.5 diferente, terá de implementar um esquema individual.  

## <a name="add-schema-to-the-edifact-agreement"></a>Adicionar o esquema para o contrato EDIFACT
### <a name="edifact-decode"></a>A descodificação EDIFACT
Para decodificar a mensagem de entrada, configurar o esquema no EDIFACT contrato receber definições
1. Adicionar o esquema para a conta de integração    
2. Configurar o esquema no EDIFACT contrato receber definições. 
3. Selecione o contrato EDIFACT e clique em **editar como JSON**.  Adicionar valor UNH2.5 no contrato de receber **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>A codificação EDIFACT
Para codificar a mensagem de entrada, configurar o esquema nas definições de envio de contrato EDIFACT
1. Adicionar o esquema para a conta de integração    
2. Configure o esquema nas definições de envio de contrato EDIFACT. 
3. Selecione o contrato EDIFACT e clique em **editar como JSON**.  Adicionar valor UNH2.5 no contrato de enviar **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Próximos Passos
* [Saiba mais sobre os contratos de conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração do enterprise")  