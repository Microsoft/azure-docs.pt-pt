---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "87375138"
---
Na secção **Gerir** (menu superior direito), na página **Azure Resources** (menu esquerdo), copie o URL **de consulta exemplo** e, em seguida, cole-o num novo separador de navegador.

O URL de ponto final parece o seguinte formato, com o seu subdomínio personalizado, iD de aplicação e chave de ponto final substituindo APP-ID e KEY-ID:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```