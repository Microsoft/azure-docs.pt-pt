---
title: Referência API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Conheça as várias moderação de conteúdos e reveja APIs para Moderador de Conteúdo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "72757386"
---
# <a name="content-moderator-api-reference"></a>Referência API moderador de conteúdo

Pode começar com APIs moderadores de conteúdo Azure nas seguintes formas:

- No portal Azure, [subscreva a API moderadora de conteúdos.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- Consulte [o Moderador de Conteúdo na web](quick-start.md) para se inscrever na ferramenta de Análise de Moderador de [Conteúdo.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="moderation-apis"></a>APIs de Moderação

Pode utilizar as seguintes APIs do Moderador de Conteúdo para configurar os seus fluxos de trabalho pós-moderação.

| Description | Referência |
| -------------------- |-------------|
| **API de moderação de imagens**<br /><br />Digitalize imagens e detete conteúdo potencial adulto e picante usando tags, pontuações de confiança e outras informações extraídas. <br /><br />Utilize estas informações para publicar, rejeitar ou rever o conteúdo no seu fluxo de trabalho pós-moderação. <br /><br />| [Referência API de Moderação de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Referência API de Moderação de Imagem")   |
| **API de moderação de textos**<br /><br />Digitalize o conteúdo do texto. Os termos de profanação e os dados pessoais são devolvidos. <br /><br />Utilize estas informações para publicar, rejeitar ou rever o conteúdo no seu fluxo de trabalho pós-moderação.<br /><br /> | [Referência da API de Moderação de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Referência da API de Moderação de Texto")   |
| **API de Moderação de Vídeo**<br /><br />Digitalize vídeos e detete potenciais conteúdos adultos e picantes. <br /><br />Utilize estas informações para publicar, rejeitar ou rever o conteúdo no seu fluxo de trabalho pós-moderação.<br /><br /> | [Visão geral da API de moderação de vídeo](video-moderation-api.md "Visão geral da API de moderação de vídeo")   |
| **API de Gestão de Listas**<br /><br />Criar e gerir listas de exclusão personalizada ou inclusão de imagens e texto. Se ativado, as operações **de Imagem - Match** e Text - As operações do **ecrã** correspondem ao conteúdo submetido com as suas listas personalizadas. <br /><br />Para eficiência, pode saltar o passo de moderação baseado em aprendizagem automática.<br /><br /> | [Referência API de Gestão de Listas](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Referência API de Gestão de Listas")   |

## <a name="review-apis"></a>APIs de revisão

As APIs de revisão têm os seguintes componentes:

| Description | Referência |
| -------------------- |-------------|
| **Trabalhos**<br /><br /> Iniciar fluxos de trabalho de moderação de digitalização e revisão para conteúdos de imagem e texto. Um trabalho de moderação digitaliza o seu conteúdo utilizando a API de Moderação de Imagem e a API de Moderação de Texto. Os trabalhos de moderação utilizam os fluxos de trabalho definidos e predefinidos para gerar revisões. <br /><br />Depois de um moderador humano ter revisto as etiquetas e dados de previsão autoatribuídos e submetido uma decisão de moderação de conteúdo, a API de Revisão submete toda a informação ao seu ponto final da API.<br /><br /> | [Referência de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Referência de trabalho")   |
| **Revisões**<br /><br />Utilize a ferramenta 'Rever' para criar diretamente revisões de imagem ou texto para moderadores humanos.<br /><br /> Depois de um moderador humano ter revisto as etiquetas e dados de previsão autoatribuídos e submetido uma decisão de moderação de conteúdo, a API de Revisão submete toda a informação ao seu ponto final da API.<br /><br /> | [Referência de revisão](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Referência de revisão")   |
| **Fluxos de trabalho**<br /><br />Crie, atualize e obtenha detalhes sobre os fluxos de trabalho personalizados que a sua equipa cria. Define fluxos de trabalho utilizando a ferramenta 'Rever'. <br /> <br />Os fluxos de trabalho normalmente usam o Moderador de Conteúdo, mas também podem usar certas outras APIs que estão disponíveis como conectores na ferramenta Review.<br /><br /> | [Referência do fluxo de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Referência do fluxo de trabalho")   |