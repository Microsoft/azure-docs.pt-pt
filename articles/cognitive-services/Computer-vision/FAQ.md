---
title: Perguntas frequentes-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para perguntas frequentes sobre o API da Pesquisa Visual Computacional nos serviços cognitivas do Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564607"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>API da Pesquisa Visual Computacional perguntas frequentes

> [!TIP]
> Se você não encontrar respostas para suas perguntas nestas perguntas frequentes, tente solicitar a API da Pesquisa Visual Computacional Comunidade no [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou contate a [ajuda e o suporte no UserVoice](https://cognitive.uservoice.com/)

---

**Pergunta**: *Posso treinar API da Pesquisa Visual Computacional usar marcas personalizadas?  Por exemplo, eu gostaria de alimentar fotos de gatos para "treinar" o ia e, em seguida, receber o valor de mercado em uma solicitação de ia.*

**Resposta**: Esta função não está disponível no momento. No entanto, nossos engenheiros estão trabalhando para trazer essa funcionalidade para Pesquisa Visual Computacional.

---

**Pergunta**: *Pesquisa Visual Computacional pode ser usado localmente sem uma conexão com a Internet?*

**Resposta**: No momento, não oferecemos uma solução local ou localmente.

---

**Pergunta**: *Pesquisa Visual Computacional pode ser usado para ler as placas de licença?*

**Resposta**: A API da visão oferece boa detecção de texto com o OCR, mas não é otimizada para as placas de licença no momento. Estamos constantemente tentando melhorar nossos serviços e adicionamos OCR para reconhecimento de placa de licença automática à nossa lista de solicitações de recursos.

---

**Pergunta**: *Que tipos de superfícies de escrita têm suporte para o reconhecimento de manuscrito?*

**Resposta**: A tecnologia funciona com diferentes tipos de superfícies, incluindo quadros de comunicações, white paper e notas auto-adesivas amarelas.

---

**Pergunta**: *Quanto tempo leva a operação de reconhecimento de manuscrito?*

**Resposta**: A quantidade de tempo que ela leva depende do comprimento do texto. Para textos mais longos, pode levar até vários segundos. Portanto, após a conclusão da operação reconhecer texto manuscrito, talvez seja necessário aguardar antes de recuperar os resultados usando a operação obter resultado da operação de texto manuscrito.

---

**Pergunta**: *Como a tecnologia de reconhecimento de manuscrito lida com o texto inserido usando um cursor no meio de uma linha?*

**Resposta**: Esse texto é retornado como uma linha separada pela operação de reconhecimento de manuscrito.

---

**Pergunta**: *Como a tecnologia de reconhecimento de manuscrito lida com palavras ou linhas cruzadas?*

**Resposta**: Se as palavras forem riscadas com várias linhas para renderizá-las não reconhecíveis, a operação de reconhecimento de manuscrito não as escolherá. No entanto, se as palavras forem riscadas usando uma única linha, esse cruzamento será tratado como ruído e as palavras ainda serão selecionadas pela operação de reconhecimento de manuscrito.

---

**Pergunta**: *Quais orientações de texto têm suporte para a tecnologia de reconhecimento de manuscrito?*

**Resposta**: O texto orientado em ângulos de até cerca de 30 graus a 40 graus pode ser selecionado pela operação de reconhecimento de manuscrito.

---
