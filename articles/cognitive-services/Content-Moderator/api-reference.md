---
title: Referência API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Conheça os vários conteúdos de moderação e reveja as APIs para Moderador de Conteúdo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757386"
---
# <a name="content-moderator-api-reference"></a>Referência a API moderador de conteúdo

Pode começar com APIs de Moderador de Conteúdo Azure das seguintes formas:

- No portal Azure, [subscreva a API moderadora de conteúdos.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- Consulte [o Moderador de Conteúdo na web](quick-start.md) para se inscrever na ferramenta De revisão de [moderadorde conteúdo](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>APIs de Moderação

Pode utilizar as seguintes APIs moderadoras de conteúdo para configurar os seus fluxos de trabalho pós-moderação.

| Descrição | Referência |
| -------------------- |-------------|
| **API de moderação de imagens**<br /><br />Digitale imagens e detete potenciais conteúdos adultos e picantes utilizando tags, pontuações de confiança e outras informações extraídas. <br /><br />Utilize estas informações para publicar, rejeitar ou rever o conteúdo no seu fluxo de trabalho pós-moderação. <br /><br />| [Referência da API de moderação de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Referência da API de moderação de imagem")   |
| **API de moderação de textos**<br /><br />Digitalizar conteúdo de texto. Os termos de profanação e os dados pessoais são devolvidos. <br /><br />Utilize estas informações para publicar, rejeitar ou rever o conteúdo no seu fluxo de trabalho pós-moderação.<br /><br /> | [Referência da API de moderação de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Referência da API de moderação de texto")   |
| **API de moderação de vídeo**<br /><br />Scaneie vídeos e detete potenciais conteúdos adultos e picantes. <br /><br />Utilize estas informações para publicar, rejeitar ou rever o conteúdo no seu fluxo de trabalho pós-moderação.<br /><br /> | [Visão geral da API da moderação de vídeo](video-moderation-api.md "Visão geral da API da moderação de vídeo")   |
| **API de Gestão de Listas**<br /><br />Crie e gere listas de exclusão ou inclusão personalizadas de imagens e texto. Se ativado, as operações **de Imagem - Jogo** e Texto - **Ecrã** combinam com o conteúdo submetido com as suas listas personalizadas. <br /><br />Para eficiência, pode saltar o passo de moderação baseado em machine learning.<br /><br /> | [Referência da API de Gestão de Listas](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Referência da API de Gestão de Listas")   |

## <a name="review-apis"></a>APIs de revisão

As APIs de revisão têm os seguintes componentes:

| Descrição | Referência |
| -------------------- |-------------|
| **Tarefas**<br /><br /> Inicie fluxos de trabalho de moderação de digitalização e revisão para conteúdos de imagem e texto. Um trabalho de moderação digitaliza o seu conteúdo utilizando a API de Moderação de Imagem e a API de Moderação de Texto. Os trabalhos de moderação utilizam os fluxos de trabalho definidos e predefinidos para gerar comentários. <br /><br />Depois de um moderador humano ter revisto as etiquetas e dados de previsão atribuídos automaticamente e ter submetido uma decisão de moderação de conteúdo, a API de revisão submete todas as informações ao seu ponto final da API.<br /><br /> | [Referência de emprego](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Referência de emprego")   |
| **Revisões**<br /><br />Utilize a ferramenta Review para criar diretamente críticas de imagem ou texto para moderadores humanos.<br /><br /> Depois de um moderador humano ter revisto as etiquetas e dados de previsão atribuídos automaticamente e ter submetido uma decisão de moderação de conteúdo, a API de revisão submete todas as informações ao seu ponto final da API.<br /><br /> | [Referência de revisão](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Referência de revisão")   |
| **Fluxos de trabalho**<br /><br />Crie, atualize e obtenha detalhes sobre os fluxos de trabalho personalizados que a sua equipa cria. Define os fluxos de trabalho utilizando a ferramenta 'Revisão'. <br /> <br />Os fluxos de trabalho normalmente usam o Moderador de Conteúdo, mas também podem usar outras APIs que estão disponíveis como conectores na ferramenta Review.<br /><br /> | [Referência do fluxo de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Referência do fluxo de trabalho")   |