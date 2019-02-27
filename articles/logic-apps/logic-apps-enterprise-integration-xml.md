---
title: Mensagens XML para a integração empresarial de B2B - Azure Logic Apps | Documentos da Microsoft
description: Processar, validar, transformar e enriquecer mensagens XML para soluções de B2B no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: a75ac9773072423c13eef85ecad29c632c13d024
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873258"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Mensagens XML e arquivos simples no Azure Logic Apps com o Enterprise Integration Pack

Utilizar o logic apps, tem a capacidade de processar mensagens do XML é que envia e recebe. Esta funcionalidade está incluída com o Enterprise Integration Pack. Para os utilizadores com um plano de fundo do BizTalk Server, o Enterprise Integration Pack fornece capacidades semelhantes para transformar e validar as mensagens, trabalhar com ficheiros simples e até mesmo usar o XPath para enriquecer ou extrair propriedades específicas de uma mensagem. 

Para os utilizadores que estão agora a este espaço, esses recursos expanda como processar mensagens no fluxo de trabalho. Por exemplo, se estiver num cenário de empresa-empresa e a trabalhar com esquemas XML específicas, em seguida, pode utilizar o Enterprise Integration Pack para melhorar a como sua empresa processa-as. 

O Enterprise Integration Pack inclui: 

* [Validação XML](logic-apps-enterprise-integration-xml-validation.md "Saiba mais sobre a validação de mensagem XML") -validar uma mensagem XML de entrada ou de saída com um esquema específico.
* [Transformação XML](../logic-apps/logic-apps-enterprise-integration-transform.md "Saiba mais sobre transformações de mensagens XML e mapas") – converter ou personalizar uma mensagem XML com base nos seus requisitos ou os requisitos de um parceiro.
* [Simples de ficheiros de codificação e descodificação de ficheiro simples](logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre codificação/decodificação ficheiro simples") - codificar ou decodificar um arquivo simples. Por exemplo, SAP aceita e envia os ficheiros IDOC no formato de ficheiro simples. Muitas plataformas de integração criar mensagens XML, incluindo o Logic Apps. Por isso, pode criar uma aplicação lógica que usa o codificador de ficheiro simples "converter" os arquivos XML para ficheiros simples. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) - enriquecer uma mensagem e extrair as propriedades específicas da mensagem. Em seguida, pode utilizar as propriedades extraídas para encaminhar a mensagem para um destino ou um ponto de extremidade intermediário.

## <a name="try-it-out"></a>Experimentar
[Implementar uma aplicação lógica totalmente operacional](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (exemplo do GitHub) ao utilizar os recursos XML no Azure Logic Apps.

## <a name="learn-more"></a>Saiba mais
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")
