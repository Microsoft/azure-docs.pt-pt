---
title: Personalizar um modelo de pessoa no indexador de vídeo - Azure
titlesuffix: Azure Media Services
description: Este artigo fornece uma descrição geral sobre o que é um modelo de pessoa no indexador de vídeo e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: b491120639421d85d2fbb1a0efb2b6dd09ec1d4c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794645"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalizar um modelo de pessoa Video Indexer

O Video Indexer oferece suporte a reconhecimento de celebridade nos seus vídeos. O recurso de reconhecimento de celebridade abrange aproximadamente os rostos de um milhão com base na origem de dados frequentemente pedidos como IMDB, Wikipedia e influenciadores principais do LinkedIn. Rostos que não são reconhecidos pelo indexador de vídeo ainda são detetados, mas são deixados sem nome. Os clientes podem criar modelos personalizados de pessoa e ativar o indexador de vídeo reconhecer rostos que não são reconhecidos por predefinição. Os clientes podem criar esses modelos de pessoa ao emparelhar o nome de uma pessoa com arquivos de imagem de face da pessoa.  

Se a sua conta atende ao diferentes casos de utilização, pode beneficiar a possibilidade de criar vários modelos de pessoa por conta. Por exemplo, se o conteúdo na sua conta destina-se a ser ordenadas em diferentes canais, pode querer criar um modelo de pessoa separado para cada canal. 

> [!NOTE]
> Cada modelo de pessoa oferece suporte a até 1 milhão de pessoas e cada conta tem um limite de 50 modelos de pessoa. 

Depois de criar um modelo, pode usá-lo ao fornecer o ID de modelo de um modelo de pessoa específico quando carregar/indexar ou reindexação um vídeo. Um novo rosto para um vídeo de formação, atualiza o modelo personalizado específico que o vídeo foi associado. 

Se não for necessário o suporte a vários modelo pessoa, não atribua uma pessoa ID de modelo para o seu vídeo quando carregar/indexar ou reindexação. Neste caso, o indexador de vídeo irá utilizar o modelo de pessoa padrão na sua conta. 

Pode utilizar o site do indexador de vídeos de rostos que foram detetados um vídeo de editar e gerir vários modelos de pessoa personalizados na sua conta, conforme descrito no [personalizar um modelo de Person usando um Web site](customize-person-model-with-website.md) tópico. Também pode utilizar a API, conforme descrito em [personalizar um modelo de pessoa com APIs](customize-person-model-with-api.md).