---
title: Personalize um modelo pessoa em Indexer de Vídeo - Azure
titleSuffix: Azure Media Services
description: Este artigo dá uma visão geral do que é um modelo pessoa no Indexer de Vídeo e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73838301"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalize um modelo de pessoa em Indexer de vídeo

O Indexer de Vídeo apoia o reconhecimento de celebridades nos seus vídeos. A funcionalidade de reconhecimento de celebridades abrange cerca de um milhão de rostos com base em fontes de dados comumente solicitadas, tais como IMDB, Wikipedia e influenciadores de topo do LinkedIn. Rostos que não são reconhecidos pelo Indexer de Vídeo ainda são detetados, mas não têm nome. Os clientes podem construir modelos pessoais personalizados e permitir que o Indexer de Vídeo reconheça rostos que não são reconhecidos por padrão. Os clientes podem construir estes modelos Pessoa emparelhando o nome de uma pessoa com ficheiros de imagem do rosto da pessoa.  

Se a sua conta atender a diferentes casos de utilização, pode beneficiar de ser capaz de criar vários modelos pessoa por conta. Por exemplo, se o conteúdo da sua conta for destinado a ser classificado em diferentes canais, é melhor criar um modelo Pessoa separado para cada canal. 

> [!NOTE]
> Cada modelo pessoa suporta até 1 milhão de pessoas e cada conta tem um limite de 50 modelos Pessoa. 

Uma vez criado um modelo, pode usá-lo fornecendo o id do modelo de um modelo pessoa específico ao carregar/indexar ou reindexar um vídeo. Treinando um novo rosto para um vídeo, atualiza o modelo personalizado específico com o que o vídeo estava associado. 

Se não necessitar do suporte do modelo pessoa múltipla, não designe um ID do modelo Pessoa para o seu vídeo ao carregar/indexar ou reindexar. Neste caso, o Indexer de Vídeo utilizará o modelo Pessoa padrão na sua conta. 

Pode utilizar o website do Indexer de Vídeo para editar rostos que foram detetados num vídeo e gerir vários modelos personalizados da Pessoa na sua conta, como descrito no modelo Personalizar a Pessoa usando um tópico de [site.](customize-person-model-with-website.md) Também pode utilizar a API, conforme descrito no [modelo Personalizar uma Pessoa utilizando APIs](customize-person-model-with-api.md).
