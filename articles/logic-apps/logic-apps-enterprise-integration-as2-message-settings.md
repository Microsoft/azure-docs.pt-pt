---
title: Definições de mensagem AS2 - Azure Logic Apps
description: Guia de referência para AS2 enviar e receber as definições no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769455"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referência para as definições de mensagem AS2 no Azure Logic Apps com o Enterprise Integration Pack

Esta referência descreve as propriedades que podem ser definidos para especificar a forma como um contrato de AS2 processa mensagens enviadas e recebidas entre parceiros comerciais. Configure estas propriedades com base no seu contrato com o parceiro que troca mensagens com.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Definições de receção de AS2

![Selecione "Receber definições"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Substituir propriedades da mensagem** | Não | Substitui as propriedades em mensagens de entrada com as suas definições de propriedade. |
| **Deve ser assinada a mensagem** | Não | Especifica se todas as mensagens de entrada devem ser assinadas digitalmente. Se precisar de iniciar sessão, do **certificado** , selecione um convidado parceiro pública certificado existente para validar a assinatura as mensagens. Se não tiver um certificado, saiba mais sobre [adicionando certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Mensagem deve ser encriptada** | Não | Especifica se todas as mensagens de entrada tem de estar encriptadas. Mensagens criptografadas não são rejeitadas. Se necessitar de encriptação, do **certificado** , selecione um anfitrião parceiro privada certificado existente para desencriptar mensagens de entrada. Se não tiver um certificado, saiba mais sobre [adicionando certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Mensagem deve ser comprimida** | Não | Especifica se a todas as mensagens de entrada devem ser comprimidas. Mensagens de compressão não são rejeitadas. |
| **Não permitir duplicados de ID da mensagem** | Não | Especifica se pretende permitir que as mensagens com IDs duplicados. Se desabilitar os IDs duplicados, selecione o número de dias entre verificações. Também pode escolher se pretende suspender duplicados. |
| **MDN Text** | Não | Especifica a padrão disposição notificação de mensagem (MDN) que pretende que sejam enviada para o remetente da mensagem. |
| **Enviar MDN** | Não | Especifica se pretende enviar MDNs síncronos para as mensagens recebidas.  |
| **Enviar MDN assinado** | Não | Especifica se pretende enviar MDNs assinados para as mensagens recebidas. Se precisar de iniciar sessão, do **algoritmo MIC** , selecione o algoritmo a utilizar para assinar mensagens. |
| **Enviar MDN assíncrono** | Não | Especifica se pretende enviar MDNs no modo assíncrono. Se selecionar MDNs assíncronas, do **URL** caixa, especifique o URL para onde enviar os MDNs. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Definições de envio de AS2

![Selecione "Definições de envio"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Ativar a assinatura da mensagem** | Não | Especifica se todas as mensagens de saída devem ser assinadas digitalmente. Se precisar de iniciar sessão, selecione estes valores: <p>-From a **algoritmo de assinatura** , selecione o algoritmo a utilizar para assinar mensagens. <br>-From a **certificado** , selecione um anfitrião parceiro privada certificado existente para a assinatura de mensagens. Se não tiver um certificado, saiba mais sobre [adicionando certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Ativar a encriptação de mensagens** | Não | Especifica se todas as mensagens de saída têm de ser encriptadas. Se necessitar de encriptação, selecione estes valores: <p>-From a **algoritmo de encriptação** , selecione o algoritmo de certificado público de parceiro convidado para utilizar para encriptar mensagens. <br>-From a **certificado** , selecione um convidado parceiro privada certificado existente para encriptação de mensagens de saída. Se não tiver um certificado, saiba mais sobre [adicionando certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Ativar compressão de mensagens** | Não | Especifica se a todas as mensagens de saída devem ser comprimidas. |
| **Desdobrar cabeçalhos HTTP** | Não | Coloca o HTTP `content-type` cabeçalho numa única linha. |
| **Transmitir o nome de ficheiro no cabeçalho MIME** | Não | Especifica se pretende incluir o nome de ficheiro no cabeçalho de MIME. |
| **Pedir MDN** | Não | Especifica se pretende receber notificações de disposição de mensagem (MDNs) para todas as mensagens de saída. |
| **Pedir MDN assinado** | Não | Especifica se pretende receber MDNs assinados para todas as mensagens de saída. Se precisar de iniciar sessão, do **algoritmo MIC** , selecione o algoritmo a utilizar para assinar mensagens. |
| **Pedir MDN assíncrono** | Não | Especifica se pretende receber MDNs de forma assíncrona. Se selecionar MDNs assíncronas, do **URL** caixa, especifique o URL para onde enviar os MDNs. |
| **Enable NRR** | Não | Especifica se exige não-repúdio receção (NRR). Este atributo de comunicação fornece uma evidência que os dados foram recebidos conforme abordado. |
| **Formato do algoritmo SHA2** | Não | Especifica o formato do algoritmo MIC a utilizar para a assinatura nos cabeçalhos de saída nas mensagens AS2 ou MDN |
||||

## <a name="next-steps"></a>Passos Seguintes

[Trocar mensagens AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
