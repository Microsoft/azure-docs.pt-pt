---
title: Referência da API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a moderação de conteúdos de várias e reveja as APIs do Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: sajagtap
ms.openlocfilehash: f802c64e141e8757f633d82ad577fa50f7d076fc
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688883"
---
# <a name="content-moderator-api-reference"></a>Referência da API de moderador de conteúdo

Pode começar a utilizar APIs de moderador de conteúdos do Azure das seguintes formas:

- No portal do Azure, [subscrever a API Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Ver [Experimente o Content Moderator na web](quick-start.md) inscrever-se com o [ferramenta de revisão de moderador de conteúdo](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>APIs de Moderação

Pode utilizar APIs do Content Moderator seguintes para configurar os fluxos de trabalho de pós-moderação.

| Descrição | Referência |
| -------------------- |-------------|
| **API de moderação de imagem**<br /><br />Analise imagens e detetar potenciais conteúdos para adultos com etiquetas e outras informações extraídas pontuações de confiança. <br /><br />Utilize estas informações para publicar, rejeitar ou rever o conteúdo no seu fluxo de trabalho de pós-moderação. <br /><br />| [Referência da API de moderação de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "referência da API de moderação de imagem")   |
| **API de moderação de texto**<br /><br />Analise o conteúdo de texto. Termos de linguagem inapropriada e dados pessoais são devolvidos. <br /><br />Utilize estas informações para publicar, rejeitar ou rever o conteúdo no seu fluxo de trabalho de pós-moderação.<br /><br /> | [Referência da API de moderação de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "referência da API de moderação de texto")   |
| **API de moderação de vídeo**<br /><br />Analisar vídeos e detetar potenciais conteúdos para adultos. <br /><br />Utilize estas informações para publicar, rejeitar ou rever o conteúdo no seu fluxo de trabalho de pós-moderação.<br /><br /> | [Descrição geral da API de moderação de vídeo](video-moderation-api.md "descrição geral da API de moderação de vídeo")   |
| **API de gestão da lista**<br /><br />Criar e gerir listas de exclusão ou inclusão personalizadas de imagens e texto. Se estiver ativada, o **imagem - corresponder** e **texto - ecrã** operações fazer a correspondência difusa do conteúdo submetido face às suas listas personalizadas. <br /><br />Para uma eficiência, pode ignorar o passo de moderação baseada na aprendizagem da máquina.<br /><br /> | [Referência da API de gestão de listar](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "referência da API de gestão da lista")   |

## <a name="review-apis"></a>APIs de revisão

As APIs de revisão tem os seguintes componentes:

| Descrição | Referência |
| -------------------- |-------------|
| **Tarefas**<br /><br /> Inicie fluxos de trabalho de análise de revisão e moderação de conteúdo de imagem e texto. Uma tarefa de moderação examina seu conteúdo ao utilizar a API de moderação de imagens e a API de moderação de texto. Tarefas de moderação utilizam o definido e predefinido para gerar as revisões de fluxos de trabalho. <br /><br />Depois de um moderador humano tiver revisto as etiquetas de atribuída automaticamente e os dados de predição e submetida uma decisão de moderação de conteúdos, a API de revisão envia todas as informações para o ponto final de API.<br /><br /> | [Referência da tarefa](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "referência da tarefa")   |
| **Revisões**<br /><br />Utilize a ferramenta de revisão para criar diretamente as revisões de imagem ou texto para moderadores humanos.<br /><br /> Depois de um moderador humano tiver revisto as etiquetas de atribuída automaticamente e os dados de predição e submetida uma decisão de moderação de conteúdos, a API de revisão envia todas as informações para o ponto final de API.<br /><br /> | [Reveja a referência](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "rever referência")   |
| **Fluxos de trabalho**<br /><br />Criar, atualizar e obter detalhes sobre os fluxos de trabalho personalizados que cria a sua equipa. Definir fluxos de trabalho utilizando a ferramenta de revisão. <br /> <br />Fluxos de trabalho, normalmente, utilizam o Content Moderator, mas também podem utilizar determinadas outras APIs que estão disponíveis como conectores na ferramenta de revisão.<br /><br /> | [Referência de fluxo de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "referência de fluxo de trabalho")   |