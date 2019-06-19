---
title: O docker compose receitas de contentor
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: IEvan
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: bd796a316dd806472254aa1cbf1e5a498df5ce43
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207438"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Utilizar vários contentores numa rede privada com o Docker Compose

```docker-compose
version: '3.3'
services:
  forms:
    image:  "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: "https://westus2.api.cognitive.microsoft.com/"
       apiKey: 0e17f5e4a83c43bb9d7756289f0c5cf4
       FormRecognizer__ComputerVisionApiKey: 02a628714e9f4a6e970eb557fc780854
       FormRecognizer__ComputerVisionEndpointUri: "https://westcentralus.api.cognitive.microsoft.com/"
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: 02a628714e9f4a6e970eb557fc780854
      billing: "https://westcentralus.api.cognitive.microsoft.com/"
    ports:
      - "5021:5000"
```
