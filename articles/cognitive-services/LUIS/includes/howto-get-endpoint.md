---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545228"
---
Na secção **Gerir** (menu superior direito), na página **Azure Resources** (menu esquerdo), copie o URL **de consulta exemplo** e, em seguida, cole-o num novo separador de navegador.

O URL de ponto final parece o seguinte formato, com o seu subdomínio personalizado, iD de aplicação e chave de ponto final substituindo APP-ID e KEY-ID:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```