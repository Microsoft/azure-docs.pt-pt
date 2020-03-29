---
title: Definições de mensagem AS2
description: Guia de referência para as2 enviar e receber configurações em Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793040"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referência para definições de mensagens AS2 em Aplicações lógicas azure com pacote de integração empresarial

Esta referência descreve as propriedades que pode definir para especificar como um acordo AS2 lida com mensagens enviadas e recebidas entre parceiros comerciais. Instale estas propriedades com base no seu acordo com o parceiro que troca mensagens consigo.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 Receber definições

![Selecione "Receber Definições"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Propriedades de mensagem de substituição** | Não | Substitui as propriedades em mensagens de entrada com as definições da sua propriedade. |
| **A mensagem deve ser assinada** | Não | Especifica se todas as mensagens de entrada devem ser assinadas digitalmente. Se necessitar de assinar, a partir da lista de **Certificados,** selecione um certificado público de parceiro convidado existente para validar a assinatura nas mensagens. Se não tiver um certificado, saiba mais sobre a adição de [certificados.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **A mensagem deve ser encriptada** | Não | Especifica se todas as mensagens de entrada devem ser encriptadas. As mensagens não encriptadas são rejeitadas. Se necessitar de encriptação, a partir da lista de **Certificados,** selecione um certificado privado de parceiro anfitrião existente para desencriptar mensagens de entrada. Se não tiver um certificado, saiba mais sobre a adição de [certificados.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **A mensagem deve ser comprimida** | Não | Especifica se todas as mensagens de entrada devem ser comprimidas. As mensagens não comprimidos são rejeitadas. |
| **Duplicados de ID de mensagem de si** | Não | Especifica se permite mensagens com IDs duplicados. Se não autorizar iDs duplicados, selecione o número de dias entre verificações. Também pode escolher se suspende duplicados. |
| **Texto MDN** | Não | Especifica a notificação de disposição de disposição de mensagem padrão (MDN) que deseja enviar para o remetente da mensagem. |
| **Enviar MDN** | Não | Especifica se deve enviar MDNs sincronizados para mensagens recebidas.  |
| **Enviar MDN assinado** | Não | Especifica se deve enviar MDNs assinados para mensagens recebidas. Se necessitar de assinar, a partir da lista de **algoritmos MIC,** selecione o algoritmo para usar para assinar mensagens. |
| **Enviar MDN assíncrono** | Não | Especifica se deve enviar MDNs sincronicamente. Se selecionar MDNs assíncronos assíncronos, na caixa **URL,** especifique o URL para onde enviar os MDNs. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 Enviar definições

![Selecione "Enviar Definições"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Ativar a assinatura de mensagens** | Não | Especifica se todas as mensagens de saída devem ser assinadas digitalmente. Se necessitar de assinar, selecione estes valores: <p>- A partir da lista de **algoritmos de assinatura,** selecione o algoritmo para utilizar para a assinatura de mensagens. <br>- A partir da lista de **Certificados,** selecione um certificado privado de parceiro anfitrião existente para a assinatura de mensagens. Se não tiver um certificado, saiba mais sobre a adição de [certificados.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Ativar encriptação de mensagem** | Não | Especifica se todas as mensagens de saída devem ser encriptadas. Se necessitar de encriptação, selecione estes valores: <p>- A partir da lista de Algoritmos de **Encriptação,** selecione o algoritmo de certificado público do parceiro convidado para usar para encriptar mensagens. <br>- A partir da lista de **Certificados,** selecione um certificado privado de parceiro convidado existente para encriptar mensagens de saída. Se não tiver um certificado, saiba mais sobre a adição de [certificados.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Ativar a compressão da mensagem** | Não | Especifica se todas as mensagens de saída devem ser comprimidas. |
| **Desdobre os cabeçalhos HTTP** | Não | Coloca o `content-type` cabeçalho HTTP numa única linha. |
| **Transmitir nome de ficheiro no cabeçalho MIME** | Não | Especifica se deve incluir o nome do ficheiro no cabeçalho MIME. |
| **Pedido MDN** | Não | Especifica se deve receber notificações de disposição de mensagens (MDNs) para todas as mensagens de saída. |
| **Pedido assinado MDN** | Não | Especifica se deve receber MDNs assinados para todas as mensagens de saída. Se necessitar de assinar, a partir da lista de **algoritmos MIC,** selecione o algoritmo para usar para assinar mensagens. |
| **Solicite MDN assíncrono assíncrono** | Não | Especifica se deve receber MDNs assincronicamente. Se selecionar MDNs assíncronos assíncronos, na caixa **URL,** especifique o URL para onde enviar os MDNs. |
| **Ativar o NRR** | Não | Especifica se deve exigir recibo de não-repúdio (NRR). Este atributo de comunicação fornece provas de que os dados foram recebidos conforme abordado. |
| **Formato algoritmo SHA2** | Não | Especifica o formato de algoritmo MIC a utilizar para a assinatura nos cabeçalhos para as mensagens AS2 ou MDN de saída |
||||

## <a name="next-steps"></a>Passos seguintes

[Trocar mensagens AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
