---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 2a98e2a97a9154d9e256a4662bb292896c6d1e77
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588799"
---
Na secção **Gerir** (menu superior-direito), na página **Dos Recursos Azure** (menu esquerdo), copie o URL de **Consulta de Exemplo** e, em seguida, cole num novo separador de navegador.

O URL de ponto final parece o seguinte formato, com o seu próprio subdomínio personalizado, ID de aplicação e chave de ponto final substituindo APP-ID, e KEY-ID:

```console
https://YOUR-CUSTOM-SUBDMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```