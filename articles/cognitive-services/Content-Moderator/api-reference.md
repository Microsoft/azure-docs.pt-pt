---
title: Referência de API-Content Moderator
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as várias APIs de análise e moderação de conteúdo para Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757386"
---
# <a name="content-moderator-api-reference"></a>Referência de API de Content Moderator

Você pode começar a usar as APIs de Content Moderator do Azure das seguintes maneiras:

- Na portal do Azure, [assine a API do Content moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Consulte [tentar Content moderator na Web](quick-start.md) para se inscrever com a [ferramenta de revisão de Content moderator](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>APIs de Moderação

Você pode usar as seguintes Content Moderator APIs para configurar seus fluxos de trabalho de resmoderação.

| Descrição | Referência |
| -------------------- |-------------|
| **API de moderação de imagem**<br /><br />Digitalize imagens e detecte conteúdo em potencial de adulto e erótico usando marcas, pontuações de confiança e outras informações extraídas. <br /><br />Use essas informações para publicar, rejeitar ou examinar o conteúdo em seu fluxo de trabalho de resmoderação. <br /><br />| [Referência da API de moderação de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Referência da API de moderação de imagem")   |
| **API de moderação de texto**<br /><br />Digitalizar conteúdo de texto. Os termos de profanação e os dados pessoais são retornados. <br /><br />Use essas informações para publicar, rejeitar ou examinar o conteúdo em seu fluxo de trabalho de resmoderação.<br /><br /> | [Referência de API de moderação de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Referência de API de moderação de texto")   |
| **API de moderação de vídeo**<br /><br />Digitalize vídeos e detecte conteúdo em potencial de adulto e erótico. <br /><br />Use essas informações para publicar, rejeitar ou examinar o conteúdo em seu fluxo de trabalho de resmoderação.<br /><br /> | [Visão geral da API de moderação de vídeo](video-moderation-api.md "Visão geral da API de moderação de vídeo")   |
| **Listar API de gerenciamento**<br /><br />Crie e gerencie listas personalizadas de exclusão ou inclusão de imagens e texto. Se habilitada, as operações de **correspondência de imagem** e **tela de texto** fazem correspondência difusa do conteúdo enviado em suas listas personalizadas. <br /><br />Para obter eficiência, você pode ignorar a etapa de moderação baseada em aprendizado de máquina.<br /><br /> | [Listar referência da API de gerenciamento](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Listar referência da API de gerenciamento")   |

## <a name="review-apis"></a>APIs de revisão

As APIs de revisão têm os seguintes componentes:

| Descrição | Referência |
| -------------------- |-------------|
| **Tarefas**<br /><br /> Inicie fluxos de trabalho de moderação de verificação e análise para conteúdo de imagem e texto. Um trabalho de moderação verifica seu conteúdo usando a API de moderação de imagem e a API de moderação de texto. Trabalhos de moderação usam os fluxos de trabalho padrão e definidos para gerar revisões. <br /><br />Depois que um moderador humano revisou as marcas e os dados de previsão atribuídos automaticamente e enviou uma decisão de moderação de conteúdo, a API de revisão envia todas as informações para o ponto de extremidade da API.<br /><br /> | [Referência de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Referência de trabalho")   |
| **Revisões**<br /><br />Use a ferramenta de revisão para criar diretamente revisões de imagem ou de texto para moderadores humanos.<br /><br /> Depois que um moderador humano revisou as marcas e os dados de previsão atribuídos automaticamente e enviou uma decisão de moderação de conteúdo, a API de revisão envia todas as informações para o ponto de extremidade da API.<br /><br /> | [Referência de revisão](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Referência de revisão")   |
| **Fluxos de trabalho**<br /><br />Crie, atualize e obtenha detalhes sobre os fluxos de trabalho personalizados que sua equipe cria. Você define fluxos de trabalho usando a ferramenta de revisão. <br /> <br />Os fluxos de trabalho normalmente usam Content Moderator, mas também podem usar algumas outras APIs que estão disponíveis como conectores na ferramenta de revisão.<br /><br /> | [Referência de fluxo de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Referência de fluxo de trabalho")   |