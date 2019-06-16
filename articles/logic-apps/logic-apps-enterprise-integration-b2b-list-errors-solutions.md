---
title: Erros e soluções para cenários B2B - Azure Logic Apps | Documentos da Microsoft
description: Encontrar erros e soluções para cenários B2B no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: f0591b47ce7ba6837f300088c856c0098fb66710
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60998829"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Erros de B2B e soluções para o Azure Logic Apps

Este artigo ajuda-o a resolver problemas de erros que podem acontecer em cenários de aplicações lógicas B2B e sugere as ações apropriadas para corrigir esses erros.

## <a name="agreement-resolution"></a>Resolução do contrato

### <a name="no-agreement-found"></a>Foi encontrado nenhum contrato 

|   |   |  
|---|---|
| Descrição do erro | Nenhum acordo encontrado com parâmetros de resolução do contrato. | 
| Ação do utilizador | O contrato deve ser adicionado à conta de integração com identidades de negócio definido. </br>As identidades de negócio devem corresponder à mensagem de entrada IDs. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Nenhum acordo encontrado com identidades

|   |   | 
|---|---|
| Descrição do erro | Nenhum acordo encontrado com identidades: 'AS2Identity':: 'Partner1' e 'AS2Identity':: 'Partner3' | 
| Ação do utilizador | AS2 inválido-partir ou AS2-a configuradas para o contrato. </br>Corrija a mensagem AS2 "AS2-de" ou "AS2-a" cabeçalhos ou o contrato de acordo com os IDs de AS2 nos cabeçalhos de mensagens AS2 com configurações de contrato. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Cabeçalhos de mensagem AS2 em falta  

|   |   |  
|---|---|
| Descrição do erro | Cabeçalhos de AS2 inválidos. Um do "AS2-a" ou "AS2-de" cabeçalhos está vazio. | 
| Ação do utilizador | Foi recebida uma mensagem AS2 que não continha o AS2-partir ou AS2-a ou ambos os cabeçalhos. </br> Verifique a mensagem AS2 AS2-do e AS2-para cabeçalhos e correto-los com base na configuração do contrato. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Falta o corpo da mensagem AS2 e cabeçalhos    

|   |   |  
|---|---|
| Descrição do erro | O conteúdo do pedido é nulo ou estar vazio. | 
| Ação do utilizador | Foi recebida uma mensagem AS2 que não continha o corpo da mensagem. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>Falha de desencriptação de mensagem AS2

|   |   | 
|---|---|
| Descrição do erro |  [processados/erro: falha de desencriptação] | 
| Ação do utilizador | Adicionar @base64ToBinary para AS2Message antes de enviar para o parceiro. |
|||

Por exemplo:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>Falha de desencriptação de MDN

|   |   | 
|---|---|
| Descrição do erro |  [processados/erro: falha de desencriptação] | 
| Ação do utilizador | Adicionar @base64ToBinary para MDN antes de enviar para o parceiro. | 
|||

Por exemplo:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Certificado de assinatura em falta

|   |   |  
|---|---|
| Descrição do erro| O certificado de assinatura não foi configurado para a entidade de AS2. </br>AS2-partir: partner1 AS2-a: partner2 | 
| Ação do utilizador | Configure definições de contrato de AS2 com o certificado correto para assinatura. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 e EDIFACT

### <a name="leading-or-trailing-space-found"></a>À esquerda ou foi encontrado o espaço à direita    
    
|   |   | 
|---|---|
| Descrição do erro | Foi encontrado um erro durante a análise. As transações de EDIFACT conjunto com o ID "123456"contido no intercâmbio (sem grupo) com o ID ' 987654 ", com o ID de remetente 'Partner1', ID de destinatário 'Partner2' está a ser suspenso com os seguintes erros: <p>"À esquerda do separador direita encontrado" |
| Ação do utilizador | As definições de contrato para ser configurado para permitir à esquerda e o espaço à direita. </br>Edite definições de contrato para permitir espaço à esquerda e à direita. |
|   |   |

![permitir espaço](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Ativou a verificação duplicada no contrato

|   |   | 
|---|---| 
| Descrição do erro | Número de controlo duplicado |
| Ação do utilizador | Este erro indica que a mensagem recebida tem números de controlo duplicado. </br>Corrija o número de controlo e reenviar a mensagem. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Esquema em falta no contrato

|   |   | 
|---|---| 
| Descrição do erro | Foi encontrado um erro durante a análise. X12 conjunto de transações com o ID '564220001' contido no grupo funcional com o ID "56422', no intercâmbio com o ID '000056422", com o ID de remetente "12345678", ID de destinatário "87654321' está a ser suspenso com os seguintes erros: <p>"A mensagem tem um tipo de documento desconhecido e não foi resolvida para qualquer um dos esquemas existentes configurados no contrato" |
| Ação do utilizador | Configure o esquema nas definições de contrato.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Esquema incorreto do contrato

|   |   | 
|---|---| 
| Descrição do erro | A mensagem tem um tipo de documento desconhecido e não foi resolvida para qualquer um dos esquemas existentes configurados no contrato. |
| Ação do utilizador | Configure o esquema correto nas definições de contrato. |
|   |   |

## <a name="flat-file"></a>Ficheiro simples

### <a name="input-message-with-no-body"></a>Mensagem de entrada com nenhum corpo

|   |   | 
|---|---|
| Descrição do erro | InvalidTemplate. Não é possível processar expressões de linguagem de modelo em ação 'Flat_File_Decoding' entradas na linha "1" e coluna "1902": ' Necessária propriedade "content" espera um valor, mas tem nulo. Caminho '.'. |
| Ação do utilizador | Este erro indica que a mensagem de entrada não contém um corpo. |
|   |   | 

