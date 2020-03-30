---
title: Soluções para erros e problemas comuns em cenários B2B
description: Encontre soluções para erros e problemas comuns ao resolver cenários B2B em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 38e281ce3d8117bff719b1bb572f09acbbb89669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666691"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Erros e soluções B2B para Aplicações Lógicas Azure

Este artigo ajuda-o a resolver erros que podem acontecer nos cenários das Aplicações Lógicas B2B e sugere ações adequadas para corrigir esses erros.

## <a name="agreement-resolution"></a>Resolução do acordo

### <a name="no-agreement-found"></a>Nenhum acordo encontrado 

|   |   |  
|---|---|
| Descrição do erro | Nenhum acordo com os parâmetros de resolução do acordo. | 
| Ação do utilizador | O acordo deve ser adicionado à conta de integração com identidades comerciais acordadas. </br>As identidades do negócio devem corresponder às identificações da mensagem de entrada. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Nenhum acordo encontrado com identidades

|   |   | 
|---|---|
| Descrição do erro | Nenhum acordo encontrado com identidades: 'AS2Identidade':'Partner1' e 'AS2Identity':'Partner3' | 
| Ação do utilizador | Inválido AS2-From ou AS2-To configurado para acordo. </br>Corrija os cabeçalhos "AS2-From" ou "AS2-To" da mensagem ou o acordo para combinar com os IDs AS2 nos cabeçalhos de mensagem AS2 com configurações de acordo. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Cabeçalhos de mensagem AS2 desaparecidos  

|   |   |  
|---|---|
| Descrição do erro | Cabeçalhos AS2 inválidos. Um dos cabeçalhos "AS2-To" ou "AS2-From" está vazio. | 
| Ação do utilizador | Foi recebida uma mensagem AS2 que não continha os cabeçalhos AS2-From ou AS2-To ou ambos os cabeçalhos. </br> Verifique os cabeçalhos AS2-From e AS2-To da mensagem AS2-From e AS2-To e corrija-os com base na configuração do acordo. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Corpo de mensagem AS2 desaparecido e cabeçalhos    

|   |   |  
|---|---|
| Descrição do erro | O conteúdo do pedido é nulo ou vazio. | 
| Ação do utilizador | Foi recebida uma mensagem AS2 que não continha o corpo da mensagem. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>Falha na desencriptação da mensagem AS2

|   |   | 
|---|---|
| Descrição do erro |  [processado/Erro: falha da desencriptação] | 
| Ação do utilizador | Adicione @base64ToBinary ao AS2Message antes de enviar para o parceiro. |
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

### <a name="mdn-decryption-failure"></a>Falha de desencriptação da MDN

|   |   | 
|---|---|
| Descrição do erro |  [processado/Erro: falha da desencriptação] | 
| Ação do utilizador | Adicione @base64ToBinary à MDN antes de enviar para o parceiro. | 
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
| Descrição do erro| O Certificado de Assinatura não foi configurado para a parte AS2. </br>AS2-From: partner1 AS2-To: partner2 | 
| Ação do utilizador | Configure as definições do acordo AS2 com o certificado correto para assinatura. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 e EDIFACT

### <a name="leading-or-trailing-space-found"></a>Espaço de liderança ou de rasto encontrado    
    
|   |   | 
|---|---|
| Descrição do erro | Erro encontrado durante a análise. A transação EDIFACT definida com id '123456' contida no intercâmbio (sem grupo) com id '987654', com ID do remetente 'Partner1', o recetor ID 'Partner2' está suspenso com os seguintes erros: <p>"Separador de trailing principal encontrado" |
| Ação do utilizador | As definições do acordo a configurar para permitir o espaço de liderança e de trailing. </br>Editar definições de acordo para permitir o espaço de liderança e de trailing. |
|   |   |

![permitir espaço](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Verificação duplicada permitiu no acordo

|   |   | 
|---|---| 
| Descrição do erro | Número de controlo duplicado |
| Ação do utilizador | Este erro indica que a mensagem recebida tem números de controlo duplicados. </br>Corrija o número de controlo e reenvie a mensagem. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Esquema desaparecido no acordo

|   |   | 
|---|---| 
| Descrição do erro | Erro encontrado durante a análise. A transação X12 definida com id '564220001' contida em grupo funcional com ID '56422', em troca com o ID '000056422', com id '12345678', o recetor ID '87654321' está suspenso com os seguintes erros: <p>"A mensagem tem um tipo de documento desconhecido e não resolveu com nenhum dos esquemas existentes configurados no acordo" |
| Ação do utilizador | Configure o esquema nas definições do contrato.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Esquema incorreto no acordo

|   |   | 
|---|---| 
| Descrição do erro | A mensagem tem um tipo de documento desconhecido e não resolveu com nenhum dos esquemas existentes configurados no acordo. |
| Ação do utilizador | Configure o esquema correto nas definições do contrato. |
|   |   |

## <a name="flat-file"></a>Ficheiro simples

### <a name="input-message-with-no-body"></a>Mensagem de entrada sem corpo

|   |   | 
|---|---|
| Descrição do erro | Modelo inválido. Incapaz de processar expressões linguísticas de modeloem inputs de "Flat_File_Decoding" de ação na linha '1' e coluna '1902': "Conteúdo de propriedade exigido espera um valor, mas ficou nulo. Caminho ''. |
| Ação do utilizador | Este erro indica que a mensagem de entrada não contém um corpo. |
|   |   | 

