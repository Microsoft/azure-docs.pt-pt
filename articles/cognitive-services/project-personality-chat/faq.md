---
title: Perguntas frequentes-Personality Chat
titlesuffix: Azure Cognitive Services
description: Perguntas frequentes sobre Personality Chat
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ROBOTS: NOINDEX
ms.openlocfilehash: e763e3c3731858e20226efbd354927f38c3d5d70
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704226"
---
# <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Por que isso não responde a todas as perguntas que eu solicito, como outros bots de bate-papo?

O chat com personalidade de projeto aprimorará um bot com uma pequena palestra comum que apresenta personalidades e cria uma experiência de usuário mais completa. Ele não foi projetado para realizar conversas longas sobre tópicos que não são relevantes para a função principal do bot. Embora possa responder a todas as conversas, ela é restrita, na versão beta, a domínios comuns de pequenas palestras.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Como posso personalizar a personalidade para atender à minha marca?

Selecione o persona mais próximo das personas padrão disponíveis. Hoje, você pode usar a biblioteca editorial e editar as respostas para se adequar melhor à sua marca. No futuro, você pode carregar um conjunto de amostras de declarações de sua personalidade escolhida e encontrar sua versão de ID de pessoa mais próxima. Também há maneiras de treinar novamente e personalizar o modelo.

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>Esse serviço está capacitando agentes inteligentes existentes como o Zo?

Os serviços que capacitam o Zo, o Cortana e o Project Personality Chat compartilham algumas técnicas semelhantes, mas são pilhas diferentes. Ele incorporou aprendizados das experiências com o zo e a Cortana.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Esse serviço pode levar a experiências ruins com o cliente?

Para fornecer uma experiência mais rica, Personality Chat pode gerar respostas além daquelas no conjunto de dados editorial e tentar interpretar todas as entradas do usuário. Portanto, é possível que uma resposta não pareça direto no contexto. Foi criada uma série de controlos para ajudar a evitar respostas desfavoráveis com base nos conhecimentos de agentes inteligentes, como o Zo. Por predefinição, o Personality Chat está definido para responder apenas às intenções reconhecidas do utilizador. Pode querer testar se o Project Personality Chat é adequado para as suas circunstâncias. Os seus comentários são importantes se vir algo que precise de mais preparação. Se você usar esse serviço com seus clientes no futuro, recomendamos que você considere o registro em log anônimo para ajudá-lo a identificar problemas com interações de usuário ao vivo.
