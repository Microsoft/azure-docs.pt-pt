---
title: Mensagens XML e arquivos simples
description: Processar, validar e transformar mensagens XML em aplicativos lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792145"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Mensagens XML e arquivos simples em aplicativos lógicos do Azure com Enterprise Integration Pack

Em [aplicativos lógicos do Azure](logic-apps-overview.md), você pode processar mensagens XML que você envia e recebe usando o Enterprise Integration Pack. Se você usou BizTalk Server, o Enterprise Integration Pack fornece recursos semelhantes para transformar e validar mensagens, trabalhar com arquivos simples e até mesmo usar o XPath para enriquecer ou extrair propriedades específicas de uma mensagem. Se você não estiver familiarizado com esse espaço, esses recursos expandirão como você processa as mensagens no fluxo de trabalho do aplicativo lógico. Por exemplo, se você tiver um cenário B2B (entre empresas) e trabalhar com esquemas XML específicos, poderá usar o Enterprise Integration Pack para aprimorar a forma como sua empresa processa essas mensagens.

Por exemplo, a Enterprise Integration Pack inclui estes recursos:

* [Validação de XML](logic-apps-enterprise-integration-xml-validation.md): valide uma mensagem XML de entrada ou de saída em um esquema específico.

* [Transformação XML](logic-apps-enterprise-integration-transform.md): Converta ou personalize uma mensagem XML com base em seus requisitos ou nos requisitos de um parceiro usando mapas.

* [Codificação de arquivo simples e decodificação de arquivo simples](logic-apps-enterprise-integration-flatfile.md): codificar ou decodificar um arquivo simples.

  Por exemplo, o SAP aceita e envia arquivos IDOC em formato de arquivo simples. Muitas plataformas de integração criam mensagens XML, incluindo aplicativos lógicos. Portanto, você pode criar um aplicativo lógico que usa o codificador de arquivo simples para "converter" arquivos XML em arquivos simples.

* [XPath](workflow-definition-language-functions-reference.md#xpath): enriquecer uma mensagem e extrair propriedades específicas da mensagem. Você pode usar as propriedades extraídas para rotear a mensagem para um destino ou um ponto de extremidade intermediário.

## <a name="sample"></a>Exemplo

[Implante um aplicativo lógico totalmente operacional](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (exemplo do GitHub) usando os recursos XML nos aplicativos lógicos do Azure.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
