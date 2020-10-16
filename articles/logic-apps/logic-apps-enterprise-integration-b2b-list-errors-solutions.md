---
title: Soluções para erros e problemas comuns em cenários B2B
description: Encontre soluções para erros e problemas comuns ao resolver os cenários B2B em Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 6400cfe7e524dcc16e08c2bba7dfba4a62d00b2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86232564"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Erros e soluções B2B para Apps Azure Logic

Este artigo ajuda-o a resolver erros que podem ocorrer em cenários de Aplicações Lógicas B2B e sugere ações apropriadas para corrigir esses erros.

## <a name="agreement-resolution"></a>Resolução do acordo

### <a name="no-agreement-found"></a>Nenhum acordo encontrado 

**Descrição do erro**: Nenhum acordo encontrado com parâmetros de resolução do acordo.

**Ação do utilizador**: O acordo deve ser adicionado à conta de integração com identidades empresariais acordadas. As identidades do negócio devem corresponder às identificações da mensagem de entrada.

### <a name="no-agreement-found-with-identities"></a>Nenhum acordo encontrado com identidades

**Descrição do erro**: Nenhum acordo encontrado com identidades: 'AS2Identity'::'Partner1' e 'AS2Identity':'Partner3'

**Ação do utilizador**: AS2-From inválidos ou AS2-To configurados para acordo. Corrija os cabeçalhos "AS2-From" ou "AS2-To" da mensagem AS2 ou o acordo para combinar os IDs AS2 nos cabeçalhos de mensagens AS2 com configurações de acordo.

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Cabeçalhos de mensagens AS2 em falta  

**Descrição do erro**: Cabeçalhos AS2 inválidos. Um dos cabeçalhos "AS2-To" ou "AS2-From" está vazio.

**Ação do utilizador**: Recebeu-se uma mensagem AS2 que não continha o AS2-From ou AS2-To ou ambos os cabeçalhos. Verifique os AS2-From de mensagens AS2 e AS2-To cabeçalhos e corrija-os com base na configuração do acordo.

### <a name="missing-as2-message-body-and-headers"></a>Corpo e cabeçalhos de mensagem AS2 desaparecidos    

**Descrição do erro**: O conteúdo do pedido é nulo ou vazio.

**Ação do utilizador**: Recebeu-se uma mensagem AS2 que não continha o corpo da mensagem.

### <a name="as2-message-decryption-failure"></a>Falha de desencriptação de mensagens AS2

**Descrição do erro**: [processo/erro: falha de desencriptação]

**Ação do utilizador**: Adicione @base64ToBinary ao AS2Message antes de enviar para o parceiro.

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

### <a name="mdn-decryption-failure"></a>Falha de desencriptação do MDN

**Descrição do erro**: [processo/erro: falha de desencriptação]

**Ação do utilizador**: Adicione @base64ToBinary ao MDN antes de enviar para o parceiro.

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

**Descrição do erro**: O Certificado de Assinatura não foi configurado para a parte AS2. AS2-From: partner1 AS2-To: partner2

**Ação do utilizador**: Configurar as definições do acordo AS2 com certificado correto para assinatura.

## <a name="x12-and-edifact"></a>X12 e EDIFACT

### <a name="leading-or-trailing-space-found"></a>Espaço de liderança ou de fuga encontrado    

**Descrição do erro**: Erro encontrado durante a análise. A transação EDIFACT definida com iD '123456' contida no intercâmbio (sem grupo) com iD '987654', com o remetente ID 'Partner1', o recetor ID 'Partner2' está a ser suspenso com os seguintes erros: "Separador de Trailing Líder encontrado"

**Ação do utilizador**: As definições do acordo devem ser configuradas para permitir o espaço de condução e de fuga. Editar as definições do acordo para permitir o espaço de liderança e de fuga.

![permitir o espaço](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Verificação duplicada habilitada no acordo

**Descrição do erro**: Número de controlo duplicado

**Ação do utilizador**: Este erro indica que a mensagem recebida tem números de controlo duplicados. Corrija o número de controlo e reenconce a mensagem.

### <a name="missing-schema-in-the-agreement"></a>Falta de esquema no acordo

**Descrição do erro**: Erro encontrado durante a análise. A transação X12 definida com iD '564220001' contida no grupo funcional com ID '56422', em troca com o ID '000056422', com o remetente ID '12345678', o recetor ID '87654321' está suspenso com os seguintes erros: "A mensagem tem um tipo de documento desconhecido e não resolveu nenhum dos esquemas existentes configurados no acordo"

**Ação do utilizador**: Configurar o esquema nas definições do acordo.

### <a name="incorrect-schema-in-the-agreement"></a>Esquema incorreto no acordo

**Descrição do erro**: A mensagem tem um tipo de documento desconhecido e não resolveu nenhum dos esquemas existentes configurados no acordo.

**Ação do utilizador**: Configurar o esquema correto nas definições do acordo.

## <a name="flat-file"></a>Ficheiro simples

### <a name="input-message-with-no-body"></a>Mensagem de entrada sem corpo

**Descrição do erro**: InvalidTemplate. Não é possível processar expressões linguísticas de modelo em ação entradas 'Flat_File_Decoding' na linha '1' e coluna '1902': 'Conteúdo de propriedade obrigatório' espera um valor mas foi nulo. Caminho ''.

**Ação do utilizador**: Este erro indica que a mensagem de entrada não contém um corpo.
