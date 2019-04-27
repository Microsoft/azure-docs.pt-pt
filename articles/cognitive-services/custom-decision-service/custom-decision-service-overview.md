---
title: O que é o Serviço de Decisão Personalizada?
titlesuffix: Azure Cognitive Services
description: Este artigo disponibiliza uma descrição geral do Serviço de Decisão Personalizada.
services: cognitive-services
author: alekh
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: overview
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: f4a0f519fe83162e7e161c3fda2b78a94b1d4bd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60507832"
---
# <a name="what-is-custom-decision-service"></a>O que é o Serviço de Decisão Personalizada?

Numa aplicação Web ou móvel típica, uma página inicial apresenta as ligações para vários artigos ou outros tipos de conteúdo. À medida que a página inicial é carregada, pode pedir ao Serviço de Decisão Personalizada para classificar artigos incluídos nessa página inicial. Assim, quando um utilizador seleciona um artigo ao clicar no mesmo, pode ser enviado um segundo pedido para o Serviço de Decisão Personalizada, que registará o resultado dessa decisão de utilizador.

O Serviço de Decisão Personalizada é fácil de utilizar, uma vez que apenas necessita que seja adicionado um feed RSS para o seu conteúdo e algumas linhas de JavaScript na sua aplicação.

O Serviço de Decisão Personalizada converte o seu conteúdo em funcionalidades para aprendizagem automática. O sistema utiliza estas funcionalidades para compreender o seu conteúdo em termos de texto, imagens, vídeos e sentimento geral. Utiliza vários outros [Serviços Cognitivos da Microsoft](https://www.microsoft.com/cognitive-services), como [Associação de Entidades](../entitylinking/home.md), [Análise de Texto](../text-analytics/overview.md), [Emoções](../emotion/home.md) e a [Imagem Digitalizada](../computer-vision/home.md).

Alguns casos de utilização comum do Serviço de Decisão Personalizada incluem:

* Personalizar artigos num site de notícias
* Personalizar conteúdo de vídeo num portal de multimédia
* Otimizar o posicionamento de anúncios ou páginas Web para as quais é direcionado pelo anúncio
* Classificação de itens recomendados num site de compra.

O Serviço de Decisão Personalizada está atualmente em *pré-visualização pública gratuita*. Pode personalizar uma lista de artigos num site ou numa aplicação. A extração de caraterísticas funciona melhor para conteúdos em inglês. A [funcionalidade limitada](../text-analytics/overview.md) é disponibilizada para outros idiomas, como espanhol, francês, alemão, português e japonês. Esta documentação será revista quando estiverem disponíveis novas funcionalidades.

O Serviço de Decisão Personalizada pode ser utilizado em aplicações que não estão no domínio de personalização de conteúdos. Estas aplicações podem ser uma boa opção para uma pré-visualização personalizada. [Contacte-nos](https://azure.microsoft.com/overview/sales-number/) para saber mais.

## <a name="api-usage-modes"></a>Modos de utilização da API

O Serviço de Decisão Personalizada pode ser aplicado a páginas Web e aplicações móveis. As APIs podem ser chamadas a partir de um browser ou de uma aplicação. A utilização da API é semelhante em ambos, mas alguns dos detalhes são diferentes.

## <a name="glossary-of-terms"></a>Glossário de termos

Vários termos ocorrem com frequência nesta documentação:

* **Conjunto de ação**: O conjunto de itens de conteúdo do serviço de decisão personalizada para classificação. Este conjunto pode ser especificado como um ponto final *RSS* ou *Atom*.
* **Classificação**: Cada pedido de serviço de decisão personalizada Especifica um ou mais de ação define. O sistema responde ao selecionar todas as opções de conteúdo destes conjuntos e devolve-os por ordem de classificação.
* **Função de retorno de chamada**: Essa função, que especificar, processa o conteúdo na sua interface do Usuário. O conteúdo está ordenado pela ordem de classificação devolvida pelo Serviço de Decisão Personalizada.
* **Recompensa**: Uma medida de como o usuário respondeu a conteúdo renderizado. O Serviço de Decisão Personalizada avalia a resposta do utilizador através da utilização de cliques. Os cliques são comunicados ao sistema através do código personalizado inserido na sua aplicação.

## <a name="next-steps"></a>Passos Seguintes

* [Registar a sua aplicação](custom-decision-service-get-started-register.md) com o Serviço de Decisão Personalizada
* Começar a otimizar [uma página Web](custom-decision-service-get-started-browser.md) ou [uma aplicação de smartphone](custom-decision-service-get-started-app.md).
* Consulte a [referência da API](custom-decision-service-api-reference.md) para saber mais sobre a funcionalidade fornecida.
