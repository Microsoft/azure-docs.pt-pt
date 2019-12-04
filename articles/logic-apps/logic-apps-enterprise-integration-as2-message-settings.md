---
title: Definições de mensagem AS2
description: Guia de referência para configurações de envio e recebimento do AS2 em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793040"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referência para configurações de mensagem AS2 em aplicativos lógicos do Azure com Enterprise Integration Pack

Esta referência descreve as propriedades que você pode definir para especificar como um contrato AS2 lida com as mensagens enviadas e recebidas entre parceiros comerciais. Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Configurações de recebimento AS2

![Selecione "configurações de recebimento"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Propriedade | Obrigatório | Descrição |
|----------|----------|-------------|
| **Substituir Propriedades da mensagem** | Não | Substitui as propriedades nas mensagens de entrada pelas configurações de propriedade. |
| **A mensagem deve ser assinada** | Não | Especifica se todas as mensagens de entrada devem ser assinadas digitalmente. Se você precisar de assinatura, na lista de **certificados** , selecione um certificado público de parceiro convidado existente para validar a assinatura nas mensagens. Se você não tiver um certificado, saiba mais sobre como [adicionar certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **A mensagem deve ser criptografada** | Não | Especifica se todas as mensagens de entrada devem ser criptografadas. Mensagens não criptografadas são rejeitadas. Se você precisar de criptografia, na lista de **certificados** , selecione um certificado privado de parceiro de host existente para descriptografar mensagens de entrada. Se você não tiver um certificado, saiba mais sobre como [adicionar certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **A mensagem deve ser compactada** | Não | Especifica se todas as mensagens de entrada devem ser compactadas. Mensagens não compactadas são rejeitadas. |
| **Não permitir duplicatas de ID de mensagem** | Não | Especifica se as mensagens com IDs duplicadas devem ser permitidas. Se você não permitir IDs duplicadas, selecione o número de dias entre as verificações. Você também pode escolher se deseja suspender duplicatas. |
| **Texto MDN** | Não | Especifica a MDN (notificação de disposição de mensagem) padrão que você deseja que seja enviada ao remetente da mensagem. |
| **Enviar MDN** | Não | Especifica se o MDNs síncrono deve ser enviado para mensagens recebidas.  |
| **Enviar MDN assinada** | Não | Especifica se é para enviar MDNs assinadas para mensagens recebidas. Se você precisar de assinatura, na lista de **algoritmos do MIC** , selecione o algoritmo a ser usado para assinar mensagens. |
| **Enviar MDN assíncrona** | Não | Especifica se é para enviar MDNs de forma assíncrona. Se você selecionar MDNs assíncronas, na caixa **URL** , ESPECIFIQUE a URL para onde enviar o mDNS. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Configurações de envio AS2

![Selecione "configurações de envio"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Propriedade | Obrigatório | Descrição |
|----------|----------|-------------|
| **Habilitar assinatura de mensagem** | Não | Especifica se todas as mensagens de saída devem ser assinadas digitalmente. Se você precisar de assinatura, selecione estes valores: <p>-Na lista **algoritmo de assinatura** , selecione o algoritmo a ser usado para assinar mensagens. <br>-Na lista de **certificados** , selecione um certificado privado de parceiro de host existente para assinar mensagens. Se você não tiver um certificado, saiba mais sobre como [adicionar certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Habilitar criptografia de mensagem** | Não | Especifica se todas as mensagens de saída devem ser criptografadas. Se você precisar de criptografia, selecione estes valores: <p>-Na lista **algoritmo de criptografia** , selecione o algoritmo de certificado público do parceiro convidado a ser usado para criptografar mensagens. <br>-Na lista de **certificados** , selecione um certificado privado de parceiro convidado existente para criptografar mensagens de saída. Se você não tiver um certificado, saiba mais sobre como [adicionar certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Habilitar compactação de mensagem** | Não | Especifica se todas as mensagens de saída devem ser compactadas. |
| **Desdobrar cabeçalhos HTTP** | Não | Coloca o cabeçalho de `content-type` HTTP em uma única linha. |
| **Nome do arquivo de transmissão no cabeçalho MIME** | Não | Especifica se o nome do arquivo deve ser incluído no cabeçalho MIME. |
| **Solicitar MDN** | Não | Especifica se as notificações de disposição de mensagem (MDNs) devem ser recebidas para todas as mensagens de saída. |
| **Solicitar MDN assinada** | Não | Especifica se deve receber MDNs assinadas para todas as mensagens de saída. Se você precisar de assinatura, na lista de **algoritmos do MIC** , selecione o algoritmo a ser usado para assinar mensagens. |
| **Solicitar MDN assíncrona** | Não | Especifica se é para receber MDNs de forma assíncrona. Se você selecionar MDNs assíncronas, na caixa **URL** , ESPECIFIQUE a URL para onde enviar o mDNS. |
| **Habilitar NRR** | Não | Especifica se é necessário exigir recibo de não repúdio (NRR). Esse atributo de comunicação fornece evidências de que os dados foram recebidos como resolvidos. |
| **Formato de algoritmo SHA2** | Não | Especifica o formato do algoritmo MIC a ser usado para assinar os cabeçalhos das mensagens AS2 de saída ou MDN |
||||

## <a name="next-steps"></a>Passos seguintes

[Trocar mensagens AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
