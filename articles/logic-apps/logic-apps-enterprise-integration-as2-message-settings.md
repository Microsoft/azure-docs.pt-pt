---
title: Definições de mensagem AS2
description: Guia de referência para AS2 enviar e receber definições em Azure Logic Apps com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "74793040"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referência para definições de mensagens AS2 em Azure Logic Apps com Pacote de Integração Empresarial

Esta referência descreve as propriedades que pode definir para especificar como um acordo AS2 lida com mensagens enviadas e recebidas entre parceiros comerciais. Conusque estas propriedades com base no seu acordo com o parceiro que troca mensagens consigo.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 Receber definições

![Selecione "Receber Definições"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Sobrepõe as propriedades da mensagem** | No | Substitui as propriedades em mensagens recebidas com as definições da sua propriedade. |
| **Mensagem deve ser assinada** | No | Especifica se todas as mensagens recebidas devem ser assinadas digitalmente. Se necessitar de assinatura, na lista **de Certificados,** selecione um certificado público de parceiro convidado existente para validar a assinatura nas mensagens. Se não tiver um certificado, saiba mais sobre [a adição de certificados.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **A mensagem deve ser encriptada** | No | Especifica se todas as mensagens recebidas devem ser encriptadas. As mensagens não encriptadas são rejeitadas. Se necessitar de encriptação, na lista de **Certificados,** selecione um certificado privado de parceiro anfitrião existente para desencriptar mensagens recebidas. Se não tiver um certificado, saiba mais sobre [a adição de certificados.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **A mensagem deve ser comprimida** | No | Especifica se todas as mensagens recebidas devem ser comprimidas. As mensagens não comprimidos são rejeitadas. |
| **Desativar duplicações de ID de mensagem** | No | Especifica se permite mensagens com IDs duplicados. Se não permitir a duplicação de IDs, selecione o número de dias entre verificações. Também pode escolher se suspende duplicados. |
| **Texto MDN** | No | Especifica a notificação de disposição da mensagem predefinida (MDN) que deseja enviar para o remetente de mensagem. |
| **Enviar MDN** | No | Especifica se deve enviar MDNs sincronizados para mensagens recebidas.  |
| **Enviar MDN assinado** | No | Especifica se deve enviar MDNs assinados para mensagens recebidas. Se necessitar de ser assinado, a partir da lista de **algoritmos MIC,** selecione o algoritmo a utilizar para a assinatura de mensagens. |
| **Enviar MDN assíncronos** | No | Especifica se deve enviar MDNs assíncronamente. Se selecionar MDNs assíncronos, na caixa **URL,** especifique o URL para onde enviar os MDNs. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 Enviar configurações

![Selecione "Enviar Definições"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Ativar a assinatura de mensagens** | No | Especifica se todas as mensagens de saída devem ser assinadas digitalmente. Se necessitar de assinatura, selecione estes valores: <p>- Da lista **de algoritmos de assinatura,** selecione o algoritmo para usar para a assinatura de mensagens. <br>- Na lista **de Certificados,** selecione um certificado privado de anfitrião existente para a assinatura de mensagens. Se não tiver um certificado, saiba mais sobre [a adição de certificados.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Ativar encriptação de mensagens** | No | Especifica se todas as mensagens de saída devem ser encriptadas. Se necessitar de encriptação, selecione estes valores: <p>- A partir da lista **de algoritmos de encriptação,** selecione o algoritmo de certificado público do parceiro convidado para usar para encriptar mensagens. <br>- Na lista **de Certificados,** selecione um certificado privado de parceiro convidado existente para encriptar mensagens de saída. Se não tiver um certificado, saiba mais sobre [a adição de certificados.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Ativar a compressão da mensagem** | No | Especifica se todas as mensagens de saída devem ser comprimidas. |
| **Desdobrar cabeçalhos HTTP** | No | Coloca o `content-type` cabeçalho HTTP numa única linha. |
| **Transmita o nome do ficheiro no cabeçalho MIME** | No | Especifica se deve incluir o nome do ficheiro no cabeçalho MIME. |
| **Solicitar MDN** | No | Especifica se deve receber notificações de disposição de mensagens (MDNs) para todas as mensagens de saída. |
| **Pedido assinado MDN** | No | Especifica se deve receber MDNs assinados para todas as mensagens de saída. Se necessitar de ser assinado, a partir da lista de **algoritmos MIC,** selecione o algoritmo a utilizar para a assinatura de mensagens. |
| **Pedido assíncronos MDN** | No | Especifica se deve receber MDNs assíncronamente. Se selecionar MDNs assíncronos, na caixa **URL,** especifique o URL para onde enviar os MDNs. |
| **Ativar O NRR** | No | Especifica se deve exigir recibo de não-repúdio (NRR). Este atributo de comunicação fornece provas de que os dados foram recebidos como endereçados. |
| **Formato de algoritmo SHA2** | No | Especifica o formato de algoritmo MIC para utilizar para a assinatura nos cabeçalhos para as mensagens AS2 ou MDN de saída |
||||

## <a name="next-steps"></a>Passos seguintes

[Trocar mensagens AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
