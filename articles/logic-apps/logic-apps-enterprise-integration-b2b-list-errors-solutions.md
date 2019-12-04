---
title: Erros e soluções para cenários B2B
description: Localizar erros e soluções para cenários B2B em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 176bc3642f7c923b50cbf2f7a97096f88dc02817
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790709"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Erros e Soluções B2B para aplicativos lógicos do Azure

Este artigo ajuda a solucionar problemas de erros que podem ocorrer em cenários de Aplicativos Lógicos B2B e sugere ações apropriadas para corrigir esses erros.

## <a name="agreement-resolution"></a>Resolução do contrato

### <a name="no-agreement-found"></a>Nenhum contrato encontrado 

|   |   |  
|---|---|
| Descrição do erro | Nenhum contrato encontrado com parâmetros de resolução de contrato. | 
| Ação do usuário | O contrato deve ser adicionado à conta de integração com identidades de negócios acordadas. </br>As identidades de negócios devem corresponder às IDs de mensagem de entrada. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Nenhum contrato encontrado com identidades

|   |   | 
|---|---|
| Descrição do erro | Nenhum contrato encontrado com identidades: ' AS2Identity ':: ' Partner1 ' and'AS2Identity ':: ' Partner3 ' | 
| Ação do usuário | AS2-from ou AS2-to inválida configurado para o contrato. </br>Corrija os cabeçalhos "AS2-from" ou "AS2-to" da mensagem AS2 ou o acordo para corresponder as IDs de AS2 nos cabeçalhos de mensagem AS2 com configurações de contrato. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Cabeçalhos de mensagem AS2 ausentes  

|   |   |  
|---|---|
| Descrição do erro | Cabeçalhos AS2 inválidos. Um dos cabeçalhos "AS2-to" ou "AS2-from" está vazio. | 
| Ação do usuário | Foi recebida uma mensagem AS2 que não continha os cabeçalhos AS2-from ou AS2-to ou Both. </br> Verifique os cabeçalhos AS2-from e AS2-to da mensagem AS2 e corrija-os com base na configuração do contrato. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Corpo e cabeçalhos de mensagem AS2 ausentes    

|   |   |  
|---|---|
| Descrição do erro | O conteúdo da solicitação é nulo ou está vazio. | 
| Ação do usuário | Foi recebida uma mensagem AS2 que não continha o corpo da mensagem. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>Falha na descriptografia de mensagem AS2

|   |   | 
|---|---|
| Descrição do erro |  [processado/erro: descriptografia-com falha] | 
| Ação do usuário | Adicione @base64ToBinary ao AS2Message antes de enviar para o parceiro. |
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

### <a name="mdn-decryption-failure"></a>Falha de descriptografia de MDN

|   |   | 
|---|---|
| Descrição do erro |  [processado/erro: descriptografia-com falha] | 
| Ação do usuário | Adicione @base64ToBinary ao MDN antes de enviar para o parceiro. | 
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

### <a name="missing-signing-certificate"></a>Certificado de autenticação ausente

|   |   |  
|---|---|
| Descrição do erro| O certificado de autenticação não foi configurado para a parte AS2. </br>AS2-de: partner1 AS2-to: partner2 | 
| Ação do usuário | Defina as configurações do contrato AS2 com o certificado correto para a assinatura. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 e EDIFACT

### <a name="leading-or-trailing-space-found"></a>Espaço à esquerda ou à direita encontrado    
    
|   |   | 
|---|---|
| Descrição do erro | Erro encontrado durante a análise. O conjunto de transações EDIFACT com a ID ' 123456 ' contido no intercâmbio (sem o grupo) com a ID ' 987654 ', com a ID de remetente ' Partner1 ', ID do destinatário ' Partner2 ' está sendo suspenso com os seguintes erros: <p>"Separador à esquerda encontrado" |
| Ação do usuário | As configurações de contrato a serem configuradas para permitir espaços à esquerda e à direita. </br>Edite as configurações de contrato para permitir o espaço à esquerda e à direita. |
|   |   |

![permitir espaço](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>A verificação duplicada está habilitada no contrato

|   |   | 
|---|---| 
| Descrição do erro | Número de controle duplicado |
| Ação do usuário | Esse erro indica que a mensagem recebida tem números de controle duplicados. </br>Corrija o número de controle e reenvie a mensagem. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Esquema ausente no contrato

|   |   | 
|---|---| 
| Descrição do erro | Erro encontrado durante a análise. O conjunto de transações X12 com a ID ' 564220001 ' contido no grupo funcional com a ID ' 56422 ', no intercâmbio com a ID ' 000056422 ', com a ID de remetente ' 12345678 ', ID do destinatário ' 87654321 ' está sendo suspenso com os seguintes erros: <p>"A mensagem tem um tipo de documento desconhecido e não foi resolvida para nenhum dos esquemas existentes configurados no contrato" |
| Ação do usuário | Configure o esquema nas configurações do contrato.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Esquema incorreto no contrato

|   |   | 
|---|---| 
| Descrição do erro | A mensagem tem um tipo de documento desconhecido e não foi resolvida para nenhum dos esquemas existentes configurados no contrato. |
| Ação do usuário | Configure o esquema correto nas configurações do contrato. |
|   |   |

## <a name="flat-file"></a>Ficheiro simples

### <a name="input-message-with-no-body"></a>Mensagem de entrada sem corpo

|   |   | 
|---|---|
| Descrição do erro | Invalidatemplate. Não é possível processar as expressões de linguagem do modelo nas entradas da ação ' Flat_File_Decoding ' na linha ' 1 ' e na coluna ' 1902 ': ' o conteúdo da propriedade necessária ' ' content ' espera um valor, mas foi nulo. Caminho ' '. '. |
| Ação do usuário | Esse erro indica que a mensagem de entrada não contém um corpo. |
|   |   | 

