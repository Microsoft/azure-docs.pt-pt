---
title: Personalize um modelo pessoa em Índice de Vídeo - Azure
titleSuffix: Azure Media Services
description: Este artigo dá uma visão geral do que é um modelo de Pessoa no Índice de Vídeo e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 613ad2d0ab90bef016e80a33be12b71cba5e2f71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047056"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalize um modelo de pessoa no Indexador de Vídeo

O Video Indexer suporta o reconhecimento de celebridades nos seus vídeos. A funcionalidade de reconhecimento de celebridades abrange aproximadamente um milhão de rostos com base em fontes de dados comumente solicitadas, tais como IMDB, Wikipedia e principais influenciadores do LinkedIn. Rostos que não são reconhecidos pelo Video Indexer ainda são detetados, mas não têm nome. Os clientes podem construir modelos personalizados da Pessoa e permitir que o Video Indexer reconheça rostos que não são reconhecidos por padrão. Os clientes podem construir estes modelos Pessoa emparelhando o nome de uma pessoa com ficheiros de imagem do rosto da pessoa.  

Se a sua conta atender a diferentes casos de utilização, poderá beneficiar de ser capaz de criar vários modelos Pessoa por conta. Por exemplo, se o conteúdo da sua conta for para ser classificado em diferentes canais, talvez queira criar um modelo pessoa separado para cada canal. 

> [!NOTE]
> Cada modelo de Pessoa suporta até 1 milhão de pessoas e cada conta tem um limite de 50 modelos Pessoas. 

Uma vez criado um modelo, pode usá-lo fornecendo o ID do modelo de um modelo específico da Pessoa ao carregar/indexar ou reindexar um vídeo. Treinando um novo rosto para um vídeo, atualiza o modelo personalizado específico com o que o vídeo estava associado. 

Se não necessitar do suporte de modelo multiple Person, não atribua um ID do modelo pessoa ao seu vídeo ao carregar/indexar ou reindexar. Neste caso, o Video Indexer utilizará o modelo Person predefinido na sua conta. 

Pode utilizar o website Do Indexer de Vídeo para editar rostos que foram detetados num vídeo e gerir vários modelos personalizados da Pessoa na sua conta, conforme descrito no [modelo Personalizar uma Pessoa usando um](customize-person-model-with-website.md) tópico do site. Também pode utilizar a API, conforme descrito no [modelo Personalizar uma Pessoa utilizando APIs](customize-person-model-with-api.md).
