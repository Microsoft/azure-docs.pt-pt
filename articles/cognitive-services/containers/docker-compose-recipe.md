---
title: O docker compose receitas de contentor
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 86b6a030af907bd0bf9d33d067ff253fd8601bbe
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295770"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Utilizar vários contentores numa rede privada com o Docker Compose

Fornecer o adequado _apikey_, _faturação_, e _URI do ponto final_ valores.

```docker-compose
version: '3.3'
services:
  forms:
    image:  "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: "< Your billing URL >"
       apikey: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
       FormRecognizer__ComputerVisionApiKey: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
       FormRecognizer__ComputerVisionEndpointUri: "< Your computer vision, form recognizer URI >"
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
      apikey: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
      billing: "< Your billing URL >"
    ports:
      - "5021:5000"
```
