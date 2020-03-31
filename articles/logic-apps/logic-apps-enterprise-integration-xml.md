---
title: Mensagens XML e ficheiros planos
description: Processar, validar e transformar mensagens XML em Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792145"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Mensagens XML e ficheiros planos em Aplicativos Lógica Azure com Pacote de Integração Empresarial

Nas [Aplicações Lógicas Azure,](logic-apps-overview.md)pode processar mensagens XML que envia e recebe utilizando o Pacote de Integração Empresarial. Se utilizou o BizTalk Server, o Enterprise Integration Pack fornece capacidades semelhantes para transformar e validar mensagens, trabalhar com ficheiros planos e até usar o XPath para enriquecer ou extrair propriedades específicas de uma mensagem. Se você é novo neste espaço, estas funcionalidades expandem a forma como processa mensagens no fluxo de trabalho da sua aplicação lógica. Por exemplo, se tiver um cenário de negócio-a-negócio (B2B) e trabalhar com esquemas específicos de XML, pode usar o Enterprise Integration Pack para melhorar a forma como a sua empresa processa estas mensagens.

Por exemplo, o Pacote de Integração Empresarial inclui estas capacidades:

* [Validação XML](logic-apps-enterprise-integration-xml-validation.md): Validar uma mensagem XML recebida ou externa contra um esquema específico.

* [Transformação XML](logic-apps-enterprise-integration-transform.md): Converter ou personalizar uma mensagem XML com base nos seus requisitos ou nos requisitos de um parceiro utilizando mapas.

* [Codificação de ficheiros planos e descodificação](logic-apps-enterprise-integration-flatfile.md)de ficheiros planos: Codificar ou descodificar um ficheiro plano.

  Por exemplo, o SAP aceita e envia ficheiros IDOC em formato de ficheiro plano. Muitas plataformas de integração criam mensagens XML, incluindo Aplicações Lógicas. Assim, pode criar uma aplicação lógica que utiliza o codificador de ficheiros planos para "converter" ficheiros XML em ficheiros planos.

* [XPath](workflow-definition-language-functions-reference.md#xpath): Enriqueça uma mensagem e extrai propriedades específicas da mensagem. Em seguida, pode utilizar as propriedades extraídas para encaminhar a mensagem para um destino, ou um ponto final intermediário.

## <a name="sample"></a>Sample

[Implemente uma aplicação lógica totalmente operacional](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (amostra GitHub) utilizando as funcionalidades XML em Aplicações Lógicas Azure.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md)
