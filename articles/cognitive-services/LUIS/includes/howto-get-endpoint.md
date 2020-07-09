---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: a6e6c89c34723fb9e11b0c7e4ab8c9bedb8aa9ca
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959049"
---
Na secção **Gerir** (menu superior direito), na página **Azure Resources** (menu esquerdo), copie o URL **de consulta exemplo** e, em seguida, cole-o num novo separador de navegador.

O URL de ponto final parece o seguinte formato, com o seu subdomínio personalizado, iD de aplicação e chave de ponto final substituindo APP-ID e KEY-ID:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```