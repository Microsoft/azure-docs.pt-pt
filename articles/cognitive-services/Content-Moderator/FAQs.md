---
title: Perguntas mais frequentes sobre - Content Moderator
titlesuffix: Azure Cognitive Services
description: Obtenha respostas para perguntas freqüentes sobre o Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 11/21/2016
ms.author: sajagtap
ms.openlocfilehash: 99b95a26a91d14edbef264ac3b107f8462e0ef5e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762182"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

#### <a name="what-does-my-content-moderator-subscription-include"></a>O que faz o meu Content Moderator incluem subscrição?
A sua subscrição do Content Moderator inclui acesso a APIs e a ferramenta de revisão. Pode decidir se pretende utilizar um ou outro ou ambos, dependendo das suas necessidades de negócio.

#### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Quais os limites/restrições do conteúdo que pode ser moderado através da API?
Ao utilizar a API, as imagens devem ter um mínimo de 128 pixéis e um tamanho de ficheiro máximo de 4 MB. Texto pode ter um máximo de 1024 carateres de comprimento. Não há nenhum limite para a duração do vídeo.

#### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>O que acontece se o conteúdo passado para a API de texto ou para a API de imagem exceder os limites de tamanho?
A API de texto devolve um código de erro que informa que o texto é mais do que o permitido. A API de imagem também devolve um código de erro que informa que a imagem não cumpre os requisitos de tamanho.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Mantém as imagens, texto ou vídeos que são submetidos para moderação?
O conteúdo é exclusivamente sua e não pode ser mantido pela Microsoft sem o seu consentimento. A Microsoft utiliza medidas de segurança líderes do setor para ajudar a proteger os seus conteúdos.

#### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Posso utilizar o Content Moderator ao ecrã para imagens de exploração de ilegal filho?
Não. No entanto, as organizações qualificadas podem utilizar o [serviço de Cloud PhotoDNA](https://www.microsoft.com/photodna "serviço Cloud da Microsoft PhotoDNA") ao ecrã para este tipo de conteúdo.

#### <a name="up-to-how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Até quantos, reveja as equipes pode juntar-se um utilizador de campo? O usuário pode alternar entre as equipes?
Um utilizador pode associar uma equipe de cada vez.

#### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Que tipo de membro da Equipe funções são suportadas pela ferramenta de revisão? Em que diferem?
Atualmente, o Studio permite atribuir funções de administrador e revisor. Os administradores podem convidar outros utilizadores e tem acesso para as definições de configuração, enquanto os revisores só podem rever os resultados de moderação e uma etiqueta ou untag conteúdo.

#### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>O que é uma marca? A ferramenta de revisão suporta etiquetas personalizadas?
Uma marca é uma ou duas letras código curtos que denota um sinalizador de moderação, por exemplo, "a" para adulto, 'r' para adultos e assim por diante. Os administradores podem definir novas marcas para seus negócios, conforme necessário.

#### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Quantos membros de equipa posso ter na minha equipa de revisão?
Pode ter um máximo de cinco membros de equipa, incluindo o administrador numa equipa.
